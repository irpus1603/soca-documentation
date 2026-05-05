# SOCA Licensing Model

**Date:** 2026-05-01  
**Version:** 1.2  
**Scope:** soca-dashboard · soca-engine · soca-entitlement

---

## Overview

SOCA uses a server-side license model managed by **soca-entitlement** — a dedicated Django service that issues and tracks UUID license keys. Each edge device activates one key; the entitlement server returns the device's allowed limits. Without an active license, an edge runs in **free tier** (1 active camera, 1 concurrent AI model).

```
┌─────────────────────────────────────────────────────────────┐
│                        EDGE DEVICE                          │
│                                                             │
│  soca-dashboard                    soca-engine              │
│  ┌───────────────────────┐         ┌──────────────────────┐ │
│  │ Settings → License tab│         │ MAX_CONCURRENT_JOBS  │ │
│  │ [Activate License]    │         │ MAX_LOADED_MODELS     │ │
│  │                       │◄──env──►│  (read from .env)    │ │
│  │ EdgeConfig            │         └──────────────────────┘ │
│  │  license_key          │                                   │
│  │  license_status       │                                   │
│  │  license_max_cameras  │                                   │
│  │  license_max_models   │                                   │
│  └──────────┬────────────┘                                   │
│             │ HTTP POST/GET                                   │
└─────────────┼───────────────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────────────────────────┐
│              soca-entitlement  (port 8080)                   │
│              http://34.101.213.40:8080                       │
│                                                              │
│  POST /api/v1/license/activate/    ← one-time activation     │
│  GET  /api/v1/license/check/<key>/ ← status check / refresh  │
└─────────────────────────────────────────────────────────────┘
```

---

## Free Tier vs Licensed

| Limit | Free Tier | Licensed |
|---|---|---|
| Active cameras | **1** | `max_cameras` from license |
| Concurrent AI models | **1** | `max_ai_models` from license |
| `MAX_CONCURRENT_JOBS` in engine | **1** | synced from license |
| `MAX_LOADED_MODELS` in engine | **1** | synced from license |

Free tier is the default when:
- No license key has been entered
- The license has expired
- The license has been revoked

---

## Data Model — `EdgeConfig` (soca-dashboard)

Seven license fields are stored on the `EdgeConfig` singleton (`pk=1`):

| Field | Type | Description |
|---|---|---|
| `license_key` | `CharField(36)` | UUID key entered by the admin |
| `license_status` | `CharField` | `UNLICENSED` / `ACTIVE` / `EXPIRED` / `REVOKED` |
| `license_max_cameras` | `PositiveIntegerField` | Cached `max_cameras` from entitlement |
| `license_max_models` | `PositiveIntegerField` | Cached `max_ai_models` from entitlement |
| `license_expires_at` | `DateField` | Cached expiry date (`null` = never expires) |
| `license_activated_at` | `DateTimeField` | When this key was activated |
| `license_last_checked` | `DateTimeField` | Last time the check endpoint was called |

**Computed properties** (no DB column):

```python
@property
def effective_max_cameras(self):
    return self.license_max_cameras if self.license_status == 'ACTIVE' else 1

@property
def effective_max_models(self):
    return self.license_max_models if self.license_status == 'ACTIVE' else 1

@property
def is_licensed(self):
    return self.license_status == 'ACTIVE'
```

**Migration:** `0026_edgeconfig_license_fields` (`soca-edge`) / `0029_edgeconfig_license_fields` (`soca-edge-nx`)

---

## Activation Flow

```
Admin pastes UUID key into Settings → License → Activate License
        │
        ▼
POST /api/v1/license/activate/
{
  "license_key": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "edge_name":   "edge-jakarta-lobby",          ← EdgeConfig.edge_name
  "mac_address": "aa:bb:cc:dd:ee:ff",           ← server NIC MAC address
  "device_info": {"edge_id": "1"}
}
        │
        ▼  soca-entitlement validates:
        │    ✓ key exists and status == ACTIVE
        │    ✓ not expired
        │    ✓ not already activated by a different MAC
        │
        ▼
200 OK
{
  "status":        "activated",
  "max_cameras":   4,
  "max_ai_models": 2,
  "expires_at":    "2027-05-01"    ← null if perpetual
}
        │
        ▼
soca-dashboard saves to EdgeConfig:
  license_status = ACTIVE
  license_max_cameras = 4
  license_max_models  = 2
  license_expires_at  = 2027-05-01
  max_concurrent_jobs = 2   ← also written, triggers env sync
  max_loaded_models   = 2
        │
        ▼
_sync_license_to_engine() writes to soca-engine .env:
  MAX_CONCURRENT_JOBS=2
  MAX_LOADED_MODELS=2
```

---

## Enforcement Points

### soca-dashboard

**Camera creation** (`app/views.py` → `camera_form`):

```python
if not camera:   # new camera only
    max_cams = cfg.effective_max_cameras   # 1 if unlicensed
    active_count = Camera.objects.filter(is_active=True).count()
    if fields.get('is_active') and active_count >= max_cams:
        messages.error(request, f'License limit reached: maximum {max_cams} active camera(s).')
        return render(request, 'cameras/form.html', {'camera': camera})
```

**Schedule start** (`app/views.py` → `schedule_start`):

```python
max_jobs = cfg.effective_max_models   # 1 if unlicensed
running_count = Schedule.objects.filter(is_active=True).exclude(current_job_id='').count()
if running_count >= max_jobs:
    messages.error(request, f'License limit reached: maximum {max_jobs} concurrent AI model(s).')
    return redirect('schedule_list')
```

### soca-engine

Reads `MAX_CONCURRENT_JOBS` and `MAX_LOADED_MODELS` from its `.env` file at startup. These are written by soca-dashboard after every license activation or refresh.

```
# soca-engine/.env (written by soca-dashboard)
MAX_CONCURRENT_JOBS=2
MAX_LOADED_MODELS=2
```

The engine checks `MAX_CONCURRENT_JOBS` in `api/jobs.py` before starting a new job:

```python
if active_jobs >= config.MAX_CONCURRENT_JOBS:
    raise HTTPException(503, "Max concurrent jobs reached")
```

> **Note:** soca-engine must be restarted after an env change for the new limits to take effect. soca-dashboard's **Restart** button in Settings → Operations handles this.

---

## License Client (`app/license_client.py`)

```
soca-dashboard/app/license_client.py
```

| Function | Description |
|---|---|
| `activate_edge_license(edge, license_key)` | POSTs to `/api/v1/license/activate/`, saves all fields on success |
| `refresh_license_status(edge)` | GETs `/api/v1/license/check/<key>/`, updates status + expiry |
| `_get_mac_address()` | Returns the server's hardware MAC (binding fingerprint) |

Entitlement server URL is hardcoded as the module constant:
```python
ENTITLEMENT_URL = 'http://34.101.213.40:8080'
```

---

## Settings UI

Navigate to **Settings → License** tab in soca-dashboard.

### Unlicensed state
- Yellow "Free Tier" warning banner
- Input field for the license key UUID
- **Activate License** button

### Licensed state
- Green **Active** badge
- Stat cards: Max Cameras · Max AI Models · Expires
- Key + timestamps shown
- **Refresh Status** button — re-calls the check endpoint
- **Unlink License** button — clears license locally (does NOT revoke on entitlement)

### Expired / Revoked state
- Red badge with reason
- **Re-check Status** button
- **Clear License Key** button

### Global warning banner
A yellow banner is shown on every page when the edge is not licensed. It links to the License tab.

### Camera list
The camera list subtitle shows `x / max active limit` with a warning color when at the cap.

---

## soca-entitlement API Reference

Base URL: `http://34.101.213.40:8080`

### POST `/api/v1/license/activate/`

Activates a license key for a specific edge device (one-time).

**Request body:**
```json
{
  "license_key": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "edge_name":   "edge-jakarta-lobby",
  "mac_address": "aa:bb:cc:dd:ee:ff",
  "device_info": {}
}
```

**Responses:**

| Status | Meaning |
|---|---|
| `200` | Activated — body contains `max_cameras`, `max_ai_models`, `expires_at` |
| `404` | License key not found |
| `409` | Already activated by another device |
| `410` | License revoked or expired |
| `422` | Missing required fields |

---

### GET `/api/v1/license/check/<license_key>/`

Returns current license status without modifying it. Used for periodic refresh.

**Response (200):**
```json
{
  "status":        "USED",
  "max_cameras":   4,
  "max_ai_models": 2,
  "duration_months": 12,
  "activated_by":  "edge-jakarta-lobby",
  "activated_at":  "2026-05-01T09:00:00Z",
  "expires_at":    "2027-05-01",
  "is_expired":    false
}
```

Status values: `ACTIVE` · `USED` · `EXPIRED` · `REVOKED`  
(`USED` = successfully activated, treated as `ACTIVE` by soca-dashboard)

---

## Configuration

### soca-dashboard `.env`

```env
APP_VERSION=1.2
```

The entitlement server URL is in `app/license_client.py`:
```python
ENTITLEMENT_URL = 'http://34.101.213.40:8080'
```

### soca-engine `.env` (managed by soca-dashboard)

```env
MAX_CONCURRENT_JOBS=1   # written after license activation
MAX_LOADED_MODELS=1     # default free-tier value
```

---

## Version

Application version is stored in `.env` and displayed at the bottom of the sidebar:

```env
# soca-dashboard/.env
APP_VERSION=1.2
```

Read in `dashboard/settings.py`:
```python
APP_VERSION = os.getenv("APP_VERSION", "1.2")
```

Exposed to all templates via `app/context_processors.py` as `{{ app_version }}`.

---

## Sequence: First Boot

```
1. Edge device boots
2. Admin opens soca-dashboard → Settings → License tab
3. Admin pastes UUID license key received from vendor
4. Dashboard calls POST /api/v1/license/activate/
5. On success: EdgeConfig fields saved, engine .env updated
6. Admin clicks Settings → Operations → Restart Engine
7. soca-engine restarts with new MAX_CONCURRENT_JOBS / MAX_LOADED_MODELS
8. Edge is now fully licensed
```

---

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| "Cannot reach entitlement server" | Network blocked to `34.101.213.40:8080` | Check firewall / internet access from edge |
| "License already activated by another device" | MAC address mismatch | Use the originally provisioned machine, or contact vendor for a new key |
| "License key not found" | Wrong UUID or key deleted from entitlement server | Verify key with vendor |
| Limits not applied in engine after activation | Engine reads `.env` only at startup | Restart soca-engine via Settings → Operations |
| Camera limit not enforced on engine side | Engine limit is applied at job start, not camera registration | Camera count is dashboard-enforced; engine limit applies to concurrent jobs |
