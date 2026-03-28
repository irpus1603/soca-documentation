# soca-control Design Spec
**Date:** 2026-03-21
**Last Updated:** 2026-03-28

## Context

soca-dashboard manages a single edge device (one soca-engine instance). As deployments grow, operators need a single control plane to see all edge devices, cameras, and alerts in one place — without logging into each device separately.

soca-control is the **central dashboard** for the entire SOCA fleet. It consumes alerts published by each edge's soca-engine (via Redis Stream or Google Pub/Sub), stores them locally, and provides:
- Fleet-wide visibility (all edges, cameras, active jobs)
- Live video monitor (MJPEG links direct to each edge)
- Filterable + exportable reports per incident category with Statistics and Detail tabs
- System settings and user management

---

## Architecture

### Technology Stack
- **Framework:** Django 6.0.x + Python
- **ORM / DB:** Django ORM, SQLite (local)
- **Background workers:**
  - `consume_streams` — Redis Stream consumer (one process per fleet)
  - `consume_pubsub` — Google Pub/Sub subscriber (one process per fleet, subscribes per-edge)
- **Redis:** Read-only consumer of each edge's `soca:detections` stream (when using Redis transport)
- **HTTP:** `requests` to call each edge's `/health`, `/system`, `/jobs/`, `/api/cameras/` endpoints
- **Export:** `csv` (stdlib) + `reportlab` for PDF
- **Frontend:** DaisyUI 4 (Tailwind CSS via CDN) + Chart.js 4 (via CDN), function-based views
- **Timezone:** `TIME_ZONE = 'Asia/Jakarta'` (GMT+7), `USE_TZ = True`

### Key Design Decisions
- Edge discovery is **manual** — admin registers each edge URL in Settings
- soca-control supports two alert transports per-edge: Redis Stream or Google Pub/Sub (configured per edge)
- All alerts are **persisted locally** in soca-control's own SQLite for fast queries
- Live video uses **direct browser links** to each edge's MJPEG stream (no proxy)
- soca-engine has **no authentication** (runs on private network only) — no token/key needed
- Snapshot images served via GCS public URL (preferred) or soca-dashboard proxy endpoint
- Dashboard fetches edge health/system in parallel using `ThreadPoolExecutor` to avoid hanging when edges are offline
- Redis DLQ replay is **out of scope** — soca-control only reads live stream data

---

## Module Structure

```
soca-control/
├── manage.py
├── requirements.txt
├── soca_control/
│   ├── settings.py
│   └── urls.py
└── app/
    ├── models.py          # Edge, Alert
    ├── views.py           # all page views (dashboard, monitor, reports, settings, users)
    ├── urls.py            # all URL patterns
    ├── edge_client.py     # thin HTTP wrapper: health, system, jobs, cameras, edge-info, parallel fetch
    ├── redis_consumer.py  # management command: consume Redis streams → save Alert records
    ├── reports.py         # CSV and PDF export logic
    └── templates/
        ├── base.html
        ├── dashboard/
        │   └── index.html
        ├── monitor/
        │   └── index.html
        ├── reports/
        │   ├── intrusion.html
        │   ├── ppe.html
        │   ├── object_detection.html
        │   ├── _stats.html        # shared statistics tab partial
        │   ├── _detail_chart.html # Chart.js bar charts partial
        │   └── _filters.html      # shared filter bar partial
        ├── settings/
        │   └── index.html
        └── users/
            ├── list.html
            └── form.html          # shared create/edit form
```

---

## Data Models

### SiteConfig
```python
SiteConfig:                     # singleton (pk=1)
    company_name     CharField
    app_name         CharField
    company_logo     FileField
    logo_height      PositiveIntegerField
    snapshot_storage CharField  # 'local' | 'gcs'
    gcs_bucket       CharField  # GCS bucket name (no gs:// prefix)
```

### Edge
```python
Edge:
    name                CharField    # unique, e.g. "edge-mac-mini-bekasi"
    url                 URLField     # soca-dashboard base URL, e.g. "http://192.168.1.10:8080"
    engine_url          URLField     # soca-engine URL, e.g. "http://192.168.1.10:8001"
    redis_url           CharField    # e.g. "redis://192.168.1.10:6379"
    redis_stream        CharField    # default "soca:detections"
    subscriber_type     CharField    # 'redis' | 'pubsub'
    pubsub_project_id   CharField    # GCP project ID (Pub/Sub only)
    pubsub_subscription CharField    # Pub/Sub subscription name (Pub/Sub only)
    gcs_path_prefix     CharField    # GCS path prefix for this edge, e.g. "edge-mac-mini-bekasi"
    location            CharField    # free text (populated from /api/edge-info/ on save)
    notes               TextField    # optional
    active              BooleanField # enabled/disabled for polling
    api_key             CharField    # API key from soca-dashboard Settings > Operations
    mediamtx_url        URLField     # public HLS URL reachable from browser, e.g. "http://192.168.1.100:8888"; blank = hide stream viewer buttons
    last_seen           DateTimeField
    created_at          DateTimeField
```

### Alert
```python
Alert:
    edge          ForeignKey(Edge, on_delete=SET_NULL, null=True)
    edge_name     CharField   # denormalized from Redis message: edge_name
    camera_id     CharField   # raw camera identifier from detection event
    camera_name   CharField   # human-readable camera name (populated from /api/cameras/)
    job_id        CharField
    frame_id      CharField
    timestamp     DateTimeField  # UTC, parsed from Redis message: timestamp field
    alert_category CharField     # "Intrusion", "PPE", "Object Detection"
    rule_name     CharField      # first triggered rule from rule_results list
    snapshot_message TextField
    snapshot_path  CharField     # edge-local path, used to build snapshot_url
    detection_count IntegerField # from roi_summary.total_count
    in_roi_count   IntegerField  # from roi_summary.in_roi_count
    cls_summary      JSONField     # from roi_summary.cls_counts, e.g. {"0": 2}
    cls_name_summary JSONField     # from roi_summary.cls_name_summary, e.g. {"person": 2, "helmet": 1}
    crossing_counts  JSONField     # {rule_name: {"in": N, "out": N}} for people_count rules
    crowd_count      IntegerField  # in-ROI person count for crowd rules
    lpr_plates       JSONField     # [{track_id, plate_number, plate_confidence, plate_expiry}]
    raw              JSONField     # full parsed message dict
    created_at       DateTimeField # when soca-control received it

    # Computed property (not stored):
    snapshot_url   # GCS public URL or edge-proxy URL depending on SiteConfig.snapshot_storage
                   # GCS:   https://storage.googleapis.com/{bucket}/{edge.gcs_path_prefix}/snapshots/...
                   # Local: {edge.url}/alerts/snapshot/{rel_path}
```

`display_camera()` method returns `camera_name` if set, falls back to `camera_id`.

---

## Redis Stream Wire Format

Each message published to `soca:detections` has a single field `"payload"` containing a JSON string.

Parsed payload (schema v2.3):
```json
{
  "schema_version": "2.2",
  "edge_name": "edge-bardi-home",
  "job_id": "550e8400-...",
  "frame_id": "bardi-stairs-front_20260320_100000_123456",
  "camera_id": "bardi-stairs-front",
  "camera_name": "Bardi Stairs Front",
  "timestamp": "2026-03-20T10:00:00.123456Z",
  "alert_category": "Intrusion",
  "snapshot_message": "2 person(s) detected at ...",
  "detections": [...],
  "roi_summary": {"total_count": 2, "in_roi_count": 1, "cls_counts": {"0": 2}},
  "aging": {...},
  "rule_results": [{"rule_name": "person_in_zone", "triggered": true, "actions_fired": [...]}],
  "snapshot_path": "snapshots/.../frame.jpg"
}
```

Key changes from v2.2:
- Added `camera_name` field (human-readable display name for the camera)
- `snapshot_message` now always populated (set from `message_template` on `publish_queue` action)

Mapping to Alert fields:
- `camera_name` ← `payload.get('camera_name') or ''`
- `rule_name` ← first item in `rule_results` where `triggered == true`
- `detection_count` ← `roi_summary.total_count`
- `in_roi_count` ← `roi_summary.in_roi_count`
- `cls_summary` ← `roi_summary.cls_counts`

---

## Edge Client API Surface (`edge_client.py`)

Two timeout constants:
- `TIMEOUT = 5` — used for settings/config operations
- `DASHBOARD_TIMEOUT = 2` — used for dashboard page loads to avoid blocking

All calls use `requests.get(url, timeout=...)`. Failures return `None` (logged, not raised).

| Function | Endpoint | Timeout | Returns |
|----------|----------|---------|---------|
| `fetch_edge_info(edge)` | `GET {edge.url}/api/edge-info/` | 5s | dict with name, engine_url, location |
| `fetch_health(edge)` | `GET {edge.engine_url}/health` | 5s | dict with status, active_jobs |
| `fetch_system(edge)` | `GET {edge.engine_url}/system` | 5s | dict with cpu.percent, memory.percent |
| `fetch_jobs(edge)` | `GET {edge.engine_url}/jobs/` | 5s | list of job dicts with job_id, status, camera_id, camera_name |
| `fetch_cameras(edge)` | `GET {edge.url}/api/cameras/` | 5s | dict `{camera_id: camera_name}` |
| `fetch_cameras_detail(edge)` | `GET {edge.url}/api/v1/cameras/` | 5s | list of camera dicts including `mediamtx_relay_enabled` |
| `fetch_edge_data_parallel(edges)` | health + system for all | 2s | `{edge.id: (health, system)}` |

`fetch_edge_data_parallel` uses `ThreadPoolExecutor(max_workers=min(len(edges), 10))` to fetch all edges concurrently, preventing page hangs when some edges are offline.

### `/api/edge-info/` Response Shape
```json
{
  "edge_name": "edge-bardi-home",
  "engine_url": "http://localhost:8001",
  "location": {
    "address": "Jl. Sudirman No. 1",
    "building": "Gedung A",
    "floor": "Lantai 3"
  },
  "cameras": {"total": 4, "active": 3},
  "updated_at": "2026-03-21T10:00:00+00:00"
}
```

`location` is stored as a formatted string in `Edge.location`: `"{address}, {building}, {floor}"`.

### `/api/cameras/` Response Shape (soca-dashboard)
```json
{"1": "Bardi Stairs Front", "2": "Garden Gate", "3": "Lobby"}
```

---

## Pages & Views

| URL | View | Purpose |
|-----|------|---------|
| `/` | `dashboard` | Fleet summary cards + compact edge cards + top cameras chart + recent activity feed |
| `/monitor/` | `monitor` | Grid of live MJPEG streams; placeholder shown when no active jobs |
| `/reports/intrusion/` | `report_intrusion` | Stats + Detail tabs, filters, CSV/PDF export |
| `/reports/ppe/` | `report_ppe` | PPE alerts with per-class violations breakdown |
| `/reports/object-detection/` | `report_object_detection` | Object detection alerts |
| `/reports/crowd/` | `report_crowd` | Crowd detection alerts |
| `/reports/people-counting/` | `report_people_counting` | Line-crossing counts (in/out) |
| `/reports/lpr/` | `report_lpr` | License plate recognition alerts |
| `/settings/` | `settings_page` | Edge CRUD, snapshot storage, GCS credentials |
| `/settings/edges/<id>/` | `edge_edit` | Edit edge: transport, GCS prefix, Pub/Sub config |
| `/settings/snapshot-storage/` | `snapshot_storage_save` | Configure GCS or local snapshot storage |
| `/settings/gac/` | `gac_save` | Upload GCS service account JSON key |
| `/users/` | `user_list` | User management |
| `/users/create/` | `user_create` | Create user |
| `/users/<id>/edit/` | `user_edit` | Edit user |
| `/users/<id>/delete/` | `user_delete` | Delete user |
| `/login/`, `/logout/` | Django built-in | Auth |

---

## Dashboard

Fleet summary (4 stat cards):
1. **Edges Online** — `online / total_edges` (green/warning/red)
2. **Active Jobs** — total running jobs across fleet
3. **Alerts Today** — count with trend vs. yesterday (↑/↓ in red/green)
4. **Alert Breakdown** — today's count by Intrusion / PPE / Object Detection

Edge device cards (compact, scalable grid — 1/2/3/4 cols by breakpoint):
- Colored top strip (green = online, red = offline)
- Edge name + Online/Offline status
- CPU and Memory progress bars (color-coded: green/warning/error thresholds at 60%/85%)
- 3-column alert count box: Intrusion / PPE / OD for today
- Active job count

Bottom row (2 cols):
- **Top Cameras Today** — Chart.js bar chart of top 5 cameras by alert count
- **Recent Activity** — last 10 alerts across fleet (category badge, camera name, message, time)

Dashboard uses `fetch_edge_data_parallel` so offline edges don't block page load.

---

## Report Pages

### Tabs

Each report page (`intrusion`, `ppe`, `object_detection`) has two tabs controlled by `?tab=stats` (default) or `?tab=detail`.

Tab switching preserves all active filter params via `base_qs` (query string without the `tab` param).

### Statistics Tab (`?tab=stats`)

Summary stat cards: today / this week / this month / week trend
Hourly distribution: CSS-based bar chart (0–23h)
14-day trend: CSS-based bar chart
Top cameras table (top 10 by count)
Top rules table (top 10)
Top edges table (top 10)
Average in-ROI count

Two Chart.js bar charts:
1. Incidents by Camera (top 20) — blue
2. Incidents by Edge Device (top 20) — teal

### Detail Tab (`?tab=detail`)

Filterable table (up to 500 rows):
- Timestamp, Edge, Camera Name, Rule, Message, ROI count
- Snapshot thumbnail (if available) — clicking opens snapshot in a shared 960×720 resizable popup window (`snapshot_pop`); all report pages (Intrusion, PPE, Object Detection, People Counting, Crowd, LPR) use the same behavior

Export buttons (CSV / PDF) only shown on Detail tab.

### Filters (`_filters.html`)

| Param | Filters on |
|-------|-----------|
| `edge` | `Alert.edge_name` |
| `camera` | `Alert.camera_id` (dropdown shows camera_name, value is camera_id) |
| `date_from` / `date_to` | `Alert.timestamp` date range |
| `rule` | `Alert.rule_name` |
| `q` | `icontains` search across `rule_name`, `camera_id`, `snapshot_message` |

Camera dropdown deduplication: query uses `order_by('camera_name', 'camera_id').values_list('camera_name', 'camera_id').distinct()` — explicit order_by prevents Django's default ordering from breaking DISTINCT.

Export:
- `?export=csv` → `StreamingHttpResponse` with csv writer
- `?export=pdf` → `HttpResponse` with reportlab (table layout)

---

## Monitor Page

- Calls `fetch_jobs(edge)` for all active edges to get list of running jobs
- For each running job: renders `<img src="{engine_url}/jobs/{job_id}/monitor">` with edge name + camera name label
- If no active jobs on an edge: shows a muted placeholder card
- If edge unreachable: shows placeholder
- Page auto-refreshes every 60s via `<meta http-equiv="refresh">`
- Job list includes `camera_name` populated from `j.config` in soca-engine
- **Maximize button** (expand icon, top-right of each card): opens the MJPEG stream in a controlled popup window (854×480, resizable) — clicking the thumbnail image also opens the same popup; uses `job_id` as the window name so clicking the same stream re-focuses the existing window rather than opening a duplicate

## Assets Page

- Lists all registered cameras per edge, grouped by edge
- Each camera row shows: name, site, floor, location, status, scheduler, **Stream** column
- **Stream column** per camera:
  - `Relay` badge (green) — soca-dashboard's `mediamtx_rtsp_url` is set; detection jobs connect via MediaMTX relay
  - `Direct` badge (yellow) — `mediamtx_rtsp_url` is blank; detection jobs connect directly to camera RTSP
  - Play button — only shown when `Edge.mediamtx_url` is set; opens `{mediamtx_url}/{camera_name}` (MediaMTX built-in HLS player) in a 900×600 resizable popup
- `mediamtx_relay_enabled` flag comes from soca-dashboard `api_v1_cameras` response per camera

---

## Background Workers

### `consume_streams` (Redis)

File: `app/management/commands/consume_streams.py`

```
python manage.py consume_streams
```

- Runs as a long-lived process; filters edges where `subscriber_type='redis'`
- For each edge: opens Redis connection using `edge.redis_url`, reads `edge.redis_stream`
- Uses `XREAD BLOCK 5000` with last consumed message ID (tracked in memory per edge)
- On new message: parses payload JSON → creates `Alert` record
- All string fields use `or ''` / `or {}` guards to handle explicit JSON nulls (NOT NULL constraint safety)
- Reloads edge list every 60s; handles Redis connection errors with 30s retry

### `consume_pubsub` (Google Pub/Sub)

File: `app/management/commands/consume_pubsub.py`

```
python manage.py consume_pubsub
```

- Runs as a long-lived process; filters edges where `subscriber_type='pubsub'`
- For each edge: opens `SubscriberClient` streaming pull on `projects/{pubsub_project_id}/subscriptions/{pubsub_subscription}`
- On new message: parses payload JSON → creates `Alert` record → `message.ack()`
- On JSON decode error: acks and discards (retrying non-JSON won't help)
- On save error: nacks (message retried by Pub/Sub)
- Reloads edge list every 10s; handles subscription errors gracefully
- Both `client` and `future` kept in `subscribers` dict to prevent GC of gRPC channel

### Backfill Management Command

`python manage.py backfill_camera_names`

One-time command to populate `camera_name` on existing Alert records that were stored before the camera_name field was added. Calls `/api/cameras/` on each registered edge and updates matching alerts by `camera_id`.

---

## soca-dashboard Changes Required

These changes to soca-dashboard are needed for soca-control to work fully:

1. **`/api/cameras/` endpoint** — returns `{camera_id_str: camera_name}` dict; used by backfill command and `fetch_cameras()`
2. **`/api/v1/cameras/` endpoint** — returns full camera list with per-camera `mediamtx_relay_enabled` flag; used by Assets page
3. **`snapshot_image` view** — must NOT require login (browser `<img>` tags from soca-control can't authenticate)
4. **`alert_category` in schedule form** — dropdown with fixed values: Intrusion / Detection / PPE (not free text)
5. **`to_job_config()`** — includes `camera_name` and `message_template` in `publish_queue` action so soca-engine can populate `snapshot_message` and `camera_name` in Redis payloads
6. **`EdgeConfig.mediamtx_rtsp_url`** — when set, `Schedule.to_job_config()` produces `rtsp://localhost:8554/<camera_name>` instead of the direct camera URL; `mediamtx_relay_enabled: true` returned in `api_v1_cameras` response

---

## soca-engine Changes Required

1. **`JobConfig`** — added `camera_name: str = ""`
2. **`FrameResult`** — added `camera_name: str` field
3. **`detection_worker`** — passes `camera_name=cfg.camera_name` when constructing `FrameResult`
4. **`action_dispatcher`** — `publish_queue` action sets `result.snapshot_message` from `action.message_template` before publishing
5. **`output_publisher`** — includes `"camera_name": result.camera_name` in Redis payload dict
6. **`/jobs/` API** — job list includes `camera_id` and `camera_name` from `j.config`

---

## Settings: Edge Management

- List registered edges with status dot
- Add edge form: name, dashboard URL, engine URL, Redis URL, stream name
  - On save: calls `fetch_edge_info()` to verify and populate location string
- Deactivate/reactivate toggle (stops stream consumption without deleting)
- Delete edge (also deletes related Alerts)

---

## User Management

- Uses Django's built-in `User` model (no custom model)
- List: username, email, last login, staff flag
- Create: username, email, password, staff checkbox
- Edit: email, new password (optional), staff checkbox
- Delete: confirm prompt; cannot delete self

---

## Authentication

- Django session-based login
- All views: `@login_required`
- Exception: soca-dashboard's `snapshot_image` view is public (required for `<img>` tags in soca-control)
- `LOGIN_URL = '/login/'`

---

## Principles Applied

- **One file per concern** — models, views, edge_client, redis_consumer, reports each in their own file
- **Parallel by default** — dashboard uses ThreadPoolExecutor so one offline edge never blocks the page
- **Camera names over IDs** — all UI shows `camera_name` with `camera_id` as fallback; stored in Alert
- **Standard library first** — `csv` for CSV, `urllib.parse` for URL building
- **No over-engineering** — no REST API, no JS framework beyond DaisyUI+Chart.js, no Celery; Django views + one management command

---

## Verification

1. `pip install -r requirements.txt && python manage.py migrate`
2. `python manage.py createsuperuser`
3. Register an edge in Settings → verify `/api/edge-info/` call succeeds and location populates
4. Run `python manage.py consume_streams` in a second terminal
5. Trigger an alert on the edge → verify it appears in the dashboard and report pages with camera name
6. Verify snapshot thumbnail appears in Detail tab of report pages
7. Test Statistics tab: hourly chart, 14-day trend, top cameras/rules/edges, Chart.js bar charts
8. Test CSV export: `/reports/intrusion/?tab=detail&export=csv`
9. Test PDF export: `/reports/intrusion/?tab=detail&export=pdf`
10. Verify live monitor loads MJPEG `<img>` tags with camera name labels
11. Disconnect an edge → verify dashboard loads without hanging (parallel fetch)
12. Run `python manage.py backfill_camera_names` to populate camera names on existing alerts
