# Edge Operations Design Spec
**Date:** 2026-03-22
**Project:** soca-dashboard + soca-control

## Context

soca-dashboard manages a single edge device. As deployments grow and data accumulates, operators need:
1. A way to purge aging detection data and free disk space
2. A machine-readable API so soca-control can remotely manage schedules and trigger purges
3. A dedicated UI section in Settings to expose these operations

---

## Scope

Three coordinated deliverables:

| Deliverable | Location |
|-------------|----------|
| Purge tool (local + remote) | soca-dashboard |
| REST API v1 for soca-control | soca-dashboard |
| Settings > Operations tab | soca-dashboard |
| Schedule remote control + purge trigger | soca-control |

---

## 1. Data Model Changes

### soca-dashboard: `EdgeConfig`

Add two fields:

```python
api_key = models.CharField(max_length=64, blank=True, default='')
# Generated via secrets.token_hex(32); stored plain text (private network only)

engine_db_path = models.CharField(max_length=500, blank=True, default='')
# Absolute path to soca-engine's SQLite file, e.g. "/opt/soca-engine/db.sqlite3"
# Shown in Settings > Edge Config as a plain text field (same section as engine_env_path)
# The snapshots root is derived: parent dir of the first snapshot_path in detection_events,
# or stored as a sibling field (see Section 2).
```

Also add:

```python
snapshots_root = models.CharField(max_length=500, blank=True, default='')
# Absolute path to the snapshots directory on this edge, e.g. "/opt/soca-engine/snapshots"
# Used by purge to find and delete snapshot files
```

Both `engine_db_path` and `snapshots_root` are added to the existing **Settings > Edge Config** tab form (not the Operations tab).

### soca-control: `Edge`

Add one field:

```python
api_key = models.CharField(max_length=64, blank=True, default='')
# Operator pastes this from the target edge's Settings > Operations tab
```

---

## 2. Purge Logic

### What Gets Purged (soca-dashboard)

soca-dashboard reads detection data directly from **soca-engine's SQLite** file at `EdgeConfig.engine_db_path`. Purge targets:

1. **`detection_events` rows** where `created_at < cutoff_date`
2. **Snapshot image files** on disk: for each matching row, derive the absolute path as `{EdgeConfig.snapshots_root}/{row.snapshot_path}`. Delete the file if it exists; skip silently if missing.

**Partial failure handling:** DB rows are deleted first in a single transaction. File deletion runs after commit — errors on individual files are logged and skipped (no rollback possible). The response reports `deleted_records`, `deleted_files`, and `skipped_files` (count of missing/unreadable files).

**`freed_bytes` computation:** File sizes are summed **before** deletion. Files that are skipped (missing or unreadable) are **excluded** from `freed_bytes`.

**Path safety:** Before deleting any file, validate that its resolved absolute path starts with `EdgeConfig.snapshots_root`. Skip any file that fails this check (path traversal guard).

### Purge Age Thresholds

| Label | `older_than` value | Cutoff |
|-------|-------------------|--------|
| Older than 1 month | `30` | `now - 30 days` |
| Older than 3 months | `90` | `now - 90 days` |
| Older than 6 months | `180` | `now - 180 days` |
| Older than 1 year | `365` | `now - 365 days` |

### Purge Steps

1. **Preview** — query count of matching rows + sum disk size of snapshot files → return JSON `{record_count, file_count, disk_bytes}`
2. **Execute** — delete rows, delete files (skip missing), return `{deleted_records, deleted_files, skipped_files, freed_bytes}`

Both steps are implemented as Django view functions callable via the UI and the API.

### What Gets Purged (soca-control)

soca-control has its own `Alert` table. Purge targets `Alert` rows filtered by `created_at < cutoff_date` (and optionally by `edge` FK). This is handled by soca-control's own view (`edge_purge_local`) — it does not go through soca-dashboard's API.

---

## 3. API Design (`/api/v1/`)

### Authentication

All `/api/v1/` endpoints are authenticated by **API key only** (not session). This simplifies the auth model — the browser UI calls separate non-`/api/v1/` view functions for the same operations (see Section 4).

Request header:
```
Authorization: Api-Key <token>
```

If the header is missing or the token doesn't match `EdgeConfig.api_key` → `403` with body:
```json
{"error": "Invalid or missing API key"}
```

Existing public endpoints (`/api/edge-info/`, `/api/cameras/`) are unaffected — no key required.

### Standard Error Responses

All endpoints return JSON. On error:

| Situation | Status | Body |
|-----------|--------|------|
| Missing/invalid API key | 403 | `{"error": "Invalid or missing API key"}` |
| Schedule not found | 404 | `{"error": "Schedule not found"}` |
| soca-engine unreachable (start/stop) | 502 | `{"error": "Engine unreachable"}` |
| engine_db_path not configured | 503 | `{"error": "Engine DB path not configured"}` |
| Unexpected server error | 500 | `{"error": "Internal error", "detail": "..."}` |

### Endpoints

#### `GET /api/v1/schedules/`
Returns all schedules with current running state.

```json
[
  {
    "id": 1,
    "name": "Stairs Intrusion",
    "camera_id": 2,
    "camera_name": "Bardi Stairs Front",
    "is_active": true,
    "alert_category": "Intrusion",
    "job_id": "550e8400-...",
    "status": "running"
  }
]
```

`status` values: `"running"` (job_id set and engine confirms running), `"stopped"` (no job_id or engine says stopped), `"unknown"` (engine unreachable).

#### `POST /api/v1/schedules/<id>/start/`
Starts the schedule (calls soca-engine, stores job_id). Returns:
```json
{"status": "started", "job_id": "550e8400-..."}
```

If schedule has no camera or is_active=False → `400 {"error": "Schedule not startable"}`.

#### `POST /api/v1/schedules/<id>/stop/`
Stops the running job. Returns:
```json
{"status": "stopped"}
```

If no job is running → returns `{"status": "already_stopped"}` (not an error).

#### `GET /api/v1/purge/preview/?older_than=<days>`
Returns purge preview without deleting anything.

`older_than` must be one of: `30`, `90`, `180`, `365`. Other values → `400 {"error": "Invalid older_than value"}`.

If `engine_db_path` is not set → `503`.

```json
{
  "older_than_days": 90,
  "cutoff_date": "2025-12-22",
  "record_count": 4821,
  "file_count": 4102,
  "disk_bytes": 2147483648
}
```

#### `POST /api/v1/purge/execute/`
Body: `{"older_than": 90}`

Same validation as preview. Executes the purge.

```json
{
  "deleted_records": 4821,
  "deleted_files": 4102,
  "skipped_files": 3,
  "freed_bytes": 2147483648
}
```

---

## 4. soca-dashboard: Settings > Operations Tab

The existing Settings page gains a **4th tab: Operations**.

The browser UI calls **separate, session-authenticated view functions** (not the `/api/v1/` endpoints) for all Operations tab actions. This avoids the need to handle both session and API key auth in the same view.

| UI action | View function | URL |
|-----------|--------------|-----|
| Generate API key | `generate_api_key` | `POST /settings/edge/generate-key/` |
| Preview purge | `purge_preview_view` | `GET /settings/operations/purge/preview/` |
| Execute purge | `purge_execute_view` | `POST /settings/operations/purge/execute/` |
| List schedules (poll) | `schedule_status_all` | `GET /settings/operations/schedules/` |

These views share the same underlying logic as the `/api/v1/` handlers via extracted helper functions (`_purge_preview()`, `_purge_execute()`, `_list_schedules()`). All three session-auth views return the **same JSON response shapes** as their `/api/v1/` counterparts.

### Block 1 — API Access

- **API Key** field: masked value with "Reveal" toggle + "Copy" button
- **Generate Key** button → `POST /settings/edge/generate-key/` → saves new `secrets.token_hex(32)` to `EdgeConfig.api_key`
  - Inline warning: "Regenerating will invalidate any existing soca-control connection"
- Static read-only list of available API endpoints (hardcoded HTML — no dynamic generation needed)

### Block 2 — Purge Edge Data

- `<select>` dropdown: "Older than" → 1 month / 3 months / 6 months / 1 year
- **Preview** button → AJAX `GET /settings/operations/purge/preview/?older_than=<days>` → inline result:
  ```
  4,821 detection records · 4,102 snapshot files · 2.0 GB will be freed
  ```
- **Purge Now** button (disabled until preview loaded) → DaisyUI confirmation modal
  - Modal shows preview numbers again
  - "Confirm Purge" → `POST /settings/operations/purge/execute/` → flash message with result
- Loading spinner during async calls

### Block 3 — Schedule Remote Control

- Table: Schedule Name | Camera | Status badge | Start / Stop buttons
- Status badge: green "Running" / gray "Stopped" / amber "Unknown"
- Start → `POST /schedules/<id>/start/` (existing view), Stop → `POST /schedules/<id>/stop/` (existing view)
- Table auto-refreshes every 10s by polling `GET /settings/operations/schedules/`
- Poll pauses when `document.visibilityState === 'hidden'` (tab not visible) to avoid unnecessary requests

---

## 5. soca-control Additions

### `Edge` model

Add `api_key = models.CharField(max_length=64, blank=True, default='')`.

### Settings > Edge form

Add "API Key" text field to the edge add/edit form. Operator pastes the key from soca-dashboard's Operations tab.

### `edge_client.py`

New functions, all send `Authorization: Api-Key {edge.api_key}` header.

**Return contract for write operations:** Return the parsed JSON dict on success, or `None` on any failure (network error, 4xx, 5xx). Callers check for `None` and show an appropriate error.

```python
def fetch_schedules(edge)             # GET /api/v1/schedules/ → list or None
def start_schedule(edge, sched_id)    # POST /api/v1/schedules/<id>/start/ → dict or None
def stop_schedule(edge, sched_id)     # POST /api/v1/schedules/<id>/stop/ → dict or None
def purge_preview(edge, older_than)   # GET /api/v1/purge/preview/ → dict or None
def purge_execute(edge, older_than)   # POST /api/v1/purge/execute/ → dict or None
```

### New page: `/edges/<edge_id>/operations/`

A dedicated operations page per edge. This is the **canonical entry point** — accessible via an "Operations" link in the edge list table row on soca-control's Settings page.

**Section 1 — Schedule Control**
- Table: Name | Camera | Status | Start / Stop
- Start → `POST /edges/<edge_id>/operations/schedules/<sched_id>/start/`
- Stop → `POST /edges/<edge_id>/operations/schedules/<sched_id>/stop/`
- Table auto-refreshes every 10s (same visibilityState pause as soca-dashboard)

**Section 2 — Purge Edge Data (remote)**
- Purges records from the remote soca-dashboard (via `/api/v1/purge/`)
- Same preview + confirm flow
- `POST /edges/<edge_id>/operations/purge/execute/` → soca-control proxy view → calls `purge_execute(edge, older_than)`

**Section 3 — Purge soca-control Records (local)**
- Purges soca-control's own `Alert` table for this specific edge (`Alert.edge = edge`)
- Preview: count of matching Alert rows (no disk files — soca-control doesn't store snapshots)
- Execute: `Alert.objects.filter(edge=edge, created_at__lt=cutoff).delete()`
- `POST /edges/<edge_id>/operations/purge/local/` → `edge_purge_local` view
- Both preview and execute accept `{"older_than": <days>}` JSON body with the same four-value allowlist (`30`, `90`, `180`, `365`) as the remote purge
- Preview response shape: `{"older_than_days": 90, "cutoff_date": "2025-12-22", "record_count": 412}`
- Execute response shape: `{"deleted_records": 412}`

---

## 6. URL Summary

### soca-dashboard (new)

```
api/v1/schedules/                         GET  → api_v1_schedules
api/v1/schedules/<id>/start/              POST → api_v1_schedule_start
api/v1/schedules/<id>/stop/               POST → api_v1_schedule_stop
api/v1/purge/preview/                     GET  → api_v1_purge_preview
api/v1/purge/execute/                     POST → api_v1_purge_execute

settings/edge/generate-key/              POST → generate_api_key
settings/operations/schedules/           GET  → schedule_status_all
settings/operations/purge/preview/       GET  → purge_preview_view
settings/operations/purge/execute/       POST → purge_execute_view
```

### soca-control (new)

```
edges/<id>/operations/                          GET  → edge_operations
edges/<id>/operations/schedules/<sid>/start/    POST → edge_schedule_start
edges/<id>/operations/schedules/<sid>/stop/     POST → edge_schedule_stop
edges/<id>/operations/purge/execute/            POST → edge_purge_remote
edges/<id>/operations/purge/local/              POST → edge_purge_local
```

---

## 7. Security Notes

- API key is stored in plain text — acceptable for private network deployments
- All write operations (start, stop, purge execute) are POST only
- Purge preview and schedule list are GET (read-only)
- File deletion validates all paths start with `EdgeConfig.snapshots_root` before deleting (path traversal guard)
- soca-dashboard's purge operates on soca-engine's SQLite — requires the engine DB file to be readable/writable by the Django process user

---

## 8. Verification

1. Add `engine_db_path` and `snapshots_root` in Settings > Edge Config → save
2. Generate API key in Settings > Operations → copy it
3. Register key in soca-control's edge settings
4. `GET /api/v1/schedules/` with key → returns schedule list
5. Start/stop a schedule from soca-control → verify job starts/stops in soca-engine
6. Run purge preview in Operations tab → verify counts match actual DB rows + files
7. Execute purge → verify rows deleted, files removed, `freed_bytes` reported
8. Trigger remote purge from soca-control Operations page → same verification
9. Purge soca-control's own Alert table → verify local records deleted
10. Regenerate API key → verify old key returns `403 {"error": "Invalid or missing API key"}`
11. Disconnect soca-engine → verify start/stop returns `502` gracefully
