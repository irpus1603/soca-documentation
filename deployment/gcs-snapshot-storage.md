# GCS Snapshot Storage Setup Guide

Complete guide to configure Google Cloud Storage as the snapshot image backend for SOCA.

---

## Architecture

```
soca-engine (edge device)
    │  rule fires → save_snapshot action → JPG written to local snapshots/
    │  snapshot_path stored as: {job_id}/{frame_id}.jpg
    │
    │  core/gcs_sync.py (async background task, runs every 10s)
    │  uploads as: {GCS_PATH_PREFIX}/snapshots/{job_id}/{frame_id}.jpg
    ▼
Google Cloud Storage
    Bucket: {GCS_BUCKET}
    Object key: {GCS_PATH_PREFIX}/snapshots/{job_id}/{frame_id}.jpg
    e.g.: edge-mac-mini-bekasi/snapshots/abc123/6_20260327_063509.jpg
    │
    ▼  (served via soca-control signed URL — bucket can be private)
soca-control
    Alert.snapshot_url → /gcs-snapshot/?b={edge-prefix}/snapshots/{job_id}/{frame}.jpg
    Displayed as thumbnail in Reports → Detail tab
```

---

## Why Per-Edge Path Prefix?

Multiple edge devices share a single GCS bucket without collision by using unique path prefixes:

```
soca-snapshot-bucket/
├── edge-jakarta-01/
│   └── snapshots/{job_id}/{frame}.jpg
├── edge-mac-mini-bekasi/
│   └── snapshots/{job_id}/{frame}.jpg
└── edge-surabaya-02/
    └── snapshots/{job_id}/{frame}.jpg
```

The prefix defaults to `EDGE_NAME` when not explicitly set — so a new edge device works without any extra configuration.

---

## Step 1 — Create GCS Bucket

1. Go to **GCP Console → Cloud Storage → Buckets → Create**
2. Choose a globally unique name (e.g. `soca-snapshot-bucket`)
3. Select region closest to your edge devices
4. Leave other settings as default → **Create**

---

## Step 2 — Create Service Account

1. Go to **GCP Console → IAM & Admin → Service Accounts → + Create**
2. Name: e.g. `soca-snapshot-sa`
3. Grant role: **Storage Object Admin** (allows upload + list)
4. Click **Done**
5. Open the service account → **Keys → Add Key → Create new key → JSON**
6. Save the downloaded JSON file

---

## Step 3 — Configure soca-engine

### Via soca-dashboard UI (recommended)

1. Go to **soca-dashboard → Settings → Edge Config**
2. Under **Cloud Storage (GCS)**:
   - Upload the service account JSON under **GCS Service Account Key**
   - Fill in **GCS Bucket** name
   - Fill in **GCS Path Prefix** (leave blank to use `EDGE_NAME` automatically)
3. Click **Save Edge Config** — this writes `GCS_BUCKET`, `GCS_KEY_PATH`, and `GCS_PATH_PREFIX` to soca-engine's `.env`
4. Click **Push Cloud Credentials to soca-engine** — sends the key file to the engine immediately (no restart needed)

### Manual (`.env` edit)

```env
GCS_BUCKET=your-bucket-name
GCS_KEY_PATH=credentials/gcs.json          # path to your service account JSON
# GCS_PATH_PREFIX defaults to EDGE_NAME — only set if different
```

> Restart soca-engine after editing `.env`. GCS sync starts automatically on boot.

**Verify sync is running** — soca-engine startup log should show:
```
INFO:core.gcs_sync:GCS sync started — bucket=your-bucket-name dir=.../snapshots
```

**Verify uploads** — set `LOG_LEVEL=DEBUG` and watch for:
```
DEBUG:core.gcs_sync:GCS sync: uploaded edge-mac-mini-bekasi/snapshots/job123/frame.jpg
```

---

## Step 4 — Configure soca-control

### 4a — Snapshot Storage

1. Go to **soca-control → Settings → Snapshot Storage**
2. Select **Google Cloud Storage**
3. Enter **Bucket name** — must match `GCS_BUCKET` in soca-engine
4. **Save**

> There is no global Path Prefix setting. Path prefix is configured per-edge (see 4b).

### 4b — Per-edge GCS Path Prefix

1. Go to **soca-control → Settings → Edges → Edit** (for each edge)
2. Fill in **GCS Path Prefix** — must exactly match `GCS_PATH_PREFIX` in that edge's soca-engine `.env`
   - Leave blank if it equals the edge name (default behaviour)
3. **Save**

### 4c — GCS Credentials (for soca-control media uploads)

soca-control uses GCS to store uploaded logos and media files (separate from snapshot storage).

Upload via UI: **Settings → Google Cloud Credentials → Browse → select .json file → Save**

This writes `GOOGLE_APPLICATION_CREDENTIALS` to soca-control's `.env` and persists across restarts.

---

## Step 5 — Verify End-to-End

**Check files exist in GCS:**
```python
from google.cloud import storage
client = storage.Client.from_service_account_json('/path/to/key.json')
blobs = list(client.list_blobs('your-bucket-name', prefix='your-edge-prefix/snapshots/', max_results=5))
for b in blobs:
    print(b.name)
```

**Check URL is correct** — Django shell in soca-control:
```python
from app.models import Alert
a = Alert.objects.filter(snapshot_path__isnull=False).last()
print(a.snapshot_url)
# Should be: /gcs-snapshot/?b=your-edge-prefix/snapshots/...
```

Open the resolved URL in a browser — image should load.

---

## How the Sync Works

`core/gcs_sync.py` runs as an asyncio background task inside the FastAPI lifespan:

- Polls `SNAPSHOTS_DIR` every 10 seconds using `asyncio.to_thread` (non-blocking)
- Tracks already-uploaded paths in an in-memory set (no re-uploads per session)
- On restart, existing files are re-uploaded (idempotent — GCS overwrites with same data)
- GCS object key: `{GCS_PATH_PREFIX}/snapshots/{job_id}/{frame_id}.jpg`
- soca-control's `Alert.snapshot_url` receives `snapshot_path` as `{job_id}/{frame_id}.jpg` and prepends `snapshots/` to reconstruct the full blob path

---

## Adding a New Edge Device

No manual GCS setup is needed. When a new edge device is configured:

1. Set `EDGE_NAME` — this automatically becomes `GCS_PATH_PREFIX` if not overridden
2. On first detection with `save_snapshot`, soca-engine uploads the file and the `edge-name/snapshots/` path is created automatically in GCS (GCS is a flat key store — there are no real folders to pre-create)

---

## Troubleshooting

### Images not showing (thumbnail blank)

1. Check `GCS_BUCKET` is set in soca-engine `.env`
2. Check soca-control Snapshot Storage is set to **Google Cloud Storage** with matching bucket name
3. Check soca-engine has been restarted (or Push Cloud Credentials clicked) after config change
4. Set `LOG_LEVEL=DEBUG` in soca-engine — look for `GCS sync: uploaded ...` lines

### Images show 403 Forbidden

Service account missing `storage.objects.get` permission. Grant **Storage Object Admin** role, or make the bucket public:
```bash
gsutil iam ch allUsers:objectViewer gs://your-bucket-name
```

### Images show 404 / NoSuchKey

The GCS blob path does not match what soca-control is requesting. Check:
1. **GCS Path Prefix** in soca-control Edge settings matches `GCS_PATH_PREFIX` in soca-engine `.env`
2. The snapshot file was actually uploaded — check soca-engine debug logs
3. Enough time has passed (sync runs every 10s)

### `google.cloud.exceptions.Forbidden` on upload

Service account missing `storage.objects.create` permission. Grant **Storage Object Admin** role.

### Sync not starting

`GCS_BUCKET` is empty in soca-engine `.env`. Set it via soca-dashboard or manually, then restart.
