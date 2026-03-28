# SOCA Platform — Complete Setup Guide

This guide covers setting up all four SOCA services from scratch in the correct order.

**Services and their default ports:**

| Service | Port | Host |
|---------|------|------|
| soca-engine | 8001 | Edge device |
| soca-dashboard | 8080 | Edge device |
| soca-service | 8010 | Server / cloud |
| soca-control | 8000 | Server / cloud |

---

## Prerequisites

- Python 3.11+
- Redis 7+ (for Redis stream transport)
- MediaMTX (for RTSP stream relay, optional)
- Google Cloud credentials (for Pub/Sub transport, optional)
- Git

---

## Step 1 — soca-engine (Edge Device)

**Role:** YOLO video processor. Ingests RTSP streams, runs inference, publishes detection alerts.

### 1.1 Clone and install

```bash
git clone <repo> soca-engine
cd soca-engine
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 1.2 Configure environment

Create `.env` in `soca-engine/`:

```env
# Edge identity
EDGE_NAME=edge-jakarta-01

# Alert transport — choose one:
# Option A: Redis Stream (default)
PUBLISHER_TYPE=redis
REDIS_URL=redis://localhost:6379
REDIS_STREAM_NAME=edge-jakarta-01:soca:detections

# Option B: Google Pub/Sub
# PUBLISHER_TYPE=pubsub
# PUBSUB_PROJECT_ID=my-gcp-project
# PUBSUB_TOPIC=soca-detections
# GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json

# Snapshot storage
SNAPSHOT_DIR=snapshots/
```

> The `.env` file is also written automatically by soca-dashboard → Settings → Edge Config when you save transport settings.

### 1.3 Start

```bash
./start.sh
# or manually:
uvicorn main:app --host 0.0.0.0 --port 8001
```

### 1.4 Verify

```bash
curl http://localhost:8001/health
# Expected: {"status": "ok", "redis": "connected", "active_jobs": 0, "uptime_seconds": ...}

curl http://localhost:8001/models
# Expected: {"models": [...]}
```

---

## Step 2 — soca-dashboard (Edge Device)

**Role:** Per-edge web UI. Manages cameras, schedules, rules, and edge configuration. Writes to soca-engine `.env`.

### 2.1 Clone and install

```bash
git clone <repo> soca-dashboard
cd soca-dashboard
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 2.2 Configure environment

Create `.env` in `soca-dashboard/`:

```env
# Django
SECRET_KEY=your-secret-key-here
DEBUG=true

# Engine integration
ENGINE_URL=http://localhost:8001
ENGINE_ENV_PATH=../soca-engine/.env

# MediaMTX (optional, for live preview)
MEDIAMTX_URL=http://localhost:8888
MEDIAMTX_YML_PATH=../soca-engine/MediaMTX/mediamtx.yml

# Port
PORT=8080
```

### 2.3 Initialize database

```bash
python manage.py migrate
python manage.py createsuperuser
```

### 2.4 Start

```bash
./start.sh
# or manually:
python manage.py runserver 0.0.0.0:8080
```

### 2.5 Verify

Open `http://localhost:8080` in a browser. Log in with the superuser credentials.

### 2.6 Configure edge settings

1. Go to **Settings → Edge Config**
2. Set **Engine URL**: `http://localhost:8001`
3. Set **Publisher Transport**: choose Redis or Google Pub/Sub
   - **Redis**: set Redis URL and stream name
   - **Pub/Sub**: set GCP Project ID, topic, and credentials path
4. Set **MediaMTX URL**: `http://localhost:8888` (HLS viewer, used to register cameras into mediamtx.yml)
5. Set **MediaMTX RTSP URL**: `rtsp://localhost:8554` (relay base URL used by soca-engine for detection jobs)
   - When set, `Schedule.to_job_config()` produces `rtsp://localhost:8554/<camera_name>` instead of the raw camera URL
   - Reduces physical camera connection slots and keeps camera credentials off the job config
   - Leave blank to fall back to direct camera RTSP (no relay)
6. Click **Save** — this writes the transport config to `soca-engine/.env`

### 2.7 Add cameras and schedules

1. Go to **Cameras → Add Camera**
   - Name, RTSP URL, credentials (username/password stored separately, embedded in URL at runtime)
2. Go to **Schedules → Add Schedule**
   - Select camera, model, ROI type (Polygon / Rectangle / Crossing Line)
   - Draw ROI on the canvas
3. Go to **Schedules → [schedule] → Rules → Add Rule**
   - Define trigger conditions, actions (snapshot, Redis/queue publish, Telegram)

### 2.8 Start a job

From **Operations** (or via soca-control → edge operations):

```bash
# Via soca-dashboard UI: Operations → Start
# Or via soca-engine API:
curl -X POST http://localhost:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{"camera_id": "1", "rtsp_url": "rtsp://...", "model_path": "yolo/yolo11n.pt", "cls_ids": [0]}'
```

---

## Step 3 — soca-control (Server / Cloud)

**Role:** Fleet dashboard. Receives ingest API calls from soca-service, stores alerts, provides multi-edge reporting.

### 3.1 Clone and install

```bash
git clone <repo> soca-control
cd soca-control
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 3.2 Configure environment

Create `.env` in `soca-control/`:

```env
# Django
SECRET_KEY=your-secret-key-here
DEBUG=true
ALLOWED_HOSTS=localhost,127.0.0.1

# Database (optional — defaults to SQLite)
# DATABASE_URL=postgresql://user:pass@localhost/soca_control

# Ingest key (set via Settings UI → generate, or manually)
# SOCA_CONTROL_INGEST_KEY=<hex-string>

# GCS media storage (optional)
# GCS_BUCKET=soca-control-media-my-project

# Port
PORT=8000
```

### 3.3 Initialize database

```bash
python manage.py migrate
python manage.py createsuperuser
```

### 3.4 Start

```bash
./start.sh
# Linux: starts gunicorn on PORT
# macOS/dev: starts runserver on PORT
```

### 3.5 Verify

Open `http://localhost:8000` in a browser. Log in with the superuser credentials.

### 3.6 Generate ingest key

1. Go to **Settings → Service Ingest Key**
2. Click **Generate Key**
3. Copy the key — you will need it for soca-service

### 3.7 Register edge(s)

1. Go to **Settings → Register New Edge**
2. Fill in:
   - **Edge Name**: must match `EDGE_NAME` in soca-engine `.env`
   - **Dashboard URL**: `http://<edge-ip>:8080`
   - **Engine URL**: `http://<edge-ip>:8001`
   - **Subscriber Transport**: Redis or Google Pub/Sub
   - For Redis: Redis URL + stream name
   - For Pub/Sub: GCP Project ID + subscription name
3. After registering, go to **Settings → Edit Edge** and optionally set:
   - **MediaMTX URL**: `http://<edge-ip>:8888` — enables the live stream viewer button in Assets; must be reachable from the operator's browser
   - Leave blank to hide stream viewer buttons for this edge
4. Click **Register Edge** / **Save**

---

## Step 4 — soca-service (Server / Cloud)

**Role:** Alert consumer microservice. Bridges Redis/Pub/Sub transport from edge to soca-control ingest API.

### 4.1 Clone / copy

```bash
# soca-service is in the same repo
cd soca-service
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 4.2 Configure environment

Copy `.env.example` to `.env`:

```bash
cp .env.example .env
```

Edit `.env`:

```env
# Required
SOCA_CONTROL_URL=http://localhost:8000
SOCA_CONTROL_INGEST_KEY=<key from soca-control Settings → Service Ingest Key>

# Optional
PORT=8010

# Required if any edge uses Pub/Sub transport
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json
```

> The `SOCA_CONTROL_INGEST_KEY` must exactly match the key shown in soca-control Settings → Service Ingest Key.

### 4.3 Start

```bash
./start.sh
# or manually:
uvicorn main:app --host 0.0.0.0 --port 8010
```

### 4.4 Verify health

```bash
curl http://localhost:8010/health
```

Expected response:
```json
{
  "status": "ok",
  "consumer_name": "hostname",
  "uptime_seconds": 5,
  "pubsub": {
    "status": "running",
    "active_edges": 0,
    "messages_processed": 0,
    "last_message_at": null,
    "last_error": null
  },
  "streams": {
    "status": "running",
    "active_edges": 0,
    "messages_processed": 0,
    "last_message_at": null,
    "last_error": null
  }
}
```

### 4.5 Monitor from soca-control

1. Go to **soca-control → Settings → soca-service**
2. Enter the soca-service URL: `http://localhost:8010`
3. Click **Save**
4. The health panel loads and auto-refreshes every 15 seconds

---

## Stopping Services

Each service has a `stop.sh` script:

```bash
# Stop each service
cd soca-engine    && ./stop.sh
cd soca-dashboard && ./stop.sh
cd soca-control   && ./stop.sh
cd soca-service   && ./stop.sh
```

---

## Verification — End-to-End Test

After all four services are running:

### 1. Test the ingest API directly

```bash
curl -X POST http://localhost:8000/api/v1/ingest/ \
  -H "Authorization: Bearer <SOCA_CONTROL_INGEST_KEY>" \
  -H "Content-Type: application/json" \
  -d '{
    "edge_name": "edge-jakarta-01",
    "camera_id": "1",
    "timestamp": "2026-03-26T10:00:00Z",
    "alert_category": "Intrusion",
    "message": "1 object(s) detected"
  }'
# Expected: 201 {"id": N}
```

### 2. Test the edges API

```bash
curl http://localhost:8000/api/v1/edges/ \
  -H "Authorization: Bearer <SOCA_CONTROL_INGEST_KEY>"
# Expected: 200 {"edges": [...]}
```

### 3. Trigger a real alert from soca-engine

Start a job with a rule that has `publish_queue` action, ensure Redis or Pub/Sub is running, then check soca-control → Alerts for a new record.

---

## Google Pub/Sub Setup (Optional)

If using Pub/Sub transport, see [deployment/pubsub-setup-guide.md](deployment/pubsub-setup-guide.md) for:

- Creating a GCP service account
- Granting Publisher and Subscriber IAM roles
- Creating topic and subscription
- Configuring soca-engine and soca-control

---

## GCP Cloud Run Deployment

For production deployment on Google Cloud Run, see [deployment/gcp-cloud-run-guide.md](deployment/gcp-cloud-run-guide.md) for:

- VPC setup and Cloud Memorystore (Redis)
- Artifact Registry and Docker image build
- Cloud SQL (PostgreSQL)
- Cloud Run services for web + workers
- CI/CD with Cloud Build

---

## Common Issues

### soca-service cannot reach soca-control

- Check `SOCA_CONTROL_URL` in `soca-service/.env`
- Ensure soca-control is running: `curl http://localhost:8000/health`
- Check firewall rules if on different hosts

### Ingest returns 401 Unauthorized

- Verify `SOCA_CONTROL_INGEST_KEY` matches in both `soca-control` (Settings) and `soca-service/.env`
- Regenerate the key in soca-control Settings and update `soca-service/.env`

### No alerts appearing in soca-control

1. Check soca-service health: `curl http://localhost:8010/health` — both consumers should show `"status": "running"`
2. Check `active_edges` — should be > 0 after edge is registered in soca-control
3. Check `last_error` — will show the most recent error if POSTs are failing
4. Check soca-engine is publishing: look for messages in the Redis stream
   ```bash
   redis-cli XLEN edge-jakarta-01:soca:detections
   ```

### soca-engine RTSP connection fails

- Verify the camera RTSP URL: `ffprobe rtsp://user:pass@camera-ip/stream`
- Check MediaMTX is running for RTSP relay: `curl http://localhost:8888/v3/config/global/get`

### Redis Consumer Group already exists (BUSYGROUP)

This is normal — soca-service handles `BUSYGROUP` errors gracefully. The consumer group already exists from a previous run, which is correct behavior.
