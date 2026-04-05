# GCS Snapshot Storage Setup Guide

Complete guide to configure Google Cloud Storage as the snapshot image backend for SOCA.

---

## Architecture

```
soca-engine (edge device)
    │  rule fires → save_snapshot action → JPG written to local snapshots/
    │
    │  core/gcs_sync.py (async background task, runs every 10s)
    ▼
Google Cloud Storage
    Bucket: {GCS_BUCKET}
    Object key: {GCS_PATH_PREFIX}/snapshots/{job_id}/{frame_id}.jpg
    e.g.: edge-mac-mini-bekasi/snapshots/abc123/6_20260327_063509.jpg
    │
    ▼  (public URL, bucket must be readable by allUsers)
soca-control
    Alert.snapshot_url → https://storage.googleapis.com/{bucket}/{prefix}/snapshots/{job_id}/{frame}.jpg
    Displayed as thumbnail in report Detail tabs
```

---

## Why Per-Edge Path Prefix?

Multiple edge devices can share a single GCS bucket without collision by using unique path prefixes. Each edge's snapshots are stored under its own folder:

```
soca-snapshot-bucket/
├── edge-jakarta-01/
│   └── snapshots/{job_id}/{frame}.jpg
├── edge-mac-mini-bekasi/
│   └── snapshots/{job_id}/{frame}.jpg
└── edge-surabaya-02/
    └── snapshots/{job_id}/{frame}.jpg
```

---

## Step 1 — Create GCS Bucket

1. Go to **GCP Console → Cloud Storage → Buckets → Create**
2. Choose a globally unique name (e.g. `soca-snapshot-bucket`)
3. Select region closest to your edge devices
4. Leave other settings as default → **Create**

---

## Step 2 — Create Service Account

1. Go to **GCP Console → IAM & Admin → Service Accounts → + Create**
2. Name: e.g. `soca-snapshot-bucket`
3. Grant role: **Storage Object Admin** (allows upload + list)
4. Click **Done**
5. Open the service account → **Keys → Add Key → Create new key → JSON**
6. Save the downloaded JSON file

---

## Step 3 — Make Bucket Publicly Readable

Snapshot URLs are served directly to the browser. The bucket must be publicly readable:

**Via GCP Cloud Shell or gcloud CLI:**
```bash
gsutil iam ch allUsers:objectViewer gs://your-bucket-name
```

**Via GCP Console:**
- Go to **Cloud Storage → your-bucket → Permissions → Grant Access**
- New principal: `allUsers`
- Role: `Storage Object Viewer`
- Save

> This allows anyone with the URL to view images. URLs contain UUIDs and are unguessable — acceptable for internal tools. For stricter security, implement signed URLs instead.

---

## Step 4 — Configure soca-engine

Edit `soca-engine/.env`:

```env
GCS_BUCKET=your-bucket-name
GCS_PATH_PREFIX=edge-mac-mini-bekasi    # unique per edge — matches folder in GCS
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account-key.json
```

> Restart soca-engine after editing `.env`. The async GCS sync task starts automatically on boot.

**Verify sync is running** — soca-engine startup log should show:
```
INFO:core.gcs_sync:GCS sync started — bucket=your-bucket-name dir=.../snapshots
```

**Verify uploads** — set `LOG_LEVEL=DEBUG` and watch for:
```
DEBUG:core.gcs_sync:GCS sync: uploaded edge-mac-mini-bekasi/snapshots/job123/frame.jpg
```

---

## Step 5 — Configure soca-control

### 5a — Snapshot Storage setting

1. Go to **soca-control → Settings → Snapshot Storage**
2. Select **Google Cloud Storage**
3. Enter **Bucket name**: same as `GCS_BUCKET` in soca-engine
4. Leave **Path Prefix** blank — prefix is configured per-edge (see 5b)
5. **Save**

### 5b — Per-edge GCS Path Prefix

1. Go to **soca-control → Settings → Edges → Edit** (for each edge)
2. Fill in **GCS Path Prefix**: must exactly match `GCS_PATH_PREFIX` in that edge's soca-engine `.env`
   - e.g. `edge-mac-mini-bekasi`
3. **Save**

### 5c — Google Cloud credentials (for media file uploads)

If `GCS_BUCKET` is also set in soca-control `.env` (for uploaded logos/media):

```env
GCS_BUCKET=your-bucket-name
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account-key.json
```

Or upload via UI: **Settings → Google Cloud Credentials → Browse → select .json file → Upload & Save**

---

## Step 6 — Verify End-to-End

**Check files exist in GCS:**
```python
from google.cloud import storage
import os
os.environ['GOOGLE_APPLICATION_CREDENTIALS'] = '/path/to/key.json'
client = storage.Client()
blobs = list(client.list_blobs('your-bucket-name', prefix='your-edge-prefix/snapshots/', max_results=5))
for b in blobs:
    print(b.name)
```

**Check URL is correct** — Django shell in soca-control:
```python
from app.models import Alert
a = Alert.objects.filter(snapshot_path__isnull=False).last()
print(a.snapshot_url)
# Should be: https://storage.googleapis.com/your-bucket/your-edge-prefix/snapshots/...
```

Open that URL in a browser — image should load.

---

## How the Sync Works

`core/gcs_sync.py` runs as an asyncio background task inside the FastAPI lifespan:

- Polls `SNAPSHOTS_DIR` every 10 seconds using `asyncio.to_thread` (non-blocking)
- Tracks already-uploaded paths in an in-memory set (no re-uploads per session)
- On restart, existing files are re-uploaded (idempotent — GCS overwrites with same data)
- GCS object key: `{GCS_PATH_PREFIX}/snapshots/{job_id}/{frame_id}.jpg`
- No dependencies beyond `google-cloud-storage`

---

## Troubleshooting

### Images show 403 Forbidden

Bucket is not public. Run:
```bash
gsutil iam ch allUsers:objectViewer gs://your-bucket-name
```

### Images show 404 Not Found

File not yet uploaded to GCS. Check:
1. `GCS_BUCKET` set in soca-engine `.env`
2. soca-engine restarted after `.env` change
3. Enough time has passed (sync runs every 10s)
4. `LOG_LEVEL=DEBUG` shows `GCS sync: uploaded ...` messages

### URL has wrong path (prefix missing or wrong)

`GCS_PATH_PREFIX` in soca-engine `.env` does not match **GCS Path Prefix** in soca-control Edge settings. They must be identical.

### `google.cloud.exceptions.Forbidden` on upload

Service account missing `storage.objects.create` permission. Grant **Storage Object Admin** role.

### Sync not starting

`GCS_BUCKET` is empty in soca-engine `.env`. Set it and restart.
