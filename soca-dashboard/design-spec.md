# SOCA Dashboard — Technical Document

## Context

`soca-engine` is a FastAPI service that runs YOLO-based object detection jobs on RTSP camera streams. It stores results in SQLite and exposes a REST API. There is no visual interface — operators cannot see what the engine is doing, manage jobs, or review detection events without curling the API manually. This dashboard solves that.

---

## Problem Statement

Operators running soca-engine on edge devices need a web UI to:
- Know if the engine is healthy and what it is doing right now
- Start/stop detection jobs on cameras
- Watch a live annotated stream from a running job
- Review detection events and snapshots after the fact

---

## soca-engine API Surface (consumed by dashboard)

| Method | Endpoint | Returns |
|--------|----------|---------|
| GET | `/health` | `{status, redis, active_jobs, uptime_seconds}` |
| GET | `/models` | `{models: [{name, path}]}` |
| GET | `/jobs/` | list of last 50 jobs |
| POST | `/jobs/start` | start job with `JobConfig` body |
| POST | `/jobs/{id}/stop` | stop running job |
| GET | `/jobs/{id}` | job status + counters |
| GET | `/jobs/{id}/monitor` | MJPEG stream (requires `monitor=true` at start) |

**JobConfig required fields**: `camera_id`, `rtsp_url`, `model_path`, `cls_ids`
**Optional**: `roi`, `frame_interval_ms`, `aging`, `rules`, `output.redis_url`, `monitor` (bool)

**No events/frames API yet** — the engine stores `DBEvent` and `DBFrame` in SQLite but does not expose them via HTTP. Dashboard reads what's available from job status counters only (MVP scope).

---

## MVP Features

1. **Health bar** — always-visible strip: engine status, redis, active jobs, uptime
2. **Job list** — table of last 50 jobs with status badge and stop button
3. **Start job form** — minimal form: camera_id, rtsp_url, model selector (from `/models`), cls_ids, frame_interval, enable monitor toggle
4. **Job detail page** — job counters (frames, events, last_frame_at) + live MJPEG viewer (if monitor enabled)
5. **Model list** — sidebar or simple page showing available YOLO models on the engine

**Out of MVP**: ROI drawing, rule builder, event log (no API endpoint yet), multi-engine support, auth

---

## User Flow

```
[Open dashboard]
    → GET /health displayed in header
    → GET /jobs/ displayed as table

[Start a job]
    → Click "New Job" → form page
    → Fill: camera_id, rtsp_url, pick model from dropdown, cls_ids, interval
    → Toggle "Enable live monitor"
    → Submit → POST /jobs/start → redirect to job detail

[Monitor live]
    → Job detail page: <img src="/jobs/{id}/stream/">
    → Django proxy view pipes MJPEG from engine to browser

[Stop a job]
    → Click "Stop" in job list or job detail → POST /jobs/{id}/stop/ → redirect

[Inspect a job]
    → Job detail: status, frames_processed, events_triggered, last_frame_at, error_msg
```

---

## Django Project Structure

```
soca-dashboard/
├── manage.py
├── requirements.txt
├── dashboard/               # project config
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── monitor/                 # single app, one file per concern
    ├── client.py            # thin wrapper around soca-engine HTTP calls
    ├── forms.py             # StartJobForm (no model, just form fields)
    ├── views.py             # all views (index, job list, job detail, start, stop, proxy)
    ├── urls.py
    └── templates/monitor/
        ├── base.html        # nav + health bar
        ├── index.html       # dashboard home (health + job table)
        ├── job_form.html    # start job form
        └── job_detail.html  # counters + live stream img tag
```

**No Django models** — no local DB. The engine owns all data.
**No DRF** — dashboard is a consumer, not an API provider.
**No JS framework** — vanilla HTML forms + one `<img>` tag for MJPEG. Auto-refresh via `<meta http-equiv="refresh">` on list page.

---

## Key Implementation Rules

- `client.py`: all `requests` calls in one file, each function maps 1:1 to an API endpoint, raises on HTTP error
- `views.py`: one view per page, thin — call client, pass data to template, done
- `forms.py`: single `StartJobForm` with clean validation only
- Templates: extend `base.html`, no inline JS, semantic HTML
- MJPEG proxy: streaming `HttpResponse` that reads chunks from engine and yields them — no buffering
- Engine base URL from `settings.ENGINE_BASE_URL` (env var `ENGINE_BASE_URL`, default `http://<edge-ip>:8001`)
- `requests.Session` reused in `client.py` (module-level singleton)

---

## Files Created

| File | Purpose |
|------|---------|
| `requirements.txt` | django, requests, python-dotenv |
| `dashboard/settings.py` | standard django settings + ENGINE_BASE_URL |
| `dashboard/urls.py` | include monitor.urls |
| `monitor/client.py` | `health()`, `list_jobs()`, `get_job()`, `list_models()`, `start_job()`, `stop_job()`, `stream_monitor()` |
| `monitor/forms.py` | `StartJobForm` |
| `monitor/views.py` | `index`, `job_detail`, `start_job_view`, `stop_job_view`, `monitor_proxy` |
| `monitor/urls.py` | URL patterns |
| `monitor/templates/monitor/base.html` | layout + health bar |
| `monitor/templates/monitor/index.html` | job table (auto-refresh 10s) |
| `monitor/templates/monitor/job_form.html` | start form |
| `monitor/templates/monitor/job_detail.html` | detail + stream (auto-refresh 5s when running) |

---

## Verification

```bash
# 1. Start engine (in soca-engine/)
uvicorn main:app --port 8001

# 2. Start dashboard (in soca-dashboard/)
pip install -r requirements.txt
python manage.py runserver 8000

# 3. Visit http://<soca-control-ip>:8000
#    - Health bar shows engine status
#    - Start a job with monitor=true
#    - Navigate to job detail — MJPEG stream renders
#    - Stop job — status changes to stopped
```

---

---

## Sprint 2 — Added Features

### Authentication
- Django built-in `LoginView` / `LogoutView`
- All views protected with `@login_required`
- Custom login page: `templates/registration/login.html`
- Create user: `python manage.py createsuperuser`
- Settings: `LOGIN_URL`, `LOGIN_REDIRECT_URL`, `LOGOUT_REDIRECT_URL`

### Cameras App (`cameras/`)
- **Model**: `Camera` — name, site_name, floor, location, rtsp_url, username, password, camera_source, is_active
- **Views**: list, create, edit, delete (all `@login_required`)
- **URLs**: `/cameras/`, `/cameras/new/`, `/cameras/<pk>/edit/`, `/cameras/<pk>/delete/`
- **DB**: stored in `dashboard.db` (Django SQLite)

### Schedules App (`schedules/`)
- **Model**: `DetectionSchedule` — FK to Camera, model_path, cls_ids (JSON), frame_interval_ms, monitor, is_active, current_job_id
- **Views**: list (filterable by camera), create, edit, delete, start, stop
- **Start** → calls `client.start_job()` with camera's rtsp_url + schedule config, stores `current_job_id`
- **Stop** → calls `client.stop_job(current_job_id)`, clears `current_job_id`
- **URLs**: `/schedules/`, `/schedules/new/`, `/schedules/<pk>/edit/`, `/schedules/<pk>/start/`, `/schedules/<pk>/stop/`

### Snapshots App (`snapshots/`)
- **Source**: reads directly from `ENGINE_DB_PATH` (`soca-engine/soca_engine.db`) via `sqlite3` stdlib
- **View**: paginated grid (30/page), filterable by job_id
- **Image serving**: `/snapshots/img/<path>` — reads file from `ENGINE_SNAPSHOTS_DIR`, prevents path traversal
- **No model** — read-only access to engine DB

### Engine Health Context Processor (`monitor/context_processors.py`)
- `engine_health(request)` — called on every request, injects `health` dict into all templates
- Registered in `settings.TEMPLATES.context_processors`

### New Settings
```
ENGINE_DB_PATH          # path to soca-engine/soca_engine.db
ENGINE_SNAPSHOTS_DIR    # path to soca-engine/snapshots/
```

### Run
```bash
python manage.py migrate
python manage.py createsuperuser
ENGINE_BASE_URL=http://<edge-ip>:8001 python manage.py runserver 8000
```

---

## Post-MVP Backlog

- **Event log page** — requires soca-engine to expose `/jobs/{id}/events` endpoint
- **ROI drawing** — canvas-based polygon editor posted with job config
- **Rule builder** — form UI for `when_all`/`when_any` conditions
- **Snapshot gallery** — browse `snapshots/` directory served by engine
- **Multi-engine** — ENGINE_BASE_URL as a per-session setting
- **Auth** — Django sessions + basic login

---

## Sprint 3 — Full Rebuild as Administration Dashboard

Sprint 3 is a complete rewrite of soca-dashboard into a production-grade administration UI for soca-engine. The old `monitor/`, `cameras/`, and `schedules/` multi-app structure is replaced by a single `app/` Django application with a 5-item left sidebar.

### Architecture Changes

#### Project Layout

```
soca-dashboard/
├── manage.py
├── requirements.txt          # added ruamel.yaml>=0.18
├── mediamtx.py               # NEW: MediaMTX YAML management module
├── engine_client.py          # NEW: thin soca-engine HTTP wrapper
├── dashboard/
│   ├── settings.py           # rewritten (see below)
│   └── urls.py               # simplified to single include("app.urls")
└── app/                      # single app replacing monitor/cameras/schedules
    ├── models.py             # User, EdgeConfig, Camera, Schedule
    ├── views.py              # all views in one file
    ├── urls.py               # 19 URL patterns
    └── templates/
        ├── base.html
        ├── dashboard/index.html
        ├── cameras/list.html, form.html
        ├── schedules/list.html, form.html
        ├── alerts/list.html
        └── settings/index.html
```

#### settings.py

- `AUTH_USER_MODEL = "app.User"` — custom user model required from the start
- `LOGIN_URL = "/login/"`, `LOGIN_REDIRECT_URL = "/"`, `LOGOUT_REDIRECT_URL = "/login/"`
- New settings:
  ```python
  ENGINE_BASE_URL      # default "http://<edge-ip>:8001"
  ENGINE_DB_PATH       # path to soca-engine/soca_engine.db
  ENGINE_SNAPSHOTS_DIR # path to soca-engine/snapshots/
  MEDIAMTX_YML_PATH    # kept for legacy; overridden by EdgeConfig.mediamtx_yml_path
  ```
- Removed old `monitor`, `cameras`, `schedules` from `INSTALLED_APPS`; added `app`

---

### Models (`app/models.py`)

#### `User(AbstractUser)`
Plain custom user model for future extensibility.

#### `EdgeConfig`
Stores edge-device connection config. One row is expected.

| Field | Default | Notes |
|-------|---------|-------|
| `edge_name` | `"edge-1"` | Written to `EDGE_NAME` in soca-engine `.env` on save |
| `engine_url` | `"http://<edge-ip>:8001"` | soca-engine FastAPI base URL |
| `mediamtx_url` | `"http://<edge-ip>:8888"` | MediaMTX HTTP API URL |
| `mediamtx_yml_path` | `"../soca-engine/MediaMTX/mediamtx.yml"` | Relative to `BASE_DIR` |
| `engine_env_path` | `"../soca-engine/.env"` | Relative to `BASE_DIR`; EDGE_NAME written here |
| `updated_at` | auto | — |

#### `Camera`
Stores camera connection details.

| Field | Notes |
|-------|-------|
| `name` | unique, used as MediaMTX path key |
| `rtsp_url` | base URL without credentials |
| `username`, `password` | stored separately, never embedded in rtsp_url |
| `full_rtsp_url` (property) | builds `rtsp://user:pass@host:port/path` at access time using `urllib.parse` |

**`full_rtsp_url` property** — injects credentials only when `username` is non-empty:
```python
@property
def full_rtsp_url(self):
    if not self.username:
        return self.rtsp_url
    parsed = urlparse(self.rtsp_url)
    netloc = f"{quote(self.username, safe='')}:{quote(self.password, safe='')}@{parsed.hostname}"
    if parsed.port:
        netloc += f":{parsed.port}"
    return urlunparse(parsed._replace(netloc=netloc))
```

This property is used in two places:
- `Schedule.to_job_config()` — sends credentialed URL to soca-engine
- `camera_snapshot()` view — grabs a single frame for the ROI editor background

#### `Schedule`
One schedule = one detection job configuration. Start/stop maps directly to soca-engine job lifecycle.

| Field | Notes |
|-------|-------|
| `camera` | FK to Camera |
| `model_path` | relative to soca-engine MODELS_DIR, e.g. `"yolo/yolo11n.pt"` |
| `cls_ids` | JSON list of YOLO class IDs to track |
| `frame_interval_ms` | sampling rate |
| `roi_type` | `"POLYGON"` or `"RECT"` |
| `roi_points` | JSON list of normalized `[x, y]` pairs (0.0–1.0 range) |
| `aging_window`, `aging_cooldown` | StateTracker config |
| `min_count` | minimum in-ROI detections to trigger rule |
| `save_snapshot`, `publish_redis`, `telegram_webhook_url` | output action toggles |
| `redis_url`, `redis_stream` | Redis connection for publish_queue action |
| `enable_monitor` | enables MJPEG stream on running job |
| `current_job_id` | populated on start, cleared on stop |

**`to_job_config()`** builds the `JobConfig` dict for `POST /jobs/start`:
- Uses `self.camera.full_rtsp_url` (credentialed)
- ROI condition path: `"detections.in_roi_count"` (dot-path resolution in rule_engine)
- Rule `when_all`: `[{"path": "detections.in_roi_count", "op": "gte", "value": self.min_count}]`

---

### `mediamtx.py` — MediaMTX YAML Manager

Standalone module (not a Django app). Manages the `paths:` section of `mediamtx.yml` using `ruamel.yaml` to preserve comments and formatting.

| Function | Description |
|----------|-------------|
| `add_source(yml_path, camera_name, rtsp_url, username, password)` | Adds a new path entry |
| `update_source(yml_path, old_name, new_name, rtsp_url, username, password)` | Renames + updates existing entry |
| `remove_source(yml_path, camera_name)` | Deletes path entry |

**Credential injection** (`_build_rtsp_url`):
```python
def _build_rtsp_url(rtsp_url, username, password):
    if not username:
        return rtsp_url
    parsed = urlparse(rtsp_url)
    netloc = f"{quote(username, safe='')}:{quote(password, safe='')}@{parsed.hostname}"
    if parsed.port:
        netloc += f":{parsed.port}"
    return urlunparse(parsed._replace(netloc=netloc))
```

Credentials are injected inside `mediamtx.py`, not in views. Views pass `username` and `password` as separate arguments.

**Dependency**: `ruamel.yaml>=0.18` — must be installed in the same virtualenv as soca-dashboard.

---

### `engine_client.py` — soca-engine HTTP Wrapper

Thin HTTP client. Engine base URL is read from `EdgeConfig` first, falls back to `settings.ENGINE_BASE_URL`.

| Function | Maps to |
|----------|---------|
| `health()` | `GET /health` |
| `list_jobs()` | `GET /jobs/` |
| `get_job(job_id)` | `GET /jobs/{id}` |
| `start_job(config)` | `POST /jobs/start` |
| `stop_job(job_id)` | `POST /jobs/{id}/stop` |
| `list_models()` | `GET /models` |

---

### `views.py` — Key Behaviours

#### Path Resolution Helpers
```python
def _yml_path(cfg):   # mediamtx_yml_path → absolute, anchored to BASE_DIR
def _env_path(cfg):   # engine_env_path → absolute, anchored to BASE_DIR
```

Both resolve relative paths against `settings.BASE_DIR` (the `soca-dashboard/` directory). This makes paths CWD-independent regardless of where `manage.py runserver` is invoked from.

#### `_write_engine_env(engine_env_path, edge_name)`
Reads the soca-engine `.env` file, updates or appends `EDGE_NAME=<value>`, writes back. Called on every EdgeConfig save.

#### `camera_snapshot(pk)`
Grabs a single OpenCV frame from `camera.full_rtsp_url`, returns JPEG `HttpResponse`. Used as the ROI canvas background in the schedule form.

#### `schedule_stop`
Calls `engine_client.stop_job()`. If the engine returns 404 (job already dead), the error is ignored and `current_job_id` is cleared anyway. This prevents schedules from getting stuck in "running" state after an engine restart.

#### `alerts` view
Reads soca-engine SQLite directly via `sqlite3` stdlib (no ORM). Queries `detection_events` joined to `detection_jobs` for camera name. Paginated, filterable by camera and date.

---

### Templates

#### ROI Canvas Editor (`schedules/form.html`)

- HTML5 `<canvas>` at 640×360 pixels
- On camera dropdown change: `loadSnapshot(cameraId)` → fetches `/cameras/{id}/snapshot/` → draws as canvas background
- Points are stored as **normalized coordinates** (0.0–1.0):
  - On click: pixel coords divided by canvas width/height before appending to `points[]`
  - On page load: existing normalized points multiplied by canvas dimensions for display
  - On form submit: `storeNormalized()` writes JSON to hidden `<input>`
- Init sequence: calls `draw()` directly — does **not** call `setRoiMode()` (which would reset `points[]`)
- Supports both POLYGON (click to place vertices) and RECT (drag rectangle) modes

#### Schedule List (`schedules/list.html`)

- Monitor link is conditionally rendered:
  ```django
  {% if s.job_status == "running" and s.enable_monitor %}
    <a href="{{ engine_url }}/jobs/{{ s.current_job_id }}/monitor">Live</a>
  {% endif %}
  ```
- `engine_url` is passed from `schedule_list` view via `EdgeConfig.engine_url`

#### Settings (`settings/index.html`)

- Edge Config form includes `engine_env_path` field with hint: "EDGE_NAME will be written here when you save"
- User management section: create/delete users

---

### URL Patterns (`app/urls.py`)

| URL | View | Notes |
|-----|------|-------|
| `/` | `dashboard` | system overview |
| `/login/`, `/logout/` | built-in auth | |
| `/cameras/` | `camera_list` | |
| `/cameras/new/`, `/cameras/<pk>/edit/`, `/cameras/<pk>/delete/` | CRUD | |
| `/cameras/<pk>/snapshot/` | `camera_snapshot` | returns JPEG |
| `/schedules/` | `schedule_list` | enriched with job status |
| `/schedules/new/`, `/schedules/<pk>/edit/`, `/schedules/<pk>/delete/` | CRUD | |
| `/schedules/<pk>/start/`, `/schedules/<pk>/stop/` | job lifecycle | |
| `/schedules/<pk>/status/` | `schedule_status` | JSON, for polling |
| `/alerts/` | `alerts` | reads engine SQLite directly |
| `/alerts/snapshot/<path>/` | `alert_snapshot` | serves snapshot image |
| `/settings/` | `settings_index` | edge config + users |
| `/settings/users/new/`, `/settings/users/<pk>/delete/` | user CRUD | |
| `/settings/edge/` | `edge_settings` | saves EdgeConfig, writes .env |

---

### Bug Fixes Applied During Sprint 3

| Bug | Root Cause | Fix |
|-----|------------|-----|
| Dashboard `VariableDoesNotExist` on `job.id` | Template used `job.id` but API returns flat `job_id` key | Updated template to use `job.job_id` and `job.camera_id` |
| Camera not written to `mediamtx.yml` | `mediamtx_yml_path` seeded to wrong directory; `_yml_path()` not anchored | Seeded correct path; anchored resolution to `BASE_DIR` |
| `no such column: app_edgeconfig.engine_env_path` | Server still running before migration applied | Restart server after migration |
| `ruamel.yaml` not found | Installed in base Python, not in `soca` virtualenv | `pip install ruamel.yaml` inside active venv |
| RTSP 401 Unauthorized | `to_job_config()` used bare `rtsp_url` without credentials | Switched to `camera.full_rtsp_url` property |
| Model path sending full dict | Template `value="{{ m }}"` serialized entire dict object | Changed to `value="{{ m.path }}"` |
| ROI not saved on form submit | `setRoiMode()` called during init reset `points[]` after loading | Replace init call with direct `draw()` |
| No snapshots captured | `OutputConfig.snapshot_dir` defaulted to relative `"snapshots/"` | Fixed in soca-engine `schemas.py` via `config.SNAPSHOTS_DIR` |
| Stop job stuck in running state | 404 from engine not handled; `current_job_id` not cleared | Ignore 404, always clear `current_job_id` |

---

### Dependencies Added

| Package | Version | Purpose |
|---------|---------|---------|
| `ruamel.yaml` | `>=0.18` | MediaMTX YAML editing preserving comments |

---

### Run (Sprint 3)

```bash
# Install dependencies (in soca-dashboard venv)
pip install -r requirements.txt

# Apply migrations
python manage.py migrate

# Create superuser
python manage.py createsuperuser

# Start dashboard
python manage.py runserver 8000

# Verify edge config at http://<soca-control-ip>:8000/settings/edge/
# Set engine_url, mediamtx_yml_path, engine_env_path to correct absolute/relative paths
```

---

## Sprint 4 — Alert Categories, Message Templates, Telegram & Redis Schema 2.2

### Overview

Sprint 4 adds per-schedule alert enrichment (category label, message template), a global Telegram Bot configuration, and ensures all new metadata flows through to the Redis stream payload.

---

### New `Schedule` Fields (`app/models.py`)

| Field | Type | Default | Notes |
|-------|------|---------|-------|
| `alert_category` | `CharField(max_length=255)` | `""` | Label attached to every alert from this schedule |
| `snapshot_message` | `TextField` | `"{in_roi_count} object(s) detected at {time}"` | Message template sent via Telegram or webhook |

**`snapshot_message` template placeholders:**

| Placeholder | Value |
|-------------|-------|
| `{count}` | Total detections in frame |
| `{in_roi_count}` | Detections inside ROI |
| `{time}` | Timestamp in GMT+7 (`YYYY-MM-DD HH:MM:SS GMT+7`) |
| `{camera_id}` | Camera ID |
| `{job_id}` | Job ID |
| `{category}` | Alert category from this schedule |

---

### New `EdgeConfig` Fields (`app/models.py`)

| Field | Type | Default | Notes |
|-------|------|---------|-------|
| `telegram_bot_token` | `CharField(max_length=200)` | `""` | Bot token from @BotFather |
| `telegram_chat_id` | `CharField(max_length=100)` | `""` | Group/channel (negative) or personal ID |

These serve as the **global Telegram config** shared by all schedules that don't set a per-schedule `telegram_webhook_url`.

---

### `Schedule.to_job_config()` — Telegram Action Logic

Priority order for outbound Telegram notifications:

1. If `schedule.telegram_webhook_url` is set → add `webhook` action with the URL and `message_template`
2. Else if `EdgeConfig.telegram_bot_token` and `telegram_chat_id` are set → add `telegram` action with global credentials and `message_template`
3. Neither → no Telegram notification

The `telegram` action in `JobConfig` triggers the engine's `_fire_telegram()` which calls `https://api.telegram.org/bot{token}/sendMessage` directly with the formatted text.

The `alert_category` is passed as `rule.category` in the generated job config.

---

### `app/views.py` Changes

- **`schedule_form`**: saves `alert_category` (truncated to 255 chars) and `snapshot_message`
- **`edge_settings`**: saves `telegram_bot_token` and `telegram_chat_id`
- **`alerts` view**: timestamps from `detection_events` (stored UTC) are converted to **GMT+7** before rendering:
  ```python
  dt.astimezone(timezone(timedelta(hours=7))).strftime("%Y-%m-%d %H:%M:%S")
  ```

---

### Template Changes

#### `schedules/form.html`

**Section 4 — Aging & Alert Rules** updated:
- Aging Window and Cooldown labels now include `seconds` unit badge and clarifying hints: "How long a detection stays active" / "Minimum gap between consecutive alerts"
- New **Alert Category** text input (max 255 chars, placeholder: `e.g. Intrusion, Crowd, Vehicle`)

**Section 5 — Output & Notifications** updated:
- Telegram Webhook URL hint updated to explain relationship with global config
- New **Alert Message Template** `<textarea>` with inline placeholder reference:
  - `{count}` · `{in_roi_count}` · `{time}` · `{camera_id}` · `{category}`

#### `settings/index.html`

- Added **Telegram** tab (third tab alongside Users and Edge Config)
- Telegram tab form posts to `{% url 'edge_settings' %}` with hidden fields preserving existing EdgeConfig values
- Two new fields: **Bot Token** and **Chat ID** with descriptive hints
- Note explaining that per-schedule webhook URL takes priority over this global config

---

### Migrations

Migration `0003_edgeconfig_telegram_bot_token_and_more.py` adds:
- `telegram_bot_token` to `EdgeConfig`
- `telegram_chat_id` to `EdgeConfig`
- `alert_category` to `Schedule`
- `snapshot_message` to `Schedule`

---

### New `Schedule` Field — `conf_threshold`

| Field | Type | Default | Notes |
|-------|------|---------|-------|
| `conf_threshold` | `FloatField` | `0.5` | YOLO detection confidence threshold passed to soca-engine |

- Rendered in `schedules/form.html` as a number input (min 0.01, max 1.0, step 0.05)
- Passed to `JobConfig.conf_threshold` in `to_job_config()`
- Clamped to `0.01–1.0` range in `schedule_form` view before saving

---

### Alerts & Snapshots Template Changes (`alerts/list.html`)

- **Removed columns**: Rule, Job ID
- **Added columns**: Category (badge), Message
- **Category** displayed as `badge-info` badge
- **Message** column shows `snapshot_message` text
- **Snapshot thumbnail** has `title="{{ ev.snapshot_message }}"` tooltip for quick preview
- Column header updated to **Timestamp (GMT+7)**

---

### Dashboard — Running Schedules Only

`dashboard` view filters job list to running state only:
```python
jobs = [j for j in engine_client.list_jobs() if j.get("status") == "running"]
```
- Section renamed from "Active Jobs" to "Running Schedules"
- Shows 0 rows when no jobs are running rather than completed/stopped history

---

### Data Flow Summary

```
Schedule form saved
  → alert_category, snapshot_message, conf_threshold stored in Schedule
  → EdgeConfig holds global telegram_bot_token + telegram_chat_id

Schedule.to_job_config() called at job start
  → rule.category = schedule.alert_category
  → JobConfig.conf_threshold = schedule.conf_threshold
  → rule.actions includes:
       save_snapshot
       publish_queue         (if enabled)
       telegram / webhook    (with message_template = schedule.snapshot_message)

soca-engine ActionDispatcher:
  → result.alert_category ← rule.category
  → _format_message(template, result) → fills placeholders → result.snapshot_message
  → snapshot JPEG annotated with ROI overlay + detection bboxes
  → _fire_telegram(token, chat_id, text, snapshot_path) → sendPhoto if file exists
  → output_publisher._to_dict() → includes alert_category + snapshot_message in Redis payload

Alerts view:
  → UTC timestamp from detection_events → converted to GMT+7 for display
  → alert_category shown as badge, snapshot_message shown as column text
```

---

## Sprint 5 — Monitor Page & Dashboard Refinements

### Monitor Page (`/monitor/`)

New page showing all running MJPEG streams in a responsive grid.

**View (`monitor_view` in `app/views.py`):**
1. Queries all `Schedule` objects where `current_job_id` is non-empty and `enable_monitor=True`
2. For each, calls `engine_client.get_job(job_id)` to verify status is still `"running"`
3. Builds stream URL as `{engine_url}/jobs/{job_id}/monitor`
4. Passes list of stream info dicts to template

```python
def monitor_view(request):
    cfg = EdgeConfig.objects.first()
    engine_url = cfg.engine_url if cfg else settings.ENGINE_BASE_URL
    streams = []
    for s in Schedule.objects.filter(current_job_id__isnull=False, enable_monitor=True).exclude(current_job_id=""):
        try:
            job = engine_client.get_job(s.current_job_id)
            if job.get("status") == "running":
                streams.append({
                    "schedule_name": s.name,
                    "camera_name": s.camera.name,
                    "job_id": s.current_job_id,
                    "stream_url": f"{engine_url}/jobs/{s.current_job_id}/monitor",
                })
        except Exception:
            continue
    return render(request, "monitor.html", {"streams": streams})
```

**Template (`templates/monitor.html`):**
- Responsive grid: 1 col → 2 col (md) → 3 col (xl)
- Each card shows: schedule name, camera name, LIVE badge (animated pulse), `<img src="stream_url">` (MJPEG consumed directly), fullscreen link
- Empty state when no streams are running

**URL:** `path("monitor/", views.monitor_view, name="monitor")`

**Sidebar:** Monitor menu item added between Schedules and Alerts & Snapshots in `templates/base.html`, with monitor icon (desktop/screen SVG), active class on `monitor` URL name.

---

### Stream Rendering

The `<img>` tag consuming the MJPEG stream:
```html
<img src="{{ s.stream_url }}"
     class="w-full rounded-lg border border-base-300 bg-black"
     style="aspect-ratio:16/9; object-fit:contain;"
     onerror="..." />
```
- Browser requests the MJPEG endpoint directly from soca-engine (no Django proxy needed)
- `onerror` handler replaces broken img with "Stream unavailable" text
- No JavaScript polling — MJPEG is a continuous HTTP response the browser handles natively

---

### URL Pattern Added

| URL | View | Notes |
|-----|------|-------|
| `/monitor/` | `monitor_view` | Grid of live MJPEG stream cards |

---

## Sprint 6 — System Resources, Theme Toggle, Edge Site Info & Public API

### Theme Toggle

- Sun/moon icon button added to the navbar (next to username)
- Clicking toggles between `data-theme="dark"` and `data-theme="light"` on `<html>`
- Preference persisted in `localStorage` and restored on every page load
- No server-side state — pure client JS in `base.html`

---

### System Resources on Dashboard

`engine_client.py` gained two new functions:

| Function | Endpoint | Returns |
|----------|----------|---------|
| `system_info()` | `GET /system` | CPU %, memory, storage, network interfaces |
| `cpu_info()` | `GET /cpu` | CPU model, core count, frequency, per-core usage |

`dashboard` view calls both and passes `system` and `cpu` to the template.

**Dashboard layout** (collapsible `<details>` sections):
- **Stat cards row** — Total Cameras, Active Schedules, Engine status, Uptime. Redundant "Engine Health Detail" card removed.
- **System Resources** (open by default) — 3-column panel with dividers: CPU / Memory / Storage. Each shows large bold % + progress bar + detail text. CPU includes model, core info, frequency, per-core bars.
- **Network Interfaces** (collapsed by default) — table: interface name, IP, UP/DOWN badge, speed, sent MB, received MB. Only interfaces with an IPv4 address shown.
- **Running Schedules** (open by default) — collapsible table of running jobs.

Progress bar colour thresholds: green < 60%, yellow 60–80%, red > 80%.

---

### `GET /api/edge-info/` — Public Edge Identity API

No authentication required. Intended for consumption by a central management server.

**URL:** `GET /api/edge-info/`

**Response:**
```json
{
  "edge_name": "edge-bardi-home",
  "engine_url": "http://<edge-ip>:8001",
  "location": {
    "latitude": -6.2088,
    "longitude": 106.8456,
    "address": "Jl. Sudirman No. 1, Jakarta",
    "building": "Gedung A",
    "floor": "Lantai 3",
    "notes": ""
  },
  "cameras": {
    "total": 4,
    "active": 3
  },
  "updated_at": "2026-03-21T10:00:00+00:00"
}
```

Data is sourced from `EdgeConfig` (single row) and live `Camera` queryset counts.

---

### New `EdgeConfig` Fields (migration `0005`)

| Field | Type | Notes |
|-------|------|-------|
| `latitude` | `FloatField(null=True)` | GPS latitude |
| `longitude` | `FloatField(null=True)` | GPS longitude |
| `address` | `CharField(500)` | Street address |
| `building` | `CharField(200)` | Building name |
| `floor` | `CharField(50)` | Floor identifier |
| `site_notes` | `TextField` | Free-form notes |

---

### Settings Page — Edge Config Tab

Reorganised into two sections:

**Connection** (existing fields): Edge Name, Engine URL, MediaMTX URL, MediaMTX Config Path, Engine .env Path

**Site Information** (new fields): Address, Building, Floor, Latitude, Longitude, Notes. Section header notes the `/api/edge-info/` URL so operators know where the data is exposed.

Telegram tab hidden fields updated to preserve new location fields when saving from that tab.

---

### Alerts Auto-Reload

- Toggle switch added to Alerts & Snapshots header (off by default)
- When enabled: 30-second countdown shown inline, page reloads at 0
- Dead empty `{% for %}` loop in pagination removed

---

### Monitor Page — Fixes & Auto-Reload

- Stream `<img>` gets `?t={timestamp}` cache-buster on page load — forces fresh MJPEG connection when navigating back to the page
- `onerror` retries every 3 seconds with new timestamp
- Page auto-reloads every 30 seconds with visible countdown (`Refreshing in Ns`) to pick up newly started/stopped streams

---

### URL Patterns Added

| URL | View | Notes |
|-----|------|-------|
| `/api/edge-info/` | `edge_info` | Public — no auth, for main server |

---

## Sprint 7 — Google Pub/Sub Publisher Transport (2026-03-25)

### Overview

Added Pub/Sub as an alternative to Redis for publishing detection events from soca-engine. The transport is configurable per-edge via the Edge Config settings tab, and the choice is written directly to `soca-engine/.env`.

---

### New `EdgeConfig` Fields (`app/models.py`)

| Field | Type | Default | Notes |
|-------|------|---------|-------|
| `publisher_type` | `CharField(10)` | `'redis'` | `'redis'` or `'pubsub'` |
| `pubsub_project_id` | `CharField(200)` | `''` | GCP project ID |
| `pubsub_topic` | `CharField(200)` | `'soca-detections'` | Topic name only (not full path) |

Migration: `app/migrations/0017_edgeconfig_pubsub.py`

---

### `_write_engine_env()` — Updated Signature

```python
def _write_engine_env(engine_env_path, edge_name, publisher_type="redis",
                      pubsub_project_id="", pubsub_topic="soca-detections"):
```

Now writes four vars to soca-engine `.env`: `EDGE_NAME`, `PUBLISHER_TYPE`, `PUBSUB_PROJECT_ID`, `PUBSUB_TOPIC`. Uses a dict-based loop to update-or-append each key, preserving all other existing `.env` lines.

---

### Settings UI (`templates/settings/index.html`)

**Edge Config tab additions:**

- **Publisher Transport** `<select>` (Redis Stream / Google Pub/Sub) — calls `togglePubSubFields(val)` on change
- **Pub/Sub fields** (shown only when Pub/Sub selected): GCP Project ID input, Pub/Sub Topic input
- Both wrapped in `id="pubsub-fields"` div with `display:none` default when Redis is selected

**Telegram tab** — added three hidden inputs to preserve new values when saving from that tab:
```html
<input type="hidden" name="publisher_type" ...>
<input type="hidden" name="pubsub_project_id" ...>
<input type="hidden" name="pubsub_topic" ...>
```

**JS function added:**
```javascript
function togglePubSubFields(val) {
  document.getElementById('pubsub-fields').style.display = val === 'pubsub' ? '' : 'none';
}
```
