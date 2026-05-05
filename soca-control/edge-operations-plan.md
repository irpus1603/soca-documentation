# Edge Operations — soca-control Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add remote schedule control, remote purge trigger, and local Alert purge to soca-control, consuming soca-dashboard's new `/api/v1/` endpoints.

**Architecture:** `Edge` model gains an `api_key` field. `edge_client.py` gains five new functions using `Authorization: Api-Key` headers. A new Operations page per edge (`/edges/<id>/operations/`) shows schedule control, remote purge, and local Alert purge. The Settings page edge table gains an "Operations" link per row and an API Key field in the add-edge form.

**Tech Stack:** Django 6.x, requests, DaisyUI 4, vanilla JS fetch API

**Spec:** (in soca-dashboard repo) `docs/superpowers/specs/2026-03-22-edge-operations-design.md`

**Prerequisite:** soca-dashboard Plan 1 must be complete and running — the `/api/v1/` endpoints must exist on each target edge.

---

## File Map

| File | Change |
|------|--------|
| `app/models.py` | Add `api_key` to `Edge` |
| `app/migrations/XXXX_edge_api_key.py` | Migration (auto-generated) |
| `app/edge_client.py` | Add 5 new functions: fetch_schedules, start_schedule, stop_schedule, purge_preview, purge_execute |
| `app/views.py` | Add: `edge_operations`, `edge_schedule_start`, `edge_schedule_stop`, `edge_purge_remote`, `edge_purge_local` |
| `app/urls.py` | Add edges operations URLs |
| `app/templates/edges/operations.html` | New: Operations page template |
| `app/templates/settings/index.html` | Add API Key field to add-edge form + Operations link in table |

---

## Task 1: Model Migration

**Files:**
- Modify: `app/models.py`
- Create: migration (auto-generated)

- [ ] **Step 1: Add `api_key` to `Edge` model in `app/models.py`**

After `active = models.BooleanField(default=True)` (line 12), add:

```python
    api_key = models.CharField(max_length=64, blank=True, default='',
        help_text='API key from soca-dashboard Settings > Operations')
```

- [ ] **Step 2: Generate and run migration**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-control"
python manage.py makemigrations app --name edge_api_key
python manage.py migrate
```

Expected: migration created, `OK` on migrate.

- [ ] **Step 3: Commit**

```bash
git add app/models.py app/migrations/
git commit -m "feat: add api_key field to Edge model"
```

---

## Task 2: edge_client New Functions

**Files:**
- Modify: `app/edge_client.py`

- [ ] **Step 1: Append five new functions to `app/edge_client.py`**

After the existing `fetch_cameras` function (end of file), add:

```python
def _api_key_headers(edge):
    """Return Authorization header dict for API v1 calls."""
    return {'Authorization': f'Api-Key {edge.api_key}'} if edge.api_key else {}


def fetch_schedules(edge):
    """GET /api/v1/schedules/ — returns list of schedule dicts or None on failure."""
    try:
        resp = requests.get(
            f'{edge.url.rstrip("/")}/api/v1/schedules/',
            headers=_api_key_headers(edge),
            timeout=TIMEOUT,
        )
        resp.raise_for_status()
        return resp.json()
    except Exception as exc:
        logger.warning('fetch_schedules %s failed: %s', edge.name, exc)
        return None


def start_schedule(edge, sched_id):
    """POST /api/v1/schedules/<id>/start/ — returns dict or None on failure."""
    try:
        resp = requests.post(
            f'{edge.url.rstrip("/")}/api/v1/schedules/{sched_id}/start/',
            headers=_api_key_headers(edge),
            timeout=TIMEOUT,
        )
        resp.raise_for_status()
        return resp.json()
    except Exception as exc:
        logger.warning('start_schedule %s/%s failed: %s', edge.name, sched_id, exc)
        return None


def stop_schedule(edge, sched_id):
    """POST /api/v1/schedules/<id>/stop/ — returns dict or None on failure."""
    try:
        resp = requests.post(
            f'{edge.url.rstrip("/")}/api/v1/schedules/{sched_id}/stop/',
            headers=_api_key_headers(edge),
            timeout=TIMEOUT,
        )
        resp.raise_for_status()
        return resp.json()
    except Exception as exc:
        logger.warning('stop_schedule %s/%s failed: %s', edge.name, sched_id, exc)
        return None


def purge_preview(edge, older_than):
    """GET /api/v1/purge/preview/?older_than=<days> — returns dict or None on failure."""
    try:
        resp = requests.get(
            f'{edge.url.rstrip("/")}/api/v1/purge/preview/',
            params={'older_than': older_than},
            headers=_api_key_headers(edge),
            timeout=TIMEOUT,
        )
        resp.raise_for_status()
        return resp.json()
    except Exception as exc:
        logger.warning('purge_preview %s failed: %s', edge.name, exc)
        return None


def purge_execute(edge, older_than):
    """POST /api/v1/purge/execute/ — returns dict or None on failure."""
    try:
        resp = requests.post(
            f'{edge.url.rstrip("/")}/api/v1/purge/execute/',
            json={'older_than': older_than},
            headers=_api_key_headers(edge),
            timeout=TIMEOUT,
        )
        resp.raise_for_status()
        return resp.json()
    except Exception as exc:
        logger.warning('purge_execute %s failed: %s', edge.name, exc)
        return None
```

- [ ] **Step 2: Verify import**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-control"
python -c "from app.edge_client import fetch_schedules, start_schedule, stop_schedule, purge_preview, purge_execute; print('ok')"
```

Expected: `ok`

- [ ] **Step 3: Commit**

```bash
git add app/edge_client.py
git commit -m "feat: add API v1 functions to edge_client (schedules, purge)"
```

---

## Task 3: Operations Views

**Files:**
- Modify: `app/views.py`

- [ ] **Step 1: Update imports in `app/views.py`**

**a)** On line 8, `from django.http import FileResponse, Http404, JsonResponse` — `JsonResponse` must be present. Check the existing import; if it doesn't include `JsonResponse`, add it:

```python
from django.http import FileResponse, Http404, JsonResponse
```

**b)** Update the `from .edge_client import ...` line (line 12) to include new functions:

```python
from .edge_client import (
    fetch_edge_info, fetch_health, fetch_system, fetch_jobs,
    fetch_edge_data_parallel,
    fetch_schedules, start_schedule, stop_schedule, purge_preview, purge_execute,
)
```

- [ ] **Step 2: Append new views to end of `app/views.py`**

```python
# ── Edge Operations ────────────────────────────────────────────────────────────

@login_required
def edge_operations(request, edge_id):
    edge = get_object_or_404(Edge, pk=edge_id)
    schedules = fetch_schedules(edge)
    return render(request, 'edges/operations.html', {
        'edge': edge,
        'schedules': schedules,
    })


@login_required
def edge_schedule_start(request, edge_id, sched_id):
    if request.method != 'POST':
        return redirect('edge_operations', edge_id=edge_id)
    edge = get_object_or_404(Edge, pk=edge_id)
    result = start_schedule(edge, sched_id)
    if result:
        messages.success(request, f'Schedule started: job {result.get("job_id", "")}')
    else:
        messages.error(request, 'Failed to start schedule. Check edge connectivity and API key.')
    return redirect('edge_operations', edge_id=edge_id)


@login_required
def edge_schedule_stop(request, edge_id, sched_id):
    if request.method != 'POST':
        return redirect('edge_operations', edge_id=edge_id)
    edge = get_object_or_404(Edge, pk=edge_id)
    result = stop_schedule(edge, sched_id)
    if result:
        messages.success(request, 'Schedule stopped.')
    else:
        messages.error(request, 'Failed to stop schedule. Check edge connectivity and API key.')
    return redirect('edge_operations', edge_id=edge_id)


@login_required
def edge_purge_remote(request, edge_id):
    """Proxy purge preview/execute to the remote edge's API v1."""
    if request.method != 'POST':
        return redirect('edge_operations', edge_id=edge_id)
    edge = get_object_or_404(Edge, pk=edge_id)
    import json as _json
    try:
        body = _json.loads(request.body)
        older_than = int(body.get('older_than', 0))
        action = body.get('action', 'execute')
    except Exception:
        return JsonResponse({'error': 'Invalid request body'}, status=400)

    if action == 'preview':
        result = purge_preview(edge, older_than)
    else:
        result = purge_execute(edge, older_than)

    if result is None:
        return JsonResponse({'error': 'Edge unreachable or invalid API key'}, status=502)
    return JsonResponse(result)


@login_required
def edge_purge_local(request, edge_id):
    """Purge soca-control's own Alert records for this edge."""
    if request.method != 'POST':
        return redirect('edge_operations', edge_id=edge_id)
    edge = get_object_or_404(Edge, pk=edge_id)
    import json as _json
    from datetime import datetime, timezone, timedelta
    from .models import Alert

    VALID_DAYS = {30, 90, 180, 365}
    try:
        body = _json.loads(request.body)
        older_than = int(body.get('older_than', 0))
        action = body.get('action', 'execute')
    except Exception:
        return JsonResponse({'error': 'Invalid request body'}, status=400)

    if older_than not in VALID_DAYS:
        return JsonResponse({'error': 'Invalid older_than value'}, status=400)

    cutoff = datetime.now(tz=timezone.utc) - timedelta(days=older_than)
    qs = Alert.objects.filter(edge=edge, created_at__lt=cutoff)

    if action == 'preview':
        return JsonResponse({
            'older_than_days': older_than,
            'cutoff_date': cutoff.strftime('%Y-%m-%d'),
            'record_count': qs.count(),
        })

    deleted, _ = qs.delete()
    return JsonResponse({'deleted_records': deleted})
```

- [ ] **Step 3: Verify**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-control"
python manage.py check
```

Expected: `System check identified no issues`

- [ ] **Step 4: Commit**

```bash
git add app/views.py
git commit -m "feat: add edge operations views (schedule control, remote/local purge)"
```

---

## Task 4: URL Patterns

**Files:**
- Modify: `app/urls.py`

- [ ] **Step 1: Add new URL patterns**

After the existing `path('settings/<int:edge_id>/delete/', ...)` line (line 11), add:

```python
    # Edge Operations
    path('edges/<int:edge_id>/operations/', views.edge_operations, name='edge_operations'),
    path('edges/<int:edge_id>/operations/schedules/<int:sched_id>/start/', views.edge_schedule_start, name='edge_schedule_start'),
    path('edges/<int:edge_id>/operations/schedules/<int:sched_id>/stop/', views.edge_schedule_stop, name='edge_schedule_stop'),
    path('edges/<int:edge_id>/operations/purge/remote/', views.edge_purge_remote, name='edge_purge_remote'),
    path('edges/<int:edge_id>/operations/purge/local/', views.edge_purge_local, name='edge_purge_local'),
```

- [ ] **Step 2: Verify**

```bash
python manage.py check
```

- [ ] **Step 3: Commit**

```bash
git add app/urls.py
git commit -m "feat: add edge operations URL patterns"
```

---

## Task 5: Operations Page Template

**Files:**
- Create: `app/templates/edges/operations.html`

- [ ] **Step 1: Create templates directory and file**

```bash
mkdir -p "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-control/app/templates/edges"
```

- [ ] **Step 2: Create `app/templates/edges/operations.html`**

```html
{% extends 'base.html' %}
{% block title %}Operations — {{ edge.name }} — soca-control{% endblock %}
{% block content %}

<div class="flex items-center gap-3 mb-6">
  <a href="{% url 'settings' %}" class="btn btn-ghost btn-sm">← Settings</a>
  <h1 class="text-2xl font-bold">{{ edge.name }} — Operations</h1>
</div>

<div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">

  {# ── Schedule Control ── #}
  <div class="bg-base-100 rounded-box shadow p-4 lg:col-span-2">
    <h2 class="font-semibold text-lg mb-4">Schedule Control</h2>
    {% if schedules is None %}
      <div class="alert alert-warning">
        <span>Cannot reach edge API. Check connectivity and API key in Settings.</span>
      </div>
    {% elif not schedules %}
      <p class="text-sm text-base-content/40">No schedules configured on this edge.</p>
    {% else %}
      <div class="overflow-x-auto">
        <table class="table table-sm">
          <thead>
            <tr><th>Schedule</th><th>Camera</th><th>Category</th><th>Status</th><th>Actions</th></tr>
          </thead>
          <tbody id="schedule-tbody">
            {% for s in schedules %}
            <tr>
              <td class="font-medium">{{ s.name }}</td>
              <td class="text-base-content/70">{{ s.camera_name }}</td>
              <td>
                {% if s.alert_category %}
                  <span class="badge badge-sm badge-outline">{{ s.alert_category }}</span>
                {% else %}
                  <span class="opacity-30 text-xs">—</span>
                {% endif %}
              </td>
              <td>
                {% if s.status == 'running' %}
                  <span class="badge badge-success badge-sm">Running</span>
                {% elif s.status == 'unknown' %}
                  <span class="badge badge-warning badge-sm">Unknown</span>
                {% else %}
                  <span class="badge badge-ghost badge-sm">Stopped</span>
                {% endif %}
              </td>
              <td>
                <div class="flex gap-1">
                  <form method="post" action="{% url 'edge_schedule_start' edge.id s.id %}">
                    {% csrf_token %}
                    <button type="submit" class="btn btn-xs btn-success"
                      {% if s.status == 'running' %}disabled{% endif %}>Start</button>
                  </form>
                  <form method="post" action="{% url 'edge_schedule_stop' edge.id s.id %}">
                    {% csrf_token %}
                    <button type="submit" class="btn btn-xs btn-error"
                      {% if s.status != 'running' %}disabled{% endif %}>Stop</button>
                  </form>
                </div>
              </td>
            </tr>
            {% endfor %}
          </tbody>
        </table>
      </div>
      <p class="text-xs text-base-content/40 mt-2">Auto-refreshes every 10s</p>
    {% endif %}
  </div>

  {# ── Purge Edge Data (remote) ── #}
  <div class="bg-base-100 rounded-box shadow p-4">
    <h2 class="font-semibold text-lg mb-1">Purge Edge Data</h2>
    <p class="text-sm text-base-content/50 mb-4">
      Delete old detection records and snapshot files from <strong>{{ edge.name }}</strong>'s soca-engine database.
    </p>
    <div class="flex items-center gap-3 flex-wrap mb-3">
      <select id="remote-purge-age" class="select select-bordered select-sm">
        <option value="30">Older than 1 month</option>
        <option value="90">Older than 3 months</option>
        <option value="180">Older than 6 months</option>
        <option value="365">Older than 1 year</option>
      </select>
      <button onclick="previewPurge('remote')" class="btn btn-sm btn-outline">Preview</button>
      <button id="remote-purge-btn" onclick="document.getElementById('modal-remote-purge').showModal()"
        class="btn btn-sm btn-error" disabled>Purge Now</button>
    </div>
    <div id="remote-purge-result" class="text-sm text-base-content/60"></div>
  </div>

  {# ── Purge soca-control Records (local) ── #}
  <div class="bg-base-100 rounded-box shadow p-4">
    <h2 class="font-semibold text-lg mb-1">Purge Local Alert Records</h2>
    <p class="text-sm text-base-content/50 mb-4">
      Delete old <strong>soca-control</strong> Alert records for this edge (does not affect the edge itself).
    </p>
    <div class="flex items-center gap-3 flex-wrap mb-3">
      <select id="local-purge-age" class="select select-bordered select-sm">
        <option value="30">Older than 1 month</option>
        <option value="90">Older than 3 months</option>
        <option value="180">Older than 6 months</option>
        <option value="365">Older than 1 year</option>
      </select>
      <button onclick="previewPurge('local')" class="btn btn-sm btn-outline">Preview</button>
      <button id="local-purge-btn" onclick="document.getElementById('modal-local-purge').showModal()"
        class="btn btn-sm btn-error" disabled>Purge Now</button>
    </div>
    <div id="local-purge-result" class="text-sm text-base-content/60"></div>
  </div>

</div>

{# Confirmation modals #}
<dialog id="modal-remote-purge" class="modal">
  <div class="modal-box">
    <h3 class="font-bold text-lg text-error">Confirm Remote Purge</h3>
    <p id="modal-remote-purge-text" class="py-4 text-sm"></p>
    <div class="modal-action">
      <form method="dialog"><button class="btn btn-ghost">Cancel</button></form>
      <button onclick="executePurge('remote')" class="btn btn-error">Confirm Purge</button>
    </div>
  </div>
  <form method="dialog" class="modal-backdrop"><button>close</button></form>
</dialog>

<dialog id="modal-local-purge" class="modal">
  <div class="modal-box">
    <h3 class="font-bold text-lg text-error">Confirm Local Purge</h3>
    <p id="modal-local-purge-text" class="py-4 text-sm"></p>
    <div class="modal-action">
      <form method="dialog"><button class="btn btn-ghost">Cancel</button></form>
      <button onclick="executePurge('local')" class="btn btn-error">Confirm Purge</button>
    </div>
  </div>
  <form method="dialog" class="modal-backdrop"><button>close</button></form>
</dialog>

<script>
const EDGE_ID = {{ edge.id }};
const CSRF = '{{ csrf_token }}';

// Schedule table auto-refresh via AJAX (avoids wiping purge UI state)
function refreshSchedules() {
  if (document.visibilityState === 'hidden') return;
  fetch(`/edges/${EDGE_ID}/operations/`)
    .then(r => r.text())
    .then(html => {
      const parser = new DOMParser();
      const doc = parser.parseFromString(html, 'text/html');
      const newTbody = doc.getElementById('schedule-tbody');
      const curTbody = document.getElementById('schedule-tbody');
      if (newTbody && curTbody) curTbody.innerHTML = newTbody.innerHTML;
    })
    .catch(() => {});
}
setInterval(refreshSchedules, 10000);

// Purge
const purgeState = {remote: null, local: null};

function previewPurge(type) {
  const days = document.getElementById(type + '-purge-age').value;
  const resultEl = document.getElementById(type + '-purge-result');
  const btn = document.getElementById(type + '-purge-btn');
  resultEl.textContent = 'Loading...';
  btn.disabled = true;
  purgeState[type] = null;

  const url = `/edges/${EDGE_ID}/operations/purge/${type}/`;
  fetch(url, {
    method: 'POST',
    headers: {'Content-Type': 'application/json', 'X-CSRFToken': CSRF},
    body: JSON.stringify({older_than: parseInt(days), action: 'preview'}),
  })
    .then(r => r.json())
    .then(data => {
      if (data.error) { resultEl.textContent = 'Error: ' + data.error; return; }
      purgeState[type] = {days, data};
      if (type === 'remote') {
        const mb = (data.disk_bytes / 1048576).toFixed(1);
        resultEl.textContent = `${data.record_count.toLocaleString()} records · ${data.file_count.toLocaleString()} files · ${mb} MB`;
        document.getElementById('modal-remote-purge-text').textContent = resultEl.textContent + ' will be freed.';
      } else {
        resultEl.textContent = `${data.record_count.toLocaleString()} Alert records will be deleted`;
        document.getElementById('modal-local-purge-text').textContent = resultEl.textContent + '.';
      }
      btn.disabled = false;
    })
    .catch(() => { resultEl.textContent = 'Request failed.'; });
}

function executePurge(type) {
  document.getElementById('modal-' + type + '-purge').close();
  if (!purgeState[type]) return;
  const {days} = purgeState[type];
  const resultEl = document.getElementById(type + '-purge-result');
  const btn = document.getElementById(type + '-purge-btn');
  resultEl.textContent = 'Purging...';
  btn.disabled = true;

  fetch(`/edges/${EDGE_ID}/operations/purge/${type}/`, {
    method: 'POST',
    headers: {'Content-Type': 'application/json', 'X-CSRFToken': CSRF},
    body: JSON.stringify({older_than: parseInt(days), action: 'execute'}),
  })
    .then(r => r.json())
    .then(data => {
      if (data.error) { resultEl.textContent = 'Error: ' + data.error; return; }
      if (type === 'remote') {
        const mb = (data.freed_bytes / 1048576).toFixed(1);
        resultEl.textContent = `Done: ${data.deleted_records.toLocaleString()} records, ${data.deleted_files.toLocaleString()} files, ${mb} MB freed` +
          (data.skipped_files ? ` (${data.skipped_files} skipped)` : '');
      } else {
        resultEl.textContent = `Done: ${data.deleted_records.toLocaleString()} records deleted`;
      }
      purgeState[type] = null;
    })
    .catch(() => { resultEl.textContent = 'Request failed.'; });
}
</script>

{% endblock %}
```

- [ ] **Step 3: Commit**

```bash
git add app/templates/edges/
git commit -m "feat: add edge operations page template"
```

---

## Task 6: Settings Template — API Key Field + Operations Link

**Files:**
- Modify: `app/templates/settings/index.html`

- [ ] **Step 1: Add API Key field to "Register New Edge" form**

In the form grid, after the `redis_stream` field block (after line 35), add before `</div>` of the form grid:

```html
      <div class="form-control sm:col-span-2">
        <label class="label label-text text-sm">API Key <span class="opacity-40 text-xs font-normal">(from edge Settings > Operations)</span></label>
        <input name="api_key" placeholder="Paste API key from soca-dashboard" class="input input-bordered input-sm font-mono">
      </div>
```

- [ ] **Step 2: Update `settings_page` view to save `api_key` on add**

In `app/views.py`, in the `settings_page` view, after `redis_stream = request.POST.get(...)` (around line 235), add:

```python
            api_key = request.POST.get('api_key', '').strip()
```

And when creating the edge (after `edge = Edge(...)`), add `api_key=api_key` to the constructor:

```python
                edge = Edge(name=name, url=url, engine_url=engine_url,
                            redis_url=redis_url, redis_stream=redis_stream,
                            api_key=api_key)
```

- [ ] **Step 3: Add "Operations" link to the edge table**

In `app/templates/settings/index.html`, in the `<td>` actions column (after the Delete form, around line 77), add:

```html
            <a href="{% url 'edge_operations' edge.id %}" class="btn btn-xs btn-outline btn-info">Operations</a>
```

- [ ] **Step 4: Verify page loads**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-control"
python manage.py check
```

- [ ] **Step 5: Commit**

```bash
git add app/templates/settings/index.html app/views.py
git commit -m "feat: add API key field to edge registration + Operations link in settings"
```

---

## Task 7: Manual End-to-End Verification

Prerequisites: soca-dashboard Plan 1 is complete and running.

- [ ] Open soca-control Settings → add or edit an edge → paste API key from soca-dashboard Settings > Operations
- [ ] Click "Operations" link for that edge → `/edges/<id>/operations/` loads
- [ ] Schedule Control table shows schedules with correct status badges
- [ ] Click "Start" on a stopped schedule → page reloads → schedule shows Running
- [ ] Click "Stop" → schedule shows Stopped
- [ ] Select age, click "Preview" on "Purge Edge Data" → counts appear
- [ ] Confirm purge → deleted counts shown
- [ ] Select age, click "Preview" on "Purge Local Alert Records" → count of local Alert rows appears
- [ ] Confirm purge → local records deleted
- [ ] Remove API key from edge → reload Operations → "Cannot reach edge API" warning shown

- [ ] **Final commit**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-control"
git add -A
git commit -m "feat: edge operations complete (remote schedule control, purge, local alert purge)"
```
