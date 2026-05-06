# Edge Operations — soca-dashboard Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add purge tool, API v1 (authenticated schedule control + purge endpoints), and Settings > Operations tab to soca-dashboard.

**Architecture:** New fields on `EdgeConfig` store the API key, engine DB path, and snapshots root. Shared helper functions contain the purge logic. Two sets of views expose the same operations: session-auth views for the browser UI, and API-key-auth views under `/api/v1/` for soca-control. A new 4th tab "Operations" in Settings provides the UI.

**Tech Stack:** Django 6.x, SQLite (sqlite3 stdlib), DaisyUI 4, vanilla JS fetch API, secrets stdlib

**Spec:** `docs/superpowers/specs/2026-03-22-edge-operations-design.md`

---

## File Map

| File | Change |
|------|--------|
| `app/models.py` | Add `api_key`, `engine_db_path`, `snapshots_root` to `EdgeConfig` |
| `app/migrations/XXXX_edge_operations_fields.py` | Migration for new fields |
| `app/purge.py` | New: shared purge helpers (`purge_preview`, `purge_execute`) |
| `app/api_auth.py` | New: `require_api_key` decorator |
| `app/views.py` | Add: `generate_api_key`, `schedule_status_all`, `purge_preview_view`, `purge_execute_view`, API v1 views |
| `app/urls.py` | Add: `/settings/operations/*`, `/settings/edge/generate-key/`, `/api/v1/*` |
| `templates/settings/index.html` | Add Operations tab + engine_db_path/snapshots_root fields to Edge Config tab |

---

## Task 1: Model Migration

**Files:**
- Modify: `app/models.py`
- Create: `app/migrations/` (auto-generated)

- [ ] **Step 1: Add three fields to `EdgeConfig` in `app/models.py`**

After line 26 (`updated_at = ...`), add:

```python
    api_key = models.CharField(max_length=64, blank=True, default='')
    engine_db_path = models.CharField(max_length=500, blank=True, default='',
        help_text="Absolute path to soca-engine's SQLite file, e.g. /opt/soca-engine/db.sqlite3")
    snapshots_root = models.CharField(max_length=500, blank=True, default='',
        help_text="Absolute path to snapshots directory, e.g. /opt/soca-engine/snapshots")
```

- [ ] **Step 2: Generate and run migration**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-dashboard"
python manage.py makemigrations app --name edge_operations_fields
python manage.py migrate
```

Expected: migration file created, `OK` on migrate.

- [ ] **Step 3: Verify fields exist**

```bash
python manage.py shell -c "from app.models import EdgeConfig; e, _ = EdgeConfig.objects.get_or_create(pk=1); print(e.api_key, e.engine_db_path, e.snapshots_root)"
```

Expected: three empty strings printed without error.

- [ ] **Step 4: Commit**

```bash
git add app/models.py app/migrations/
git commit -m "feat: add api_key, engine_db_path, snapshots_root to EdgeConfig"
```

---

## Task 2: API Key Auth Decorator

**Files:**
- Create: `app/api_auth.py`

- [ ] **Step 1: Create `app/api_auth.py`**

```python
import functools
from django.http import JsonResponse
from .models import EdgeConfig


def require_api_key(view_func):
    """Decorator: validates Authorization: Api-Key <token> header."""
    @functools.wraps(view_func)
    def wrapper(request, *args, **kwargs):
        auth = request.headers.get('Authorization', '')
        if not auth.startswith('Api-Key '):
            return JsonResponse({'error': 'Invalid or missing API key'}, status=403)
        token = auth[len('Api-Key '):]
        edge = EdgeConfig.objects.first()
        if not edge or not edge.api_key or token != edge.api_key:
            return JsonResponse({'error': 'Invalid or missing API key'}, status=403)
        return view_func(request, *args, **kwargs)
    return wrapper
```

- [ ] **Step 2: Verify import works**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-dashboard"
python -c "from app.api_auth import require_api_key; print('ok')"
```

Expected: `ok`

- [ ] **Step 3: Commit**

```bash
git add app/api_auth.py
git commit -m "feat: add require_api_key decorator for API v1 auth"
```

---

## Task 3: Purge Helpers

**Files:**
- Create: `app/purge.py`

- [ ] **Step 1: Create `app/purge.py`**

```python
import logging
import os
import sqlite3
from datetime import datetime, timezone, timedelta
from pathlib import Path

logger = logging.getLogger(__name__)

VALID_DAYS = {30, 90, 180, 365}


def _cutoff(older_than_days: int) -> datetime:
    return datetime.now(tz=timezone.utc) - timedelta(days=older_than_days)


def purge_preview(engine_db_path: str, snapshots_root: str, older_than_days: int) -> dict:
    """Return counts and disk usage of records older than cutoff. No deletion."""
    if older_than_days not in VALID_DAYS:
        raise ValueError(f"older_than must be one of {VALID_DAYS}")
    cutoff = _cutoff(older_than_days)
    cutoff_str = cutoff.strftime('%Y-%m-%d %H:%M:%S')

    con = sqlite3.connect(engine_db_path)
    try:
        rows = con.execute(
            "SELECT snapshot_path FROM detection_events WHERE created_at < ?",
            (cutoff_str,)
        ).fetchall()
    finally:
        con.close()

    record_count = len(rows)
    file_count = 0
    disk_bytes = 0
    snapshots_root_path = Path(snapshots_root)

    for (snap_path,) in rows:
        if not snap_path:
            continue
        full = (snapshots_root_path / snap_path).resolve()
        if not str(full).startswith(str(snapshots_root_path)):
            continue  # path traversal guard
        try:
            size = full.stat().st_size
            file_count += 1
            disk_bytes += size
        except OSError:
            pass  # missing file — skip

    return {
        'older_than_days': older_than_days,
        'cutoff_date': cutoff.strftime('%Y-%m-%d'),
        'record_count': record_count,
        'file_count': file_count,
        'disk_bytes': disk_bytes,
    }


def purge_execute(engine_db_path: str, snapshots_root: str, older_than_days: int) -> dict:
    """Delete records and snapshot files older than cutoff."""
    if older_than_days not in VALID_DAYS:
        raise ValueError(f"older_than must be one of {VALID_DAYS}")
    cutoff = _cutoff(older_than_days)
    cutoff_str = cutoff.strftime('%Y-%m-%d %H:%M:%S')

    con = sqlite3.connect(engine_db_path)
    try:
        rows = con.execute(
            "SELECT snapshot_path FROM detection_events WHERE created_at < ?",
            (cutoff_str,)
        ).fetchall()
        con.execute("DELETE FROM detection_events WHERE created_at < ?", (cutoff_str,))
        con.commit()
        deleted_records = len(rows)
    finally:
        con.close()

    snapshots_root_path = Path(snapshots_root)
    deleted_files = 0
    skipped_files = 0
    freed_bytes = 0

    for (snap_path,) in rows:
        if not snap_path:
            continue
        full = (snapshots_root_path / snap_path).resolve()
        if not str(full).startswith(str(snapshots_root_path)):
            skipped_files += 1
            continue  # path traversal guard
        try:
            size = full.stat().st_size
            freed_bytes += size
            os.remove(full)
            deleted_files += 1
        except OSError as e:
            logger.warning('purge: could not delete %s: %s', full, e)
            skipped_files += 1

    return {
        'deleted_records': deleted_records,
        'deleted_files': deleted_files,
        'skipped_files': skipped_files,
        'freed_bytes': freed_bytes,
    }
```

- [ ] **Step 2: Verify import**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-dashboard"
python -c "from app.purge import purge_preview, purge_execute, VALID_DAYS; print(VALID_DAYS)"
```

Expected: `{30, 90, 180, 365}`

- [ ] **Step 3: Commit**

```bash
git add app/purge.py
git commit -m "feat: add purge helpers (preview + execute) for detection_events"
```

---

## Task 4: Views — Session-Auth Operations + API v1

**Files:**
- Modify: `app/views.py` (append to end of file)

- [ ] **Step 1: Add imports at top of `app/views.py`**

After the existing imports (line 18), add:

```python
import secrets
from .api_auth import require_api_key
from .purge import purge_preview as _purge_preview_helper, purge_execute as _purge_execute_helper, VALID_DAYS
```

- [ ] **Step 2: Append new views to end of `app/views.py`**

```python
# ── Operations: API key management ────────────────────────────────────────────

@login_required
def generate_api_key(request):
    if request.method == 'POST':
        edge, _ = EdgeConfig.objects.get_or_create(pk=1)
        edge.api_key = secrets.token_hex(32)
        edge.save(update_fields=['api_key'])
        messages.success(request, 'API key regenerated.')
    return redirect('settings')


# ── Operations: schedule list (session-auth) ───────────────────────────────────

@login_required
def schedule_status_all(request):
    return JsonResponse(_schedule_list_data(), safe=False)


# ── Operations: purge (session-auth) ──────────────────────────────────────────

@login_required
def purge_preview_view(request):
    try:
        older_than = int(request.GET.get('older_than', 0))
    except (ValueError, TypeError):
        return JsonResponse({'error': 'Invalid older_than value'}, status=400)
    if older_than not in VALID_DAYS:
        return JsonResponse({'error': 'Invalid older_than value'}, status=400)
    edge = EdgeConfig.objects.first()
    if not edge or not edge.engine_db_path:
        return JsonResponse({'error': 'Engine DB path not configured'}, status=503)
    try:
        result = _purge_preview_helper(edge.engine_db_path, edge.snapshots_root, older_than)
        return JsonResponse(result)
    except Exception as e:
        return JsonResponse({'error': 'Internal error', 'detail': str(e)}, status=500)


@login_required
def purge_execute_view(request):
    if request.method != 'POST':
        return JsonResponse({'error': 'POST required'}, status=405)
    import json as _json
    try:
        body = _json.loads(request.body)
        older_than = int(body.get('older_than', 0))
    except Exception:
        return JsonResponse({'error': 'Invalid request body'}, status=400)
    if older_than not in VALID_DAYS:
        return JsonResponse({'error': 'Invalid older_than value'}, status=400)
    edge = EdgeConfig.objects.first()
    if not edge or not edge.engine_db_path:
        return JsonResponse({'error': 'Engine DB path not configured'}, status=503)
    try:
        result = _purge_execute_helper(edge.engine_db_path, edge.snapshots_root, older_than)
        return JsonResponse(result)
    except Exception as e:
        return JsonResponse({'error': 'Internal error', 'detail': str(e)}, status=500)


# ── API v1: shared data helpers ────────────────────────────────────────────────

def _schedule_list_data():
    schedules = Schedule.objects.select_related('camera').order_by('name')
    result = []
    for s in schedules:
        status = 'stopped'
        if s.current_job_id:
            job = engine_client.get_job(s.current_job_id)
            if job is None:
                status = 'unknown'
            elif job.get('status') == 'running':
                status = 'running'
        result.append({
            'id': s.id,
            'name': s.name,
            'camera_id': s.camera_id,
            'camera_name': s.camera.name,
            'is_active': s.is_active,
            'alert_category': s.alert_category,
            'job_id': s.current_job_id or None,
            'status': status,
        })
    return result


# ── API v1 endpoints (API-key auth) ───────────────────────────────────────────

@require_api_key
def api_v1_schedules(request):
    return JsonResponse(_schedule_list_data(), safe=False)


@require_api_key
def api_v1_schedule_start(request, pk):
    if request.method != 'POST':
        return JsonResponse({'error': 'POST required'}, status=405)
    schedule = Schedule.objects.filter(pk=pk).select_related('camera').first()
    if not schedule:
        return JsonResponse({'error': 'Schedule not found'}, status=404)
    if not schedule.is_active or not schedule.camera_id:
        return JsonResponse({'error': 'Schedule not startable'}, status=400)
    try:
        config = schedule.to_job_config()
        result = engine_client.start_job(config)
        schedule.current_job_id = result['job_id']
        schedule.save(update_fields=['current_job_id'])
        return JsonResponse({'status': 'started', 'job_id': result['job_id']})
    except Exception as e:
        return JsonResponse({'error': 'Engine unreachable', 'detail': str(e)}, status=502)


@require_api_key
def api_v1_schedule_stop(request, pk):
    if request.method != 'POST':
        return JsonResponse({'error': 'POST required'}, status=405)
    schedule = Schedule.objects.filter(pk=pk).first()
    if not schedule:
        return JsonResponse({'error': 'Schedule not found'}, status=404)
    if not schedule.current_job_id:
        return JsonResponse({'status': 'already_stopped'})
    try:
        engine_client.stop_job(schedule.current_job_id)
    except Exception as e:
        return JsonResponse({'error': 'Engine unreachable', 'detail': str(e)}, status=502)
    schedule.current_job_id = ''
    schedule.save(update_fields=['current_job_id'])
    return JsonResponse({'status': 'stopped'})


@require_api_key
def api_v1_purge_preview(request):
    try:
        older_than = int(request.GET.get('older_than', 0))
    except (ValueError, TypeError):
        return JsonResponse({'error': 'Invalid older_than value'}, status=400)
    if older_than not in VALID_DAYS:
        return JsonResponse({'error': 'Invalid older_than value'}, status=400)
    edge = EdgeConfig.objects.first()
    if not edge or not edge.engine_db_path:
        return JsonResponse({'error': 'Engine DB path not configured'}, status=503)
    try:
        return JsonResponse(_purge_preview_helper(edge.engine_db_path, edge.snapshots_root, older_than))
    except Exception as e:
        return JsonResponse({'error': 'Internal error', 'detail': str(e)}, status=500)


@require_api_key
def api_v1_purge_execute(request):
    if request.method != 'POST':
        return JsonResponse({'error': 'POST required'}, status=405)
    import json as _json
    try:
        body = _json.loads(request.body)
        older_than = int(body.get('older_than', 0))
    except Exception:
        return JsonResponse({'error': 'Invalid request body'}, status=400)
    if older_than not in VALID_DAYS:
        return JsonResponse({'error': 'Invalid older_than value'}, status=400)
    edge = EdgeConfig.objects.first()
    if not edge or not edge.engine_db_path:
        return JsonResponse({'error': 'Engine DB path not configured'}, status=503)
    try:
        return JsonResponse(_purge_execute_helper(edge.engine_db_path, edge.snapshots_root, older_than))
    except Exception as e:
        return JsonResponse({'error': 'Internal error', 'detail': str(e)}, status=500)
```

- [ ] **Step 3: Verify no import errors**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-dashboard"
python manage.py check
```

Expected: `System check identified no issues`

- [ ] **Step 4: Commit**

```bash
git add app/views.py
git commit -m "feat: add operations views (purge, schedule list, API key gen, API v1)"
```

---

## Task 5: URL Patterns

**Files:**
- Modify: `app/urls.py`

- [ ] **Step 1: Add new URL patterns to `app/urls.py`**

After the existing `path("settings/edge/", ...)` line (line 43), add:

```python
    path("settings/edge/generate-key/", views.generate_api_key, name="generate_api_key"),
    path("settings/operations/schedules/", views.schedule_status_all, name="schedule_status_all"),
    path("settings/operations/purge/preview/", views.purge_preview_view, name="purge_preview"),
    path("settings/operations/purge/execute/", views.purge_execute_view, name="purge_execute"),

    # API v1 (API-key auth)
    path("api/v1/schedules/", views.api_v1_schedules, name="api_v1_schedules"),
    path("api/v1/schedules/<int:pk>/start/", views.api_v1_schedule_start, name="api_v1_schedule_start"),
    path("api/v1/schedules/<int:pk>/stop/", views.api_v1_schedule_stop, name="api_v1_schedule_stop"),
    path("api/v1/purge/preview/", views.api_v1_purge_preview, name="api_v1_purge_preview"),
    path("api/v1/purge/execute/", views.api_v1_purge_execute, name="api_v1_purge_execute"),
```

- [ ] **Step 2: Verify**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-dashboard"
python manage.py show_urls 2>/dev/null | grep -E "api/v1|operations|generate-key" || python manage.py check
```

Expected: new URL patterns listed or `no issues`.

- [ ] **Step 3: Commit**

```bash
git add app/urls.py
git commit -m "feat: add URL patterns for API v1 and operations endpoints"
```

---

## Task 6: Settings Template

**Files:**
- Modify: `templates/settings/index.html`

- [ ] **Step 1: Add `operations` to the tab bar**

Replace the existing tab bar (lines 11–15):

```html
  <div role="tablist" class="tabs tabs-boxed bg-base-100">
    <a role="tab" id="tab-users" class="tab tab-active" onclick="showTab('users', this)">Users</a>
    <a role="tab" id="tab-edge" class="tab" onclick="showTab('edge', this)">Edge Config</a>
    <a role="tab" id="tab-telegram" class="tab" onclick="showTab('telegram', this)">Telegram</a>
    <a role="tab" id="tab-operations" class="tab" onclick="showTab('operations', this)">Operations</a>
  </div>
```

- [ ] **Step 2: Add `engine_db_path` and `snapshots_root` fields to Edge Config form**

In the Edge Config form, after the `engine_env_path` field block (after line 184 ending `</div>`), add:

```html
            <div class="form-control md:col-span-2">
              <label class="label"><span class="label-text font-medium">Engine DB Path</span></label>
              <input type="text" name="engine_db_path" value="{{ edge.engine_db_path|default:'' }}"
                placeholder="/opt/soca-engine/db.sqlite3"
                class="input input-bordered font-mono text-sm" />
              <label class="label"><span class="label-text-alt opacity-60">Used by purge tool to delete old detection records</span></label>
            </div>
            <div class="form-control md:col-span-2">
              <label class="label"><span class="label-text font-medium">Snapshots Root</span></label>
              <input type="text" name="snapshots_root" value="{{ edge.snapshots_root|default:'' }}"
                placeholder="/opt/soca-engine/snapshots"
                class="input input-bordered font-mono text-sm" />
              <label class="label"><span class="label-text-alt opacity-60">Used by purge tool to delete old snapshot image files</span></label>
            </div>
```

- [ ] **Step 3: Update `edge_settings` view to save new fields**

In `app/views.py`, in the `edge_settings` view, add the two lines **before** the `edge.save()` call (which is at line ~523), after `edge.site_notes = p.get("site_notes", ...)`:

```python
        edge.engine_db_path = p.get('engine_db_path', edge.engine_db_path)
        edge.snapshots_root = p.get('snapshots_root', edge.snapshots_root)
        # edge.save() follows immediately after — do NOT place after it
```

Also add hidden fields to the **Telegram tab form** (`templates/settings/index.html` around line 119, inside `<form method="post" action="{% url 'edge_settings' %}">` that already has other hidden fields) to preserve values when saving from the Telegram tab:
```html
<input type="hidden" name="engine_db_path" value="{{ edge.engine_db_path|default:'' }}" />
<input type="hidden" name="snapshots_root" value="{{ edge.snapshots_root|default:'' }}" />
```
Insert these after the existing hidden field for `engine_env_path` (line ~115).

- [ ] **Step 4: Add Operations tab panel**

Insert the following **before** line 224 (the closing `</div>` of the outer wrapper `<div class="max-w-3xl space-y-6">`). The Operations panel must remain inside the outer wrapper. Line 222 closes the Edge Config panel; line 224 closes the outer wrapper; the delete-user modals begin at line 226. Insert at line 223:

```html
  <!-- Operations Tab -->
  <div id="panel-operations" class="space-y-6" style="display:none">

    <!-- API Access -->
    <div class="card bg-base-100 shadow">
      <div class="card-body">
        <h3 class="card-title text-base mb-3">API Access</h3>
        <p class="text-sm opacity-60 mb-4">
          Share this key with soca-control to allow remote schedule control and purge.
          Regenerating will invalidate any existing soca-control connection.
        </p>

        <div class="flex items-center gap-3 mb-4">
          <input type="password" id="api-key-display"
            value="{{ edge.api_key|default:'' }}"
            class="input input-bordered font-mono text-sm flex-1" readonly />
          <button type="button" onclick="toggleKey()" class="btn btn-ghost btn-sm">Reveal</button>
          <button type="button" onclick="copyKey()" class="btn btn-ghost btn-sm">Copy</button>
        </div>

        <form method="post" action="{% url 'generate_api_key' %}"
              onsubmit="return confirm('Regenerate API key? soca-control will lose access until you update the key there.')">
          {% csrf_token %}
          <button type="submit" class="btn btn-warning btn-sm">Regenerate Key</button>
        </form>

        <div class="mt-4 text-xs opacity-50 space-y-1">
          <p class="font-semibold uppercase tracking-wide mb-2">Available API Endpoints</p>
          <p><code>GET  /api/v1/schedules/</code> — list schedules with status</p>
          <p><code>POST /api/v1/schedules/&lt;id&gt;/start/</code> — start a schedule</p>
          <p><code>POST /api/v1/schedules/&lt;id&gt;/stop/</code> — stop a schedule</p>
          <p><code>GET  /api/v1/purge/preview/?older_than=&lt;days&gt;</code> — preview purge</p>
          <p><code>POST /api/v1/purge/execute/</code> — execute purge</p>
          <p class="mt-2">All endpoints require: <code>Authorization: Api-Key &lt;token&gt;</code></p>
        </div>
      </div>
    </div>

    <!-- Purge Edge Data -->
    <div class="card bg-base-100 shadow">
      <div class="card-body">
        <h3 class="card-title text-base mb-1">Purge Edge Data</h3>
        <p class="text-sm opacity-60 mb-4">
          Permanently delete old detection records from soca-engine's database and snapshot image files from disk.
          Configure Engine DB Path and Snapshots Root in the Edge Config tab first.
        </p>

        <div class="flex items-center gap-3 flex-wrap mb-4">
          <select id="purge-age" class="select select-bordered select-sm">
            <option value="30">Older than 1 month</option>
            <option value="90">Older than 3 months</option>
            <option value="180">Older than 6 months</option>
            <option value="365">Older than 1 year</option>
          </select>
          <button type="button" onclick="previewPurge()" class="btn btn-sm btn-outline">Preview</button>
          <button type="button" id="purge-btn" onclick="document.getElementById('confirm-purge-modal').showModal()"
            class="btn btn-sm btn-error" disabled>Purge Now</button>
        </div>

        <div id="purge-preview-result" class="text-sm opacity-70 mb-2"></div>
      </div>
    </div>

    <!-- Schedule Remote Control -->
    <div class="card bg-base-100 shadow">
      <div class="card-body">
        <h3 class="card-title text-base mb-3">Schedule Control</h3>
        <div id="schedule-table-wrap">
          <p class="text-sm opacity-40">Loading...</p>
        </div>
      </div>
    </div>

  </div>

  <!-- Purge confirmation modal -->
  <dialog id="confirm-purge-modal" class="modal">
    <div class="modal-box">
      <h3 class="font-bold text-lg text-error">Confirm Purge</h3>
      <p id="purge-confirm-text" class="py-4 text-sm"></p>
      <div class="modal-action">
        <form method="dialog"><button class="btn btn-ghost">Cancel</button></form>
        <button type="button" onclick="executePurge()" class="btn btn-error">Confirm Purge</button>
      </div>
    </div>
    <form method="dialog" class="modal-backdrop"><button>close</button></form>
  </dialog>
```

- [ ] **Step 5: Update `showTab` JS and add operations JS**

The confirmation modals added in Step 4 go **before** the `<script>` block (at line 246) and **after** the existing delete-user modals (lines 226–244), but also **before** `{% endblock %}` at line 256. Keep everything inside `{% block content %}`.

Replace the existing `<script>` block (lines 246–255) with:

```html
<script>
const ALL_TABS = ['users', 'edge', 'telegram', 'operations'];

function showTab(name, el) {
  ALL_TABS.forEach(t => {
    document.getElementById('panel-' + t).style.display = 'none';
    document.getElementById('tab-' + t).classList.remove('tab-active');
  });
  document.getElementById('panel-' + name).style.display = '';
  el.classList.add('tab-active');
  if (name === 'operations') loadScheduleTable();
}

function toggleKey() {
  const el = document.getElementById('api-key-display');
  el.type = el.type === 'password' ? 'text' : 'password';
}

function copyKey() {
  const val = document.getElementById('api-key-display').value;
  navigator.clipboard.writeText(val).then(() => alert('Copied!'));
}

// Purge
let purgePreviewData = null;

function previewPurge() {
  const days = document.getElementById('purge-age').value;
  const resultEl = document.getElementById('purge-preview-result');
  const btn = document.getElementById('purge-btn');
  resultEl.textContent = 'Loading...';
  btn.disabled = true;
  purgePreviewData = null;

  fetch(`/settings/operations/purge/preview/?older_than=${days}`)
    .then(r => r.json())
    .then(data => {
      if (data.error) {
        resultEl.textContent = 'Error: ' + data.error;
        return;
      }
      purgePreviewData = data;
      const mb = (data.disk_bytes / 1048576).toFixed(1);
      resultEl.textContent =
        `${data.record_count.toLocaleString()} detection records · ` +
        `${data.file_count.toLocaleString()} snapshot files · ` +
        `${mb} MB will be freed`;
      document.getElementById('purge-confirm-text').textContent = resultEl.textContent;
      btn.disabled = false;
    })
    .catch(() => { resultEl.textContent = 'Request failed.'; });
}

function executePurge() {
  document.getElementById('confirm-purge-modal').close();
  if (!purgePreviewData) return;
  const days = document.getElementById('purge-age').value;
  const resultEl = document.getElementById('purge-preview-result');
  resultEl.textContent = 'Purging...';
  document.getElementById('purge-btn').disabled = true;

  fetch('/settings/operations/purge/execute/', {
    method: 'POST',
    headers: {'Content-Type': 'application/json', 'X-CSRFToken': getCookie('csrftoken')},
    body: JSON.stringify({older_than: parseInt(days)}),
  })
    .then(r => r.json())
    .then(data => {
      if (data.error) { resultEl.textContent = 'Error: ' + data.error; return; }
      const mb = (data.freed_bytes / 1048576).toFixed(1);
      resultEl.textContent =
        `Done: ${data.deleted_records.toLocaleString()} records deleted, ` +
        `${data.deleted_files.toLocaleString()} files removed, ` +
        `${mb} MB freed` +
        (data.skipped_files ? ` (${data.skipped_files} skipped)` : '');
      purgePreviewData = null;
    })
    .catch(() => { resultEl.textContent = 'Request failed.'; });
}

// Schedule table
let schedulePoller = null;

function loadScheduleTable() {
  fetch('/settings/operations/schedules/')
    .then(r => r.json())
    .then(renderScheduleTable)
    .catch(() => {
      document.getElementById('schedule-table-wrap').innerHTML =
        '<p class="text-sm text-error">Failed to load schedules.</p>';
    });

  clearInterval(schedulePoller);
  schedulePoller = setInterval(() => {
    if (document.visibilityState === 'hidden') return;
    fetch('/settings/operations/schedules/')
      .then(r => r.json())
      .then(renderScheduleTable)
      .catch(() => {});
  }, 10000);
}

function renderScheduleTable(data) {
  const wrap = document.getElementById('schedule-table-wrap');
  if (!data.length) {
    wrap.innerHTML = '<p class="text-sm opacity-40">No schedules configured.</p>';
    return;
  }
  const rows = data.map(s => {
    const badge = s.status === 'running'
      ? '<span class="badge badge-success badge-sm">Running</span>'
      : s.status === 'unknown'
      ? '<span class="badge badge-warning badge-sm">Unknown</span>'
      : '<span class="badge badge-ghost badge-sm">Stopped</span>';
    const startBtn = `<form method="post" action="/schedules/${s.id}/start/" style="display:inline">
      <input type="hidden" name="csrfmiddlewaretoken" value="${getCookie('csrftoken')}">
      <button class="btn btn-xs btn-success" ${s.status === 'running' ? 'disabled' : ''}>Start</button></form>`;
    const stopBtn = `<form method="post" action="/schedules/${s.id}/stop/" style="display:inline">
      <input type="hidden" name="csrfmiddlewaretoken" value="${getCookie('csrftoken')}">
      <button class="btn btn-xs btn-error" ${s.status !== 'running' ? 'disabled' : ''}>Stop</button></form>`;
    return `<tr>
      <td class="font-medium">${s.name}</td>
      <td class="opacity-60">${s.camera_name}</td>
      <td>${badge}</td>
      <td class="space-x-1">${startBtn} ${stopBtn}</td>
    </tr>`;
  }).join('');
  wrap.innerHTML = `<div class="overflow-x-auto"><table class="table table-sm">
    <thead><tr><th>Schedule</th><th>Camera</th><th>Status</th><th>Actions</th></tr></thead>
    <tbody>${rows}</tbody></table></div>`;
}

function getCookie(name) {
  const v = document.cookie.match('(^|;) ?' + name + '=([^;]*)(;|$)');
  return v ? v[2] : '';
}
</script>
```

- [ ] **Step 6: Verify page loads**

Start the dev server and open `/settings/` — verify the 4th "Operations" tab appears and clicking it shows the three blocks.

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-dashboard"
python manage.py runserver 8000 &
sleep 2
curl -s -o /dev/null -w "%{http_code}" http://<soca-control-ip>:8000/settings/ -H "Cookie: sessionid=..." || echo "manual check needed"
```

- [ ] **Step 7: Commit**

```bash
git add templates/settings/index.html app/views.py
git commit -m "feat: add Settings > Operations tab (API key, purge, schedule control)"
```

---

## Task 7: Manual End-to-End Verification

- [ ] Log in to soca-dashboard at `http://<soca-control-ip>:8000`
- [ ] Go to **Settings > Edge Config**: fill in `Engine DB Path` and `Snapshots Root`, save
- [ ] Go to **Settings > Operations**: click **Generate Key** → key appears masked
- [ ] Click **Reveal** → key shows in plain text; **Copy** copies to clipboard
- [ ] Select "Older than 1 month", click **Preview** → counts appear (even if 0)
- [ ] Verify **Purge Now** button enables after preview
- [ ] Click **Purge Now** → confirmation modal appears → cancel → nothing deleted
- [ ] Schedule Control table loads and refreshes every 10s
- [ ] Test API: `curl -H "Authorization: Api-Key <key>" http://<soca-control-ip>:8000/api/v1/schedules/` → JSON list
- [ ] Test bad key: `curl -H "Authorization: Api-Key wrong" http://<soca-control-ip>:8000/api/v1/schedules/` → `{"error": "Invalid or missing API key"}`

```bash
kill %1 2>/dev/null  # stop dev server
```

- [ ] **Final commit**

```bash
git add -A
git commit -m "feat: edge operations complete (purge, API v1, operations settings tab)"
```
