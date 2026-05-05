# soca-control Implementation Plan
**Status: COMPLETE** (as of 2026-03-21)

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build soca-control, a Django web app that aggregates CCTV detection events from multiple edge devices and provides a central fleet dashboard, live monitor, filterable reports, and user management.

**Architecture:** Edge devices (soca-engine + soca-dashboard) publish detection events to Redis Streams. soca-control polls those streams via a background management command (`consume_streams`), persists alerts locally in SQLite, and serves Django views for the dashboard UI. Video streams are direct MJPEG links to each edge — no proxying.

**Tech Stack:** Django 6.0.x, SQLite, redis-py, requests, reportlab, DaisyUI 4 (Tailwind CSS via CDN), Chart.js 4 (via CDN), Python stdlib `csv`

---

## File Map

| File | Responsibility |
|------|---------------|
| `manage.py` | Django entry point |
| `requirements.txt` | Python dependencies |
| `soca_control/settings.py` | Django config (DB, auth, installed apps, ENGINE_TIMEOUT, TIME_ZONE=Asia/Jakarta) |
| `soca_control/urls.py` | Root URL routing → app.urls |
| `app/models.py` | `Edge` and `Alert` Django models |
| `app/views.py` | All page views (dashboard, monitor, reports, settings, users) |
| `app/urls.py` | All URL patterns |
| `app/edge_client.py` | HTTP wrapper: fetch_edge_info, fetch_health, fetch_system, fetch_jobs, fetch_cameras, fetch_edge_data_parallel |
| `app/management/commands/consume_streams.py` | Long-running Redis stream consumer → saves Alerts |
| `app/management/commands/backfill_camera_names.py` | One-time backfill of camera_name on existing alerts |
| `app/reports.py` | export_csv and export_pdf functions |
| `app/templates/base.html` | Sidebar nav, DaisyUI layout, font-size overrides |
| `app/templates/dashboard/index.html` | Fleet summary cards + edge cards + top cameras chart + activity feed |
| `app/templates/monitor/index.html` | MJPEG grid with camera name labels |
| `app/templates/reports/intrusion.html` | Intrusion report (Stats/Detail tabs) |
| `app/templates/reports/ppe.html` | PPE report (Stats/Detail tabs) |
| `app/templates/reports/object_detection.html` | Object detection report (Stats/Detail tabs) |
| `app/templates/reports/_stats.html` | Shared statistics tab partial |
| `app/templates/reports/_detail_chart.html` | Chart.js bar charts (by camera + by edge) |
| `app/templates/reports/_filters.html` | Shared filter bar partial |
| `app/templates/settings/index.html` | Edge CRUD |
| `app/templates/users/list.html` | User list |
| `app/templates/users/form.html` | Create/edit user (shared) |
| `app/migrations/0001_initial.py` | Edge + Alert initial schema |
| `app/migrations/0002_*.py` | Additional fields |
| `app/migrations/0003_add_camera_name_to_alert.py` | Add camera_name to Alert |
| `app/migrations/0004_fix_camera_name_default.py` | Fix NOT NULL default for camera_name |

---

## Task 1: Project Scaffold ✅

- [x] Create Django project: `django-admin startproject soca_control .`
- [x] Create app: `python manage.py startapp app`
- [x] Write `requirements.txt` (django, requests, redis, reportlab)
- [x] Configure `settings.py`: INSTALLED_APPS, LOGIN_URL, ENGINE_TIMEOUT, TIME_ZONE='Asia/Jakarta'
- [x] Wire `soca_control/urls.py` → auth views + `include('app.urls')`

---

## Task 2: Models ✅

- [x] Write `Edge` model (name, url, engine_url, redis_url, redis_stream, location, active, last_seen)
- [x] Write `Alert` model (edge FK, edge_name, camera_id, camera_name, job_id, frame_id, timestamp, alert_category, rule_name, snapshot_message, snapshot_path, detection_count, in_roi_count, cls_summary, raw)
- [x] Add `snapshot_url` property to Alert (builds `{edge.url}/alerts/snapshot/{rel_path}`)
- [x] Add `display_camera()` method to Alert (returns camera_name or camera_id)
- [x] Run `makemigrations && migrate`

---

## Task 3: edge_client.py ✅

- [x] `_get(url, timeout)` — safe GET wrapper, returns None on failure
- [x] `fetch_edge_info(edge)` → `GET {edge.url}/api/edge-info/`
- [x] `fetch_health(edge)` → `GET {edge.engine_url}/health`
- [x] `fetch_system(edge)` → `GET {edge.engine_url}/system`
- [x] `fetch_jobs(edge)` → `GET {edge.engine_url}/jobs/`
- [x] `fetch_cameras(edge)` → `GET {edge.url}/api/cameras/` (returns `{camera_id: camera_name}`)
- [x] `fetch_edge_data_parallel(edges)` → ThreadPoolExecutor, DASHBOARD_TIMEOUT=2s

---

## Task 4: consume_streams (management command) ✅

- [x] `app/management/commands/consume_streams.py`
- [x] XREAD BLOCK 5000 per edge, cursor tracked in memory, starts at `$`
- [x] `parse_message` extracts all Alert fields including `camera_name`
- [x] All string fields use `or ''` to handle explicit JSON nulls
- [x] Reloads edge list every 60s
- [x] Retry on Redis connection error after 30s

---

## Task 5: backfill_camera_names (management command) ✅

- [x] `app/management/commands/backfill_camera_names.py`
- [x] For each active edge: calls `fetch_cameras()`, updates matching Alert records by camera_id
- [x] Run once to populate camera_name on 94 existing alerts

---

## Task 6: views.py + urls.py ✅

- [x] `dashboard` — parallel edge fetch, fleet stats, top cameras, recent activity
- [x] `monitor` — active jobs grid with camera name labels
- [x] `_apply_alert_filters` — edge/camera/date_from/date_to/rule/q params
- [x] `_compute_stats` — hourly distribution, 14-day trend, top cameras/rules/edges, avg ROI
- [x] `_report_view` — tabbed Stats/Detail, chart_data, base_qs for tab switching
- [x] `report_intrusion`, `report_ppe`, `report_object_detection`
- [x] `settings_page` — Edge CRUD with fetch_edge_info on add
- [x] `edge_delete`
- [x] `user_list`, `user_create`, `user_edit`, `user_delete`

---

## Task 7: reports.py ✅

- [x] `export_csv(queryset, filename)` → StreamingHttpResponse
- [x] `export_pdf(queryset, title)` → HttpResponse with reportlab

---

## Task 8: Templates ✅

- [x] `base.html` — sidebar nav (Dashboard, Monitor, Reports dropdown, Settings, Users), font-size overrides
- [x] `dashboard/index.html` — 4 fleet stat cards, compact edge cards (CPU/MEM bars, 3-col alert counts), top cameras Chart.js bar chart, recent activity feed
- [x] `monitor/index.html` — MJPEG grid with camera name, placeholder cards, 60s auto-refresh
- [x] `reports/_filters.html` — filter bar with camera dropdown (name/id tuples), hidden tab input
- [x] `reports/_stats.html` — summary cards, hourly CSS bars, 14-day CSS bars, top tables
- [x] `reports/_detail_chart.html` — two Chart.js bar charts (by camera + by edge)
- [x] `reports/intrusion.html`, `ppe.html`, `object_detection.html` — tabbed layout extending base
- [x] `settings/index.html` — edge list + add/toggle/delete form
- [x] `users/list.html`, `users/form.html`

---

## Task 9: Settings & Migrations ✅

- [x] `TIME_ZONE = 'Asia/Jakarta'` in both soca-control and soca-dashboard
- [x] Migration 0003: add `camera_name` to Alert
- [x] Migration 0004: fix `default=''` for camera_name NOT NULL constraint

---

## soca-dashboard Changes ✅

- [x] Added `/api/cameras/` endpoint (public, returns `{camera_id_str: camera_name}`)
- [x] Removed `@login_required` from `snapshot_image` view (needed for `<img>` tags)
- [x] Changed `alert_category` field in schedule form from text input to dropdown (Intrusion/Detection/PPE)
- [x] `to_job_config()` includes `camera_name` and `message_template` in `publish_queue` action
- [x] `publish_redis` default changed to `True`

---

## soca-engine Changes ✅

- [x] `JobConfig` — added `camera_name: str = ""`
- [x] `FrameResult` — added `camera_name: str` field
- [x] `detection_worker` — passes `camera_name=cfg.camera_name` to FrameResult
- [x] `action_dispatcher` — `publish_queue` sets `result.snapshot_message` from `message_template`
- [x] `output_publisher` — includes `"camera_name"` in Redis payload
- [x] `/jobs/` API — job list includes `camera_id` and `camera_name`

---

## Verification

1. `pip install -r requirements.txt && python manage.py migrate`
2. `python manage.py createsuperuser`
3. Register an edge in Settings → verify location auto-populates
4. `python manage.py consume_streams` in second terminal
5. Trigger alert on edge → verify in dashboard + report with camera name + snapshot thumbnail
6. Test Statistics tab: hourly chart, 14-day trend, top cameras/rules/edges, Chart.js charts
7. Test `?export=csv` and `?export=pdf` on Detail tab
8. Verify live monitor loads with camera names
9. Disconnect edge → verify dashboard loads without hanging
10. `python manage.py backfill_camera_names` for existing alerts
