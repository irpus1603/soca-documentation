# NX VMS Integration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add NX VMS (Network Optix) as an optional RTSP relay and AI event push target alongside existing MediaMTX support.

**Architecture:** A per-site `relay_type` toggle in `EdgeConfig` switches the entire edge between MediaMTX and NX VMS. When NX VMS is active, cameras are registered via NX REST API (physicalId stored on each Camera), soca-engine pulls RTSP from NX VMS instead of MediaMTX, and triggered rules fire HTTP Generic Events back to NX VMS.

**Tech Stack:** Django 5 (soca-dashboard), FastAPI + httpx (soca-engine), NX VMS REST API v3 (auth), v2 (devices), `/api/createEvent` (generic events).

---

## File Map

### soca-dashboard (new/modified)
| File | Change |
|------|--------|
| `soca-dashboard/nxvms.py` | **New** — `NXVMSClient` class: login, register/update/remove/list cameras |
| `soca-dashboard/app/models.py` | Add 5 fields to `EdgeConfig`, 1 field to `Camera`, 1 field to `Rule`; add `_resolve_rtsp_url()` helper to `Schedule`; update `to_job_config()` |
| `soca-dashboard/app/views.py` | Update `camera_form()`, `camera_delete()`, `edge_settings()` |
| `soca-dashboard/templates/settings/index.html` | Add NX VMS section + relay type toggle to Edge Config tab |
| `soca-dashboard/templates/cameras/form.html` | Show `nxvms_device_id` as read-only |
| `soca-dashboard/templates/cameras/list.html` | Add NX badge column |
| `soca-dashboard/app/management/__init__.py` | **New** — empty |
| `soca-dashboard/app/management/commands/__init__.py` | **New** — empty |
| `soca-dashboard/app/management/commands/register_cameras_to_nxvms.py` | **New** — batch registration command |

### soca-engine (new/modified)
| File | Change |
|------|--------|
| `soca-engine/core/nxvms_client.py` | **New** — `NXVMSEventPusher` class: login, push_event |
| `soca-engine/models/schemas.py` | Add `nxvms: dict \| None = None` field to `JobConfig` |
| `soca-engine/core/action_dispatcher.py` | Add `nxvms_pusher` + `nxvms_device_id` params to `dispatch()`; add `nxvms_event` handler |
| `soca-engine/workers/detection_worker.py` | Instantiate `NXVMSEventPusher` from `cfg.nxvms`; pass to `dispatch()` |

---

## Task 1: soca-dashboard Models & Migration

**Files:**
- Modify: `soca-dashboard/app/models.py`

- [ ] **Step 1: Add fields to `EdgeConfig`**

In `soca-dashboard/app/models.py`, after `last_engine_push_ok` (line 71), add:

```python
    relay_type = models.CharField(
        max_length=10, default='mediamtx',
        choices=[('mediamtx', 'MediaMTX'), ('nxvms', 'NX VMS')],
        help_text='RTSP relay: MediaMTX (file-based) or NX VMS (REST API)'
    )
    nxvms_url = models.CharField(max_length=200, blank=True, default='',
        help_text='NX VMS server URL, e.g. http://192.168.1.100:7001')
    nxvms_username = models.CharField(max_length=100, blank=True, default='')
    nxvms_password = models.CharField(max_length=100, blank=True, default='')
    nxvms_rtsp_url = models.CharField(max_length=200, blank=True, default='',
        help_text='NX VMS RTSP base URL, e.g. rtsp://192.168.1.100:7554')
```

- [ ] **Step 2: Add `nxvms_device_id` to `Camera`**

After `created_at` (line 89):

```python
    nxvms_device_id = models.CharField(max_length=200, blank=True, default='',
        help_text='NX VMS physicalId stored after registration')
```

- [ ] **Step 3: Add `action_nxvms` to `Rule`**

After `action_snapshot` (line 290):

```python
    action_nxvms = models.BooleanField(default=False,
        help_text='Fire NX VMS HTTP Generic Event on trigger')
```

- [ ] **Step 4: Add `_resolve_rtsp_url()` helper to `Schedule`**

Add this method to the `Schedule` class (before `to_job_config`):

```python
    def _resolve_rtsp_url(self, edge) -> str:
        """Build the RTSP URL soca-engine should connect to, based on relay_type."""
        if edge and getattr(edge, 'relay_type', 'mediamtx') == 'nxvms' and edge.nxvms_rtsp_url:
            device_id = self.camera.nxvms_device_id
            if not device_id:
                import logging
                logging.getLogger(__name__).warning(
                    f"Camera '{self.camera.name}' has no nxvms_device_id — falling back to direct RTSP"
                )
                return self.camera.full_rtsp_url
            return f"{edge.nxvms_rtsp_url.rstrip('/')}/{device_id}"
        if edge and edge.mediamtx_rtsp_url:
            return f"{edge.mediamtx_rtsp_url.rstrip('/')}/{self.camera.name}"
        return self.camera.full_rtsp_url
```

- [ ] **Step 5: Update `to_job_config()` — replace RTSP URL blocks and add NX VMS config**

There are two places in `to_job_config()` that build `rtsp_url` (lines ~198-202 and ~248-252). Replace **both** identical blocks:

```python
# OLD (two places):
rtsp_url = (
    f"{edge.mediamtx_rtsp_url.rstrip('/')}/{self.camera.name}"
    if edge and edge.mediamtx_rtsp_url
    else self.camera.full_rtsp_url
)

# NEW (replace both):
rtsp_url = self._resolve_rtsp_url(edge)
```

Also add NX VMS config and `nxvms_event` action in the **rules branch** (after building the `rules_config` list, before `return`). Add after the `rtsp_url = self._resolve_rtsp_url(edge)` line in the rules branch:

```python
            nxvms_cfg = None
            if edge and getattr(edge, 'relay_type', 'mediamtx') == 'nxvms' and edge.nxvms_url:
                nxvms_cfg = {
                    'url': edge.nxvms_url,
                    'username': edge.nxvms_username,
                    'password': edge.nxvms_password,
                    'device_id': self.camera.nxvms_device_id,
                }
```

And in the `rules_config` loop, after the Telegram action block, add:

```python
                if rule.action_nxvms and nxvms_cfg:
                    actions.append({"type": "nxvms_event"})
```

Update the `return` dict to include:

```python
                "nxvms": nxvms_cfg,
```

- [ ] **Step 6: Run migrations**

```bash
cd /Users/mac-mini-home/Supriyadi/Projects/soca-ioh/soca-dashboard
python manage.py makemigrations app
python manage.py migrate
```

Expected output: new migration file + `Applying app.XXXX_...OK`

---

## Task 2: soca-dashboard NX VMS Client

**Files:**
- Create: `soca-dashboard/nxvms.py`

- [ ] **Step 1: Create `nxvms.py`**

```python
import logging
import httpx

logger = logging.getLogger(__name__)


class NXVMSError(Exception):
    pass


class NXVMSClient:
    """REST client for NX VMS (Network Optix) server.

    Authenticates on construction and caches the bearer token.
    Re-authenticates automatically on 401 responses.
    """

    def __init__(self, base_url: str, username: str, password: str):
        self._base_url = base_url.rstrip('/')
        self._username = username
        self._password = password
        self._token: str | None = None
        self._login()

    # ── Auth ──────────────────────────────────────────────────────────────────

    def _login(self):
        try:
            resp = httpx.post(
                f"{self._base_url}/rest/v3/login/sessions",
                json={"username": self._username, "password": self._password, "setCookie": False},
                timeout=10,
            )
            resp.raise_for_status()
            self._token = resp.json().get("token") or resp.json().get("authToken")
            if not self._token:
                raise NXVMSError(f"Login succeeded but no token in response: {resp.text[:200]}")
        except httpx.HTTPError as e:
            raise NXVMSError(f"NX VMS login failed: {e}") from e

    def _headers(self) -> dict:
        return {"Authorization": f"Bearer {self._token}", "Content-Type": "application/json"}

    def _request(self, method: str, path: str, **kwargs):
        """Make authenticated request; retry once after re-login on 401."""
        url = f"{self._base_url}{path}"
        resp = httpx.request(method, url, headers=self._headers(), timeout=10, **kwargs)
        if resp.status_code == 401:
            self._login()
            resp = httpx.request(method, url, headers=self._headers(), timeout=10, **kwargs)
        return resp

    # ── Camera CRUD ───────────────────────────────────────────────────────────

    def register_camera(self, name: str, rtsp_url: str,
                        cam_username: str = '', cam_password: str = '') -> str:
        """Register a new RTSP camera in NX VMS. Returns physicalId (device_id)."""
        payload = {
            "name": name,
            "url": rtsp_url,
            "typeId": "{7d2af20d-04f2-149f-ef37-ad585281e3b7}",  # Generic RTSP camera typeId
        }
        if cam_username:
            payload["credentials"] = {"user": cam_username, "password": cam_password}
        try:
            resp = self._request("POST", "/rest/v2/devices", json=payload)
            resp.raise_for_status()
            data = resp.json()
            # NX VMS returns {"id": "...", "physicalId": "..."}
            device_id = data.get("physicalId") or data.get("id") or ""
            if not device_id:
                raise NXVMSError(f"NX VMS device registration returned no id: {data}")
            logger.info(f"NX VMS: registered '{name}' → physicalId={device_id}")
            return device_id
        except httpx.HTTPError as e:
            raise NXVMSError(f"NX VMS register_camera failed: {e}") from e

    def update_camera(self, device_id: str, name: str, rtsp_url: str,
                      cam_username: str = '', cam_password: str = ''):
        """Update an existing camera's name, URL, and credentials."""
        payload = {"name": name, "url": rtsp_url}
        if cam_username:
            payload["credentials"] = {"user": cam_username, "password": cam_password}
        try:
            resp = self._request("PATCH", f"/rest/v2/devices/{device_id}", json=payload)
            resp.raise_for_status()
            logger.info(f"NX VMS: updated device {device_id} → '{name}'")
        except httpx.HTTPError as e:
            raise NXVMSError(f"NX VMS update_camera failed: {e}") from e

    def remove_camera(self, device_id: str):
        """Delete a camera from NX VMS by its physicalId/deviceId."""
        try:
            resp = self._request("DELETE", f"/rest/v2/devices/{device_id}")
            if resp.status_code == 404:
                logger.warning(f"NX VMS: device {device_id} not found on delete (already removed?)")
                return
            resp.raise_for_status()
            logger.info(f"NX VMS: removed device {device_id}")
        except httpx.HTTPError as e:
            raise NXVMSError(f"NX VMS remove_camera failed: {e}") from e

    def list_cameras(self) -> list[dict]:
        """Return all devices registered in NX VMS."""
        try:
            resp = self._request("GET", "/rest/v2/devices")
            resp.raise_for_status()
            return resp.json() if isinstance(resp.json(), list) else resp.json().get("data", [])
        except httpx.HTTPError as e:
            raise NXVMSError(f"NX VMS list_cameras failed: {e}") from e
```

---

## Task 3: soca-dashboard Camera Registration Integration

**Files:**
- Modify: `soca-dashboard/app/views.py`

The import for `mediamtx` is already at the top of views.py as `import mediamtx as mtx`. Add after it:

- [ ] **Step 1: Add import at top of `views.py`**

Find the `import mediamtx as mtx` line and add below it:

```python
import nxvms as nvx
from nxvms import NXVMSError
```

- [ ] **Step 2: Update `camera_form()` — create path**

In `camera_form()`, find the block that creates a new camera (lines ~140-145):

```python
        else:
            camera = Camera.objects.create(**fields)
            cfg = EdgeConfig.objects.first()
            if cfg:
                mtx.add_source(_yml_path(cfg), name, rtsp_url, username, password)
            messages.success(request, f"Camera '{name}' created.")
```

Replace with:

```python
        else:
            camera = Camera.objects.create(**fields)
            cfg = EdgeConfig.objects.first()
            if cfg:
                if getattr(cfg, 'relay_type', 'mediamtx') == 'nxvms' and cfg.nxvms_url:
                    try:
                        client = nvx.NXVMSClient(cfg.nxvms_url, cfg.nxvms_username, cfg.nxvms_password)
                        device_id = client.register_camera(name, rtsp_url, username, password)
                        camera.nxvms_device_id = device_id
                        camera.save(update_fields=['nxvms_device_id'])
                    except NXVMSError as e:
                        logger.warning(f"NX VMS registration failed for '{name}': {e}")
                        messages.warning(request, f"Camera saved but NX VMS registration failed: {e}")
                else:
                    mtx.add_source(_yml_path(cfg), name, rtsp_url, username, password)
            messages.success(request, f"Camera '{name}' created.")
```

- [ ] **Step 3: Update `camera_form()` — update path**

Find the existing update block (lines ~124-139) and add NX VMS after the MediaMTX update block:

```python
        if camera:
            mtx_changed = (
                name != old_name
                or rtsp_url != camera.rtsp_url
                or username != camera.username
                or password != camera.password
            )
            for k, v in fields.items():
                setattr(camera, k, v)
            camera.save()
            cfg = EdgeConfig.objects.first()
            if cfg:
                if getattr(cfg, 'relay_type', 'mediamtx') == 'nxvms' and cfg.nxvms_url:
                    if mtx_changed and camera.nxvms_device_id:
                        try:
                            client = nvx.NXVMSClient(cfg.nxvms_url, cfg.nxvms_username, cfg.nxvms_password)
                            client.update_camera(camera.nxvms_device_id, name, rtsp_url, username, password)
                        except NXVMSError as e:
                            logger.warning(f"NX VMS update failed for '{name}': {e}")
                            messages.warning(request, f"Camera saved but NX VMS update failed: {e}")
                elif mtx_changed:
                    mtx.update_source(_yml_path(cfg), old_name, name, rtsp_url, username, password)
            messages.success(request, f"Camera '{name}' updated.")
```

- [ ] **Step 4: Update `camera_delete()`**

Replace the existing `camera_delete()` body:

```python
@role_required('admin', 'operator')
def camera_delete(request, pk):
    camera = get_object_or_404(Camera, pk=pk)
    if request.method == "POST":
        cfg = EdgeConfig.objects.first()
        if cfg:
            if getattr(cfg, 'relay_type', 'mediamtx') == 'nxvms' and camera.nxvms_device_id:
                try:
                    client = nvx.NXVMSClient(cfg.nxvms_url, cfg.nxvms_username, cfg.nxvms_password)
                    client.remove_camera(camera.nxvms_device_id)
                except NXVMSError as e:
                    logger.warning(f"NX VMS removal failed for '{camera.name}': {e}")
            else:
                mtx.remove_source(_yml_path(cfg), camera.name)
        camera.delete()
        messages.success(request, "Camera deleted.")
    return redirect("camera_list")
```

---

## Task 4: soca-dashboard `edge_settings()` View

**Files:**
- Modify: `soca-dashboard/app/views.py`

- [ ] **Step 1: Add NX VMS field assignments in `edge_settings()`**

Find the `edge_settings()` function. After the line `edge.publisher_type = p.get("publisher_type", "redis")`, add:

```python
        edge.relay_type = p.get("relay_type", "mediamtx")
        edge.nxvms_url = p.get("nxvms_url", "").strip()
        edge.nxvms_username = p.get("nxvms_username", "").strip()
        if p.get("nxvms_password"):  # only update if non-empty (avoid clearing stored password)
            edge.nxvms_password = p.get("nxvms_password")
        edge.nxvms_rtsp_url = p.get("nxvms_rtsp_url", "").strip()
```

---

## Task 5: soca-dashboard Settings UI

**Files:**
- Modify: `soca-dashboard/templates/settings/index.html`

- [ ] **Step 1: Add `relay_type` toggle + NX VMS fields to Edge Config tab**

In the Edge Config tab section (after the `<p class="text-xs font-semibold uppercase tracking-wider opacity-50 mb-3">Connection</p>` line), add a new relay type section before the existing Connection grid:

```html
          <!-- Relay Type -->
          <div class="form-control mb-4">
            <label class="label"><span class="label-text font-medium">RTSP Relay Type</span></label>
            <div class="flex gap-6">
              <label class="flex items-center gap-2 cursor-pointer">
                <input type="radio" name="relay_type" value="mediamtx" class="radio radio-sm"
                       {% if edge.relay_type != 'nxvms' %}checked{% endif %}
                       onchange="toggleRelayFields(this.value)" />
                <span class="text-sm">MediaMTX</span>
              </label>
              <label class="flex items-center gap-2 cursor-pointer">
                <input type="radio" name="relay_type" value="nxvms" class="radio radio-sm radio-primary"
                       {% if edge.relay_type == 'nxvms' %}checked{% endif %}
                       onchange="toggleRelayFields(this.value)" />
                <span class="text-sm">NX VMS</span>
              </label>
            </div>
            <label class="label"><span class="label-text-alt opacity-60">Determines how camera RTSP streams are relayed to soca-engine</span></label>
          </div>

          <!-- NX VMS Fields -->
          <div id="nxvms-fields" class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6 p-4 bg-base-200/50 rounded-lg border border-base-300"
               style="{% if edge.relay_type != 'nxvms' %}display:none{% endif %}">
            <div class="md:col-span-2">
              <p class="text-xs font-semibold uppercase tracking-wider opacity-50 mb-2">NX VMS Connection</p>
            </div>
            <div class="form-control md:col-span-2">
              <label class="label"><span class="label-text font-medium">NX VMS Server URL</span></label>
              <input type="text" name="nxvms_url" value="{{ edge.nxvms_url|default:'' }}"
                     placeholder="http://192.168.1.100:7001"
                     class="input input-bordered font-mono text-sm" />
              <label class="label"><span class="label-text-alt opacity-60">Base URL of NX VMS server (REST API endpoint)</span></label>
            </div>
            <div class="form-control">
              <label class="label"><span class="label-text font-medium">NX VMS Username</span></label>
              <input type="text" name="nxvms_username" value="{{ edge.nxvms_username|default:'' }}"
                     placeholder="admin" class="input input-bordered" autocomplete="off" />
            </div>
            <div class="form-control">
              <label class="label"><span class="label-text font-medium">NX VMS Password</span></label>
              <input type="password" name="nxvms_password" value=""
                     placeholder="Leave blank to keep current"
                     class="input input-bordered" autocomplete="off" />
              <label class="label"><span class="label-text-alt opacity-60">Leave blank to keep existing password</span></label>
            </div>
            <div class="form-control md:col-span-2">
              <label class="label"><span class="label-text font-medium">NX VMS RTSP Base URL</span></label>
              <input type="text" name="nxvms_rtsp_url" value="{{ edge.nxvms_rtsp_url|default:'' }}"
                     placeholder="rtsp://192.168.1.100:7554"
                     class="input input-bordered font-mono text-sm" />
              <label class="label"><span class="label-text-alt opacity-60">RTSP relay URL soca-engine connects to. Streams served as {base_url}/{physicalId}</span></label>
            </div>
          </div>
```

- [ ] **Step 2: Add `toggleRelayFields()` JS function**

In the `<script>` block at the bottom, add:

```javascript
function toggleRelayFields(val) {
  document.getElementById('nxvms-fields').style.display = val === 'nxvms' ? '' : 'none';
}
```

- [ ] **Step 3: Add hidden `relay_type` + NX VMS fields to the Telegram tab form**

The Telegram tab form uses hidden inputs to preserve EdgeConfig values. Add before the `<div class="grid...">` in the Telegram form:

```html
          <input type="hidden" name="relay_type" value="{{ edge.relay_type|default:'mediamtx' }}" />
          <input type="hidden" name="nxvms_url" value="{{ edge.nxvms_url|default:'' }}" />
          <input type="hidden" name="nxvms_username" value="{{ edge.nxvms_username|default:'' }}" />
          <input type="hidden" name="nxvms_password" value="" />
          <input type="hidden" name="nxvms_rtsp_url" value="{{ edge.nxvms_rtsp_url|default:'' }}" />
```

---

## Task 6: Camera Form & List UI

**Files:**
- Modify: `soca-dashboard/templates/cameras/form.html`
- Modify: `soca-dashboard/templates/cameras/list.html`

- [ ] **Step 1: Add `nxvms_device_id` read-only field to camera form**

In `cameras/form.html`, after the `is_active` checkbox block (before `<div class="divider">`), add:

```html
        {% if camera and camera.nxvms_device_id %}
        <div class="form-control mt-4">
          <label class="label"><span class="label-text font-medium">NX VMS Device ID</span></label>
          <input type="text" value="{{ camera.nxvms_device_id }}" class="input input-bordered font-mono text-sm opacity-60" readonly />
          <label class="label"><span class="label-text-alt opacity-60">Assigned by NX VMS after registration — do not edit</span></label>
        </div>
        {% endif %}
```

- [ ] **Step 2: Add NX badge to camera list**

In `cameras/list.html`, in the table header row after `<th ... >Status</th>`, add:

```html
              <th class="text-xs font-semibold text-base-content/60">NX VMS</th>
```

In the table body row after the status `<td>` block, add:

```html
              <td>
                {% if cam.nxvms_device_id %}
                  <span class="badge badge-primary badge-xs" title="Registered in NX VMS: {{ cam.nxvms_device_id }}">NX</span>
                {% else %}
                  <span class="text-base-content/30 text-xs">—</span>
                {% endif %}
              </td>
```

---

## Task 7: Batch Registration Management Command

**Files:**
- Create: `soca-dashboard/app/management/__init__.py`
- Create: `soca-dashboard/app/management/commands/__init__.py`
- Create: `soca-dashboard/app/management/commands/register_cameras_to_nxvms.py`

- [ ] **Step 1: Create package files**

```bash
touch /Users/mac-mini-home/Supriyadi/Projects/soca-ioh/soca-dashboard/app/management/__init__.py
touch /Users/mac-mini-home/Supriyadi/Projects/soca-ioh/soca-dashboard/app/management/commands/__init__.py
```

- [ ] **Step 2: Create the management command**

```python
# soca-dashboard/app/management/commands/register_cameras_to_nxvms.py
import sys
from django.core.management.base import BaseCommand
from app.models import Camera, EdgeConfig
import nxvms as nvx
from nxvms import NXVMSError


class Command(BaseCommand):
    help = (
        'Register all active soca-dashboard cameras to NX VMS. '
        'Stores the returned physicalId in Camera.nxvms_device_id. '
        'Requires EdgeConfig.relay_type == "nxvms" and nxvms_url to be set.'
    )

    def add_arguments(self, parser):
        parser.add_argument('--dry-run', action='store_true',
                            help='Print what would be registered without making API calls.')
        parser.add_argument('--force', action='store_true',
                            help='Re-register cameras that already have a nxvms_device_id.')

    def handle(self, *args, **options):
        dry_run = options['dry_run']
        force = options['force']

        edge = EdgeConfig.objects.first()
        if not edge:
            self.stderr.write(self.style.ERROR('No EdgeConfig found. Run the dashboard and create one.'))
            sys.exit(1)

        if getattr(edge, 'relay_type', 'mediamtx') != 'nxvms':
            self.stderr.write(self.style.ERROR(
                f"EdgeConfig.relay_type is '{edge.relay_type}', not 'nxvms'. "
                "Switch relay type to NX VMS in Settings → Edge Config first."
            ))
            sys.exit(1)

        if not edge.nxvms_url:
            self.stderr.write(self.style.ERROR('EdgeConfig.nxvms_url is empty. Set it in Settings → Edge Config.'))
            sys.exit(1)

        cameras = Camera.objects.filter(is_active=True).order_by('name')
        total = cameras.count()
        self.stdout.write(f"Found {total} active camera(s). NX VMS: {edge.nxvms_url}")

        if dry_run:
            self.stdout.write(self.style.WARNING('[DRY RUN] No API calls will be made.'))

        client = None
        if not dry_run:
            try:
                client = nvx.NXVMSClient(edge.nxvms_url, edge.nxvms_username, edge.nxvms_password)
                self.stdout.write(self.style.SUCCESS('  ✓ Authenticated with NX VMS'))
            except NXVMSError as e:
                self.stderr.write(self.style.ERROR(f'  ✗ Authentication failed: {e}'))
                sys.exit(1)

        registered = skipped = failed = 0

        for cam in cameras:
            if cam.nxvms_device_id and not force:
                self.stdout.write(f'  - {cam.name}: skip (already has device_id={cam.nxvms_device_id})')
                skipped += 1
                continue

            if dry_run:
                self.stdout.write(f'  [dry] {cam.name}: would register rtsp_url={cam.rtsp_url}')
                registered += 1
                continue

            try:
                device_id = client.register_camera(cam.name, cam.rtsp_url, cam.username, cam.password)
                cam.nxvms_device_id = device_id
                cam.save(update_fields=['nxvms_device_id'])
                self.stdout.write(self.style.SUCCESS(f'  ✓ {cam.name} → {device_id}'))
                registered += 1
            except NXVMSError as e:
                self.stderr.write(self.style.ERROR(f'  ✗ {cam.name}: {e}'))
                failed += 1

        self.stdout.write('')
        self.stdout.write(
            f"Done: {registered} registered, {skipped} skipped, {failed} failed"
            + (' [dry run]' if dry_run else '')
        )
        if failed:
            sys.exit(1)
```

- [ ] **Step 3: Verify command is discoverable**

```bash
cd /Users/mac-mini-home/Supriyadi/Projects/soca-ioh/soca-dashboard
python manage.py help register_cameras_to_nxvms
```

Expected: Shows command help text without errors.

---

## Task 8: soca-engine — `JobConfig` & `NXVMSEventPusher`

**Files:**
- Modify: `soca-engine/models/schemas.py`
- Create: `soca-engine/core/nxvms_client.py`

- [ ] **Step 1: Add `nxvms` field to `JobConfig`**

In `soca-engine/models/schemas.py`, in the `JobConfig` class after `imgsz: int | None = None` (line 81), add:

```python
    nxvms: dict | None = None  # NX VMS config: {url, username, password, device_id}
    source: str = 'frame'      # 'frame' | 'video'  (already present — do not duplicate)
    agnostic_nms: bool = False  # (already present — do not duplicate)
```

Note: Only add the `nxvms` field. `source` and `agnostic_nms` may already be there — check the file first and only add what's missing.

- [ ] **Step 2: Create `soca-engine/core/nxvms_client.py`**

```python
import logging
import threading
from datetime import datetime

import httpx

logger = logging.getLogger(__name__)


class NXVMSEventPusher:
    """Pushes AI detection events to NX VMS as HTTP Generic Events.

    Instantiate once per detection job. Thread-safe: push_event() fires
    asynchronously via a daemon thread so it never blocks the inference loop.
    """

    def __init__(self, base_url: str, username: str, password: str):
        self._base_url = base_url.rstrip('/')
        self._username = username
        self._password = password
        self._token: str | None = None
        self._lock = threading.Lock()
        self._login()

    def _login(self):
        try:
            resp = httpx.post(
                f"{self._base_url}/rest/v3/login/sessions",
                json={"username": self._username, "password": self._password, "setCookie": False},
                timeout=10,
            )
            resp.raise_for_status()
            token = resp.json().get("token") or resp.json().get("authToken")
            if not token:
                logger.error(f"NX VMS login succeeded but no token: {resp.text[:200]}")
                return
            with self._lock:
                self._token = token
            logger.info("NXVMSEventPusher: authenticated successfully")
        except Exception as e:
            logger.error(f"NXVMSEventPusher: login failed: {e}")

    def _headers(self) -> dict:
        return {"Authorization": f"Bearer {self._token}", "Content-Type": "application/json"}

    def push_event(self, device_id: str, caption: str, description: str, timestamp: datetime):
        """Fire NX VMS HTTP Generic Event asynchronously (non-blocking)."""
        threading.Thread(
            target=self._send_event,
            args=(device_id, caption, description, timestamp),
            daemon=True,
        ).start()

    def _send_event(self, device_id: str, caption: str, description: str, timestamp: datetime):
        with self._lock:
            token = self._token
        if not token:
            logger.warning("NXVMSEventPusher: no token — skipping event push")
            return
        payload = {
            "timestamp": int(timestamp.timestamp() * 1000),  # NX VMS expects ms epoch
            "state": "instant",
            "source": "soca-engine",
            "caption": caption[:255],
            "description": description[:1000],
            "deviceIds": [device_id] if device_id else [],
        }
        try:
            resp = httpx.post(
                f"{self._base_url}/api/createEvent",
                json=payload,
                headers={"Authorization": f"Bearer {token}", "Content-Type": "application/json"},
                timeout=5,
            )
            if resp.status_code == 401:
                logger.info("NXVMSEventPusher: token expired — re-authenticating")
                self._login()
                with self._lock:
                    token = self._token
                if token:
                    httpx.post(
                        f"{self._base_url}/api/createEvent",
                        json=payload,
                        headers={"Authorization": f"Bearer {token}", "Content-Type": "application/json"},
                        timeout=5,
                    )
            elif not resp.is_success:
                logger.warning(f"NXVMSEventPusher: createEvent returned {resp.status_code}: {resp.text[:200]}")
        except Exception as e:
            logger.warning(f"NXVMSEventPusher: push_event failed: {e}")
```

---

## Task 9: soca-engine — Action Dispatcher `nxvms_event`

**Files:**
- Modify: `soca-engine/core/action_dispatcher.py`

- [ ] **Step 1: Add `nxvms_pusher` and `nxvms_device_id` parameters to `dispatch()`**

Change the `dispatch()` signature from:

```python
def dispatch(result: FrameResult, frame: np.ndarray, rules: list[Rule],
             snapshot_mgr: SnapshotManager, stream_name: str, roi=None) -> bool:
```

To:

```python
def dispatch(result: FrameResult, frame: np.ndarray, rules: list[Rule],
             snapshot_mgr: SnapshotManager, stream_name: str, roi=None,
             nxvms_pusher=None, nxvms_device_id: str = "") -> bool:
```

- [ ] **Step 2: Add `nxvms_event` handler**

In `dispatch()`, inside the `for action in rule.actions:` loop, after the `elif action.type == "log":` block (before `return True`), add:

```python
            elif action.type == "nxvms_event" and nxvms_pusher is not None:
                in_roi = sum(1 for d in result.detections if d.in_roi)
                caption = f"{rule_result.rule_name}"
                if result.alert_category:
                    caption = f"{result.alert_category} — {rule_result.rule_name}"
                desc_parts = [
                    f"Camera: {result.camera_name or result.camera_id}",
                    f"Detections in ROI: {in_roi}",
                    f"Job: {result.job_id}",
                ]
                if result.snapshot_path:
                    desc_parts.append(f"Snapshot: {result.snapshot_path}")
                nxvms_pusher.push_event(
                    device_id=nxvms_device_id,
                    caption=caption[:255],
                    description="\n".join(desc_parts),
                    timestamp=result.timestamp,
                )
```

---

## Task 10: soca-engine — Detection Worker Wires `NXVMSEventPusher`

**Files:**
- Modify: `soca-engine/workers/detection_worker.py`

- [ ] **Step 1: Import `NXVMSEventPusher`**

At the top of `detection_worker.py`, after the existing core imports, add:

```python
from core.nxvms_client import NXVMSEventPusher
```

- [ ] **Step 2: Instantiate `NXVMSEventPusher` in `_run()`**

In `DetectionWorker._run()`, after `snapshot_mgr = SnapshotManager(...)` (line ~358), add:

```python
        # NX VMS event pusher — only if nxvms config is present in job config
        nxvms_pusher = None
        nxvms_device_id = ""
        if cfg.nxvms and cfg.nxvms.get('url'):
            try:
                nxvms_pusher = NXVMSEventPusher(
                    cfg.nxvms['url'],
                    cfg.nxvms.get('username', ''),
                    cfg.nxvms.get('password', ''),
                )
                nxvms_device_id = cfg.nxvms.get('device_id', '')
                logger.info(f"NX VMS event push enabled for job {cfg.job_id}")
            except Exception as e:
                logger.warning(f"NX VMS EventPusher init failed: {e} — NX events disabled")
```

- [ ] **Step 3: Pass `nxvms_pusher` to `dispatch()`**

Find line 496:

```python
                dispatched = action_dispatcher.dispatch(result, frame, cfg.rules, snapshot_mgr, cfg.output.stream_name, cfg.roi)
```

Replace with:

```python
                dispatched = action_dispatcher.dispatch(
                    result, frame, cfg.rules, snapshot_mgr, cfg.output.stream_name, cfg.roi,
                    nxvms_pusher=nxvms_pusher, nxvms_device_id=nxvms_device_id,
                )
```

---

## Task 11: Rule Form UI — `action_nxvms` Toggle

**Files:**
- Modify: `soca-dashboard/templates/schedules/_rule_fields.html`

- [ ] **Step 1: Read the current rule fields template**

```bash
cat /Users/mac-mini-home/Supriyadi/Projects/soca-ioh/soca-dashboard/templates/schedules/_rule_fields.html
```

Find the existing action checkboxes (`action_snapshot`, `action_redis`, `action_telegram`) and add an NX VMS checkbox after the Telegram one:

```html
              <label class="flex items-center gap-2 cursor-pointer">
                <input type="checkbox" name="action_nxvms" class="checkbox checkbox-sm checkbox-primary"
                       {% if rule.action_nxvms %}checked{% endif %} />
                <span class="text-sm">NX VMS Event</span>
              </label>
```

- [ ] **Step 2: Update `schedule_form()` view to save `action_nxvms`**

In `soca-dashboard/app/views.py`, in `schedule_form()`, find where rule fields are extracted from POST and add:

```python
rule.action_nxvms = request.POST.get(f"action_nxvms_{rule_idx}") == "on"
```

(The exact field name depends on how the form names inputs — read the existing rule-save logic first to match the pattern.)

---

## Verification Steps

- [ ] **V1: EdgeConfig UI** — Open Dashboard → Settings → Edge Config. Confirm "RTSP Relay Type" radio appears. Select NX VMS → NX VMS fields slide into view. Select MediaMTX → NX VMS fields hide.

- [ ] **V2: Camera Registration** — With relay_type=nxvms configured, add a new camera. Check the camera list: NX badge appears. Check DB: `Camera.nxvms_device_id` is populated.

- [ ] **V3: Camera Update** — Edit the camera's RTSP URL. Check NX VMS: device is updated, not duplicated.

- [ ] **V4: Camera Delete** — Delete the camera. Check NX VMS: device is removed. No errors in dashboard.

- [ ] **V5: RTSP URL in JobConfig** — Start a schedule. In soca-engine logs, confirm the job's rtsp_url is `rtsp://{nxvms_host}:7554/{physicalId}`.

- [ ] **V6: NX VMS Event** — Enable `action_nxvms` on a rule. Trigger a detection. Check NX VMS event log: a "soca-engine" generic event appears.

- [ ] **V7: Batch Command** — With 3 cameras lacking `nxvms_device_id`, run:

```bash
cd soca-dashboard
python manage.py register_cameras_to_nxvms --dry-run
python manage.py register_cameras_to_nxvms
```

Confirm all cameras get `nxvms_device_id` after the second run.

- [ ] **V8: MediaMTX fallback** — Switch EdgeConfig back to `relay_type=mediamtx`. Start a schedule. Confirm rtsp_url is `rtsp://localhost:8554/{camera_name}`.
