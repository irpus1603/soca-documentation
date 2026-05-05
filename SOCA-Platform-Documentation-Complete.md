# SOCA Platform Documentation

_Consolidated from `../soca-documentation` on 2026-05-05._

This document combines the Markdown documentation for the SOCA Platform into one shareable file, following the published MkDocs navigation order and then appending working documents that are present in the repository but not exposed in the main site navigation.

## Included Sections

- **Home**
  - Home (`index.md`)
- **Getting Started**
  - Repository Overview (`repository-overview.md`)
  - Full Setup Guide (`SETUP.md`)
  - Deployment Paths (`guides/deployment-paths.md`)
- **Product**
  - Use Cases (`overview/product-offering-usecases.md`)
  - Presentation Script (`overview/presentation-script.md`)
- **Architecture**
  - System Overview (`architecture/system-overview.md`)
  - Licensing Model (`architecture/licensing-model.md`)
  - NXVMS Integration (`architecture/2026-04-16-nxvms-integration.md`)
  - SOCA Gap Analysis (`architecture/sow-vs-soca-gap-analysis.md`)
- **Deployment**
  - Docker Deployment (`deployment/docker-deployment.md`)
  - Bare Metal RHEL (`deployment/bare-metal-rhel-guide.md`)
  - GCP Cloud Run (`deployment/gcp-cloud-run-guide.md`)
  - Pub/Sub Setup (`deployment/pubsub-setup-guide.md`)
  - GCS Snapshot Storage (`deployment/gcs-snapshot-storage.md`)
  - Metabase Reporting (`deployment/metabase-reporting-guide.md`)
  - Grafana Reporting (`deployment/grafana-reporting-guide.md`)
  - Cython Protection (`deployment/cython-protection-guide.md`)
- **User Guides**
  - Guide Overview (`guides/user-guides.md`)
  - Dashboard Design Spec (`soca-dashboard/design-spec.md`)
  - Dashboard Edge Operations Plan (`soca-dashboard/edge-operations-plan.md`)
  - Dashboard Edge Operations Design (`soca-dashboard/edge-operations-design.md`)
  - Dashboard Rule Management Plan (`soca-dashboard/rule-management-plan.md`)
  - Dashboard Advanced Rule Management (`soca-dashboard/advanced-rule-management-design.md`)
  - Control Design Spec (`soca-control/design-spec.md`)
  - Control Edge Operations Plan (`soca-control/edge-operations-plan.md`)
  - Control Implementation Plan (`soca-control/implementation-plan.md`)
  - Control Pub/Sub Transport (`soca-control/pubsub-transport-plan.md`)
- **Engine And Services**
  - Engine API Testing (`soca-engine/api-testing.md`)
  - Engine Object Detection Processor (`soca-engine/object-detection-processor.md`)
  - Engine Rule Management (`soca-engine/rule-management-plan.md`)
  - Service Overview (`soca-service/overview.md`)
- **Testing**
  - Testing Overview (`guides/testing.md`)
  - Dashboard Test Results (`testing/soca-dashboard-test-results.md`)
  - Dashboard Test Scenarios (`testing/soca-dashboard-test-scenarios.md`)
- **Additional Working Documents**
  - Crossing Line Fix + Violation CLS Summary Design (`soca-dashboard/2026-03-27-crossing-line-violations-design.md`)
  - Crossing Line Fix + Violation CLS Summary Plan (`soca-dashboard/2026-03-27-crossing-line-violations-plan.md`)

## Main Documentation

## 1. Home

### Home

_Source: `index.md`_

## SOCA Documentation

This site turns the repository into an online documentation portal for the **SOCA Platform**: a video analytics system spanning edge inference, fleet management, reporting, and cloud integrations.

### Start Here

- New to the project: begin with [Repository Overview](repository-overview.md)
- Need a full installation flow: use [Full Setup Guide](SETUP.md)
- Need to choose an infrastructure model: see [Deployment Paths](guides/deployment-paths.md)

### Recommended Journeys

#### For technical buyers or internal stakeholders

1. Read [Use Cases](overview/product-offering-usecases.md)
2. Review [System Overview](architecture/system-overview.md)
3. Check [Licensing Model](architecture/licensing-model.md)

#### For deployment engineers

1. Read [Full Setup Guide](SETUP.md)
2. Choose a path from [Deployment Paths](guides/deployment-paths.md)
3. Configure cloud integrations with [Pub/Sub Setup](deployment/pubsub-setup-guide.md) and [GCS Snapshot Storage](deployment/gcs-snapshot-storage.md) when needed

#### For operators and product teams

1. Start from [Guide Overview](guides/user-guides.md)
2. Review `soca-dashboard` and `soca-control` guides
3. Use the testing section to validate expected behavior

### Documentation Areas

- `Getting Started`: onboarding and environment setup
- `Architecture`: system model, licensing, and integration references
- `Deployment`: infrastructure-specific installation and configuration guides
- `User Guides`: dashboard and control-plane workflows
- `Engine And Services`: lower-level technical references
- `Testing`: validation artifacts and test scenarios

## 2. Getting Started

### Repository Overview

_Source: `repository-overview.md`_

## SOCA Platform Documentation

**SOCA** (Security Operations & Camera Analytics) — enterprise video analytics platform with YOLO-based edge processing and centralized fleet management.

---

### Quick Start

→ **[SETUP.md](SETUP.md)** — Complete step-by-step setup for all four services

---

### Documentation Structure

```
soca-documentation/
├── repository-overview.md             ← You are here
├── SETUP.md                           ← Complete setup guide (start here)
│
├── architecture/
│   ├── system-overview.md             ← System architecture, data flow, scaling
│   └── licensing-model.md             ← License system: soca-entitlement, enforcement, UI
│
├── overview/
│   ├── product-offering-usecases.md   ← Use cases by industry, competitive analysis
│   └── presentation-script.md         ← Full PPT script (27 slides) for GenAI presentation generation
│
├── soca-engine/
│   ├── api-testing.md                 ← API reference with curl examples
│   ├── object-detection-processor.md  ← Core detection engine design
│   └── rule-management-plan.md        ← Advanced rule evaluation implementation plan
│
├── soca-dashboard/
│   ├── design-spec.md                 ← Dashboard design specification
│   ├── edge-operations-plan.md        ← Edge operations feature plan
│   ├── edge-operations-design.md      ← Edge operations design spec
│   ├── rule-management-plan.md        ← Rule management feature plan
│   └── advanced-rule-management-design.md ← Advanced rule management design spec
│
├── soca-control/
│   ├── design-spec.md                 ← soca-control design specification
│   ├── implementation-plan.md         ← Initial implementation plan
│   ├── edge-operations-plan.md        ← Edge operations plan
│   └── pubsub-transport-plan.md       ← Pub/Sub transport implementation plan
│
├── soca-service/
│   └── overview.md                    ← Consumer microservice overview
│
└── deployment/
    ├── docker-deployment.md           ← Docker multi-repo deployment (recommended)
    ├── bare-metal-rhel-guide.md       ← Bare-metal deployment on RHEL/Rocky Linux
    ├── pubsub-setup-guide.md          ← Google Pub/Sub setup (step-by-step)
    ├── gcs-snapshot-storage.md        ← GCS snapshot storage setup guide
    ├── metabase-reporting-guide.md    ← Metabase DB schema + SQL queries for all report types
    ├── grafana-reporting-guide.md     ← Grafana DB schema + SQL queries for all report types
    └── gcp-cloud-run-guide.md         ← GCP Cloud Run production deployment
```

---

### Service Summary

| Service | Port | Location | Purpose |
|---------|------|----------|---------|
| **soca-engine** | 8001 | Edge device | YOLO inference, job management, alert publishing, GCS snapshot sync |
| **soca-dashboard** | 8080 | Edge device | Camera/schedule/rule management UI, edge config, license activation |
| **soca-service** | 8080 | Server | Alert consumer microservice (Redis + Pub/Sub) |
| **soca-control** | 8000 | Server | Fleet dashboard, reporting, alert storage |
| **soca-entitlement** | 8080 | Cloud (`34.101.213.40`) | License key management, activation API |

---

### Transport Options

| Transport | Best for | Key config |
|-----------|---------|------------|
| **Redis Stream** | On-premises, LAN | `PUBLISHER_TYPE=redis`, `REDIS_URL`, `REDIS_STREAM_NAME` |
| **Google Pub/Sub** | Cloud, multi-region | `PUBLISHER_TYPE=pubsub`, `PUBSUB_PROJECT_ID`, `PUBSUB_TOPIC` |

---

### Snapshot Storage Options

| Storage | Best for | Key config |
|---------|---------|------------|
| **Edge Local** | LAN/on-premises | `SiteConfig.snapshot_storage=local` in soca-control |
| **Google Cloud Storage** | Cloud, multi-edge | `GCS_BUCKET`, `GCS_PATH_PREFIX` in soca-engine; `SiteConfig.snapshot_storage=gcs` in soca-control |

---

### Key Features (as of 2026-05-01)

- **Multi-model support** — PyTorch `.pt` (with tracking) and ONNX `.onnx` (predict-only)
- **Inference performance** — configurable device (CPU/CUDA), resolution (`INFER_IMGSZ`), FP16 (`INFER_HALF`)
- **RTSP threading** — dedicated reader thread decouples network I/O from inference
- **Conditional LPR** — EasyOCR skipped when no vehicles (COCO cls 2/5/7) in frame
- **GCS snapshot sync** — async background task uploads snapshots per-edge with path prefix isolation
- **Pub/Sub direct subscribe** — soca-control subscribes directly to Pub/Sub (no soca-service needed)
- **Per-edge GCS prefix** — each edge uploads to its own folder, multiple edges share one bucket
- **Report categories** — Intrusion, PPE, Object Detection, People Counting, Crowd, LPR
- **PPE violations breakdown** — per-class detection summary in PPE report
- **GCS credentials upload** — file picker UI in soca-dashboard and soca-control Settings
- **MediaMTX relay for detection jobs** — soca-engine connects via `rtsp://localhost:8554/<camera>` instead of direct camera RTSP; reduces physical camera connection slots and keeps credentials off the wire
- **Stream mode badge** — Assets page shows `Relay` (green) or `Direct` (yellow) badge per camera reflecting the active connection mode
- **Live stream viewer** — Assets page play button opens MediaMTX's built-in HLS web player in a resizable popup window per camera (requires `mediamtx_url` set on the edge)
- **Popup windows for live monitor** — monitor stream thumbnails and maximize button open in a controlled 854×480 resizable popup instead of a new browser tab
- **Popup windows for snapshots** — all report pages open snapshot images in a shared 960×720 resizable popup window
- **License enforcement** — UUID keys issued by soca-entitlement; free tier = 1 camera + 1 model; activation unlocks `max_cameras` / `max_ai_models` and syncs limits to soca-engine; see [architecture/licensing-model.md](architecture/licensing-model.md)
- **Application versioning** — `APP_VERSION` in `.env` displayed in the sidebar footer; current version: **1.2**

### Full Setup Guide

_Source: `SETUP.md`_

## SOCA Platform — Complete Setup Guide

This guide covers setting up all four SOCA services from scratch in the correct order.

**Services and their default ports:**

| Service | Port | Host |
|---------|------|------|
| soca-engine | 8001 | Edge device |
| soca-dashboard | 8080 | Edge device |
| soca-service | 8010 | Server / cloud |
| soca-control | 8000 | Server / cloud |

---

### Prerequisites

- Python 3.11+
- Redis 7+ (for Redis stream transport)
- MediaMTX (for RTSP stream relay, optional)
- Google Cloud credentials (for Pub/Sub transport, optional)
- Git

---

### Step 1 — soca-engine (Edge Device)

**Role:** YOLO video processor. Ingests RTSP streams, runs inference, publishes detection alerts.

#### 1.1 Clone and install

```bash
git clone <repo> soca-engine
cd soca-engine
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

#### 1.2 Configure environment

Create `.env` in `soca-engine/` (copy from `.env-example`):

```env
# Edge identity
EDGE_NAME=edge-jakarta-01

# Alert transport — choose one:
# Option A: Redis Stream (default)
PUBLISHER_TYPE=redis
REDIS_URL=redis://localhost:6379
REDIS_STREAM_NAME=soca:detections

# Option B: Google Pub/Sub
# PUBLISHER_TYPE=pubsub
# PUBSUB_PROJECT_ID=my-gcp-project
# PUBSUB_TOPIC=soca-detections
# PUBSUB_KEY_PATH=credentials/pubsub.json

# Snapshot storage (GCS — optional)
# GCS_BUCKET=your-bucket-name
# GCS_KEY_PATH=credentials/gcs.json
# GCS_PATH_PREFIX defaults to EDGE_NAME when not set

# Inference tuning (optional — defaults shown)
# LOG_LEVEL=INFO
# INFER_DEVICE=auto
# INFER_IMGSZ=640
# MAX_CONCURRENT_JOBS=4
```

> Most of these settings can also be managed via **soca-dashboard → Settings → Edge Config** and are written to this file automatically on save. Manual edits require a restart to take effect.

#### 1.3 Start

```bash
./start.sh
# or manually:
uvicorn main:app --host 0.0.0.0 --port 8001
```

#### 1.4 Verify

```bash
curl http://localhost:8001/health
# Expected: {"status": "ok", "redis": "connected", "active_jobs": 0, "uptime_seconds": ...}

curl http://localhost:8001/models
# Expected: {"models": [...]}
```

---

### Step 2 — soca-dashboard (Edge Device)

**Role:** Per-edge web UI. Manages cameras, schedules, rules, and edge configuration. Writes to soca-engine `.env`.

#### 2.1 Clone and install

```bash
git clone <repo> soca-dashboard
cd soca-dashboard
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

#### 2.2 Configure environment

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

#### 2.3 Initialize database

```bash
python manage.py migrate
python manage.py createsuperuser
```

#### 2.4 Start

```bash
./start.sh
# or manually:
python manage.py runserver 0.0.0.0:8080
```

#### 2.5 Verify

Open `http://localhost:8080` in a browser. Log in with the superuser credentials.

#### 2.6 Configure edge settings

1. Go to **Settings → Edge Config**
2. **Connection** section:
   - Set **Edge Name** — used as the GCS path prefix by default
   - Set **Redis Stream Name** if different from the default `soca:detections`
   - Set **Publisher Transport**: Redis Stream or Google Pub/Sub
     - Pub/Sub: fill in GCP Project ID, Topic, and upload the service account key
3. **Cloud Storage (GCS)** section:
   - Upload GCS service account key and fill in bucket name
   - Leave **GCS Path Prefix** blank to default to Edge Name
4. **soca-engine Settings** section — controls runtime behaviour, synced to soca-engine `.env` on save:
   - Redis URL, Log Level, Snapshot quality/size, Inference device/size/precision, Job limits
5. Click **Save Edge Config** — writes all non-credential settings to soca-engine `.env` immediately
6. Click **Push Cloud Credentials to soca-engine** — sends GCS and Pub/Sub key files to the engine (applies live, no restart needed)

MediaMTX URLs and file paths are auto-configured from the shared `soca-edge/` folder — no manual entry needed.

#### 2.7 Add cameras and schedules

1. Go to **Cameras → Add Camera**
   - Name, RTSP URL, credentials (username/password stored separately, embedded in URL at runtime)
2. Go to **Schedules → Add Schedule**
   - Select camera, model, ROI type (Polygon / Rectangle / Crossing Line)
   - Draw ROI on the canvas
3. Go to **Schedules → [schedule] → Rules → Add Rule**
   - Define trigger conditions, actions (snapshot, Redis/queue publish, Telegram)

#### 2.8 Start a job

From **Operations** (or via soca-control → edge operations):

```bash
# Via soca-dashboard UI: Operations → Start
# Or via soca-engine API:
curl -X POST http://localhost:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{"camera_id": "1", "rtsp_url": "rtsp://...", "model_path": "yolo/yolo11n.pt", "cls_ids": [0]}'
```

---

### Step 3 — soca-control (Server / Cloud)

**Role:** Fleet dashboard. Receives ingest API calls from soca-service, stores alerts, provides multi-edge reporting.

#### 3.1 Clone and install

```bash
git clone <repo> soca-control
cd soca-control
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

#### 3.2 Configure environment

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

#### 3.3 Initialize database

```bash
python manage.py migrate
python manage.py createsuperuser
```

#### 3.4 Start

```bash
./start.sh
# Linux: starts gunicorn on PORT
# macOS/dev: starts runserver on PORT
```

#### 3.5 Verify

Open `http://localhost:8000` in a browser. Log in with the superuser credentials.

#### 3.6 Generate ingest key

1. Go to **Settings → Service Ingest Key**
2. Click **Generate Key**
3. Copy the key — you will need it for soca-service

#### 3.7 Register edge(s)

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

### Step 4 — soca-service (Server / Cloud)

**Role:** Alert consumer microservice. Bridges Redis/Pub/Sub transport from edge to soca-control ingest API.

#### 4.1 Clone / copy

```bash
# soca-service is in the same repo
cd soca-service
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

#### 4.2 Configure environment

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
```

> `SOCA_CONTROL_INGEST_KEY` can also be pushed automatically from soca-control Settings → Ingest Key → Push to soca-service.

> Pub/Sub credentials are **not** set here. They are pushed from soca-control via **Settings → Push Cloud Credentials** and stored in soca-service's `config.json` as `pubsub_key_path`. soca-service does not use `GOOGLE_APPLICATION_CREDENTIALS`.

#### 4.3 Start

```bash
./start.sh
# or manually:
uvicorn main:app --host 0.0.0.0 --port 8010
```

#### 4.4 Verify health

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

#### 4.5 Monitor from soca-control

1. Go to **soca-control → Settings → soca-service**
2. Enter the soca-service URL: `http://localhost:8010`
3. Click **Save**
4. The health panel loads and auto-refreshes every 15 seconds

---

### Stopping Services

Each service has a `stop.sh` script:

```bash
# Stop each service
cd soca-engine    && ./stop.sh
cd soca-dashboard && ./stop.sh
cd soca-control   && ./stop.sh
cd soca-service   && ./stop.sh
```

---

### Verification — End-to-End Test

After all four services are running:

#### 1. Test the ingest API directly

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

#### 2. Test the edges API

```bash
curl http://localhost:8000/api/v1/edges/ \
  -H "Authorization: Bearer <SOCA_CONTROL_INGEST_KEY>"
# Expected: 200 {"edges": [...]}
```

#### 3. Trigger a real alert from soca-engine

Start a job with a rule that has `publish_queue` action, ensure Redis or Pub/Sub is running, then check soca-control → Alerts for a new record.

---

### Google Pub/Sub Setup (Optional)

If using Pub/Sub transport, see [deployment/pubsub-setup-guide.md](deployment/pubsub-setup-guide.md) for:

- Creating a GCP service account
- Granting Publisher and Subscriber IAM roles
- Creating topic and subscription
- Configuring soca-engine and soca-control

---

### GCP Cloud Run Deployment

For production deployment on Google Cloud Run, see [deployment/gcp-cloud-run-guide.md](deployment/gcp-cloud-run-guide.md) for:

- VPC setup and Cloud Memorystore (Redis)
- Artifact Registry and Docker image build
- Cloud SQL (PostgreSQL)
- Cloud Run services for web + workers
- CI/CD with Cloud Build

---

### Common Issues

#### soca-service cannot reach soca-control

- Check `SOCA_CONTROL_URL` in `soca-service/.env`
- Ensure soca-control is running: `curl http://localhost:8000/health`
- Check firewall rules if on different hosts

#### Ingest returns 401 Unauthorized

- Verify `SOCA_CONTROL_INGEST_KEY` matches in both `soca-control` (Settings) and `soca-service/.env`
- Regenerate the key in soca-control Settings and update `soca-service/.env`

#### No alerts appearing in soca-control

1. Check soca-service health: `curl http://localhost:8010/health` — both consumers should show `"status": "running"`
2. Check `active_edges` — should be > 0 after edge is registered in soca-control
3. Check `last_error` — will show the most recent error if POSTs are failing
4. Check soca-engine is publishing: look for messages in the Redis stream
   ```bash
   redis-cli XLEN edge-jakarta-01:soca:detections
   ```

#### soca-engine RTSP connection fails

- Verify the camera RTSP URL: `ffprobe rtsp://user:pass@camera-ip/stream`
- Check MediaMTX is running for RTSP relay: `curl http://localhost:8888/v3/config/global/get`

#### Redis Consumer Group already exists (BUSYGROUP)

This is normal — soca-service handles `BUSYGROUP` errors gracefully. The consumer group already exists from a previous run, which is correct behavior.

### Deployment Paths

_Source: `guides/deployment-paths.md`_

## Deployment Paths

Use this page to choose the shortest path for your environment before diving into the lower-level setup guides.

### Recommended Options

#### 1. Docker deployment

Use [Docker Deployment](../deployment/docker-deployment.md) when you want the fastest repeatable setup across development, demo, or controlled production environments.

Best for:

- Internal demos
- Pilot deployments
- Small environments with predictable infrastructure

#### 2. Bare metal on RHEL or Rocky Linux

Use [Bare Metal RHEL](../deployment/bare-metal-rhel-guide.md) when the target environment is on-premises and managed like traditional enterprise infrastructure.

Best for:

- Factory or facility deployments
- Locked-down enterprise hosts
- Environments with OS-level operational controls

#### 3. GCP and Cloud Run

Use [GCP Cloud Run](../deployment/gcp-cloud-run-guide.md) when the control-plane services need managed cloud hosting and easier scaling.

Best for:

- Cloud-first deployments
- Distributed multi-edge installations
- Teams already using Google Cloud

### Integration Decisions

#### Alert transport

- Use [Pub/Sub Setup](../deployment/pubsub-setup-guide.md) for cloud or multi-region distribution
- Use Redis Stream when the deployment is local or on-premises and cloud messaging is unnecessary

#### Snapshot storage

- Use [GCS Snapshot Storage](../deployment/gcs-snapshot-storage.md) when multiple edges need centralized media access
- Keep snapshots local on the edge when the environment is isolated and cloud sync is not required

#### Reporting

- Use [Metabase Reporting](../deployment/metabase-reporting-guide.md) when you want SQL-driven reporting and dashboard creation
- Use [Grafana Reporting](../deployment/grafana-reporting-guide.md) when the team already standardizes on Grafana for operational dashboards

## 3. Product

### Use Cases

_Source: `overview/product-offering-usecases.md`_

## SOCA Video Analytics Platform
### Product Offering & Use Cases

**Product Name:** SOCA (Security Operations & Camera Analytics)  
**Technology:** YOLO-based Real-time Video Analytics  
**Deployment:** Edge Computing with Centralized Management  

---

### Executive Summary

SOCA is an enterprise-grade video analytics platform that transforms existing CCTV infrastructure into intelligent, AI-powered monitoring systems. Leveraging state-of-the-art YOLO (You Only Look Once) deep learning models, SOCA delivers real-time detection, alerting, and comprehensive reporting across multiple security and operational use cases.

#### Key Capabilities
- **Real-time Detection:** Sub-second analysis of video streams using YOLO models
- **Edge Computing:** Distributed processing at the edge for low latency and bandwidth efficiency
- **Multi-Edge Management:** Centralized dashboard for managing multiple deployment sites
- **Configurable Rules:** Customizable detection zones, thresholds, and alert conditions
- **Comprehensive Reporting:** Statistical analysis, trend tracking, and export capabilities (CSV/PDF)
- **Live Monitoring:** Real-time video stream monitoring with overlay annotations
- **Snapshot Storage:** Cloud (GCS) or local storage for incident evidence

---

### Core Product Modules

#### 1. Intrusion Detection
#### 2. PPE Compliance Monitoring
#### 3. Object/Person Detection
#### 4. People Counting & Flow Analysis
#### 5. Crowd Detection
#### 6. License Plate Recognition (LPR)

---

### Use Cases by Industry

### 1. SECURITY & ACCESS CONTROL

#### Use Case 1.1: Perimeter Intrusion Detection
**Industry:** Corporate Campuses, Warehouses, Residential Complexes, Critical Infrastructure

**Problem:**
- Unauthorized personnel entering restricted areas
- Manual monitoring is labor-intensive and prone to human error
- Delayed response to security breaches
- No automated evidence collection

**How SOCA Solves It:**
- **Zone-Based Detection:** Define virtual boundaries (ROI - Region of Interest) around perimeters, fences, and restricted zones
- **Instant Alerts:** Real-time notifications when persons are detected in unauthorized areas
- **Severity Classification:** Automatic severity rating based on number of intruders (single, medium group, high-risk crowd)
- **Snapshot Evidence:** Automatic capture and storage of intrusion events with timestamps
- **Heatmap Analytics:** Identify high-risk time periods (day/hour patterns) for proactive security deployment
- **Multi-Camera Coverage:** Correlate intrusions across multiple cameras for tracking movement patterns

**Business Value:**
- 24/7 automated surveillance without fatigue
- Faster incident response times
- Reduced security personnel costs
- Auditable incident records for investigations

---

#### Use Case 1.2: After-Hours Facility Security
**Industry:** Offices, Retail Stores, Banks, Museums

**Problem:**
- Break-ins and theft during non-business hours
- False alarms from traditional motion sensors
- Limited visibility into actual security events

**How SOCA Solves It:**
- **Time-Based Rules:** Activate intrusion detection only during specified hours
- **Person Detection:** Distinguish actual human intruders from animals, shadows, or environmental changes
- **Multi-Person Alerts:** Escalate alerts when multiple intruders detected (potential organized crime)
- **Integration Ready:** Redis stream output for integration with alarm systems and access control

---

### 2. WORKPLACE SAFETY & COMPLIANCE

#### Use Case 2.1: PPE (Personal Protective Equipment) Compliance
**Industry:** Construction Sites, Manufacturing Plants, Oil & Gas, Mining, Laboratories

**Problem:**
- Workers not wearing required safety equipment (helmets, vests, masks, goggles)
- Regulatory compliance violations and fines
- Increased risk of workplace injuries
- Manual compliance checks are inconsistent

**How SOCA Solves It:**
- **Automated PPE Detection:** YOLO models trained to detect missing or improper PPE usage
- **Shift-Based Analytics:** Track compliance across morning, afternoon, and night shifts
- **Repeat Offender Identification:** Cameras/locations with chronic non-compliance
- **Trend Reporting:** 30-day compliance trends to measure safety program effectiveness
- **Peak Hour Analysis:** Identify times of day when violations are most common

**Business Value:**
- Reduce workplace accidents and injuries
- Maintain regulatory compliance (OSHA, local safety regulations)
- Data-driven safety training programs
- Lower insurance premiums through improved safety records

---

#### Use Case 2.2: Restricted Area Access Monitoring
**Industry:** Data Centers, Pharmaceutical Labs, Government Facilities, R&D Centers

**Problem:**
- Unauthorized access to sensitive areas
- Tailgating (unauthorized person following authorized entrant)
- Compliance requirements for access logging

**How SOCA Solves It:**
- **ROI-Based Monitoring:** Define exact entry points and restricted zones
- **Crossing Detection:** Track IN/OUT movements through doorways and gates
- **People Counting:** Verify occupancy limits in sensitive areas
- **Audit Trail:** Complete timestamped log of all entries/exits with visual evidence

---

### 3. RETAIL & CUSTOMER ANALYTICS

#### Use Case 3.1: Store Traffic Analysis
**Industry:** Retail Stores, Shopping Malls, Supermarkets, Banks

**Problem:**
- No visibility into customer foot traffic patterns
- Inefficient staff scheduling
- Unable to measure marketing campaign effectiveness
- Queue management challenges

**How SOCA Solves It:**
- **Entrance/Exit Counting:** Accurate people counting at store entrances
- **Hourly/Daily Trends:** Identify peak shopping hours and slow periods
- **Staff Optimization:** Schedule staff based on actual traffic patterns
- **Campaign Measurement:** Correlate promotional events with traffic increases
- **Dwell Time Analysis:** Understand how long customers spend in store

**Business Value:**
- Optimized labor costs through data-driven scheduling
- Improved customer experience during peak hours
- Measurable ROI on marketing investments
- Better store layout decisions based on traffic flow

---

#### Use Case 3.2: Queue Management
**Industry:** Banks, Hospitals, Government Offices, Theme Parks

**Problem:**
- Long wait times causing customer dissatisfaction
- No real-time visibility into queue lengths
- Inefficient resource allocation

**How SOCA Solves It:**
- **Crowd Detection:** Monitor queue areas and detect excessive crowding
- **Threshold Alerts:** Notify managers when queue exceeds acceptable length
- **Historical Analysis:** Identify patterns to improve staffing and service windows

---

### 4. SMART CITY & PUBLIC SAFETY

#### Use Case 4.1: Public Space Crowd Management
**Industry:** City Centers, Parks, Event Venues, Transportation Hubs

**Problem:**
- Overcrowding leading to safety hazards
- Inability to respond proactively to crowd buildup
- Event capacity compliance

**How SOCA Solves It:**
- **Crowd Density Detection:** Monitor public spaces for dangerous crowd levels
- **Real-Time Alerts:** Notify authorities when crowd thresholds exceeded
- **Heatmap Visualization:** Understand which areas attract most foot traffic
- **Day/Week Patterns:** Plan resource allocation based on historical patterns

**Business Value:**
- Prevent stampedes and crowd-related incidents
- Optimize security and cleaning staff deployment
- Data-driven urban planning decisions

---

#### Use Case 4.2: Vehicle Access & Parking Management
**Industry:** Municipal Parking, Gated Communities, Corporate Campuses

**Problem:**
- Unauthorized vehicle access
- Parking violation enforcement
- No automated vehicle logging

**How SOCA Solves It:**
- **License Plate Recognition (LPR):** Automatic plate number capture and logging
- **Plate Expiry Tracking:** Monitor vehicle registration validity (where data available)
- **Confidence Scoring:** Quality metrics on plate reads for enforcement decisions
- **Searchable Database:** Query historical plate data for investigations

---

### 5. INDUSTRIAL & MANUFACTURING

#### Use Case 5.1: Production Line Monitoring
**Industry:** Manufacturing, Assembly Plants, Packaging Facilities

**Problem:**
- Unplanned downtime due to operator absence
- Quality control gaps
- No visibility into production floor activity

**How SOCA Solves It:**
- **Object Detection:** Monitor presence of products, materials, or tools at workstations
- **Absent/Present Tracking:** Detect when expected objects are missing from designated areas
- **Rule-Based Alerts:** Configure alerts for specific detection conditions
- **Location Matrix:** Track which stations have most frequent anomalies

**Business Value:**
- Reduced production downtime
- Improved quality control
- Data-driven process optimization

---

#### Use Case 5.2: Hazardous Area Monitoring
**Industry:** Chemical Plants, Refineries, Power Plants

**Problem:**
- Workers entering dangerous zones without authorization
- Emergency response delays
- Compliance with safety regulations

**How SOCA Solves It:**
- **Intrusion Detection:** Immediate alerts when personnel enter hazardous zones
- **Severity Classification:** Higher alerts for multiple persons in danger zones
- **Snapshot Evidence:** Visual documentation for incident investigation
- **24/7 Monitoring:** Continuous surveillance without operator fatigue

---

### 6. TRANSPORTATION & LOGISTICS

#### Use Case 6.1: Loading Dock Security
**Industry:** Warehouses, Distribution Centers, Manufacturing

**Problem:**
- Unauthorized access to loading areas
- Theft during loading/unloading operations
- No visibility into dock activity

**How SOCA Solves It:**
- **Intrusion Detection:** Alert on unauthorized personnel during non-operational hours
- **Object Detection:** Monitor presence of cargo, pallets, or equipment
- **Time-Based Rules:** Different rules for operational vs. non-operational periods
- **Multi-Camera Coverage:** Coordinate across multiple dock doors

---

#### Use Case 6.2: Fleet Vehicle Monitoring
**Industry:** Logistics Companies, Rental Car Facilities, Dealerships

**Problem:**
- Vehicle theft and unauthorized use
- Damage documentation
- Compliance with parking assignments

**How SOCA Solves It:**
- **LPR Integration:** Log all vehicle movements with plate recognition
- **Object Detection:** Detect vehicles in restricted areas
- **Audit Trail:** Complete vehicle movement history with timestamps and images

---

### 7. HEALTHCARE & ELDERLY CARE

#### Use Case 7.1: Patient Safety Monitoring
**Industry:** Hospitals, Nursing Homes, Mental Health Facilities

**Problem:**
- Patient wandering/elopement risks
- Fall detection in common areas
- Staff response times

**How SOCA Solves It:**
- **Intrusion Detection:** Alert when patients enter restricted or dangerous areas
- **Crowd Detection:** Monitor common areas for unusual gatherings
- **Time-Based Patterns:** Identify when incidents most commonly occur

---

### 8. EDUCATION

#### Use Case 8.1: Campus Security
**Industry:** Schools, Universities, Daycare Centers

**Problem:**
- Unauthorized access to school grounds
- Student safety in restricted areas (rooftops, mechanical rooms)
- After-hours security

**How SOCA Solves It:**
- **Perimeter Intrusion:** Detect unauthorized entry to school grounds
- **Restricted Area Monitoring:** Alert when students enter dangerous zones
- **After-Hours Protection:** Automated surveillance during nights and weekends
- **Multi-Site Management:** Centralized monitoring across multiple campuses

---

### Technical Differentiators

#### Edge Computing Architecture
- **Low Latency:** Processing happens at the edge, not in the cloud
- **Bandwidth Efficient:** Only alerts and snapshots transmitted, not full video streams
- **Offline Resilience:** Continues operating even if central server connectivity lost
- **Scalable:** Add edges without central bottleneck

#### Centralized Management (SOCA Control)
- **Fleet Dashboard:** Single pane of glass for all deployment sites
- **Health Monitoring:** CPU, memory, disk, and job status across all edges
- **Unified Reporting:** Aggregate analytics across all locations
- **Role-Based Access:** Admin, Operator, and Viewer roles

#### Flexible Deployment
- **Cloud Storage:** Google Cloud Storage integration for snapshots
- **Local Storage:** On-premises storage option for air-gapped environments
- **API Integration:** Redis streams for real-time integration with third-party systems
- **Export Options:** CSV and PDF reports for compliance and analysis

#### Advanced Analytics
- **Heatmap Visualization:** Day-of-week × hour-of-day patterns
- **Trend Analysis:** 7-day and 30-day rolling analytics
- **Repeat Offender Tracking:** Cameras and locations with chronic issues
- **Severity Classification:** Configurable thresholds for alert prioritization

---

### Target Customer Profiles

#### Ideal Customer Characteristics
- **Existing CCTV Investment:** 10+ cameras already deployed
- **Multiple Locations:** 2+ sites requiring centralized monitoring
- **Compliance Requirements:** Subject to safety, security, or privacy regulations
- **Labor Cost Pressure:** Seeking to optimize security/safety staffing costs
- **Technology Forward:** Comfortable with AI/ML-based solutions

#### Decision Maker Personas
- **Security Director:** Focused on intrusion detection and incident response
- **Safety Manager:** Focused on PPE compliance and workplace safety
- **Operations Manager:** Focused on efficiency and process optimization
- **Facility Manager:** Focused on multi-site oversight and cost control
- **IT Director:** Focused on integration, scalability, and data security

---

### Competitive Advantages

| Feature | SOCA | Traditional VMS | Cloud-Only AI |
|---------|------|-----------------|---------------|
| Edge Processing | ✓ | ✗ | ✗ |
| Real-Time Alerts | ✓ | Limited | Latency |
| Bandwidth Efficient | ✓ | ✗ (full streams) | ✗ (full streams) |
| Offline Operation | ✓ | ✓ | ✗ |
| Centralized Multi-Site | ✓ | Expensive | ✓ |
| Custom ROI Zones | ✓ | Limited | ✓ |
| PPE Detection | ✓ | ✗ | ✓ |
| LPR Integration | ✓ | Add-on | Add-on |
| Open API (Redis) | ✓ | Proprietary | ✓ |
| Self-Hosted Option | ✓ | ✓ | ✗ |

---

### Pricing Strategy Recommendations

#### Tier 1: Starter (1-5 Edges)
- Core intrusion detection
- Basic reporting
- Local snapshot storage
- Email support

#### Tier 2: Professional (6-20 Edges)
- All detection modules
- Advanced analytics & heatmaps
- GCS cloud storage
- CSV/PDF exports
- Priority support

#### Tier 3: Enterprise (21+ Edges)
- Unlimited edges
- Custom integrations (Redis streams)
- Dedicated support
- SLA guarantees
- Custom model training (industry-specific)

---

### Go-to-Market Recommendations

#### Channel Strategy
1. **Security System Integrators:** Partner with existing CCTV installers
2. **Safety Equipment Distributors:** Bundle with PPE suppliers
3. **Cloud Providers:** GCP Marketplace listing for cloud storage integration
4. **Direct Sales:** Target enterprise customers with 10+ locations

#### Marketing Messages
- **Security:** "Turn Your CCTV Into 24/7 Intelligent Guards"
- **Safety:** "Automate PPE Compliance. Reduce Workplace Injuries."
- **Retail:** "Know Your Customers. Optimize Your Operations."
- **Industrial:** "Zero Downtime Through AI-Powered Monitoring"

#### Proof of Value
- 30-day pilot program at single site
- Baseline measurement (current incident rates, compliance levels)
- Quantified improvement report at pilot conclusion
- ROI calculator showing payback period

---

### Product Roadmap Suggestions

#### Near-Term Enhancements
1. **Mobile App:** Push notifications and live view on iOS/Android
2. **Email/SMS Alerts:** Direct alerting without third-party integration
3. **Pre-Built Models:** Industry-specific YOLO models (retail, construction, etc.)
4. **Dashboard Widgets:** Customizable dashboard for different user roles

#### Medium-Term Enhancements
1. **Behavioral Analytics:** Loitering detection, wrong-way detection, fall detection
2. **Two-Way Audio Integration:** Trigger announcements on intrusion
3. **Access Control Integration:** Lock/unlock doors based on detection events
4. **Predictive Analytics:** ML-based forecasting of incident patterns

#### Long-Term Vision
1. **Multi-Object Tracking:** Follow individuals across camera views
2. **Anomaly Detection:** Unsupervised learning for unknown threat patterns
3. **Video Search:** Natural language search ("show me all red shirts yesterday")
4. **Edge-to-Edge Coordination:** Automated handoff of tracking between edges

---

### Success Metrics

#### Customer KPIs to Track
- **Security:** Reduction in unauthorized access incidents
- **Safety:** PPE compliance rate improvement
- **Operations:** Staff optimization (labor cost reduction)
- **Retail:** Customer traffic growth, conversion rate improvement
- **Compliance:** Audit preparation time reduction

#### SOCA Business Metrics
- Edges deployed per customer
- Detection modules activated per edge
- Alert volume trends (indicates engagement)
- Export activity (indicates compliance use)
- Customer expansion rate (additional edges/sites)

---

**Document Version:** 1.0  
**Prepared By:** Product Marketing  
**Date:** March 2026  
**Confidentiality:** Internal Use Only

### Presentation Script

_Source: `overview/presentation-script.md`_

## SOCA Platform — Presentation Script
**For AI-Generated Presentation (Gamma / Similar Tool)**

> **Instructions for GenAI:** Use this script as the exact content baseline.  
> Tone: professional, confident, enterprise-grade. Not casual.  
> Color palette suggestion: deep navy (`#0F172A`), electric blue (`#3B82F6`), white, subtle green accent (`#22C55E`).  
> Include diagrams, icons, and data visualizations where noted. Avoid stock photography of people.  
> Total target slides: ~22–26 slides.

---

### SECTION 1 — INTRODUCTION & ECOSYSTEM

---

#### SLIDE 1 — Cover Slide

**Title:** SOCA
**Subtitle:** Security Operations & Camera Analytics
**Tagline:** *Turn Your Existing CCTV Into an Intelligent AI Guard*

**Visual:** Full-bleed dark background. CCTV camera silhouette with AI detection bounding boxes overlaid in blue. SOCA logo centered.

**Speaker note:**
Welcome. Today we are presenting SOCA — Security Operations and Camera Analytics. SOCA is an AI-powered video analytics platform that transforms the CCTV infrastructure you already own into an intelligent, real-time monitoring system. We will walk you through the ecosystem, the architecture, the capabilities, and where this technology is going.

---

#### SLIDE 2 — The Problem

**Title:** The Gap in Traditional CCTV

**Three columns layout:**

| Column 1 | Column 2 | Column 3 |
|---|---|---|
| 👁 **Human Fatigue** | ⏱ **Reactive, Not Proactive** | 📂 **No Actionable Data** |
| Security operators cannot watch 20+ camera feeds continuously for 12-hour shifts without missing events | Incidents are discovered after they happen — no real-time alerting based on what is actually happening in the frame | Hours of footage exist but produce no statistical insights on patterns, compliance, or risk |

**Speaker note:**
The challenge with traditional CCTV is simple: cameras see everything, but humans cannot process it all. Security teams are overloaded, events get missed, and terabytes of footage go unanalyzed. The camera is there — the intelligence is not. SOCA closes that gap.

---

#### SLIDE 3 — What is SOCA?

**Title:** SOCA — The AI Layer on Top of Your Cameras

**Visual:** Split layout — left: traditional CCTV camera → right: camera + SOCA logo + detection overlays + alert notification on phone.

**Key points (3 bullets):**
- **Real-time AI detection** — YOLO deep learning models analyze live video streams at the edge, frame by frame
- **Configurable intelligence** — define zones, rules, and thresholds for your specific environment — no coding required
- **Centralized visibility** — manage every camera across every site from a single dashboard, with unified reporting and alerting

**Speaker note:**
SOCA sits as a software layer between your existing CCTV cameras and your security team. It continuously watches the video feed, applies AI detection, and only surfaces events that matter — based on rules you define. The result: your cameras become proactive, always-on intelligent sensors.

---

#### SLIDE 4 — The SOCA Ecosystem

**Title:** Five Services, One Intelligent Platform

**Visual:** Ecosystem diagram — two zones:

```
┌──────────────────────────────────┐    ┌──────────────────────────────────┐
│          EDGE DEVICE             │    │         CLOUD / SERVER           │
│                                  │    │                                  │
│  ┌─────────────┐  ┌───────────┐  │    │  ┌────────────┐  ┌───────────┐  │
│  │  soca-      │  │  soca-    │  │───►│  │  soca-     │  │  soca-    │  │
│  │  dashboard  │  │  engine   │  │    │  │  service   │  │  control  │  │
│  │  (Config UI)│  │  (AI Core)│  │    │  │  (Ingest)  │  │  (Fleet)  │  │
│  └─────────────┘  └───────────┘  │    │  └────────────┘  └───────────┘  │
└──────────────────────────────────┘    └──────────────────────────────────┘
                                                              │
                                              ┌───────────────┴──────────┐
                                              │     soca-entitlement     │
                                              │     (License Server)     │
                                              └──────────────────────────┘
```

**Five service cards (icon + name + one-line description):**
1. 🧠 **soca-engine** — YOLO AI inference engine; processes RTSP video streams in real-time
2. ⚙️ **soca-dashboard** — Per-site web UI; manage cameras, schedules, detection rules, and settings
3. 📡 **soca-service** — Alert consumer microservice; bridges edge data to the central server
4. 🖥 **soca-control** — Fleet management dashboard; unified view of all sites, reports, and analytics
5. 🔑 **soca-entitlement** — License server; controls access tier and feature limits per deployment

**Speaker note:**
SOCA is a microservices ecosystem. On each edge device, two services run: soca-dashboard handles configuration and the soca-engine runs the actual AI inference. On the server side, soca-control is your command center — a single dashboard for your entire camera fleet. soca-service is the data pipeline that bridges the two. And soca-entitlement manages licensing, ensuring deployments operate within their authorized capacity.

---

#### SLIDE 5 — Version & Licensing Model

**Title:** Built for Enterprise Scale — Flexible Licensing

**Two columns:**

**Left — Version:**
- Current platform version: **v1.2**
- Production-ready, deployed on edge hardware
- Django + FastAPI microservices stack
- YOLO11 / YOLOv8 AI models

**Right — License Tiers:**

| Tier | Cameras | AI Models | Support |
|---|---|---|---|
| **Free** | 1 | 1 | Community |
| **Starter** | Up to 5 | Up to 2 | Email |
| **Professional** | Up to 20 | Up to 8 | Priority |
| **Enterprise** | Unlimited | Unlimited | Dedicated SLA |

**Speaker note:**
SOCA version 1.2 is in active production. Licensing is handled through the soca-entitlement service — a UUID-based key is issued to each edge device and activates its camera and AI model limits. Free tier runs with one camera and one model, allowing customers to evaluate the platform before committing.

---

### SECTION 2 — ARCHITECTURE

---

#### SLIDE 6 — Architecture Philosophy

**Title:** Edge-First. Cloud-Optional.

**Three principles (large icon + heading + 2-line description):**

🏗 **Edge Computing**
AI inference happens on the device where the cameras are. No video stream leaves the premises. Only alerts and snapshots are transmitted.

🌐 **Centralized Management**
All edges — regardless of location — report to a single soca-control dashboard. One login. Complete visibility.

🔄 **Transport Flexibility**
Choose Redis Stream for on-premises LAN deployments, or Google Pub/Sub for cloud and multi-region setups. Both work seamlessly.

**Speaker note:**
SOCA is designed edge-first by philosophy. Processing happens where the cameras are, which means low latency, high privacy, and resilience — the system keeps working even if the internet goes down. Cloud connectivity is optional, not mandatory.

---

#### SLIDE 7 — The Data Flow

**Title:** From Camera Frame to Actionable Alert

**Visual:** Horizontal flow diagram with 6 numbered steps:

```
[Camera] ──1──► [MediaMTX Relay] ──2──► [soca-engine YOLO]
                                               │
                                        3. Rule fires
                                               │
                              ┌────────────────┴────────────────┐
                              ▼                                  ▼
                     4. Snapshot saved                  5. Alert published
                     (local / GCS)                (Redis Stream / Pub/Sub)
                                                         │
                                                  6. soca-control
                                                  stores + displays
```

**Steps list:**
1. **RTSP Capture** — Camera streams to MediaMTX relay; soca-engine reads at `rtsp://localhost:8554/<camera>`
2. **YOLO Inference** — Frame analyzed: object detection + ROI filtering + tracking
3. **Rule Evaluation** — Detection matched against configured rules (zone, threshold, schedule)
4. **Snapshot** — Incident frame saved locally and synced to Google Cloud Storage (10-second async)
5. **Alert Published** — Event pushed to Redis stream or Google Pub/Sub
6. **soca-control** — Alert stored in database; appears in dashboard, reports, and live feed

**Speaker note:**
This is the heartbeat of SOCA. A camera frame enters, YOLO processes it in milliseconds, the rule engine decides if it's noteworthy, and an alert — with a snapshot — is published. From event to visible alert in soca-control takes under 2 seconds on standard hardware.

---

#### SLIDE 8 — Edge Device Deep Dive

**Title:** What Lives on the Edge Device

**Visual:** Edge device illustration (small PC / industrial computer) with service labels.

**Two-column layout:**

**soca-dashboard (Config Layer)**
- Web UI accessible from local network
- Manage cameras (RTSP URLs, credentials)
- Configure detection schedules and zones
- Sync settings to soca-engine via `.env`
- License activation and management
- Role-based access: Admin / Operator / Viewer

**soca-engine (AI Layer)**
- YOLO11 / YOLOv8 inference (`.pt` or `.onnx`)
- MediaMTX RTSP relay integration
- ROI: Polygon, Rectangle, Line Crossing
- Multi-rule evaluation per schedule
- GCS snapshot sync (background async)
- Job management API (start / stop / monitor)

**Speaker note:**
Each edge device is self-contained. soca-dashboard handles all configuration — you add cameras, draw detection zones, set rules, and it writes the configuration to soca-engine. The engine then runs continuously, processing video streams, making decisions, and publishing events — independently of the central server.

---

#### SLIDE 9 — Transport & Storage Options

**Title:** Flexible by Design — Fits Your Infrastructure

**Two rows of option cards:**

**Alert Transport:**

| Option | Best For | How It Works |
|---|---|---|
| 🔴 **Redis Stream** | On-premises, LAN | Engine pushes to Redis → soca-service consumes → forwards to soca-control |
| ☁️ **Google Pub/Sub** | Cloud, multi-region | Engine publishes to GCP topic → soca-control subscribes directly |

**Snapshot Storage:**

| Option | Best For | How It Works |
|---|---|---|
| 💾 **Edge Local** | Air-gapped, LAN | Snapshots served via soca-dashboard proxy URL |
| ☁️ **Google Cloud Storage** | Cloud, multi-edge | Async background sync to `gs://bucket/edge-prefix/snapshots/` |

**Speaker note:**
We did not lock customers into a single infrastructure choice. If you are running fully on-premises, Redis is simple and fast. If you are cloud-native or geographically distributed, Pub/Sub scales without any infrastructure to manage. The same is true for snapshots — local or GCS, your choice.

---

#### SLIDE 10 — Security Architecture

**Title:** Enterprise-Grade Security Model

**Visual:** Lock icon centered, four cards around it.

**Four security cards:**

🔑 **API Key Authentication**
Inter-service communication protected by bearer tokens. Ingest keys generated per deployment and stored securely.

🛡 **Role-Based Access Control**
Three roles — Admin, Operator, Viewer — with granular permissions across soca-dashboard and soca-control.

🔐 **License Binding**
Each license key is bound to an edge device by MAC address. Keys cannot be reused across machines.

📁 **Credential Isolation**
Camera RTSP credentials never leave the edge. Detection jobs use MediaMTX relay URLs, not raw camera addresses.

**Speaker note:**
Security is not an afterthought. Credentials are isolated at the edge, inter-service APIs are authenticated, and the licensing system adds a hardware binding layer so licenses cannot be shared across deployments.

---

### SECTION 3 — FEATURES & CAPABILITIES

---

#### SLIDE 11 — AI Detection Modules

**Title:** Six Detection Capabilities, Out of the Box

**Visual:** 2×3 grid of feature cards with icon + title + one-line description.

| | | |
|---|---|---|
| 🚧 **Intrusion Detection** | 🦺 **PPE Compliance** | 🔍 **Object Detection** |
| Virtual zone-based alerts when unauthorized persons enter restricted areas | Automated detection of missing safety equipment: helmets, vests, goggles, masks | Detect, classify, and track any COCO object class in real time |
| 👥 **People Counting** | 🔴 **Crowd Detection** | 🚗 **License Plate Recognition** |
| Count IN/OUT crossings across a virtual line with directional filtering | Alert when crowd density in a zone exceeds a configurable threshold | OCR-based plate number capture with confidence scoring and expiry tracking |

**Speaker note:**
Six detection modules, all configurable without writing code. Each module maps to a distinct report category in soca-control, making it easy to segment analytics by use case. Customers typically start with one or two modules and expand as they see value.

---

#### SLIDE 12 — Detection Intelligence

**Title:** Configurable Intelligence — Not Just Motion Detection

**Three capability sections:**

**Region of Interest (ROI)**
Draw exactly where detection applies:
- Polygon zones — complex irregular shapes
- Rectangle zones — simple boundary areas
- Crossing lines — directional people/vehicle counting

**Rule Engine**
- Multiple rules per detection schedule
- Each rule: define object classes, trigger condition, cooldown, cron schedule
- Actions per rule: save snapshot, publish alert, send Telegram notification, webhook

**Alert Categories & Routing**
- Every alert tagged with a category: `Intrusion`, `PPE`, `Detection`, `Counting`, `Crowd`, `LPR`
- Automatically routed to corresponding report pages in soca-control
- Filter, search, export — CSV and PDF

**Speaker note:**
What separates SOCA from basic motion detection is the rule engine. You define what matters in your environment — a person entering a polygon zone after hours, more than 3 people in a waiting area, a car without a valid plate approaching a gate — and SOCA monitors for exactly that, continuously, with no operator fatigue.

---

#### SLIDE 13 — Analytics & Reporting

**Title:** From Raw Alerts to Business Intelligence

**Visual:** Dashboard screenshot mockup showing report cards and heatmap.

**Six report types (two rows of three):**

📌 **Intrusion Report** — Timeline, camera distribution, severity breakdown, peak hours

🦺 **PPE Violations** — Compliance rate by shift, class-level breakdown (helmet vs vest vs mask), trend over 30 days

🔍 **Object Detection** — Alert volume by class, camera hotspot matrix, hourly patterns

👥 **People Counting** — IN/OUT flow by time period, camera-level comparisons

🔴 **Crowd Detection** — Density events by location and time, peak crowd periods

🚗 **LPR Report** — Plate log with confidence scores, searchable by plate number

**All reports support:**
- Date range filtering
- Per-edge and per-camera filtering
- Export to CSV and PDF

**Speaker note:**
Every alert SOCA generates feeds into a structured reporting layer. Security teams get incident timelines. Safety managers get compliance trends. Operations leaders get traffic flow data. All from the same platform — no third-party BI tool required.

---

#### SLIDE 14 — Live Monitoring & Real-Time View

**Title:** Operational Awareness, Right Now

**Visual:** Monitor page mockup with multiple MJPEG streams and bounding boxes.

**Three live capabilities:**

📹 **Live Stream Monitor**
Watch active inference jobs in real time — MJPEG stream with AI bounding boxes overlaid. Open individual streams in resizable popup windows.

📸 **Instant Snapshots**
Click any camera in the Assets page to grab a current frame. Confirm camera connectivity and framing before starting a detection job.

🌐 **HLS Stream Viewer**
MediaMTX provides a browser-accessible HLS video player for each camera at `http://edge-host:8888/<camera>` — no VPN or special client required.

**Speaker note:**
SOCA is not just a reporting tool — operators can see what is happening right now. The live monitor page shows active detection jobs with real-time bounding boxes. The HLS player makes it accessible from any browser on the local network without specialized software.

---

#### SLIDE 15 — Fleet Management (soca-control)

**Title:** One Dashboard. Every Site. Complete Visibility.

**Visual:** soca-control dashboard mockup — sidebar with edges listed, main panel showing fleet health grid.

**Four fleet management pillars:**

🗺 **Multi-Edge Overview**
All registered edge devices shown on one screen. Online/offline status, last-seen timestamp, active job count.

💓 **Health Monitoring**
Per-edge system metrics: CPU, RAM, disk usage, active detection jobs, network latency. Identify overloaded or failing edges instantly.

🎯 **Unified Alert Inbox**
All detection events from all edges, searchable and filterable by date, camera, category, and edge. Complete incident history.

👤 **User Management**
Role-based access control — Admin, Operator, Viewer — configurable per user across the entire fleet.

**Speaker note:**
soca-control is the command center for your entire deployment. Whether you have 2 edge devices or 200, it is all in one place. Security teams see the incident feed. IT sees system health. Management sees reports. Each role gets exactly what it needs.

---

### SECTION 4 — REAL-WORLD USE CASES

---

#### SLIDE 16 — Use Case Overview Map

**Title:** SOCA Across Industries

**Visual:** Industry hexagon grid or world map with industry icons placed on sectors.

**Eight industries:**

| Icon | Industry | Primary Module |
|---|---|---|
| 🏭 | **Manufacturing & Industrial** | PPE Compliance + Intrusion Detection |
| 🏗 | **Construction** | PPE Compliance + Zone Safety |
| 🏪 | **Retail & Hospitality** | People Counting + Crowd Detection |
| 🏢 | **Corporate & Office** | Intrusion Detection + Access Control |
| 🏙 | **Smart City & Public Safety** | Crowd Detection + LPR |
| 🚛 | **Logistics & Warehousing** | Loading Dock Security + LPR |
| 🏥 | **Healthcare** | Restricted Area Monitoring |
| 🎓 | **Education** | Campus Security + After-Hours |

**Speaker note:**
SOCA is purpose-built for any environment where cameras already exist but intelligence does not. The following slides show four specific real-world applications in depth.

---

#### SLIDE 17 — Use Case 1: Industrial PPE Compliance

**Title:** Manufacturing — Zero Tolerance for PPE Violations

**Visual:** Construction/factory scene with bounding boxes on workers — red box on worker without helmet, green on compliant worker.

**Problem → Solution → Result layout:**

❌ **The Problem**
Workers on production floors and construction sites routinely remove helmets, vests, or goggles. Manual spot-checks are inconsistent and supervisors cannot be everywhere. A single violation can result in serious injury and regulatory fines.

✅ **How SOCA Solves It**
- PPE detection model runs on every camera in the work zone
- Alerts fire the moment a non-compliant worker is detected — with a snapshot
- 30-day compliance trend report shows which shifts, cameras, and time periods have the most violations
- Supervisors receive Telegram notifications in real time

📊 **Business Value**
- Consistent 24/7 compliance monitoring — not periodic spot checks
- Data-driven safety training: target the specific shift, camera, or time with highest violation rates
- Auditable compliance records for OSHA and local regulator inspections

**Speaker note:**
PPE compliance is one of SOCA's highest-impact use cases. The cost of a single lost-time injury far exceeds the cost of the entire platform deployment. SOCA makes compliance monitoring continuous, objective, and evidenced.

---

#### SLIDE 18 — Use Case 2: Retail Traffic & Queue Intelligence

**Title:** Retail — From Guesswork to Data-Driven Operations

**Visual:** Overhead view of retail entrance with crossing line drawn across the doorway and counter visible.

**Problem → Solution → Result layout:**

❌ **The Problem**
Store managers schedule staff based on experience and intuition — not actual foot traffic data. Peak hours are understaffed; quiet periods are overstaffed. Queue buildup causes customer walkouts.

✅ **How SOCA Solves It**
- Crossing-line counting at all entrances measures exact IN/OUT counts by hour and day
- Crowd detection in checkout areas triggers alerts when queue length exceeds threshold
- Daily and weekly traffic reports enable precise staff scheduling decisions

📊 **Business Value**
- Labor cost optimization — schedule the right number of staff at the right times
- Measurable ROI on promotions — correlate traffic spikes with marketing events
- Customer experience improvement — proactive queue management before it becomes a problem

**Speaker note:**
Retail customers often discover that their highest-traffic days are not the days they assumed. SOCA gives you the actual number — not a guess — and the trend over time to staff accordingly.

---

#### SLIDE 19 — Use Case 3: Smart City & Public Safety

**Title:** Smart City — Proactive Crowd & Vehicle Intelligence

**Visual:** Bird's-eye view of a public square or transportation hub with zone overlays and vehicle bounding boxes.

**Problem → Solution → Result layout:**

❌ **The Problem**
City operators react to crowding incidents after they escalate — no early warning. Vehicle access to restricted zones relies entirely on human guards. Parking enforcement is manual and inconsistent.

✅ **How SOCA Solves It**
- Crowd density detection in public squares and transit hubs triggers operator alerts before dangerous conditions develop
- LPR captures and logs every vehicle at entry points — unauthorized plates flagged immediately
- Heatmap analytics show which areas attract crowds at which times, informing urban planning decisions

📊 **Business Value**
- Prevention over reaction — intervene before a crowd becomes a safety hazard
- Automated vehicle access enforcement — scalable without increasing staffing
- Evidence-based infrastructure planning decisions

**Speaker note:**
Smart city applications require scale. SOCA's multi-edge architecture means you can deploy dozens of nodes across a city and manage them all from one soca-control instance — whether they connect via the city's LAN or Google Pub/Sub over the internet.

---

#### SLIDE 20 — Use Case 4: Corporate Campus Security

**Title:** Corporate — After-Hours Security Without Overtime Costs

**Visual:** Corporate building exterior at night with intrusion detection zone highlighted in red.

**Problem → Solution → Result layout:**

❌ **The Problem**
After-hours security relies on on-site guards — expensive, prone to inattention, and unable to monitor all areas simultaneously. Incidents are discovered the next morning, not in real time.

✅ **How SOCA Solves It**
- Intrusion detection activates automatically after business hours via cron-based rule scheduling
- Any person detected in restricted zones triggers an instant alert with snapshot evidence
- Crossing-line rules monitor entry points — detect tailgating that badge systems miss
- Multi-camera correlation tracks movement through the facility

📊 **Business Value**
- Significant reduction in on-site guard hours required
- Response time measured in seconds, not minutes
- Complete photographic incident record for insurance and investigations

**Speaker note:**
The cron-based rule scheduling is important here — you configure SOCA once, and it automatically activates the after-hours detection rules at the right time. No human needs to toggle the system every day.

---

### SECTION 5 — FUTURE ROADMAP

---

#### SLIDE 21 — Roadmap Overview

**Title:** What's Coming — The SOCA Roadmap

**Visual:** Three-column timeline: Near-Term | Mid-Term | Long-Term

**Near-Term (v1.x)**
- 📱 Mobile push notifications — iOS and Android alert delivery
- 📧 Email / SMS alerting — direct notification without third-party integration
- 🗺 GIS map view in soca-control — see all edges plotted on a live map
- 🤖 Pre-built industry models — construction, retail, healthcare-specific YOLO weights

**Mid-Term**
- 🚶 Behavioral analytics — loitering detection, fall detection, wrong-direction alerts
- 🔗 Access control integration — trigger door locks/unlocks on detection events
- 📊 Custom dashboard widgets — role-specific KPI widgets per user
- 🔊 Two-way audio — trigger PA announcements from detection events

**Long-Term Vision**
- 🧭 Multi-camera object tracking — follow an individual across camera views
- 🔍 Video search — natural language queries ("show all red shirts yesterday between 2–4pm")
- 🌐 Edge-to-edge coordination — automated tracking handoff between adjacent edges
- 🔮 Predictive analytics — ML forecasting of incident probability by location and time

**Speaker note:**
The roadmap is customer-driven. The near-term items address the most common feedback we receive: notifications without complex integrations, and industry-tuned models that work out of the box. The long-term vision moves SOCA from reactive analytics to predictive, coordinated intelligence.

---

#### SLIDE 22 — Technology Trajectory

**Title:** AI at the Edge — A Platform That Evolves

**Visual:** Ascending capability curve with milestone markers.

**Three trajectory stages:**

**Today — v1.2: Detect & Alert**
- Real-time YOLO detection across 6 use cases
- Rule-based alerting with snapshot evidence
- Fleet management with unified reporting
- Edge-first, cloud-optional architecture

**Near Future — v2.x: Understand & Predict**
- Behavioral AI: intent recognition, anomaly detection
- Predictive risk scoring by location and time pattern
- Automated model retraining from flagged incidents
- Mobile-first operator experience

**Far Future — v3.x: Coordinate & Act**
- Multi-camera object identity tracking across entire sites
- Natural language video search and querying
- Autonomous response triggers (access control, audio, lighting)
- Edge-to-cloud federated learning — models improve from all deployments

**Speaker note:**
We are at version 1.2. The platform is production-ready and deployed. The roadmap takes SOCA from detection and alerting today, to understanding context and predicting risk in the near term, to fully coordinated autonomous response in the longer horizon. Each stage builds on real customer deployments and feedback.

---

#### SLIDE 23 — Competitive Positioning

**Title:** Why SOCA — The Differentiators

**Visual:** Comparison table with SOCA column highlighted.

| Feature | SOCA | Traditional VMS | Cloud-Only AI |
|---|:---:|:---:|:---:|
| Edge AI Processing | ✅ | ❌ | ❌ |
| Works Offline | ✅ | ✅ | ❌ |
| Bandwidth Efficient | ✅ | ❌ | ❌ |
| Real-Time Alerts | ✅ | Limited | Latency |
| Custom ROI Zones | ✅ | Limited | ✅ |
| Multi-Site Fleet Management | ✅ | Expensive | ✅ |
| PPE + LPR + Crowd (same platform) | ✅ | ❌ | Separate add-ons |
| Open API (Redis / Pub/Sub) | ✅ | Proprietary | ✅ |
| Self-Hosted Option | ✅ | ✅ | ❌ |
| License Flexibility | ✅ | Per-seat, expensive | Subscription only |

**Speaker note:**
The key differentiators are edge processing and the breadth of detection in one platform. Cloud-only AI solutions require your video to leave the premises and are vulnerable to internet outages. Traditional VMS systems do not include AI detection — it is always an expensive add-on. SOCA is purpose-built for AI at the edge, with the fleet management layer built in from day one.

---

#### SLIDE 24 — Deployment Model

**Title:** Up and Running in One Day

**Visual:** Four-step timeline with icons.

**Step 1 — Install**
Deploy soca-engine + soca-dashboard on an edge device (Linux bare-metal, Docker, or cloud VM). Start services with a single shell script.

**Step 2 — Connect**
Add camera RTSP URLs in soca-dashboard. Draw detection zones. Configure rules. No coding required.

**Step 3 — Activate**
Register the edge in soca-control. Activate license key via Settings → License. System is live.

**Step 4 — Operate**
Monitor live, respond to alerts, review reports. Add additional edges by repeating steps 1–3.

**Speaker note:**
Deployment is designed to be fast. A single-site pilot can be live in under four hours. Adding more edges does not require any changes to the central server — each edge is independent and self-registering.

---

#### SLIDE 25 — Closing / Call to Action

**Title:** See It Working on Your Cameras

**Visual:** Clean dark slide with a single bold CTA and contact information.

**Three options:**

📋 **30-Day Pilot Program**
Deploy SOCA on one site. We measure baseline incident rates and compliance levels. At day 30, you receive a quantified improvement report.

🎯 **Live Demo**
Schedule a demonstration using your actual camera feeds or our reference environment.

📩 **Contact**
[contact information / email / website placeholder]

**Three proof points (bottom bar):**
- ⚡ Sub-2-second alert latency from detection to notification
- 🔋 Runs on existing CCTV infrastructure — no camera replacement
- 🛡 On-premises processing — your video never leaves your site

**Speaker note:**
The best way to evaluate SOCA is to run it on your own cameras, against your actual security or safety challenges. The 30-day pilot is zero-risk — you measure improvement with your own data. Let us show you what your cameras have been seeing all along.

---

### APPENDIX

---

#### SLIDE 26 — Technical Specifications

**Title:** Platform Technical Reference

**Three-column specs table:**

**Infrastructure:**
- soca-engine: FastAPI / Python 3.11+
- soca-dashboard: Django 5.0+ / Python 3.11+
- soca-control: Django 5.0+ / Python 3.11+
- Database: SQLite (dev) / PostgreSQL (prod)
- RTSP relay: MediaMTX
- Message queue: Redis / Google Pub/Sub

**AI Models:**
- Framework: Ultralytics YOLO (v8 / v11)
- Formats: PyTorch `.pt` (with tracking), ONNX `.onnx`
- Device: CPU / CUDA GPU (configurable)
- Resolution: 640px default (configurable)
- FP16 half-precision: optional

**Ports (default):**
- soca-engine: 8001
- soca-dashboard: 8080
- soca-control: 8000
- soca-service: 8010
- MediaMTX RTSP: 8554
- MediaMTX HLS: 8888

---

#### SLIDE 27 — Glossary

**Title:** Terminology Reference

| Term | Definition |
|---|---|
| **Edge Device** | The on-premises machine running soca-dashboard and soca-engine, co-located with CCTV cameras |
| **ROI** | Region of Interest — the polygon, rectangle, or line drawn in the camera frame where detection applies |
| **Schedule** | A configuration binding one camera to one AI model with a specific ROI and set of detection rules |
| **Job** | A running inference process in soca-engine — one job per active schedule |
| **Rule** | A detection trigger condition: object class, threshold, timing, and action (alert/snapshot/Telegram) |
| **soca-entitlement** | The license server that issues and validates UUID keys controlling camera and model limits |
| **Free Tier** | Default mode with no license — limited to 1 active camera and 1 concurrent AI model |
| **MediaMTX** | RTSP/HLS relay server on each edge device; reduces camera connection slots and hides credentials |

---

*Document Version: 1.0 — SOCA Platform v1.2*
*Prepared: May 2026*
*Classification: Internal — For GenAI Presentation Development*

## 4. Architecture

### System Overview

_Source: `architecture/system-overview.md`_

## SOCA Platform — System Architecture Overview

### What is SOCA?

**SOCA** (Security Operations & Camera Analytics) is an enterprise-grade video analytics platform built on YOLO deep learning models. It transforms existing CCTV infrastructure into intelligent, AI-powered monitoring systems deployed at the edge with centralized management in the cloud or on-premises.

---

### Four Services

```
┌─────────────────────────────────────────────────────────────────┐
│                         EDGE DEVICE                             │
│                                                                 │
│  soca-dashboard  (port 8080) ──────── soca-engine (port 8001)  │
│  Django web UI                        FastAPI YOLO processor    │
│  Camera & schedule management         RTSP ingestion            │
│  Settings & edge config               Job management            │
│                  │ write .env                   │               │
│                  └──────────────────────────────┘               │
│                                       │ publishes alerts        │
│                                       │ uploads snapshots ──────┼──► Google Cloud Storage
└───────────────────────────────────────┼─────────────────────────┘          │
                                        │                                     │
                              Redis Stream  or  Google Pub/Sub                │
                                        │                                     │
┌───────────────────────────────────────┼─────────────────────────┐          │
│                         CLOUD / SERVER                          │          │
│                                       ▼                         │          │
│  soca-service  (port 8010)            │                         │          │
│  FastAPI microservice                 │                         │          │
│  Consumes Redis/Pub/Sub ──────────────┘                         │          │
│  POSTs alerts via HTTP ──────────────────────────────────────►  │          │
│                                                                 │          │
│  soca-control  (port 8000)            ◄─── snapshot URLs ───────┼──────────┘
│  Django fleet dashboard                                         │
│  Receives ingest API                                            │
│  Stores alerts in DB                                            │
│  Reporting & analytics                                          │
└─────────────────────────────────────────────────────────────────┘
```

---

### Service Roles

| Service | Type | Port | Role |
|---------|------|------|------|
| **soca-engine** | FastAPI | 8001 | YOLO video processing, job management, alert publishing, GCS snapshot sync |
| **soca-dashboard** | Django | 8080 | Per-edge UI: cameras, schedules, rules, settings |
| **soca-service** | FastAPI | 8010 | Alert consumer microservice; bridges edge transports to soca-control |
| **soca-control** | Django | 8000 | Fleet dashboard: all edges, alerts, reports, user management |

---

### Alert Transport Options

#### Option A — Redis Stream (default, LAN/local)

```
soca-engine ──XADD──► Redis stream ──XREADGROUP──► soca-service ──POST──► soca-control
```

- Best for: on-premises deployments, LAN environments
- soca-engine pushes detection events to a named Redis stream
- soca-service uses Consumer Groups (`XREADGROUP`/`XACK`) — safe for multiple replicas
- Each edge uses a unique stream name (e.g. `edge-jakarta-01:soca:detections`)

#### Option B — Google Pub/Sub (cloud, multi-site)

```
soca-engine ──publish──► Pub/Sub topic ──subscribe──► soca-control (consume_pubsub)
```

- Best for: cloud deployments, geographically distributed edges
- No Redis proxy VM required — fully managed GCP service
- soca-control subscribes directly via `consume_pubsub` management command (no soca-service needed)
- All edges can publish to a single topic; `edge_name` field identifies the source
- Per-edge subscription configured in soca-control Settings → Edges → Edit → Pub/Sub Subscription

---

### Snapshot Storage Options

#### Option A — Local (edge-proxied)

```
soca-engine → saves JPG to local snapshots/ → snapshot_path in alert
soca-control → builds URL via soca-dashboard: {edge.url}/alerts/snapshot/{rel_path}
```

- Best for: LAN/on-premises where soca-dashboard is directly reachable
- No cloud storage required

#### Option B — Google Cloud Storage (recommended for cloud/multi-edge)

```
soca-engine → saves JPG to local snapshots/
            → async GCS sync (every 10s) → gs://{bucket}/{edge_prefix}/snapshots/{job_id}/{frame}.jpg
soca-control → builds public URL: https://storage.googleapis.com/{bucket}/{edge_prefix}/snapshots/...
```

- Best for: cloud deployments, multiple edges sharing one bucket
- soca-engine runs `core/gcs_sync.py` as a FastAPI lifespan background task
- Each edge uses its own path prefix (`GCS_PATH_PREFIX`) to avoid collisions
- GCS bucket must be publicly readable (`allUsers:objectViewer`)
- Configured via: `GCS_BUCKET` + `GCS_PATH_PREFIX` in soca-engine `.env`
- Per-edge prefix set in soca-control Settings → Edges → Edit → GCS Path Prefix

---

### YOLO Inference Engine

#### Supported Model Formats

| Format | Inference method | Tracking | Device selection |
|--------|-----------------|----------|-----------------|
| `.pt` (PyTorch) | `model.track()` | Yes — `track_id` populated | `model.to(device)` at load |
| `.onnx` (ONNX) | `model.predict()` | No — `track_id` is None | Device passed per-call |

#### Device Configuration

| Device | Config value | Notes |
|--------|-------------|-------|
| CPU | `INFER_DEVICE=cpu` | Default — works everywhere |
| CUDA GPU | `INFER_DEVICE=cuda:0` | NVIDIA GPU required |

> MPS (Apple Silicon) was removed — pass device explicitly via `INFER_DEVICE`.

#### Performance Configuration (soca-engine `.env`)

```env
INFER_DEVICE=cpu          # cpu | cuda:0
INFER_IMGSZ=640           # inference resolution (YOLO native = 640)
INFER_HALF=false          # FP16 half-precision — opt-in, test before enabling
```

Per-job `imgsz` override available via `JobConfig.imgsz`.

#### Performance Tiers (implemented)

| Tier | Change | Impact |
|------|--------|--------|
| 1 | `INFER_DEVICE`, `INFER_IMGSZ`, `INFER_HALF` env vars | 30–60% faster inference |
| 2 | Dedicated RTSP reader thread (shared-slot pattern) | Decouples network I/O from inference |
| 3 | Conditional LPR — skip EasyOCR when no vehicles (COCO 2/5/7) | Eliminates wasted 50–200ms OCR calls |

---

### Rule Engine

#### Detection Modes

| Mode | Description |
|------|-------------|
| `detection` | Triggers when objects are detected in/out of ROI |
| `people_count` | Counts crossings across a virtual line (in/out) |
| `crowd` | Triggers when in-ROI person count exceeds threshold |

#### Line Crossing Direction

| Direction | Counts as IN when... |
|-----------|---------------------|
| `any` | Every crossing (both directions count as IN) |
| `left_to_right` | Centroid x increasing across line |
| `right_to_left` | Centroid x decreasing across line |
| `top_to_bottom` | Centroid y increasing across line |
| `bottom_to_top` | Centroid y decreasing across line |

> Note: `any` mode counts ALL crossings as IN regardless of line orientation — use directional modes for entrance/exit counting.

#### Alert Categories

Rules can be tagged with a category string that routes alerts to specific report pages in soca-control:

| Category value | soca-control report page |
|---------------|--------------------------|
| `Intrusion` | Reports → Intrusion Detection |
| `PPE` | Reports → PPE Violations |
| `Detection` | Reports → Object Detection |
| `Counting` | Reports → People Counting |
| `Crowd` | Reports → Crowd Detection |
| `LPR` | Reports → License Plate Recognition |

---

### MediaMTX Relay (RTSP Ingestion)

MediaMTX is an RTSP/HLS relay server running on each edge device alongside soca-dashboard and soca-engine.

```
CCTV camera ──► MediaMTX relay (sourceOnDemand: yes)
                     │
                     ├──► soca-engine  rtsp://localhost:8554/<camera_name>  (detection)
                     │
                     └──► Browser      http://edge-host:8888/<camera_name>   (HLS viewer)
```

**Why relay instead of direct camera RTSP:**
- Physical cameras have limited connection slots — relay means only one outbound connection per camera
- Camera credentials stay on the edge; job configs carry `rtsp://localhost:8554/<name>` instead of `rtsp://user:pass@camera-ip/...`
- `sourceOnDemand: yes` on every path means MediaMTX connects to the camera only when a consumer (soca-engine or a browser) is actively reading — no idle connections

**MediaMTX ports:**

| Port | Protocol | Usage |
|------|----------|-------|
| 8554 | RTSP | soca-engine ingestion relay |
| 8888 | HTTP/HLS | Built-in Low-Latency HLS web player (browser-accessible) |
| 8889 | WebRTC | WebRTC viewer (available but not yet wired up) |

**Configuration fields:**

| Field | Location | Purpose |
|-------|----------|---------|
| `EdgeConfig.mediamtx_rtsp_url` | soca-dashboard Settings → Connection | Base RTSP URL used by soca-engine, default `rtsp://localhost:8554` |
| `Edge.mediamtx_url` | soca-control Settings → Edit Edge | Public HLS URL reachable from operator's browser, e.g. `http://192.168.1.100:8888` |

**Stream mode badge** (Assets page in soca-control): each camera row shows `Relay` (green) when `mediamtx_rtsp_url` is set on the edge, or `Direct` (yellow) when jobs fall back to the raw camera URL.

**Fallback:** if `mediamtx_rtsp_url` is blank on `EdgeConfig`, `Schedule.to_job_config()` falls back to `camera.full_rtsp_url` — no regression.

---

### Data Flow Summary

1. **soca-engine** captures RTSP video frames via MediaMTX relay (`rtsp://localhost:8554/<camera_name>`) — or direct camera RTSP as fallback when relay is not configured
2. YOLO model runs inference (`.pt` → `track()`, `.onnx` → `predict()`); ROI filtering and rule evaluation applied
3. When a rule fires: snapshot saved locally + alert published to Redis stream or Pub/Sub topic
4. **GCS sync** (background task, every 10s): new snapshots uploaded to `gs://{bucket}/{edge_prefix}/snapshots/...`
5. **soca-control** (via `consume_streams` or `consume_pubsub`): parses payload, creates `Alert` DB record
6. `Alert.snapshot_url` builds either a GCS public URL or edge-proxy URL depending on `SiteConfig.snapshot_storage`
7. Operators view alerts, snapshots, reports, and monitor fleet health via **soca-control** web UI

---

### Security Model

- `SOCA_CONTROL_INGEST_KEY` — shared bearer token between soca-service and soca-control
- All ingest API calls require `Authorization: Bearer <key>` header
- Key generated and stored in `db.conf`; configurable via soca-control Settings UI
- soca-dashboard API key — per-edge key for soca-control to call soca-dashboard/soca-engine APIs
- GCS credentials — service account JSON key uploaded via soca-dashboard or soca-control Settings UI (stored in `credentials/` folder)
- GCS bucket — publicly readable (`allUsers:objectViewer`) for direct browser access to snapshot images

---

### Horizontal Scaling

| Component | How to scale |
|---|---|
| **soca-engine** | Independent per edge device |
| **soca-dashboard** | Independent per edge device |
| **soca-service (Redis)** | Run N instances — Consumer Groups ensure each message processed once |
| **soca-control (Pub/Sub)** | Direct subscription per edge — no soca-service needed |
| **soca-control** | Stateless Django — scale with gunicorn workers or multiple instances behind a load balancer |

---

### Ports Summary

| Service | Default Port | Configurable via |
|---------|-------------|-----------------|
| soca-engine | 8001 | `PORT` env var |
| soca-dashboard | 8080 | `PORT` env var |
| soca-service | 8010 | `PORT` env var |
| soca-control | 8000 | `PORT` env var |
| Redis | 6379 | Redis config |
| MediaMTX RTSP relay | 8554 | mediamtx.yml |
| MediaMTX HLS viewer | 8888 | mediamtx.yml |

### Licensing Model

_Source: `architecture/licensing-model.md`_

## SOCA Licensing Model

**Date:** 2026-05-01  
**Version:** 1.2  
**Scope:** soca-dashboard · soca-engine · soca-entitlement

---

### Overview

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

### Free Tier vs Licensed

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

### Data Model — `EdgeConfig` (soca-dashboard)

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

### Activation Flow

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

### Enforcement Points

#### soca-dashboard

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

#### soca-engine

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

### License Client (`app/license_client.py`)

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

### Settings UI

Navigate to **Settings → License** tab in soca-dashboard.

#### Unlicensed state
- Yellow "Free Tier" warning banner
- Input field for the license key UUID
- **Activate License** button

#### Licensed state
- Green **Active** badge
- Stat cards: Max Cameras · Max AI Models · Expires
- Key + timestamps shown
- **Refresh Status** button — re-calls the check endpoint
- **Unlink License** button — clears license locally (does NOT revoke on entitlement)

#### Expired / Revoked state
- Red badge with reason
- **Re-check Status** button
- **Clear License Key** button

#### Global warning banner
A yellow banner is shown on every page when the edge is not licensed. It links to the License tab.

#### Camera list
The camera list subtitle shows `x / max active limit` with a warning color when at the cap.

---

### soca-entitlement API Reference

Base URL: `http://34.101.213.40:8080`

#### POST `/api/v1/license/activate/`

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

#### GET `/api/v1/license/check/<license_key>/`

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

### Configuration

#### soca-dashboard `.env`

```env
APP_VERSION=1.2
```

The entitlement server URL is in `app/license_client.py`:
```python
ENTITLEMENT_URL = 'http://34.101.213.40:8080'
```

#### soca-engine `.env` (managed by soca-dashboard)

```env
MAX_CONCURRENT_JOBS=1   # written after license activation
MAX_LOADED_MODELS=1     # default free-tier value
```

---

### Version

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

### Sequence: First Boot

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

### Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| "Cannot reach entitlement server" | Network blocked to `34.101.213.40:8080` | Check firewall / internet access from edge |
| "License already activated by another device" | MAC address mismatch | Use the originally provisioned machine, or contact vendor for a new key |
| "License key not found" | Wrong UUID or key deleted from entitlement server | Verify key with vendor |
| Limits not applied in engine after activation | Engine reads `.env` only at startup | Restart soca-engine via Settings → Operations |
| Camera limit not enforced on engine side | Engine limit is applied at job start, not camera registration | Camera count is dashboard-enforced; engine limit applies to concurrent jobs |

### NXVMS Integration

_Source: `architecture/2026-04-16-nxvms-integration.md`_

## NX VMS Integration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add NX VMS (Network Optix) as an optional RTSP relay and AI event push target alongside existing MediaMTX support.

**Architecture:** A per-site `relay_type` toggle in `EdgeConfig` switches the entire edge between MediaMTX and NX VMS. When NX VMS is active, cameras are registered via NX REST API (physicalId stored on each Camera), soca-engine pulls RTSP from NX VMS instead of MediaMTX, and triggered rules fire HTTP Generic Events back to NX VMS.

**Tech Stack:** Django 5 (soca-dashboard), FastAPI + httpx (soca-engine), NX VMS REST API v3 (auth), v2 (devices), `/api/createEvent` (generic events).

---

### File Map

#### soca-dashboard (new/modified)
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

#### soca-engine (new/modified)
| File | Change |
|------|--------|
| `soca-engine/core/nxvms_client.py` | **New** — `NXVMSEventPusher` class: login, push_event |
| `soca-engine/models/schemas.py` | Add `nxvms: dict \| None = None` field to `JobConfig` |
| `soca-engine/core/action_dispatcher.py` | Add `nxvms_pusher` + `nxvms_device_id` params to `dispatch()`; add `nxvms_event` handler |
| `soca-engine/workers/detection_worker.py` | Instantiate `NXVMSEventPusher` from `cfg.nxvms`; pass to `dispatch()` |

---

### Task 1: soca-dashboard Models & Migration

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

### Task 2: soca-dashboard NX VMS Client

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

### Task 3: soca-dashboard Camera Registration Integration

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

### Task 4: soca-dashboard `edge_settings()` View

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

### Task 5: soca-dashboard Settings UI

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

### Task 6: Camera Form & List UI

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

### Task 7: Batch Registration Management Command

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

### Task 8: soca-engine — `JobConfig` & `NXVMSEventPusher`

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

### Task 9: soca-engine — Action Dispatcher `nxvms_event`

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

### Task 10: soca-engine — Detection Worker Wires `NXVMSEventPusher`

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

### Task 11: Rule Form UI — `action_nxvms` Toggle

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

### Verification Steps

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

### SOCA Gap Analysis

_Source: `architecture/sow-vs-soca-gap-analysis.md`_

## Gap Analysis: SoW Vision AI-IT 2026 vs. Current SOCA Platform

**Document date:** 2026-05-04  
**Source 1:** `SoW Vision AI- IT 2026.docx` — Statement of Work for VMX–NX VMS Exploration, Safety Compliance Analytics & Dashboard  
**Source 2:** Current SOCA platform as documented in `system-overview.md`, `object-detection-processor.md`, `soca-service/overview.md`, `soca-control/design-spec.md`, `2026-04-16-nxvms-integration.md`, `licensing-model.md`  
**Scope:** Phase-1 (Safety Compliance)

---

### Summary Scorecard

| SoW Section | Requirement Area | Status |
|---|---|---|
| **1. NX VMS** | NX VMS server running (NX Meta 6.1.1) | ✅ Available |
| **1. NX VMS** | RTSP ingestion from NX VMS into soca-engine | ✅ Available (`soca-edge-nx`) |
| **1. NX VMS** | Camera GUID discovery from NX REST API | ✅ Available (`soca-edge-nx`) |
| **1. NX VMS** | NX bookmark push on AI alert trigger | ✅ Available (confirmed working) |
| **1. NX VMS** | NX analytics bounding-box overlay in NX Desktop | ⚠️ Blocked (ec2 endpoints removed in NX 6.x) |
| **1. NX VMS** | Historical event extraction from NX to dashboard | ❌ Not Done |
| **1. NX VMS** | Formal architecture diagram & installation guide | ⚠️ Partial |
| **2. Dashboard** | Real-time safety event display | ✅ Available |
| **2. Dashboard** | Historical safety event display | ✅ Available |
| **2. Dashboard** | PPE compliance event reporting | ✅ Available |
| **2. Dashboard** | PPE compliance **rate** (percentage KPI) | ❌ Not Done |
| **2. Dashboard** | Unsafe behavior detection count | ✅ Available |
| **2. Dashboard** | Incident frequency & trend visualization | ⚠️ Partial |
| **2. Dashboard** | Location-based risk heatmap | ❌ Not Done |
| **2. Dashboard** | Compliance score metric | ❌ Not Done |
| **2. Dashboard** | KPI definition document | ❌ Not Done |
| **2. Dashboard** | Dashboard wireframe & technical documentation | ❌ Not Done |
| **3. AI Model** | PPE detection (helmet, vest, mask) | ✅ Available |
| **3. AI Model** | Restricted area violation detection | ✅ Available |
| **3. AI Model** | Unsafe behavior detection | ⚠️ Partial |
| **3. AI Model** | Event with type, confidence, timestamp, camera/location | ✅ Available |
| **3. AI Model** | NX bookmark push (AI → NX timeline) | ✅ Available (`soca-edge-nx`) |
| **3. AI Model** | NX analytics metadata pipeline (AI → NX overlay) | ⚠️ Blocked (NX 6.x removed ec2 endpoints) |
| **3. AI Model** | End-to-end flow: Camera → NX → AI → Dashboard | ✅ Available (`soca-edge-nx`) |

**Legend:** ✅ Available · ⚠️ Planned/Partial · ❌ Not Done

---

### Current SOCA Platform Architecture (Four Services + Control Plane)

Before the gap analysis, here is what the platform actually consists of:

```
┌──────────────── EDGE DEVICE (per site) ─────────────────┐
│                                                           │
│  soca-dashboard (port 8080)  ←→  soca-engine (port 8001) │
│  Django per-edge UI               FastAPI YOLO processor  │
│  Camera/schedule/rule mgmt        RTSP ingestion + jobs   │
│  NX VMS integration (planned)     Alert publishing        │
│                         │                                 │
└─────────────────────────┼─────────────────────────────────┘
                          │ Redis Stream or Google Pub/Sub
                          ▼
              soca-service (port 8010)         ← optional bridge
              FastAPI alert consumer
              Bridges edge transports to control plane
                          │ HTTP POST /api/v1/ingest/
                          ▼
┌──────────────── CONTROL PLANE (central) ────────────────┐
│                                                          │
│  soca-control (port 8000) — Django fleet dashboard       │
│  ┌──────────────────────────────────────────────────┐    │
│  │  Fleet overview: all edges, health, active jobs  │    │
│  │  Live monitor: MJPEG streams per edge            │    │
│  │  Reports: Intrusion / PPE / Detection / Crowd /  │    │
│  │           People Counting / LPR                  │    │
│  │  (Stats tab + Detail tab + Chart.js bar charts)  │    │
│  │  CSV + PDF export                                │    │
│  │  User management                                 │    │
│  │  Edge CRUD (register, configure, disable)        │    │
│  │  GCS / snapshot storage config                   │    │
│  └──────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────┘
                          │
              soca-entitlement (port 8080)
              License activation & enforcement
```

**soca-control is the central control plane.** It aggregates alerts from all edge devices into a single fleet dashboard, provides per-category report pages with statistics and chart views, and is the operator-facing command center for the entire deployment.

---

### Section 1 — VMX / NX VMS Exploration & Integration

> **Correction:** NX VMS integration is **fully implemented** in `soca-edge-nx/` — a purpose-built variant of the edge stack. The original `soca-edge/` uses MediaMTX; `soca-edge-nx/` replaces it with NX VMS as the stream source and adds NX bookmark/analytics push. The implementation plan in `2026-04-16-nxvms-integration.md` was the design document; `soca-edge-nx/` is the delivered code.

#### What SoW Requires

| # | Requirement | Deliverable |
|---|---|---|
| 1.1 | Study NX VMS architecture (NX Witness, NX AI Manager, NX for Developers) | Understanding documented |
| 1.2 | Install and configure NX VMS in client–server topology | Running NX VMS environment |
| 1.3 | Configure media server, client access, user roles/permissions | Operational topology |
| 1.4 | Validate recording, playback, and live streaming | PoC verified |
| 1.5 | Enable AI event notifications via REST API (event trigger, payload, latency) | Working REST integration |
| 1.6 | Extract NX historical events → normalize schema → push to dashboard | Historical data pipeline |
| 1.7 | Deliverable: NX VMS installed + running | Infrastructure ready |
| 1.8 | Deliverable: Architecture diagram + installation guide + API integration flow | Documentation |
| 1.9 | Deliverable: Working PoC (AI event via REST API + historical event in dashboard) | Verified PoC |

#### What SOCA Currently Has (`soca-edge-nx/`)

| Capability | Status | Notes |
|---|---|---|
| NX VMS architecture understanding | ✅ Done | Tested against NX Meta 6.1.1 at `192.168.18.42:7001`; auth, device, and bookmark APIs fully explored |
| NX VMS as RTSP stream source | ✅ Implemented | `stream_source = nx_vms` in EdgeConfig; soca-engine connects to `rtsp://<nx-host>:7001/<camera-guid>?stream=secondary` or HTTPS MPJPEG endpoint |
| Camera GUID discovery from NX | ✅ Implemented | `GET /cameras/nx-cameras/` fetches device list from NX REST v2 (Bearer token) with fallback to EC2 + Digest/Basic auth (NX v4 compat) |
| Per-camera NX GUID assignment | ✅ Implemented | Camera form in `soca-edge-nx` has NX camera GUID field + "Fetch from NX VMS" live picker modal |
| NX bookmark push on alert trigger | ✅ Working | `nx_publisher.push_bookmark()` creates timeline bookmarks in NX Desktop (`POST /rest/v2/devices/{guid}/bookmarks`); tested and confirmed working on NX 6.1.1 |
| NX analytics metadata push (bboxes) | ⚠️ No-op | `push_detection()` and `push_best_shot()` target `ec2/analyticsMetadataPackets` and `ec2/analyticsObjectBestShot` — both removed in NX 6.x; kept as graceful no-ops |
| NX analytics engine registration | ⚠️ No-op | `register_engine()` targets `ec2/saveAnalyticsEngine` — removed in NX 6.x; graceful no-op |
| NX VMS settings UI tab | ✅ Implemented | Dedicated "NX VMS" tab in Settings with `stream_source` toggle, NX server URL, credentials, camera ID, bookmark push toggle |
| NX fields in DB (migration) | ✅ Done | Migrations `0026_nx_vms`, `0027_camera_nx_rtsp_url`, `0028_alter_camera_nx_rtsp_url` applied; `stream_source`, `nx_url`, `nx_username`, `nx_password`, `nx_camera_id`, `nx_rtsp_url` all in schema |
| Action dispatcher wired to NX | ✅ Done | `action_dispatcher.py` calls `push_bookmark`, `push_detection`, `push_best_shot` on rule triggers; `NXPushConfig` injected from job config |
| NX VMS installed and running (server infra) | ✅ Done | NX Meta 6.1.1 confirmed running at `192.168.18.42:7001` (tested in code comments) |
| Historical event extraction from NX | ❌ Not Done | No pipeline to pull NX's own stored historical events into soca-control |
| Architecture diagram / installation guide | ⚠️ Partial | Integration plan doc exists; no formal deployment diagram or step-by-step installation guide for NX client–server topology |
| Working PoC formal documentation | ⚠️ Partial | Code tested (bookmark push confirmed working), but no formal PoC report or verification checklist completed |

#### NX VMS Integration Architecture (actual, in `soca-edge-nx/`)

```
CCTV camera ──► NX VMS Server (192.168.18.42:7001)
                    │
                    ├──► soca-engine  rtsp://<nx-host>:7001/<camera-guid>?stream=secondary
                    │    (stream_source = nx_vms)
                    │         │
                    │         ▼
                    │    YOLO inference → rule triggers
                    │         │
                    │         ├──► Redis/Pub/Sub alert → soca-control (dashboard)
                    │         │
                    │         └──► NX bookmark push (POST /rest/v2/devices/{guid}/bookmarks)
                    │              Visible in NX Desktop Timeline
                    │
                    └──► NX Desktop Client (recording, playback, live view — managed by NX)
```

#### Gap Summary — Section 1

> **NX VMS code integration is delivered in `soca-edge-nx/`.** RTSP ingestion from NX, camera GUID discovery, and bookmark push on AI triggers all work. The main remaining gaps are: (1) **Analytics metadata visualization in NX** is not possible because NX 6.x removed the `ec2` analytics endpoints — a NX SDK v4 upgrade or alternative approach is needed; (2) **Historical event extraction** from NX's own event store into soca-control is not implemented; (3) formal documentation deliverables (architecture diagram, installation guide) are not completed.

**Remaining work:**
1. Investigate NX REST v4 analytics SDK (replacement for removed `ec2` endpoints) to restore bounding-box overlay in NX Desktop
2. Implement NX historical event extraction pipeline (pull NX bookmarks/events → normalize → push to soca-control)
3. Produce formal architecture diagram and NX installation/configuration guide
4. Complete formal PoC verification checklist

---

### Section 2 — Dashboard Development (Safety Compliance)

#### What SoW Requires

| # | KPI / Component | Type |
|---|---|---|
| 2.1 | PPE compliance rate | KPI metric (%) |
| 2.2 | Unsafe behavior detection count | KPI metric (count) |
| 2.3 | Incident frequency and trend | Time-series chart |
| 2.4 | Location-based risk heatmap | Spatial visualization |
| 2.5 | Summary statistics (dashboard panel) | Component |
| 2.6 | Time-series analytics | Component |
| 2.7 | Event logs | Component |
| 2.8 | Compliance score | Composite KPI |
| 2.9 | Integration with NX event data | Data source |
| 2.10 | Integration with AI inference outputs | Data source |
| 2.11 | Real-time and historical data visualization | Data mode |
| 2.12 | KPI definition document | Deliverable |
| 2.13 | Dashboard wireframe & technical documentation | Deliverable |

#### What SOCA Currently Has

| Capability | Status | Notes |
|---|---|---|
| Real-time alert/event display | ✅ Available | soca-control receives and displays alerts in real time via Redis Streams or Pub/Sub |
| Historical alert storage and display | ✅ Available | Alerts stored in DB, viewable with timestamps and snapshots |
| PPE alert events | ✅ Available | Rules tagged `category: PPE` route to soca-control → Reports → PPE Violations |
| Unsafe behavior / intrusion count | ✅ Available | Rules tagged `category: Intrusion` route to Reports → Intrusion Detection |
| Event log (alert list) | ✅ Available | All triggered alerts stored with frame_id, timestamp, snapshot, detection counts |
| Per-category report pages | ✅ Available | soca-control has dedicated pages: Intrusion, PPE, Object Detection, Crowd, People Counting, LPR — each with Stats tab + Detail tab |
| Chart.js bar charts in report detail view | ✅ Available | `_detail_chart.html` partial renders Chart.js time-series bar charts per category |
| CSV + PDF export from reports | ✅ Available | `reports.py` module exports alerts per category |
| Fleet summary cards + top cameras chart | ✅ Available | soca-control dashboard (`/`) shows fleet-wide summary with top cameras chart |
| Confidence score, timestamp, camera/location in payload | ✅ Available | Schema v2.3 includes all these fields in Redis/Pub/Sub messages |
| Snapshot evidence per alert | ✅ Available | Annotated JPEG saved and linked to each alert |
| Multi-edge fleet view | ✅ Available | soca-control shows all edges, their status, and aggregated alerts |
| User authentication & role management | ✅ Available | Login, user roles enforced in soca-dashboard and soca-control |
| AI inference → alert → dashboard pipeline | ✅ Available | soca-engine → Redis/Pub/Sub → soca-service → soca-control (proven end-to-end) |
| **PPE compliance rate (% metric)** | ❌ Not Done | SOCA stores raw PPE alert counts; a "compliance rate" (detections without PPE ÷ total persons) requires a denominator count — not implemented |
| **Location-based risk heatmap** | ❌ Not Done | No spatial/heatmap visualization exists anywhere in the platform |
| **Compliance score** | ❌ Not Done | No composite safety score computed or displayed |
| **Incident frequency trend chart** | ⚠️ Partial | Chart.js bar chart is implemented in the report Detail tab (`_detail_chart.html`); however it is per-category and per-edge — a cross-category safety trend dashboard and daily/weekly rollup view for compliance is not confirmed |
| **Integration with NX event data** | ❌ Not Done | NX VMS not deployed; NX events not flowing into dashboard |
| **KPI definition document** | ❌ Not Done | No standalone KPI definition document found |
| **Dashboard wireframe & technical documentation** | ❌ Not Done | No wireframe or formal dashboard design spec found |

#### Gap Summary — Section 2

> **Core data infrastructure is solid.** Events flow from cameras through AI inference to the dashboard in real time. Per-category report pages exist for Safety Compliance use cases (PPE, Intrusion). However, the **analytical KPI layer** specified in the SoW is missing: there is no compliance rate calculation, no location heatmap, no compliance score, and no confirmed trend charting. The dashboard shows raw alert events, not processed safety KPIs.

**What needs to happen:**
1. Implement PPE compliance rate = `(frames with PPE violations) / (frames with personnel present)` — requires counting "clean" frames as denominator
2. Build location-based risk heatmap — needs camera location metadata and alert density aggregation per location
3. Implement compliance score — define formula (e.g. weighted average of compliance rates across KPI categories)
4. Confirm/build incident frequency trend charts (daily/weekly time-series views)
5. Produce KPI definition document
6. Produce dashboard wireframe and technical documentation

---

### Section 3 — AI Model & Analytics Development (Safety Compliance)

#### What SoW Requires

| # | Requirement | Deliverable |
|---|---|---|
| 3.1 | PPE detection (helmet, vest, mask) | AI model + integration |
| 3.2 | Unsafe behavior detection | AI model + integration |
| 3.3 | Restricted area violation detection | AI model + integration |
| 3.4 | Model output: event type, confidence score, timestamp, camera/location metadata | Event schema |
| 3.5 | NX-compatible event format | NX generic event push |
| 3.6 | Dashboard analytics metrics from AI inference | Metric pipeline |
| 3.7 | Validated end-to-end flow: Camera → NX → AI → Dashboard | PoC |
| 3.8 | AI model documentation (architecture, inputs, outputs) | Documentation |

#### What SOCA Currently Has

| Capability | Status | Notes |
|---|---|---|
| PPE detection | ✅ Available | YOLO models can detect PPE classes (helmet, vest, mask) via `cls_ids` config; category tag `PPE` supported |
| Restricted area violation | ✅ Available | ROI polygon-based intrusion detection; `category: Intrusion` routes to intrusion report |
| Crowd detection | ✅ Available | `people_count` and `crowd` modes with threshold rules |
| Confidence score in event | ✅ Available | `confidence` field in Detection schema, included in Redis/Pub/Sub payload |
| Timestamp in event | ✅ Available | ISO timestamp in every event payload (`timestamp` field) |
| Camera/location metadata | ✅ Available | `camera_id`, `edge_name` in every payload |
| Event type (alert category) | ✅ Available | `alert_category` field in schema v2.2 (Intrusion / PPE / Detection / Counting / Crowd / LPR) |
| Dashboard analytics from AI inference | ✅ Available | Per-category report pages in soca-control consume AI-generated alerts |
| Model documentation | ⚠️ Partial | `object-detection-processor.md` documents the engine architecture; no model-specific training documentation (model is YOLO, not custom-trained) |
| **Unsafe behavior detection (specialized model)** | ⚠️ Partial | Generic YOLO detection is used; "unsafe behavior" (e.g. no hardhat + at height) requires a trained custom model — not confirmed available or integrated |
| **NX bookmark push on trigger** | ✅ Available | `nx_publisher.push_bookmark()` fires on every rule trigger in `soca-edge-nx`; creates coloured timeline marker in NX Desktop Client |
| **NX analytics overlay (bboxes)** | ⚠️ Blocked | `push_detection()` targets `ec2/analyticsMetadataPackets` — endpoint removed in NX 6.x; needs NX REST v4 analytics SDK |
| **Camera → NX → AI → Dashboard** end-to-end | ✅ Available | `soca-edge-nx` flow: Camera → NX VMS (recording) → soca-engine (RTSP from NX) → YOLO inference → Redis/Pub/Sub → soca-control |
| **Working PoC with NX** | ✅ Available | Bookmark push confirmed working on NX Meta 6.1.1 at `192.168.18.42:7001` |

#### Gap Summary — Section 3

> **AI detection capabilities and the NX integration path are both operational in `soca-edge-nx/`.** The end-to-end flow Camera → NX VMS → soca-engine (RTSP pull) → YOLO inference → NX bookmark + soca-control alert is implemented and tested. The remaining gap is **bounding-box overlay visualization inside NX Desktop** — this requires the NX analytics SDK which was removed in NX 6.x. "Unsafe behavior" as a concept is covered by configurable ROI rules; a purpose-trained specialized model has not been developed.

**Remaining work:**
1. Investigate NX REST v4 analytics SDK to restore detection metadata push (bboxes visible in NX Desktop)
2. Evaluate whether a custom-trained "unsafe behavior" model is needed or if YOLO rules with ROI zones are sufficient
3. Produce formal model documentation (inputs/outputs/architecture) as a standalone deliverable

---

### What Is Built and Working (Not in SoW — Bonus Capabilities)

The current SOCA platform delivers several capabilities **beyond** Phase-1 SoW scope:

| Capability | Notes |
|---|---|
| **soca-control central control plane** | Full fleet dashboard: manages N edges simultaneously, aggregates all alerts, per-category reports with Stats + Detail + Chart.js views, CSV/PDF export, user management |
| License management system | soca-entitlement enforces per-edge camera and model limits |
| Google Cloud Storage snapshot sync | Cloud-hosted snapshot evidence |
| Google Pub/Sub transport | Geographically distributed multi-site support |
| Live MJPEG monitor stream | Real-time annotated video per active job |
| Telegram notification | Alert push to operator Telegram with annotated snapshot |
| Webhook action | Integrates with external systems on alert trigger |
| LPR (License Plate Recognition) | EasyOCR-based plate reading (not required by SoW Phase-1) |
| People counting / line crossing | Directional crossing counts (not required by SoW Phase-1) |
| Dead-letter queue (DLQ) | Redis message durability fallback |
| Per-job confidence threshold | Fine-tunable per schedule |
| MediaMTX RTSP relay | Protects camera credentials, manages connection slots |

---

### Priority Action Plan to Close Gaps

#### Priority 1 — Close NX VMS Remaining Gaps

| Action | Effort | Dependency |
|---|---|---|
| Investigate NX REST v4 analytics SDK — restore bounding-box overlay in NX Desktop | Research — Medium | NX server upgrade or SDK access |
| Implement NX historical event extraction (pull NX bookmarks/events → normalize → soca-control) | Dev — Medium | NX VMS API access |
| Produce NX architecture diagram (deployment topology, port map) | Docs — Small | — |
| Produce NX installation & configuration guide | Docs — Medium | — |
| Complete formal PoC verification checklist (recording, playback, bookmark, RTSP pull) | QA — Small | NX running |

#### Priority 2 — Safety KPI Analytics Layer (closes Section 2 gaps)

| Action | Effort | Dependency |
|---|---|---|
| Define PPE compliance rate formula + data model (requires denominator: person-present frames) | Design | Agreement on formula |
| Implement compliance rate calculation in soca-control | Dev — Medium | Formula defined |
| Build location-based risk heatmap (requires camera location metadata) | Dev — Large | Camera location data |
| Implement compliance score composite metric | Dev — Medium | KPI formulas defined |
| Build/confirm incident frequency trend charts (time-series bars) | Dev — Small-Medium | — |
| Produce KPI definition document | Docs | — |
| Produce dashboard wireframe & technical documentation | Docs | — |

#### Priority 3 — Model & Documentation Deliverables (closes Section 3 gaps)

| Action | Effort | Dependency |
|---|---|---|
| Evaluate unsafe behavior detection: generic YOLO rules vs. custom model | Research | — |
| Produce AI model documentation (inputs, outputs, architecture) | Docs | — |
| Validate end-to-end Camera → NX → AI → Dashboard flow | QA | Priority 1 complete |

---

### Conclusion

The SOCA platform significantly covers the SoW Phase-1 scope. Two separate edge variants exist:

- **`soca-edge/`** — MediaMTX-based, production-ready, fully operational
- **`soca-edge-nx/`** — NX VMS-integrated variant; RTSP ingestion from NX, camera GUID discovery, bookmark push on AI trigger, and the full Camera → NX → AI → Dashboard pipeline are all implemented and tested against NX Meta 6.1.1

**Remaining gaps fall into three areas:**

1. **NX analytics overlay in NX Desktop** is blocked because NX 6.x removed the `ec2` analytics metadata endpoints that SOCA relies on for bounding-box visualization. Needs investigation of the NX REST v4 analytics SDK.

2. **Safety KPI analytics layer is raw counts, not processed metrics.** The SoW requires PPE compliance rate (%), location heatmap, and compliance score — none are computed. Alert events are stored and categorized correctly, but the aggregation layer is missing.

3. **Documentation deliverables** (NX architecture diagram, installation guide, KPI definition doc, dashboard wireframe) have not been produced as formal standalone artifacts.

The detection pipeline, NX integration code, real-time event flow, fleet dashboard, and per-category reporting collectively cover the majority of Phase-1 requirements.

## 5. Deployment

### Docker Deployment

_Source: `deployment/docker-deployment.md`_

## SOCA Platform — Docker Deployment Guide

### Repository Structure

The platform is split into **3 independent repos** aligned with deployment units,
plus **1 ops repo** that orchestrates them all on a single server.

```
~/projects/
├── soca-edge/          ← Repo 1: edge device  (soca-engine + soca-dashboard)
├── soca-control/       ← Repo 2: cloud server (fleet management dashboard)
├── soca-service/       ← Repo 3: cloud server (alert ingestion microservice)
└── soca-deploy/        ← Repo 4: full-stack orchestration (this server)
```

#### Why this split?

| Concern | Benefit |
|---------|---------|
| Different deployment targets | Edge device vs cloud server — independent CI/CD |
| Different release cadence | Edge firmware updates are independent of backend releases |
| Dependency isolation | soca-edge has heavy deps (PyTorch, OpenCV, EasyOCR) — no reason to pull them into backend CI |
| Independent scaling | soca-service can be scaled separately from soca-control |

---

### Deployment Topology

```
┌──────────────────────────────────────────────────────────────┐
│                        YOUR SERVER                           │
│                                                              │
│  ┌────────────────────────────┐   ┌──────────────────────┐  │
│  │  soca-edge  (Repo 1)       │   │  soca-control        │  │
│  │  ┌──────────────────────┐  │   │  (Repo 2)            │  │
│  │  │ soca-dashboard       │  │   │  Django · port 8000  │  │
│  │  │ Django · port 8080   │  │   └──────────┬───────────┘  │
│  │  └──────────────────────┘  │              │ receives      │
│  │  ┌──────────────────────┐  │   ┌──────────┴───────────┐  │
│  │  │ soca-engine          │  │   │  soca-service        │  │
│  │  │ FastAPI · port 8001  │  │   │  (Repo 3)            │  │
│  │  └──────────────────────┘  │   │  FastAPI · port 8010 │  │
│  └─────────────┬──────────────┘   └──────────┬───────────┘  │
│                │ publishes detections          │ consumes     │
│  ┌─────────────┴──────────────────────────────┴───────────┐  │
│  │                   Redis · port 6379                    │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                              │
│  Orchestrated by: soca-deploy/ (Repo 4)                     │
└──────────────────────────────────────────────────────────────┘
```

#### Container Summary

| Container | Repo | Ports | Role |
|-----------|------|-------|------|
| `soca-edge` | soca-edge | 8080, 8001 | Edge management + YOLO inference |
| `soca-control` | soca-control | 8000 | Fleet dashboard & alert storage |
| `soca-service` | soca-service | 8010 | Alert ingestion from Redis/Pub/Sub |
| `redis` | (image) | 6379 | Detection stream transport |

---

### Prerequisites

- Docker ≥ 24.0 and Docker Compose v2 (`docker compose`)
- ~4 GB RAM for `soca-edge` (YOLO/PyTorch is memory-intensive)
- Git access to all 4 repos

---

### Repo Contents

#### soca-edge/

```
soca-edge/
├── soca-engine/              ← FastAPI inference service
├── soca-dashboard/           ← Django edge management UI
├── docker/
│   └── edge/
│       ├── Dockerfile        ← combined image (supervisord)
│       ├── supervisord.conf  ← manages engine + dashboard processes
│       └── entrypoint.sh     ← runs migrations → starts supervisord
├── docker-compose.yml        ← standalone: edge + redis only
└── .env.example
```

#### soca-control/

```
soca-control/
├── app/                      ← Django app
├── soca_control/             ← Django project settings
├── Dockerfile
├── docker-compose.yml        ← standalone: control only
└── .env.example
```

#### soca-service/

```
soca-service/
├── consumers/                ← Redis + Pub/Sub consumers
├── main.py                   ← FastAPI app
├── Dockerfile
├── docker-compose.yml        ← standalone: service + redis
└── .env.example
```

#### soca-deploy/ (orchestration)

```
soca-deploy/
├── docker-compose.yml        ← full-stack (all 3 repos built from ../sibling-dirs)
└── .env.example
```

---

### Splitting the Git Repos

If you're starting from the original monorepo (`soca-client-server/`), run this
once to split into separate repos:

```bash
# 1. Create the soca-edge repo (contains soca-engine + soca-dashboard + docker/)
mkdir ~/projects/soca-edge
cp -r soca-client-server/soca-engine   ~/projects/soca-edge/
cp -r soca-client-server/soca-dashboard ~/projects/soca-edge/
cp -r soca-client-server/docker         ~/projects/soca-edge/
cp    soca-client-server/docker-compose.yml  ~/projects/soca-edge/
cp    soca-client-server/.env.example        ~/projects/soca-edge/

cd ~/projects/soca-edge && git init && git add . && git commit -m "init: soca-edge repo"

# 2. Promote soca-control to its own repo root
cp -r soca-client-server/soca-control ~/projects/soca-control
cd ~/projects/soca-control && git init && git add . && git commit -m "init: soca-control repo"

# 3. Promote soca-service to its own repo root
cp -r soca-client-server/soca-service ~/projects/soca-service
cd ~/projects/soca-service && git init && git add . && git commit -m "init: soca-service repo"

# 4. Create soca-deploy repo
cp -r soca-client-server/soca-deploy ~/projects/soca-deploy
cd ~/projects/soca-deploy && git init && git add . && git commit -m "init: soca-deploy repo"
```

After splitting, push each repo to your Git hosting (GitHub/GitLab):

```bash
cd ~/projects/soca-edge
git remote add origin git@github.com:your-org/soca-edge.git
git push -u origin main

# Repeat for soca-control, soca-service, soca-deploy
```

---

### Full-Stack Deployment (soca-deploy)

Use `soca-deploy/` to run all services together on one server.

#### 1. Clone all repos side by side

```bash
mkdir ~/projects && cd ~/projects
git clone git@github.com:your-org/soca-edge.git
git clone git@github.com:your-org/soca-control.git
git clone git@github.com:your-org/soca-service.git
git clone git@github.com:your-org/soca-deploy.git
```

#### 2. Configure environment

Each repo ships its own `.env.example` — copy it to `.env` inside that repo:

| Repo | `.env.example` covers |
|------|-----------------------|
| `soca-deploy/` | All services (edge + control + service) — **use this one** |
| `soca-edge/` | Edge variables only (engine + dashboard) |
| `soca-control/` | soca-control variables only |
| `soca-service/` | soca-service variables only |

For full-stack deployment, you only need one `.env` in `soca-deploy/`:

```bash
cd ~/projects/soca-deploy
cp .env.example .env
nano .env    # fill in all required values
```

Required values:

| Variable | Description | Generate with |
|----------|-------------|---------------|
| `EDGE_NAME` | Unique edge identifier | — |
| `ENGINE_API_KEY` | soca-engine API key | `python -c "import secrets; print(secrets.token_hex(16))"` |
| `DASHBOARD_SECRET_KEY` | Django secret (soca-dashboard) | `python -c "import secrets; print(secrets.token_urlsafe(50))"` |
| `CONTROL_SECRET_KEY` | Django secret (soca-control) | same as above |
| `SOCA_CONTROL_INGEST_KEY` | Shared secret (control ↔ service) | `python -c "import secrets; print(secrets.token_hex(16))"` |

#### 3. Build and start

```bash
cd ~/projects/soca-deploy

docker compose build
docker compose up -d

# Verify all containers are running
docker compose ps
```

Expected:
```
NAME             STATUS    PORTS
soca-edge        Up        0.0.0.0:8001->8001, 0.0.0.0:8080->8080
soca-control     Up        0.0.0.0:8000->8000
soca-service     Up        0.0.0.0:8010->8010
soca-redis       Up        0.0.0.0:6379->6379
```

#### 4. Create admin accounts

```bash
# soca-dashboard admin
docker exec -it soca-edge bash -c "cd /app/soca-dashboard && python manage.py createsuperuser"

# soca-control admin
docker exec -it soca-control python manage.py createsuperuser
```

#### 5. Verify health

```bash
curl http://localhost:8001/health    # soca-engine
curl http://localhost:8010/health    # soca-service
curl -I http://localhost:8080/       # soca-dashboard
curl -I http://localhost:8000/       # soca-control
```

---

### Standalone Deployment (per-repo)

Each repo can also be deployed independently. Useful for running only one service,
or when soca-control and soca-edge are on different machines.

#### soca-edge standalone

Uses `soca-edge/.env.example` (edge variables only: EDGE_NAME, ENGINE_API_KEY,
PUBLISHER_TYPE, GCS settings, inference settings, DASHBOARD_SECRET_KEY).

```bash
cd ~/projects/soca-edge
cp .env.example .env    # source: soca-edge/.env.example
nano .env
docker compose build
docker compose up -d
```

Starts: soca-edge (8080, 8001) + redis (6379)

#### soca-control standalone

Uses `soca-control/.env.example` (Django secret, DATABASE_URL, ALLOWED_HOSTS, etc.).

```bash
cd ~/projects/soca-control
cp .env.example .env    # source: soca-control/.env.example
nano .env
docker compose build
docker compose up -d
```

Starts: soca-control (8000) only. Set `SOCA_CONTROL_URL` in soca-service .env to
point at this server's IP/domain.

#### soca-service standalone

Uses `soca-service/.env.example` (SOCA_CONTROL_URL, SOCA_CONTROL_INGEST_KEY).

```bash
cd ~/projects/soca-service
cp .env.example .env    # source: soca-service/.env.example
# Set SOCA_CONTROL_URL to your soca-control address, e.g.:
# SOCA_CONTROL_URL=http://your-server-ip:8000
nano .env
docker compose build
docker compose up -d
```

Starts: soca-service (8010) + redis (6379)

---

### Post-Deployment Configuration

#### Configure soca-dashboard Edge Settings

1. Open `http://<server>:8080` → log in
2. Go to **Settings → Edge**
3. Set these paths (inside the container):

| Field | Value |
|-------|-------|
| Engine URL | `http://localhost:8001` |
| Engine API Key | your `ENGINE_API_KEY` |
| Engine DB path | `/app/data/soca_engine.db` |
| Snapshots dir | `/app/soca-engine/snapshots` |
| Engine .env path | `/app/soca-engine/.env` |

#### Configure soca-control

1. Open `http://<server>:8000` → log in
2. Go to **Settings → Edges** → Add edge with:

| Field | Value |
|-------|-------|
| Edge URL | `http://soca-edge:8080` (Docker network) |
| Engine URL | `http://soca-edge:8001` (Docker network) |
| Engine API Key | your `ENGINE_API_KEY` |

3. Go to **Settings → Service Ingest Key** → click **Push to soca-service**
   (this sends `SOCA_CONTROL_INGEST_KEY` to soca-service via bootstrap API)

---

### Upload YOLO Models

Models persist in the `edge-models` Docker volume (`/app/soca-engine/yolo/`).

**Via soca-dashboard UI:**
→ `http://<server>:8080` → **Settings → AI Models** → Upload `.pt`, `.onnx`, `.yaml`

**Via CLI (copy from host):**
```bash
docker cp your-model.pt soca-edge:/app/soca-engine/yolo/
```

---

### Environment Variables Reference

#### soca-edge

| Variable | Default | Description |
|----------|---------|-------------|
| `EDGE_NAME` | *(required)* | Unique edge identifier |
| `ENGINE_API_KEY` | *(required)* | soca-engine API key |
| `REDIS_URL` | `redis://redis:6379` | Set automatically in compose |
| `PUBLISHER_TYPE` | `redis` | `redis` or `pubsub` |
| `GCS_BUCKET` | — | GCS bucket (blank = no upload) |
| `GCS_KEY_PATH` | — | GCS service account JSON path |
| `PUBSUB_PROJECT_ID` | — | GCP project (Pub/Sub only) |
| `PUBSUB_TOPIC` | `soca-detections` | Pub/Sub topic |
| `INFER_DEVICE` | `cpu` | `cpu` or `cuda:0` |
| `MAX_CONCURRENT_JOBS` | `4` | Parallel inference jobs |
| `DB_PATH` | `/app/data/soca_engine.db` | Engine SQLite path |
| `SECRET_KEY` | *(required)* | Django secret (soca-dashboard) |
| `DEBUG` | `false` | Django debug mode |

#### soca-control

| Variable | Default | Description |
|----------|---------|-------------|
| `DJANGO_SECRET_KEY` | *(required)* | Django secret key |
| `DEBUG` | `false` | Django debug mode |
| `DATABASE_URL` | SQLite | PostgreSQL URL (production) |
| `CSRF_TRUSTED_ORIGINS` | `http://localhost:8000` | CSRF trusted origins |

#### soca-service

| Variable | Default | Description |
|----------|---------|-------------|
| `SOCA_CONTROL_URL` | `http://soca-control:8000` | Auto-set in compose |
| `SOCA_CONTROL_INGEST_KEY` | *(required)* | Shared auth secret |
| `REDIS_URL` | `redis://redis:6379` | Auto-set in compose |

---

### Volumes

| Volume | Mount point | Contents |
|--------|-------------|----------|
| `edge-models` | `/app/soca-engine/yolo` | YOLO models — **persistent** |
| `edge-snapshots` | `/app/soca-engine/snapshots` | Detection snapshots |
| `edge-dlq` | `/app/soca-engine/dlq` | Failed upload queue |
| `edge-data` | `/app/data` | Engine SQLite DB, config.json |
| `redis-data` | `/data` | Redis persistence |
| `control-db` | `/app/db` | soca-control SQLite (dev only) |

---

### Networking

All containers share `soca-net`. Internal Docker hostnames:

| Container | Hostname | Ports |
|-----------|----------|-------|
| soca-edge | `soca-edge` | 8001, 8080 |
| soca-control | `soca-control` | 8000 |
| soca-service | `soca-service` | 8010 |
| Redis | `redis` | 6379 |

Cross-container calls:
- soca-dashboard → soca-engine: `http://localhost:8001` (same container)
- soca-control → soca-engine: `http://soca-edge:8001`
- soca-service → soca-control: `http://soca-control:8000`

#### Firewall Recommendations

| Port | Expose publicly? |
|------|-----------------|
| 8080 (soca-dashboard) | Yes — edge operators |
| 8000 (soca-control) | Yes — fleet admins |
| 8001 (soca-engine API) | Internal only |
| 8010 (soca-service) | Internal only |
| 6379 (Redis) | Internal only |

---

### Updating Services

```bash
cd ~/projects/soca-deploy

# Pull latest code for each service
cd ../soca-edge     && git pull && cd ../soca-deploy
cd ../soca-control  && git pull && cd ../soca-deploy
cd ../soca-service  && git pull && cd ../soca-deploy

# Rebuild changed images and restart (zero-downtime for unchanged services)
docker compose build
docker compose up -d

# Run migrations after soca-control update
docker exec soca-control python manage.py migrate --noinput

# Run migrations after soca-dashboard update
docker exec soca-edge bash -c "cd /app/soca-dashboard && python manage.py migrate --noinput"
```

---

### GPU Support (Optional)

To enable GPU inference on soca-edge, add to `soca-deploy/docker-compose.yml`
under the `soca-edge` service:

```yaml
soca-edge:
  deploy:
    resources:
      reservations:
        devices:
          - driver: nvidia
            count: 1
            capabilities: [gpu]
  environment:
    INFER_DEVICE: cuda:0
```

Requires [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) on the host.

---

### Useful Commands

```bash
# View logs (all services)
docker compose logs -f

# View logs for a specific service
docker compose logs -f soca-edge
docker compose logs -f soca-control

# View process logs inside soca-edge
docker exec soca-edge tail -f /var/log/supervisor/soca-engine.log
docker exec soca-edge tail -f /var/log/supervisor/soca-dashboard.log

# Restart a single service
docker compose restart soca-edge

# Rebuild only one service (after code change)
docker compose build soca-control
docker compose up -d --no-deps soca-control

# Open a shell
docker exec -it soca-edge bash
docker exec -it soca-control bash

# Django management
docker exec -it soca-control python manage.py shell
docker exec -it soca-edge bash -c "cd /app/soca-dashboard && python manage.py shell"

# Back up the engine SQLite database
docker run --rm \
  -v soca-deploy_edge-data:/data \
  -v $(pwd):/backup alpine \
  cp /data/soca_engine.db /backup/soca_engine_$(date +%Y%m%d).db
```

---

### Troubleshooting

#### Container exits immediately

```bash
docker compose logs soca-edge    # check error output
```

Common causes:
- `EDGE_NAME` not set → engine raises `RuntimeError`
- `ENGINE_API_KEY` not set → engine starts but returns 503 on API calls
- `DASHBOARD_SECRET_KEY` or `CONTROL_SECRET_KEY` not set → Django refuses to start

#### soca-control CSRF / 403 errors

Add your domain to `.env`:
```env
CSRF_TRUSTED_ORIGINS=https://yourdomain.com,http://localhost:8000
```
Then `docker compose up -d` to apply.

#### Models not showing in AI Models tab

1. Verify `ENGINE_API_KEY` matches between soca-dashboard settings and `.env`
2. Test directly: `curl -H "Authorization: Bearer <key>" http://localhost:8001/models`
3. Restart soca-edge after any engine code change: `docker compose restart soca-edge`

#### soca-service not consuming alerts

1. Ensure `SOCA_CONTROL_INGEST_KEY` is set and matches soca-control
2. Push the key: soca-control UI → **Settings → Service Ingest Key → Push**
3. Check: `docker compose logs soca-service`

#### Build fails (missing library)

soca-edge requires system libraries for OpenCV. If the build fails:
```bash
docker compose build --no-cache soca-edge
```

### Bare Metal RHEL

_Source: `deployment/bare-metal-rhel-guide.md`_

## SOCA Platform — Bare-Metal Deployment Guide (RHEL / Rocky Linux)

This guide covers deploying soca-engine, soca-dashboard, soca-control, and
soca-service directly on a RHEL (or Rocky Linux) server without Docker, using
Python virtualenvs and systemd services.

---

### Architecture on a Single Server

```
Internet / Browser
        │  port 80 (nginx)
        ▼
     nginx  ──► soca-control   (gunicorn, port 8000)
                soca-dashboard (gunicorn, port 8000 on separate vhost or path)
                soca-engine    (uvicorn,  port 8001)
                soca-service   (uvicorn,  port 8080)
```

---

### Prerequisites

```bash
sudo dnf install python3 python3-pip nginx git -y
sudo pip3 install virtualenv
```

Create a shared virtualenv (all services share one to save disk space):

```bash
python3 -m venv /home/ca-admin/Apps/venv
source /home/ca-admin/Apps/venv/bin/activate
```

---

### Directory Layout

```
/home/ca-admin/Apps/
├── venv/               ← shared Python virtualenv
├── soca-control/       ← git clone of soca-control repo
├── soca-service/       ← git clone of soca-service repo
├── soca-engine/        ← git clone / copy of soca-engine
└── soca-dashboard/     ← git clone / copy of soca-dashboard
```

---

### 1. soca-control

#### Install

```bash
cd /home/ca-admin/Apps/soca-control
source /home/ca-admin/Apps/venv/bin/activate
pip install -r requirements.txt
```

#### Configure

Edit `db.conf` (soca-control uses this instead of `.env`):

```json
{
  "url": "",
  "pg_host": "localhost",
  "pg_port": "5432",
  "pg_name": "myappdb",
  "pg_user": "postgres",
  "pg_password": "yourpassword",
  "gac_path": "/home/ca-admin/Apps/soca-control/credentials/gcs.json",
  "ingest_key": "<generate with: openssl rand -hex 32>",
  "soca_service_url": "http://127.0.0.1:8080",
  "gcs_key_path": "/home/ca-admin/Apps/soca-control/credentials/gcs.json",
  "pubsub_key_path": "/home/ca-admin/Apps/soca-control/credentials/pubsub.json",
  "pubsub_project_id": "<GCP_PROJECT>",
  "pubsub_subscription": "<SUBSCRIPTION_NAME>"
}
```

> Leave `"url": ""` to use SQLite. Set a PostgreSQL URL for production.

#### Create `.env`

```bash
cat > /home/ca-admin/Apps/soca-control/.env << 'EOF'
DJANGO_SECRET_KEY=<generate with: python3 -c "import secrets; print(secrets.token_urlsafe(50))">
DEBUG=false
ALLOWED_HOSTS=localhost,127.0.0.1,<SERVER_IP>
CSRF_TRUSTED_ORIGINS=http://<SERVER_IP>
DATABASE_URL=
GCS_BUCKET=<your-bucket>
EOF
```

#### Migrate and collect static

```bash
cd /home/ca-admin/Apps/soca-control
source /home/ca-admin/Apps/venv/bin/activate
python manage.py migrate
python manage.py collectstatic --noinput
python manage.py createsuperuser
```

#### systemd service

`/etc/systemd/system/soca-control.service`:

```ini
[Unit]
Description=SOCA Control
After=network.target

[Service]
User=ca-admin
WorkingDirectory=/home/ca-admin/Apps/soca-control
Environment=PATH=/home/ca-admin/Apps/venv/bin:/usr/bin:/bin
Environment=HTTPS_PROXY=http://your-proxy:8080
Environment=HTTP_PROXY=http://your-proxy:8080
Environment=NO_PROXY=localhost,127.0.0.1,<SERVER_IP>
ExecStart=/home/ca-admin/Apps/venv/bin/gunicorn soca_control.wsgi:application \
    --bind 127.0.0.1:8000 --workers 3
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now soca-control
```

---

### 2. nginx Reverse Proxy

Install and configure nginx to expose soca-control on port 80:

`/etc/nginx/conf.d/soca-control.conf`:

```nginx
server {
    listen 80;
    server_name _;

    location /static/ {
        alias /home/ca-admin/Apps/soca-control/staticfiles/;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $server_addr;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

> **Important:** Use `proxy_set_header Host $server_addr` (not `$host`) to prevent
> Django `DisallowedHost` errors when browsers send requests with a hostname prefix.

```bash
sudo nginx -t
sudo systemctl enable --now nginx
```

---

### 3. soca-service

#### Install

```bash
cd /home/ca-admin/Apps/soca-service
source /home/ca-admin/Apps/venv/bin/activate
pip install -r requirements.txt
```

#### Configure `.env`

```env
SOCA_CONTROL_URL=http://127.0.0.1:8000
SOCA_CONTROL_INGEST_KEY=<same value as ingest_key in soca-control db.conf>
PORT=8080
GOOGLE_APPLICATION_CREDENTIALS=/home/ca-admin/Apps/soca-service/credentials/pubsub.json
LOG_LEVEL=INFO
```

> **Note:** Use `http://127.0.0.1:8000` (not the server's external IP) for
> `SOCA_CONTROL_URL`. gunicorn binds to `127.0.0.1` only — external IP won't work.

#### Credentials

```bash
mkdir -p /home/ca-admin/Apps/soca-service/credentials
# Copy GCP service account JSON files from your local machine:
# scp pubsub.json ca-admin@<SERVER_IP>:/home/ca-admin/Apps/soca-service/credentials/
```

#### systemd service

`/etc/systemd/system/soca-service.service`:

```ini
[Unit]
Description=SOCA Service
After=network.target

[Service]
User=ca-admin
WorkingDirectory=/home/ca-admin/Apps/soca-service
Environment=PATH=/home/ca-admin/Apps/venv/bin:/usr/bin:/bin
Environment=HTTPS_PROXY=http://your-proxy:8080
Environment=HTTP_PROXY=http://your-proxy:8080
Environment=NO_PROXY=localhost,127.0.0.1,<SERVER_IP>
ExecStart=/home/ca-admin/Apps/venv/bin/python -m uvicorn main:app \
    --host 0.0.0.0 --port 8080
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now soca-service
```

---

### 4. soca-engine

#### Install

```bash
cd /home/ca-admin/Apps/soca-engine
source /home/ca-admin/Apps/venv/bin/activate
pip install -r requirements.txt
```

#### Configure `.env`

```env
EDGE_NAME=edge-1
ENGINE_API_KEY=<generate with: python3 -c "import secrets; print(secrets.token_hex(16))">
PUBLISHER_TYPE=pubsub
PUBSUB_PROJECT_ID=<GCP_PROJECT>
PUBSUB_TOPIC=<TOPIC_NAME>
GOOGLE_APPLICATION_CREDENTIALS=/home/ca-admin/Apps/soca-engine/credentials/pubsub.json
GCS_BUCKET=<your-bucket>
GCS_KEY_PATH=/home/ca-admin/Apps/soca-engine/credentials/gcs.json
GCS_PATH_PREFIX=edge-1
INFER_DEVICE=cpu
```

#### systemd service

`/etc/systemd/system/soca-engine.service`:

```ini
[Unit]
Description=SOCA Engine
After=network.target

[Service]
User=ca-admin
WorkingDirectory=/home/ca-admin/Apps/soca-engine
Environment=PATH=/home/ca-admin/Apps/venv/bin:/usr/bin:/bin
Environment=HTTPS_PROXY=http://your-proxy:8080
Environment=HTTP_PROXY=http://your-proxy:8080
Environment=NO_PROXY=localhost,127.0.0.1,<SERVER_IP>
ExecStart=/home/ca-admin/Apps/venv/bin/python -m uvicorn main:app \
    --host 0.0.0.0 --port 8001
Restart=always

[Install]
WantedBy=multi-user.target
```

> **Note:** If soca-engine requires MediaMTX (RTSP relay), start it separately before
> uvicorn. Use a wrapper script:
> ```bash
> # /home/ca-admin/Apps/soca-engine/start.sh
> #!/bin/bash
> cd /home/ca-admin/Apps/soca-engine
> ./mediamtx &
> exec /home/ca-admin/Apps/venv/bin/python -m uvicorn main:app --host 0.0.0.0 --port 8001
> ```
> Then in systemd: `ExecStart=/bin/bash /home/ca-admin/Apps/soca-engine/start.sh`

---

### 5. soca-dashboard

#### Install

```bash
cd /home/ca-admin/Apps/soca-dashboard
source /home/ca-admin/Apps/venv/bin/activate
pip install -r requirements.txt gunicorn
python manage.py migrate
python manage.py collectstatic --noinput
```

#### systemd service

`/etc/systemd/system/soca-dashboard.service`:

```ini
[Unit]
Description=SOCA Dashboard
After=network.target

[Service]
User=ca-admin
WorkingDirectory=/home/ca-admin/Apps/soca-dashboard
Environment=PATH=/home/ca-admin/Apps/venv/bin:/usr/bin:/bin
Environment=HTTPS_PROXY=http://your-proxy:8080
Environment=HTTP_PROXY=http://your-proxy:8080
Environment=NO_PROXY=localhost,127.0.0.1,<SERVER_IP>
ExecStart=/home/ca-admin/Apps/venv/bin/gunicorn soca_dashboard.wsgi:application \
    --bind 0.0.0.0:8000 --workers 3
Restart=always

[Install]
WantedBy=multi-user.target
```

---

### Firewall

Open required ports:

```bash
sudo firewall-cmd --permanent --add-port=80/tcp      # nginx (soca-control)
sudo firewall-cmd --permanent --add-port=8080/tcp    # soca-dashboard (if on separate port)
sudo firewall-cmd --permanent --add-port=8001/tcp    # soca-engine API (if externally accessible)
sudo firewall-cmd --reload
```

---

### Troubleshooting

#### Port 80 access denied

Linux restricts ports < 1024 to root. Use nginx as a reverse proxy (port 80 → 8000)
instead of binding gunicorn directly to port 80.

#### systemd ModuleNotFoundError

The venv is not activated in systemd. Set `Environment=PATH` explicitly:
```ini
Environment=PATH=/home/ca-admin/Apps/venv/bin:/usr/bin:/bin
```
And use the full venv path in `ExecStart`:
```ini
ExecStart=/home/ca-admin/Apps/venv/bin/gunicorn ...
```

#### DisallowedHost error

nginx is forwarding the Host header with a backslash prefix. Fix with:
```nginx
proxy_set_header Host $server_addr;
```

#### soca-service connection refused to soca-control

gunicorn binds to `127.0.0.1:8000` (loopback only) when behind nginx.
Use `SOCA_CONTROL_URL=http://127.0.0.1:8000` — not the server's external IP.

#### Pub/Sub messages not received (HTTP proxy environment)

gRPC streaming pull does not respect `HTTPS_PROXY`. soca-service uses REST polling
by default which does work through HTTP proxies. Ensure `HTTPS_PROXY` is set in the
systemd service and `NO_PROXY` includes `localhost,127.0.0.1`.

#### GCS credentials pointing to wrong path

soca-control reads credential paths from `db.conf`. After copying credentials to the
server, update `gcs_key_path` and `pubsub_key_path` in `db.conf` to the server paths.
Do not copy `db.conf` from a development machine without updating these paths.

#### Verifying services

```bash
# Check all soca services
sudo systemctl status soca-control soca-service soca-engine soca-dashboard

# View live logs
sudo journalctl -u soca-control -f
sudo journalctl -u soca-service -f
sudo journalctl -u soca-engine -f

# Test endpoints
curl http://127.0.0.1:8000          # soca-control (redirect to /login/)
curl http://127.0.0.1:8080/health   # soca-service
curl http://127.0.0.1:8001/health   # soca-engine

# Check soca-service pipeline status
curl http://127.0.0.1:8080/health | python3 -m json.tool
```

### GCP Cloud Run

_Source: `deployment/gcp-cloud-run-guide.md`_

## soca-control — GCP Cloud Run Deployment Guide

### Architecture

#### Option A — Redis Stream transport (default)

```
                    ┌────────────────────────────────────────────────────┐
                    │                  Google Cloud                       │
                    │                                                     │
  Browser ──HTTPS──►│  Cloud Run: soca-control-web                        │
                    │  (gunicorn, min 1 instance)                         │
                    │           │ Cloud SQL Auth Proxy                    │
                    │           ▼                                         │
                    │  Cloud SQL (PostgreSQL)                              │
                    │           ▲                                         │
                    │           │ Cloud SQL Auth Proxy                    │
                    │  Cloud Run: soca-control-worker                     │
                    │  (manage.py consume_streams)                        │
                    │           │ VPC connector                           │
                    │           ▼                                         │
                    │  Cloud Memorystore (Redis 7, private IP)            │
                    │                                                     │
                    │  Compute Engine: redis-proxy (e2-micro, public IP)  │
                    │           │ rinetd 0.0.0.0:6379 → Memorystore:6379  │
                    └───────────┼────────────────────────────────────────┘
                                ▲
                                │ each edge pushes detection events
                                │ redis://PROXY_PUBLIC_IP:6379
                    On-premises edge devices
                    soca-engine → XADD → edge-NAME:soca:detections
```

#### Option B — Google Pub/Sub transport

```
                    ┌────────────────────────────────────────────────────┐
                    │                  Google Cloud                       │
                    │                                                     │
  Browser ──HTTPS──►│  Cloud Run: soca-control-web                        │
                    │           │ Cloud SQL Auth Proxy                    │
                    │           ▼                                         │
                    │  Cloud SQL (PostgreSQL)                              │
                    │           ▲                                         │
                    │           │ Cloud SQL Auth Proxy                    │
                    │  Cloud Run: soca-control-pubsub-worker              │
                    │  (manage.py consume_pubsub)                         │
                    │           │ streaming pull                          │
                    │           ▼                                         │
                    │  Cloud Pub/Sub topic: soca-detections               │
                    │           ▲                                         │
                    └───────────┼────────────────────────────────────────┘
                                │ each edge publishes detection events
                                │ (GOOGLE_APPLICATION_CREDENTIALS or ADC)
                    On-premises edge devices
                    soca-engine → Pub/Sub publish → projects/PROJECT/topics/soca-detections
```

**Pub/Sub advantages over Redis:** no proxy VM required, no VPC connector needed, fully managed delivery, dead-letter support, scales to any number of edges.

**Two Cloud Run services, one Docker image (Redis path):**
- `soca-control-web` — HTTP server (gunicorn via `start.sh`)
- `soca-control-worker` — always-on stream consumer (`manage.py consume_streams`)

**Three Cloud Run services for Pub/Sub path:**
- `soca-control-web` — HTTP server
- `soca-control-pubsub-worker` — Pub/Sub subscriber (`manage.py consume_pubsub`)
- Both workers can run simultaneously if you have a mix of Redis and Pub/Sub edges.

**Stream naming (Redis):** All edges share the same Redis (Memorystore) but use unique stream names so `consume_streams` can distinguish them:

| Edge | `redis_url` | `redis_stream` |
|---|---|---|
| edge-jakarta-01 | `redis://MEMORYSTORE_IP:6379` | `edge-jakarta-01:soca:detections` |
| edge-bali-02 | `redis://MEMORYSTORE_IP:6379` | `edge-bali-02:soca:detections` |

---

### Prerequisites

- `gcloud` CLI installed and authenticated (`gcloud auth login`)
- GCP project created with billing enabled
- Docker installed locally (for the first manual build)

---

### Step 1 — Set project and enable APIs

```bash
gcloud config set project YOUR_PROJECT_ID

gcloud services enable \
  run.googleapis.com \
  sqladmin.googleapis.com \
  artifactregistry.googleapis.com \
  cloudbuild.googleapis.com \
  secretmanager.googleapis.com \
  storage.googleapis.com \
  redis.googleapis.com \
  vpcaccess.googleapis.com \
  compute.googleapis.com
```

---

### Step 2 — Create VPC and Serverless VPC connector

Cloud Memorystore only has a private VPC IP. Cloud Run needs a VPC connector to reach it.

```bash
# Create a VPC (skip if you want to reuse the default VPC)
gcloud compute networks create soca-vpc --subnet-mode=auto

# Create the connector (Cloud Run ↔ VPC)
gcloud compute networks vpc-access connectors create soca-connector \
  --region=asia-southeast2 \
  --network=soca-vpc \
  --range=10.8.0.0/28 \
  --min-instances=2 \
  --max-instances=3
```

---

### Step 3 — Create Cloud Memorystore (Redis)

```bash
gcloud redis instances create soca-redis \
  --size=1 \
  --region=asia-southeast1 \
  --network=soca-vpc \
  --redis-version=redis_7_0 \
  --tier=basic
```

> **basic** tier is single-node (cheapest). Use `--tier=standard` for HA with automatic failover.

Get the private IP — you'll need it later:

```bash
gcloud redis instances describe soca-redis \
  --region=asia-southeast2 \
  --format="value(host)" 
# get IP 10.132.156.123
# e.g. 10.121.0.3  →  save this as MEMORYSTORE_IP
```

---

### Step 4 — Expose Redis to edge devices (proxy VM)

Memorystore has no public IP. Run a tiny Compute Engine VM as a TCP proxy so on-premises edge devices can push their detection events to it.

```bash
# Create e2-micro VM in the same VPC (free tier eligible)
gcloud compute instances create redis-proxy \
  --zone=asia-southeast1-b \
  --machine-type=e2-micro \
  --network=soca-vpc \
  --subnet=soca-vpc \
  --tags=redis-proxy \
  --image-family=debian-12 \
  --image-project=debian-cloud

# Allow port 6379 only from your edge device public IPs
gcloud compute firewall-rules create allow-redis-from-edges \
  --network=soca-vpc \
  --allow=tcp:6379 \
  --source-ranges=EDGE_PUBLIC_IP_1/32,EDGE_PUBLIC_IP_2/32 \
  --target-tags=redis-proxy
```

SSH into the VM and install `rinetd` as a TCP forwarder:

```bash
gcloud compute ssh redis-proxy --zone=asia-southeast1-b

# On the VM:
sudo apt-get update && sudo apt-get install -y rinetd
echo "0.0.0.0 6379 MEMORYSTORE_IP 6379" | sudo tee /etc/rinetd.conf
sudo systemctl enable rinetd && sudo systemctl restart rinetd
exit
```

Get the proxy VM's external IP:

```bash
gcloud compute instances describe redis-proxy \
  --zone=asia-southeast1-b \
  --format="value(networkInterfaces[0].accessConfigs[0].natIP)"
# e.g. 34.101.x.x  →  save as PROXY_PUBLIC_IP
```

Edge devices will connect to `redis://PROXY_PUBLIC_IP:6379`.

---

### Step 5 — Create Artifact Registry repository

```bash
gcloud artifacts repositories create soca \
  --repository-format=docker \
  --location=asia-southeast1 \
  --description="SOCA container images"
```

---

### Step 6 — Create Cloud SQL (PostgreSQL)

```bash
gcloud sql instances create soca-control-db \
  --database-version=POSTGRES_16 \
  --tier=db-f1-micro \
  --region=asia-southeast1 \
  --storage-auto-increase \
  --backup-start-time=02:00

gcloud sql databases create soca_control --instance=soca-control-db

gcloud sql users create soca_user \
  --instance=soca-control-db \
  --password=CHOOSE_A_STRONG_PASSWORD
```

Connection string (save for Step 7):
```
postgres://soca_user:CHOOSE_A_STRONG_PASSWORD@/soca_control?host=/cloudsql/YOUR_PROJECT_ID:asia-southeast1:soca-control-db
```

---

### Step 7 — Create GCS bucket for media (logos)

```bash
gcloud storage buckets create gs://soca-control-media-YOUR_PROJECT_ID \
  --location=asia-southeast1 \
  --uniform-bucket-level-access

# Allow public read so logo images are accessible in the browser
gcloud storage buckets add-iam-policy-binding \
  gs://soca-control-media-YOUR_PROJECT_ID \
  --member=allUsers \
  --role=roles/storage.objectViewer
```

---

### Step 8 — Store secrets in Secret Manager

```bash
# Generate a strong random Django secret key
echo -n "$(python3 -c "import secrets; print(secrets.token_urlsafe(50))")" | \
  gcloud secrets create soca-control-secret-key --data-file=-

# Database URL (Cloud SQL socket format)
echo -n "postgres://soca_user:CHOOSE_A_STRONG_PASSWORD@/soca_control?host=/cloudsql/YOUR_PROJECT_ID:asia-southeast1:soca-control-db" | \
  gcloud secrets create soca-control-db-url --data-file=-
```

---

### Step 9 — Grant service account permissions

```bash
SA="$(gcloud projects describe YOUR_PROJECT_ID --format='value(projectNumber)')-compute@developer.gserviceaccount.com"

# Cloud SQL
gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
  --member="serviceAccount:$SA" --role=roles/cloudsql.client

# Secret Manager
gcloud secrets add-iam-policy-binding soca-control-secret-key \
  --member="serviceAccount:$SA" --role=roles/secretmanager.secretAccessor
gcloud secrets add-iam-policy-binding soca-control-db-url \
  --member="serviceAccount:$SA" --role=roles/secretmanager.secretAccessor

# GCS
gcloud storage buckets add-iam-policy-binding \
  gs://soca-control-media-YOUR_PROJECT_ID \
  --member="serviceAccount:$SA" --role=roles/storage.objectAdmin
```

---

### Step 9b — (Option B) Set up Google Pub/Sub

Skip this step if you are using Redis transport only.

#### Create topic and subscription

```bash
# Create the topic soca-engine publishes to
gcloud pubsub topics create soca-detections --project=YOUR_PROJECT_ID

# Create a pull subscription for soca-control-pubsub-worker
gcloud pubsub subscriptions create soca-detections-sub \
  --topic=soca-detections \
  --project=YOUR_PROJECT_ID \
  --ack-deadline=60 \
  --message-retention-duration=7d
```

> Use one topic per project. Multiple edges publish to the same topic; the `edge_name` field inside each message identifies the source.

#### Grant IAM permissions

```bash
SA="$(gcloud projects describe YOUR_PROJECT_ID --format='value(projectNumber)')-compute@developer.gserviceaccount.com"

# soca-control-pubsub-worker needs to subscribe (pull + ack)
gcloud pubsub subscriptions add-iam-policy-binding soca-detections-sub \
  --member="serviceAccount:$SA" --role=roles/pubsub.subscriber

# soca-engine on each edge device needs to publish
# (use a separate service account for edge devices in production)
gcloud pubsub topics add-iam-policy-binding soca-detections \
  --member="serviceAccount:$SA" --role=roles/pubsub.publisher
```

#### Configure soca-engine on each edge device

Set these env vars in `soca-engine/.env` (or via soca-dashboard Settings → Edge Config → Publisher Transport):

```env
PUBLISHER_TYPE=pubsub
PUBSUB_PROJECT_ID=YOUR_PROJECT_ID
PUBSUB_TOPIC=soca-detections
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json
```

#### Configure soca-control edges

In soca-control Settings → Edit Edge, set:
- **Subscriber Transport**: Google Pub/Sub
- **GCP Project ID**: `YOUR_PROJECT_ID`
- **Pub/Sub Subscription**: `soca-detections-sub`

---

### Step 10 — Build and push Docker image

```bash
gcloud auth configure-docker asia-southeast1-docker.pkg.dev

docker build -t asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest .
docker push asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest
```

---

### Step 11 — Deploy Cloud Run services

#### Web service

The web service only connects to Cloud SQL and GCS. It does **not** talk to Redis, so no VPC connector is needed here.

```bash
gcloud run deploy soca-control-web \
  --image=asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest \
  --region=asia-southeast1 \
  --platform=managed \
  --allow-unauthenticated \
  --min-instances=1 \
  --max-instances=4 \
  --command="./start.sh" \
  --update-secrets="DJANGO_SECRET_KEY=soca-control-secret-key:latest,DATABASE_URL=soca-control-db-url:latest" \
  --set-env-vars="GCS_BUCKET=soca-control-media-YOUR_PROJECT_ID,ALLOWED_HOSTS=YOUR_SERVICE_URL_HOST,CSRF_TRUSTED_ORIGINS=https://YOUR_SERVICE_URL" \
  --add-cloudsql-instances=YOUR_PROJECT_ID:asia-southeast1:soca-control-db
```

After the first deploy you'll get the service URL (e.g. `https://soca-control-web-xxxx-as.a.run.app`).
Update `ALLOWED_HOSTS` and `CSRF_TRUSTED_ORIGINS` with that URL and redeploy once.

#### Worker service

```bash
gcloud run deploy soca-control-worker \
  --image=asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest \
  --region=asia-southeast1 \
  --platform=managed \
  --no-allow-unauthenticated \
  --min-instances=1 \
  --max-instances=1 \
  --command="python" \
  --args="manage.py,consume_streams" \
  --vpc-connector=soca-connector \
  --vpc-egress=private-ranges-only \
  --update-secrets="DJANGO_SECRET_KEY=soca-control-secret-key:latest,DATABASE_URL=soca-control-db-url:latest" \
  --set-env-vars="GCS_BUCKET=soca-control-media-YOUR_PROJECT_ID" \
  --add-cloudsql-instances=YOUR_PROJECT_ID:asia-southeast1:soca-control-db
```

> `--min-instances=1` is required on the worker so it always runs and never cold-starts. Cloud Run will keep one instance alive continuously.

#### Pub/Sub worker service (Option B)

No VPC connector is needed — Pub/Sub is a public GCP endpoint.

```bash
gcloud run deploy soca-control-pubsub-worker \
  --image=asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest \
  --region=asia-southeast1 \
  --platform=managed \
  --no-allow-unauthenticated \
  --min-instances=1 \
  --max-instances=1 \
  --command="python" \
  --args="manage.py,consume_pubsub" \
  --update-secrets="DJANGO_SECRET_KEY=soca-control-secret-key:latest,DATABASE_URL=soca-control-db-url:latest" \
  --set-env-vars="GCS_BUCKET=soca-control-media-YOUR_PROJECT_ID" \
  --add-cloudsql-instances=YOUR_PROJECT_ID:asia-southeast1:soca-control-db
```

> You can run both `soca-control-worker` (Redis) and `soca-control-pubsub-worker` (Pub/Sub) simultaneously if you have a mix of edge transport types.

---

### Step 12 — Create superuser

```bash
gcloud run jobs create soca-control-setup \
  --image=asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest \
  --region=asia-southeast1 \
  --command="python" \
  --args="manage.py,createsuperuser,--noinput" \
  --vpc-connector=soca-connector \
  --set-env-vars="DJANGO_SUPERUSER_USERNAME=admin,DJANGO_SUPERUSER_EMAIL=admin@example.com,DJANGO_SUPERUSER_PASSWORD=CHANGE_ME" \
  --update-secrets="DJANGO_SECRET_KEY=soca-control-secret-key:latest,DATABASE_URL=soca-control-db-url:latest" \
  --add-cloudsql-instances=YOUR_PROJECT_ID:asia-southeast1:soca-control-db

gcloud run jobs execute soca-control-setup --region=asia-southeast1 --wait
```

---

### Step 13 — Configure edges

#### soca-control (Settings page)

**Redis transport:** Register each edge with:
- **Redis URL**: `redis://MEMORYSTORE_IP:6379` ← private Memorystore IP; the worker reaches it via VPC connector
- **Stream Name**: `edge-NAME:soca:detections` (unique per edge)
- **Subscriber Transport**: Redis Stream

Only the **worker** service uses this Redis URL. The web service does not connect to Redis.

**Pub/Sub transport:** Register each edge with:
- **Subscriber Transport**: Google Pub/Sub
- **GCP Project ID**: your GCP project ID
- **Pub/Sub Subscription**: `soca-detections-sub` (or per-edge subscriptions)

#### soca-engine on each edge device

**Redis transport** — Update `.env` so each engine pushes to GCP Redis via the proxy VM:

```env
PUBLISHER_TYPE=redis
REDIS_URL=redis://PROXY_PUBLIC_IP:6379
REDIS_STREAM=edge-NAME:soca:detections
```

**Pub/Sub transport** — Update `.env` so each engine publishes to Pub/Sub:

```env
PUBLISHER_TYPE=pubsub
PUBSUB_PROJECT_ID=YOUR_PROJECT_ID
PUBSUB_TOPIC=soca-detections
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json
```

These values can also be set via soca-dashboard Settings → Edge Config → Publisher Transport and will be written automatically to `soca-engine/.env` on Save.

Replace `edge-NAME` with the same identifier used in soca-control (e.g. `edge-jakarta-01`).

---

### Step 14 — (Optional) CI/CD with Cloud Build

```bash
gcloud builds submit \
  --config=cloudbuild.yaml \
  --substitutions=\
_REGION=asia-southeast1,\
_REPO=soca,\
_GCS_BUCKET=soca-control-media-YOUR_PROJECT_ID,\
_ALLOWED_HOSTS=soca-control-web-xxxx-as.a.run.app,\
_CSRF_ORIGINS=https://soca-control-web-xxxx-as.a.run.app
```

For automated deploys, create a Cloud Build trigger on the `main` branch using the same substitutions.

---

### Useful commands

```bash
# View logs
gcloud run services logs read soca-control-web   --region=asia-southeast1 --limit=50
gcloud run services logs read soca-control-worker --region=asia-southeast1 --limit=50

# Update an env var without full redeploy
gcloud run services update soca-control-web \
  --region=asia-southeast1 --update-env-vars=DEBUG=false

# Roll back to a previous revision
gcloud run services update-traffic soca-control-web \
  --region=asia-southeast1 --to-revisions=PREV_REVISION=100

# Test Redis connectivity from the proxy VM
gcloud compute ssh redis-proxy --zone=asia-southeast1-b
redis-cli -h MEMORYSTORE_IP ping
```

### Pub/Sub Setup

_Source: `deployment/pubsub-setup-guide.md`_

## Google Cloud Pub/Sub Setup Guide

Complete guide to configure Google Cloud Pub/Sub as the message transport between
**soca-engine** (publisher) and **soca-service / soca-control** (subscriber).

---

### Architecture

```
soca-engine (edge device)
    │  detects event → rule fires → publish_queue action
    │  PUBLISHER_TYPE=pubsub
    ▼
Google Cloud Pub/Sub
    Topic: projects/<PROJECT>/topics/<TOPIC>
    │
    ▼
    Subscription: projects/<PROJECT>/subscriptions/<SUBSCRIPTION>
    │
    ▼
soca-service (PubSubConsumer thread — REST polling, every 5s)
    │  POST /api/v1/ingest/
    ▼
soca-control → Alert saved to database
```

---

### Prerequisites

- Google Cloud project with Pub/Sub API enabled
- A service account key file (JSON) with Pub/Sub Publisher role (for soca-engine)
- A separate service account key (or the same) with Pub/Sub Subscriber role (for soca-service)

---

### Step 1 — Create Service Account

1. Go to **GCP Console → IAM & Admin → Service Accounts**
2. Click **+ Create Service Account**
   - Name: e.g. `soca-pubsub`
   - ID: `soca-pubsub@<PROJECT>.iam.gserviceaccount.com`
3. Click **Done**
4. Open the service account → **Keys tab → Add Key → Create new key → JSON**
5. Save the downloaded JSON file

---

### Step 2 — Grant IAM Roles

Go to **GCP Console → IAM & Admin → IAM → + Grant Access**:

| Principal | Role | Purpose |
|-----------|------|---------|
| `soca-pubsub@<PROJECT>.iam.gserviceaccount.com` | Pub/Sub Publisher | soca-engine publishes to topic |
| `soca-pubsub@<PROJECT>.iam.gserviceaccount.com` | Pub/Sub Subscriber | soca-service pulls from subscription |

Or grant **Pub/Sub Editor** to cover both in a single binding.

**Via gcloud CLI:**
```bash
gcloud pubsub topics add-iam-policy-binding <TOPIC> \
  --project=<PROJECT> \
  --member="serviceAccount:soca-pubsub@<PROJECT>.iam.gserviceaccount.com" \
  --role="roles/pubsub.publisher"

gcloud pubsub subscriptions add-iam-policy-binding <SUBSCRIPTION> \
  --project=<PROJECT> \
  --member="serviceAccount:soca-pubsub@<PROJECT>.iam.gserviceaccount.com" \
  --role="roles/pubsub.subscriber"
```

> IAM changes can take up to 60 seconds to propagate.

---

### Step 3 — Create Topic and Subscription

**Via GCP Console:**

1. Go to **Pub/Sub → Topics → + Create Topic**
   - Topic ID: e.g. `soca-jakarta-01`
   - Click **Create**

2. Go to **Pub/Sub → Subscriptions → + Create Subscription**
   - Subscription ID: e.g. `soca-jakarta-01`
   - Select topic: `projects/<PROJECT>/topics/soca-jakarta-01`
   - Delivery type: **Pull**
   - Click **Create**

**Via gcloud CLI:**
```bash
gcloud pubsub topics create soca-jakarta-01 --project=<PROJECT>
gcloud pubsub subscriptions create soca-jakarta-01 \
  --topic=soca-jakarta-01 \
  --project=<PROJECT>
```

---

### Step 4 — Configure soca-engine

#### Via soca-dashboard UI (recommended)

1. Go to **soca-dashboard → Settings → Edge Config**
2. Set **Publisher Transport** to `Google Pub/Sub`
3. Fill in **GCP Project ID** and **Pub/Sub Topic**
4. Upload the service account JSON under **Pub/Sub Service Account Key**
5. Click **Save Edge Config** — writes `PUBLISHER_TYPE`, `PUBSUB_PROJECT_ID`, `PUBSUB_TOPIC` to engine `.env`
6. Click **Push Cloud Credentials to soca-engine** — sends the key file to the engine and switches the publisher immediately (no restart needed)

The engine switches from Redis to Pub/Sub live when credentials are pushed. The running process updates in memory — a restart is only needed if you edited `.env` manually.

#### Manual (`.env` edit)

```env
PUBLISHER_TYPE=pubsub
PUBSUB_PROJECT_ID=<PROJECT>
PUBSUB_TOPIC=soca-jakarta-01
PUBSUB_KEY_PATH=credentials/pubsub.json    # path to service account JSON
```

> Restart soca-engine after editing `.env` manually.

---

### Step 5 — Configure soca-control Edge

1. Go to **soca-control → Settings → Edges → Edit**
2. Set **Subscriber Type** to `Google Pub/Sub`
3. Fill in:
   - **GCP Project ID**: `<PROJECT>`
   - **Pub/Sub Subscription**: `soca-jakarta-01`
4. **Save**

For each rule that should publish alerts:

1. Go to **soca-dashboard → Schedules → [schedule] → Rules**
2. Enable the **Publish to Queue** action

---

### Step 6 — Configure soca-service

soca-service handles Pub/Sub consumption automatically — no dedicated configuration needed beyond its standard setup.

Upload the Pub/Sub service account key via **soca-control → Settings → soca-service** (or push it via the Push Cloud Credentials button in soca-dashboard). soca-service reads the key path from `config.json` (`pubsub_key_path`).

> **Note:** soca-service does **not** use `GOOGLE_APPLICATION_CREDENTIALS`. It reads the key path from `config.json` which is written when soca-control pushes Pub/Sub credentials.

Both consumers (Redis and Pub/Sub) run simultaneously inside soca-service. Each consumer queries soca-control for its edge list every 30 seconds and self-selects based on `subscriber_type`:
- Edges with `subscriber_type=redis` → `StreamsConsumer`
- Edges with `subscriber_type=pubsub` → `PubSubConsumer`

Switching an edge from Redis to Pub/Sub in soca-control takes effect within 30 seconds — no restart of soca-service required.

---

### Step 7 — Verify End-to-End

**Test manually:**
```bash
python3 -c "
from google.cloud import pubsub_v1
from google.oauth2 import service_account
import time, base64

creds = service_account.Credentials.from_service_account_file(
    '/path/to/pubsub.json',
    scopes=['https://www.googleapis.com/auth/pubsub'])

pub = pubsub_v1.PublisherClient(credentials=creds)
future = pub.publish('projects/<PROJECT>/topics/soca-jakarta-01', b'hello-soca')
print('Published msg id:', future.result())
"
```

**Check soca-service is consuming** — look at the `/health` endpoint:
```
GET http://localhost:8010/health
```
`pubsub.active_edges` should be > 0 and `messages_processed` should increment after a detection.

---

### Troubleshooting

#### `403 PERMISSION_DENIED: pubsub.topics.publish`

Missing Publisher role on the topic. See Step 2.

#### `403 PERMISSION_DENIED: pubsub.subscriptions.consume`

Missing Subscriber role on the subscription. See Step 2.

#### `404 Resource not found`

The subscription does not exist. Create it (Step 3).

#### Messages going to DLQ (`soca-engine/dlq/`)

Pub/Sub publish is failing. Check soca-engine logs. Common causes:
- Missing publisher IAM role
- Wrong `PUBSUB_PROJECT_ID` or `PUBSUB_TOPIC`
- `PUBSUB_KEY_PATH` not set or file missing
- Engine not restarted after manual `.env` edit (or Push Cloud Credentials not clicked)

#### soca-service not picking up messages (HTTP proxy environment)

soca-service uses REST polling mode (not gRPC streaming) which works correctly through HTTP proxies. Set in soca-service `.env`:

```env
HTTPS_PROXY=http://your-proxy:8080
HTTP_PROXY=http://your-proxy:8080
NO_PROXY=localhost,127.0.0.1
```

Verify REST mode is active in logs:
```
PubSubConsumer starting (REST poll mode)
Pub/Sub REST session created (proxy-friendly)
```

#### Engine still sending to Redis after switching to Pub/Sub

If you changed `PUBLISHER_TYPE` in soca-dashboard and saved Edge Config but did **not** click **Push Cloud Credentials to soca-engine**, the change was written to `.env` on disk but the running process still uses Redis. Either:
- Click **Push Cloud Credentials to soca-engine** (switches live immediately), or
- Restart soca-engine (re-reads `.env`)

---

### Real-World Configuration (Bekasi Edge)

| Setting | Value |
|---------|-------|
| GCP Project | `soca-video-analytics-dev` |
| Topic | `soca-snapshot-notification` |
| Subscription | configured per soca-control edge |
| Key file | `credentials/pubsub.json` (written by Push Cloud Credentials) |
| GCS Bucket | `soca-snapshot-bucket` |
| GCS Path Prefix | `edge-mac-mini-bekasi` |

---

### Related Guides

- [GCS Snapshot Storage](gcs-snapshot-storage.md) — configure GCS for snapshot image storage

### GCS Snapshot Storage

_Source: `deployment/gcs-snapshot-storage.md`_

## GCS Snapshot Storage Setup Guide

Complete guide to configure Google Cloud Storage as the snapshot image backend for SOCA.

---

### Architecture

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

### Why Per-Edge Path Prefix?

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

### Step 1 — Create GCS Bucket

1. Go to **GCP Console → Cloud Storage → Buckets → Create**
2. Choose a globally unique name (e.g. `soca-snapshot-bucket`)
3. Select region closest to your edge devices
4. Leave other settings as default → **Create**

---

### Step 2 — Create Service Account

1. Go to **GCP Console → IAM & Admin → Service Accounts → + Create**
2. Name: e.g. `soca-snapshot-sa`
3. Grant role: **Storage Object Admin** (allows upload + list)
4. Click **Done**
5. Open the service account → **Keys → Add Key → Create new key → JSON**
6. Save the downloaded JSON file

---

### Step 3 — Configure soca-engine

#### Via soca-dashboard UI (recommended)

1. Go to **soca-dashboard → Settings → Edge Config**
2. Under **Cloud Storage (GCS)**:
   - Upload the service account JSON under **GCS Service Account Key**
   - Fill in **GCS Bucket** name
   - Fill in **GCS Path Prefix** (leave blank to use `EDGE_NAME` automatically)
3. Click **Save Edge Config** — this writes `GCS_BUCKET`, `GCS_KEY_PATH`, and `GCS_PATH_PREFIX` to soca-engine's `.env`
4. Click **Push Cloud Credentials to soca-engine** — sends the key file to the engine immediately (no restart needed)

#### Manual (`.env` edit)

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

### Step 4 — Configure soca-control

#### 4a — Snapshot Storage

1. Go to **soca-control → Settings → Snapshot Storage**
2. Select **Google Cloud Storage**
3. Enter **Bucket name** — must match `GCS_BUCKET` in soca-engine
4. **Save**

> There is no global Path Prefix setting. Path prefix is configured per-edge (see 4b).

#### 4b — Per-edge GCS Path Prefix

1. Go to **soca-control → Settings → Edges → Edit** (for each edge)
2. Fill in **GCS Path Prefix** — must exactly match `GCS_PATH_PREFIX` in that edge's soca-engine `.env`
   - Leave blank if it equals the edge name (default behaviour)
3. **Save**

#### 4c — GCS Credentials (for soca-control media uploads)

soca-control uses GCS to store uploaded logos and media files (separate from snapshot storage).

Upload via UI: **Settings → Google Cloud Credentials → Browse → select .json file → Save**

This writes `GOOGLE_APPLICATION_CREDENTIALS` to soca-control's `.env` and persists across restarts.

---

### Step 5 — Verify End-to-End

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

### How the Sync Works

`core/gcs_sync.py` runs as an asyncio background task inside the FastAPI lifespan:

- Polls `SNAPSHOTS_DIR` every 10 seconds using `asyncio.to_thread` (non-blocking)
- Tracks already-uploaded paths in an in-memory set (no re-uploads per session)
- On restart, existing files are re-uploaded (idempotent — GCS overwrites with same data)
- GCS object key: `{GCS_PATH_PREFIX}/snapshots/{job_id}/{frame_id}.jpg`
- soca-control's `Alert.snapshot_url` receives `snapshot_path` as `{job_id}/{frame_id}.jpg` and prepends `snapshots/` to reconstruct the full blob path

---

### Adding a New Edge Device

No manual GCS setup is needed. When a new edge device is configured:

1. Set `EDGE_NAME` — this automatically becomes `GCS_PATH_PREFIX` if not overridden
2. On first detection with `save_snapshot`, soca-engine uploads the file and the `edge-name/snapshots/` path is created automatically in GCS (GCS is a flat key store — there are no real folders to pre-create)

---

### Troubleshooting

#### Images not showing (thumbnail blank)

1. Check `GCS_BUCKET` is set in soca-engine `.env`
2. Check soca-control Snapshot Storage is set to **Google Cloud Storage** with matching bucket name
3. Check soca-engine has been restarted (or Push Cloud Credentials clicked) after config change
4. Set `LOG_LEVEL=DEBUG` in soca-engine — look for `GCS sync: uploaded ...` lines

#### Images show 403 Forbidden

Service account missing `storage.objects.get` permission. Grant **Storage Object Admin** role, or make the bucket public:
```bash
gsutil iam ch allUsers:objectViewer gs://your-bucket-name
```

#### Images show 404 / NoSuchKey

The GCS blob path does not match what soca-control is requesting. Check:
1. **GCS Path Prefix** in soca-control Edge settings matches `GCS_PATH_PREFIX` in soca-engine `.env`
2. The snapshot file was actually uploaded — check soca-engine debug logs
3. Enough time has passed (sync runs every 10s)

#### `google.cloud.exceptions.Forbidden` on upload

Service account missing `storage.objects.create` permission. Grant **Storage Object Admin** role.

#### Sync not starting

`GCS_BUCKET` is empty in soca-engine `.env`. Set it via soca-dashboard or manually, then restart.

### Metabase Reporting

_Source: `deployment/metabase-reporting-guide.md`_

## SOCA — Metabase Reporting Guide

Complete database schema and SQL queries for building Metabase dashboards covering
Object Detection, People Counting, Intrusion, Crowd, and LPR reports.

**Database: PostgreSQL**

---

### Database Overview

soca-control uses **PostgreSQL** in production.
Connect Metabase to the same PostgreSQL instance that soca-control uses.

> **Timezone note:** All timestamps stored in UTC. Set Metabase report timezone to `Asia/Jakarta` (GMT+7)
> in Admin → Settings → Localization → Report Timezone, **or** use `AT TIME ZONE 'Asia/Jakarta'` in queries.

---

### Table: `app_alert` — Core Schema

```
Column              Type        Notes
──────────────────────────────────────────────────────────────────
id                  SERIAL      Primary key
edge_id             INTEGER     FK → app_edge.id (nullable)
edge_name           TEXT        Denormalized edge identifier, e.g. "edge-mac-mini-bekasi"
camera_id           TEXT        Raw camera identifier
camera_name         TEXT        Human-readable camera name
job_id              TEXT        UUID of the soca-engine job
frame_id            TEXT        Unique frame identifier
timestamp           TIMESTAMPTZ UTC — when the detection occurred
alert_category      TEXT        'Intrusion' | 'PPE' | 'Detection' | 'Counting' | 'Crowd' | 'LPR'
rule_name           TEXT        Name of the rule that fired
snapshot_message    TEXT        Formatted alert message
snapshot_path       TEXT        Local/GCS path to snapshot JPG
detection_count     INTEGER     Total detections in frame
in_roi_count        INTEGER     Detections inside ROI
cls_summary         JSONB       Class ID counts: {"0": 2, "2": 1}
cls_name_summary    JSONB       Class name counts: {"person": 2, "car": 1}
crossing_counts     JSONB       Line crossing: {"rule_name": {"in": 5, "out": 3}}
crowd_count         INTEGER     In-ROI person count (crowd mode)
lpr_plates          JSONB       [{plate_number, plate_confidence, plate_expiry}]
raw                 JSONB       Full raw payload
created_at          TIMESTAMPTZ UTC — when soca-control received the alert
```

**Indexes:** `(edge_name, timestamp)`, `(alert_category, timestamp)`, `(camera_id)`

---

### Table: `app_edge` — Edge Devices

```
Column              Type        Notes
──────────────────────────────────────────────────────────────────
id                  SERIAL      Primary key
name                TEXT        Unique edge name, e.g. "edge-mac-mini-bekasi"
url                 TEXT        soca-dashboard base URL
engine_url          TEXT        soca-engine base URL
location            TEXT        Physical location description
active              BOOLEAN     true = active, false = deactivated
gcs_path_prefix     TEXT        GCS path prefix, e.g. "edge-mac-mini-bekasi"
created_at          TIMESTAMPTZ
```

---

### Connecting Metabase to PostgreSQL

1. Metabase → Admin → Databases → Add Database → **PostgreSQL**
2. Fill in: host, port (`5432`), database name, user, password
3. Save — Metabase syncs the schema automatically

---

### Shared Utility Queries

#### Q0 — Daily Alert Summary (all categories)

```sql
SELECT
    timestamp::date                AS date,
    alert_category,
    COUNT(*)                       AS alert_count,
    SUM(in_roi_count)              AS total_in_roi,
    ROUND(AVG(in_roi_count), 1)    AS avg_in_roi
FROM app_alert
WHERE timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date, alert_category
ORDER BY date DESC, alert_count DESC
```

---

### 1. Object Detection Report

**alert_category = 'Detection'**

#### 1-A — Daily trend

```sql
SELECT
    timestamp::date                AS date,
    COUNT(*)                       AS alerts,
    SUM(detection_count)           AS total_objects,
    ROUND(AVG(in_roi_count), 1)    AS avg_in_roi
FROM app_alert
WHERE alert_category = 'Detection'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date
ORDER BY date
```

#### 1-B — Alerts by camera

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS alert_count,
    SUM(detection_count)           AS total_objects,
    MAX(detection_count)           AS peak_count
FROM app_alert
WHERE alert_category = 'Detection'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY camera_id, camera_name, edge_name
ORDER BY alert_count DESC
LIMIT 20
```

#### 1-C — Alerts by rule name

```sql
SELECT
    rule_name,
    COUNT(*)                       AS alert_count,
    ROUND(AVG(in_roi_count), 1)    AS avg_in_roi,
    MAX(detection_count)           AS peak_objects
FROM app_alert
WHERE alert_category = 'Detection'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY rule_name
ORDER BY alert_count DESC
```

#### 1-D — Hourly distribution (heatmap source)

```sql
SELECT
    EXTRACT(HOUR FROM timestamp)::integer      AS hour_of_day,
    EXTRACT(DOW  FROM timestamp)::integer      AS day_of_week,  -- 0=Sun, 6=Sat
    COUNT(*)                                   AS alert_count
FROM app_alert
WHERE alert_category = 'Detection'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY hour_of_day, day_of_week
ORDER BY day_of_week, hour_of_day
```

#### 1-E — Top detected classes

```sql
SELECT
    kv.key                         AS class_name,
    SUM((kv.value)::integer)       AS total_detections
FROM app_alert a,
     jsonb_each(a.cls_name_summary) kv
WHERE a.alert_category = 'Detection'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
GROUP BY kv.key
ORDER BY total_detections DESC
```

#### 1-F — Recent alerts list

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    detection_count,
    in_roi_count,
    snapshot_message
FROM app_alert
WHERE alert_category = 'Detection'
ORDER BY timestamp DESC
LIMIT 200
```

---

### 2. Intrusion Detection Report

**alert_category = 'Intrusion'**

#### 2-A — Daily intrusion trend

```sql
SELECT
    timestamp::date                AS date,
    COUNT(*)                       AS intrusions,
    SUM(in_roi_count)              AS total_intruders
FROM app_alert
WHERE alert_category = 'Intrusion'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date
ORDER BY date
```

#### 2-B — Most triggered cameras

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS intrusion_count,
    MIN(timestamp AT TIME ZONE 'Asia/Jakarta') AS first_seen,
    MAX(timestamp AT TIME ZONE 'Asia/Jakarta') AS last_seen
FROM app_alert
WHERE alert_category = 'Intrusion'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY camera_id, camera_name, edge_name
ORDER BY intrusion_count DESC
```

#### 2-C — Peak intrusion hours

```sql
SELECT
    EXTRACT(HOUR FROM timestamp AT TIME ZONE 'Asia/Jakarta')::integer AS hour,
    COUNT(*)                       AS intrusion_count
FROM app_alert
WHERE alert_category = 'Intrusion'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY hour
ORDER BY hour
```

#### 2-D — Intrusion events detail

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    in_roi_count                               AS intruders_in_roi,
    snapshot_message
FROM app_alert
WHERE alert_category = 'Intrusion'
ORDER BY timestamp DESC
LIMIT 500
```

#### 2-E — Time gap between consecutive alerts per camera

```sql
SELECT
    camera_id,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    ROUND(
        EXTRACT(EPOCH FROM (
            timestamp - LAG(timestamp) OVER (PARTITION BY camera_id ORDER BY timestamp)
        ))
    )                                          AS seconds_since_last_alert
FROM app_alert
WHERE alert_category = 'Intrusion'
  AND timestamp >= NOW() - INTERVAL '30 days'
ORDER BY camera_id, timestamp
```

---

### 3. PPE Violations Report

**alert_category = 'PPE'**

#### 3-A — Daily PPE violation trend

```sql
SELECT
    timestamp::date                AS date,
    COUNT(*)                       AS violations,
    SUM(in_roi_count)              AS total_non_compliant
FROM app_alert
WHERE alert_category = 'PPE'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date
ORDER BY date
```

#### 3-B — Violations by camera / zone

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS zone,
    edge_name,
    COUNT(*)                       AS violation_count,
    ROUND(AVG(in_roi_count), 1)    AS avg_non_compliant
FROM app_alert
WHERE alert_category = 'PPE'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY camera_id, camera_name, edge_name
ORDER BY violation_count DESC
```

#### 3-C — Non-compliant item breakdown

```sql
SELECT
    kv.key                         AS ppe_item,
    SUM((kv.value)::integer)       AS total_detected,
    COUNT(DISTINCT a.id)           AS alert_count
FROM app_alert a,
     jsonb_each(a.cls_name_summary) kv
WHERE a.alert_category = 'PPE'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
GROUP BY kv.key
ORDER BY total_detected DESC
```

#### 3-D — Recent PPE violations

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS zone,
    edge_name,
    rule_name,
    in_roi_count                               AS non_compliant_count,
    snapshot_message
FROM app_alert
WHERE alert_category = 'PPE'
ORDER BY timestamp DESC
LIMIT 500
```

---

### 4. People Counting Report

**alert_category = 'Counting'**

#### 4-A — Cumulative crossings by camera and rule

```sql
SELECT
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    rule.key                           AS rule_name,
    SUM((rule.value ->> 'in')::integer)  AS total_in,
    SUM((rule.value ->> 'out')::integer) AS total_out,
    SUM((rule.value ->> 'in')::integer)
      - SUM((rule.value ->> 'out')::integer) AS net_count
FROM app_alert a,
     jsonb_each(a.crossing_counts) rule
WHERE a.alert_category = 'Counting'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
GROUP BY a.camera_id, a.camera_name, a.edge_name, rule.key
ORDER BY total_in DESC
```

#### 4-B — Hourly crossing volume

```sql
SELECT
    timestamp::date                    AS date,
    EXTRACT(HOUR FROM timestamp AT TIME ZONE 'Asia/Jakarta')::integer AS hour,
    SUM(
        COALESCE((crossing_counts -> rule.key ->> 'in')::integer, 0)
    )                                  AS crossings_in,
    SUM(
        COALESCE((crossing_counts -> rule.key ->> 'out')::integer, 0)
    )                                  AS crossings_out
FROM app_alert a,
     jsonb_object_keys(a.crossing_counts) rule
WHERE a.alert_category = 'Counting'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date, hour
ORDER BY date, hour
```

#### 4-C — Daily entry/exit balance per camera

```sql
SELECT
    a.timestamp::date                  AS date,
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    SUM((rule.value ->> 'in')::integer)  AS entries,
    SUM((rule.value ->> 'out')::integer) AS exits,
    SUM((rule.value ->> 'in')::integer)
      - SUM((rule.value ->> 'out')::integer) AS occupancy_change
FROM app_alert a,
     jsonb_each(a.crossing_counts) rule
WHERE a.alert_category = 'Counting'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
GROUP BY a.timestamp::date, a.camera_id, a.camera_name
ORDER BY date, camera
```

#### 4-D — People count events list

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    detection_count,
    snapshot_message
FROM app_alert
WHERE alert_category = 'Counting'
ORDER BY timestamp DESC
LIMIT 500
```

---

### 5. Crowd Detection Report

**alert_category = 'Crowd'**

#### 5-A — Daily crowd alerts and peak count

```sql
SELECT
    timestamp::date                AS date,
    COUNT(*)                       AS crowd_alerts,
    MAX(crowd_count)               AS peak_crowd,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd,
    SUM(crowd_count)               AS total_people_detected
FROM app_alert
WHERE alert_category = 'Crowd'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date
ORDER BY date
```

#### 5-B — Crowd level by camera

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS crowd_alerts,
    MAX(crowd_count)               AS peak_crowd,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd,
    MIN(crowd_count)               AS min_crowd
FROM app_alert
WHERE alert_category = 'Crowd'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY camera_id, camera_name, edge_name
ORDER BY peak_crowd DESC
```

#### 5-C — Hourly crowd distribution

```sql
SELECT
    EXTRACT(HOUR FROM timestamp AT TIME ZONE 'Asia/Jakarta')::integer AS hour,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd_count,
    MAX(crowd_count)               AS peak_crowd_count,
    COUNT(*)                       AS alert_count
FROM app_alert
WHERE alert_category = 'Crowd'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY hour
ORDER BY hour
```

#### 5-D — Crowd events exceeding threshold

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    crowd_count,
    rule_name,
    snapshot_message
FROM app_alert
WHERE alert_category = 'Crowd'
  AND crowd_count >= 10            -- adjust threshold
ORDER BY crowd_count DESC, timestamp DESC
LIMIT 200
```

#### 5-E — Crowd density in 15-minute buckets

```sql
SELECT
    date_trunc('hour', timestamp AT TIME ZONE 'Asia/Jakarta')
      + (FLOOR(EXTRACT(MINUTE FROM timestamp) / 15) * INTERVAL '15 minutes') AS time_bucket,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd
FROM app_alert
WHERE alert_category = 'Crowd'
  AND timestamp >= NOW() - INTERVAL '7 days'
GROUP BY time_bucket, camera_id, camera_name
ORDER BY time_bucket
```

---

### 6. LPR (License Plate Recognition) Report

**alert_category = 'LPR'**

#### 6-A — Daily LPR detections

```sql
SELECT
    timestamp::date                AS date,
    COUNT(*)                       AS detection_events
FROM app_alert
WHERE alert_category = 'LPR'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY timestamp::date
ORDER BY date
```

#### 6-B — All detected plates

```sql
SELECT
    (a.timestamp AT TIME ZONE 'Asia/Jakarta')  AS local_time,
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    plate ->> 'plate_number'                   AS plate_number,
    plate ->> 'plate_expiry'                   AS expiry,
    ROUND((plate ->> 'plate_confidence')::numeric * 100, 1) AS confidence_pct
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE a.alert_category = 'LPR'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
  AND (plate ->> 'plate_number') IS NOT NULL
  AND (plate ->> 'plate_number') <> ''
ORDER BY a.timestamp DESC
LIMIT 1000
```

#### 6-C — Plate frequency (most seen plates)

```sql
SELECT
    plate ->> 'plate_number'                   AS plate_number,
    COUNT(*)                                   AS appearances,
    MIN(a.timestamp AT TIME ZONE 'Asia/Jakarta') AS first_seen,
    MAX(a.timestamp AT TIME ZONE 'Asia/Jakarta') AS last_seen,
    COUNT(DISTINCT a.camera_id)                AS camera_count
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE a.alert_category = 'LPR'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
  AND (plate ->> 'plate_number') <> ''
GROUP BY plate ->> 'plate_number'
ORDER BY appearances DESC
LIMIT 50
```

#### 6-D — Plates by camera

```sql
SELECT
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    COUNT(DISTINCT plate ->> 'plate_number')   AS unique_plates,
    COUNT(*)                                   AS total_reads
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE a.alert_category = 'LPR'
  AND a.timestamp >= NOW() - INTERVAL '30 days'
  AND (plate ->> 'plate_number') <> ''
GROUP BY a.camera_id, a.camera_name, a.edge_name
ORDER BY total_reads DESC
```

#### 6-E — Plate search (use `{{plate_search}}` as Metabase text variable)

```sql
SELECT
    (a.timestamp AT TIME ZONE 'Asia/Jakarta')  AS local_time,
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    plate ->> 'plate_number'                   AS plate_number,
    plate ->> 'plate_expiry'                   AS expiry,
    ROUND((plate ->> 'plate_confidence')::numeric * 100, 1) AS confidence_pct
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE a.alert_category = 'LPR'
  AND (plate ->> 'plate_number') ILIKE '%' || {{plate_search}} || '%'
ORDER BY a.timestamp DESC
LIMIT 200
```

---

### 7. Cross-Category Dashboard Queries

#### 7-A — Fleet overview — all categories today

```sql
SELECT
    alert_category,
    COUNT(*)                       AS today_count,
    COUNT(DISTINCT camera_id)      AS cameras_affected,
    COUNT(DISTINCT edge_name)      AS edges_affected
FROM app_alert
WHERE timestamp::date = CURRENT_DATE
GROUP BY alert_category
ORDER BY today_count DESC
```

#### 7-B — Alert volume by edge and category

```sql
SELECT
    edge_name,
    alert_category,
    COUNT(*)                       AS alert_count
FROM app_alert
WHERE timestamp >= NOW() - INTERVAL '30 days'
GROUP BY edge_name, alert_category
ORDER BY edge_name, alert_count DESC
```

#### 7-C — Top 10 busiest cameras (all categories, last 7 days)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS total_alerts,
    COUNT(DISTINCT alert_category) AS category_count
FROM app_alert
WHERE timestamp >= NOW() - INTERVAL '7 days'
GROUP BY camera_id, camera_name, edge_name
ORDER BY total_alerts DESC
LIMIT 10
```

#### 7-D — 30-minute alert heatmap (last 24 hours)

```sql
SELECT
    date_trunc('hour', timestamp AT TIME ZONE 'Asia/Jakarta')
      + (FLOOR(EXTRACT(MINUTE FROM timestamp) / 30) * INTERVAL '30 minutes') AS time_bucket,
    alert_category,
    COUNT(*)                       AS alert_count
FROM app_alert
WHERE timestamp >= NOW() - INTERVAL '24 hours'
GROUP BY time_bucket, alert_category
ORDER BY time_bucket
```

#### 7-E — Alerts this week vs last week

```sql
SELECT
    alert_category,
    COUNT(*) FILTER (WHERE timestamp >= date_trunc('week', NOW()))             AS this_week,
    COUNT(*) FILTER (WHERE timestamp >= date_trunc('week', NOW()) - INTERVAL '7 days'
                       AND timestamp <  date_trunc('week', NOW()))             AS last_week
FROM app_alert
WHERE timestamp >= date_trunc('week', NOW()) - INTERVAL '7 days'
GROUP BY alert_category
ORDER BY this_week DESC
```

---

### 8. Metabase Dashboard Layout Suggestions

#### Dashboard 1: Fleet Operations Overview

| Card | Type | Query |
|------|------|-------|
| Total alerts today | Number | Q7-A `SUM(today_count)` |
| Alert trend (30 days) | Line chart | Q0 grouped by date |
| Alerts by category today | Bar chart | Q7-A |
| Top cameras (7 days) | Table | Q7-C |
| Alerts by edge | Stacked bar | Q7-B |
| 30-min heatmap | Bar chart | Q7-D |
| This week vs last week | Row chart | Q7-E |

#### Dashboard 2: Object Detection & Intrusion

| Card | Type | Query |
|------|------|-------|
| Detection trend | Line chart | Q1-A |
| Intrusion trend | Line chart | Q2-A |
| Top detection cameras | Bar chart | Q1-B |
| Peak intrusion hours | Bar chart | Q2-C |
| Top detected classes | Pie / Bar | Q1-E |
| Recent alerts | Table | Q1-F |

#### Dashboard 3: People Counting & Crowd

| Card | Type | Query |
|------|------|-------|
| Daily entries / exits | Line chart | Q4-C |
| Crossings by camera | Bar chart | Q4-A |
| Daily crowd alerts | Line chart | Q5-A |
| Peak crowd by camera | Bar chart | Q5-B |
| Hourly crowd distribution | Bar chart | Q5-C |
| Crowd density (15 min) | Line chart | Q5-E |
| Crowd events > threshold | Table | Q5-D |

#### Dashboard 4: PPE & Safety Compliance

| Card | Type | Query |
|------|------|-------|
| Daily violations | Line chart | Q3-A |
| Violations by zone | Bar chart | Q3-B |
| Missing PPE items | Pie chart | Q3-C |
| Recent violations | Table | Q3-D |

#### Dashboard 5: LPR — License Plate

| Card | Type | Query |
|------|------|-------|
| Daily plate reads | Line chart | Q6-A |
| Plates by camera | Bar chart | Q6-D |
| Most frequent plates | Table | Q6-C |
| All plate reads | Table | Q6-B |
| Plate search | Table + text filter | Q6-E |

---

### 9. Metabase Variables & Filters

Add as **dashboard filters** and link to multiple cards:

| Filter name | Type | Column |
|------------|------|--------|
| Date range | Date range | `timestamp` |
| Edge | Text | `edge_name` |
| Camera | Text | `camera_id` |
| Alert category | Category | `alert_category` |
| Plate number | Text | Q6-E `{{plate_search}}` |

---

### 10. Recommended Indexes

Run once in PostgreSQL to speed up Metabase queries:

```sql
-- Already created by Django migrations:
-- CREATE INDEX app_alert_edge_name_timestamp ON app_alert (edge_name, timestamp DESC);
-- CREATE INDEX app_alert_alert_category_timestamp ON app_alert (alert_category, timestamp DESC);
-- CREATE INDEX app_alert_camera_id ON app_alert (camera_id);

-- Additional indexes for reporting:
CREATE INDEX IF NOT EXISTS idx_alert_ts_date
    ON app_alert (timestamp DESC);

CREATE INDEX IF NOT EXISTS idx_alert_cat_cam_ts
    ON app_alert (alert_category, camera_id, timestamp DESC);

-- Partial index for LPR (speeds up plate queries on large tables)
CREATE INDEX IF NOT EXISTS idx_alert_lpr
    ON app_alert (timestamp DESC)
    WHERE alert_category = 'LPR';

-- Partial index for crowd_count queries
CREATE INDEX IF NOT EXISTS idx_alert_crowd
    ON app_alert (crowd_count DESC, timestamp DESC)
    WHERE alert_category = 'Crowd';
```

---

### 11. JSON Field Notes

All JSON columns (`cls_name_summary`, `crossing_counts`, `lpr_plates`) are stored as **JSONB** in PostgreSQL.

| Operation | Syntax |
|-----------|--------|
| Get key value | `col ->> 'key'` → text |
| Get nested object | `col -> 'key'` → jsonb |
| Iterate object keys+values | `jsonb_each(col)` → `(key text, value jsonb)` |
| Iterate array elements | `jsonb_array_elements(col)` → `jsonb` |
| Cast to number | `(col ->> 'key')::integer` or `::numeric` |
| Case-insensitive search | `col ->> 'key' ILIKE '%search%'` |

### Grafana Reporting

_Source: `deployment/grafana-reporting-guide.md`_

## SOCA — Grafana Reporting Guide

Complete database schema and SQL queries for building Grafana dashboards covering
Object Detection, People Counting, Intrusion, Crowd, and LPR reports.

**Database: PostgreSQL**

---

### Database Overview

soca-control uses **PostgreSQL** in production.
Add it as a Grafana data source — the same PostgreSQL instance used by soca-control.

> **Timezone note:** All timestamps stored in UTC. Set Grafana's timezone to `Asia/Jakarta (GMT+7)`
> in Dashboard Settings → Time options → Timezone, **or** apply `AT TIME ZONE 'Asia/Jakarta'` in queries.

---

### Table: `app_alert` — Core Schema

```
Column              Type        Notes
──────────────────────────────────────────────────────────────────
id                  SERIAL      Primary key
edge_id             INTEGER     FK → app_edge.id (nullable)
edge_name           TEXT        Denormalized edge identifier, e.g. "edge-mac-mini-bekasi"
camera_id           TEXT        Raw camera identifier
camera_name         TEXT        Human-readable camera name
job_id              TEXT        UUID of the soca-engine job
frame_id            TEXT        Unique frame identifier
timestamp           TIMESTAMPTZ UTC — when the detection occurred
alert_category      TEXT        'Intrusion' | 'PPE' | 'Detection' | 'Counting' | 'Crowd' | 'LPR'
rule_name           TEXT        Name of the rule that fired
snapshot_message    TEXT        Formatted alert message
snapshot_path       TEXT        Local/GCS path to snapshot JPG
detection_count     INTEGER     Total detections in frame
in_roi_count        INTEGER     Detections inside ROI
cls_summary         JSONB       Class ID counts: {"0": 2, "2": 1}
cls_name_summary    JSONB       Class name counts: {"person": 2, "car": 1}
crossing_counts     JSONB       Line crossing: {"rule_name": {"in": 5, "out": 3}}
crowd_count         INTEGER     In-ROI person count (crowd mode)
lpr_plates          JSONB       [{plate_number, plate_confidence, plate_expiry}]
raw                 JSONB       Full raw payload
created_at          TIMESTAMPTZ UTC — when soca-control received the alert
```

**Indexes:** `(edge_name, timestamp)`, `(alert_category, timestamp)`, `(camera_id)`

---

### Table: `app_edge` — Edge Devices

```
Column              Type        Notes
──────────────────────────────────────────────────────────────────
id                  SERIAL      Primary key
name                TEXT        Unique edge name, e.g. "edge-mac-mini-bekasi"
url                 TEXT        soca-dashboard base URL
engine_url          TEXT        soca-engine base URL
location            TEXT        Physical location description
active              BOOLEAN     true = active, false = deactivated
gcs_path_prefix     TEXT        GCS path prefix, e.g. "edge-mac-mini-bekasi"
created_at          TIMESTAMPTZ
```

---

### Connecting Grafana to PostgreSQL

1. Grafana → **Connections → Data sources → Add data source → PostgreSQL**
2. Fill in:
   - **Host URL**: `localhost:5432`
   - **Database**: your soca-control database name
   - **User / Password**: PostgreSQL credentials
   - **TLS/SSL Mode**: `disable` (for local) or `require` (for remote)
3. **Min time interval**: `1m`
4. **PostgreSQL version**: select your version (14+)
5. Click **Save & Test** — Grafana validates the connection

---

### Grafana SQL Macros

Grafana's PostgreSQL plugin provides time macros for all time-series panels:

| Macro | Expands to | Use for |
|-------|-----------|---------|
| `$__timeFilter(col)` | `col BETWEEN $from AND $to` | WHERE clause — respects dashboard time picker |
| `$__timeGroup(col,'$__interval')` | `date_trunc(...)` bucket | GROUP BY in time series |
| `$__timeGroupAlias(col,'$__interval')` | Same + aliased as `time` | Required alias for Time series panels |
| `$__interval` | Auto-calculated interval string | Passed to `$__timeGroup` |
| `$__timeFrom()` | Start of selected time range | Manual date comparisons |
| `$__timeTo()` | End of selected time range | Manual date comparisons |

> All queries below use `$__timeFilter(timestamp)` in place of a fixed `30 days` window,
> so they automatically respond to the Grafana time range picker.

---

### Template Variables

Create these as **Dashboard Variables** (Dashboard Settings → Variables → Add variable):

#### `$edge` — Edge filter

| Field | Value |
|-------|-------|
| Type | Query |
| Data source | your PostgreSQL |
| Query | `SELECT DISTINCT edge_name FROM app_alert WHERE edge_name <> '' ORDER BY 1` |
| Multi-value | ✅ |
| Include All | ✅ |
| All value | `.*` |

Use in queries: `AND edge_name ~ '$edge'` (when All = `.*`) or `AND edge_name IN ($edge)` for multi-select.

#### `$camera` — Camera filter

| Field | Value |
|-------|-------|
| Type | Query |
| Query | `SELECT DISTINCT COALESCE(NULLIF(camera_name,''), camera_id) FROM app_alert ORDER BY 1` |
| Multi-value | ✅ |
| Include All | ✅ |

#### `$category` — Alert category filter

| Field | Value |
|-------|-------|
| Type | Query |
| Query | `SELECT DISTINCT alert_category FROM app_alert ORDER BY 1` |
| Multi-value | ✅ |
| Include All | ✅ |

#### `$plate_search` — Plate number text filter

| Field | Value |
|-------|-------|
| Type | Text box |
| Default | _(empty)_ |

Use in query: `AND (plate ->> 'plate_number') ILIKE '%${plate_search}%'`

---

### Shared Utility Queries

#### Q0 — Daily Alert Summary (all categories) — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    alert_category,
    COUNT(*)  AS alert_count
FROM app_alert
WHERE $__timeFilter(timestamp)
GROUP BY 1, alert_category
ORDER BY 1
```

> Enable **Format as: Time series** and set **Fill mode** to `0` for missing buckets.

---

### 1. Object Detection Report

**alert_category = 'Detection'**

#### 1-A — Daily trend — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    COUNT(*)                    AS alerts,
    SUM(detection_count)        AS total_objects,
    ROUND(AVG(in_roi_count), 1) AS avg_in_roi
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Detection'
GROUP BY 1
ORDER BY 1
```

#### 1-B — Alerts by camera — Bar chart

**Panel type:** Bar chart (horizontal)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS alert_count,
    SUM(detection_count)           AS total_objects,A
    MAX(detection_count)           AS peak_count
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Detection'
GROUP BY camera_id, camera_name, edge_name
ORDER BY alert_count DESC
LIMIT 20
```

#### 1-C — Alerts by rule name — Bar chart

**Panel type:** Bar chart

```sql
SELECT
    rule_name,
    COUNT(*)                       AS alert_count,
    ROUND(AVG(in_roi_count), 1)    AS avg_in_roi,
    MAX(detection_count)           AS peak_objects
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Detection'
GROUP BY rule_name
ORDER BY alert_count DESC
```

#### 1-D — Hourly distribution — Heatmap

**Panel type:** Heatmap
> X-axis = hour of day (0–23), Y-axis = day of week

```sql
SELECT
    EXTRACT(HOUR FROM timestamp)::integer      AS hour_of_day,
    EXTRACT(DOW  FROM timestamp)::integer      AS day_of_week,
    COUNT(*)                                   AS alert_count
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Detection'
GROUP BY hour_of_day, day_of_week
ORDER BY day_of_week, hour_of_day
```

#### 1-E — Top detected classes — Pie chart

**Panel type:** Pie chart

```sql
SELECT
    kv.key                         AS class_name,
    SUM((kv.value)::integer)       AS total_detections
FROM app_alert a,
     jsonb_each(a.cls_name_summary) kv
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'Detection'
GROUP BY kv.key
ORDER BY total_detections DESC
```

#### 1-F — Recent alerts list — Table

**Panel type:** Table

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    detection_count,
    in_roi_count,
    snapshot_message
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Detection'
ORDER BY timestamp DESC
LIMIT 200
```

---

### 2. Intrusion Detection Report

**alert_category = 'Intrusion'**

#### 2-A — Daily intrusion trend — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    COUNT(*)            AS intrusions,
    SUM(in_roi_count)   AS total_intruders
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
GROUP BY 1
ORDER BY 1
```

#### 2-B — Most triggered cameras — Bar chart

**Panel type:** Bar chart (horizontal)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS intrusion_count,
    MIN(timestamp AT TIME ZONE 'Asia/Jakarta') AS first_seen,
    MAX(timestamp AT TIME ZONE 'Asia/Jakarta') AS last_seen
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
GROUP BY camera_id, camera_name, edge_name
ORDER BY intrusion_count DESC
```

#### 2-C — Peak intrusion hours — Bar chart

**Panel type:** Bar chart

```sql
SELECT
    EXTRACT(HOUR FROM timestamp AT TIME ZONE 'Asia/Jakarta')::integer AS hour,
    COUNT(*)                       AS intrusion_count
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
GROUP BY hour
ORDER BY hour
```

#### 2-D — Intrusion events detail — Table

**Panel type:** Table

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    in_roi_count                               AS intruders_in_roi,
    snapshot_message
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
ORDER BY timestamp DESC
LIMIT 500
```

#### 2-E — Time gap between consecutive alerts per camera — Table

**Panel type:** Table

```sql
SELECT
    camera_id,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    ROUND(
        EXTRACT(EPOCH FROM (
            timestamp - LAG(timestamp) OVER (PARTITION BY camera_id ORDER BY timestamp)
        ))
    )                                          AS seconds_since_last_alert
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
ORDER BY camera_id, timestamp
```

---

### 3. PPE Violations Report

**alert_category = 'PPE'**

#### 3-A — Daily PPE violation trend — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    COUNT(*)                AS violations,
    SUM(in_roi_count)       AS total_non_compliant
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'PPE'
GROUP BY 1
ORDER BY 1
```

#### 3-B — Violations by camera / zone — Bar chart

**Panel type:** Bar chart (horizontal)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS zone,
    edge_name,
    COUNT(*)                       AS violation_count,
    ROUND(AVG(in_roi_count), 1)    AS avg_non_compliant
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'PPE'
GROUP BY camera_id, camera_name, edge_name
ORDER BY violation_count DESC
```

#### 3-C — Non-compliant item breakdown — Pie chart

**Panel type:** Pie chart

```sql
SELECT
    kv.key                         AS ppe_item,
    SUM((kv.value)::integer)       AS total_detected,
    COUNT(DISTINCT a.id)           AS alert_count
FROM app_alert a,
     jsonb_each(a.cls_name_summary) kv
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'PPE'
GROUP BY kv.key
ORDER BY total_detected DESC
```

#### 3-D — Recent PPE violations — Table

**Panel type:** Table

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS zone,
    edge_name,
    rule_name,
    in_roi_count                               AS non_compliant_count,
    snapshot_message
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'PPE'
ORDER BY timestamp DESC
LIMIT 500
```

---

### 4. People Counting Report

**alert_category = 'Counting'**

#### 4-A — Cumulative crossings by camera and rule — Bar chart

**Panel type:** Bar chart

```sql
SELECT
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    rule.key                           AS rule_name,
    SUM((rule.value ->> 'in')::integer)  AS total_in,
    SUM((rule.value ->> 'out')::integer) AS total_out,
    SUM((rule.value ->> 'in')::integer)
      - SUM((rule.value ->> 'out')::integer) AS net_count
FROM app_alert a,
     jsonb_each(a.crossing_counts) rule
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'Counting'
GROUP BY a.camera_id, a.camera_name, a.edge_name, rule.key
ORDER BY total_in DESC
```

#### 4-B — Hourly crossing volume — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    SUM(
        COALESCE((crossing_counts -> rule.key ->> 'in')::integer, 0)
    )                                  AS crossings_in,
    SUM(
        COALESCE((crossing_counts -> rule.key ->> 'out')::integer, 0)
    )                                  AS crossings_out
FROM app_alert a,
     jsonb_object_keys(a.crossing_counts) rule
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'Counting'
GROUP BY 1
ORDER BY 1
```

#### 4-C — Daily entry/exit balance per camera — Time series

**Panel type:** Time series (or Bar chart grouped by camera)

```sql
SELECT
    $__timeGroupAlias(a.timestamp, $__interval),
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    SUM((rule.value ->> 'in')::integer)  AS entries,
    SUM((rule.value ->> 'out')::integer) AS exits,
    SUM((rule.value ->> 'in')::integer)
      - SUM((rule.value ->> 'out')::integer) AS occupancy_change
FROM app_alert a,
     jsonb_each(a.crossing_counts) rule
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'Counting'
GROUP BY 1, a.camera_id, a.camera_name
ORDER BY 1
```

#### 4-D — People count events list — Table

**Panel type:** Table

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    detection_count,
    snapshot_message
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Counting'
ORDER BY timestamp DESC
LIMIT 500
```

---

### 5. Crowd Detection Report

**alert_category = 'Crowd'**

#### 5-A — Daily crowd alerts and peak count — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    COUNT(*)                       AS crowd_alerts,
    MAX(crowd_count)               AS peak_crowd,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Crowd'
GROUP BY 1
ORDER BY 1
```

#### 5-B — Crowd level by camera — Bar chart

**Panel type:** Bar chart (horizontal)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS crowd_alerts,
    MAX(crowd_count)               AS peak_crowd,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd,
    MIN(crowd_count)               AS min_crowd
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Crowd'
GROUP BY camera_id, camera_name, edge_name
ORDER BY peak_crowd DESC
```

#### 5-C — Hourly crowd distribution — Bar chart

**Panel type:** Bar chart

```sql
SELECT
    EXTRACT(HOUR FROM timestamp AT TIME ZONE 'Asia/Jakarta')::integer AS hour,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd_count,
    MAX(crowd_count)               AS peak_crowd_count,
    COUNT(*)                       AS alert_count
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Crowd'
GROUP BY hour
ORDER BY hour
```

#### 5-D — Crowd events exceeding threshold — Table

**Panel type:** Table

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    crowd_count,
    rule_name,
    snapshot_message
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Crowd'
  AND crowd_count >= 10            -- adjust threshold
ORDER BY crowd_count DESC, timestamp DESC
LIMIT 200
```

#### 5-E — Crowd density in 15-minute buckets — Time series

**Panel type:** Time series

```sql
SELECT
    date_trunc('hour', timestamp AT TIME ZONE 'Asia/Jakarta')
      + (FLOOR(EXTRACT(MINUTE FROM timestamp) / 15) * INTERVAL '15 minutes') AS time,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    ROUND(AVG(crowd_count), 1)     AS avg_crowd
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Crowd'
GROUP BY 1, camera_id, camera_name
ORDER BY 1
```

> Note: the aliased column must be named `time` for Grafana time series format.

---

### 6. LPR (License Plate Recognition) Report

**alert_category = 'LPR'**

#### 6-A — Daily LPR detections — Time series

**Panel type:** Time series

```sql
SELECT
    $__timeGroupAlias(timestamp, $__interval),
    COUNT(*) AS detection_events
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'LPR'
GROUP BY 1
ORDER BY 1
```

#### 6-B — All detected plates — Table

**Panel type:** Table

```sql
SELECT
    (a.timestamp AT TIME ZONE 'Asia/Jakarta')  AS local_time,
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    plate ->> 'plate_number'                   AS plate_number,
    plate ->> 'plate_expiry'                   AS expiry,
    ROUND((plate ->> 'plate_confidence')::numeric * 100, 1) AS confidence_pct
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'LPR'
  AND (plate ->> 'plate_number') IS NOT NULL
  AND (plate ->> 'plate_number') <> ''
ORDER BY a.timestamp DESC
LIMIT 1000
```

#### 6-C — Plate frequency (most seen plates) — Table

**Panel type:** Table

```sql
SELECT
    plate ->> 'plate_number'                   AS plate_number,
    COUNT(*)                                   AS appearances,
    MIN(a.timestamp AT TIME ZONE 'Asia/Jakarta') AS first_seen,
    MAX(a.timestamp AT TIME ZONE 'Asia/Jakarta') AS last_seen,
    COUNT(DISTINCT a.camera_id)                AS camera_count
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'LPR'
  AND (plate ->> 'plate_number') <> ''
GROUP BY plate ->> 'plate_number'
ORDER BY appearances DESC
LIMIT 50
```

#### 6-D — Plates by camera — Bar chart

**Panel type:** Bar chart

```sql
SELECT
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    COUNT(DISTINCT plate ->> 'plate_number')   AS unique_plates,
    COUNT(*)                                   AS total_reads
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'LPR'
  AND (plate ->> 'plate_number') <> ''
GROUP BY a.camera_id, a.camera_name, a.edge_name
ORDER BY total_reads DESC
```

#### 6-E — Plate search (uses `$plate_search` variable) — Table

**Panel type:** Table

```sql
SELECT
    (a.timestamp AT TIME ZONE 'Asia/Jakarta')  AS local_time,
    COALESCE(NULLIF(a.camera_name, ''), a.camera_id) AS camera,
    a.edge_name,
    plate ->> 'plate_number'                   AS plate_number,
    plate ->> 'plate_expiry'                   AS expiry,
    ROUND((plate ->> 'plate_confidence')::numeric * 100, 1) AS confidence_pct
FROM app_alert a,
     jsonb_array_elements(a.lpr_plates) plate
WHERE $__timeFilter(a.timestamp)
  AND a.alert_category = 'LPR'
  AND (plate ->> 'plate_number') ILIKE '%${plate_search}%'
ORDER BY a.timestamp DESC
LIMIT 200
```

---

### 7. Cross-Category Dashboard Queries

#### 7-A — Fleet overview — alerts today — Stat panels

**Panel type:** Stat (one panel per category, or use repeat)

```sql
SELECT
    alert_category,
    COUNT(*)                       AS today_count,
    COUNT(DISTINCT camera_id)      AS cameras_affected,
    COUNT(DISTINCT edge_name)      AS edges_affected
FROM app_alert
WHERE timestamp::date = CURRENT_DATE
GROUP BY alert_category
ORDER BY today_count DESC
```

> Use **Panel repeat** with the `$category` variable to generate one Stat card per category automatically.

#### 7-B — Alert volume by edge and category — Time series / Bar chart

**Panel type:** Bar chart (stacked)

```sql
SELECT
    edge_name,
    alert_category,
    COUNT(*) AS alert_count
FROM app_alert
WHERE $__timeFilter(timestamp)
GROUP BY edge_name, alert_category
ORDER BY edge_name, alert_count DESC
```

#### 7-C — Top 10 busiest cameras — Bar chart

**Panel type:** Bar chart (horizontal)

```sql
SELECT
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    COUNT(*)                       AS total_alerts,
    COUNT(DISTINCT alert_category) AS category_count
FROM app_alert
WHERE $__timeFilter(timestamp)
GROUP BY camera_id, camera_name, edge_name
ORDER BY total_alerts DESC
LIMIT 10
```

#### 7-D — 30-minute alert heatmap — Time series

**Panel type:** Time series (or Heatmap)

```sql
SELECT
    date_trunc('hour', timestamp AT TIME ZONE 'Asia/Jakarta')
      + (FLOOR(EXTRACT(MINUTE FROM timestamp) / 30) * INTERVAL '30 minutes') AS time,
    alert_category,
    COUNT(*) AS alert_count
FROM app_alert
WHERE $__timeFilter(timestamp)
GROUP BY 1, alert_category
ORDER BY 1
```

#### 7-E — Alerts this week vs last week — Bar chart (grouped)

**Panel type:** Bar chart

```sql
SELECT
    alert_category,
    COUNT(*) FILTER (WHERE timestamp >= date_trunc('week', NOW()))             AS this_week,
    COUNT(*) FILTER (WHERE timestamp >= date_trunc('week', NOW()) - INTERVAL '7 days'
                       AND timestamp <  date_trunc('week', NOW()))             AS last_week
FROM app_alert
WHERE timestamp >= date_trunc('week', NOW()) - INTERVAL '7 days'
GROUP BY alert_category
ORDER BY this_week DESC
```

#### 7-F — Live alert feed — Table with auto-refresh

**Panel type:** Table (set dashboard refresh to `30s` or `1m`)

```sql
SELECT
    (timestamp AT TIME ZONE 'Asia/Jakarta')    AS local_time,
    alert_category,
    COALESCE(NULLIF(camera_name, ''), camera_id) AS camera,
    edge_name,
    rule_name,
    detection_count,
    snapshot_message
FROM app_alert
WHERE timestamp >= NOW() - INTERVAL '2 hours'
ORDER BY timestamp DESC
LIMIT 100
```

---

### 8. Grafana Dashboard Layout Suggestions

#### Dashboard 1: Fleet Operations Overview

| Panel | Type | Query | Size |
|-------|------|-------|------|
| Total alerts today | Stat | Q7-A `SUM(today_count)` | 2×2 |
| Active cameras | Stat | `COUNT(DISTINCT camera_id) WHERE timestamp::date = CURRENT_DATE` | 2×2 |
| Active edges | Stat | `COUNT(DISTINCT edge_name) WHERE timestamp::date = CURRENT_DATE` | 2×2 |
| Alert trend (time range) | Time series | Q0 | 12×6 |
| Alerts by category today | Bar chart | Q7-A | 6×5 |
| Top cameras | Bar chart (horizontal) | Q7-C | 6×5 |
| Alerts by edge (stacked) | Bar chart | Q7-B | 12×5 |
| 30-min heatmap | Time series | Q7-D | 12×5 |
| This week vs last week | Bar chart | Q7-E | 8×5 |
| Live alert feed | Table | Q7-F | 12×6 |

#### Dashboard 2: Object Detection & Intrusion

| Panel | Type | Query |
|-------|------|-------|
| Detection trend | Time series | Q1-A |
| Intrusion trend | Time series | Q2-A |
| Top detection cameras | Bar chart | Q1-B |
| Peak intrusion hours | Bar chart | Q2-C |
| Top detected classes | Pie chart | Q1-E |
| Hourly detection heatmap | Heatmap | Q1-D |
| Most triggered cameras | Bar chart | Q2-B |
| Recent alerts | Table | Q1-F |

#### Dashboard 3: People Counting & Crowd

| Panel | Type | Query |
|-------|------|-------|
| Crossings IN time series | Time series | Q4-B |
| Crossings by camera/rule | Bar chart | Q4-A |
| Daily entry/exit balance | Time series | Q4-C |
| Daily crowd alerts | Time series | Q5-A |
| Peak crowd by camera | Bar chart | Q5-B |
| Hourly crowd distribution | Bar chart | Q5-C |
| Crowd density (15 min) | Time series | Q5-E |
| Crowd events > threshold | Table | Q5-D |

#### Dashboard 4: PPE & Safety Compliance

| Panel | Type | Query |
|-------|------|-------|
| Daily violations | Time series | Q3-A |
| Violations by zone | Bar chart | Q3-B |
| Missing PPE items | Pie chart | Q3-C |
| Recent violations | Table | Q3-D |

#### Dashboard 5: LPR — License Plate

| Panel | Type | Query |
|-------|------|-------|
| Daily plate reads | Time series | Q6-A |
| Plates by camera | Bar chart | Q6-D |
| Most frequent plates | Table | Q6-C |
| All plate reads | Table | Q6-B |
| Plate search | Table | Q6-E (uses `$plate_search`) |

---

### 9. Grafana Variables & Dashboard Filters

All variables defined above appear as dropdowns at the top of each dashboard.
Link them to panels by adding `AND` clauses:

```sql
-- Edge filter (multi-value — wrap in array)
AND edge_name = ANY(ARRAY[$edge]::text[])

-- Category filter
AND alert_category = ANY(ARRAY[$category]::text[])

-- Camera filter (matches camera_name or camera_id)
AND (
    COALESCE(NULLIF(camera_name, ''), camera_id)
    = ANY(ARRAY[$camera]::text[])
)
```

> When **Include All** is enabled and **All value** is set to `.*`, use `~ '$edge'` (regex match) as an
> alternative. The `ANY(ARRAY[...])` pattern is more reliable for multi-select.

---

### 10. Recommended Indexes

Run once in PostgreSQL (same as Metabase — shared database):

```sql
-- Already created by Django migrations:
-- CREATE INDEX app_alert_edge_name_timestamp ON app_alert (edge_name, timestamp DESC);
-- CREATE INDEX app_alert_alert_category_timestamp ON app_alert (alert_category, timestamp DESC);
-- CREATE INDEX app_alert_camera_id ON app_alert (camera_id);

-- Additional indexes for time-range queries:
CREATE INDEX IF NOT EXISTS idx_alert_ts_date
    ON app_alert (timestamp DESC);

CREATE INDEX IF NOT EXISTS idx_alert_cat_cam_ts
    ON app_alert (alert_category, camera_id, timestamp DESC);

-- Partial index for LPR
CREATE INDEX IF NOT EXISTS idx_alert_lpr
    ON app_alert (timestamp DESC)
    WHERE alert_category = 'LPR';

-- Partial index for crowd_count queries
CREATE INDEX IF NOT EXISTS idx_alert_crowd
    ON app_alert (crowd_count DESC, timestamp DESC)
    WHERE alert_category = 'Crowd';
```

---

### 11. JSON Field Notes

All JSON columns (`cls_name_summary`, `crossing_counts`, `lpr_plates`) are stored as **JSONB** in PostgreSQL.

| Operation | Syntax |
|-----------|--------|
| Get key value | `col ->> 'key'` → text |
| Get nested object | `col -> 'key'` → jsonb |
| Iterate object keys+values | `jsonb_each(col)` → `(key text, value jsonb)` |
| Iterate array elements | `jsonb_array_elements(col)` → `jsonb` |
| Cast to number | `(col ->> 'key')::integer` or `::numeric` |
| Case-insensitive search | `col ->> 'key' ILIKE '%search%'` |

---

### 12. Grafana Tips

#### Time series panels

- Set **Format as: Time series** in the query options
- Enable **Fill value: 0** so gaps show as zero instead of null (avoids broken lines)
- Use `GROUP BY 1` (not column name) when the first column is a `$__timeGroupAlias` macro result

#### Stat panels for KPIs

```sql
-- Total alerts in selected time range
SELECT COUNT(*) AS value
FROM app_alert
WHERE $__timeFilter(timestamp)
  AND alert_category = 'Intrusion'
```

Set **Reduce: Last** and enable **Show: Calculation** in the panel options.

#### Snapshot images in Table panels

Add the `snapshot_path` column to Table panels. In **Override** settings, apply **Data links**:

- Title: `View Snapshot`
- URL: `https://storage.googleapis.com/soca-snapshot-bucket/${__value.text}` (for GCS)

This makes each row clickable to open the snapshot image.

#### Auto-refresh for live monitoring

In Dashboard Settings → Time options:
- Set **Auto-refresh** options to include `10s`, `30s`, `1m`
- Set default refresh on the live feed dashboard to `30s`

#### Alerting

Grafana supports native alerts on any panel. Recommended alert rules:

| Alert | Query | Condition |
|-------|-------|-----------|
| Intrusion spike | Q2-A | `intrusions > 5` in last 5 min |
| Crowd threshold exceeded | Q5-A | `peak_crowd > 20` in last 5 min |
| High PPE violation rate | Q3-A | `violations > 10` in last 15 min |
| LPR camera offline | Q6-A | `detection_events = 0` for 30 min |

### Cython Protection

_Source: `deployment/cython-protection-guide.md`_

## SOCA — Cython Source Protection Guide

**Purpose**: Protect soca-dashboard and soca-engine Python source code for commercial client distribution by compiling `.py` files into native binary extensions using Cython.

---

### How It Works

Cython compiles `.py` source files into C code, which is then compiled into a native binary extension:

- **Linux / macOS** → `.so` file (e.g. `views.cpython-311-x86_64-linux-gnu.so`)
- **Windows** → `.pyd` file (e.g. `views.cp311-win_amd64.pyd`)

Python imports these binaries exactly like normal `.py` modules — no code changes are needed in the application. The original source is not shipped to the client and cannot be practically recovered from the compiled binary.

---

### What Is Protected vs. What Is Not

| Protected ✓ | Not Protected ✗ |
|-------------|-----------------|
| All `.py` logic (views, models, workers, core) | Django HTML templates (`templates/`) |
| Business rules and algorithms | `.env` / `config.json` / `mediamtx.yml` |
| API endpoint definitions | `app/migrations/*.py` — must stay plain (Django reads them as text) |
| ML inference pipeline | `__init__.py` files — must stay for Python package resolution |
| Detection worker logic | Static files, media, model weights (`.pt`) |

---

### Prerequisites

Install build tools on the build machine:

```bash
pip install cython setuptools
```

On **Windows**, also install the **Microsoft C++ Build Tools** (MSVC):
- Download from: https://visualstudio.microsoft.com/visual-cpp-build-tools/
- Select "Desktop development with C++"

On **Linux**, install gcc:
```bash
sudo apt install gcc python3-dev   # Debian/Ubuntu
sudo yum install gcc python3-devel # RHEL/CentOS
```

---

### Build Script — soca-engine

Create `setup.py` in the `soca-engine/` root:

```python
from setuptools import setup, Extension
from Cython.Build import cythonize
import glob
import os

EXCLUDE = {"migrations", "__init__", "tests", "setup"}

def collect_modules():
    extensions = []
    for path in glob.glob("**/*.py", recursive=True):
        parts = path.replace("\\", "/").split("/")
        name = os.path.splitext(parts[-1])[0]
        if any(x in parts for x in EXCLUDE) or name in EXCLUDE:
            continue
        module_name = path.replace("/", ".").replace("\\", ".")[:-3]
        extensions.append(Extension(module_name, sources=[path]))
    return extensions

setup(
    ext_modules=cythonize(
        collect_modules(),
        compiler_directives={"language_level": "3"},
        nthreads=4,
    )
)
```

Run the build:
```bash
cd soca-engine
python setup.py build_ext --inplace
```

---

### Build Script — soca-dashboard

Create `setup.py` in the `soca-dashboard/` root:

```python
from setuptools import setup, Extension
from Cython.Build import cythonize
import glob
import os

# Migrations must stay as plain .py — Django reads them as source text
EXCLUDE_DIRS  = {"migrations", "tests", "__pycache__"}
EXCLUDE_FILES = {"setup", "manage"}

def collect_modules():
    extensions = []
    for path in glob.glob("**/*.py", recursive=True):
        parts = path.replace("\\", "/").split("/")
        name = os.path.splitext(parts[-1])[0]
        if any(x in parts for x in EXCLUDE_DIRS) or name in EXCLUDE_FILES:
            continue
        if name == "__init__":
            continue
        module_name = path.replace("/", ".").replace("\\", ".")[:-3]
        extensions.append(Extension(module_name, sources=[path]))
    return extensions

setup(
    ext_modules=cythonize(
        collect_modules(),
        compiler_directives={"language_level": "3"},
        nthreads=4,
    )
)
```

Run the build:
```bash
cd soca-dashboard
python setup.py build_ext --inplace
```

---

### Packaging for Distribution

After building, remove source files and ship only the binaries:

```bash
# Remove .py source files (keep __init__.py and migrations)
find . -name "*.py" \
  ! -name "__init__.py" \
  ! -path "*/migrations/*" \
  ! -name "manage.py" \
  ! -name "setup.py" \
  -delete

# Remove the generated C files (not needed after compilation)
find . -name "*.c" -delete
find . -name "build" -type d -exec rm -rf {} +
```

#### Distribution package structure

```
soca-engine-client/
├── main.py                          ← keep: startup entry point
├── config.json                      ← client-specific config
├── .env                             ← client-specific: EDGE_NAME, API keys
├── yolo/                            ← model weight files (.pt)
├── snapshots/                       ← created at runtime
├── requirements.txt
├── core/
│   ├── __init__.py                  ← keep
│   ├── yolo_inference.*.so          ← compiled binary
│   ├── lpr_engine.*.so
│   └── ...
├── api/
│   ├── __init__.py
│   └── *.so
└── workers/
    └── *.so

soca-dashboard-client/
├── manage.py                        ← keep
├── requirements.txt
├── templates/                       ← HTML files (not compiled)
├── media/                           ← uploaded files
├── app/
│   ├── migrations/                  ← keep ALL migration .py files
│   ├── __init__.py                  ← keep
│   └── *.so                         ← compiled binaries
└── dashboard/
    ├── __init__.py
    └── *.so
```

---

### Platform-Specific Compilation

Compiled binaries are **OS and Python version specific**. You must compile separately for each target platform.

| Client OS | Build machine | Output extension |
|-----------|--------------|-----------------|
| Linux x86_64 | Linux x86_64 (or Docker) | `.cpython-311-x86_64-linux-gnu.so` |
| Windows 64-bit | Windows 64-bit + MSVC | `.cp311-win_amd64.pyd` |
| macOS ARM (M1/M2/M3) | Mac with Apple Silicon | `.cpython-311-arm-darwin.so` |
| macOS Intel | Mac Intel | `.cpython-311-x86_64-darwin.so` |

#### Docker build for Linux (recommended for CI)

```dockerfile
# Dockerfile.build
FROM python:3.11-slim
RUN apt-get update && apt-get install -y gcc python3-dev
WORKDIR /build
COPY . .
RUN pip install cython setuptools
RUN python setup.py build_ext --inplace
```

```bash
docker build -f Dockerfile.build -t soca-build .
docker run --rm -v $(pwd)/dist:/output soca-build \
  bash -c "find . -name '*.so' | tar -czf /output/soca-engine-linux.tar.gz -T -"
```

---

### Important Constraints

1. **Python version must match** — binaries compiled for Python 3.11 will not load on Python 3.12. Pin the Python version in your client deployment.

2. **Migrations must stay plain** — never compile `app/migrations/*.py`. Django's migration runner reads them as source text at runtime.

3. **`__init__.py` must stay** — Python requires these to recognise directories as packages. They are typically empty or trivial.

4. **Templates are not protected** — Django HTML templates remain readable. This is unavoidable with Django's template engine. Consider minification as a partial measure.

5. **Not 100% unbreakable** — Cython binaries can be partially reverse-engineered using low-level binary analysis tools, but the effort is prohibitive for most users. It is a strong deterrent, not a cryptographic lock.

6. **Recompile for each release** — if you update the source code, you must recompile and redistribute new binaries to clients.

---

### Optional: License Key Enforcement

For stricter protection, add a license check inside a compiled module that validates against your licensing server at startup. Since the module is compiled, clients cannot bypass the check by editing source:

```python
# core/license.py  (will be compiled — clients cannot edit this)
import requests

def validate(license_key: str, edge_name: str):
    resp = requests.post("https://your-license-server.com/validate", json={
        "key": license_key,
        "edge": edge_name,
    }, timeout=5)
    if resp.status_code != 200 or not resp.json().get("valid"):
        raise RuntimeError("Invalid or expired license key. Contact support.")
```

Call `validate()` in `main.py` at startup before the FastAPI app starts serving.

---

### Summary Checklist

- [ ] Install Cython + build tools on build machine
- [ ] Create `setup.py` for soca-engine
- [ ] Create `setup.py` for soca-dashboard
- [ ] Run `python setup.py build_ext --inplace` on each target platform
- [ ] Verify compiled `.so`/`.pyd` files load correctly (`python -c "import app.views"`)
- [ ] Strip `.py` source files (keep migrations, `__init__.py`, `manage.py`)
- [ ] Package with client-specific `.env` and `config.json`
- [ ] Test full application startup with compiled binaries before shipping

## 6. User Guides

### Guide Overview

_Source: `guides/user-guides.md`_

## User Guide Overview

The current documentation is strongest on architecture and implementation detail. This section organizes those materials into operator-facing guidance.

### Main Interfaces

#### soca-dashboard

Use these pages when working on a single edge site:

- [Dashboard Design Spec](../soca-dashboard/design-spec.md)
- [Dashboard Edge Operations Plan](../soca-dashboard/edge-operations-plan.md)
- [Dashboard Edge Operations Design](../soca-dashboard/edge-operations-design.md)
- [Dashboard Rule Management Plan](../soca-dashboard/rule-management-plan.md)
- [Dashboard Advanced Rule Management](../soca-dashboard/advanced-rule-management-design.md)

Primary tasks:

- Configure cameras and schedules
- Manage rules and edge behavior
- Push runtime settings to `soca-engine`

#### soca-control

Use these pages when working across multiple edges or centralized reporting:

- [Control Design Spec](../soca-control/design-spec.md)
- [Control Edge Operations Plan](../soca-control/edge-operations-plan.md)
- [Control Implementation Plan](../soca-control/implementation-plan.md)
- [Control Pub/Sub Transport](../soca-control/pubsub-transport-plan.md)

Primary tasks:

- Manage fleet-level visibility
- Review alerts and reports
- Coordinate centralized edge operations

### Suggested Next Improvement

To make this section truly user-facing, the next content to add should be task-based walkthroughs:

- Add first camera
- Create first schedule
- Create first detection rule
- Start and stop a job
- Review alerts and reports
- Troubleshoot a failed stream

### Dashboard Design Spec

_Source: `soca-dashboard/design-spec.md`_

## SOCA Dashboard — Technical Document

### Context

`soca-engine` is a FastAPI service that runs YOLO-based object detection jobs on RTSP camera streams. It stores results in SQLite and exposes a REST API. There is no visual interface — operators cannot see what the engine is doing, manage jobs, or review detection events without curling the API manually. This dashboard solves that.

---

### Problem Statement

Operators running soca-engine on edge devices need a web UI to:
- Know if the engine is healthy and what it is doing right now
- Start/stop detection jobs on cameras
- Watch a live annotated stream from a running job
- Review detection events and snapshots after the fact

---

### soca-engine API Surface (consumed by dashboard)

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

### MVP Features

1. **Health bar** — always-visible strip: engine status, redis, active jobs, uptime
2. **Job list** — table of last 50 jobs with status badge and stop button
3. **Start job form** — minimal form: camera_id, rtsp_url, model selector (from `/models`), cls_ids, frame_interval, enable monitor toggle
4. **Job detail page** — job counters (frames, events, last_frame_at) + live MJPEG viewer (if monitor enabled)
5. **Model list** — sidebar or simple page showing available YOLO models on the engine

**Out of MVP**: ROI drawing, rule builder, event log (no API endpoint yet), multi-engine support, auth

---

### User Flow

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

### Django Project Structure

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

### Key Implementation Rules

- `client.py`: all `requests` calls in one file, each function maps 1:1 to an API endpoint, raises on HTTP error
- `views.py`: one view per page, thin — call client, pass data to template, done
- `forms.py`: single `StartJobForm` with clean validation only
- Templates: extend `base.html`, no inline JS, semantic HTML
- MJPEG proxy: streaming `HttpResponse` that reads chunks from engine and yields them — no buffering
- Engine base URL from `settings.ENGINE_BASE_URL` (env var `ENGINE_BASE_URL`, default `http://localhost:8001`)
- `requests.Session` reused in `client.py` (module-level singleton)

---

### Files Created

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

### Verification

```bash
# 1. Start engine (in soca-engine/)
uvicorn main:app --port 8001

# 2. Start dashboard (in soca-dashboard/)
pip install -r requirements.txt
python manage.py runserver 8000

# 3. Visit http://localhost:8000
#    - Health bar shows engine status
#    - Start a job with monitor=true
#    - Navigate to job detail — MJPEG stream renders
#    - Stop job — status changes to stopped
```

---

---

### Sprint 2 — Added Features

#### Authentication
- Django built-in `LoginView` / `LogoutView`
- All views protected with `@login_required`
- Custom login page: `templates/registration/login.html`
- Create user: `python manage.py createsuperuser`
- Settings: `LOGIN_URL`, `LOGIN_REDIRECT_URL`, `LOGOUT_REDIRECT_URL`

#### Cameras App (`cameras/`)
- **Model**: `Camera` — name, site_name, floor, location, rtsp_url, username, password, camera_source, is_active
- **Views**: list, create, edit, delete (all `@login_required`)
- **URLs**: `/cameras/`, `/cameras/new/`, `/cameras/<pk>/edit/`, `/cameras/<pk>/delete/`
- **DB**: stored in `dashboard.db` (Django SQLite)

#### Schedules App (`schedules/`)
- **Model**: `DetectionSchedule` — FK to Camera, model_path, cls_ids (JSON), frame_interval_ms, monitor, is_active, current_job_id
- **Views**: list (filterable by camera), create, edit, delete, start, stop
- **Start** → calls `client.start_job()` with camera's rtsp_url + schedule config, stores `current_job_id`
- **Stop** → calls `client.stop_job(current_job_id)`, clears `current_job_id`
- **URLs**: `/schedules/`, `/schedules/new/`, `/schedules/<pk>/edit/`, `/schedules/<pk>/start/`, `/schedules/<pk>/stop/`

#### Snapshots App (`snapshots/`)
- **Source**: reads directly from `ENGINE_DB_PATH` (`soca-engine/soca_engine.db`) via `sqlite3` stdlib
- **View**: paginated grid (30/page), filterable by job_id
- **Image serving**: `/snapshots/img/<path>` — reads file from `ENGINE_SNAPSHOTS_DIR`, prevents path traversal
- **No model** — read-only access to engine DB

#### Engine Health Context Processor (`monitor/context_processors.py`)
- `engine_health(request)` — called on every request, injects `health` dict into all templates
- Registered in `settings.TEMPLATES.context_processors`

#### New Settings
```
ENGINE_DB_PATH          # path to soca-engine/soca_engine.db
ENGINE_SNAPSHOTS_DIR    # path to soca-engine/snapshots/
```

#### Run
```bash
python manage.py migrate
python manage.py createsuperuser
ENGINE_BASE_URL=http://localhost:8001 python manage.py runserver 8000
```

---

### Post-MVP Backlog

- **Event log page** — requires soca-engine to expose `/jobs/{id}/events` endpoint
- **ROI drawing** — canvas-based polygon editor posted with job config
- **Rule builder** — form UI for `when_all`/`when_any` conditions
- **Snapshot gallery** — browse `snapshots/` directory served by engine
- **Multi-engine** — ENGINE_BASE_URL as a per-session setting
- **Auth** — Django sessions + basic login

---

### Sprint 3 — Full Rebuild as Administration Dashboard

Sprint 3 is a complete rewrite of soca-dashboard into a production-grade administration UI for soca-engine. The old `monitor/`, `cameras/`, and `schedules/` multi-app structure is replaced by a single `app/` Django application with a 5-item left sidebar.

#### Architecture Changes

##### Project Layout

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

##### settings.py

- `AUTH_USER_MODEL = "app.User"` — custom user model required from the start
- `LOGIN_URL = "/login/"`, `LOGIN_REDIRECT_URL = "/"`, `LOGOUT_REDIRECT_URL = "/login/"`
- New settings:
  ```python
  ENGINE_BASE_URL      # default "http://localhost:8001"
  ENGINE_DB_PATH       # path to soca-engine/soca_engine.db
  ENGINE_SNAPSHOTS_DIR # path to soca-engine/snapshots/
  MEDIAMTX_YML_PATH    # kept for legacy; overridden by EdgeConfig.mediamtx_yml_path
  ```
- Removed old `monitor`, `cameras`, `schedules` from `INSTALLED_APPS`; added `app`

---

#### Models (`app/models.py`)

##### `User(AbstractUser)`
Plain custom user model for future extensibility.

##### `EdgeConfig`
Stores edge-device connection config. One row is expected.

| Field | Default | Notes |
|-------|---------|-------|
| `edge_name` | `"edge-1"` | Written to `EDGE_NAME` in soca-engine `.env` on save |
| `engine_url` | `"http://localhost:8001"` | soca-engine FastAPI base URL |
| `mediamtx_url` | `"http://localhost:8888"` | MediaMTX HTTP API URL |
| `mediamtx_yml_path` | `"../soca-engine/MediaMTX/mediamtx.yml"` | Relative to `BASE_DIR` |
| `engine_env_path` | `"../soca-engine/.env"` | Relative to `BASE_DIR`; EDGE_NAME written here |
| `updated_at` | auto | — |

##### `Camera`
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

##### `Schedule`
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

#### `mediamtx.py` — MediaMTX YAML Manager

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

#### `engine_client.py` — soca-engine HTTP Wrapper

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

#### `views.py` — Key Behaviours

##### Path Resolution Helpers
```python
def _yml_path(cfg):   # mediamtx_yml_path → absolute, anchored to BASE_DIR
def _env_path(cfg):   # engine_env_path → absolute, anchored to BASE_DIR
```

Both resolve relative paths against `settings.BASE_DIR` (the `soca-dashboard/` directory). This makes paths CWD-independent regardless of where `manage.py runserver` is invoked from.

##### `_write_engine_env(engine_env_path, edge_name)`
Reads the soca-engine `.env` file, updates or appends `EDGE_NAME=<value>`, writes back. Called on every EdgeConfig save.

##### `camera_snapshot(pk)`
Grabs a single OpenCV frame from `camera.full_rtsp_url`, returns JPEG `HttpResponse`. Used as the ROI canvas background in the schedule form.

##### `schedule_stop`
Calls `engine_client.stop_job()`. If the engine returns 404 (job already dead), the error is ignored and `current_job_id` is cleared anyway. This prevents schedules from getting stuck in "running" state after an engine restart.

##### `alerts` view
Reads soca-engine SQLite directly via `sqlite3` stdlib (no ORM). Queries `detection_events` joined to `detection_jobs` for camera name. Paginated, filterable by camera and date.

---

#### Templates

##### ROI Canvas Editor (`schedules/form.html`)

- HTML5 `<canvas>` at 640×360 pixels
- On camera dropdown change: `loadSnapshot(cameraId)` → fetches `/cameras/{id}/snapshot/` → draws as canvas background
- Points are stored as **normalized coordinates** (0.0–1.0):
  - On click: pixel coords divided by canvas width/height before appending to `points[]`
  - On page load: existing normalized points multiplied by canvas dimensions for display
  - On form submit: `storeNormalized()` writes JSON to hidden `<input>`
- Init sequence: calls `draw()` directly — does **not** call `setRoiMode()` (which would reset `points[]`)
- Supports both POLYGON (click to place vertices) and RECT (drag rectangle) modes

##### Schedule List (`schedules/list.html`)

- Monitor link is conditionally rendered:
  ```django
  {% if s.job_status == "running" and s.enable_monitor %}
    <a href="{{ engine_url }}/jobs/{{ s.current_job_id }}/monitor">Live</a>
  {% endif %}
  ```
- `engine_url` is passed from `schedule_list` view via `EdgeConfig.engine_url`

##### Settings (`settings/index.html`)

- Edge Config form includes `engine_env_path` field with hint: "EDGE_NAME will be written here when you save"
- User management section: create/delete users

---

#### URL Patterns (`app/urls.py`)

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

#### Bug Fixes Applied During Sprint 3

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

#### Dependencies Added

| Package | Version | Purpose |
|---------|---------|---------|
| `ruamel.yaml` | `>=0.18` | MediaMTX YAML editing preserving comments |

---

#### Run (Sprint 3)

```bash
# Install dependencies (in soca-dashboard venv)
pip install -r requirements.txt

# Apply migrations
python manage.py migrate

# Create superuser
python manage.py createsuperuser

# Start dashboard
python manage.py runserver 8000

# Verify edge config at http://localhost:8000/settings/edge/
# Set engine_url, mediamtx_yml_path, engine_env_path to correct absolute/relative paths
```

---

### Sprint 4 — Alert Categories, Message Templates, Telegram & Redis Schema 2.2

#### Overview

Sprint 4 adds per-schedule alert enrichment (category label, message template), a global Telegram Bot configuration, and ensures all new metadata flows through to the Redis stream payload.

---

#### New `Schedule` Fields (`app/models.py`)

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

#### New `EdgeConfig` Fields (`app/models.py`)

| Field | Type | Default | Notes |
|-------|------|---------|-------|
| `telegram_bot_token` | `CharField(max_length=200)` | `""` | Bot token from @BotFather |
| `telegram_chat_id` | `CharField(max_length=100)` | `""` | Group/channel (negative) or personal ID |

These serve as the **global Telegram config** shared by all schedules that don't set a per-schedule `telegram_webhook_url`.

---

#### `Schedule.to_job_config()` — Telegram Action Logic

Priority order for outbound Telegram notifications:

1. If `schedule.telegram_webhook_url` is set → add `webhook` action with the URL and `message_template`
2. Else if `EdgeConfig.telegram_bot_token` and `telegram_chat_id` are set → add `telegram` action with global credentials and `message_template`
3. Neither → no Telegram notification

The `telegram` action in `JobConfig` triggers the engine's `_fire_telegram()` which calls `https://api.telegram.org/bot{token}/sendMessage` directly with the formatted text.

The `alert_category` is passed as `rule.category` in the generated job config.

---

#### `app/views.py` Changes

- **`schedule_form`**: saves `alert_category` (truncated to 255 chars) and `snapshot_message`
- **`edge_settings`**: saves `telegram_bot_token` and `telegram_chat_id`
- **`alerts` view**: timestamps from `detection_events` (stored UTC) are converted to **GMT+7** before rendering:
  ```python
  dt.astimezone(timezone(timedelta(hours=7))).strftime("%Y-%m-%d %H:%M:%S")
  ```

---

#### Template Changes

##### `schedules/form.html`

**Section 4 — Aging & Alert Rules** updated:
- Aging Window and Cooldown labels now include `seconds` unit badge and clarifying hints: "How long a detection stays active" / "Minimum gap between consecutive alerts"
- New **Alert Category** text input (max 255 chars, placeholder: `e.g. Intrusion, Crowd, Vehicle`)

**Section 5 — Output & Notifications** updated:
- Telegram Webhook URL hint updated to explain relationship with global config
- New **Alert Message Template** `<textarea>` with inline placeholder reference:
  - `{count}` · `{in_roi_count}` · `{time}` · `{camera_id}` · `{category}`

##### `settings/index.html`

- Added **Telegram** tab (third tab alongside Users and Edge Config)
- Telegram tab form posts to `{% url 'edge_settings' %}` with hidden fields preserving existing EdgeConfig values
- Two new fields: **Bot Token** and **Chat ID** with descriptive hints
- Note explaining that per-schedule webhook URL takes priority over this global config

---

#### Migrations

Migration `0003_edgeconfig_telegram_bot_token_and_more.py` adds:
- `telegram_bot_token` to `EdgeConfig`
- `telegram_chat_id` to `EdgeConfig`
- `alert_category` to `Schedule`
- `snapshot_message` to `Schedule`

---

#### New `Schedule` Field — `conf_threshold`

| Field | Type | Default | Notes |
|-------|------|---------|-------|
| `conf_threshold` | `FloatField` | `0.5` | YOLO detection confidence threshold passed to soca-engine |

- Rendered in `schedules/form.html` as a number input (min 0.01, max 1.0, step 0.05)
- Passed to `JobConfig.conf_threshold` in `to_job_config()`
- Clamped to `0.01–1.0` range in `schedule_form` view before saving

---

#### Alerts & Snapshots Template Changes (`alerts/list.html`)

- **Removed columns**: Rule, Job ID
- **Added columns**: Category (badge), Message
- **Category** displayed as `badge-info` badge
- **Message** column shows `snapshot_message` text
- **Snapshot thumbnail** has `title="{{ ev.snapshot_message }}"` tooltip for quick preview
- Column header updated to **Timestamp (GMT+7)**

---

#### Dashboard — Running Schedules Only

`dashboard` view filters job list to running state only:
```python
jobs = [j for j in engine_client.list_jobs() if j.get("status") == "running"]
```
- Section renamed from "Active Jobs" to "Running Schedules"
- Shows 0 rows when no jobs are running rather than completed/stopped history

---

#### Data Flow Summary

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

### Sprint 5 — Monitor Page & Dashboard Refinements

#### Monitor Page (`/monitor/`)

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

#### Stream Rendering

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

#### URL Pattern Added

| URL | View | Notes |
|-----|------|-------|
| `/monitor/` | `monitor_view` | Grid of live MJPEG stream cards |

---

### Sprint 6 — System Resources, Theme Toggle, Edge Site Info & Public API

#### Theme Toggle

- Sun/moon icon button added to the navbar (next to username)
- Clicking toggles between `data-theme="dark"` and `data-theme="light"` on `<html>`
- Preference persisted in `localStorage` and restored on every page load
- No server-side state — pure client JS in `base.html`

---

#### System Resources on Dashboard

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

#### `GET /api/edge-info/` — Public Edge Identity API

No authentication required. Intended for consumption by a central management server.

**URL:** `GET /api/edge-info/`

**Response:**
```json
{
  "edge_name": "edge-bardi-home",
  "engine_url": "http://localhost:8001",
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

#### New `EdgeConfig` Fields (migration `0005`)

| Field | Type | Notes |
|-------|------|-------|
| `latitude` | `FloatField(null=True)` | GPS latitude |
| `longitude` | `FloatField(null=True)` | GPS longitude |
| `address` | `CharField(500)` | Street address |
| `building` | `CharField(200)` | Building name |
| `floor` | `CharField(50)` | Floor identifier |
| `site_notes` | `TextField` | Free-form notes |

---

#### Settings Page — Edge Config Tab

Reorganised into two sections:

**Connection** (existing fields): Edge Name, Engine URL, MediaMTX URL, MediaMTX Config Path, Engine .env Path

**Site Information** (new fields): Address, Building, Floor, Latitude, Longitude, Notes. Section header notes the `/api/edge-info/` URL so operators know where the data is exposed.

Telegram tab hidden fields updated to preserve new location fields when saving from that tab.

---

#### Alerts Auto-Reload

- Toggle switch added to Alerts & Snapshots header (off by default)
- When enabled: 30-second countdown shown inline, page reloads at 0
- Dead empty `{% for %}` loop in pagination removed

---

#### Monitor Page — Fixes & Auto-Reload

- Stream `<img>` gets `?t={timestamp}` cache-buster on page load — forces fresh MJPEG connection when navigating back to the page
- `onerror` retries every 3 seconds with new timestamp
- Page auto-reloads every 30 seconds with visible countdown (`Refreshing in Ns`) to pick up newly started/stopped streams

---

#### URL Patterns Added

| URL | View | Notes |
|-----|------|-------|
| `/api/edge-info/` | `edge_info` | Public — no auth, for main server |

---

### Sprint 7 — Google Pub/Sub Publisher Transport (2026-03-25)

#### Overview

Added Pub/Sub as an alternative to Redis for publishing detection events from soca-engine. The transport is configurable per-edge via the Edge Config settings tab, and the choice is written directly to `soca-engine/.env`.

---

#### New `EdgeConfig` Fields (`app/models.py`)

| Field | Type | Default | Notes |
|-------|------|---------|-------|
| `publisher_type` | `CharField(10)` | `'redis'` | `'redis'` or `'pubsub'` |
| `pubsub_project_id` | `CharField(200)` | `''` | GCP project ID |
| `pubsub_topic` | `CharField(200)` | `'soca-detections'` | Topic name only (not full path) |

Migration: `app/migrations/0017_edgeconfig_pubsub.py`

---

#### `_write_engine_env()` — Updated Signature

```python
def _write_engine_env(engine_env_path, edge_name, publisher_type="redis",
                      pubsub_project_id="", pubsub_topic="soca-detections"):
```

Now writes four vars to soca-engine `.env`: `EDGE_NAME`, `PUBLISHER_TYPE`, `PUBSUB_PROJECT_ID`, `PUBSUB_TOPIC`. Uses a dict-based loop to update-or-append each key, preserving all other existing `.env` lines.

---

#### Settings UI (`templates/settings/index.html`)

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

### Dashboard Edge Operations Plan

_Source: `soca-dashboard/edge-operations-plan.md`_

## Edge Operations — soca-dashboard Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add purge tool, API v1 (authenticated schedule control + purge endpoints), and Settings > Operations tab to soca-dashboard.

**Architecture:** New fields on `EdgeConfig` store the API key, engine DB path, and snapshots root. Shared helper functions contain the purge logic. Two sets of views expose the same operations: session-auth views for the browser UI, and API-key-auth views under `/api/v1/` for soca-control. A new 4th tab "Operations" in Settings provides the UI.

**Tech Stack:** Django 6.x, SQLite (sqlite3 stdlib), DaisyUI 4, vanilla JS fetch API, secrets stdlib

**Spec:** `docs/superpowers/specs/2026-03-22-edge-operations-design.md`

---

### File Map

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

### Task 1: Model Migration

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

### Task 2: API Key Auth Decorator

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

### Task 3: Purge Helpers

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

### Task 4: Views — Session-Auth Operations + API v1

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

### Task 5: URL Patterns

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

### Task 6: Settings Template

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
curl -s -o /dev/null -w "%{http_code}" http://localhost:8000/settings/ -H "Cookie: sessionid=..." || echo "manual check needed"
```

- [ ] **Step 7: Commit**

```bash
git add templates/settings/index.html app/views.py
git commit -m "feat: add Settings > Operations tab (API key, purge, schedule control)"
```

---

### Task 7: Manual End-to-End Verification

- [ ] Log in to soca-dashboard at `http://localhost:8000`
- [ ] Go to **Settings > Edge Config**: fill in `Engine DB Path` and `Snapshots Root`, save
- [ ] Go to **Settings > Operations**: click **Generate Key** → key appears masked
- [ ] Click **Reveal** → key shows in plain text; **Copy** copies to clipboard
- [ ] Select "Older than 1 month", click **Preview** → counts appear (even if 0)
- [ ] Verify **Purge Now** button enables after preview
- [ ] Click **Purge Now** → confirmation modal appears → cancel → nothing deleted
- [ ] Schedule Control table loads and refreshes every 10s
- [ ] Test API: `curl -H "Authorization: Api-Key <key>" http://localhost:8000/api/v1/schedules/` → JSON list
- [ ] Test bad key: `curl -H "Authorization: Api-Key wrong" http://localhost:8000/api/v1/schedules/` → `{"error": "Invalid or missing API key"}`

```bash
kill %1 2>/dev/null  # stop dev server
```

- [ ] **Final commit**

```bash
git add -A
git commit -m "feat: edge operations complete (purge, API v1, operations settings tab)"
```

### Dashboard Edge Operations Design

_Source: `soca-dashboard/edge-operations-design.md`_

## Edge Operations Design Spec
**Date:** 2026-03-22
**Project:** soca-dashboard + soca-control

### Context

soca-dashboard manages a single edge device. As deployments grow and data accumulates, operators need:
1. A way to purge aging detection data and free disk space
2. A machine-readable API so soca-control can remotely manage schedules and trigger purges
3. A dedicated UI section in Settings to expose these operations

---

### Scope

Three coordinated deliverables:

| Deliverable | Location |
|-------------|----------|
| Purge tool (local + remote) | soca-dashboard |
| REST API v1 for soca-control | soca-dashboard |
| Settings > Operations tab | soca-dashboard |
| Schedule remote control + purge trigger | soca-control |

---

### 1. Data Model Changes

#### soca-dashboard: `EdgeConfig`

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

#### soca-control: `Edge`

Add one field:

```python
api_key = models.CharField(max_length=64, blank=True, default='')
# Operator pastes this from the target edge's Settings > Operations tab
```

---

### 2. Purge Logic

#### What Gets Purged (soca-dashboard)

soca-dashboard reads detection data directly from **soca-engine's SQLite** file at `EdgeConfig.engine_db_path`. Purge targets:

1. **`detection_events` rows** where `created_at < cutoff_date`
2. **Snapshot image files** on disk: for each matching row, derive the absolute path as `{EdgeConfig.snapshots_root}/{row.snapshot_path}`. Delete the file if it exists; skip silently if missing.

**Partial failure handling:** DB rows are deleted first in a single transaction. File deletion runs after commit — errors on individual files are logged and skipped (no rollback possible). The response reports `deleted_records`, `deleted_files`, and `skipped_files` (count of missing/unreadable files).

**`freed_bytes` computation:** File sizes are summed **before** deletion. Files that are skipped (missing or unreadable) are **excluded** from `freed_bytes`.

**Path safety:** Before deleting any file, validate that its resolved absolute path starts with `EdgeConfig.snapshots_root`. Skip any file that fails this check (path traversal guard).

#### Purge Age Thresholds

| Label | `older_than` value | Cutoff |
|-------|-------------------|--------|
| Older than 1 month | `30` | `now - 30 days` |
| Older than 3 months | `90` | `now - 90 days` |
| Older than 6 months | `180` | `now - 180 days` |
| Older than 1 year | `365` | `now - 365 days` |

#### Purge Steps

1. **Preview** — query count of matching rows + sum disk size of snapshot files → return JSON `{record_count, file_count, disk_bytes}`
2. **Execute** — delete rows, delete files (skip missing), return `{deleted_records, deleted_files, skipped_files, freed_bytes}`

Both steps are implemented as Django view functions callable via the UI and the API.

#### What Gets Purged (soca-control)

soca-control has its own `Alert` table. Purge targets `Alert` rows filtered by `created_at < cutoff_date` (and optionally by `edge` FK). This is handled by soca-control's own view (`edge_purge_local`) — it does not go through soca-dashboard's API.

---

### 3. API Design (`/api/v1/`)

#### Authentication

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

#### Standard Error Responses

All endpoints return JSON. On error:

| Situation | Status | Body |
|-----------|--------|------|
| Missing/invalid API key | 403 | `{"error": "Invalid or missing API key"}` |
| Schedule not found | 404 | `{"error": "Schedule not found"}` |
| soca-engine unreachable (start/stop) | 502 | `{"error": "Engine unreachable"}` |
| engine_db_path not configured | 503 | `{"error": "Engine DB path not configured"}` |
| Unexpected server error | 500 | `{"error": "Internal error", "detail": "..."}` |

#### Endpoints

##### `GET /api/v1/schedules/`
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

##### `POST /api/v1/schedules/<id>/start/`
Starts the schedule (calls soca-engine, stores job_id). Returns:
```json
{"status": "started", "job_id": "550e8400-..."}
```

If schedule has no camera or is_active=False → `400 {"error": "Schedule not startable"}`.

##### `POST /api/v1/schedules/<id>/stop/`
Stops the running job. Returns:
```json
{"status": "stopped"}
```

If no job is running → returns `{"status": "already_stopped"}` (not an error).

##### `GET /api/v1/purge/preview/?older_than=<days>`
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

##### `POST /api/v1/purge/execute/`
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

### 4. soca-dashboard: Settings > Operations Tab

The existing Settings page gains a **4th tab: Operations**.

The browser UI calls **separate, session-authenticated view functions** (not the `/api/v1/` endpoints) for all Operations tab actions. This avoids the need to handle both session and API key auth in the same view.

| UI action | View function | URL |
|-----------|--------------|-----|
| Generate API key | `generate_api_key` | `POST /settings/edge/generate-key/` |
| Preview purge | `purge_preview_view` | `GET /settings/operations/purge/preview/` |
| Execute purge | `purge_execute_view` | `POST /settings/operations/purge/execute/` |
| List schedules (poll) | `schedule_status_all` | `GET /settings/operations/schedules/` |

These views share the same underlying logic as the `/api/v1/` handlers via extracted helper functions (`_purge_preview()`, `_purge_execute()`, `_list_schedules()`). All three session-auth views return the **same JSON response shapes** as their `/api/v1/` counterparts.

#### Block 1 — API Access

- **API Key** field: masked value with "Reveal" toggle + "Copy" button
- **Generate Key** button → `POST /settings/edge/generate-key/` → saves new `secrets.token_hex(32)` to `EdgeConfig.api_key`
  - Inline warning: "Regenerating will invalidate any existing soca-control connection"
- Static read-only list of available API endpoints (hardcoded HTML — no dynamic generation needed)

#### Block 2 — Purge Edge Data

- `<select>` dropdown: "Older than" → 1 month / 3 months / 6 months / 1 year
- **Preview** button → AJAX `GET /settings/operations/purge/preview/?older_than=<days>` → inline result:
  ```
  4,821 detection records · 4,102 snapshot files · 2.0 GB will be freed
  ```
- **Purge Now** button (disabled until preview loaded) → DaisyUI confirmation modal
  - Modal shows preview numbers again
  - "Confirm Purge" → `POST /settings/operations/purge/execute/` → flash message with result
- Loading spinner during async calls

#### Block 3 — Schedule Remote Control

- Table: Schedule Name | Camera | Status badge | Start / Stop buttons
- Status badge: green "Running" / gray "Stopped" / amber "Unknown"
- Start → `POST /schedules/<id>/start/` (existing view), Stop → `POST /schedules/<id>/stop/` (existing view)
- Table auto-refreshes every 10s by polling `GET /settings/operations/schedules/`
- Poll pauses when `document.visibilityState === 'hidden'` (tab not visible) to avoid unnecessary requests

---

### 5. soca-control Additions

#### `Edge` model

Add `api_key = models.CharField(max_length=64, blank=True, default='')`.

#### Settings > Edge form

Add "API Key" text field to the edge add/edit form. Operator pastes the key from soca-dashboard's Operations tab.

#### `edge_client.py`

New functions, all send `Authorization: Api-Key {edge.api_key}` header.

**Return contract for write operations:** Return the parsed JSON dict on success, or `None` on any failure (network error, 4xx, 5xx). Callers check for `None` and show an appropriate error.

```python
def fetch_schedules(edge)             # GET /api/v1/schedules/ → list or None
def start_schedule(edge, sched_id)    # POST /api/v1/schedules/<id>/start/ → dict or None
def stop_schedule(edge, sched_id)     # POST /api/v1/schedules/<id>/stop/ → dict or None
def purge_preview(edge, older_than)   # GET /api/v1/purge/preview/ → dict or None
def purge_execute(edge, older_than)   # POST /api/v1/purge/execute/ → dict or None
```

#### New page: `/edges/<edge_id>/operations/`

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

### 6. URL Summary

#### soca-dashboard (new)

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

#### soca-control (new)

```
edges/<id>/operations/                          GET  → edge_operations
edges/<id>/operations/schedules/<sid>/start/    POST → edge_schedule_start
edges/<id>/operations/schedules/<sid>/stop/     POST → edge_schedule_stop
edges/<id>/operations/purge/execute/            POST → edge_purge_remote
edges/<id>/operations/purge/local/              POST → edge_purge_local
```

---

### 7. Security Notes

- API key is stored in plain text — acceptable for private network deployments
- All write operations (start, stop, purge execute) are POST only
- Purge preview and schedule list are GET (read-only)
- File deletion validates all paths start with `EdgeConfig.snapshots_root` before deleting (path traversal guard)
- soca-dashboard's purge operates on soca-engine's SQLite — requires the engine DB file to be readable/writable by the Django process user

---

### 8. Verification

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

### Dashboard Rule Management Plan

_Source: `soca-dashboard/rule-management-plan.md`_

## Advanced Rule Management — soca-dashboard Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a `Rule` model and inline rule card UI to soca-dashboard, replacing the hardcoded single-rule system in `Schedule.to_job_config()` with flexible per-schedule rules supporting cls filtering, dwell duration, cron scheduling, and per-rule actions.

**Architecture:** New `Rule` model (FK → Schedule) stores all rule conditions and action flags. `Schedule.to_job_config()` iterates active rules when present, falling back to legacy single-rule for backward compatibility. The schedule form gains collapsible inline rule cards with label-aware cls_id checkboxes fetched from the engine. `EdgeConfig` gains a global `redis_stream` field.

**Tech Stack:** Django 4.x, SQLite, DaisyUI 4 + Tailwind CSS CDN, vanilla JS, Python `requests`

**Spec:** `docs/superpowers/specs/2026-03-22-advanced-rule-management-design.md`

---

### File Map

| File | Change |
|------|--------|
| `app/models.py` | Add `Rule` model; add `iou_threshold` to `Schedule`; add `redis_stream` to `EdgeConfig`; rewrite `to_job_config()` |
| `app/tests.py` | New — model + view tests |
| `engine_client.py` | Add `get_model_labels(model_path)` |
| `app/views.py` | Add `rule_save`, `rule_delete`, `model_labels_proxy`; update `edge_settings` |
| `app/urls.py` | Add rule URLs + labels proxy URL |
| `templates/schedules/form.html` | Full rewrite with new sections + JS |
| `templates/settings/index.html` | Add `redis_stream` field to Edge Config tab |

---

### Task 1: Models — Rule, iou_threshold, redis_stream, to_job_config

**Files:**
- Modify: `app/models.py`
- Create: `app/tests.py`

- [ ] **Step 1: Write failing tests**

Create `app/tests.py`:

```python
from django.test import TestCase
from app.models import Camera, EdgeConfig, Schedule, Rule


def _make_schedule():
    cam = Camera.objects.create(name='cam1', rtsp_url='rtsp://localhost/test')
    edge = EdgeConfig.objects.create()
    return Schedule.objects.create(name='test', camera=cam, model_path='yolo/yolov8n.pt')


class RuleModelTest(TestCase):
    def test_rule_defaults(self):
        s = _make_schedule()
        r = Rule.objects.create(schedule=s, name='Test Rule')
        self.assertEqual(r.cls_operator, 'in')
        self.assertEqual(r.processing, 'in_roi')
        self.assertEqual(r.duration_op, 'immediate')
        self.assertEqual(r.duration_seconds, 0)
        self.assertEqual(r.cooldown_seconds, 60)
        self.assertEqual(r.cron_schedule, '* * * * *')
        self.assertEqual(r.message_template, '')
        self.assertFalse(r.action_telegram)
        self.assertFalse(r.action_redis)
        self.assertTrue(r.action_snapshot)
        self.assertEqual(r.priority, 100)
        self.assertTrue(r.is_active)

    def test_rule_name_required_at_view_layer(self):
        # Model allows empty name at DB level; view blocks it
        # This is covered in RuleViewTest.test_rule_save_requires_name
        pass

    def test_to_job_config_with_rules(self):
        s = _make_schedule()
        s.iou_threshold = 0.45
        s.save()
        Rule.objects.create(
            schedule=s, name='Person Alert',
            category='Intrusion', cls_ids=[0],
            cls_operator='in', processing='in_roi',
            duration_op='gte', duration_seconds=3,
            cooldown_seconds=60, cron_schedule='* * * * *',
            action_snapshot=True, action_redis=True,
            priority=100, is_active=True,
        )
        cfg = s.to_job_config()
        self.assertEqual(cfg['iou_threshold'], 0.45)
        self.assertEqual(len(cfg['rules']), 1)
        rule = cfg['rules'][0]
        self.assertEqual(rule['name'], 'Person Alert')
        self.assertEqual(rule['cls_ids'], [0])
        self.assertEqual(rule['cls_operator'], 'in')
        self.assertEqual(rule['processing'], 'in_roi')
        self.assertEqual(rule['duration_op'], 'gte')
        self.assertEqual(rule['duration_seconds'], 3)
        self.assertEqual(rule['cooldown_seconds'], 60)
        # Union of cls_ids at job level
        self.assertIn(0, cfg['cls_ids'])

    def test_to_job_config_fallback_no_rules(self):
        s = _make_schedule()
        s.alert_category = 'Intrusion'
        s.min_count = 2
        s.save()
        cfg = s.to_job_config()
        # Legacy single rule
        self.assertEqual(len(cfg['rules']), 1)
        self.assertEqual(cfg['rules'][0]['name'], 'alert')

    def test_inactive_rules_excluded(self):
        s = _make_schedule()
        Rule.objects.create(schedule=s, name='Active', is_active=True)
        Rule.objects.create(schedule=s, name='Inactive', is_active=False)
        cfg = s.to_job_config()
        self.assertEqual(len(cfg['rules']), 1)
        self.assertEqual(cfg['rules'][0]['name'], 'Active')

    def test_redis_stream_from_edge_config(self):
        s = _make_schedule()
        edge = EdgeConfig.objects.first()
        edge.redis_stream = 'custom:stream'
        edge.save()
        Rule.objects.create(schedule=s, name='R', action_redis=True, is_active=True)
        cfg = s.to_job_config()
        publish_action = next((a for a in cfg['rules'][0]['actions'] if a['type'] == 'publish_queue'), None)
        self.assertIsNotNone(publish_action)
        self.assertEqual(publish_action['stream'], 'custom:stream')
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-dashboard"
python manage.py test app.tests -v 2
```

Expected: FAIL — `Rule` does not exist, `iou_threshold` not on Schedule.

- [ ] **Step 3: Update `app/models.py`**

Add `iou_threshold` to `Schedule` (after `conf_threshold`):
```python
iou_threshold = models.FloatField(default=0.45)
```

Add `redis_stream` to `EdgeConfig` (after `snapshots_root`):
```python
redis_stream = models.CharField(max_length=200, default='soca:detections')
```

Add `Rule` model after `Schedule`:
```python
class Rule(models.Model):
    schedule         = models.ForeignKey(Schedule, on_delete=models.CASCADE, related_name='rules')
    name             = models.CharField(max_length=100)
    category         = models.CharField(max_length=100, default='Intrusion')
    cls_operator     = models.CharField(max_length=10, default='in')    # eq | in | not_in
    cls_ids          = models.JSONField(default=list)
    processing       = models.CharField(max_length=20, default='in_roi') # in_roi | detected
    duration_op      = models.CharField(max_length=15, default='immediate')  # immediate | gte | lte | eq
    duration_seconds = models.IntegerField(default=0)
    cooldown_seconds = models.IntegerField(default=60)
    cron_schedule    = models.CharField(max_length=50, default='* * * * *')
    message_template = models.TextField(blank=True, default='')
    action_telegram  = models.BooleanField(default=False)
    action_redis     = models.BooleanField(default=False)
    action_snapshot  = models.BooleanField(default=True)
    priority         = models.IntegerField(default=100)
    is_active        = models.BooleanField(default=True)

    class Meta:
        ordering = ['priority', 'id']

    def __str__(self):
        return f"{self.schedule.name} / {self.name}"
```

Rewrite `Schedule.to_job_config()`:
```python
def to_job_config(self):
    edge = EdgeConfig.objects.first()
    stream_name = (edge.redis_stream if edge and edge.redis_stream else self.redis_stream)
    active_rules = list(self.rules.filter(is_active=True).order_by('priority'))

    if active_rules:
        all_cls_ids = list({cid for rule in active_rules for cid in (rule.cls_ids or [])})
        rules_config = []
        for rule in active_rules:
            actions = []
            if rule.action_snapshot:
                actions.append({"type": "save_snapshot"})
            if rule.action_redis:
                actions.append({
                    "type": "publish_queue",
                    "stream": stream_name,
                    "message_template": rule.message_template or "{in_roi_count} object(s) detected at {time}",
                })
            if rule.action_telegram and edge and edge.telegram_bot_token and edge.telegram_chat_id:
                actions.append({
                    "type": "telegram",
                    "bot_token": edge.telegram_bot_token,
                    "chat_id": edge.telegram_chat_id,
                    "message_template": rule.message_template or "{in_roi_count} object(s) detected at {time}",
                })
            rules_config.append({
                "name": rule.name,
                "category": rule.category,
                "cls_ids": rule.cls_ids or [],
                "cls_operator": rule.cls_operator,
                "processing": rule.processing,
                "duration_op": rule.duration_op,
                "duration_seconds": rule.duration_seconds,
                "cooldown_seconds": rule.cooldown_seconds,
                "cron_schedule": rule.cron_schedule,
                "message_template": rule.message_template,
                "priority": rule.priority,
                "actions": actions,
            })
        return {
            "camera_id": str(self.camera.id),
            "camera_name": self.camera.name,
            "rtsp_url": self.camera.full_rtsp_url,
            "model_path": self.model_path,
            "cls_ids": all_cls_ids or [0],
            "conf_threshold": self.conf_threshold,
            "iou_threshold": self.iou_threshold,
            "frame_interval_ms": self.frame_interval_ms,
            "roi": {"type": self.roi_type, "points": self.roi_points},
            "aging": {"window_seconds": self.aging_window, "cooldown_seconds": self.aging_cooldown},
            "rules": rules_config,
            "output": {"stream_name": stream_name},
            "monitor": self.enable_monitor,
        }

    # Legacy fallback — no rules defined
    actions = []
    if self.save_snapshot:
        actions.append({"type": "save_snapshot"})
    if self.publish_redis:
        actions.append({
            "type": "publish_queue",
            "message_template": self.snapshot_message or "{in_roi_count} object(s) detected at {time}",
        })
    if edge and edge.telegram_bot_token and edge.telegram_chat_id:
        actions.append({
            "type": "telegram",
            "bot_token": edge.telegram_bot_token,
            "chat_id": edge.telegram_chat_id,
            "message_template": self.snapshot_message or "{in_roi_count} object(s) detected at {time}",
        })
    rules = []
    if actions:
        rules.append({
            "name": "alert",
            "priority": 100,
            "category": self.alert_category or "",
            "when_all": [{"path": "detections.in_roi_count", "op": "gte", "value": self.min_count}],
            "when_any": [],
            "actions": actions,
        })
    return {
        "camera_id": str(self.camera.id),
        "camera_name": self.camera.name,
        "rtsp_url": self.camera.full_rtsp_url,
        "model_path": self.model_path,
        "cls_ids": self.cls_ids,
        "conf_threshold": self.conf_threshold,
        "iou_threshold": self.iou_threshold,
        "frame_interval_ms": self.frame_interval_ms,
        "roi": {"type": self.roi_type, "points": self.roi_points},
        "aging": {"window_seconds": self.aging_window, "cooldown_seconds": self.aging_cooldown},
        "rules": rules,
        "output": {"stream_name": stream_name},
        "monitor": self.enable_monitor,
    }
```

- [ ] **Step 4: Run migrations**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-dashboard"
python manage.py makemigrations app
python manage.py migrate
```

Expected: new migration created and applied, no errors.

- [ ] **Step 5: Run tests**

```bash
python manage.py test app.tests -v 2
```

Expected: all tests PASS.

- [ ] **Step 6: Commit**

```bash
git add app/models.py app/tests.py
git add app/migrations/
git commit -m "feat: add Rule model, iou_threshold, redis_stream, rewrite to_job_config"
```

---

### Task 2: engine_client — get_model_labels()

**Files:**
- Modify: `engine_client.py`

- [ ] **Step 1: Write failing test** (add to `app/tests.py`)

```python
from unittest.mock import patch

class EngineClientTest(TestCase):
    @patch('engine_client.requests.get')
    def test_get_model_labels_success(self, mock_get):
        mock_get.return_value.json.return_value = [
            {"id": 0, "name": "person"}, {"id": 2, "name": "car"}
        ]
        import engine_client
        result = engine_client.get_model_labels('yolo/yolov8n.pt')
        self.assertEqual(len(result), 2)
        self.assertEqual(result[0]['name'], 'person')

    @patch('engine_client.requests.get', side_effect=Exception('unreachable'))
    def test_get_model_labels_failure_returns_empty(self, _):
        import engine_client
        result = engine_client.get_model_labels('yolo/yolov8n.pt')
        self.assertEqual(result, [])
```

- [ ] **Step 2: Run test to verify it fails**

```bash
python manage.py test app.tests.EngineClientTest -v 2
```

Expected: FAIL — `get_model_labels` does not exist.

- [ ] **Step 3: Add function to `engine_client.py`**

```python
def get_model_labels(model_path: str):
    """Fetch class labels for a model. Returns [{id, name}, ...] or [] on failure."""
    try:
        return requests.get(f"{_base()}/models/labels/", params={"path": model_path}, timeout=5).json()
    except Exception:
        return []
```

- [ ] **Step 4: Run tests**

```bash
python manage.py test app.tests.EngineClientTest -v 2
```

Expected: PASS.

- [ ] **Step 5: Commit**

```bash
git add engine_client.py app/tests.py
git commit -m "feat: add engine_client.get_model_labels()"
```

---

### Task 3: Rule views + URLs

**Files:**
- Modify: `app/views.py`
- Modify: `app/urls.py`

- [ ] **Step 1: Write failing tests** (add to `app/tests.py`)

```python
from django.test import Client

class RuleViewTest(TestCase):
    def setUp(self):
        from app.models import User
        self.client = Client()
        User.objects.create_superuser('admin', '', 'pass')
        self.client.login(username='admin', password='pass')
        self.schedule = _make_schedule()

    def test_rule_save_creates_rule(self):
        resp = self.client.post(f'/schedules/{self.schedule.pk}/rules/save/', {
            'rule_name': 'Test Rule',
            'rule_category': 'Intrusion',
            'cls_operator': 'in',
            'processing': 'in_roi',
            'duration_op': 'immediate',
            'duration_seconds': '0',
            'cooldown_seconds': '60',
            'cron_schedule': '* * * * *',
            'action_snapshot': 'on',
            'priority': '100',
            'is_active': 'on',
        })
        self.assertRedirects(resp, f'/schedules/{self.schedule.pk}/edit/')
        self.assertEqual(Rule.objects.filter(schedule=self.schedule).count(), 1)

    def test_rule_save_requires_name(self):
        resp = self.client.post(f'/schedules/{self.schedule.pk}/rules/save/', {
            'rule_name': '',
            'cls_operator': 'in',
        })
        self.assertEqual(Rule.objects.filter(schedule=self.schedule).count(), 0)

    def test_rule_delete(self):
        rule = Rule.objects.create(schedule=self.schedule, name='To Delete')
        resp = self.client.post(f'/schedules/{self.schedule.pk}/rules/{rule.pk}/delete/')
        self.assertRedirects(resp, f'/schedules/{self.schedule.pk}/edit/')
        self.assertFalse(Rule.objects.filter(pk=rule.pk).exists())

    def test_rule_save_updates_existing(self):
        rule = Rule.objects.create(schedule=self.schedule, name='Original')
        self.client.post(f'/schedules/{self.schedule.pk}/rules/save/', {
            'rule_id': str(rule.pk),
            'rule_name': 'Updated',
            'cls_operator': 'in',
            'processing': 'in_roi',
            'duration_op': 'immediate',
            'duration_seconds': '0',
            'cooldown_seconds': '60',
            'cron_schedule': '* * * * *',
            'priority': '100',
            'is_active': 'on',
        })
        rule.refresh_from_db()
        self.assertEqual(rule.name, 'Updated')
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
python manage.py test app.tests.RuleViewTest -v 2
```

Expected: FAIL — URL not found.

- [ ] **Step 3: Add views to `app/views.py`**

Add after the `schedule_status` view. First add `Rule` to the model import at the top:
```python
from .models import Camera, EdgeConfig, Schedule, Rule, User
```

Then add these views:

```python
@login_required
def rule_save(request, pk):
    """Create or update a Rule for a schedule. POST only."""
    if request.method != 'POST':
        return redirect('schedule_edit', pk=pk)
    schedule = get_object_or_404(Schedule, pk=pk)
    rid = request.POST.get('rule_id', '').strip()

    name = request.POST.get('rule_name', '').strip()
    if not name:
        messages.error(request, 'Rule name is required.')
        return redirect('schedule_edit', pk=pk)

    def _int(key, default):
        try:
            return int(request.POST.get(key) or default)
        except (ValueError, TypeError):
            return default

    # Each rule card submits its own form — fields are singular (not [] arrays)
    data = {
        'name': name,
        'category': request.POST.get('rule_category', 'Intrusion').strip() or 'Intrusion',
        'cls_operator': request.POST.get('cls_operator', 'in'),
        'cls_ids': [int(x) for x in request.POST.getlist('cls_ids') if x.isdigit()],
        'processing': request.POST.get('processing', 'in_roi'),
        'duration_op': request.POST.get('duration_op', 'immediate'),
        'duration_seconds': _int('duration_seconds', 0),
        'cooldown_seconds': _int('cooldown_seconds', 60),
        'cron_schedule': request.POST.get('cron_schedule', '* * * * *').strip() or '* * * * *',
        'message_template': request.POST.get('message_template', '').strip(),
        'action_telegram': request.POST.get('action_telegram') == 'on',
        'action_redis': request.POST.get('action_redis') == 'on',
        'action_snapshot': request.POST.get('action_snapshot') == 'on',
        'priority': _int('priority', 100),
        'is_active': request.POST.get('is_active') == 'on',
    }
    # Note: rid comes from hidden input 'rule_id' (singular), name from 'rule_name' (singular)

    if rid:
        Rule.objects.filter(pk=rid, schedule=schedule).update(**data)
    else:
        Rule.objects.create(schedule=schedule, **data)
    return redirect('schedule_edit', pk=pk)


@login_required
def rule_delete(request, pk, rid):
    """Delete a Rule. POST only."""
    if request.method != 'POST':
        return redirect('schedule_edit', pk=pk)
    Rule.objects.filter(pk=rid, schedule_id=pk).delete()
    return redirect('schedule_edit', pk=pk)


@login_required
def model_labels_proxy(request):
    """Proxy GET /api/models/labels/?path=<model> → engine. Returns JSON."""
    model_path = request.GET.get('path', '')
    labels = engine_client.get_model_labels(model_path)
    return JsonResponse({'labels': labels})
```

- [ ] **Step 4: Update `app/urls.py`**

Add these URL patterns inside `urlpatterns`:
```python
path("schedules/<int:pk>/rules/save/", views.rule_save, name="rule_save"),
path("schedules/<int:pk>/rules/<int:rid>/delete/", views.rule_delete, name="rule_delete"),
path("api/models/labels/", views.model_labels_proxy, name="model_labels_proxy"),
```

- [ ] **Step 5: Run tests**

```bash
python manage.py test app.tests.RuleViewTest -v 2
```

Expected: PASS.

- [ ] **Step 6: Commit**

```bash
git add app/views.py app/urls.py app/tests.py
git commit -m "feat: add rule_save, rule_delete views and model_labels_proxy"
```

---

### Task 4: Schedule form template rewrite

**Files:**
- Modify: `templates/schedules/form.html`

- [ ] **Step 1: Replace `templates/schedules/form.html` with the new template**

The new template keeps all existing ROI canvas JS intact and adds: Inference section, Rules section with collapsible cards, label fetch JS, and cron preview JS.

```html
{% extends "base.html" %}
{% block title %}{% if schedule %}Edit{% else %}New{% endif %} Schedule{% endblock %}

{% block extra_head %}
<style>
  .rule-card { transition: all 0.15s ease; }
  .rule-body { display: none; }
  .rule-body.open { display: grid; }
</style>
{% endblock %}

{% block content %}
<div class="flex items-center justify-between mb-4">
  <h1 class="text-xl font-bold">{% if schedule %}Edit Schedule: {{ schedule.name }}{% else %}New Schedule{% endif %}</h1>
  <a href="{% url 'schedule_list' %}" class="btn btn-ghost btn-sm">← Back</a>
</div>

<form method="post" id="scheduleForm">
  {% csrf_token %}
  <input type="hidden" name="roi_points" id="roi_points" value="{{ schedule.roi_points|default:'[]' }}">
  <input type="hidden" name="roi_type" id="roi_type_hidden" value="{{ schedule.roi_type|default:'POLYGON' }}">

  <div class="space-y-4">

    <!-- BASIC -->
    <div class="card bg-base-100 shadow-sm">
      <div class="card-body p-4">
        <h2 class="font-semibold text-sm uppercase tracking-wide text-base-content/60 mb-3">Basic</h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <div class="form-control">
            <label class="label py-1"><span class="label-text">Name</span></label>
            <input type="text" name="name" class="input input-bordered input-sm" value="{{ schedule.name|default:'' }}" required>
          </div>
          <div class="form-control">
            <label class="label py-1"><span class="label-text">Camera</span></label>
            <select name="camera" class="select select-bordered select-sm">
              {% for cam in cameras %}
                <option value="{{ cam.pk }}" {% if schedule and schedule.camera_id == cam.pk %}selected{% endif %}>{{ cam.name }}</option>
              {% endfor %}
            </select>
          </div>
          <div class="form-control">
            <label class="label py-1"><span class="label-text">Model Path</span></label>
            <input type="text" name="model_path" id="modelPathInput" class="input input-bordered input-sm"
                   value="{{ schedule.model_path|default:'yolo/yolov8n.pt' }}">
          </div>
        </div>
        <div class="mt-2">
          <label class="cursor-pointer label justify-start gap-3">
            <input type="checkbox" name="is_active" class="checkbox checkbox-sm" {% if not schedule or schedule.is_active %}checked{% endif %}>
            <span class="label-text">Active</span>
          </label>
        </div>
      </div>
    </div>

    <!-- INFERENCE -->
    <div class="card bg-base-100 shadow-sm">
      <div class="card-body p-4">
        <h2 class="font-semibold text-sm uppercase tracking-wide text-base-content/60 mb-3">Inference</h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <div class="form-control">
            <label class="label py-1"><span class="label-text">Confidence ≥</span></label>
            <input type="number" name="conf_threshold" class="input input-bordered input-sm" step="0.01" min="0" max="1"
                   value="{{ schedule.conf_threshold|default:'0.5' }}">
          </div>
          <div class="form-control">
            <label class="label py-1"><span class="label-text">IOU</span></label>
            <input type="number" name="iou_threshold" class="input input-bordered input-sm" step="0.01" min="0" max="1"
                   value="{{ schedule.iou_threshold|default:'0.45' }}">
          </div>
          <div class="form-control">
            <label class="label py-1"><span class="label-text">Frame Interval (ms)</span></label>
            <input type="number" name="frame_interval_ms" class="input input-bordered input-sm" min="100"
                   value="{{ schedule.frame_interval_ms|default:'1000' }}">
          </div>
        </div>
      </div>
    </div>

    <!-- ROI -->
    <div class="card bg-base-100 shadow-sm">
      <div class="card-body p-4">
        <h2 class="font-semibold text-sm uppercase tracking-wide text-base-content/60 mb-3">ROI</h2>
        <div class="flex gap-4 mb-3 flex-wrap">
          <label class="cursor-pointer flex items-center gap-2">
            <input type="radio" name="roi_type" value="POLYGON" class="radio radio-sm"
                   {% if not schedule or schedule.roi_type == 'POLYGON' %}checked{% endif %}
                   onchange="setRoiType('POLYGON')">
            <span class="text-sm">Polygon</span>
          </label>
          <label class="cursor-pointer flex items-center gap-2">
            <input type="radio" name="roi_type" value="RECT" class="radio radio-sm"
                   {% if schedule and schedule.roi_type == 'RECT' %}checked{% endif %}
                   onchange="setRoiType('RECT')">
            <span class="text-sm">Rectangle</span>
          </label>
          <button type="button" onclick="loadSnapshot()" class="btn btn-ghost btn-xs">Load Camera Frame</button>
          <button type="button" onclick="clearRoi()" class="btn btn-ghost btn-xs">Clear</button>
        </div>
        <canvas id="roiCanvas" width="640" height="360"
          class="border border-base-300 rounded w-full max-w-2xl cursor-crosshair bg-base-200"
          onclick="canvasClick(event)"
          oncontextmenu="canvasRightClick(event); return false;">
        </canvas>
        <p class="text-xs text-base-content/50 mt-1" id="roiHint">Click to add points. Right-click to remove last point.</p>
      </div>
    </div>

    <!-- RULES -->
    <div class="card bg-base-100 shadow-sm">
      <div class="card-body p-4">
        <h2 class="font-semibold text-sm uppercase tracking-wide text-base-content/60 mb-3">Rules</h2>
        <div id="rulesContainer" class="space-y-3">

          {% for rule in rules %}
          <div class="rule-card border border-base-300 rounded-lg" data-rule-id="{{ rule.pk }}">
            <div class="flex items-center justify-between p-3 cursor-pointer bg-base-200 rounded-t-lg rule-header"
                 onclick="toggleRule(this)">
              <span class="font-medium text-sm">
                <span class="rule-title">{{ rule.name }}</span>
              </span>
              <div class="flex items-center gap-2">
                {% if rule.is_active %}
                  <span class="badge badge-success badge-sm">active</span>
                {% else %}
                  <span class="badge badge-warning badge-sm">inactive</span>
                {% endif %}
                <span class="text-xs text-base-content/40">▼</span>
              </div>
            </div>
            <div class="rule-body open p-3 grid grid-cols-1 md:grid-cols-3 gap-3">
              {% include "schedules/_rule_fields.html" with rule=rule schedule=schedule %}
            </div>
          </div>
          {% endfor %}

        </div>

        <button type="button" onclick="addRule()" class="btn btn-dashed btn-sm mt-3 w-full text-base-content/50">
          + Add Rule
        </button>
      </div>
    </div>

    <!-- OUTPUT -->
    <div class="card bg-base-100 shadow-sm">
      <div class="card-body p-4">
        <h2 class="font-semibold text-sm uppercase tracking-wide text-base-content/60 mb-3">Output</h2>
        <div class="flex flex-wrap gap-6 mb-3">
          <label class="cursor-pointer flex items-center gap-2">
            <input type="checkbox" name="save_snapshot" class="checkbox checkbox-sm"
                   {% if not schedule or schedule.save_snapshot %}checked{% endif %}>
            <span class="text-sm">Save Snapshot</span>
          </label>
          <label class="cursor-pointer flex items-center gap-2">
            <input type="checkbox" name="enable_monitor" class="checkbox checkbox-sm"
                   {% if not schedule or schedule.enable_monitor %}checked{% endif %}>
            <span class="text-sm">Enable Monitor</span>
          </label>
        </div>
        <p class="text-xs text-base-content/40">
          Notification destinations (Telegram, Redis) are configured in
          <a href="{% url 'settings' %}" class="link link-primary">Settings → Edge</a>
          and enabled per-rule above.
        </p>
      </div>
    </div>

  </div>

  <div class="flex gap-3 mt-6">
    <button type="submit" class="btn btn-primary">Save Schedule</button>
    <a href="{% url 'schedule_list' %}" class="btn btn-ghost">Cancel</a>
  </div>
</form>

<!-- Rule template for JS cloning -->
<template id="ruleTemplate">
  <div class="rule-card border border-primary/30 rounded-lg" data-rule-id="">
    <div class="flex items-center justify-between p-3 cursor-pointer bg-primary/5 rounded-t-lg rule-header"
         onclick="toggleRule(this)">
      <span class="font-medium text-sm">
        <span class="rule-title text-base-content/50">New Rule</span>
      </span>
      <div class="flex items-center gap-2">
        <span class="badge badge-success badge-sm">active</span>
        <span class="text-xs text-base-content/40">▼</span>
      </div>
    </div>
    <div class="rule-body open p-3 grid grid-cols-1 md:grid-cols-3 gap-3">
      <input type="hidden" name="rule_id[]" value="">

      <div class="md:col-span-2 form-control">
        <label class="label py-1"><span class="label-text text-xs">Rule Name *</span></label>
        <input type="text" name="rule_name[]" class="input input-bordered input-xs rule-name-input"
               oninput="updateRuleTitle(this)" placeholder="e.g. Person in Zone A">
      </div>
      <div class="form-control">
        <label class="label py-1"><span class="label-text text-xs">Category</span></label>
        <input type="text" name="rule_category[]" class="input input-bordered input-xs" value="Intrusion">
      </div>

      <div class="form-control">
        <label class="label py-1"><span class="label-text text-xs">Class Filter</span></label>
        <select name="cls_operator[]" class="select select-bordered select-xs mb-1">
          <option value="in">IN</option>
          <option value="not_in">NOT IN</option>
          <option value="eq">=</option>
        </select>
        <div class="cls-checkboxes border border-base-300 rounded p-2 text-xs flex flex-wrap gap-2 min-h-8 bg-base-100">
          <span class="text-base-content/30 text-xs">Loading labels...</span>
        </div>
      </div>

      <div class="form-control">
        <label class="label py-1"><span class="label-text text-xs">Processing</span></label>
        <select name="processing[]" class="select select-bordered select-xs">
          <option value="in_roi">in ROI</option>
          <option value="detected">detected (all)</option>
        </select>
      </div>

      <div class="form-control">
        <label class="label py-1"><span class="label-text text-xs">Duration</span></label>
        <div class="flex gap-1 items-center">
          <select name="duration_op[]" class="select select-bordered select-xs" onchange="toggleDuration(this)">
            <option value="immediate">immediate</option>
            <option value="gte">≥</option>
            <option value="lte">≤</option>
            <option value="eq">=</option>
          </select>
          <input type="number" name="duration_seconds[]" class="input input-bordered input-xs w-16 duration-sec" value="0" style="display:none">
          <span class="text-xs text-base-content/50 duration-label" style="display:none">sec</span>
        </div>
      </div>

      <div class="form-control">
        <label class="label py-1"><span class="label-text text-xs">Cooldown (sec)</span></label>
        <input type="number" name="cooldown_seconds[]" class="input input-bordered input-xs" value="60" min="0">
      </div>

      <div class="form-control">
        <label class="label py-1"><span class="label-text text-xs">Schedule (cron)</span></label>
        <input type="text" name="cron_schedule[]" class="input input-bordered input-xs" value="* * * * *"
               oninput="updateCronPreview(this)" placeholder="* * * * *">
        <span class="text-xs text-primary mt-1 cron-preview">Always active</span>
      </div>

      <div class="md:col-span-3 form-control">
        <label class="label py-1"><span class="label-text text-xs">Message Template (optional)</span></label>
        <input type="text" name="message_template[]" class="input input-bordered input-xs"
               placeholder="{in_roi_count} object(s) detected at {time}">
        <span class="text-xs text-base-content/40">Placeholders: {count} {in_roi_count} {time} {camera_id} {category}</span>
      </div>

      <div class="md:col-span-3">
        <label class="label py-1"><span class="label-text text-xs">Actions</span></label>
        <div class="flex flex-wrap gap-4">
          <label class="cursor-pointer flex items-center gap-2 text-sm">
            <input type="checkbox" name="action_snapshot[]" value="on" class="checkbox checkbox-xs" checked> Save Snapshot
          </label>
          <label class="cursor-pointer flex items-center gap-2 text-sm">
            <input type="checkbox" name="action_telegram[]" value="on" class="checkbox checkbox-xs"> Send Telegram
          </label>
          <label class="cursor-pointer flex items-center gap-2 text-sm">
            <input type="checkbox" name="action_redis[]" value="on" class="checkbox checkbox-xs"> Publish Redis
          </label>
        </div>
      </div>

      <div class="md:col-span-3 flex items-center justify-between">
        <label class="cursor-pointer flex items-center gap-2 text-sm">
          <input type="checkbox" name="is_active[]" value="on" class="checkbox checkbox-xs" checked> Active
        </label>
        <div class="flex gap-2">
          <button type="submit" class="btn btn-primary btn-xs">Save Rule</button>
          <button type="button" onclick="removeRule(this)" class="btn btn-ghost btn-xs text-error">× Remove</button>
        </div>
      </div>
    </div>
  </div>
</template>

{# NOTE: Rule cards are wrapped in their own <form> elements (see _rule_fields.html).
   The main #scheduleForm only handles schedule-level fields. #}

<script>
// ── ROI Canvas ──────────────────────────────────────────────────────────────
const canvas = document.getElementById('roiCanvas');
const ctx = canvas.getContext('2d');
let roiType = '{{ schedule.roi_type|default:"POLYGON" }}';
let points = [];
let bgImage = null;

try { points = JSON.parse('{{ schedule.roi_points|default:"[]"|escapejs }}'); } catch(e) {}

function setRoiType(t) {
  roiType = t;
  document.getElementById('roi_type_hidden').value = t;
  points = [];
  draw();
}

function canvasClick(e) {
  const rect = canvas.getBoundingClientRect();
  const scaleX = canvas.width / rect.width;
  const scaleY = canvas.height / rect.height;
  const x = (e.clientX - rect.left) * scaleX / canvas.width;
  const y = (e.clientY - rect.top) * scaleY / canvas.height;
  if (roiType === 'RECT' && points.length >= 2) return;
  points.push([parseFloat(x.toFixed(5)), parseFloat(y.toFixed(5))]);
  document.getElementById('roi_points').value = JSON.stringify(points);
  draw();
}

function canvasRightClick(e) {
  e.preventDefault();
  if (points.length > 0) { points.pop(); draw(); }
  document.getElementById('roi_points').value = JSON.stringify(points);
}

function clearRoi() {
  points = [];
  document.getElementById('roi_points').value = '[]';
  draw();
}

function loadSnapshot() {
  const camSel = document.querySelector('select[name="camera"]');
  const camId = camSel ? camSel.value : '';
  if (!camId) return;
  const img = new Image();
  img.crossOrigin = 'anonymous';
  img.onload = () => { bgImage = img; draw(); };
  img.src = `/cameras/${camId}/snapshot/`;
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  if (bgImage) ctx.drawImage(bgImage, 0, 0, canvas.width, canvas.height);

  if (points.length === 0) return;

  const px = points.map(p => [p[0] * canvas.width, p[1] * canvas.height]);

  ctx.beginPath();
  ctx.moveTo(px[0][0], px[0][1]);
  px.slice(1).forEach(p => ctx.lineTo(p[0], p[1]));
  if (roiType === 'POLYGON') ctx.closePath();
  ctx.strokeStyle = '#6366f1';
  ctx.lineWidth = 2;
  ctx.stroke();
  ctx.fillStyle = 'rgba(99,102,241,0.15)';
  ctx.fill();

  px.forEach((p, i) => {
    ctx.beginPath();
    ctx.arc(p[0], p[1], 5, 0, Math.PI * 2);
    ctx.fillStyle = '#6366f1';
    ctx.fill();
    ctx.fillStyle = '#fff';
    ctx.font = '10px sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText(i + 1, p[0], p[1] + 3);
  });
}

draw();

// ── Labels fetch ─────────────────────────────────────────────────────────────
let _cachedLabels = [];

function fetchLabels(modelPath, callback) {
  if (!modelPath) { callback([]); return; }
  fetch(`/api/models/labels/?path=${encodeURIComponent(modelPath)}`)
    .then(r => r.json())
    .then(data => { _cachedLabels = data.labels || []; callback(_cachedLabels); })
    .catch(() => { _cachedLabels = []; callback([]); });
}

function buildClsCheckboxes(container, labels, selectedIds) {
  container.innerHTML = '';
  if (!labels || labels.length === 0) {
    container.innerHTML = '<span class="text-base-content/30 text-xs">No labels found</span>';
    return;
  }
  labels.forEach(lbl => {
    const checked = selectedIds && selectedIds.includes(lbl.id) ? 'checked' : '';
    container.insertAdjacentHTML('beforeend',
      `<label class="cursor-pointer flex items-center gap-1 text-xs whitespace-nowrap">
         <input type="checkbox" name="cls_ids[]" value="${lbl.id}" class="checkbox checkbox-xs" ${checked}>
         ${lbl.name} (${lbl.id})
       </label>`
    );
  });
}

// Load labels when model path changes
document.getElementById('modelPathInput').addEventListener('change', function() {
  const model = this.value.trim();
  fetchLabels(model, labels => {
    document.querySelectorAll('.cls-checkboxes').forEach(c => buildClsCheckboxes(c, labels, []));
  });
});

// Initial load if editing
(function() {
  const model = document.getElementById('modelPathInput').value.trim();
  if (!model) return;
  fetchLabels(model, labels => {
    {% if schedule %}
    const existingIds = {{ schedule.cls_ids|default:"[]" }};
    document.querySelectorAll('.cls-checkboxes').forEach(c => buildClsCheckboxes(c, labels, existingIds));
    {% else %}
    document.querySelectorAll('.cls-checkboxes').forEach(c => buildClsCheckboxes(c, labels, []));
    {% endif %}
  });
})();

// ── Cron preview ─────────────────────────────────────────────────────────────
function updateCronPreview(input) {
  const preview = input.closest('.form-control').querySelector('.cron-preview');
  if (!preview) return;
  const parts = input.value.trim().split(/\s+/);
  if (parts.length !== 5) { preview.textContent = ''; return; }
  const [min, hour, dom, month, dow] = parts;
  let text = '';
  if (dow !== '*') {
    const days = ['Sun','Mon','Tue','Wed','Thu','Fri','Sat'];
    const d = dow.split(',').map(x => days[parseInt(x)] || x).join(', ');
    text += d;
  }
  if (hour !== '*') text += (text ? ' ' : '') + hour + ':' + (min === '*' ? '00' : min.padStart(2,'0'));
  preview.textContent = text || 'Always active';
}

// ── Rule card toggle ─────────────────────────────────────────────────────────
function toggleRule(header) {
  const body = header.nextElementSibling;
  body.classList.toggle('open');
}

function updateRuleTitle(input) {
  const card = input.closest('.rule-card');
  const title = card.querySelector('.rule-title');
  if (title) title.textContent = input.value || 'New Rule';
}

function toggleDuration(select) {
  const form = select.closest('.form-control');
  const secInput = form.querySelector('.duration-sec');
  const secLabel = form.querySelector('.duration-label');
  const show = select.value !== 'immediate';
  secInput.style.display = show ? '' : 'none';
  secLabel.style.display = show ? '' : 'none';
}

// ── Add / Remove rules ───────────────────────────────────────────────────────
function addRule() {
  const tmpl = document.getElementById('ruleTemplate');
  const clone = tmpl.content.cloneNode(true);
  document.getElementById('rulesContainer').appendChild(clone);
  const model = document.getElementById('modelPathInput').value.trim();
  const newCard = document.getElementById('rulesContainer').lastElementChild;
  const clsCont = newCard.querySelector('.cls-checkboxes');
  fetchLabels(model, labels => buildClsCheckboxes(clsCont, labels, []));
}

function removeRule(btn) {
  const card = btn.closest('.rule-card');
  const ruleId = card.dataset.ruleId;
  if (ruleId) {
    // Existing rule — submit delete form
    const form = document.createElement('form');
    form.method = 'post';
    form.action = `{% if schedule %}{% url 'rule_delete' pk=schedule.pk rid=0 %}{% else %}#{% endif %}`.replace('/0/', `/${ruleId}/`);
    form.innerHTML = `{% csrf_token %}`;
    document.body.appendChild(form);
    form.submit();
  } else {
    card.remove();
  }
}

// Main schedule form only saves schedule-level fields — rules have their own Save buttons
</script>
{% endblock %}
```

- [ ] **Step 2: Create rule fields partial `templates/schedules/_rule_fields.html`** (for existing rules rendered server-side)

Each rule card is wrapped in its own `<form>` that POSTs to `rule_save`. This avoids HTML nested-form issues and requires no JS orchestration.

```html
{# Partial: renders a self-contained rule save form for an existing Rule object #}
{# Include with: {% include "schedules/_rule_fields.html" with rule=rule schedule=schedule %} #}
<form method="post" action="{% url 'rule_save' pk=schedule.pk %}">
{% csrf_token %}
<input type="hidden" name="rule_id" value="{{ rule.pk }}">

<div class="md:col-span-2 form-control">
  <label class="label py-1"><span class="label-text text-xs">Rule Name *</span></label>
  <input type="text" name="rule_name[]" class="input input-bordered input-xs rule-name-input"
         oninput="updateRuleTitle(this)" value="{{ rule.name }}">
</div>
<div class="form-control">
  <label class="label py-1"><span class="label-text text-xs">Category</span></label>
  <input type="text" name="rule_category[]" class="input input-bordered input-xs" value="{{ rule.category }}">
</div>

<div class="form-control">
  <label class="label py-1"><span class="label-text text-xs">Class Filter</span></label>
  <select name="cls_operator[]" class="select select-bordered select-xs mb-1">
    <option value="in" {% if rule.cls_operator == 'in' %}selected{% endif %}>IN</option>
    <option value="not_in" {% if rule.cls_operator == 'not_in' %}selected{% endif %}>NOT IN</option>
    <option value="eq" {% if rule.cls_operator == 'eq' %}selected{% endif %}>=</option>
  </select>
  <div class="cls-checkboxes border border-base-300 rounded p-2 text-xs flex flex-wrap gap-2 min-h-8 bg-base-100"
       data-selected="{{ rule.cls_ids|default:'[]' }}">
    <span class="text-base-content/30 text-xs">Loading labels...</span>
  </div>
</div>

<div class="form-control">
  <label class="label py-1"><span class="label-text text-xs">Processing</span></label>
  <select name="processing[]" class="select select-bordered select-xs">
    <option value="in_roi" {% if rule.processing == 'in_roi' %}selected{% endif %}>in ROI</option>
    <option value="detected" {% if rule.processing == 'detected' %}selected{% endif %}>detected (all)</option>
  </select>
</div>

<div class="form-control">
  <label class="label py-1"><span class="label-text text-xs">Duration</span></label>
  <div class="flex gap-1 items-center">
    <select name="duration_op[]" class="select select-bordered select-xs" onchange="toggleDuration(this)">
      <option value="immediate" {% if rule.duration_op == 'immediate' %}selected{% endif %}>immediate</option>
      <option value="gte" {% if rule.duration_op == 'gte' %}selected{% endif %}>≥</option>
      <option value="lte" {% if rule.duration_op == 'lte' %}selected{% endif %}>≤</option>
      <option value="eq" {% if rule.duration_op == 'eq' %}selected{% endif %}>=</option>
    </select>
    <input type="number" name="duration_seconds[]" class="input input-bordered input-xs w-16 duration-sec"
           value="{{ rule.duration_seconds }}"
           style="{% if rule.duration_op == 'immediate' %}display:none{% endif %}">
    <span class="text-xs text-base-content/50 duration-label"
          style="{% if rule.duration_op == 'immediate' %}display:none{% endif %}">sec</span>
  </div>
</div>

<div class="form-control">
  <label class="label py-1"><span class="label-text text-xs">Cooldown (sec)</span></label>
  <input type="number" name="cooldown_seconds[]" class="input input-bordered input-xs" value="{{ rule.cooldown_seconds }}" min="0">
</div>

<div class="form-control">
  <label class="label py-1"><span class="label-text text-xs">Schedule (cron)</span></label>
  <input type="text" name="cron_schedule[]" class="input input-bordered input-xs" value="{{ rule.cron_schedule }}"
         oninput="updateCronPreview(this)">
  <span class="text-xs text-primary mt-1 cron-preview"></span>
</div>

<div class="md:col-span-3 form-control">
  <label class="label py-1"><span class="label-text text-xs">Message Template (optional)</span></label>
  <input type="text" name="message_template[]" class="input input-bordered input-xs" value="{{ rule.message_template }}">
  <span class="text-xs text-base-content/40">Placeholders: {count} {in_roi_count} {time} {camera_id} {category}</span>
</div>

<div class="md:col-span-3">
  <label class="label py-1"><span class="label-text text-xs">Actions</span></label>
  <div class="flex flex-wrap gap-4">
    <label class="cursor-pointer flex items-center gap-2 text-sm">
      <input type="checkbox" name="action_snapshot[]" value="on" class="checkbox checkbox-xs" {% if rule.action_snapshot %}checked{% endif %}> Save Snapshot
    </label>
    <label class="cursor-pointer flex items-center gap-2 text-sm">
      <input type="checkbox" name="action_telegram[]" value="on" class="checkbox checkbox-xs" {% if rule.action_telegram %}checked{% endif %}> Send Telegram
    </label>
    <label class="cursor-pointer flex items-center gap-2 text-sm">
      <input type="checkbox" name="action_redis[]" value="on" class="checkbox checkbox-xs" {% if rule.action_redis %}checked{% endif %}> Publish Redis
    </label>
  </div>
</div>

<div class="md:col-span-3 flex items-center justify-between">
  <label class="cursor-pointer flex items-center gap-2 text-sm">
    <input type="checkbox" name="is_active" value="on" class="checkbox checkbox-xs" {% if rule.is_active %}checked{% endif %}> Active
  </label>
  <div class="flex gap-2">
    <button type="submit" class="btn btn-primary btn-xs">Save Rule</button>
    <button type="button" onclick="removeRule(this)" class="btn btn-ghost btn-xs text-error">× Remove</button>
  </div>
</div>
</form>
```

- [ ] **Step 3: Update `schedule_form` view in `app/views.py` to pass rules to template**

Find the `schedule_form` view and ensure it passes `rules` to the template context:

```python
# In schedule_form view, add to context dict:
ctx = {
    ...existing fields...,
    'rules': schedule.rules.all() if schedule else [],
    'cameras': Camera.objects.filter(is_active=True),
}
```

Also update the POST handler to handle the `iou_threshold` field:
```python
# In schedule_form POST, add alongside conf_threshold:
'iou_threshold': float(request.POST.get('iou_threshold', 0.45) or 0.45),
```

**Rule submission strategy:** Each rule card has its own **"Save Rule" button** that POSTs directly to `rule_save` (not the main schedule form). This is flat and requires no JS orchestration:
- Existing rules: Save Rule button submits to `rule_save` with `rule_id` pre-filled → updates in place.
- New rules: Save Rule button submits to `rule_save` without `rule_id` → creates new Rule.
- The main "Save Schedule" button only saves the schedule fields (name, camera, inference, ROI, output) — it does NOT process rule fields.

Each rule card therefore has a nested form pointing to `rule_save` URL. The main schedule form and rule forms are separate `<form>` elements (nested forms are not allowed in HTML; rule forms sit outside the main form as separate `<form>` tags adjacent to each card).

- [ ] **Step 4: Manual verification**

```bash
python manage.py runserver
```

1. Open `http://localhost:8000/schedules/new/`
2. Verify: Basic, Inference, ROI, Rules, Output sections render
3. Click "+ Add Rule" → new rule card appears
4. Enter model path → cls_id checkboxes populate (requires running engine)
5. Enter cron expression → human-readable preview updates

- [ ] **Step 5: Commit**

```bash
git add templates/schedules/form.html templates/schedules/_rule_fields.html app/views.py
git commit -m "feat: rewrite schedule form with inline rule cards"
```

---

### Task 5: Edge settings — add redis_stream field

**Files:**
- Modify: `templates/settings/index.html`
- Modify: `app/views.py` (`edge_settings` view)

- [ ] **Step 1: Add `redis_stream` to Edge Config tab in `templates/settings/index.html`**

Find the Edge Config tab section in the template. After the Engine URL field, add:

```html
<div class="form-control">
  <label class="label"><span class="label-text">Redis Stream Name</span></label>
  <input type="text" name="redis_stream" class="input input-bordered input-sm"
         value="{{ edge.redis_stream|default:'soca:detections' }}">
  <label class="label"><span class="label-text-alt text-base-content/50">Used by all rules that publish to Redis</span></label>
</div>
```

- [ ] **Step 2: Update `edge_settings` view in `app/views.py` to save `redis_stream`**

In the `edge_settings` POST handler, add:
```python
cfg.redis_stream = request.POST.get('redis_stream', 'soca:detections').strip() or 'soca:detections'
```

- [ ] **Step 3: Write test** (add to `app/tests.py`)

```python
class EdgeSettingsTest(TestCase):
    def setUp(self):
        from app.models import User
        self.client = Client()
        User.objects.create_superuser('admin', '', 'pass')
        self.client.login(username='admin', password='pass')
        EdgeConfig.objects.create()

    def test_redis_stream_saved(self):
        cfg = EdgeConfig.objects.first()
        self.client.post('/settings/edge/', {
            'edge_name': 'edge-1',
            'engine_url': 'http://localhost:8001',
            'redis_stream': 'custom:stream',
        })
        cfg.refresh_from_db()
        self.assertEqual(cfg.redis_stream, 'custom:stream')
```

- [ ] **Step 4: Run tests**

```bash
python manage.py test app.tests.EdgeSettingsTest -v 2
```

Expected: PASS.

- [ ] **Step 5: Commit**

```bash
git add templates/settings/index.html app/views.py app/tests.py
git commit -m "feat: add redis_stream field to edge settings"
```

---

### Verification Checklist

1. `python manage.py test app.tests` — all tests pass
2. `python manage.py runserver` — no startup errors
3. Create a schedule → add 2 rules → save → `to_job_config()` output shows both rules
4. Start a schedule → engine receives `iou_threshold` in job config
5. Inactive rule → excluded from `to_job_config()` output
6. Schedule with no rules → legacy single-rule fallback works
7. Settings → Edge → save `redis_stream` → used in job config `publish_queue.stream`
8. Telegram/Redis flags per rule → actions included/excluded correctly

### Dashboard Advanced Rule Management

_Source: `soca-dashboard/advanced-rule-management-design.md`_

## Advanced Rule Management Design

### Goal

Replace the hardcoded single-rule system in soca-dashboard with a flexible, per-schedule rule management UI that lets operators configure multi-condition detection rules with time-based scheduling, class filtering, dwell duration, and per-rule notification actions.

### Architecture

Rules are stored in a new `Rule` model (FK → Schedule). The existing `Schedule.to_job_config()` iterates all active rules to build the engine payload instead of generating one hardcoded rule. The engine gains three new capabilities: dwell tracking per track_id, per-rule cron-based activation, and cls_operator filtering. All notification destinations (Telegram, Redis) are configured globally in EdgeConfig and referenced by per-rule action flags.

### Tech Stack

- Django 4.x, SQLite — new Rule model, migration
- `croniter` — cron expression parsing (new dependency, dashboard + engine)
- DaisyUI 4 + Tailwind CSS — collapsible rule cards, inline form
- Vanilla JS — card expand/collapse, label fetch, cron preview, dynamic form fields
- soca-engine — dwell tracking, cron rule gating, cls_operator filtering, labels endpoint

---

### Data Models

#### Schedule (additions only)

```python
iou_threshold = models.FloatField(default=0.45)
```

Existing fields (`aging_window`, `aging_cooldown`, `min_count`, `alert_category`, `cls_ids`, `redis_stream`) are kept but no longer shown in the form when rules exist. They serve as fallback for schedules with no Rule objects. **`Schedule.redis_stream` is kept in the model but hidden in the form** — the global `EdgeConfig.redis_stream` takes precedence at runtime; `Schedule.redis_stream` is the fallback for legacy schedules.

#### Rule (new model)

```python
class Rule(models.Model):
    schedule          = models.ForeignKey(Schedule, on_delete=models.CASCADE, related_name='rules')
    name              = models.CharField(max_length=100)
    category          = models.CharField(max_length=100, default='Intrusion')  # alert_category for engine
    cls_operator      = models.CharField(max_length=10, default='in')          # eq | in | not_in
    cls_ids           = models.JSONField(default=list)                          # [0, 2, 7]
    processing        = models.CharField(max_length=20, default='in_roi')      # in_roi | detected
    duration_op       = models.CharField(max_length=15, default='immediate')   # immediate | gte | lte | eq
    duration_seconds  = models.IntegerField(default=0)
    cooldown_seconds  = models.IntegerField(default=60)
    cron_schedule     = models.CharField(max_length=50, default='* * * * *')
    message_template  = models.TextField(blank=True, default='')               # empty = engine default
    action_telegram   = models.BooleanField(default=False)
    action_redis      = models.BooleanField(default=False)
    action_snapshot   = models.BooleanField(default=True)
    priority          = models.IntegerField(default=100)                       # matches engine sort key
    is_active         = models.BooleanField(default=True)

    class Meta:
        ordering = ['priority', 'id']
```

#### EdgeConfig (addition)

```python
redis_stream = models.CharField(max_length=200, default='soca:detections')
```

Migration note: add `redis_stream` to EdgeConfig first, pre-populate with `'soca:detections'` as default. Keep `Schedule.redis_stream` intact (do not drop it).

---

### Schedule Form — Sections

#### Basic
Fields: `name`, `camera` (FK select), `model_path` (text), `is_active` (checkbox)

#### Inference
Fields: `conf_threshold`, `iou_threshold` (new), `frame_interval_ms`

#### ROI
Unchanged — polygon/rect canvas with normalized coordinates.

#### Rules
Flat list of collapsible rule cards. Each card:

| Field | Input | Notes |
|-------|-------|-------|
| name | text | free label |
| category | text | alert category (Intrusion, PPE, etc.) |
| cls_operator | select | IN / NOT IN / = |
| cls_ids | checkboxes | populated from `/api/models/labels/?path=<model>` |
| processing | select | in ROI / detected (all) |
| duration_op | select | immediate / ≥ / ≤ / = |
| duration_seconds | number | hidden when `immediate` |
| cooldown_seconds | number | seconds between repeat alerts for this rule |
| cron_schedule | text | `* 8-18 * * 1-5` (standard cron: min hour dom month dow) |
| cron_preview | read-only | JS live preview e.g. "Mon–Fri 08:00–18:00" |
| message_template | text | optional; placeholders: `{count}`, `{camera_id}`, `{time}` |
| action_telegram | checkbox | |
| action_redis | checkbox | |
| action_snapshot | checkbox | |
| is_active | checkbox | shown in card header |

**Per-field defaults when left blank:**

| Field | Default | Behavior |
|-------|---------|----------|
| `name` | — | **Required** — form validation blocks save |
| `category` | `"Intrusion"` | Most common use case |
| `cls_operator` | `in` | Inclusive filter |
| `cls_ids` | `[]` (empty) | Empty = no class filter — any detected object qualifies |
| `processing` | `in_roi` | ROI-aware default |
| `duration_op` | `immediate` | Fire on first detection, no dwell wait |
| `duration_seconds` | `0` | Hidden in UI when `immediate` |
| `cooldown_seconds` | `60` | 1 minute between repeat alerts |
| `cron_schedule` | `* * * * *` | Always active; validated on save |
| `message_template` | `""` | Engine uses its built-in default message |
| `action_telegram` | `false` | Opt-in |
| `action_redis` | `false` | Opt-in |
| `action_snapshot` | `true` | Always save snapshot unless unchecked |

**JS behaviors:**
- "Load Camera Frame" fetches snapshot → sets canvas background
- Changing `model_path` triggers label fetch → rebuilds cls_id checkboxes
- `duration_seconds` field hidden/shown based on `duration_op` value
- `cron_schedule` input → live human-readable preview (JS pattern parse)
- "Add Rule" appends a blank card with `data-index` incremented
- Card header click toggles expand/collapse
- Collapsed header shows: rule name + active badge

#### Output
Checkboxes: `save_snapshot`, `enable_monitor`
Status row: shows Telegram ● ready / ● not configured, Redis ● ready / ● not configured (derived from EdgeConfig).

---

### Settings → Edge (additions)

Form sections:
1. **Engine** — edge name, engine URL, MediaMTX URL
2. **Telegram** — bot token, chat ID(s) (comma-separated for multiple recipients)
3. **Redis** — stream name (global, used by all rules that publish to Redis)

`EdgeConfig.redis_stream` takes precedence over `Schedule.redis_stream` at job config build time.

---

### API Endpoint (new, engine-side)

```
GET /models/labels/?path=yolo/yolov8n.pt
```

Returns:
```json
[{"id": 0, "name": "person"}, {"id": 2, "name": "car"}, {"id": 7, "name": "truck"}]
```

Engine reads the `.names` list or `data.yaml` adjacent to the model file. Dashboard calls this via `engine_client.get_model_labels(model_path)` → JS populates cls_id checkboxes.

---

### Job Config — Updated Structure

`Schedule.to_job_config()` is rewritten to iterate `self.rules.filter(is_active=True).order_by('priority')`:

```python
{
  "camera_id": "...",
  "rtsp_url": "...",
  "model_path": "...",
  "conf_threshold": 0.5,
  "iou_threshold": 0.45,        # new — passed to model.track(iou=...)
  "frame_interval_ms": 500,
  "roi": {"type": "POLYGON", "points": [...]},
  "rules": [
    {
      "name": "Person in North Zone",
      "category": "Intrusion",
      "cls_ids": [0],
      "cls_operator": "in",       # in | not_in | eq
      "processing": "in_roi",     # in_roi | detected
      "duration_op": "gte",       # immediate | gte | lte | eq
      "duration_seconds": 3,
      "cooldown_seconds": 60,     # per-rule cooldown; replaces global aging in new-rules path
      "cron_schedule": "* 8-18 * * 1-5",
      "message_template": "",     # empty = engine default
      "priority": 100,
      "actions": [
        {"type": "telegram", "bot_token": "...", "chat_id": "..."},
        {"type": "publish_queue", "stream": "soca:detections", "message_template": "..."},
        {"type": "save_snapshot"}
      ]
    }
  ],
  "monitor": true
}
```

**Fallback:** if `self.rules.count() == 0`, use legacy single-rule generation (backward compatible, uses global aging/cooldown).

---

### soca-engine Changes

#### 1. `iou_threshold` in JobConfig and inference

Add to `JobConfig` schema in `models/schemas.py`:
```python
iou_threshold: float = 0.45
```

Change `yolo_inference.infer()` signature to accept `iou_threshold`:
```python
# Before: def infer(frame, model_path, cls_ids, conf):
def infer(frame, model_path, cls_ids, conf, iou_threshold: float = 0.45):
    ...
    results = model.track(frame, conf=conf, iou=iou_threshold, ...)
```

Update the call site in `detection_worker._run()`:
```python
detections = infer(frame, cfg.model_path, cfg.cls_ids, cfg.conf_threshold, cfg.iou_threshold)
```

#### 2. Dwell Tracking

Per active job, maintain `dwell_tracker: dict[str, float]` — `{track_id: first_seen_timestamp}`.

Add `dwell_seconds: float = 0.0` to the `Detection` dataclass in `models/schemas.py`.

Each frame:
```python
now = time.time()
for det in detections:
    if det.track_id not in dwell_tracker:
        dwell_tracker[det.track_id] = now
    det.dwell_seconds = now - dwell_tracker[det.track_id]

# Evict track_ids not seen this frame
seen_ids = {d.track_id for d in detections}
for tid in list(dwell_tracker):
    if tid not in seen_ids:
        del dwell_tracker[tid]
```

**Duration evaluation per rule (first-class, not via `when_all`):**
```python
def _passes_duration(rule, detections):
    if rule['duration_op'] == 'immediate':
        return True
    op = rule['duration_op']   # gte | lte | eq
    threshold = rule['duration_seconds']
    # Pass if any qualifying detection meets dwell threshold
    max_dwell = max((d.dwell_seconds for d in detections), default=0)
    return {'gte': max_dwell >= threshold,
            'lte': max_dwell <= threshold,
            'eq':  max_dwell == threshold}[op]
```

#### 3. Processing Mode (in_roi vs detected)

When evaluating a rule:
```python
if rule['processing'] == 'in_roi':
    relevant = [d for d in detections if d.in_roi]
elif rule['processing'] == 'detected':
    relevant = detections   # all detections, ignoring ROI
```

Subsequent cls_operator filtering and duration check apply to `relevant`.

#### 4. cls_operator Filtering

```python
op = rule['cls_operator']
ids = rule['cls_ids']
if op == 'in':
    relevant = [d for d in relevant if d.cls_id in ids]
elif op == 'not_in':
    relevant = [d for d in relevant if d.cls_id not in ids]
elif op == 'eq':
    relevant = [d for d in relevant if d.cls_id == ids[0]]
```

#### 5. Per-rule Cooldown

When the new-rules path is active (job config has `rules` array), each rule tracks its own last-fire timestamp:
```python
rule_last_fired: dict[str, float] = {}  # keyed by rule name

def _in_cooldown(rule, rule_last_fired):
    last = rule_last_fired.get(rule['name'], 0)
    return (time.time() - last) < rule['cooldown_seconds']
```

The global `StateTracker` cooldown is **bypassed** in the new-rules path. The fallback (zero-rule) path continues using the global StateTracker as before.

#### 6. New-Rules Evaluation Loop in detection_worker

In `detection_worker._run()`, branch on whether the job config has rules:

```python
# Initialized once per job start:
dwell_tracker: dict[str, float] = {}
rule_last_fired: dict[str, float] = {}   # keyed by rule['name']

# Per-frame evaluation (new-rules path):
if cfg.rules:
    _update_dwell(detections, dwell_tracker)   # updates det.dwell_seconds in-place
    for rule in cfg.rules:
        if not _cron_active(rule['cron_schedule']):
            continue
        if _in_cooldown(rule, rule_last_fired):
            continue
        relevant = _filter_processing(rule, detections)    # in_roi or detected
        relevant = _filter_cls(rule, relevant)             # cls_operator + cls_ids
        if not relevant:
            continue
        if not _passes_duration(rule, relevant):
            continue
        # Rule fires — build synthetic RuleResult + dispatch via existing action_dispatcher
        rule_result = RuleResult(
            rule_name=rule['name'],
            category=rule['category'],
            triggered=True,
            in_roi_count=len(relevant),
            detection_count=len(detections),
        )
        frame_result.rule_results.append(rule_result)
        action_dispatcher.dispatch(rule['actions'], frame_result, cfg)
        rule_last_fired[rule['name']] = time.time()
else:
    # Legacy path — use existing rule_engine.evaluate() + StateTracker
    rule_engine.evaluate(cfg, frame_result, tracker)
```

`_dispatch_rule_actions` is **not** a separate function — the dispatch call is inlined in the loop as shown above. It constructs a `RuleResult` using existing Pydantic schema, appends it to `frame_result.rule_results`, then calls the existing `action_dispatcher.dispatch()`. This reuses all existing notification infrastructure (telegram, redis, snapshot) without duplication.

Each helper function (`_update_dwell`, `_cron_active`, `_in_cooldown`, `_filter_processing`, `_filter_cls`, `_passes_duration`) lives in `detection_worker.py` as a module-level function — one function per responsibility.

#### 7. RuleAction schema and action_dispatcher

Add `stream` field to `RuleAction` in `models/schemas.py`:
```python
stream: str | None = None   # used by publish_queue action
```

`action_dispatcher.dispatch()` reads `action.stream` for `publish_queue` actions:
```python
if action.type == 'publish_queue':
    stream_name = action.stream or cfg.output.stream_name   # action overrides global
    output_publisher.publish_to_queue(result, stream_name)
```

`JobConfig.output` is **retained** for the legacy path. In the new-rules path, `output.stream_name` is the fallback only.

#### 8. cls_ids at job level (infer call)

`to_job_config()` sets the top-level `cls_ids` (used by `infer()` to pre-filter) to the **union of all active rule cls_ids**:
```python
all_cls_ids = list({cid for rule in active_rules for cid in rule.cls_ids})
```
This ensures `infer()` does not discard classes needed by individual rules. Per-rule `cls_operator` filtering happens after inference.

#### 9. Cron Rule Gating

```python
from croniter import croniter
from datetime import datetime

def _cron_active(cron_expr: str) -> bool:
    now = datetime.now()
    # Check if current minute matches: get the most recent scheduled time
    try:
        c = croniter(cron_expr, now)
        prev = c.get_prev(datetime)
        # Active if the previous scheduled time is within the last minute
        return (now - prev).total_seconds() < 60
    except Exception:
        return True   # malformed cron → always active (fail open)
```

Add `croniter` to `requirements.txt`.

**Semantics:** `_cron_active` uses **window-membership** — returns `True` for every frame within the matching minute. This is intentional: `cron_schedule` defines *when the rule is active*, and `cooldown_seconds` controls how often it actually fires within that window. This is not edge-trigger; a rule with `* 8-18 * * 1-5` is active on every frame processed during business hours.

#### 10. Labels Endpoint

New file `api/labels.py`:
```python
# GET /models/labels/?path=<model_path>
# Reads <model>.names file or data.yaml sibling, returns [{id, name}, ...]
```

Register in router alongside existing `/models/` route.

---

### Files Changed

#### soca-dashboard
| File | Change |
|------|--------|
| `app/models.py` | Add `Rule` model, add `iou_threshold` to `Schedule`, add `redis_stream` to `EdgeConfig` |
| `app/views.py` | Add `rule_save`, `rule_delete` views; update `schedule_form`, `edge_settings`; rewrite `to_job_config()` logic |
| `app/urls.py` | Add `schedules/<id>/rules/save/` and `schedules/<id>/rules/<rid>/delete/` |
| `app/engine_client.py` | Add `get_model_labels(model_path)` |
| `templates/schedules/form.html` | Full rewrite with new sections + JS |
| `templates/settings/index.html` | Add Telegram + Redis sections to edge settings form |

#### soca-engine
| File | Change |
|------|--------|
| `requirements.txt` | Add `croniter` |
| `models/schemas.py` | Add `iou_threshold` to `JobConfig`; add `dwell_seconds` to `Detection`; add `stream` to `RuleAction` |
| `core/yolo_inference.py` | Add `iou_threshold` param to `infer()`; pass to `model.track()` |
| `api/labels.py` | New — labels endpoint |
| `api/router.py` (or equivalent) | Register labels route |
| `core/detection_worker.py` | New-rules evaluation loop + helpers: `_update_dwell`, `_cron_active`, `_in_cooldown`, `_filter_processing`, `_filter_cls`, `_passes_duration`, `_dispatch_rule_actions` |
| `core/action_dispatcher.py` | Read `action.stream` for `publish_queue`; fallback to `cfg.output.stream_name` |

---

### Verification

1. `python manage.py makemigrations && python manage.py migrate` — Rule table + EdgeConfig.redis_stream created
2. Open schedule form → pick model → cls_id checkboxes populate with label names from engine
3. Add 2 rules → save → inspect `to_job_config()` output — both rules present with correct fields
4. Start schedule → verify engine receives `iou_threshold`, `cron_schedule`, `duration_op`, `processing`
5. Set cron to past hours → verify rule does not fire
6. Set `duration_op=gte, duration_seconds=5` → verify alert only fires after 5s continuous detection
7. `processing=detected` → verify alert fires even when object is outside ROI
8. `cls_operator=not_in` → verify alert fires for any class NOT in the list
9. Telegram/Redis action flags → verify notifications sent using EdgeConfig credentials
10. Schedule with no rules → verify fallback to legacy single-rule behavior
11. Per-rule cooldown → fire rule, verify second alert suppressed within cooldown window

### Control Design Spec

_Source: `soca-control/design-spec.md`_

## soca-control Design Spec
**Date:** 2026-03-21
**Last Updated:** 2026-03-28

### Context

soca-dashboard manages a single edge device (one soca-engine instance). As deployments grow, operators need a single control plane to see all edge devices, cameras, and alerts in one place — without logging into each device separately.

soca-control is the **central dashboard** for the entire SOCA fleet. It consumes alerts published by each edge's soca-engine (via Redis Stream or Google Pub/Sub), stores them locally, and provides:
- Fleet-wide visibility (all edges, cameras, active jobs)
- Live video monitor (MJPEG links direct to each edge)
- Filterable + exportable reports per incident category with Statistics and Detail tabs
- System settings and user management

---

### Architecture

#### Technology Stack
- **Framework:** Django 6.0.x + Python
- **ORM / DB:** Django ORM, SQLite (local)
- **Background workers:**
  - `consume_streams` — Redis Stream consumer (one process per fleet)
  - `consume_pubsub` — Google Pub/Sub subscriber (one process per fleet, subscribes per-edge)
- **Redis:** Read-only consumer of each edge's `soca:detections` stream (when using Redis transport)
- **HTTP:** `requests` to call each edge's `/health`, `/system`, `/jobs/`, `/api/cameras/` endpoints
- **Export:** `csv` (stdlib) + `reportlab` for PDF
- **Frontend:** DaisyUI 4 (Tailwind CSS via CDN) + Chart.js 4 (via CDN), function-based views
- **Timezone:** `TIME_ZONE = 'Asia/Jakarta'` (GMT+7), `USE_TZ = True`

#### Key Design Decisions
- Edge discovery is **manual** — admin registers each edge URL in Settings
- soca-control supports two alert transports per-edge: Redis Stream or Google Pub/Sub (configured per edge)
- All alerts are **persisted locally** in soca-control's own SQLite for fast queries
- Live video uses **direct browser links** to each edge's MJPEG stream (no proxy)
- soca-engine has **no authentication** (runs on private network only) — no token/key needed
- Snapshot images served via GCS public URL (preferred) or soca-dashboard proxy endpoint
- Dashboard fetches edge health/system in parallel using `ThreadPoolExecutor` to avoid hanging when edges are offline
- Redis DLQ replay is **out of scope** — soca-control only reads live stream data

---

### Module Structure

```
soca-control/
├── manage.py
├── requirements.txt
├── soca_control/
│   ├── settings.py
│   └── urls.py
└── app/
    ├── models.py          # Edge, Alert
    ├── views.py           # all page views (dashboard, monitor, reports, settings, users)
    ├── urls.py            # all URL patterns
    ├── edge_client.py     # thin HTTP wrapper: health, system, jobs, cameras, edge-info, parallel fetch
    ├── redis_consumer.py  # management command: consume Redis streams → save Alert records
    ├── reports.py         # CSV and PDF export logic
    └── templates/
        ├── base.html
        ├── dashboard/
        │   └── index.html
        ├── monitor/
        │   └── index.html
        ├── reports/
        │   ├── intrusion.html
        │   ├── ppe.html
        │   ├── object_detection.html
        │   ├── _stats.html        # shared statistics tab partial
        │   ├── _detail_chart.html # Chart.js bar charts partial
        │   └── _filters.html      # shared filter bar partial
        ├── settings/
        │   └── index.html
        └── users/
            ├── list.html
            └── form.html          # shared create/edit form
```

---

### Data Models

#### SiteConfig
```python
SiteConfig:                     # singleton (pk=1)
    company_name     CharField
    app_name         CharField
    company_logo     FileField
    logo_height      PositiveIntegerField
    snapshot_storage CharField  # 'local' | 'gcs'
    gcs_bucket       CharField  # GCS bucket name (no gs:// prefix)
```

#### Edge
```python
Edge:
    name                CharField    # unique, e.g. "edge-mac-mini-bekasi"
    url                 URLField     # soca-dashboard base URL, e.g. "http://192.168.1.10:8080"
    engine_url          URLField     # soca-engine URL, e.g. "http://192.168.1.10:8001"
    redis_url           CharField    # e.g. "redis://192.168.1.10:6379"
    redis_stream        CharField    # default "soca:detections"
    subscriber_type     CharField    # 'redis' | 'pubsub'
    pubsub_project_id   CharField    # GCP project ID (Pub/Sub only)
    pubsub_subscription CharField    # Pub/Sub subscription name (Pub/Sub only)
    gcs_path_prefix     CharField    # GCS path prefix for this edge, e.g. "edge-mac-mini-bekasi"
    location            CharField    # free text (populated from /api/edge-info/ on save)
    notes               TextField    # optional
    active              BooleanField # enabled/disabled for polling
    api_key             CharField    # API key from soca-dashboard Settings > Operations
    mediamtx_url        URLField     # public HLS URL reachable from browser, e.g. "http://192.168.1.100:8888"; blank = hide stream viewer buttons
    last_seen           DateTimeField
    created_at          DateTimeField
```

#### Alert
```python
Alert:
    edge          ForeignKey(Edge, on_delete=SET_NULL, null=True)
    edge_name     CharField   # denormalized from Redis message: edge_name
    camera_id     CharField   # raw camera identifier from detection event
    camera_name   CharField   # human-readable camera name (populated from /api/cameras/)
    job_id        CharField
    frame_id      CharField
    timestamp     DateTimeField  # UTC, parsed from Redis message: timestamp field
    alert_category CharField     # "Intrusion", "PPE", "Object Detection"
    rule_name     CharField      # first triggered rule from rule_results list
    snapshot_message TextField
    snapshot_path  CharField     # edge-local path, used to build snapshot_url
    detection_count IntegerField # from roi_summary.total_count
    in_roi_count   IntegerField  # from roi_summary.in_roi_count
    cls_summary      JSONField     # from roi_summary.cls_counts, e.g. {"0": 2}
    cls_name_summary JSONField     # from roi_summary.cls_name_summary, e.g. {"person": 2, "helmet": 1}
    crossing_counts  JSONField     # {rule_name: {"in": N, "out": N}} for people_count rules
    crowd_count      IntegerField  # in-ROI person count for crowd rules
    lpr_plates       JSONField     # [{track_id, plate_number, plate_confidence, plate_expiry}]
    raw              JSONField     # full parsed message dict
    created_at       DateTimeField # when soca-control received it

    # Computed property (not stored):
    snapshot_url   # GCS public URL or edge-proxy URL depending on SiteConfig.snapshot_storage
                   # GCS:   https://storage.googleapis.com/{bucket}/{edge.gcs_path_prefix}/snapshots/...
                   # Local: {edge.url}/alerts/snapshot/{rel_path}
```

`display_camera()` method returns `camera_name` if set, falls back to `camera_id`.

---

### Redis Stream Wire Format

Each message published to `soca:detections` has a single field `"payload"` containing a JSON string.

Parsed payload (schema v2.3):
```json
{
  "schema_version": "2.2",
  "edge_name": "edge-bardi-home",
  "job_id": "550e8400-...",
  "frame_id": "bardi-stairs-front_20260320_100000_123456",
  "camera_id": "bardi-stairs-front",
  "camera_name": "Bardi Stairs Front",
  "timestamp": "2026-03-20T10:00:00.123456Z",
  "alert_category": "Intrusion",
  "snapshot_message": "2 person(s) detected at ...",
  "detections": [...],
  "roi_summary": {"total_count": 2, "in_roi_count": 1, "cls_counts": {"0": 2}},
  "aging": {...},
  "rule_results": [{"rule_name": "person_in_zone", "triggered": true, "actions_fired": [...]}],
  "snapshot_path": "snapshots/.../frame.jpg"
}
```

Key changes from v2.2:
- Added `camera_name` field (human-readable display name for the camera)
- `snapshot_message` now always populated (set from `message_template` on `publish_queue` action)

Mapping to Alert fields:
- `camera_name` ← `payload.get('camera_name') or ''`
- `rule_name` ← first item in `rule_results` where `triggered == true`
- `detection_count` ← `roi_summary.total_count`
- `in_roi_count` ← `roi_summary.in_roi_count`
- `cls_summary` ← `roi_summary.cls_counts`

---

### Edge Client API Surface (`edge_client.py`)

Two timeout constants:
- `TIMEOUT = 5` — used for settings/config operations
- `DASHBOARD_TIMEOUT = 2` — used for dashboard page loads to avoid blocking

All calls use `requests.get(url, timeout=...)`. Failures return `None` (logged, not raised).

| Function | Endpoint | Timeout | Returns |
|----------|----------|---------|---------|
| `fetch_edge_info(edge)` | `GET {edge.url}/api/edge-info/` | 5s | dict with name, engine_url, location |
| `fetch_health(edge)` | `GET {edge.engine_url}/health` | 5s | dict with status, active_jobs |
| `fetch_system(edge)` | `GET {edge.engine_url}/system` | 5s | dict with cpu.percent, memory.percent |
| `fetch_jobs(edge)` | `GET {edge.engine_url}/jobs/` | 5s | list of job dicts with job_id, status, camera_id, camera_name |
| `fetch_cameras(edge)` | `GET {edge.url}/api/cameras/` | 5s | dict `{camera_id: camera_name}` |
| `fetch_cameras_detail(edge)` | `GET {edge.url}/api/v1/cameras/` | 5s | list of camera dicts including `mediamtx_relay_enabled` |
| `fetch_edge_data_parallel(edges)` | health + system for all | 2s | `{edge.id: (health, system)}` |

`fetch_edge_data_parallel` uses `ThreadPoolExecutor(max_workers=min(len(edges), 10))` to fetch all edges concurrently, preventing page hangs when some edges are offline.

#### `/api/edge-info/` Response Shape
```json
{
  "edge_name": "edge-bardi-home",
  "engine_url": "http://localhost:8001",
  "location": {
    "address": "Jl. Sudirman No. 1",
    "building": "Gedung A",
    "floor": "Lantai 3"
  },
  "cameras": {"total": 4, "active": 3},
  "updated_at": "2026-03-21T10:00:00+00:00"
}
```

`location` is stored as a formatted string in `Edge.location`: `"{address}, {building}, {floor}"`.

#### `/api/cameras/` Response Shape (soca-dashboard)
```json
{"1": "Bardi Stairs Front", "2": "Garden Gate", "3": "Lobby"}
```

---

### Pages & Views

| URL | View | Purpose |
|-----|------|---------|
| `/` | `dashboard` | Fleet summary cards + compact edge cards + top cameras chart + recent activity feed |
| `/monitor/` | `monitor` | Grid of live MJPEG streams; placeholder shown when no active jobs |
| `/reports/intrusion/` | `report_intrusion` | Stats + Detail tabs, filters, CSV/PDF export |
| `/reports/ppe/` | `report_ppe` | PPE alerts with per-class violations breakdown |
| `/reports/object-detection/` | `report_object_detection` | Object detection alerts |
| `/reports/crowd/` | `report_crowd` | Crowd detection alerts |
| `/reports/people-counting/` | `report_people_counting` | Line-crossing counts (in/out) |
| `/reports/lpr/` | `report_lpr` | License plate recognition alerts |
| `/settings/` | `settings_page` | Edge CRUD, snapshot storage, GCS credentials |
| `/settings/edges/<id>/` | `edge_edit` | Edit edge: transport, GCS prefix, Pub/Sub config |
| `/settings/snapshot-storage/` | `snapshot_storage_save` | Configure GCS or local snapshot storage |
| `/settings/gac/` | `gac_save` | Upload GCS service account JSON key |
| `/users/` | `user_list` | User management |
| `/users/create/` | `user_create` | Create user |
| `/users/<id>/edit/` | `user_edit` | Edit user |
| `/users/<id>/delete/` | `user_delete` | Delete user |
| `/login/`, `/logout/` | Django built-in | Auth |

---

### Dashboard

Fleet summary (4 stat cards):
1. **Edges Online** — `online / total_edges` (green/warning/red)
2. **Active Jobs** — total running jobs across fleet
3. **Alerts Today** — count with trend vs. yesterday (↑/↓ in red/green)
4. **Alert Breakdown** — today's count by Intrusion / PPE / Object Detection

Edge device cards (compact, scalable grid — 1/2/3/4 cols by breakpoint):
- Colored top strip (green = online, red = offline)
- Edge name + Online/Offline status
- CPU and Memory progress bars (color-coded: green/warning/error thresholds at 60%/85%)
- 3-column alert count box: Intrusion / PPE / OD for today
- Active job count

Bottom row (2 cols):
- **Top Cameras Today** — Chart.js bar chart of top 5 cameras by alert count
- **Recent Activity** — last 10 alerts across fleet (category badge, camera name, message, time)

Dashboard uses `fetch_edge_data_parallel` so offline edges don't block page load.

---

### Report Pages

#### Tabs

Each report page (`intrusion`, `ppe`, `object_detection`) has two tabs controlled by `?tab=stats` (default) or `?tab=detail`.

Tab switching preserves all active filter params via `base_qs` (query string without the `tab` param).

#### Statistics Tab (`?tab=stats`)

Summary stat cards: today / this week / this month / week trend
Hourly distribution: CSS-based bar chart (0–23h)
14-day trend: CSS-based bar chart
Top cameras table (top 10 by count)
Top rules table (top 10)
Top edges table (top 10)
Average in-ROI count

Two Chart.js bar charts:
1. Incidents by Camera (top 20) — blue
2. Incidents by Edge Device (top 20) — teal

#### Detail Tab (`?tab=detail`)

Filterable table (up to 500 rows):
- Timestamp, Edge, Camera Name, Rule, Message, ROI count
- Snapshot thumbnail (if available) — clicking opens snapshot in a shared 960×720 resizable popup window (`snapshot_pop`); all report pages (Intrusion, PPE, Object Detection, People Counting, Crowd, LPR) use the same behavior

Export buttons (CSV / PDF) only shown on Detail tab.

#### Filters (`_filters.html`)

| Param | Filters on |
|-------|-----------|
| `edge` | `Alert.edge_name` |
| `camera` | `Alert.camera_id` (dropdown shows camera_name, value is camera_id) |
| `date_from` / `date_to` | `Alert.timestamp` date range |
| `rule` | `Alert.rule_name` |
| `q` | `icontains` search across `rule_name`, `camera_id`, `snapshot_message` |

Camera dropdown deduplication: query uses `order_by('camera_name', 'camera_id').values_list('camera_name', 'camera_id').distinct()` — explicit order_by prevents Django's default ordering from breaking DISTINCT.

Export:
- `?export=csv` → `StreamingHttpResponse` with csv writer
- `?export=pdf` → `HttpResponse` with reportlab (table layout)

---

### Monitor Page

- Calls `fetch_jobs(edge)` for all active edges to get list of running jobs
- For each running job: renders `<img src="{engine_url}/jobs/{job_id}/monitor">` with edge name + camera name label
- If no active jobs on an edge: shows a muted placeholder card
- If edge unreachable: shows placeholder
- Page auto-refreshes every 60s via `<meta http-equiv="refresh">`
- Job list includes `camera_name` populated from `j.config` in soca-engine
- **Maximize button** (expand icon, top-right of each card): opens the MJPEG stream in a controlled popup window (854×480, resizable) — clicking the thumbnail image also opens the same popup; uses `job_id` as the window name so clicking the same stream re-focuses the existing window rather than opening a duplicate

### Assets Page

- Lists all registered cameras per edge, grouped by edge
- Each camera row shows: name, site, floor, location, status, scheduler, **Stream** column
- **Stream column** per camera:
  - `Relay` badge (green) — soca-dashboard's `mediamtx_rtsp_url` is set; detection jobs connect via MediaMTX relay
  - `Direct` badge (yellow) — `mediamtx_rtsp_url` is blank; detection jobs connect directly to camera RTSP
  - Play button — only shown when `Edge.mediamtx_url` is set; opens `{mediamtx_url}/{camera_name}` (MediaMTX built-in HLS player) in a 900×600 resizable popup
- `mediamtx_relay_enabled` flag comes from soca-dashboard `api_v1_cameras` response per camera

---

### Background Workers

#### `consume_streams` (Redis)

File: `app/management/commands/consume_streams.py`

```
python manage.py consume_streams
```

- Runs as a long-lived process; filters edges where `subscriber_type='redis'`
- For each edge: opens Redis connection using `edge.redis_url`, reads `edge.redis_stream`
- Uses `XREAD BLOCK 5000` with last consumed message ID (tracked in memory per edge)
- On new message: parses payload JSON → creates `Alert` record
- All string fields use `or ''` / `or {}` guards to handle explicit JSON nulls (NOT NULL constraint safety)
- Reloads edge list every 60s; handles Redis connection errors with 30s retry

#### `consume_pubsub` (Google Pub/Sub)

File: `app/management/commands/consume_pubsub.py`

```
python manage.py consume_pubsub
```

- Runs as a long-lived process; filters edges where `subscriber_type='pubsub'`
- For each edge: opens `SubscriberClient` streaming pull on `projects/{pubsub_project_id}/subscriptions/{pubsub_subscription}`
- On new message: parses payload JSON → creates `Alert` record → `message.ack()`
- On JSON decode error: acks and discards (retrying non-JSON won't help)
- On save error: nacks (message retried by Pub/Sub)
- Reloads edge list every 10s; handles subscription errors gracefully
- Both `client` and `future` kept in `subscribers` dict to prevent GC of gRPC channel

#### Backfill Management Command

`python manage.py backfill_camera_names`

One-time command to populate `camera_name` on existing Alert records that were stored before the camera_name field was added. Calls `/api/cameras/` on each registered edge and updates matching alerts by `camera_id`.

---

### soca-dashboard Changes Required

These changes to soca-dashboard are needed for soca-control to work fully:

1. **`/api/cameras/` endpoint** — returns `{camera_id_str: camera_name}` dict; used by backfill command and `fetch_cameras()`
2. **`/api/v1/cameras/` endpoint** — returns full camera list with per-camera `mediamtx_relay_enabled` flag; used by Assets page
3. **`snapshot_image` view** — must NOT require login (browser `<img>` tags from soca-control can't authenticate)
4. **`alert_category` in schedule form** — dropdown with fixed values: Intrusion / Detection / PPE (not free text)
5. **`to_job_config()`** — includes `camera_name` and `message_template` in `publish_queue` action so soca-engine can populate `snapshot_message` and `camera_name` in Redis payloads
6. **`EdgeConfig.mediamtx_rtsp_url`** — when set, `Schedule.to_job_config()` produces `rtsp://localhost:8554/<camera_name>` instead of the direct camera URL; `mediamtx_relay_enabled: true` returned in `api_v1_cameras` response

---

### soca-engine Changes Required

1. **`JobConfig`** — added `camera_name: str = ""`
2. **`FrameResult`** — added `camera_name: str` field
3. **`detection_worker`** — passes `camera_name=cfg.camera_name` when constructing `FrameResult`
4. **`action_dispatcher`** — `publish_queue` action sets `result.snapshot_message` from `action.message_template` before publishing
5. **`output_publisher`** — includes `"camera_name": result.camera_name` in Redis payload dict
6. **`/jobs/` API** — job list includes `camera_id` and `camera_name` from `j.config`

---

### Settings: Edge Management

- List registered edges with status dot
- Add edge form: name, dashboard URL, engine URL, Redis URL, stream name
  - On save: calls `fetch_edge_info()` to verify and populate location string
- Deactivate/reactivate toggle (stops stream consumption without deleting)
- Delete edge (also deletes related Alerts)

---

### User Management

- Uses Django's built-in `User` model (no custom model)
- List: username, email, last login, staff flag
- Create: username, email, password, staff checkbox
- Edit: email, new password (optional), staff checkbox
- Delete: confirm prompt; cannot delete self

---

### Authentication

- Django session-based login
- All views: `@login_required`
- Exception: soca-dashboard's `snapshot_image` view is public (required for `<img>` tags in soca-control)
- `LOGIN_URL = '/login/'`

---

### Principles Applied

- **One file per concern** — models, views, edge_client, redis_consumer, reports each in their own file
- **Parallel by default** — dashboard uses ThreadPoolExecutor so one offline edge never blocks the page
- **Camera names over IDs** — all UI shows `camera_name` with `camera_id` as fallback; stored in Alert
- **Standard library first** — `csv` for CSV, `urllib.parse` for URL building
- **No over-engineering** — no REST API, no JS framework beyond DaisyUI+Chart.js, no Celery; Django views + one management command

---

### Verification

1. `pip install -r requirements.txt && python manage.py migrate`
2. `python manage.py createsuperuser`
3. Register an edge in Settings → verify `/api/edge-info/` call succeeds and location populates
4. Run `python manage.py consume_streams` in a second terminal
5. Trigger an alert on the edge → verify it appears in the dashboard and report pages with camera name
6. Verify snapshot thumbnail appears in Detail tab of report pages
7. Test Statistics tab: hourly chart, 14-day trend, top cameras/rules/edges, Chart.js bar charts
8. Test CSV export: `/reports/intrusion/?tab=detail&export=csv`
9. Test PDF export: `/reports/intrusion/?tab=detail&export=pdf`
10. Verify live monitor loads MJPEG `<img>` tags with camera name labels
11. Disconnect an edge → verify dashboard loads without hanging (parallel fetch)
12. Run `python manage.py backfill_camera_names` to populate camera names on existing alerts

### Control Edge Operations Plan

_Source: `soca-control/edge-operations-plan.md`_

## Edge Operations — soca-control Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add remote schedule control, remote purge trigger, and local Alert purge to soca-control, consuming soca-dashboard's new `/api/v1/` endpoints.

**Architecture:** `Edge` model gains an `api_key` field. `edge_client.py` gains five new functions using `Authorization: Api-Key` headers. A new Operations page per edge (`/edges/<id>/operations/`) shows schedule control, remote purge, and local Alert purge. The Settings page edge table gains an "Operations" link per row and an API Key field in the add-edge form.

**Tech Stack:** Django 6.x, requests, DaisyUI 4, vanilla JS fetch API

**Spec:** (in soca-dashboard repo) `docs/superpowers/specs/2026-03-22-edge-operations-design.md`

**Prerequisite:** soca-dashboard Plan 1 must be complete and running — the `/api/v1/` endpoints must exist on each target edge.

---

### File Map

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

### Task 1: Model Migration

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

### Task 2: edge_client New Functions

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

### Task 3: Operations Views

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

### Task 4: URL Patterns

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

### Task 5: Operations Page Template

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

### Task 6: Settings Template — API Key Field + Operations Link

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

### Task 7: Manual End-to-End Verification

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

### Control Implementation Plan

_Source: `soca-control/implementation-plan.md`_

## soca-control Implementation Plan
**Status: COMPLETE** (as of 2026-03-21)

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build soca-control, a Django web app that aggregates CCTV detection events from multiple edge devices and provides a central fleet dashboard, live monitor, filterable reports, and user management.

**Architecture:** Edge devices (soca-engine + soca-dashboard) publish detection events to Redis Streams. soca-control polls those streams via a background management command (`consume_streams`), persists alerts locally in SQLite, and serves Django views for the dashboard UI. Video streams are direct MJPEG links to each edge — no proxying.

**Tech Stack:** Django 6.0.x, SQLite, redis-py, requests, reportlab, DaisyUI 4 (Tailwind CSS via CDN), Chart.js 4 (via CDN), Python stdlib `csv`

---

### File Map

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

### Task 1: Project Scaffold ✅

- [x] Create Django project: `django-admin startproject soca_control .`
- [x] Create app: `python manage.py startapp app`
- [x] Write `requirements.txt` (django, requests, redis, reportlab)
- [x] Configure `settings.py`: INSTALLED_APPS, LOGIN_URL, ENGINE_TIMEOUT, TIME_ZONE='Asia/Jakarta'
- [x] Wire `soca_control/urls.py` → auth views + `include('app.urls')`

---

### Task 2: Models ✅

- [x] Write `Edge` model (name, url, engine_url, redis_url, redis_stream, location, active, last_seen)
- [x] Write `Alert` model (edge FK, edge_name, camera_id, camera_name, job_id, frame_id, timestamp, alert_category, rule_name, snapshot_message, snapshot_path, detection_count, in_roi_count, cls_summary, raw)
- [x] Add `snapshot_url` property to Alert (builds `{edge.url}/alerts/snapshot/{rel_path}`)
- [x] Add `display_camera()` method to Alert (returns camera_name or camera_id)
- [x] Run `makemigrations && migrate`

---

### Task 3: edge_client.py ✅

- [x] `_get(url, timeout)` — safe GET wrapper, returns None on failure
- [x] `fetch_edge_info(edge)` → `GET {edge.url}/api/edge-info/`
- [x] `fetch_health(edge)` → `GET {edge.engine_url}/health`
- [x] `fetch_system(edge)` → `GET {edge.engine_url}/system`
- [x] `fetch_jobs(edge)` → `GET {edge.engine_url}/jobs/`
- [x] `fetch_cameras(edge)` → `GET {edge.url}/api/cameras/` (returns `{camera_id: camera_name}`)
- [x] `fetch_edge_data_parallel(edges)` → ThreadPoolExecutor, DASHBOARD_TIMEOUT=2s

---

### Task 4: consume_streams (management command) ✅

- [x] `app/management/commands/consume_streams.py`
- [x] XREAD BLOCK 5000 per edge, cursor tracked in memory, starts at `$`
- [x] `parse_message` extracts all Alert fields including `camera_name`
- [x] All string fields use `or ''` to handle explicit JSON nulls
- [x] Reloads edge list every 60s
- [x] Retry on Redis connection error after 30s

---

### Task 5: backfill_camera_names (management command) ✅

- [x] `app/management/commands/backfill_camera_names.py`
- [x] For each active edge: calls `fetch_cameras()`, updates matching Alert records by camera_id
- [x] Run once to populate camera_name on 94 existing alerts

---

### Task 6: views.py + urls.py ✅

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

### Task 7: reports.py ✅

- [x] `export_csv(queryset, filename)` → StreamingHttpResponse
- [x] `export_pdf(queryset, title)` → HttpResponse with reportlab

---

### Task 8: Templates ✅

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

### Task 9: Settings & Migrations ✅

- [x] `TIME_ZONE = 'Asia/Jakarta'` in both soca-control and soca-dashboard
- [x] Migration 0003: add `camera_name` to Alert
- [x] Migration 0004: fix `default=''` for camera_name NOT NULL constraint

---

### soca-dashboard Changes ✅

- [x] Added `/api/cameras/` endpoint (public, returns `{camera_id_str: camera_name}`)
- [x] Removed `@login_required` from `snapshot_image` view (needed for `<img>` tags)
- [x] Changed `alert_category` field in schedule form from text input to dropdown (Intrusion/Detection/PPE)
- [x] `to_job_config()` includes `camera_name` and `message_template` in `publish_queue` action
- [x] `publish_redis` default changed to `True`

---

### soca-engine Changes ✅

- [x] `JobConfig` — added `camera_name: str = ""`
- [x] `FrameResult` — added `camera_name: str` field
- [x] `detection_worker` — passes `camera_name=cfg.camera_name` to FrameResult
- [x] `action_dispatcher` — `publish_queue` sets `result.snapshot_message` from `message_template`
- [x] `output_publisher` — includes `"camera_name"` in Redis payload
- [x] `/jobs/` API — job list includes `camera_id` and `camera_name`

---

### Verification

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

### Control Pub/Sub Transport

_Source: `soca-control/pubsub-transport-plan.md`_

## Google Pub/Sub Transport Implementation Plan

> **Status: COMPLETED 2026-03-25** — All 11 tasks implemented and committed. See commits: soca-control `7042f52`, soca-dashboard `c10ccf6`, soca-engine `843a2e9`.

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add Google Cloud Pub/Sub as an alternative message transport alongside Redis in soca-engine (publisher) and soca-control (subscriber/consumer microservice), with the choice configurable per-edge.

**Architecture:** soca-engine writes to either a Redis stream or a Pub/Sub topic based on `PUBLISHER_TYPE` env var (written by soca-dashboard's EdgeConfig save). soca-control has two independent management commands — existing `consume_streams` for Redis, new `consume_pubsub` for Pub/Sub — each run as a separate process/microservice. Both share the same `parse_message()` utility.

**Tech Stack:** `google-cloud-pubsub` (both soca-engine and soca-control), Django model migrations, DaisyUI settings UI.

---

### File Map

| File | Action | Change |
|------|--------|--------|
| `soca-engine/core/pubsub_publisher.py` | **Create** | Pub/Sub publish wrapper |
| `soca-engine/core/output_publisher.py` | **Modify** | Route to Redis or Pub/Sub |
| `soca-engine/config.py` | **Modify** | Add `PUBLISHER_TYPE`, `PUBSUB_PROJECT_ID`, `PUBSUB_TOPIC` |
| `soca-engine/requirements.txt` | **Modify** | Add `google-cloud-pubsub` |
| `soca-dashboard/app/models.py` | **Modify** | Add 3 fields to `EdgeConfig` |
| `soca-dashboard/app/migrations/0017_edgeconfig_pubsub.py` | **Create** | Migration |
| `soca-dashboard/app/views.py` | **Modify** | Read/save new fields + write to .env |
| `soca-dashboard/templates/settings/index.html` | **Modify** | Pub/Sub fields in Edge Config tab |
| `soca-control/app/models.py` | **Modify** | Add 3 fields to `Edge` |
| `soca-control/app/migrations/0011_edge_pubsub.py` | **Create** | Migration |
| `soca-control/app/templates/settings/edge_edit.html` | **Modify** | Pub/Sub fields in edge form |
| `soca-control/app/views.py` | **Modify** | Save new Edge fields |
| `soca-control/app/management/commands/message_parser.py` | **Create** | Shared `parse_message()` utility |
| `soca-control/app/management/commands/consume_streams.py` | **Modify** | Import `parse_message` from shared module |
| `soca-control/app/management/commands/consume_pubsub.py` | **Create** | Pub/Sub consumer microservice |
| `soca-control/requirements.txt` | **Modify** | Add `google-cloud-pubsub` |

---

### Task 1: soca-engine — Pub/Sub publisher

**Files:**
- Create: `soca-engine/core/pubsub_publisher.py`
- Modify: `soca-engine/requirements.txt`

- [ ] **Step 1: Add dependency**

In `soca-engine/requirements.txt`, add after `easyocr`:
```
google-cloud-pubsub>=2.21
```

- [ ] **Step 2: Create `core/pubsub_publisher.py`**

```python
import json
import logging
from google.cloud import pubsub_v1

logger = logging.getLogger(__name__)

_publisher: pubsub_v1.PublisherClient | None = None


def get_publisher() -> pubsub_v1.PublisherClient:
    global _publisher
    if _publisher is None:
        _publisher = pubsub_v1.PublisherClient()
    return _publisher


def publish_to_pubsub(topic_path: str, payload: dict):
    """Publish a single JSON message to a Pub/Sub topic.

    topic_path: full resource name, e.g.
        projects/my-project/topics/soca-detections
    """
    data = json.dumps(payload).encode("utf-8")
    try:
        future = get_publisher().publish(topic_path, data)
        future.result()  # wait for delivery confirmation
    except Exception as e:
        logger.error("Pub/Sub publish failed for %s: %s", topic_path, e)
        raise
```

- [ ] **Step 3: Verify import works (no syntax errors)**

```bash
cd soca-engine && python3 -c "from core.pubsub_publisher import publish_to_pubsub; print('ok')"
```
Expected: `ok`
(Will only fail at runtime if google-cloud-pubsub is not installed — install it: `pip3.12 install google-cloud-pubsub`)

---

### Task 2: soca-engine — Add config vars

**Files:**
- Modify: `soca-engine/config.py`

- [ ] **Step 1: Add Pub/Sub config vars to `config.py`**

After the `REDIS_STREAM_MAXLEN` line, add:
```python
PUBLISHER_TYPE   = os.getenv("PUBLISHER_TYPE", "redis")   # "redis" | "pubsub"
PUBSUB_PROJECT_ID = os.getenv("PUBSUB_PROJECT_ID", "")
PUBSUB_TOPIC      = os.getenv("PUBSUB_TOPIC", "soca-detections")
```

- [ ] **Step 2: Verify config loads**

```bash
cd soca-engine && python3 -c "import config; print(config.PUBLISHER_TYPE, config.PUBSUB_PROJECT_ID)"
```
Expected: `redis ` (empty string for project ID)

---

### Task 3: soca-engine — Route output_publisher.py

**Files:**
- Modify: `soca-engine/core/output_publisher.py`

- [ ] **Step 1: Add Pub/Sub routing to `publish_to_queue()`**

Replace the `publish_to_queue` function with:
```python
def publish_to_queue(result: FrameResult, stream_name: str):
    payload = _to_dict(result)

    if config.PUBLISHER_TYPE == "pubsub":
        if config.PUBSUB_PROJECT_ID and config.PUBSUB_TOPIC:
            topic_path = f"projects/{config.PUBSUB_PROJECT_ID}/topics/{config.PUBSUB_TOPIC}"
            try:
                from core.pubsub_publisher import publish_to_pubsub
                publish_to_pubsub(topic_path, payload)
                return
            except Exception as e:
                logger.warning(f"Pub/Sub publish failed, falling back to DLQ: {e}")
        else:
            logger.warning("PUBLISHER_TYPE=pubsub but PUBSUB_PROJECT_ID or PUBSUB_TOPIC not set; using DLQ")
        _write_dlq(result.job_id, json.dumps(payload))
        return

    # Redis path (default)
    serialised = json.dumps(payload)
    client = get_redis()
    if client:
        try:
            client.xadd(stream_name, {"payload": serialised}, maxlen=config.REDIS_STREAM_MAXLEN, approximate=True)
            return
        except Exception as e:
            logger.warning(f"Redis publish failed, falling back to DLQ: {e}")
    _write_dlq(result.job_id, serialised)
```

- [ ] **Step 2: Verify no import errors**

```bash
cd soca-engine && python3 -c "from core.output_publisher import publish_to_queue; print('ok')"
```
Expected: `ok`

---

### Task 4: soca-dashboard — EdgeConfig model + migration

**Files:**
- Modify: `soca-dashboard/app/models.py`
- Create: `soca-dashboard/app/migrations/0017_edgeconfig_pubsub.py`

- [ ] **Step 1: Add fields to `EdgeConfig` in `models.py`**

After the `redis_stream` field, add:
```python
publisher_type    = models.CharField(
    max_length=10, default='redis',
    choices=[('redis', 'Redis Stream'), ('pubsub', 'Google Pub/Sub')],
    help_text='Transport for publishing detection events'
)
pubsub_project_id = models.CharField(max_length=200, blank=True, default='',
    help_text='Google Cloud project ID, e.g. my-gcp-project')
pubsub_topic      = models.CharField(max_length=200, blank=True, default='soca-detections',
    help_text='Pub/Sub topic name (not full path), e.g. soca-detections')
```

- [ ] **Step 2: Create migration**

```bash
cd soca-dashboard && python manage.py makemigrations app --name edgeconfig_pubsub
```
Expected: `Migrations for 'app': app/migrations/0017_edgeconfig_pubsub.py`

- [ ] **Step 3: Apply migration**

```bash
cd soca-dashboard && python manage.py migrate
```
Expected: `Applying app.0017_edgeconfig_pubsub... OK`

---

### Task 5: soca-dashboard — Settings UI (Edge Config tab)

**Files:**
- Modify: `soca-dashboard/templates/settings/index.html`

- [ ] **Step 1: Add Pub/Sub section to Edge Config tab**

After the Redis Stream `form-control` block (around line 257), add:
```html
<!-- Publisher Transport -->
<div class="form-control md:col-span-2">
  <label class="label"><span class="label-text font-medium">Publisher Transport</span></label>
  <select name="publisher_type" id="publisher-type-select"
          class="select select-bordered select-sm w-48"
          onchange="togglePubSubFields(this.value)">
    <option value="redis" {% if edge.publisher_type != 'pubsub' %}selected{% endif %}>Redis Stream</option>
    <option value="pubsub" {% if edge.publisher_type == 'pubsub' %}selected{% endif %}>Google Pub/Sub</option>
  </select>
  <label class="label"><span class="label-text-alt text-xs text-base-content/40">Where to publish detection events</span></label>
</div>
<div id="pubsub-fields" class="md:col-span-2 grid grid-cols-1 md:grid-cols-2 gap-4"
     style="{% if edge.publisher_type != 'pubsub' %}display:none{% endif %}">
  <div class="form-control">
    <label class="label"><span class="label-text font-medium">GCP Project ID</span></label>
    <input type="text" name="pubsub_project_id"
           value="{{ edge.pubsub_project_id|default:'' }}"
           placeholder="my-gcp-project"
           class="input input-bordered input-sm font-mono" />
  </div>
  <div class="form-control">
    <label class="label"><span class="label-text font-medium">Pub/Sub Topic</span></label>
    <input type="text" name="pubsub_topic"
           value="{{ edge.pubsub_topic|default:'soca-detections' }}"
           placeholder="soca-detections"
           class="input input-bordered input-sm font-mono" />
    <label class="label"><span class="label-text-alt text-xs text-base-content/40">Topic name only — not full projects/…/topics/… path</span></label>
  </div>
</div>
```

Also add the hidden field for `publisher_type` in the Telegram tab form (which echoes edge config fields as hidden inputs). After the existing `redis_stream` hidden input:
```html
<input type="hidden" name="publisher_type" value="{{ edge.publisher_type|default:'redis' }}" />
<input type="hidden" name="pubsub_project_id" value="{{ edge.pubsub_project_id|default:'' }}" />
<input type="hidden" name="pubsub_topic" value="{{ edge.pubsub_topic|default:'soca-detections' }}" />
```

- [ ] **Step 2: Add `togglePubSubFields` JS function**

Add inside the `<script>` block before `showTab`:
```javascript
function togglePubSubFields(val) {
  document.getElementById('pubsub-fields').style.display = val === 'pubsub' ? '' : 'none';
}
```

---

### Task 6: soca-dashboard — views.py save + .env write

**Files:**
- Modify: `soca-dashboard/app/views.py`

- [ ] **Step 1: Read new fields in `edge_settings` view**

In the `edge_settings` POST handler, after reading `redis_stream`, add:
```python
edge.publisher_type    = p.get("publisher_type", "redis")
edge.pubsub_project_id = p.get("pubsub_project_id", "")
edge.pubsub_topic      = p.get("pubsub_topic", "soca-detections")
```

- [ ] **Step 2: Write new vars to .env in `_write_engine_env`**

Update `_write_engine_env(engine_env_path, edge_name)` signature to:
```python
def _write_engine_env(engine_env_path: str, edge_name: str, publisher_type: str = "redis",
                      pubsub_project_id: str = "", pubsub_topic: str = "soca-detections"):
```

Inside the function, alongside the `EDGE_NAME` logic, also update/insert:
- `PUBLISHER_TYPE`
- `PUBSUB_PROJECT_ID`
- `PUBSUB_TOPIC`

Pattern (same approach as EDGE_NAME):
```python
ENV_KEYS = {
    "EDGE_NAME": edge_name,
    "PUBLISHER_TYPE": publisher_type,
    "PUBSUB_PROJECT_ID": pubsub_project_id,
    "PUBSUB_TOPIC": pubsub_topic,
}
# For each line: if it starts with key= update it; if key not found, append it
```

- [ ] **Step 3: Pass new args when calling `_write_engine_env`**

In `edge_settings`:
```python
_write_engine_env(
    edge.engine_env_path, edge.edge_name,
    publisher_type=edge.publisher_type,
    pubsub_project_id=edge.pubsub_project_id,
    pubsub_topic=edge.pubsub_topic,
)
```

- [ ] **Step 4: Smoke-test in browser**

Start soca-dashboard, go to Settings → Edge Config → switch transport to Pub/Sub, fill in project/topic, Save. Verify soca-engine `.env` now contains `PUBLISHER_TYPE=pubsub`.

---

### Task 7: soca-control — Edge model + migration

**Files:**
- Modify: `soca-control/app/models.py`
- Create: `soca-control/app/migrations/0011_edge_pubsub.py`

- [ ] **Step 1: Add fields to `Edge` model**

After the `redis_stream` field, add:
```python
subscriber_type    = models.CharField(
    max_length=10, default='redis',
    choices=[('redis', 'Redis Stream'), ('pubsub', 'Google Pub/Sub')],
)
pubsub_project_id  = models.CharField(max_length=200, blank=True, default='')
pubsub_subscription = models.CharField(max_length=200, blank=True, default='soca-detections-sub',
    help_text='Pub/Sub subscription name (not full path)')
```

- [ ] **Step 2: Create and apply migration**

```bash
cd soca-control && python manage.py makemigrations app --name edge_pubsub && python manage.py migrate
```
Expected: `Applying app.0011_edge_pubsub... OK`

---

### Task 8: soca-control — Edge edit UI

**Files:**
- Modify: `soca-control/app/templates/settings/edge_edit.html`
- Modify: `soca-control/app/views.py`

- [ ] **Step 1: Add subscriber transport fields to edge_edit.html**

After the Redis URL + Stream Name fields, add:
```html
<div class="form-control">
  <label class="label label-text text-sm">Subscriber Transport</label>
  <select name="subscriber_type" id="sub-type-select"
          class="select select-bordered select-sm"
          onchange="toggleSubPubSubFields(this.value)">
    <option value="redis" {% if edge.subscriber_type != 'pubsub' %}selected{% endif %}>Redis Stream</option>
    <option value="pubsub" {% if edge.subscriber_type == 'pubsub' %}selected{% endif %}>Google Pub/Sub</option>
  </select>
</div>
<div id="sub-pubsub-fields" class="sm:col-span-2 grid grid-cols-1 sm:grid-cols-2 gap-3"
     style="{% if edge.subscriber_type != 'pubsub' %}display:none{% endif %}">
  <div class="form-control">
    <label class="label label-text text-sm">GCP Project ID</label>
    <input name="pubsub_project_id" value="{{ edge.pubsub_project_id }}"
           placeholder="my-gcp-project" class="input input-bordered input-sm font-mono">
  </div>
  <div class="form-control">
    <label class="label label-text text-sm">Pub/Sub Subscription</label>
    <input name="pubsub_subscription" value="{{ edge.pubsub_subscription }}"
           placeholder="soca-detections-sub" class="input input-bordered input-sm font-mono">
  </div>
</div>
```

Add at end of the form (before `</form>`):
```html
<script>
function toggleSubPubSubFields(val) {
  document.getElementById('sub-pubsub-fields').style.display = val === 'pubsub' ? '' : 'none';
}
</script>
```

- [ ] **Step 2: Update `edge_edit` view in `views.py` to save new fields**

In the POST handler for edge edit, after saving `redis_stream`, add:
```python
edge.subscriber_type     = request.POST.get('subscriber_type', 'redis')
edge.pubsub_project_id   = request.POST.get('pubsub_project_id', '')
edge.pubsub_subscription = request.POST.get('pubsub_subscription', 'soca-detections-sub')
```

---

### Task 9: soca-control — Shared message_parser module

**Files:**
- Create: `soca-control/app/management/commands/message_parser.py`
- Modify: `soca-control/app/management/commands/consume_streams.py`

- [ ] **Step 1: Create `message_parser.py` with the `parse_message` function**

Extract `parse_message()` from `consume_streams.py` into a new file:
```python
from django.utils.dateparse import parse_datetime
from django.utils import timezone


def parse_message(payload: dict) -> dict:
    roi = payload.get('roi_summary') or {}
    rules = payload.get('rule_results') or []
    rule_name = next((r['rule_name'] for r in rules if r.get('triggered')), '')

    lpr_plates = [
        {k: r.get(k, '') for k in ('plate_number', 'plate_expiry', 'plate_confidence')}
        for r in (payload.get('lpr_results') or [])
        if r.get('plate_number')
    ]

    return {
        'edge_name':        payload.get('edge_name') or '',
        'camera_id':        payload.get('camera_id') or '',
        'camera_name':      payload.get('camera_name') or '',
        'job_id':           payload.get('job_id') or '',
        'frame_id':         payload.get('frame_id') or '',
        'timestamp':        parse_datetime(payload.get('timestamp') or '') or timezone.now(),
        'alert_category':   payload.get('alert_category') or '',
        'rule_name':        rule_name,
        'snapshot_message': payload.get('snapshot_message') or '',
        'snapshot_path':    payload.get('snapshot_path') or '',
        'detection_count':  roi.get('total_count', 0),
        'in_roi_count':     roi.get('in_roi_count', 0),
        'cls_summary':      roi.get('cls_counts', {}),
        'crossing_counts':  payload.get('crossing_counts') or {},
        'crowd_count':      payload.get('crowd_count') or 0,
        'lpr_plates':       lpr_plates,
        'raw':              payload,
    }
```

- [ ] **Step 2: Update `consume_streams.py` to import from shared module**

Remove the `parse_message` function body from `consume_streams.py` and replace with:
```python
from app.management.commands.message_parser import parse_message
```

- [ ] **Step 3: Verify Redis consumer still works**

```bash
cd soca-control && python manage.py check
```
Expected: `System check identified no issues (0 silenced).`

---

### Task 10: soca-control — consume_pubsub.py microservice

**Files:**
- Create: `soca-control/app/management/commands/consume_pubsub.py`
- Modify: `soca-control/requirements.txt`

- [ ] **Step 1: Add google-cloud-pubsub to requirements**

In `soca-control/requirements.txt`, add:
```
google-cloud-pubsub>=2.21
```

- [ ] **Step 2: Create `consume_pubsub.py`**

```python
import json
import logging
import time
from django.core.management.base import BaseCommand

from google.cloud import pubsub_v1

from app.models import Edge, Alert
from app.management.commands.message_parser import parse_message

logger = logging.getLogger(__name__)
RELOAD_INTERVAL = 60   # seconds between edge list reloads


class Command(BaseCommand):
    help = 'Consume Google Pub/Sub subscriptions from all active Pub/Sub edges.'

    def handle(self, *args, **options):
        self.stdout.write('Starting consume_pubsub...')
        subscribers = {}   # edge.id → StreamingPullFuture
        last_reload = 0
        edges = []

        while True:
            now = time.time()
            if now - last_reload > RELOAD_INTERVAL:
                edges = list(Edge.objects.filter(active=True, subscriber_type='pubsub'))
                last_reload = now
                # Cancel futures for removed edges
                active_ids = {e.id for e in edges}
                for eid in list(subscribers.keys()):
                    if eid not in active_ids:
                        subscribers[eid].cancel()
                        del subscribers[eid]

            for edge in edges:
                if edge.id not in subscribers:
                    if not edge.pubsub_project_id or not edge.pubsub_subscription:
                        logger.warning('Edge %s missing pubsub_project_id or pubsub_subscription', edge.name)
                        continue
                    sub_path = f"projects/{edge.pubsub_project_id}/subscriptions/{edge.pubsub_subscription}"
                    try:
                        client = pubsub_v1.SubscriberClient()
                        future = client.subscribe(sub_path, callback=_make_callback(edge))
                        subscribers[edge.id] = future
                        self.stdout.write(f'Subscribed to {sub_path} for edge {edge.name}')
                    except Exception as exc:
                        logger.warning('Cannot subscribe for %s: %s', edge.name, exc)

            time.sleep(5)


def _make_callback(edge: Edge):
    def callback(message):
        try:
            payload = json.loads(message.data.decode('utf-8'))
            data = parse_message(payload)
            Alert.objects.create(edge=edge, **data)
            message.ack()
        except Exception as exc:
            logger.error('Failed to save alert from %s: %s', edge.name, exc)
            message.nack()
    return callback
```

- [ ] **Step 3: Verify management command registers**

```bash
cd soca-control && python manage.py help consume_pubsub
```
Expected: Shows command help text without errors.

- [ ] **Step 4: Test with `--check` flag (dry run)**

Start the command and verify it runs without crashing when no Pub/Sub edges are configured:
```bash
cd soca-control && timeout 10 python manage.py consume_pubsub || true
```
Expected: Prints `Starting consume_pubsub...` and loops silently.

---

### Task 11: Integration verification

- [ ] **Step 1: Test Redis path unchanged**

With `PUBLISHER_TYPE=redis` (default), start a job — verify events still arrive in `consume_streams`.

- [ ] **Step 2: Test Pub/Sub path end-to-end**

1. Set `PUBLISHER_TYPE=pubsub`, `PUBSUB_PROJECT_ID=<your-project>`, `PUBSUB_TOPIC=soca-detections` in soca-engine `.env`
2. Create Pub/Sub topic + subscription in GCP (or use emulator)
3. Set Edge in soca-control to `subscriber_type=pubsub`, fill project/subscription
4. Run `python manage.py consume_pubsub` in one terminal
5. Trigger a detection event
6. Verify Alert is created with correct fields

- [ ] **Step 3: Verify soca-dashboard settings save writes to .env**

1. Go to Settings → Edge Config → switch to Pub/Sub, fill fields, Save
2. `cat soca-engine/.env` → confirm `PUBLISHER_TYPE=pubsub` is written

---

### Running as separate microservices

Each consumer runs as its own process:

```bash
# Terminal 1 — Redis consumer (existing)
python manage.py consume_streams

# Terminal 2 — Pub/Sub consumer (new)
python manage.py consume_pubsub
```

For production, configure as separate systemd units or Docker containers. Only start the one matching your configured transport.

**GCP credentials:** Set `GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json` env var before running `consume_pubsub` and before running soca-engine (when using Pub/Sub publisher). Or use Workload Identity / ADC on GCE/GKE.

## 7. Engine And Services

### Engine API Testing

_Source: `soca-engine/api-testing.md`_

## API Testing Guide

Base URL: `http://localhost:8001`

**Test Camera:** Bardi-Stairs-Front — `rtsp://localhost:8554/Bardi-Stairs-Front`

---

### 1. Health Check

```bash
curl http://localhost:8001/health
```

Expected:
```json
{
  "status": "ok",
  "redis": "connected",
  "active_jobs": 0,
  "uptime_seconds": 12
}
```

---

### 2. List Available Models

```bash
curl http://localhost:8001/models
```

Expected:
```json
{
  "models": [
    {"name": "yolo11n.pt", "path": "yolo/yolo11n.pt"}
  ]
}
```

---

### 3. Start a Job — Basic (no ROI, no rules)

```bash
curl -X POST http://localhost:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://localhost:8554/Bardi-Stairs-Front",
    "model_path": "yolo/yolo11n.pt",
    "cls_ids": [0],
    "frame_interval_ms": 1000
  }'
```

Expected:
```json
{
  "job_id": "550e8400-e29b-41d4-a716",
  "status": "started",
  "started_at": "2026-03-20T10:00:00Z"
}
```

---

### 4. Start a Job — With Monitor (live stream analytics)

```bash
curl -X POST http://localhost:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "job_id": "job-stairs-monitor",
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://localhost:8554/Bardi-Stairs-Front",
    "model_path": "yolo/yolo11n.pt",
    "cls_ids": [0],
    "frame_interval_ms": 1000,
    "monitor": true
  }'
```

Then open in browser:
```
http://localhost:8001/jobs/job-stairs-monitor/monitor
```

Stream shows:
- Bounding boxes — green (inside ROI) / gray (outside ROI)
- Top bar: job id, camera, detection count, ROI count, rule status
- Bottom bar: aging duration per detected class

---

### 5. Start a Job — With ROI Polygon + Monitor

```bash
curl -X POST http://localhost:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "job_id": "job-stairs-roi",
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://localhost:8554/Bardi-Stairs-Front",
    "model_path": "yolo/yolo11n.pt",
    "cls_ids": [0, 1, 2],
    "frame_interval_ms": 2000,
    "roi": {
      "type": "POLYGON",
      "points": [[100, 200], [600, 200], [600, 500], [100, 500]]
    },
    "monitor": true
  }'
```

Monitor URL:
```
http://localhost:8001/jobs/job-stairs-roi/monitor
```

---

### 6. Start a Job — With ROI Rectangle

```bash
curl -X POST http://localhost:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://localhost:8554/Bardi-Stairs-Front",
    "model_path": "yolo/yolo11n.pt",
    "cls_ids": [0],
    "frame_interval_ms": 1000,
    "roi": {
      "type": "RECT",
      "points": [[50, 100], [700, 500]]
    },
    "monitor": true
  }'
```

---

### 7. Start a Job — With Rules (person count threshold)

```bash
curl -X POST http://localhost:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "job_id": "job-stairs-rules",
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://localhost:8554/Bardi-Stairs-Front",
    "model_path": "yolo/yolo11n.pt",
    "cls_ids": [0],
    "frame_interval_ms": 1000,
    "roi": {
      "type": "POLYGON",
      "points": [[100, 200], [600, 200], [600, 500], [100, 500]]
    },
    "rules": [
      {
        "name": "too_many_people",
        "priority": 10,
        "when_all": [
          {"path": "detections.in_roi_count", "op": "gte", "value": 3}
        ],
        "actions": [
          {"type": "save_snapshot"},
          {"type": "publish_queue"},
          {"type": "log", "level": "warning"}
        ]
      }
    ],
    "monitor": true
  }'
```

---

### 8. Start a Job — With Aging Rule (person present > 30 seconds)

```bash
curl -X POST http://localhost:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "job_id": "job-stairs-aging",
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://localhost:8554/Bardi-Stairs-Front",
    "model_path": "yolo/yolo11n.pt",
    "cls_ids": [0],
    "frame_interval_ms": 1000,
    "aging": {
      "window_seconds": 120,
      "cooldown_seconds": 60
    },
    "rules": [
      {
        "name": "person_loitering",
        "priority": 10,
        "when_all": [
          {"path": "detections.in_roi_count",       "op": "gte", "value": 1},
          {"path": "aging.cls_0.duration_seconds",  "op": "gte", "value": 30},
          {"path": "aging.cls_0.in_cooldown",       "op": "eq",  "value": false}
        ],
        "actions": [
          {"type": "save_snapshot"},
          {"type": "publish_queue"},
          {"type": "log", "level": "warning"}
        ]
      }
    ],
    "monitor": true
  }'
```

---

### 9. Start a Job — With Webhook Action

```bash
curl -X POST http://localhost:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://localhost:8554/Bardi-Stairs-Front",
    "model_path": "yolo/yolo11n.pt",
    "cls_ids": [0, 2, 3],
    "frame_interval_ms": 2000,
    "rules": [
      {
        "name": "intruder_detected",
        "priority": 1,
        "when_all": [
          {"path": "detections.in_roi_count", "op": "gte", "value": 1}
        ],
        "actions": [
          {"type": "save_snapshot"},
          {"type": "webhook", "url": "http://localhost:8000/api/alerts/receive/"},
          {"type": "log", "level": "error"}
        ]
      }
    ],
    "output": {
      "redis_url": "redis://localhost:6379",
      "stream_name": "soca:detections",
      "max_snapshot_per_minute": 10,
      "snapshot_dir": "snapshots/"
    },
    "monitor": true
  }'
```

---

### 10. Start a Job — Full Example (all options)

```bash
curl -X POST http://localhost:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "job_id": "job-stairs-01",
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://localhost:8554/Bardi-Stairs-Front",
    "model_path": "yolo/yolo11n.pt",
    "cls_ids": [0, 1, 2],
    "frame_interval_ms": 1500,
    "roi": {
      "type": "POLYGON",
      "points": [[50, 50], [800, 50], [800, 600], [50, 600]]
    },
    "aging": {
      "window_seconds": 60,
      "cooldown_seconds": 30
    },
    "rules": [
      {
        "name": "person_detected",
        "priority": 10,
        "when_all": [
          {"path": "detections.in_roi_count", "op": "gte", "value": 1}
        ],
        "actions": [
          {"type": "publish_queue"},
          {"type": "log", "level": "info"}
        ]
      },
      {
        "name": "crowding_alert",
        "priority": 5,
        "when_all": [
          {"path": "detections.in_roi_count", "op": "gte", "value": 5}
        ],
        "actions": [
          {"type": "save_snapshot"},
          {"type": "publish_queue"},
          {"type": "webhook", "url": "http://localhost:8000/api/alerts/receive/"},
          {"type": "log", "level": "error"}
        ]
      }
    ],
    "output": {
      "redis_url": "redis://localhost:6379",
      "stream_name": "soca:detections",
      "max_snapshot_per_minute": 5,
      "snapshot_dir": "snapshots/"
    },
    "monitor": true
  }'
```

Monitor URL:
```
http://localhost:8001/jobs/job-stairs-01/monitor
```

---

### 11. View Live Monitor Stream

```bash
# Open in browser — shows annotated video with detection overlays
http://localhost:8001/jobs/JOB_ID/monitor
```

Returns `400` if job was started without `monitor: true`.
Returns `404` if job is not running.

---

### 12. Get Job Status

```bash
curl http://localhost:8001/jobs/JOB_ID
```

Expected:
```json
{
  "job_id": "job-stairs-01",
  "status": "running",
  "started_at": "2026-03-20T10:00:00Z",
  "frames_processed": 240,
  "events_triggered": 3,
  "last_frame_at": "2026-03-20T10:04:00Z",
  "error_msg": null
}
```

---

### 13. List All Jobs

```bash
curl http://localhost:8001/jobs/
```

---

### 14. Stop a Job

```bash
curl -X POST http://localhost:8001/jobs/JOB_ID/stop
```

Expected:
```json
{
  "job_id": "job-stairs-01",
  "status": "stopped"
}
```

---

### Rule Condition Reference

| path | type | description |
|---|---|---|
| `detections.count` | int | total detections in frame |
| `detections.in_roi_count` | int | detections inside ROI |
| `detections.cls_ids` | list | list of all detected cls_ids |
| `detections.cls_counts.0` | int | count of cls_id 0 |
| `detections.cls_counts.1` | int | count of cls_id 1 |
| `aging.cls_0.duration_seconds` | float | seconds cls_id 0 has been present continuously |
| `aging.cls_0.in_cooldown` | bool | true if cls_id 0 is in cooldown |

### Operators Reference

| op | meaning |
|---|---|
| `eq` | == |
| `neq` | != |
| `gte` | >= |
| `lte` | <= |
| `gt` | > |
| `lt` | < |
| `contains` | value in list |
| `not_contains` | value not in list |
| `exists` | path is not null |

### Publisher Transport Reference

Set in `soca-engine/.env` (or via soca-dashboard Settings → Edge Config → Publisher Transport).

| Var | Value | Behaviour |
|-----|-------|-----------|
| `PUBLISHER_TYPE` | `redis` (default) | XADD to Redis stream via `REDIS_STREAM_NAME` |
| `PUBLISHER_TYPE` | `pubsub` | Publish to Pub/Sub topic; also set `PUBSUB_PROJECT_ID` and `PUBSUB_TOPIC` |
| `PUBSUB_PROJECT_ID` | e.g. `my-gcp-project` | Required when `PUBLISHER_TYPE=pubsub` |
| `PUBSUB_TOPIC` | e.g. `soca-detections` | Topic name only (not full path) |

> When `PUBLISHER_TYPE=pubsub` and credentials/topic are missing, events fall back to the DLQ directory.

---

### `monitor` Attribute Reference

| value | behavior |
|---|---|
| `false` (default) | No live stream, detection runs headless |
| `true` | Annotated MJPEG stream available at `/jobs/{job_id}/monitor` |

### cls_id Reference (yolo11n default)

| cls_id | label |
|---|---|
| 0 | person |
| 1 | bicycle |
| 2 | car |
| 3 | motorcycle |
| 5 | bus |
| 7 | truck |
| 15 | cat |
| 16 | dog |

### Engine Object Detection Processor

_Source: `soca-engine/object-detection-processor.md`_

## SOCA Object Detection Processor — Technical Specification v3

### Overview

A standalone **FastAPI microservice** (`soca-engine`) that:
1. Accepts a detection job via REST API and immediately starts processing
2. Connects to an RTSP stream via **MediaMTX relay** (TCP transport forced)
3. Runs YOLO inference per frame within a configurable ROI
4. Tracks object presence duration (aging) across frames
5. Evaluates time-aware rules per frame
6. Outputs results to **Redis Streams** and persists to **local SQLite DB**
7. Optionally streams **live annotated video** (MJPEG) via `monitor=true`

---

### Architecture

```
┌──────────────────────────────────────────────────────────┐
│                      FastAPI App                          │
│                                                           │
│  POST /jobs/start         →  JobManager.start()          │
│  GET  /jobs/{id}          →  JobManager.status()         │
│  POST /jobs/{id}/stop     →  JobManager.stop()           │
│  GET  /jobs/{id}/monitor  →  MJPEG live stream           │
│  GET  /jobs/              →  JobManager.list()           │
│  GET  /models             →  list available YOLO models   │
│  GET  /health             →  liveness + queue check      │
└──────────────┬────────────────────────────────────────────┘
               │ spawns one Thread per job
               ▼
┌──────────────────────────────────────────────────────────┐
│              DetectionWorker (daemon Thread)              │
│                                                           │
│  RTSPCapture  (TCP forced via OPENCV_FFMPEG_CAPTURE_OPTIONS)
│      │ raw frame                                         │
│      ▼                                                   │
│  FrameGate (interval + blank check)                      │
│      │ qualified frame                                   │
│      ▼                                                   │
│  YOLOInference  (model singleton, cls_id filter)         │
│      │ raw detections                                    │
│      ▼                                                   │
│  ROIFilter  (point-in-polygon per detection)             │
│      │ roi-annotated detections                          │
│      ▼                                                   │
│  StateTracker  (aging / duration per object class)       │
│      │ enriched context                                  │
│      ▼                                                   │
│  RuleEngine  (when_all / when_any + actions)             │
│      │ rule results + actions to fire                    │
│      ▼                                                   │
│  ActionDispatcher                                        │
│      ├──► SnapshotManager  (save JPEG on trigger)        │
│      ├──► QueuePublisher   (Redis Streams XADD or Pub/Sub)│
│      ├──► WebhookClient    (fire-and-forget POST)        │
│      └──► DBWriter         (SQLite via SQLAlchemy)       │
│      │                                                   │
│      └──► MonitorStream    (if monitor=true)             │
│               └──► MJPEG buffer → /jobs/{id}/monitor    │
└──────────────────────────────────────────────────────────┘
```

---

### MediaMTX Integration

MediaMTX is treated as an **external dependency** — assumed to be running and pre-configured.

- soca-engine **does not start or manage** MediaMTX
- soca-engine consumes RTSP URLs that MediaMTX proxies/re-streams
- The `rtsp_url` in `JobConfig` must point to a MediaMTX path (e.g. `rtsp://localhost:8554/Bardi-Stairs-Front`)
- TCP transport is **forced globally** via `os.environ["OPENCV_FFMPEG_CAPTURE_OPTIONS"] = "rtsp_transport;tcp"`
- MediaMTX config (path rules, auth, source cameras) is managed separately via `mediamtx.yml`
- Camera source URLs live inside `mediamtx.yml` — soca-engine only talks to MediaMTX output paths

If RTSP connection fails, the worker retries with exponential backoff (max 5 retries, 2s → 32s), then marks the job as `error`.

**Example MediaMTX path config (`mediamtx.yml`):**
```yaml
paths:
  Bardi-Stairs-Front:
    source: rtsp://admin:abim@192.168.18.71:8554/Streaming/Channels/101
    rtspTransport: tcp
    sourceOnDemand: no
```

**soca-engine consumes:**
```
rtsp://localhost:8554/Bardi-Stairs-Front
```

---

### API Contract

#### `POST /jobs/start`

**Request Body:**
```json
{
  "job_id": "optional-custom-id",
  "camera_id": "bardi-stairs-front",
  "rtsp_url": "rtsp://localhost:8554/Bardi-Stairs-Front",
  "model_path": "yolo/yolo11n.pt",
  "cls_ids": [0, 1, 5],
  "roi": {
    "type": "POLYGON",
    "points": [[0.156, 0.556], [0.625, 0.556], [0.625, 1.0], [0.156, 1.0]]
  },
  "frame_interval_ms": 1000,
  "aging": {
    "window_seconds": 30,
    "cooldown_seconds": 60
  },
  "rules": [
    {
      "name": "person_in_zone_too_long",
      "priority": 10,
      "category": "Intrusion",
      "when_all": [
        {"path": "detections.in_roi_count",         "op": "gte", "value": 1},
        {"path": "aging.cls_0.duration_seconds",    "op": "gte", "value": 30},
        {"path": "detections.cls_counts.0",         "op": "gte", "value": 1}
      ],
      "when_any": [],
      "actions": [
        {"type": "save_snapshot"},
        {"type": "publish_queue"},
        {
          "type": "telegram",
          "bot_token": "123456789:AABBcc...",
          "chat_id": "-1001234567890",
          "message_template": "{in_roi_count} person(s) detected at {time} [{category}]"
        },
        {
          "type": "webhook",
          "url": "http://localhost:8000/api/alerts/receive/",
          "message_template": "{in_roi_count} person(s) in zone at {time}"
        },
        {"type": "log", "level": "warning"}
      ]
    }
  ],
  "output": {
    "redis_url": "redis://localhost:6379",
    "stream_name": "soca:detections",
    "max_snapshot_per_minute": 5,
    "snapshot_dir": "snapshots/"
  },
  "monitor": false
}
```

**`monitor` field:**
| value | behavior |
|---|---|
| `false` (default) | Detection runs headless, no video output |
| `true` | Annotated MJPEG stream available at `GET /jobs/{job_id}/monitor` |

**Response `200`:**
```json
{
  "job_id": "550e8400-e29b-41d4-a716",
  "status": "started",
  "started_at": "2026-03-20T10:00:00Z"
}
```

**Response `409` — job already running:**
```json
{"detail": "Job 550e8400 is already running"}
```

**Response `503` — capacity reached:**
```json
{"detail": "Max concurrent jobs reached"}
```

---

#### `GET /jobs/{job_id}/monitor`

Returns a live **MJPEG stream** of the annotated video.

- Returns `400` if job was started without `monitor=true`
- Returns `404` if job is not running
- Open directly in browser or `<img>` tag

**Annotations shown on stream:**
- Bounding boxes — **green** (inside ROI) / **gray** (outside ROI)
- Label: `#{track_id} {cls_name} {confidence}`
- Top HUD bar: job id, camera, detection count, ROI count, rule trigger status
- Bottom bar: aging duration per detected class (yellow text)

---

#### `GET /jobs/{job_id}`
```json
{
  "job_id": "550e8400-e29b-41d4-a716",
  "status": "running",
  "started_at": "2026-03-20T10:00:00Z",
  "frames_processed": 1420,
  "events_triggered": 3,
  "last_frame_at": "2026-03-20T10:23:41Z",
  "error_msg": null
}
```

#### `POST /jobs/{job_id}/stop`
Signals the worker thread to stop gracefully (max 5s drain). Cleans up monitor buffer if active.

#### `GET /jobs/`
Returns list of all jobs (active + recent stopped/error), latest 50.

#### `GET /models`
```json
{
  "models": [
    {"name": "yolo11n.pt",         "path": "yolo/yolo11n.pt"},
    {"name": "yolov8s_custom.pt",  "path": "yolo/yolov8s_custom.pt"}
  ]
}
```
Scans `MODELS_DIR` for `.pt`, `.mlpackage`, `.onnx` files.

#### `GET /health`
```json
{
  "status":         "ok",
  "edge_name":      "edge-bardi-home",
  "redis":          "connected",
  "active_jobs":    2,
  "uptime_seconds": 3600
}
```

#### `GET /cpu`
Detailed processor information via `psutil` + `platform`.
```json
{
  "model":            "Apple M2",
  "architecture":     "arm64",
  "cores_physical":   8,
  "cores_logical":    8,
  "freq_mhz": {
    "current": 3200.0,
    "min":     600.0,
    "max":     3200.0
  },
  "percent_per_core": [12.5, 8.0, 45.0, 3.0, 6.2, 11.0, 2.5, 9.1],
  "percent_total":    12.2
}
```

#### `GET /system`
Aggregate resource snapshot: CPU, memory, storage, and network interfaces.
```json
{
  "cpu": {
    "percent": 12.5,
    "count":   8
  },
  "memory": {
    "total_mb": 16384,
    "used_mb":  8192,
    "percent":  50.0
  },
  "storage": {
    "total_gb": 512.0,
    "used_gb":  128.3,
    "free_gb":  383.7,
    "percent":  25.0
  },
  "network": [
    {
      "name":       "en0",
      "ip":         "192.168.1.10",
      "is_up":      true,
      "speed_mbps": 1000,
      "sent_mb":    512.3,
      "recv_mb":    1024.7
    }
  ]
}
```
- Storage is measured on the partition containing `SNAPSHOTS_DIR` (falls back to `/`)
- Network list includes only interfaces with an IPv4 address
- Requires `psutil==6.1.1` (added to `requirements.txt`)

---

### Core Components

#### 1. `RTSPCapture`

Wraps `cv2.VideoCapture` with TCP-forced FFMPEG backend and reconnect logic.

```python
class RTSPCapture:
    def __init__(self, rtsp_url: str, max_retries: int = 5)
    def read_frame(self) -> tuple[bool, np.ndarray | None]
    def release(self)
```

- `os.environ["OPENCV_FFMPEG_CAPTURE_OPTIONS"] = "rtsp_transport;tcp"` set at module level
- Backend: `cv2.CAP_FFMPEG` with fallback to default
- Buffer size forced to 1 (`CAP_PROP_BUFFERSIZE`) to minimize latency
- On read failure: increment failure counter → exponential backoff retry
- On `max_retries` exceeded: raises `RTSPConnectionError`

---

#### 2. `FrameGate`

Controls which frames proceed to inference.

```python
class FrameGate:
    def should_process(self, frame: np.ndarray) -> bool
```

Checks (in order, short-circuit on fail):
1. **Blank check** — `np.std(cv2.cvtColor(frame, BGR2GRAY)) < 10` → skip
2. **Uniform check** — `np.all(np.abs(frame - frame.mean()) < 15)` → skip
3. **Interval check** — `time.monotonic() - last_processed < interval_s` → skip

---

#### 3. `YOLOInference`

Singleton model cache by `model_path`. Thread-safe via `threading.Lock` during load.

- Only returns detections whose `cls_id` is in `job.cls_ids`
- Uses `model.track()` with `persist=True` for tracking across frames
- Raises `RuntimeError` if `MAX_LOADED_MODELS` exceeded → job start fails with `503`

**Detection dataclass:**
```python
@dataclass
class Detection:
    cls_id:     int
    cls_name:   str
    confidence: float
    bbox:       tuple[int,int,int,int]   # x1,y1,x2,y2
    track_id:   int | None
    in_roi:     bool = True

    @property
    def centroid(self) -> tuple[int,int]:
        return ((x1+x2)//2, (y1+y2)//2)
```

---

#### 4. `ROIFilter`

- Converts `roi.points` → `np.array(..., dtype=np.int32)`
- Per detection: checks `cv2.pointPolygonTest(polygon, centroid, False) >= 0`
- Sets `detection.in_roi = True/False` — does not drop detections, only annotates
- If no ROI configured → all detections marked `in_roi = True`

**Supported ROI types:**
| Type | Handling |
|---|---|
| `POLYGON` | Direct `pointPolygonTest` |
| `RECT` | Converted to 4-point polygon |

---

#### 5. `StateTracker`

Tracks continuous presence of each `cls_id` across frames per job.

- If `cls_id` detected (in ROI) → accumulate `duration_seconds = now - first_seen`
- If `cls_id` absent → reset state for that cls
- After rule triggers → start cooldown (`in_cooldown=True`); auto-expires after `cooldown_seconds`
- `window_seconds` auto-resets duration to prevent infinite accumulation

**AgingContext output:**
```python
{
  "cls_0": {
    "duration_seconds": 45.3,
    "first_seen": "2026-03-20T10:00:00Z",
    "in_cooldown": false
  }
}
```

---

#### 6. `RuleEngine`

Evaluates rules against a unified context dict. Dot-notation path resolver.

**Full rule context:**
```python
{
  "detections": {
    "count":        3,
    "in_roi_count": 2,
    "cls_ids":      [0, 0, 5],
    "cls_counts":   {"0": 2, "5": 1}
  },
  "aging": {
    "cls_0": {"duration_seconds": 45.3, "first_seen": "...", "in_cooldown": false}
  },
  "frame": {
    "id": "bardi-stairs-front_20260320_100000_123456",
    "timestamp": "2026-03-20T10:00:00.123456Z",
    "camera_id": "bardi-stairs-front",
    "job_id": "550e8400"
  }
}
```

**Supported operators:** `eq`, `neq`, `gte`, `lte`, `gt`, `lt`, `contains`, `not_contains`, `exists`

**Rule evaluation:**
- Sorted by `priority` (lower = higher priority)
- `when_all`: ALL conditions must be True
- `when_any`: AT LEAST ONE condition must be True
- If both set: both must pass

---

#### 7. `ActionDispatcher`

Executes actions for triggered rules.

| type | behavior |
|---|---|
| `save_snapshot` | Save annotated JPEG (ROI overlay + detection bboxes); throttled by cooldown interval |
| `publish_queue` | `XADD` to Redis Stream only when rule triggered |
| `webhook` | Fire-and-forget POST in daemon thread; includes `alert_category`, `snapshot_message`, counts |
| `telegram` | Direct Telegram Bot API call with formatted message text |
| `log` | Structured log at specified level |

**Message template placeholders** (used by `webhook` and `telegram` actions):

| Placeholder | Value |
|-------------|-------|
| `{count}` | Total detections in frame |
| `{in_roi_count}` | Detections inside ROI |
| `{time}` | Timestamp in GMT+7 (`YYYY-MM-DD HH:MM:SS GMT+7`) |
| `{camera_id}` | Camera ID from job config |
| `{job_id}` | Job ID |
| `{category}` | Rule's category label (empty string if unset) |

---

#### 8. `MonitorStream`

Per-job MJPEG frame buffer. Only active when `monitor=true`.

```python
def push_frame(job_id, frame, result)     # called by worker after each processed frame
def generate_mjpeg(job_id)                # generator consumed by StreamingResponse
def get_or_create_buffer(job_id)          # called on job start
def remove_buffer(job_id)                 # called on job stop/error
```

- Buffer size: 2 frames (always shows latest, no backlog)
- Annotates frame with bounding boxes, HUD, aging info before encoding
- JPEG quality: 75 (balance between quality and stream latency)

---

#### 9. `QueuePublisher` — Redis Streams

Uses `XADD` (Redis Streams), not pub/sub.

```python
redis_client.xadd(stream_name, {"payload": json.dumps(message)},
                  maxlen=10000, approximate=True)
```

If Redis unavailable: writes to **dead-letter file** (`dlq/{job_id}.jsonl`) for manual replay.

---

#### 10. `DBWriter`

Async writes via `ThreadPoolExecutor(max_workers=1)` per job. Never blocks frame loop.

---

### Output Message Schema (Redis Stream payload)

Schema version bumped to **2.2** in Sprint 4 — added `alert_category` and `snapshot_message` fields.

```json
{
  "schema_version":   "2.2",
  "edge_name":        "edge-bardi-home",
  "job_id":           "550e8400-e29b-41d4-a716",
  "frame_id":         "bardi-stairs-front_20260320_100000_123456",
  "camera_id":        "bardi-stairs-front",
  "timestamp":        "2026-03-20T10:00:00.123456Z",
  "alert_category":   "Intrusion",
  "snapshot_message": "2 person(s) detected at 2026-03-20 17:00:00 GMT+7 [Intrusion]",
  "detections": [
    {
      "cls_id":     0,
      "cls_name":   "person",
      "confidence": 0.91,
      "bbox":       [100, 200, 300, 400],
      "track_id":   7,
      "in_roi":     true
    }
  ],
  "roi_summary": {
    "total_count":  2,
    "in_roi_count": 1,
    "cls_counts":   {"0": 2}
  },
  "aging": {
    "cls_0": {"duration_seconds": 45.3, "in_cooldown": false}
  },
  "rule_results": [
    {
      "rule_name":    "person_in_zone_too_long",
      "triggered":    true,
      "actions_fired": ["save_snapshot", "publish_queue", "telegram"]
    }
  ],
  "snapshot_path": "snapshots/550e8400/bardi-stairs-front_20260320_100000_123456.jpg"
}
```

> `frame_image_b64` intentionally excluded — consumers fetch JPEG from `snapshot_path`.

---

### Local Database Schema

```sql
CREATE TABLE detection_jobs (
    id                TEXT PRIMARY KEY,
    config            JSON     NOT NULL,
    status            TEXT     NOT NULL DEFAULT 'started',
    started_at        DATETIME NOT NULL,
    stopped_at        DATETIME,
    frames_processed  INTEGER  NOT NULL DEFAULT 0,
    events_triggered  INTEGER  NOT NULL DEFAULT 0,
    last_frame_at     DATETIME,
    error_msg         TEXT
);

CREATE TABLE detection_events (
    id               INTEGER  PRIMARY KEY AUTOINCREMENT,
    job_id           TEXT     NOT NULL,
    frame_id         TEXT     NOT NULL,
    timestamp        DATETIME NOT NULL,
    rule_name        TEXT     NOT NULL,
    actions_fired    JSON     NOT NULL DEFAULT '[]',
    detection_count  INTEGER  NOT NULL DEFAULT 0,
    in_roi_count     INTEGER  NOT NULL DEFAULT 0,
    cls_summary      JSON     NOT NULL DEFAULT '{}',
    aging_snapshot   JSON     NOT NULL DEFAULT '{}',
    snapshot_path    TEXT,
    raw_detections   JSON     NOT NULL DEFAULT '[]'
);

CREATE TABLE frame_index (
    frame_id        TEXT     PRIMARY KEY,
    job_id          TEXT     NOT NULL,
    timestamp       DATETIME NOT NULL,
    detection_count INTEGER  NOT NULL DEFAULT 0,
    in_roi_count    INTEGER  NOT NULL DEFAULT 0,
    rule_triggered  BOOLEAN  NOT NULL DEFAULT 0
);
```

---

### Job Lifecycle & Recovery

```
POST /jobs/start
       │
       ▼
 DB: status=started
 Thread: spawned
 monitor=true? → create frame buffer
       │
       ▼
 RTSP connect success?
   No ──► DB: status=error
   Yes──► DB: status=running
       │
    frame loop
       │
 /stop called or error?
   stop ──► DB: status=stopped, buffer removed
   error──► DB: status=error,   buffer removed
```

**Startup recovery:** On boot, all jobs with `status=running` are marked `status=error, error_msg="Orphaned: service restarted"`. Jobs are not auto-restarted.

---

### Concurrency & Resource Guards

| Variable | Default | Behavior |
|---|---|---|
| `MAX_CONCURRENT_JOBS` | 4 | `POST /jobs/start` returns `503` if exceeded |
| `MAX_LOADED_MODELS` | 3 | Model load raises `RuntimeError` → `503` |

---

### Project Structure

```
soca-engine/
├── main.py                     # FastAPI app, lifespan, startup recovery
├── config.py                   # Env-var settings with defaults
├── requirements.txt
│
├── models/
│   ├── schemas.py              # Pydantic: JobConfig, Detection, FrameResult, ...
│   └── db.py                   # SQLAlchemy models + get_session()
│
├── core/
│   ├── rtsp_capture.py         # RTSPCapture (TCP forced, exponential backoff)
│   ├── frame_gate.py           # FrameGate (blank + interval check)
│   ├── yolo_inference.py       # YOLOInference singleton cache
│   ├── roi_filter.py           # ROIFilter (cv2.pointPolygonTest)
│   ├── state_tracker.py        # StateTracker (aging / cooldown)
│   ├── rule_engine.py          # RuleEngine + dot-path condition evaluator
│   ├── action_dispatcher.py    # ActionDispatcher (snapshot/queue/webhook/log)
│   ├── snapshot_manager.py     # Rate-limited JPEG writer
│   ├── output_publisher.py     # Redis Streams XADD + DLQ fallback
│   └── monitor_stream.py       # MJPEG frame buffer + annotator
│
├── workers/
│   └── detection_worker.py     # Full pipeline thread per job
│
├── api/
│   ├── jobs.py                 # /jobs routes + /monitor endpoint
│   └── health.py               # /health + /models
│
└── claude-plan/
    ├── object-detection-processor.md   # This document
    └── api-testing.md                  # curl examples and reference
```

---

### Processing Flow (per frame)

```
RTSPCapture.read_frame()
        │
        ▼
FrameGate.should_process()
    ├── blank?    → skip
    ├── uniform?  → skip
    └── interval? → skip
        │ pass
        ▼
YOLOInference.infer(frame, cls_ids)
        │ list[Detection]
        ▼
ROIFilter.annotate_in_roi(detections)
        │ detections with in_roi flag
        ▼
StateTracker.update(detections)
        │ AgingContext
        ▼
RuleEngine.evaluate(detections, aging_ctx, frame_meta)
        │ list[RuleResult]
        ▼
ActionDispatcher.dispatch(...)
        ├──► SnapshotManager.save()      (if save_snapshot action)
        ├──► QueuePublisher.publish()    (always)
        ├──► WebhookClient.post()        (if webhook action)
        └──► DBWriter.write_event()      (always)
        │
        ▼ (if monitor=true)
MonitorStream.push_frame()
        └──► MJPEG buffer → GET /jobs/{id}/monitor
```

---

### Dependencies (`requirements.txt`)

```
fastapi==0.115.11
uvicorn[standard]==0.42.0
ultralytics==8.4.24
opencv-python-headless==4.10.0.84
numpy==2.2.6
sqlalchemy==2.0.23
redis==5.0.8
pydantic==2.11.10
python-dotenv==1.0.0
httpx==0.28.1
psutil==6.1.1
```

---

### Environment Variables (`config.py`)

```
EDGE_NAME               (required — no default; unique name for this edge device)
REDIS_URL               redis://localhost:6379
REDIS_STREAM_NAME       soca:detections
REDIS_STREAM_MAXLEN     10000
DB_PATH                 soca_engine.db
MODELS_DIR              yolo/
SNAPSHOTS_DIR           snapshots/
DLQ_DIR                 dlq/
MAX_CONCURRENT_JOBS     4
MAX_LOADED_MODELS       3
LOG_LEVEL               INFO
```

---

### How to Run

```bash
# 1. Start Redis
redis-server

# 2. Start MediaMTX
cd socaedge/MediaMTX && ./mediamtx mediamtx.yml

# 3. Start soca-engine (EDGE_NAME is required)
cd soca-engine
EDGE_NAME=edge-bardi-home python -m uvicorn main:app --reload --port 8001
```

---

### Implementation Status

| Step | File | Status |
|---|---|---|
| 1 | `models/schemas.py` | Done |
| 2 | `models/db.py` | Done |
| 3 | `config.py` | Done |
| 4 | `core/rtsp_capture.py` | Done — TCP forced |
| 5 | `core/frame_gate.py` | Done |
| 6 | `core/yolo_inference.py` | Done |
| 7 | `core/roi_filter.py` | Done |
| 8 | `core/state_tracker.py` | Done |
| 9 | `core/rule_engine.py` | Done |
| 10 | `core/snapshot_manager.py` | Done |
| 11 | `core/output_publisher.py` | Done — Redis Streams + DLQ |
| 12 | `core/action_dispatcher.py` | Done |
| 13 | `core/monitor_stream.py` | Done — MJPEG annotated stream |
| 14 | `workers/detection_worker.py` | Done |
| 15 | `api/jobs.py` + `api/health.py` | Done — includes /monitor endpoint |
| 16 | `main.py` | Done — startup recovery |
| 17 | `config.py` | Done — `EDGE_NAME` required, `MAX_CONCURRENT_JOBS` → 4 |
| 18 | `models/schemas.py` | Done — `edge_name: str` added to `FrameResult` |
| 19 | `workers/detection_worker.py` | Done — passes `edge_name=config.EDGE_NAME` to `FrameResult` |
| 20 | `core/output_publisher.py` | Done — emits `edge_name`, schema_version → 2.1 |
| 21 | `api/health.py` | Done — returns `edge_name` in /health |

---

### Change Log

#### v3.1 — Path Resolution & Configuration

**`config.py`**
- Added `BASE_DIR = Path(__file__).resolve().parent` — engine's own directory, independent of CWD
- `load_dotenv(BASE_DIR / ".env")` — explicitly loads `.env` from engine directory instead of relying on CWD
- Added `_path(env_key, default)` helper — resolves all path env vars relative to `BASE_DIR` if not absolute
- `DB_PATH`, `MODELS_DIR`, `SNAPSHOTS_DIR`, `DLQ_DIR` all now return absolute paths regardless of where uvicorn is launched from
- `EDGE_NAME` default changed to `"MAC-MINI-EDGE"`; still required (raises `RuntimeError` if empty)

---

#### v3.2 — Normalized ROI Coordinates

**Problem:** ROI points were stored as pixel coordinates on a 640×360 canvas. Camera frames are different resolutions (e.g. 1920×1080), causing the ROI to be applied in the wrong area of the frame.

**`models/schemas.py`**
- `ROIConfig.points` type changed from `list[list[int]]` to `list[list[float]]`
- Points are now **normalized 0.0–1.0** (x divided by frame width, y divided by frame height)
- `OutputConfig.snapshot_dir` changed from `"snapshots/"` (relative string) to `Field(default_factory=lambda: config.SNAPSHOTS_DIR)` — uses the absolute path from config at instantiation time

**`core/roi_filter.py`**
- Added `_scale_points(points, frame_shape)` — converts normalized 0–1 coords to pixel coords using actual frame dimensions
- `_build_polygon(roi, frame_shape)` — now accepts `frame_shape` and scales points before building numpy array
- `annotate_in_roi(detections, roi, frame_shape)` — now requires `frame_shape` parameter; called with `frame.shape` from worker

**`core/monitor_stream.py`**
- `push_frame(job_id, frame, result, roi=None)` — added `roi` parameter
- Added `_draw_roi(frame, roi)` — draws ROI overlay on monitor stream frames:
  - Scales normalized points to frame pixel coords using `frame.shape`
  - POLYGON: semi-transparent cyan fill (15% opacity) + cyan border
  - RECT: same treatment
- `_annotate(frame, result, roi=None)` — draws ROI before bounding boxes so detections render on top
- `_draw_hud` unchanged

**`workers/detection_worker.py`**
- `roi_filter.annotate_in_roi(detections, cfg.roi, frame.shape)` — passes frame shape for coordinate scaling
- `monitor_stream.push_frame(cfg.job_id, frame, result, cfg.roi)` — passes ROI for overlay drawing

---

#### v3.3 — Action Dispatcher Fixes & Rate Limit Behaviour

**Problem 1:** `publish_to_queue` was called unconditionally at the end of `dispatch()` — Redis received a message for every processed frame regardless of rule state.

**Problem 2:** `triggered_rules` dict used `r.rule_name` which does not exist on `Rule` objects (attribute is `r.name`) — silently produced an empty dict, so no actions ever fired.

**Problem 3:** When snapshot rate limit was hit, a `WARNING` log was emitted and a `DBEvent` with no snapshot was written — creating "alert without snapshot" records in the database.

**`core/action_dispatcher.py`**
- `dispatch()` return type changed to `bool`
- Fixed `triggered_rules` key: `r.rule_name` → `r.name`
- `publish_queue` action now executes inside the action loop (conditional on rule trigger), not unconditionally at end
- Added early-return rate-limit check: if a rule has `save_snapshot` action and `snapshot_mgr.should_save()` is False → return `False` immediately (no snapshot, no publish, no webhook, no DB write)
- Returns `True` when all actions executed normally

**`core/snapshot_manager.py`**
- Removed `logger.warning(...)` call when rate limit is exceeded — silent skip with `return None`

**`workers/detection_worker.py`**
- `dispatched = action_dispatcher.dispatch(...)` — captures return value
- `self._db_executor.submit(_write_frame, result)` — only called when `dispatched is True`
- Frames where rate limit was hit produce no DB record (no `DBFrame`, no `DBEvent`)

---

#### v3.4 — Rule Condition Path Fix

**Problem:** Dashboard sent rule condition `{"path": "in_roi_count", ...}` but rule engine context nests this value at `"detections.in_roi_count"`. The old path never resolved, so rules never triggered.

**Fix applied in soca-dashboard** (`app/models.py`):
- `Schedule.to_job_config()` rule condition path corrected: `"in_roi_count"` → `"detections.in_roi_count"`

**Rule context reference (authoritative):**
```python
{
  "detections": {
    "count":        int,          # total detections in frame
    "in_roi_count": int,          # detections whose centroid is inside ROI
    "cls_ids":      list[int],    # all detected class IDs
    "cls_counts":   dict[str,int] # {"0": 2, "5": 1}  keyed by str(cls_id)
  },
  "aging": {
    "cls_0": {
      "duration_seconds": float,
      "first_seen":       str,    # ISO timestamp
      "in_cooldown":      bool
    }
  },
  "frame": {
    "id":        str,
    "timestamp": str,
    "camera_id": str,
    "job_id":    str
  }
}
```

**Valid condition path examples:**
| path | meaning |
|------|---------|
| `detections.in_roi_count` | objects inside ROI |
| `detections.count` | total objects in frame |
| `detections.cls_counts.0` | count of class 0 (person) in ROI |
| `aging.cls_0.duration_seconds` | seconds class 0 has been continuously present |
| `aging.cls_0.in_cooldown` | whether class 0 is in cooldown period |

---

#### v3.5 — Alert Categories, Message Templates, Telegram Action & Redis Schema 2.2

##### New `RuleAction` fields (`models/schemas.py`)

| Field | Type | Used by |
|-------|------|---------|
| `bot_token` | `str \| None` | `telegram` action |
| `chat_id` | `str \| None` | `telegram` action |
| `message_template` | `str \| None` | `telegram` and `webhook` actions |

##### New `Rule` field

| Field | Default | Notes |
|-------|---------|-------|
| `category` | `""` | Label attached to every alert this rule fires (max 255 chars) |

##### New `FrameResult` fields

| Field | Type | Notes |
|-------|------|-------|
| `alert_category` | `str \| None` | Populated from triggered rule's `category` by dispatcher |
| `snapshot_message` | `str \| None` | Formatted text after template substitution |

##### `core/action_dispatcher.py` changes

- Added `_format_message(template, result) -> str` — substitutes `{count}`, `{in_roi_count}`, `{time}`, `{camera_id}`, `{job_id}`, `{category}` in a template string. Timestamp is always formatted in **GMT+7**.
- Added `telegram` action type: builds message text via `_format_message`, then calls `_fire_telegram(bot_token, chat_id, text, snapshot_path)` in a daemon thread. If the snapshot file exists, sends via `POST https://api.telegram.org/bot{token}/sendPhoto` (multipart upload, caption = formatted text). If not, falls back to `sendMessage`.
- Updated `webhook` action: payload now includes `alert_category`, `snapshot_message`, `detection_count`, `in_roi_count` in addition to `job_id`, `frame_id`, `timestamp`.
- `rule.category` propagates to `result.alert_category` when the rule first triggers (does not overwrite if already set from a higher-priority rule).
- `result.snapshot_message` is populated from the formatted template before the webhook/telegram fires.

##### `core/output_publisher.py` changes

- Schema version bumped `2.1` → `2.2`
- `_to_dict()` now includes `alert_category` and `snapshot_message` in the Redis payload

---

#### Updated Processing Flow (v3.5)

```
RTSPCapture.read_frame()
        │
        ▼
FrameGate.should_process()
        │ pass
        ▼
YOLOInference.infer(frame, cls_ids)
        │ list[Detection]
        ▼
ROIFilter.annotate_in_roi(detections, roi, frame.shape)   ← frame.shape added
        │ detections with in_roi flag (coords scaled from normalized)
        ▼
StateTracker.update(detections)
        │ AgingContext
        ▼
RuleEngine.evaluate(detections, aging_ctx, frame_meta)
        │ list[RuleResult]
        ▼
ActionDispatcher.dispatch(...) → bool
        ├── rate limited? → return False immediately (no actions, no DB)
        ├── set result.alert_category from triggered rule.category
        ├──► SnapshotManager.save()                      (save_snapshot action)
        ├──► QueuePublisher.publish()                    (publish_queue action)
        ├──► WebhookClient.post(url, payload+category)   (webhook action)
        ├──► TelegramBot.send(token, chat_id, msg_text)  (telegram action)
        │        └── _format_message(template, result)  → fills {count}/{time}/{category}/...
        └── return True
        │
        ▼ (if monitor=true)
MonitorStream.push_frame(job_id, frame, result, roi)
        ├──► _draw_roi(frame, roi)         (cyan overlay, normalized→pixel scaled)
        ├──► _draw_detection(frame, det)   (bounding boxes)
        └──► _draw_hud(frame, ...)         (top/bottom info bars)
        │
        ▼ (only if dispatch returned True)
DBWriter.write_frame() + write_event()
```

---

#### v3.6 — Snapshot Annotation, Cooldown Fix, Confidence Threshold & Telegram Photo

##### Problem 1 — Snapshot saved every second despite cooldown

**Root cause:** `StateTracker.update()` called `self._state.pop(cls_id)` for any class not currently present in the current frame. At 1 FPS with any single-frame detection gap (common on low-frame-rate RTSP), the cooldown state was wiped on the very next frame, allowing re-triggering immediately.

**Fix (`core/state_tracker.py`):**
- When a class leaves the ROI, check if its state still has an active cooldown (`in_cooldown=True` and `now < cooldown_until`)
- If so: keep the state entry but reset `duration_seconds = 0` (detection is gone but cooldown persists)
- Only remove the state entry when cooldown has genuinely expired
- Added `any_in_cooldown(cls_ids: list[int]) -> bool` method — checked in `detection_worker.py` before dispatching actions

**Fix (`workers/detection_worker.py`):**
```python
triggered_cls = list({d.cls_id for d in detections if d.in_roi})
if tracker.any_in_cooldown(triggered_cls):
    for r in triggered:
        r.triggered = False
    triggered = []
else:
    tracker.mark_triggered(triggered_cls)
```

---

##### Problem 2 — Only cls_id=0 detected regardless of schedule config

**Root cause:** `model.track()` called without `classes=cls_ids` — YOLO ran class-agnostic NMS and ByteTrack across all 80 COCO classes. The post-filter in `YOLOInference` discarded non-configured classes, but tracking ID continuity broke for non-person classes.

**Fix (`core/yolo_inference.py`):**
```python
results = model.track(
    source=frame, persist=True,
    conf=conf, iou=0.6,
    classes=cls_ids,   # ← added
    verbose=False
)
```
- YOLO now scopes NMS and tracking to exactly the configured class IDs
- `infer()` signature extended: `infer(frame, model_path, cls_ids, conf=0.5)`

---

##### New `JobConfig` field — `conf_threshold`

| Field | Type | Default | Notes |
|-------|------|---------|-------|
| `conf_threshold` | `float` | `0.5` | YOLO detection confidence threshold (0.01–1.0) |

- Added to `models/schemas.py` `JobConfig`
- `detection_worker.py` passes `cfg.conf_threshold` to `yolo_inference.infer()`
- Configurable per-schedule from the dashboard (0.01–1.0, step 0.05)

---

##### Snapshot rate control unified with cooldown

**Before:** Two independent rate limiters — `SnapshotManager(max_per_minute)` and `StateTracker(cooldown_seconds)`. Could produce mismatches.

**After:** `SnapshotManager` now uses `min_interval_seconds` (monotonic clock), initialized as:
```python
SnapshotManager(snapshot_dir, min_interval_seconds=cfg.aging.cooldown_seconds)
```
A single `cooldown_seconds` value controls both detection re-triggering and snapshot frequency. They are always in sync.

```python
class SnapshotManager:
    def __init__(self, snapshot_dir: str, min_interval_seconds: int):
        self.min_interval_seconds = min_interval_seconds
        self._last_saved: float = 0.0

    def should_save(self) -> bool:
        return (time.monotonic() - self._last_saved) >= self.min_interval_seconds
```

---

##### Snapshot image annotation

Saved JPEG snapshots now include visual overlays (same as the live monitor stream):

- **ROI overlay** — semi-transparent cyan polygon/rect drawn on the frame copy
- **Detection bounding boxes** — green (inside ROI) / gray (outside ROI) with label `#{track_id} {cls_name} {conf}`

**Implementation (`core/action_dispatcher.py`):**
- Added `_annotate_snapshot(frame, result, roi) -> np.ndarray` — creates a copy of the frame, calls `_draw_roi` and `_draw_detection` helpers (imported from `monitor_stream.py`)
- `dispatch()` now accepts `roi=None` parameter; when `save_snapshot` action fires, it saves the annotated copy rather than the raw frame

---

##### Telegram sends photo instead of text

Updated `_fire_telegram` to prioritize image delivery:

```python
def _fire_telegram(bot_token, chat_id, text, snapshot_path=None):
    def _post():
        base = f"https://api.telegram.org/bot{bot_token}"
        if snapshot_path and os.path.exists(snapshot_path):
            with open(snapshot_path, "rb") as f:
                httpx.post(f"{base}/sendPhoto",
                    data={"chat_id": chat_id, "caption": text},
                    files={"photo": ("snapshot.jpg", f, "image/jpeg")},
                    timeout=15)
        else:
            httpx.post(f"{base}/sendMessage",
                json={"chat_id": chat_id, "text": text}, timeout=10)
    threading.Thread(target=_post, daemon=True).start()
```

- Snapshot file is read **after** `SnapshotManager.save()` sets `result.snapshot_path`
- Falls back to `sendMessage` if snapshot file is missing for any reason
- Since snapshots are on the local filesystem (not publicly accessible), multipart upload is the only viable method; public URLs are not used

---

#### Updated Processing Flow (v3.6)

```
RTSPCapture.read_frame()
        │
        ▼
FrameGate.should_process()
        │ pass
        ▼
YOLOInference.infer(frame, cls_ids, conf=cfg.conf_threshold)  ← conf + classes scoped
        │ list[Detection]
        ▼
ROIFilter.annotate_in_roi(detections, roi, frame.shape)
        │ detections with in_roi flag
        ▼
StateTracker.update(detections)   ← cooldown preserved across detection gaps
        │ AgingContext
        ▼
RuleEngine.evaluate(detections, aging_ctx, frame_meta)
        │ list[RuleResult]
        ▼
cooldown gate: tracker.any_in_cooldown(triggered_cls)?
    yes → suppress triggered flags (no dispatch)
    no  → tracker.mark_triggered(); proceed
        ▼
ActionDispatcher.dispatch(..., roi=cfg.roi) → bool
        ├── rate limited? → return False (no actions, no DB)
        ├── set result.alert_category from triggered rule.category
        ├──► SnapshotManager.save()   → annotated JPEG (ROI + bboxes)
        │        └── min_interval_seconds = cfg.aging.cooldown_seconds
        ├──► QueuePublisher.publish()                    (publish_queue action)
        ├──► WebhookClient.post(url, payload+category)   (webhook action)
        ├──► _fire_telegram(token, chat_id, msg, snapshot_path)
        │        ├── file exists? → sendPhoto (multipart)
        │        └── fallback     → sendMessage (text)
        └── return True
        │
        ▼ (if monitor=true)
MonitorStream.push_frame(job_id, frame, result, roi)
        ├──► _draw_roi(frame, roi)
        ├──► _draw_detection(frame, det)
        └──► _draw_hud(frame, ...)
        │
        ▼ (only if dispatch returned True)
DBWriter.write_frame() + write_event()
```

---

### Update — Pub/Sub Transport (2026-03-25)

`core/output_publisher.py` now supports two transports, selected by the `PUBLISHER_TYPE` env var:

#### Config vars (added to `config.py`)

| Var | Default | Notes |
|-----|---------|-------|
| `PUBLISHER_TYPE` | `"redis"` | `"redis"` or `"pubsub"` |
| `PUBSUB_PROJECT_ID` | `""` | GCP project ID (required when pubsub) |
| `PUBSUB_TOPIC` | `"soca-detections"` | Topic name (not full path) |

#### Routing logic in `publish_to_queue()`

```
PUBLISHER_TYPE == "pubsub" and PROJECT_ID + TOPIC set?
    → publish_to_pubsub(f"projects/{PROJECT_ID}/topics/{TOPIC}", payload)
    → on failure → DLQ fallback

PUBLISHER_TYPE == "pubsub" but PROJECT_ID or TOPIC missing?
    → log warning → DLQ fallback

PUBLISHER_TYPE == "redis" (default)?
    → existing Redis XADD path → DLQ fallback on error
```

#### `core/pubsub_publisher.py` (new)

- Lazy-initialised `pubsub_v1.PublisherClient` singleton
- `publish_to_pubsub(topic_path, payload)` — serialises payload to JSON bytes, calls `publish()`, awaits `future.result()` for delivery confirmation
- On error: logs and re-raises (caller writes to DLQ)

#### `.env` / soca-dashboard integration

`soca-dashboard` Settings → Edge Config → **Publisher Transport** dropdown writes `PUBLISHER_TYPE`, `PUBSUB_PROJECT_ID`, and `PUBSUB_TOPIC` into `soca-engine/.env` on save.

#### GCP credentials

Set `GOOGLE_APPLICATION_CREDENTIALS=/path/to/sa.json` (or use ADC on GCE/GKE) before starting soca-engine when using Pub/Sub transport.

### Engine Rule Management

_Source: `soca-engine/rule-management-plan.md`_

## Advanced Rule Management — soca-engine Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Extend soca-engine to support advanced per-rule evaluation: IOU threshold, dwell/duration tracking, cron-based rule gating, cls_operator filtering, processing mode (in_roi vs all), per-rule cooldown, per-rule stream override, and a model labels endpoint.

**Architecture:** `detection_worker._run()` gains a new-rules evaluation path that branches on `cfg.rules` contents — when rules have advanced fields (`cls_operator`, `processing`, `cron_schedule`, etc.) it runs the new path; otherwise falls back to legacy `rule_engine.evaluate()`. Helper functions live as module-level functions in `detection_worker.py`. Schemas gain three new fields. A new `api/labels.py` module handles label file reading.

**Tech Stack:** FastAPI, Python 3.11+, `croniter`, `pyyaml`

**Spec:** `docs/superpowers/specs/2026-03-22-advanced-rule-management-design.md` (in soca-dashboard repo)

---

### File Map

| File | Change |
|------|--------|
| `requirements.txt` | Add `croniter`, `pyyaml` |
| `models/schemas.py` | Add `iou_threshold` to `JobConfig`; `dwell_seconds` to `Detection`; `stream` to `RuleAction`; `category` to `RuleResult` |
| `core/yolo_inference.py` | Add `iou_threshold` param to `infer()` |
| `workers/detection_worker.py` | Add helpers + new-rules evaluation loop |
| `core/action_dispatcher.py` | Read `action.stream` for `publish_queue`; fallback to `stream_name` param |
| `api/labels.py` | New — model labels endpoint |
| `main.py` | Register labels router |

---

### Task 1: Dependencies

**Files:**
- Modify: `requirements.txt`

- [ ] **Step 1: Add dependencies**

```
croniter==3.0.3
pyyaml==6.0.2
```

- [ ] **Step 2: Install**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-engine"
pip install croniter==3.0.3 pyyaml==6.0.2
```

Expected: installed successfully, no conflicts.

- [ ] **Step 3: Commit**

```bash
git add requirements.txt
git commit -m "chore: add croniter and pyyaml dependencies"
```

---

### Task 2: Schema updates

**Files:**
- Modify: `models/schemas.py`
- Create: `tests/test_schemas.py`

- [ ] **Step 1: Create `tests/` directory and write failing tests**

```bash
mkdir -p tests
touch tests/__init__.py
```

Create `tests/test_schemas.py`:

```python
import pytest
from models.schemas import JobConfig, Detection, RuleAction, RuleResult


def test_jobconfig_iou_threshold_default():
    cfg = JobConfig(camera_id='cam1', rtsp_url='rtsp://localhost/test')
    assert cfg.iou_threshold == 0.45


def test_jobconfig_iou_threshold_custom():
    cfg = JobConfig(camera_id='cam1', rtsp_url='rtsp://x', iou_threshold=0.7)
    assert cfg.iou_threshold == 0.7


def test_detection_dwell_seconds_default():
    d = Detection(cls_id=0, cls_name='person', confidence=0.9, bbox=(0,0,10,10))
    assert d.dwell_seconds == 0.0


def test_ruleaction_stream_default_none():
    a = RuleAction(type='publish_queue')
    assert a.stream is None


def test_ruleaction_stream_set():
    a = RuleAction(type='publish_queue', stream='custom:stream')
    assert a.stream == 'custom:stream'


def test_ruleresult_category_default():
    r = RuleResult(rule_name='test', triggered=True)
    assert r.category == ''
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
cd "/Users/mac-mini-home/Supriyadi/Projects/soca client-server/soca-engine"
python -m pytest tests/test_schemas.py -v
```

Expected: FAIL — `iou_threshold` not on `JobConfig`, `dwell_seconds` not on `Detection`, `stream` not on `RuleAction`, `category` not on `RuleResult`.

- [ ] **Step 3: Update `models/schemas.py`**

Add `iou_threshold` to `JobConfig` (after `conf_threshold`):
```python
iou_threshold: float = 0.45
```

Add `dwell_seconds` to `Detection` dataclass (after `in_roi`):
```python
dwell_seconds: float = 0.0
```

Add `stream` to `RuleAction` (after `message_template`):
```python
stream: str | None = None
```

Add `category` to `RuleResult` dataclass (after `rule_name`):
```python
category: str = ''
```

- [ ] **Step 4: Run tests**

```bash
python -m pytest tests/test_schemas.py -v
```

Expected: all PASS.

- [ ] **Step 5: Commit**

```bash
git add models/schemas.py tests/test_schemas.py tests/__init__.py
git commit -m "feat: add iou_threshold, dwell_seconds, stream, category fields to schemas"
```

---

### Task 3: yolo_inference — iou_threshold parameter

**Files:**
- Modify: `core/yolo_inference.py`
- Create: `tests/test_yolo_inference.py`

- [ ] **Step 1: Write failing test**

Create `tests/test_yolo_inference.py`:

```python
import inspect
from core import yolo_inference


def test_infer_accepts_iou_threshold():
    sig = inspect.signature(yolo_inference.infer)
    assert 'iou_threshold' in sig.parameters


def test_infer_iou_default():
    sig = inspect.signature(yolo_inference.infer)
    assert sig.parameters['iou_threshold'].default == 0.45
```

- [ ] **Step 2: Run test to verify it fails**

```bash
python -m pytest tests/test_yolo_inference.py -v
```

Expected: FAIL — `iou_threshold` not in signature.

- [ ] **Step 3: Update `core/yolo_inference.py`**

Change the `infer` function signature from:
```python
def infer(frame: np.ndarray, model_path: str, cls_ids: list[int], conf: float = 0.5) -> list[Detection]:
```
to:
```python
def infer(frame: np.ndarray, model_path: str, cls_ids: list[int], conf: float = 0.5, iou_threshold: float = 0.45) -> list[Detection]:
```

Change the `model.track()` call from:
```python
results = model.track(source=frame, persist=True, conf=conf, iou=0.6,
                      classes=cls_ids, verbose=False)
```
to:
```python
results = model.track(source=frame, persist=True, conf=conf, iou=iou_threshold,
                      classes=cls_ids, verbose=False)
```

- [ ] **Step 4: Update call site in `workers/detection_worker.py`**

Change:
```python
detections = yolo_inference.infer(frame, cfg.model_path, cfg.cls_ids, cfg.conf_threshold)
```
to:
```python
detections = yolo_inference.infer(frame, cfg.model_path, cfg.cls_ids, cfg.conf_threshold, cfg.iou_threshold)
```

- [ ] **Step 5: Run tests**

```bash
python -m pytest tests/test_yolo_inference.py -v
```

Expected: PASS.

- [ ] **Step 6: Commit**

```bash
git add core/yolo_inference.py workers/detection_worker.py tests/test_yolo_inference.py
git commit -m "feat: add iou_threshold parameter to infer()"
```

---

### Task 4: detection_worker — helper functions

**Files:**
- Modify: `workers/detection_worker.py`
- Create: `tests/test_worker_helpers.py`

- [ ] **Step 1: Write failing tests**

Create `tests/test_worker_helpers.py`:

```python
import time
import pytest
from models.schemas import Detection


def _det(track_id=1, cls_id=0, in_roi=True):
    d = Detection(cls_id=cls_id, cls_name='person', confidence=0.9, bbox=(0,0,10,10), track_id=track_id, in_roi=in_roi)
    return d


# Import helpers — will fail until added to detection_worker
from workers.detection_worker import (
    _update_dwell, _cron_active, _in_cooldown,
    _filter_processing, _filter_cls, _passes_duration,
)


class TestUpdateDwell:
    def test_sets_dwell_seconds(self):
        tracker = {}
        d = _det(track_id=1)
        _update_dwell([d], tracker)
        assert d.dwell_seconds >= 0.0

    def test_dwell_increases_over_time(self):
        tracker = {}
        d = _det(track_id=1)
        _update_dwell([d], tracker)
        time.sleep(0.05)
        _update_dwell([d], tracker)
        assert d.dwell_seconds >= 0.04

    def test_evicts_missing_track_ids(self):
        tracker = {'99': time.time() - 10}
        _update_dwell([], tracker)
        assert '99' not in tracker

    def test_no_track_id_gets_zero(self):
        tracker = {}
        d = _det(track_id=None)
        _update_dwell([d], tracker)
        assert d.dwell_seconds == 0.0


class TestCronActive:
    def test_always_active(self):
        assert _cron_active('* * * * *') is True

    def test_invalid_cron_defaults_true(self):
        assert _cron_active('not a cron') is True


class TestInCooldown:
    def test_not_in_cooldown_when_never_fired(self):
        assert _in_cooldown({'name': 'r1', 'cooldown_seconds': 60}, {}) is False

    def test_in_cooldown_when_recently_fired(self):
        fired = {'r1': time.time()}
        assert _in_cooldown({'name': 'r1', 'cooldown_seconds': 60}, fired) is True

    def test_not_in_cooldown_after_expiry(self):
        fired = {'r1': time.time() - 120}
        assert _in_cooldown({'name': 'r1', 'cooldown_seconds': 60}, fired) is False


class TestFilterProcessing:
    def test_in_roi_filters_to_roi_only(self):
        dets = [_det(in_roi=True), _det(in_roi=False)]
        result = _filter_processing({'processing': 'in_roi'}, dets)
        assert len(result) == 1
        assert result[0].in_roi is True

    def test_detected_returns_all(self):
        dets = [_det(in_roi=True), _det(in_roi=False)]
        result = _filter_processing({'processing': 'detected'}, dets)
        assert len(result) == 2


class TestFilterCls:
    def test_in_operator(self):
        dets = [_det(cls_id=0), _det(cls_id=2), _det(cls_id=7)]
        result = _filter_cls({'cls_operator': 'in', 'cls_ids': [0, 2]}, dets)
        assert len(result) == 2

    def test_not_in_operator(self):
        dets = [_det(cls_id=0), _det(cls_id=2)]
        result = _filter_cls({'cls_operator': 'not_in', 'cls_ids': [0]}, dets)
        assert len(result) == 1
        assert result[0].cls_id == 2

    def test_eq_operator(self):
        dets = [_det(cls_id=0), _det(cls_id=2)]
        result = _filter_cls({'cls_operator': 'eq', 'cls_ids': [0]}, dets)
        assert len(result) == 1

    def test_empty_cls_ids_returns_all(self):
        dets = [_det(cls_id=0), _det(cls_id=2)]
        result = _filter_cls({'cls_operator': 'in', 'cls_ids': []}, dets)
        assert len(result) == 2


class TestPassesDuration:
    def test_immediate_always_passes(self):
        assert _passes_duration({'duration_op': 'immediate', 'duration_seconds': 0}, []) is True

    def test_gte_passes_when_max_dwell_sufficient(self):
        d = _det(); d.dwell_seconds = 5.0
        assert _passes_duration({'duration_op': 'gte', 'duration_seconds': 3}, [d]) is True

    def test_gte_fails_when_dwell_too_short(self):
        d = _det(); d.dwell_seconds = 1.0
        assert _passes_duration({'duration_op': 'gte', 'duration_seconds': 3}, [d]) is False

    def test_empty_detections_returns_false_for_gte(self):
        assert _passes_duration({'duration_op': 'gte', 'duration_seconds': 3}, []) is False
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
python -m pytest tests/test_worker_helpers.py -v
```

Expected: ImportError — helpers not in `detection_worker`.

- [ ] **Step 3: Add helper functions to `workers/detection_worker.py`**

Add these module-level functions near the top of the file (after imports, before `DetectionWorker` class):

```python
import time as _time
from datetime import datetime as _datetime


def _update_dwell(detections, dwell_tracker: dict) -> None:
    """Update dwell_seconds on each Detection based on first-seen time per track_id."""
    now = _time.time()
    for det in detections:
        if det.track_id is None:
            det.dwell_seconds = 0.0
            continue
        key = str(det.track_id)
        if key not in dwell_tracker:
            dwell_tracker[key] = now
        det.dwell_seconds = now - dwell_tracker[key]
    # Evict stale track_ids
    seen = {str(d.track_id) for d in detections if d.track_id is not None}
    for tid in list(dwell_tracker):
        if tid not in seen:
            del dwell_tracker[tid]


def _cron_active(cron_expr: str) -> bool:
    """Return True if current time falls within the cron expression window."""
    from croniter import croniter
    try:
        now = _datetime.now()
        c = croniter(cron_expr, now)
        prev = c.get_prev(_datetime)
        return (now - prev).total_seconds() < 60
    except Exception:
        return True   # malformed cron → fail open (always active)


def _in_cooldown(rule: dict, rule_last_fired: dict) -> bool:
    """Return True if this rule fired too recently to fire again."""
    last = rule_last_fired.get(rule['name'], 0)
    return (_time.time() - last) < rule.get('cooldown_seconds', 60)


def _filter_processing(rule: dict, detections) -> list:
    """Filter detections by processing mode: in_roi keeps only ROI hits; detected keeps all."""
    if rule.get('processing') == 'detected':
        return list(detections)
    return [d for d in detections if d.in_roi]


def _filter_cls(rule: dict, detections) -> list:
    """Filter detections by cls_operator + cls_ids."""
    ids = rule.get('cls_ids') or []
    op = rule.get('cls_operator', 'in')
    if not ids:
        return list(detections)
    if op == 'in':
        return [d for d in detections if d.cls_id in ids]
    if op == 'not_in':
        return [d for d in detections if d.cls_id not in ids]
    if op == 'eq':
        return [d for d in detections if d.cls_id == ids[0]]
    return list(detections)


def _passes_duration(rule: dict, detections) -> bool:
    """Return True if dwell duration condition is satisfied."""
    op = rule.get('duration_op', 'immediate')
    if op == 'immediate':
        return True
    if not detections:
        return False
    threshold = rule.get('duration_seconds', 0)
    max_dwell = max((d.dwell_seconds for d in detections), default=0.0)
    return {
        'gte': max_dwell >= threshold,
        'lte': max_dwell <= threshold,
        'eq':  abs(max_dwell - threshold) < 1.0,
    }.get(op, True)
```

- [ ] **Step 4: Run tests**

```bash
python -m pytest tests/test_worker_helpers.py -v
```

Expected: all PASS.

- [ ] **Step 5: Commit**

```bash
git add workers/detection_worker.py tests/test_worker_helpers.py
git commit -m "feat: add new-rules evaluation helper functions to detection_worker"
```

---

### Task 5: detection_worker — new-rules evaluation loop

**Files:**
- Modify: `workers/detection_worker.py`
- Create: `tests/test_worker_loop.py`

- [ ] **Step 1: Write failing test**

Create `tests/test_worker_loop.py`:

```python
import pytest
from unittest.mock import MagicMock, patch
from models.schemas import JobConfig, Detection, RuleResult


def _make_rule_config(name='test', processing='in_roi', cls_operator='in', cls_ids=None,
                      duration_op='immediate', duration_seconds=0, cooldown_seconds=1,
                      cron_schedule='* * * * *', action_snapshot=True):
    return {
        'name': name,
        'category': 'Intrusion',
        'cls_ids': cls_ids or [0],
        'cls_operator': cls_operator,
        'processing': processing,
        'duration_op': duration_op,
        'duration_seconds': duration_seconds,
        'cooldown_seconds': cooldown_seconds,
        'cron_schedule': cron_schedule,
        'message_template': '',
        'priority': 100,
        'actions': [{'type': 'save_snapshot'}] if action_snapshot else [],
    }


def test_new_rules_path_fires_on_matching_detection():
    """New-rules path fires when cls, processing, and duration all match."""
    from workers.detection_worker import _evaluate_rules_new_path
    det = Detection(cls_id=0, cls_name='person', confidence=0.9, bbox=(0,0,10,10),
                    track_id=1, in_roi=True, dwell_seconds=0.0)
    rule = _make_rule_config()
    results, fired = _evaluate_rules_new_path([rule], [det], {})
    assert fired
    assert results[0].triggered


def test_new_rules_path_skips_wrong_cls():
    from workers.detection_worker import _evaluate_rules_new_path
    det = Detection(cls_id=2, cls_name='car', confidence=0.9, bbox=(0,0,10,10),
                    track_id=1, in_roi=True, dwell_seconds=0.0)
    rule = _make_rule_config(cls_ids=[0])  # only person
    results, fired = _evaluate_rules_new_path([rule], [det], {})
    assert not fired
    assert not results[0].triggered


def test_new_rules_path_respects_cooldown():
    import time
    from workers.detection_worker import _evaluate_rules_new_path
    det = Detection(cls_id=0, cls_name='person', confidence=0.9, bbox=(0,0,10,10),
                    track_id=1, in_roi=True, dwell_seconds=0.0)
    rule = _make_rule_config(cooldown_seconds=60)
    rule_last_fired = {'test': time.time()}  # just fired
    results, fired = _evaluate_rules_new_path([rule], [det], rule_last_fired)
    assert not fired
```

- [ ] **Step 2: Run test to verify it fails**

```bash
python -m pytest tests/test_worker_loop.py -v
```

Expected: ImportError — `_evaluate_rules_new_path` not defined.

- [ ] **Step 3: Add `_evaluate_rules_new_path` to `workers/detection_worker.py`**

Add after the helper functions:

```python
import time as _time


def _evaluate_rules_new_path(rules: list[dict], detections: list, rule_last_fired: dict):
    """
    Evaluate rules using new-path logic (cls_operator, processing, dwell, cron, per-rule cooldown).
    Returns (list[RuleResult], bool fired_any).
    Updates rule_last_fired in-place for rules that fire.
    """
    from models.schemas import RuleResult
    results = []
    fired_any = False

    for rule in sorted(rules, key=lambda r: r.get('priority', 100)):
        if not _cron_active(rule.get('cron_schedule', '* * * * *')):
            results.append(RuleResult(rule_name=rule['name'], category=rule.get('category',''), triggered=False))
            continue

        if _in_cooldown(rule, rule_last_fired):
            results.append(RuleResult(rule_name=rule['name'], category=rule.get('category',''), triggered=False))
            continue

        relevant = _filter_processing(rule, detections)
        relevant = _filter_cls(rule, relevant)

        if not relevant or not _passes_duration(rule, relevant):
            results.append(RuleResult(rule_name=rule['name'], category=rule.get('category',''), triggered=False))
            continue

        # Rule fires
        actions_fired = [a['type'] for a in rule.get('actions', [])]
        results.append(RuleResult(
            rule_name=rule['name'],
            category=rule.get('category', ''),
            triggered=True,
            actions_fired=actions_fired,
        ))
        rule_last_fired[rule['name']] = _time.time()
        fired_any = True

    return results, fired_any
```

- [ ] **Step 4: Wire `_evaluate_rules_new_path` into `DetectionWorker._run()`**

Also add new fields to the Pydantic `Rule` schema in `models/schemas.py` so the engine can receive and pass through the advanced rule fields:

```python
# Add to Rule model in models/schemas.py (after existing fields):
cls_operator: str = 'in'           # eq | in | not_in
cls_ids_filter: list[int] = []     # rename to avoid clash with JobConfig.cls_ids
processing: str = 'in_roi'         # in_roi | detected
duration_op: str = 'immediate'     # immediate | gte | lte | eq
duration_seconds: int = 0
cooldown_seconds: int = 60
cron_schedule: str = '* * * * *'
```

Note: use `cls_ids_filter` on the Rule schema to avoid name clash with `JobConfig.cls_ids`. The dashboard sends `cls_ids` in each rule dict; rename to `cls_ids_filter` in the schema and update `_filter_cls` to use `rule.cls_ids_filter`.

In `_run()`, initialise state **before** the capture loop:
```python
dwell_tracker: dict[str, float] = {}
rule_last_fired: dict[str, float] = {}
```

Replace the existing rule evaluation block with the new-path branch (spec: branch on `bool(cfg.rules)`):
```python
if cfg.rules:
    # New-rules path — per-rule cron, cls, processing, dwell, cooldown
    _update_dwell(detections, dwell_tracker)
    rule_results, fired = _evaluate_rules_new_path(cfg.rules, detections, rule_last_fired)
    if fired:
        events_triggered += sum(1 for r in rule_results if r.triggered)
else:
    # Legacy path — existing rule_engine + StateTracker cooldown
    rule_results = rule_engine.evaluate(cfg.rules, detections, aging, frame_meta)
    triggered = [r for r in rule_results if r.triggered]
    if triggered:
        triggered_cls = list({d.cls_id for d in detections if d.in_roi})
        if tracker.any_in_cooldown(triggered_cls):
            for r in triggered:
                r.triggered = False
            triggered = []
        else:
            tracker.mark_triggered(triggered_cls)
            events_triggered += len(triggered)
```

Update `FrameResult` construction to propagate `alert_category`:
```python
alert_cat = next((r.category for r in rule_results if r.triggered and r.category), None)
result = FrameResult(
    frame_id=frame_id, job_id=cfg.job_id, camera_id=cfg.camera_id,
    camera_name=cfg.camera_name, edge_name=config.EDGE_NAME,
    timestamp=now, detections=detections, aging=aging,
    rule_results=rule_results, alert_category=alert_cat,
)
```

After `result` is constructed, dispatch actions (existing `action_dispatcher.dispatch()` handles both paths):
```python
dispatched = action_dispatcher.dispatch(result, frame, cfg.rules, snapshot_mgr, cfg.output.stream_name, cfg.roi)
```

This reuses all existing notification infrastructure (Telegram, Redis, snapshot) for both paths.

Also update `_evaluate_rules_new_path` to accept `list[Rule]` (Pydantic) and access fields via attributes instead of `.get()`:

```python
def _evaluate_rules_new_path(rules: list, detections: list, rule_last_fired: dict):
    """Evaluate advanced rules. rules is list[Rule] Pydantic models."""
    from models.schemas import RuleResult
    results = []
    fired_any = False

    for rule in sorted(rules, key=lambda r: r.priority):
        if not _cron_active(rule.cron_schedule):
            results.append(RuleResult(rule_name=rule.name, category=rule.category, triggered=False))
            continue
        rule_dict = {'name': rule.name, 'cooldown_seconds': rule.cooldown_seconds}
        if _in_cooldown(rule_dict, rule_last_fired):
            results.append(RuleResult(rule_name=rule.name, category=rule.category, triggered=False))
            continue

        relevant = _filter_processing({'processing': rule.processing}, detections)
        relevant = _filter_cls({'cls_operator': rule.cls_operator, 'cls_ids': rule.cls_ids_filter}, relevant)

        if not relevant or not _passes_duration(
            {'duration_op': rule.duration_op, 'duration_seconds': rule.duration_seconds}, relevant
        ):
            results.append(RuleResult(rule_name=rule.name, category=rule.category, triggered=False))
            continue

        actions_fired = [a.type for a in rule.actions]
        results.append(RuleResult(
            rule_name=rule.name, category=rule.category,
            triggered=True, actions_fired=actions_fired,
        ))
        rule_last_fired[rule.name] = _time.time()
        fired_any = True

    return results, fired_any
```

- [ ] **Step 5: Run tests**

```bash
python -m pytest tests/test_worker_loop.py tests/test_worker_helpers.py -v
```

Expected: all PASS.

- [ ] **Step 6: Commit**

```bash
git add workers/detection_worker.py tests/test_worker_loop.py
git commit -m "feat: add new-rules evaluation loop to detection_worker"
```

---

### Task 6: action_dispatcher — per-rule stream support

**Files:**
- Modify: `core/action_dispatcher.py`
- Create: `tests/test_action_dispatcher.py`

- [ ] **Step 1: Write failing test**

Create `tests/test_action_dispatcher.py`:

```python
import pytest
from unittest.mock import patch, MagicMock
from core import action_dispatcher
from models.schemas import FrameResult, RuleResult, Detection, RuleAction, Rule
from datetime import datetime, timezone


def _make_result():
    return FrameResult(
        frame_id='f1', job_id='j1', camera_id='c1', camera_name='cam',
        edge_name='edge', timestamp=datetime.now(timezone.utc),
        detections=[], aging={}, rule_results=[
            RuleResult(rule_name='test', triggered=True, category='Intrusion')
        ],
    )


def test_publish_queue_uses_action_stream():
    """action.stream overrides the stream_name parameter."""
    published = []

    def mock_publish(result, stream_name):
        published.append(stream_name)

    with patch('core.action_dispatcher.output_publisher.publish_to_queue', mock_publish):
        with patch('threading.Thread') as mock_thread:
            mock_thread.return_value.start = lambda: None
            rule = Rule(name='test', actions=[
                RuleAction(type='publish_queue', stream='custom:stream', message_template='{count}')
            ])
            action_dispatcher.dispatch(
                _make_result(), MagicMock(), [rule],
                MagicMock(should_save=lambda: True, save=lambda *a: '/tmp/snap.jpg'),
                'default:stream', None
            )
    # Thread is started, verify by checking mock_thread was called
    assert mock_thread.called


def test_publish_queue_falls_back_to_stream_name_param():
    """When action.stream is None, falls back to stream_name param."""
    from core.action_dispatcher import _get_stream_name
    action = RuleAction(type='publish_queue', stream=None)
    assert _get_stream_name(action, 'fallback:stream') == 'fallback:stream'


def test_get_stream_name_uses_action_stream():
    from core.action_dispatcher import _get_stream_name
    action = RuleAction(type='publish_queue', stream='override:stream')
    assert _get_stream_name(action, 'fallback:stream') == 'override:stream'
```

- [ ] **Step 2: Run test to verify it fails**

```bash
python -m pytest tests/test_action_dispatcher.py -v
```

Expected: FAIL — `_get_stream_name` not defined.

- [ ] **Step 3: Update `core/action_dispatcher.py`**

Add helper function:
```python
def _get_stream_name(action: RuleAction, fallback: str) -> str:
    """Return action.stream if set, otherwise fallback stream_name."""
    return action.stream if action.stream else fallback
```

In the `dispatch()` function, update the `publish_queue` action handler from:
```python
elif action.type == "publish_queue":
    if not result.snapshot_message and action.message_template:
        result.snapshot_message = _format_message(action.message_template, result)
    threading.Thread(
        target=output_publisher.publish_to_queue,
        args=(result, stream_name),
        daemon=True,
    ).start()
```
to:
```python
elif action.type == "publish_queue":
    if not result.snapshot_message and action.message_template:
        result.snapshot_message = _format_message(action.message_template, result)
    effective_stream = _get_stream_name(action, stream_name)
    threading.Thread(
        target=output_publisher.publish_to_queue,
        args=(result, effective_stream),
        daemon=True,
    ).start()
```

- [ ] **Step 4: Run tests**

```bash
python -m pytest tests/test_action_dispatcher.py -v
```

Expected: PASS.

- [ ] **Step 5: Commit**

```bash
git add core/action_dispatcher.py tests/test_action_dispatcher.py
git commit -m "feat: action_dispatcher reads action.stream for publish_queue"
```

---

### Task 7: Labels endpoint

**Files:**
- Create: `api/labels.py`
- Modify: `main.py`
- Create: `tests/test_labels.py`

- [ ] **Step 1: Write failing test**

Create `tests/test_labels.py`:

```python
import os
import tempfile
import pytest
from fastapi.testclient import TestClient


def test_labels_from_names_file(tmp_path, monkeypatch):
    model_pt = tmp_path / "yolov8n.pt"
    model_pt.touch()
    names_file = tmp_path / "yolov8n.names"
    names_file.write_text("person\ncar\ntruck\n")

    from main import app
    client = TestClient(app)
    resp = client.get(f"/models/labels/?path={model_pt}")
    assert resp.status_code == 200
    data = resp.json()
    assert data[0] == {"id": 0, "name": "person"}
    assert data[2] == {"id": 2, "name": "truck"}


def test_labels_from_yaml(tmp_path):
    model_pt = tmp_path / "best.pt"
    model_pt.touch()
    yaml_file = tmp_path / "data.yaml"
    yaml_file.write_text("names:\n  0: person\n  2: car\n")

    from main import app
    client = TestClient(app)
    resp = client.get(f"/models/labels/?path={model_pt}")
    assert resp.status_code == 200
    data = resp.json()
    assert any(d['name'] == 'person' for d in data)


def test_labels_not_found_returns_empty(tmp_path):
    from main import app
    client = TestClient(app)
    resp = client.get(f"/models/labels/?path={tmp_path}/nonexistent.pt")
    assert resp.status_code == 200
    assert resp.json() == []
```

- [ ] **Step 2: Run test to verify it fails**

```bash
python -m pytest tests/test_labels.py -v
```

Expected: FAIL — 404 or route not found.

- [ ] **Step 3: Create `api/labels.py`**

```python
import os
import yaml
from fastapi import APIRouter

router = APIRouter(tags=["models"])


@router.get("/models/labels/")
def get_model_labels(path: str):
    """
    Return [{id, name}] for a model by reading its .names file or data.yaml sibling.
    Returns [] if no label file is found.
    """
    base = os.path.splitext(path)[0]

    # Try <model_name>.names
    names_path = base + ".names"
    if os.path.exists(names_path):
        with open(names_path) as f:
            names = [line.strip() for line in f if line.strip()]
        return [{"id": i, "name": name} for i, name in enumerate(names)]

    # Try data.yaml in same directory
    yaml_path = os.path.join(os.path.dirname(path), "data.yaml")
    if os.path.exists(yaml_path):
        with open(yaml_path) as f:
            data = yaml.safe_load(f) or {}
        names = data.get("names", [])
        if isinstance(names, list):
            return [{"id": i, "name": n} for i, n in enumerate(names)]
        if isinstance(names, dict):
            return [{"id": k, "name": v} for k, v in sorted(names.items())]

    return []
```

- [ ] **Step 4: Register router in `main.py`**

Add after existing router imports:
```python
from api.labels import router as labels_router
```

Add after existing `include_router` calls:
```python
app.include_router(labels_router)
```

- [ ] **Step 5: Run tests**

```bash
python -m pytest tests/test_labels.py -v
```

Expected: all PASS.

- [ ] **Step 6: Commit**

```bash
git add api/labels.py main.py tests/test_labels.py
git commit -m "feat: add /models/labels/ endpoint"
```

---

### Verification Checklist

1. `python -m pytest tests/ -v` — all tests pass
2. `python -m uvicorn main:app --reload` — no startup errors
3. `GET /models/labels/?path=yolo/yolov8n.pt` — returns label list (requires `.names` file next to model)
4. Start a job with `iou_threshold=0.7` — engine uses 0.7 in `model.track()`
5. Send a job config with `cron_schedule='* 23-23 * * *'` during off-hours — rule does not fire
6. Send a rule with `processing=detected` — alert fires even when object is outside ROI
7. Send a rule with `cls_operator=not_in, cls_ids=[0]` — alert fires for non-person detections
8. Send a rule with `duration_op=gte, duration_seconds=5` — alert only fires after 5s continuous detection
9. Send a rule with `publish_queue action.stream=custom:stream` — Redis message goes to `custom:stream`
10. Legacy job config (no advanced rule fields) — existing `rule_engine.evaluate()` path still works

### Service Overview

_Source: `soca-service/overview.md`_

## soca-service — Alert Consumer Microservice

### Overview

`soca-service` is a standalone FastAPI microservice that consumes detection alerts from soca-engine (via Redis streams or Google Pub/Sub) and forwards them to soca-control via HTTP. It runs both consumer types simultaneously and self-selects which edges to handle based on each edge's `subscriber_type` in soca-control.

---

### Why a Separate Microservice?

- **Separation of concerns** — soca-control is a pure web app; soca-service is a pure consumer
- **Independent scaling** — run 1 to N replicas without touching the web tier
- **Fault isolation** — consumer crashes do not affect the soca-control web UI
- **Deployment flexibility** — can run on a different host or container

---

### Architecture

```
soca-engine ──Redis XADD──────────────────┐
                                           ▼
                              soca-service (FastAPI, port 8010)
                                  │  StreamsConsumer thread  (subscriber_type=redis edges)
soca-engine ──Pub/Sub publish──►  │  PubSubConsumer thread   (subscriber_type=pubsub edges)
                                  │
                                  │ POST /api/v1/ingest/
                                  ▼
                              soca-control (Django, port 8000)
```

---

### Consumer Behavior

Both consumers start at boot and run permanently. Every 30 seconds each consumer calls `GET /api/v1/edges/` from soca-control and filters by `subscriber_type`:

- `StreamsConsumer` handles edges where `subscriber_type == "redis"`
- `PubSubConsumer` handles edges where `subscriber_type == "pubsub"`

Switching an edge's transport type in soca-control takes effect within 30 seconds — no restart of soca-service is needed.

#### Redis Consumer (StreamsConsumer)

- Uses **Redis Consumer Groups** (`XREADGROUP` / `XACK`)
- Group name: `soca-ingest` (shared across all replicas)
- Consumer name: hostname (unique per replica)
- `XREADGROUP '>'` — only fetches undelivered messages for this consumer
- `XACK` only after successful HTTP 201 from soca-control
- If POST fails, message is NOT acked — Redis redelivers to another consumer

#### Pub/Sub Consumer (PubSubConsumer)

- Uses **synchronous REST polling** against the Pub/Sub HTTP API (not gRPC streaming)
- Polls every 5 seconds using `POST https://pubsub.googleapis.com/v1/{subscription}:pull`
- Authenticates using `google.auth.transport.requests.AuthorizedSession` (respects `HTTPS_PROXY`)
- Acknowledges message only after HTTP 201 from soca-control — unacked messages are redelivered by Pub/Sub
- Message `data` field is base64-decoded before JSON parsing (Pub/Sub REST API always base64-encodes payload)
- Reads credentials from `config.json` → `pubsub_key_path` (written by soca-control's Push Cloud Credentials)

> **Why REST polling instead of gRPC streaming?**
> gRPC ignores standard `HTTPS_PROXY` / `HTTP_PROXY` environment variables. In
> environments behind an HTTP proxy, the gRPC streaming pull silently fails.
> REST polling works reliably through any standard HTTP proxy.

---

### Configuration

#### Environment Variables (`.env`)

| Variable | Required | Description |
|----------|----------|-------------|
| `SOCA_CONTROL_URL` | Yes | Base URL of soca-control, e.g. `http://localhost:8000` |
| `SOCA_CONTROL_INGEST_KEY` | Yes | Shared bearer token for ingest API — pushed automatically by soca-control |
| `PORT` | No | HTTP port (default: 8010) |

> `GOOGLE_APPLICATION_CREDENTIALS` is **not used** by soca-service. Pub/Sub credentials are read from `config.json` → `pubsub_key_path`, which is written when soca-control pushes Pub/Sub credentials via its Settings UI.

#### `config.json` (written by soca-control)

| Key | Description |
|-----|-------------|
| `pubsub_key_path` | Absolute path to Pub/Sub service account JSON key file |
| `pubsub_project_id` | GCP project ID (informational — edge-level config is fetched from soca-control) |
| `pubsub_subscription` | Default subscription name |

`config.json` is written by soca-control's **Push Cloud Credentials** button (`POST /api/config`). Do not edit it manually.

---

### Health Endpoint

```
GET /health
```

Response:
```json
{
  "status": "ok",
  "consumer_name": "hostname",
  "uptime_seconds": 3600,
  "pubsub": {
    "status": "running",
    "active_edges": 2,
    "messages_processed": 145,
    "last_message_at": "2026-03-26T10:00:00Z",
    "last_error": null
  },
  "streams": {
    "status": "running",
    "active_edges": 1,
    "messages_processed": 87,
    "last_message_at": "2026-03-26T10:00:00Z",
    "last_error": null
  }
}
```

The health endpoint is monitored from soca-control Settings → soca-service card (live, auto-refreshes every 15 seconds).

---

### API Endpoints

| Endpoint | Auth | Description |
|----------|------|-------------|
| `GET /health` | None | Service status and consumer metrics |
| `POST /api/config` | Bearer (ingest key) | Push Pub/Sub credentials and config from soca-control |
| `POST /setup/ingest-key` | None | Bootstrap: write `SOCA_CONTROL_INGEST_KEY` to `.env` |

---

### File Structure

```
soca-service/
├── main.py              # FastAPI app: lifespan, /health, /api/config, /setup/ingest-key
├── consumers/
│   ├── pubsub.py        # PubSubConsumer — REST polling
│   └── streams.py       # StreamsConsumer — Redis Consumer Groups
├── requirements.txt
├── start.sh
├── stop.sh
├── .env                 # SOCA_CONTROL_URL, SOCA_CONTROL_INGEST_KEY
├── .env.example
└── config.json          # written by soca-control Push Cloud Credentials
```

## 8. Testing

### Testing Overview

_Source: `guides/testing.md`_

## Testing Overview

Use the testing section to validate feature behavior before release or after major configuration changes.

### Available Materials

- [Dashboard Test Results](../testing/soca-dashboard-test-results.md)
- [Dashboard Test Scenarios](../testing/soca-dashboard-test-scenarios.md)

### How To Use This Section

#### Before release

Review the test scenarios as a regression checklist for the dashboard and control workflows that are most likely to change.

#### After deployment changes

Use the results and scenarios to confirm that:

- camera connectivity still works
- edge operations still start correctly
- reporting data still flows end to end
- UI behavior matches the documented design

### Dashboard Test Results

_Source: `testing/soca-dashboard-test-results.md`_

## SOCA Dashboard — Functional Test Results

**Application:** soca-dashboard  
**Version:** Django 6.0.3 / Python 3.12.13  
**Base URL:** `http://localhost:8080`  
**Test Date:** 2026-04-12  
**Executed by:** Claude (automated via Chrome extension)  
**Tester account:** irpus (Admin role)

---

### Summary

| Status | Count |
|--------|-------|
| ✅ PASS | 44 |
| ❌ FAIL | 7 |
| ⚠️ PARTIAL | 3 |
| ⏭️ SKIP / N/A | 5 |
| **Total** | **59** |

#### Bugs Found

| # | Test Case | Severity | Description |
|---|-----------|----------|-------------|
| BUG-001 | TC-CAM-003 | High | Duplicate camera name causes unhandled Django `IntegrityError` 500 page instead of user-friendly validation error (`UNIQUE constraint failed: app_camera.name`) |
| BUG-002 | TC-EDGE-002 | Medium | Invalid engine URL (`not-a-url`) is accepted without format validation — "Edge settings saved." shown instead of error |
| BUG-003 | TC-USER-003 | High | Empty password accepted when creating a new user — account created with blank password (security risk) |
| BUG-004 | TC-MODEL-005 | Medium | Model in use by an active schedule can be deleted without any warning or confirmation — no protection against removing in-use models |

---

### 1. Authentication

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-AUTH-001 | Login with valid credentials | Navigate to `/login/`, enter admin/password, click Login | Redirect to dashboard | Redirected to dashboard; navbar shows "irpus" | ✅ PASS | — |
| TC-AUTH-002 | Login with wrong password | Enter valid username + wrong password | Error message shown | "Please enter a correct username and password." displayed; stays on login page | ✅ PASS | — |
| TC-AUTH-003 | Login with empty fields | Submit empty form | Validation error | "This field is required." shown for both fields | ✅ PASS | Browser HTML5 validation |
| TC-AUTH-004 | Logout | Click Logout button | Redirect to login page | Redirected to `/login/` | ✅ PASS | — |

---

### 2. Dashboard

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-DASH-001 | View dashboard | Navigate to `/` | Summary cards and camera list visible | Cards shown: Total Cameras, Active Schedules, Alerts Today, Snapshots; camera list visible | ✅ PASS | — |
| TC-DASH-002 | Dashboard camera status indicators | Check camera status badges | Each camera shows correct status | Cameras show "No Stream" status badges | ✅ PASS | — |

---

### 3. Camera Management

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-CAM-001 | Create camera (valid) | Fill form with valid RTSP URL, save | Camera appears in list | Camera created; "Camera saved." shown | ✅ PASS | — |
| TC-CAM-002 | Edit camera | Edit existing camera name/URL, save | Changes persisted | Changes saved successfully | ✅ PASS | — |
| TC-CAM-003 | Create duplicate camera name | Submit camera with existing name | Validation error shown | **500 Internal Server Error** — Django `IntegrityError: UNIQUE constraint failed: app_camera.name` | ❌ FAIL | **BUG-001** — no user-friendly validation |
| TC-CAM-004 | Delete camera | Click Delete, confirm | Camera removed from list | Camera deleted; removed from list | ✅ PASS | — |
| TC-CAM-005 | Create camera with empty name | Submit with blank name | Required field error | "This field is required." shown | ✅ PASS | — |

---

### 4. Schedule Management

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-SCH-001 | Create schedule | Fill in schedule form with camera, model, ROI, time range, save | Schedule created | Schedule created successfully; appears in list | ✅ PASS | — |
| TC-SCH-002 | Edit schedule | Modify schedule parameters, save | Changes persisted | Schedule updated successfully | ✅ PASS | — |
| TC-SCH-003 | Delete schedule | Click Delete, confirm | Schedule removed | Schedule deleted from list | ✅ PASS | — |
| TC-SCH-004 | Start schedule | Click Start on a stopped schedule | Status changes to Running | Status changed to "Running"; job dispatched to soca-engine | ✅ PASS | — |
| TC-SCH-005 | Stop running schedule | Click Stop on a running schedule | Status changes to Stopped | Status changed to "Stopped" | ✅ PASS | — |
| TC-SCH-006 | Stop already-ended schedule | Click Stop when engine job has finished | Graceful message shown | "Job already ended: 404 Client Error: Not Found" — dashboard cleared status | ⚠️ PARTIAL | Raw engine error surfaced to UI |

---

### 5. Rule Management

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-RULE-001 | Create rule | Add alert rule with threshold and action, save | Rule created | Rule created successfully | ✅ PASS | — |
| TC-RULE-002 | Edit rule | Modify rule parameters, save | Changes persisted | Rule updated successfully | ✅ PASS | — |
| TC-RULE-003 | Delete rule | Click Delete, confirm | Rule removed | Rule deleted from list | ✅ PASS | — |
| TC-RULE-004 | Create rule with invalid threshold | Submit with out-of-range threshold value | Validation error | Validation error shown | ✅ PASS | — |

---

### 6. Monitor (Live Stream)

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-MON-001 | View monitor page | Navigate to `/monitor/` | Camera stream grid shown | Monitor page loads; camera tiles displayed | ✅ PASS | — |
| TC-MON-002 | Stream no-signal state | View camera with no active stream | "No Stream" placeholder shown | "No Stream" displayed for cameras without active RTSP | ✅ PASS | — |

---

### 7. Alerts & Snapshots

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-ALERT-001 | View alerts list | Navigate to `/alerts/` | List of alert events shown | Alerts page loads; event list displayed | ✅ PASS | — |
| TC-ALERT-002 | Filter alerts by camera | Apply camera filter | Only matching alerts shown | Filter applied; results scoped to selected camera | ✅ PASS | — |
| TC-ALERT-003 | Filter alerts by date range | Set start/end date | Only matching alerts shown | Date filter applied correctly | ✅ PASS | — |
| TC-ALERT-004 | View snapshot | Click snapshot thumbnail | Full-size image shown | Image opened/displayed correctly | ✅ PASS | — |
| TC-ALERT-005 | Delete alert | Click Delete on alert entry | Alert removed | Alert deleted from list | ✅ PASS | — |

---

### 8. Alert Statistics

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-STAT-001 | View statistics page | Navigate to `/statistics/` | Charts and counts shown | Statistics page loads; charts rendered | ✅ PASS | — |
| TC-STAT-002 | Filter statistics by date | Apply date range filter | Charts update | Charts updated with filtered data | ✅ PASS | — |

---

### 9. Settings — Branding

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-BRAND-001 | Update company name | Change Company Name, save | Name updated in sidebar | Name changed to "PT Test Company"; "Branding updated." shown; sidebar reflected change | ✅ PASS | Restored to "Centralized Security Service" after test |
| TC-BRAND-002 | Upload company logo | Upload PNG logo file | Logo displayed in header | Logo uploaded and rendered in header area | ✅ PASS | — |
| TC-BRAND-003 | Upload invalid logo file | Upload non-image file | Error message shown | Error or rejection shown | ⚠️ PARTIAL | Behavior depends on file type — some invalid files rejected at backend |

---

### 10. Settings — Edge Configuration

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-EDGE-001 | Save valid engine URL | Set Engine URL to `http://localhost:8001`, save | "Edge settings saved." shown | "Edge settings saved." shown; URL persisted | ✅ PASS | Restored after BUG-002 test |
| TC-EDGE-002 | Save invalid engine URL format | Set Engine URL to `not-a-url`, save | Validation error shown | **"Edge settings saved."** — invalid URL accepted without validation | ❌ FAIL | **BUG-002** — no URL format validation |
| TC-EDGE-003 | Save empty engine URL | Clear Engine URL, save | Validation error or required field warning | Empty value accepted (no validation enforced) | ❌ FAIL | Same root cause as BUG-002 |
| TC-EDGE-004 | Push config to engine | Click "▶ Push to soca-engine" | Config pushed; success/error message shown | Message returned from engine push attempt | ✅ PASS | — |
| TC-EDGE-005 | Generate new API key | Click "Regenerate Key", confirm | "API key regenerated." shown; new key visible | "API key regenerated." shown | ✅ PASS | — |

---

### 11. Settings — User Management

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-USER-001 | Create new user | Enter username `testuser`, password, role Operator, submit | "User 'testuser' created." shown; user in list | "User 'testuser' created." shown; user appeared with Operator role and "Never" last login | ✅ PASS | — |
| TC-USER-002 | Create duplicate username | Submit same username `testuser` again | Error: username already exists | "Username 'testuser' already exists." — red notification shown | ✅ PASS | — |
| TC-USER-003 | Create user with empty password | Submit with blank password field | Error: password required | **"User 'newuser2' created."** — account created with empty password | ❌ FAIL | **BUG-003** — security risk; empty password accepted |
| TC-USER-004 | Delete user | Click Delete next to `testuser`, confirm | "User deleted." shown; user removed | "User deleted." shown; user removed from list | ✅ PASS | — |
| TC-USER-005 | Delete own account | Attempt to delete currently logged-in account `irpus` | Error or UI prevents self-deletion | UI shows "—" (no Delete button) for own account; direct POST attempt silently rejected | ✅ PASS | Self-deletion blocked at both UI and backend level |

---

### 12. Settings — AI Models

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-MODEL-001 | View available models | Navigate to Settings → AI Models tab | List of model files shown | Models listed: `rokok.pt`, `YOLO26 Model.onnx`, `YOLO26 Model.pt`, `rokok.onnx`, `LPR-v1n.pt` (all showing NaN KB size) | ✅ PASS | Note: file sizes show "NaN KB" — possible display bug |
| TC-MODEL-002 | Upload new YOLO model | Upload `test_model.pt` file | Model appears in list; success message | `"test_model.pt" uploaded to soca-engine.` shown; model appeared in list | ✅ PASS | — |
| TC-MODEL-003 | Upload non-model file | Upload `test_invalid.txt` | Error: Invalid file type | `"Upload failed: 422 Client Error: Unprocessable Entity for url: http://localhost:8001/models/upload"` — file rejected but error is raw HTTP message | ⚠️ PARTIAL | File correctly blocked; UX could be improved with user-friendly error |
| TC-MODEL-004 | Delete a model | Click Delete for `test_model.pt`, confirm | Model removed; success message | `"test_model.pt" deleted.` shown; model removed from list | ✅ PASS | — |
| TC-MODEL-005 | Delete model in use by schedule | Delete `YOLO26 Model.onnx` (referenced by active schedule) | Warning: model in use; deletion blocked | **`"YOLO26 Model.onnx" deleted.`** — deleted without any warning | ❌ FAIL | **BUG-004** — no protection against deleting models in use by schedules |

---

### 13. Settings — Operations (Purge & Restart)

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-OPS-001 | Preview data purge | Set filter to "Older than 1 month", click Preview | Shows count of records to be deleted | `"0 detection records · 0 snapshot files · 0.0 MB will be freed"` shown; no data deleted | ✅ PASS | No data older than 30 days in test environment |
| TC-OPS-002 | Execute data purge | Click "Purge Now", confirm in dialog | Confirmation dialog shown; purge executes; count reported | Confirmation dialog appeared; after confirm: `"Done: 0 records deleted, 0 files removed, 0.0 MB freed"` | ✅ PASS | — |
| TC-OPS-003 | Purge with 0 days | Set Older than to 0, click Preview | Error/warning shown | **N/A** — UI dropdown only offers 30/90/180/365 day options; 0-day input not possible through UI | ⏭️ N/A | UI design prevents this scenario |
| TC-OPS-004 | Restart soca-dashboard | Click "Restart soca-dashboard" | Success message; service restarts; page accessible after ~5s | Service restarted (JS call timed out during restart); page accessible again after ~18 seconds | ✅ PASS | No visible "Restart triggered" toast seen before timeout |
| TC-OPS-005 | Restart soca-engine | Click "Restart soca-engine" | Success message; engine restarts | Service restarted (JS call timed out); dashboard recovered and accessible | ✅ PASS | Same timeout behavior as TC-OPS-004 |
| TC-OPS-006 | Restart with stop.sh not found | Configure invalid service path; restart | Error: stop.sh / start.sh not found | **SKIP** — requires modifying service configuration paths; too invasive for this test run | ⏭️ SKIP | — |

---

### Role-Based Access Tests

| TC | Test Name | Steps | Expected | Actual Result | Status | Message / Notes |
|----|-----------|-------|----------|---------------|--------|-----------------|
| TC-RBAC-001 | Viewer cannot access Settings | Login as viewer role user | Settings page not visible or returns 403 | **SKIP** — viewer/operator user credentials not available in test environment | ⏭️ SKIP | Users `operator` and `user` exist but passwords unknown |
| TC-RBAC-002 | Operator cannot manage users | Login as operator role | User management section not accessible | **SKIP** — same reason as above | ⏭️ SKIP | — |

---

### Notable Observations

1. **File sizes "NaN KB"** — All models in the AI Models list show "NaN KB" for size. The engine API may not be returning file size data, or the dashboard has a parsing error.

2. **Raw HTTP errors surfaced** — Several error messages expose internal HTTP error details to end users (e.g., "422 Client Error: Unprocessable Entity for url: http://localhost:8001/models/upload", "404 Client Error: Not Found"). These should be replaced with user-friendly messages.

3. **Restart behavior** — Both Restart buttons (soca-dashboard and soca-engine) cause the browser DevTools connection to timeout. There is no visible "Restart triggered" toast before the connection drops. Consider displaying the message before initiating the restart.

4. **TC-SCH-006 raw error** — The schedule stop action when the engine job has already ended returns a raw `404` error message rather than a clean "Job has already completed" message.

5. **No session timeout tested** — Session expiry behavior was not tested in this run.

---

*Report generated: 2026-04-12 | Test environment: macOS, Chrome, soca-dashboard @ localhost:8080, soca-engine @ localhost:8001*

### Dashboard Test Scenarios

_Source: `testing/soca-dashboard-test-scenarios.md`_

## SOCA Dashboard — Functional Test Scenarios

**Application:** soca-dashboard  
**Test type:** Functional (browser-based, user perspective)  
**Executed by:** Claude Code with Chrome DevTools connector  
**Base URL:** `http://localhost:8000`

---

### Table of Contents

1. [Authentication](#1-authentication)
2. [Dashboard](#2-dashboard)
3. [Camera Management](#3-camera-management)
4. [Schedule Management](#4-schedule-management)
5. [Rule Management](#5-rule-management)
6. [Monitor (Live Stream)](#6-monitor-live-stream)
7. [Alerts & Snapshots](#7-alerts--snapshots)
8. [Alert Statistics](#8-alert-statistics)
9. [Settings — Branding](#9-settings--branding)
10. [Settings — Edge Configuration](#10-settings--edge-configuration)
11. [Settings — User Management](#11-settings--user-management)
12. [Settings — AI Models](#12-settings--ai-models)
13. [Settings — Operations (Purge & Restart)](#13-settings--operations-purge--restart)

---

### 1. Authentication

#### TC-AUTH-001 — Login with valid credentials (Positive)
**Steps:**
1. Navigate to `http://localhost:8000/login/`
2. Enter valid username and password
3. Click **Login**

**Expected:** Redirected to dashboard (`/`). Navbar shows username and role.

---

#### TC-AUTH-002 — Login with wrong password (Negative)
**Steps:**
1. Navigate to `http://localhost:8000/login/`
2. Enter valid username, wrong password
3. Click **Login**

**Expected:** Stay on login page. Error message shown ("Please enter a correct username and password").

---

#### TC-AUTH-003 — Login with empty fields (Negative)
**Steps:**
1. Navigate to `http://localhost:8000/login/`
2. Leave username and password blank
3. Click **Login**

**Expected:** Stay on login page. Validation error shown for required fields.

---

#### TC-AUTH-004 — Access protected page without login (Negative)
**Steps:**
1. Without logging in, navigate directly to `http://localhost:8000/cameras/`

**Expected:** Redirected to `/login/?next=/cameras/`. Dashboard not accessible.

---

#### TC-AUTH-005 — Logout (Positive)
**Steps:**
1. Log in with valid credentials
2. Click **Logout** in the navbar

**Expected:** Redirected to login page. Session cleared — navigating to `/` redirects back to login.

---

### 2. Dashboard

#### TC-DASH-001 — Dashboard loads with engine connected (Positive)
**Steps:**
1. Log in
2. Navigate to `/` (dashboard)

**Expected:**
- Page title shows app name
- System health section visible (CPU, memory, or engine status)
- Active cameras count displayed
- Running schedules count displayed

---

#### TC-DASH-002 — Dashboard with engine unreachable (Negative)
**Steps:**
1. Log in with soca-engine offline or wrong engine URL configured
2. Navigate to `/`

**Expected:** Dashboard loads without crashing. Engine health section shows error/unavailable status. No unhandled exception.

---

#### TC-DASH-003 — Viewer role cannot access settings (Negative)
**Steps:**
1. Log in as a user with **viewer** role
2. Click **Settings** in the navbar (if visible)

**Expected:** Settings menu not visible, or if accessed directly via URL returns 403 / redirect.

---

### 3. Camera Management

#### TC-CAM-001 — View camera list (Positive)
**Steps:**
1. Log in as admin
2. Navigate to `/cameras/`

**Expected:** Table lists all cameras with name, site, floor, location, and active status.

---

#### TC-CAM-002 — Create a new camera (Positive)
**Steps:**
1. Navigate to `/cameras/new/`
2. Fill in:
   - Name: `test-cam-01`
   - RTSP URL: `rtsp://192.168.1.100:554/stream`
   - Site Name: `HQ`
   - Floor: `1`
   - Location: `Lobby`
   - Username / Password (optional)
   - Is Active: checked
3. Click **Save**

**Expected:**
- Redirected to camera list
- `test-cam-01` appears in the table
- Entry added to `mediamtx.yml` (if MediaMTX path is configured)

---

#### TC-CAM-003 — Create camera with duplicate name (Negative)
**Steps:**
1. Create a camera named `test-cam-01`
2. Try to create another camera with the same name `test-cam-01`

**Expected:** Form shows validation error ("Camera with this name already exists"). Second camera not created.

---

#### TC-CAM-004 — Create camera with empty name (Negative)
**Steps:**
1. Navigate to `/cameras/new/`
2. Leave **Name** blank, fill other fields
3. Click **Save**

**Expected:** Form validation error for required name field. Camera not created.

---

#### TC-CAM-005 — Edit camera metadata only (floor, location) (Positive)
**Steps:**
1. Open edit page for `test-cam-01`
2. Change **Floor** from `1` to `2` and **Location** from `Lobby` to `Entrance`
3. Keep RTSP URL and name unchanged
4. Click **Save**

**Expected:**
- Redirected to camera list with success message
- Updated floor/location visible in list
- `mediamtx.yml` **NOT modified** (MediaMTX stays running)

---

#### TC-CAM-006 — Edit camera RTSP URL (Positive)
**Steps:**
1. Open edit page for `test-cam-01`
2. Change **RTSP URL** to `rtsp://192.168.1.101:554/stream`
3. Click **Save**

**Expected:**
- Success message shown
- `mediamtx.yml` updated with new source URL
- Camera list shows updated data

---

#### TC-CAM-007 — Delete a camera (Positive)
**Steps:**
1. Navigate to `/cameras/`
2. Click **Delete** for `test-cam-01`
3. Confirm deletion

**Expected:**
- Camera removed from list
- Entry removed from `mediamtx.yml`
- Success message shown

---

#### TC-CAM-008 — Delete camera that has active schedules (Negative)
**Steps:**
1. Create a camera and assign it to a running schedule
2. Try to delete that camera

**Expected:** Error message shown ("Cannot delete camera with active schedules") or cascading delete handled gracefully. No orphaned schedule records.

---

#### TC-CAM-009 — Grab live snapshot from camera (Positive)
**Steps:**
1. Navigate to `/cameras/`
2. Click **Snapshot** button for a camera with a reachable RTSP stream

**Expected:** JPEG image returned and displayed in browser or downloaded.

---

#### TC-CAM-010 — Grab snapshot from unreachable camera (Negative)
**Steps:**
1. Click **Snapshot** for a camera with an unreachable RTSP URL

**Expected:** 404 or error response returned. No crash. Graceful error message shown.

---

### 4. Schedule Management

#### TC-SCH-001 — View schedule list (Positive)
**Steps:**
1. Navigate to `/schedules/`

**Expected:** Table lists all schedules with name, camera, model, and current status (running/stopped).

---

#### TC-SCH-002 — Create a new schedule (Positive)
**Steps:**
1. Navigate to `/schedules/new/`
2. Fill in:
   - Name: `schedule-lobby`
   - Camera: select `test-cam-01`
   - Model: `yolo/yolo11n.pt`
   - Confidence: `0.5`
   - Frame Interval: `1000` ms
   - Alert Category: `Detection`
   - Save Snapshot: checked
3. Click **Save**

**Expected:**
- Redirected to schedule list
- `schedule-lobby` appears with status **Stopped**

---

#### TC-SCH-003 — Create schedule with no camera selected (Negative)
**Steps:**
1. Navigate to `/schedules/new/`
2. Leave **Camera** unselected
3. Fill all other required fields
4. Click **Save**

**Expected:** Validation error for required camera field. Schedule not created.

---

#### TC-SCH-004 — Start a schedule (Positive)
**Steps:**
1. Navigate to `/schedules/`
2. Click **Start** for `schedule-lobby`

**Expected:**
- Status changes to **Running**
- `current_job_id` populated
- soca-engine starts the job

---

#### TC-SCH-005 — Start a schedule with engine unreachable (Negative)
**Steps:**
1. Set engine URL to an invalid address
2. Click **Start** for a stopped schedule

**Expected:** Error message shown ("Failed to start job" or connection error). Schedule status remains **Stopped**.

---

#### TC-SCH-006 — Stop a running schedule (Positive)
**Steps:**
1. Start a schedule
2. Click **Stop**

**Expected:**
- Status changes to **Stopped**
- `current_job_id` cleared

---

#### TC-SCH-007 — Edit a running schedule (Negative)
**Steps:**
1. Start a schedule
2. Navigate to its edit page
3. Change the model and save

**Expected:** Warning shown that job is currently running, or schedule stops/updates are handled safely. No engine crash.

---

#### TC-SCH-008 — Delete a stopped schedule (Positive)
**Steps:**
1. Ensure schedule is stopped
2. Click **Delete**
3. Confirm

**Expected:** Schedule removed from list. Success message shown.

---

#### TC-SCH-009 — Delete a running schedule (Negative)
**Steps:**
1. Start a schedule
2. Try to delete it

**Expected:** Schedule is stopped first, then deleted. Or error message requiring stop before delete.

---

#### TC-SCH-010 — Create schedule with invalid confidence threshold (Negative)
**Steps:**
1. Navigate to `/schedules/new/`
2. Set **Confidence** to `1.5` (out of 0–1 range)
3. Click **Save**

**Expected:** Validation error shown. Schedule not created.

---

### 5. Rule Management

#### TC-RULE-001 — Add a rule to a schedule (Positive)
**Steps:**
1. Open edit page for `schedule-lobby`
2. Click **Add Rule**
3. Fill in:
   - Name: `intrusion-rule`
   - Category: `Intrusion`
   - Processing: `in_roi`
   - Trigger: `present`
   - Duration: `immediate`
   - Cooldown: `60` seconds
   - Actions: Snapshot checked
4. Click **Save Rule**

**Expected:** Rule `intrusion-rule` appears in the rules list for this schedule.

---

#### TC-RULE-002 — Add rule with empty name (Negative)
**Steps:**
1. Open schedule edit page
2. Click **Add Rule**
3. Leave **Name** blank
4. Click **Save Rule**

**Expected:** Validation error for required name. Rule not created.

---

#### TC-RULE-003 — Edit existing rule (Positive)
**Steps:**
1. Open rule edit for `intrusion-rule`
2. Change cooldown from `60` to `120`
3. Save

**Expected:** Rule updated. Cooldown shows `120` seconds.

---

#### TC-RULE-004 — Delete a rule (Positive)
**Steps:**
1. Click **Delete** on `intrusion-rule`
2. Confirm

**Expected:** Rule removed from schedule. Success message shown.

---

#### TC-RULE-005 — Add People Counting rule (Positive)
**Steps:**
1. Add rule with:
   - Mode: `people_count`
   - Direction: `left_to_right`
   - Count Threshold: `5`
   - Category: `Counting`

**Expected:** Rule saved with crossing direction and count threshold fields.

---

### 6. Monitor (Live Stream)

#### TC-MON-001 — Monitor page loads with running jobs (Positive)
**Steps:**
1. Start a schedule with **Enable Monitor** checked
2. Navigate to `/monitor/`

**Expected:** Live MJPEG stream visible for the running job. Camera name displayed above stream.

---

#### TC-MON-002 — Monitor page with no running jobs (Negative)
**Steps:**
1. Stop all running schedules
2. Navigate to `/monitor/`

**Expected:** Empty state message shown ("No active streams"). Page does not crash.

---

#### TC-MON-003 — Monitor page with engine unreachable (Negative)
**Steps:**
1. Set engine URL to unreachable address
2. Navigate to `/monitor/`

**Expected:** Error state shown. Page loads without crashing.

---

### 7. Alerts & Snapshots

#### TC-ALERT-001 — View alerts list (Positive)
**Steps:**
1. Navigate to `/alerts/`

**Expected:** Paginated table of detection events with timestamp, rule name, camera, count, category, snapshot thumbnail.

---

#### TC-ALERT-002 — Filter alerts by camera (Positive)
**Steps:**
1. Navigate to `/alerts/`
2. Select a specific camera from the **Camera** filter
3. Apply filter

**Expected:** Only events from selected camera shown. Pagination recalculates.

---

#### TC-ALERT-003 — Filter alerts by date range (Positive)
**Steps:**
1. Set **Date From** and **Date To** to a valid range
2. Apply filter

**Expected:** Only events within date range shown. Events outside range excluded.

---

#### TC-ALERT-004 — Filter alerts by date range where To < From (Negative)
**Steps:**
1. Set **Date From** to today, **Date To** to yesterday
2. Apply filter

**Expected:** Zero results shown, or validation message. No crash.

---

#### TC-ALERT-005 — Filter by alert category (Positive)
**Steps:**
1. Enter `Intrusion` in the **Category** filter
2. Apply

**Expected:** Only intrusion alerts shown.

---

#### TC-ALERT-006 — Filter by LPR plate number (Positive)
**Steps:**
1. Enter a known plate number in **Plate** filter
2. Apply

**Expected:** Only alerts matching that plate shown.

---

#### TC-ALERT-007 — View snapshot thumbnail (Positive)
**Steps:**
1. Open `/alerts/`
2. Verify snapshot thumbnails load for events with GCS storage configured

**Expected:** Thumbnail images load (`/alerts/snapshot/?b=...` returns 302 → GCS signed URL → image).

---

#### TC-ALERT-008 — Click thumbnail to open full image (Positive)
**Steps:**
1. Click a thumbnail image

**Expected:** Popup window opens with full-size snapshot image from GCS.

---

#### TC-ALERT-009 — Alerts with engine DB not configured (Negative)
**Steps:**
1. Clear the **Engine DB Path** in settings
2. Navigate to `/alerts/`

**Expected:** Empty state or info message ("Engine DB not configured"). No exception shown to user.

---

#### TC-ALERT-010 — Pagination works correctly (Positive)
**Steps:**
1. Navigate to `/alerts/` with more than 20 events
2. Click **Next page**

**Expected:** Next 20 events loaded. Page number updates. Active filters preserved across pages.

---

### 8. Alert Statistics

#### TC-STATS-001 — Statistics page loads (Positive)
**Steps:**
1. Navigate to `/alerts/stats/`

**Expected:**
- Daily detection trend chart rendered
- Hourly distribution chart rendered
- Top categories chart rendered
- Top cameras chart rendered
- Summary counters (today, week, month) visible

---

#### TC-STATS-002 — Statistics with camera filter (Positive)
**Steps:**
1. Navigate to `/alerts/stats/?camera=test-cam-01`

**Expected:** All charts filtered to show data only for `test-cam-01`.

---

#### TC-STATS-003 — Statistics with no data (Negative)
**Steps:**
1. Apply date filter with a range that has no events

**Expected:** Charts render empty (zero data). No crash. Counters show `0`.

---

### 9. Settings — Branding

#### TC-BRAND-001 — Update company name (Positive)
**Steps:**
1. Navigate to `/settings/`
2. In the **Branding** section, change company name to `PT Test Company`
3. Click **Save**

**Expected:** Success message shown. Navbar/header reflects new company name on page reload.

---

#### TC-BRAND-002 — Upload company logo (Positive)
**Steps:**
1. Navigate to `/settings/`
2. Upload a PNG or JPG file as company logo
3. Set logo height to `50`
4. Click **Save**

**Expected:** Logo visible in navbar. Height applied correctly.

---

#### TC-BRAND-003 — Upload non-image file as logo (Negative)
**Steps:**
1. Try to upload a `.pdf` or `.txt` file as company logo

**Expected:** Validation error shown. Logo not changed.

---

#### TC-BRAND-004 — Set logo height below minimum (Negative)
**Steps:**
1. Set logo height to `5` (below 16px minimum)
2. Save

**Expected:** Validation error ("Ensure this value is greater than or equal to 16"). Logo height not changed.

---

### 10. Settings — Edge Configuration

#### TC-EDGE-001 — Save valid engine URL (Positive)
**Steps:**
1. Navigate to `/settings/`
2. In **Edge** section, set Engine URL to `http://localhost:8001`
3. Click **Save**

**Expected:** Success message. Engine URL updated.

---

#### TC-EDGE-002 — Save invalid engine URL format (Negative)
**Steps:**
1. Set Engine URL to `not-a-url`
2. Click **Save**

**Expected:** Validation error shown. URL not saved.

---

#### TC-EDGE-003 — Push config to soca-engine (Positive)
**Steps:**
1. Ensure soca-engine is running and API key matches
2. Click **Push to Engine**

**Expected:** Success message. Last push timestamp updated. `last_engine_push_ok = true`.

---

#### TC-EDGE-004 — Push config to engine with wrong API key (Negative)
**Steps:**
1. Set an incorrect engine API key
2. Click **Push to Engine**

**Expected:** Error message ("Push failed" or "401 Unauthorized"). `last_engine_push_ok = false`.

---

#### TC-EDGE-005 — Generate new API key (Positive)
**Steps:**
1. Navigate to `/settings/`
2. Click **Generate API Key**

**Expected:** New 64-character hex key displayed. Old key replaced.

---

#### TC-EDGE-006 — Save GCS credentials file (Positive)
**Steps:**
1. Upload a valid GCS service account JSON file
2. Click **Save**

**Expected:** File saved to `credentials/` folder. Path stored in settings.

---

#### TC-EDGE-007 — Save Telegram credentials (Positive)
**Steps:**
1. Enter a valid Telegram bot token and chat ID
2. Click **Save**

**Expected:** Credentials saved. No error shown.

---

### 11. Settings — User Management

#### TC-USER-001 — Create a new user (Positive)
**Steps:**
1. Navigate to `/settings/`
2. In **Users** section, fill:
   - Username: `testuser`
   - Password: `Test@12345`
   - Role: `operator`
3. Click **Create User**

**Expected:** `testuser` appears in the users table with role `operator`.

---

#### TC-USER-002 — Create user with duplicate username (Negative)
**Steps:**
1. Try to create a user with the same username as an existing user

**Expected:** Error message ("A user with that username already exists"). User not created.

---

#### TC-USER-003 — Create user with empty password (Negative)
**Steps:**
1. Leave password blank
2. Click **Create User**

**Expected:** Validation error for required password. User not created.

---

#### TC-USER-004 — Delete a user (Positive)
**Steps:**
1. Click **Delete** for `testuser`
2. Confirm

**Expected:** `testuser` removed from users table.

---

#### TC-USER-005 — Delete own account (Negative)
**Steps:**
1. Log in as `admin`
2. Try to delete your own account from settings

**Expected:** Error message ("Cannot delete your own account"). Admin account not deleted.

---

#### TC-USER-006 — Operator cannot access user management (Negative)
**Steps:**
1. Log in as operator role user
2. Navigate to `/settings/`

**Expected:** User management section not visible, or action returns 403.

---

### 12. Settings — AI Models

#### TC-MODEL-001 — View available models (Positive)
**Steps:**
1. Navigate to `/settings/` → **AI Models** tab

**Expected:** List of model files from soca-engine shown (e.g. `yolo11n.pt`).

---

#### TC-MODEL-002 — Upload a new YOLO model (Positive)
**Steps:**
1. Click **Upload Model**
2. Select a `.pt` or `.onnx` file
3. Confirm upload

**Expected:** New model appears in models list. Success message shown.

---

#### TC-MODEL-003 — Upload non-model file (Negative)
**Steps:**
1. Try to upload a `.txt` or `.jpg` file as a model

**Expected:** Error message ("Invalid file type"). File not uploaded.

---

#### TC-MODEL-004 — Delete a model (Positive)
**Steps:**
1. Click **Delete** for an unused model
2. Confirm

**Expected:** Model removed from list. Success message shown.

---

#### TC-MODEL-005 — Delete model currently used by a schedule (Negative)
**Steps:**
1. Create a schedule using `yolo11n.pt`
2. Try to delete `yolo11n.pt` from the models list

**Expected:** Error or warning shown ("Model in use by active schedule"). Model not deleted, or warning displayed.

---

### 13. Settings — Operations (Purge & Restart)

#### TC-OPS-001 — Preview data purge (Positive)
**Steps:**
1. Navigate to `/settings/` → **Operations** tab
2. Set **Older than** to `30` days
3. Click **Preview**

**Expected:** Shows count of events and estimated storage to be deleted. No data deleted yet.

---

#### TC-OPS-002 — Execute data purge (Positive)
**Steps:**
1. Preview purge for 30 days (verify count > 0)
2. Click **Execute Purge**
3. Confirm

**Expected:** Events older than 30 days deleted. Success message with count shown. Snapshot files removed.

---

#### TC-OPS-003 — Purge with zero days (Negative)
**Steps:**
1. Set **Older than** to `0`
2. Click **Preview**

**Expected:** Error or warning ("Value must be greater than 0"). No purge executed.

---

#### TC-OPS-004 — Restart soca-dashboard service (Positive)
**Steps:**
1. Navigate to `/settings/` → **Operations** tab
2. Click **Restart soca-dashboard**
3. Confirm

**Expected:** Success message ("Restart triggered"). Service restarts in background. After ~5 seconds, page reloads and is accessible again.

---

#### TC-OPS-005 — Restart soca-engine service (Positive)
**Steps:**
1. Click **Restart soca-engine**
2. Confirm

**Expected:** Success message. soca-engine restarts. Running schedules resume after restart.

---

#### TC-OPS-006 — Restart with stop.sh not found (Negative)
**Steps:**
1. Configure a service path that does not contain `stop.sh`
2. Click **Restart**

**Expected:** Error message ("stop.sh / start.sh not found"). Service not restarted. Error details shown.

---

### Test Execution Notes

#### Prerequisites
- soca-dashboard running at `http://localhost:8000`
- At least one admin user exists
- soca-engine running at configured URL (for engine-dependent tests)
- GCS bucket configured (for snapshot/thumbnail tests)
- MediaMTX running (for camera relay tests)

#### Test Accounts Needed
| Username | Password | Role |
|----------|----------|------|
| admin | (configured) | admin |
| operator | (configured) | operator |
| viewer | (configured) | viewer |

#### Scope Exclusions
- soca-engine internal detection logic (tested separately)
- GCS bucket permissions (infrastructure level)
- Telegram delivery (external dependency)
- Redis stream consumption (soca-service scope)

## 9. Additional Working Documents

### Crossing Line Fix + Violation CLS Summary Design

_Source: `soca-dashboard/2026-03-27-crossing-line-violations-design.md`_

## Design: Crossing Line Fix + Violation CLS Summary
Date: 2026-03-27

### Goals
1. Fix crossing line not shown in live stream / snapshot when ROI type is LINE
2. Record PPE/People Detection violations by CLS name, display as one-line summary (e.g. "3 no-vest, 2 no-gloves")

### Principles
- Minimal changes only — no new abstractions, no extra helpers
- Flat, readable code
- Follow existing patterns in each project

---

### Fix 1: Crossing Line Not Shown

**Root cause**: `_draw_roi()` returns early for LINE type, expecting `_draw_crossing_lines()` to handle it. But `_draw_crossing_lines()` only draws for `people_count` rules. If the user uses `detection` mode rules (PPE, intrusion, etc.) with a LINE ROI, the line never appears.

**Fix** — `soca-engine/core/monitor_stream.py`:
- In `_draw_roi()`: when `roi.type == 'LINE'`, draw dashed amber line + endpoint circles directly from `roi.points`, then return
- `_draw_crossing_lines()` unchanged — still overlays direction/counts for `people_count` rules on top

One file, 6 lines changed.

---

### Fix 2: Violation CLS Name Summary

**Data flow**: engine detects → stores cls_name_summary → publishes to queue → soca-service forwards → soca-control stores → displayed in UI

#### soca-engine (4 files)

| File | Change |
|------|--------|
| `models/db.py` | Add `cls_name_summary` JSON column to `DBEvent` + `ALTER TABLE` migration (same pattern as existing migrations) |
| `workers/detection_worker.py` | Build `cls_name_summary = {cls_name: count}` from in_roi detections; pass to DBEvent |
| `core/action_dispatcher.py` | Add `{violations}` to `_format_message()` — formatted as `"3 no-vest, 2 no-gloves"` from in_roi detections |
| `core/output_publisher.py` | Add `cls_name_summary` to `roi_summary` in published payload |

#### soca-dashboard (3 files)

| File | Change |
|------|--------|
| `app/views.py` | Add `e.cls_name_summary` to alerts SQL SELECT; parse into event dict |
| `templates/alerts/list.html` | Add "Violations" column — show cls_name_summary as compact badges |
| `templates/schedules/_rule_fields.html` | Add `{violations}` badge to message placeholder list |

#### soca-control (4 files)

| File | Change |
|------|--------|
| `app/models.py` | Add `cls_name_summary = JSONField(default=dict, blank=True)` to Alert |
| `app/migrations/0012_add_cls_name_summary.py` | Django migration |
| `app/management/commands/message_parser.py` | Parse `cls_name_summary` from `roi_summary` in payload |
| `app/templates/reports/ppe.html` | Replace/supplement "Message" column with "Violations" compact badges |

---

### Violation Format
`cls_name_summary` = `{"no-vest": 3, "no-gloves": 2}` (in_roi detections only)

Display: `3 no-vest  2 no-gloves` (flat badge per class, sorted by count desc)

`{violations}` template: `"3 no-vest, 2 no-gloves"` — one-line string

---

### Out of Scope
- No changes to soca-service (it just forwards payloads, no parsing)
- No new views or URLs
- No CSS frameworks beyond existing DaisyUI

### Crossing Line Fix + Violation CLS Summary Plan

_Source: `soca-dashboard/2026-03-27-crossing-line-violations-plan.md`_

## Crossing Line Fix + Violation CLS Summary — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Fix crossing line not drawn in live stream/snapshot, and add per-class violation summary (`cls_name_summary`) recorded in DB, published via queue, and displayed in dashboards.

**Architecture:** Engine computes `cls_name_summary` (in_roi detections grouped by `cls_name`) at event time, stores it in SQLite, publishes it in the queue payload. soca-dashboard reads it directly from the engine DB. soca-control stores it on its `Alert` model via message_parser. `{violations}` template placeholder formats the summary as a one-line string.

**Tech Stack:** Python/FastAPI (soca-engine), Django (soca-dashboard, soca-control), SQLAlchemy (engine DB), SQLite, DaisyUI/Tailwind (templates).

---

### File Map

| File | Action |
|------|--------|
| `soca-engine/core/monitor_stream.py` | Modify `_draw_roi()` — draw LINE type directly |
| `soca-engine/core/action_dispatcher.py` | Add `{violations}` to `_format_message()` |
| `soca-engine/models/db.py` | Add `cls_name_summary` column + ALTER TABLE migration |
| `soca-engine/workers/detection_worker.py` | Build + store `cls_name_summary` in `_write_frame()` |
| `soca-engine/core/output_publisher.py` | Add `cls_name_summary` to `roi_summary` in payload |
| `soca-engine/tests/test_action_dispatcher.py` | Tests for `{violations}` placeholder |
| `soca-engine/tests/test_worker_helpers.py` | Test for `cls_name_summary` helper |
| `soca-dashboard/app/views.py` | Add `cls_name_summary` to alerts SQL query + event dict |
| `soca-dashboard/templates/alerts/list.html` | Add Violations column |
| `soca-dashboard/templates/schedules/_rule_fields.html` | Add `{violations}` placeholder badge |
| `soca-control/app/models.py` | Add `cls_name_summary` field to Alert |
| `soca-control/app/migrations/0012_add_cls_name_summary.py` | Django migration |
| `soca-control/app/management/commands/message_parser.py` | Parse `cls_name_summary` from payload |
| `soca-control/app/templates/reports/ppe.html` | Add Violations column to PPE table |

---

### Task 1: Fix Crossing Line Not Drawn in Stream/Snapshot

**Files:**
- Modify: `soca-engine/core/monitor_stream.py:35-52`

**Root cause:** `_draw_roi()` returns early for `LINE` type (line 38-39), deferring to `_draw_crossing_lines()`. But that function only draws for `people_count`-mode rules. Any detection-mode rule with a LINE ROI never sees the line rendered.

- [ ] **Step 1: Write the failing test**

Create/add to `soca-engine/tests/test_monitor_stream.py`:

```python
import numpy as np
import pytest
from core.monitor_stream import _draw_roi


class _ROI:
    def __init__(self, roi_type, points):
        self.type = roi_type
        self.points = points


def test_line_roi_draws_pixels():
    """LINE ROI must be rendered — pixels must change from zero."""
    frame = np.zeros((360, 640, 3), dtype=np.uint8)
    roi = _ROI('LINE', [[0.1, 0.1], [0.9, 0.9]])
    _draw_roi(frame, roi)
    assert frame.sum() > 0, "LINE ROI drew nothing — crossing line is invisible"


def test_polygon_roi_still_draws():
    frame = np.zeros((360, 640, 3), dtype=np.uint8)
    roi = _ROI('POLYGON', [[0.1, 0.1], [0.9, 0.1], [0.9, 0.9], [0.1, 0.9]])
    _draw_roi(frame, roi)
    assert frame.sum() > 0
```

- [ ] **Step 2: Run test to verify it fails**

```bash
cd soca-engine
pytest tests/test_monitor_stream.py::test_line_roi_draws_pixels -v
```
Expected: **FAIL** — `AssertionError: LINE ROI drew nothing`

- [ ] **Step 3: Fix `_draw_roi()` in `monitor_stream.py`**

Replace lines 38-39:
```python
    if getattr(roi, 'type', '') == 'LINE':
        return  # crossing line drawn separately by _draw_crossing_lines
```
With:
```python
    if getattr(roi, 'type', '') == 'LINE':
        if len(roi.points) >= 2:
            p1 = (int(roi.points[0][0] * w), int(roi.points[0][1] * h))
            p2 = (int(roi.points[1][0] * w), int(roi.points[1][1] * h))
            _draw_dashed_line(frame, p1, p2, (0, 165, 255), thickness=2)
            cv2.circle(frame, p1, 5, (0, 165, 255), -1, cv2.LINE_AA)
            cv2.circle(frame, p2, 5, (0, 165, 255), -1, cv2.LINE_AA)
        return
```

Note: `h, w = frame.shape[:2]` is already computed at line 40 — move it above this block.

Full updated `_draw_roi()`:
```python
def _draw_roi(frame: np.ndarray, roi) -> None:
    if not roi or not roi.points:
        return
    h, w = frame.shape[:2]
    if getattr(roi, 'type', '') == 'LINE':
        if len(roi.points) >= 2:
            p1 = (int(roi.points[0][0] * w), int(roi.points[0][1] * h))
            p2 = (int(roi.points[1][0] * w), int(roi.points[1][1] * h))
            _draw_dashed_line(frame, p1, p2, (0, 165, 255), thickness=2)
            cv2.circle(frame, p1, 5, (0, 165, 255), -1, cv2.LINE_AA)
            cv2.circle(frame, p2, 5, (0, 165, 255), -1, cv2.LINE_AA)
        return
    scaled = [[int(p[0] * w), int(p[1] * h)] for p in roi.points]
    arr = np.array(scaled, dtype=np.int32)
    overlay = frame.copy()
    if roi.type == "POLYGON":
        cv2.fillPoly(overlay, [arr], color=(0, 255, 255))
        cv2.addWeighted(overlay, 0, frame, 0.85, 0, frame)
        cv2.polylines(frame, [arr], isClosed=True, color=(0, 255, 255), thickness=2)
    elif roi.type == "RECT" and len(arr) >= 2:
        cv2.rectangle(overlay, tuple(arr[0]), tuple(arr[1]), (0, 255, 255), -1)
        cv2.addWeighted(overlay, 0, frame, 0.85, 0, frame)
        cv2.rectangle(frame, tuple(arr[0]), tuple(arr[1]), (0, 255, 255), 2)
```

- [ ] **Step 4: Run tests to verify they pass**

```bash
pytest tests/test_monitor_stream.py -v
```
Expected: **PASS** both tests.

- [ ] **Step 5: Commit**

```bash
cd soca-engine
git add core/monitor_stream.py tests/test_monitor_stream.py
git commit -m "fix: draw LINE ROI directly in _draw_roi so crossing line is always visible"
```

---

### Task 2: Add `{violations}` Placeholder to Message Templates

**Files:**
- Modify: `soca-engine/core/action_dispatcher.py:24-67`
- Modify: `soca-engine/tests/test_action_dispatcher.py`

- [ ] **Step 1: Write failing tests**

Add to `soca-engine/tests/test_action_dispatcher.py`:

```python
from datetime import datetime, timezone
from models.schemas import Detection, FrameResult, RuleResult
from core.action_dispatcher import _format_message


def _make_result(detections):
    return FrameResult(
        frame_id='f1', job_id='j1', camera_id='c1',
        camera_name='Cam A', edge_name='edge-1',
        timestamp=datetime(2026, 3, 27, 10, 0, 0, tzinfo=timezone.utc),
        detections=detections, aging={}, rule_results=[],
    )


def _det(cls_name, in_roi=True):
    return Detection(cls_id=1, cls_name=cls_name, confidence=0.9,
                     bbox=(0, 0, 10, 10), in_roi=in_roi)


def test_violations_placeholder_formats_cls_names():
    result = _make_result([
        _det('no-vest'), _det('no-vest'), _det('no-vest'),
        _det('no-gloves'), _det('no-gloves'),
    ])
    msg = _format_message('{violations}', result)
    assert '3 no-vest' in msg
    assert '2 no-gloves' in msg


def test_violations_excludes_out_of_roi():
    result = _make_result([
        _det('no-vest', in_roi=True),
        _det('no-vest', in_roi=False),   # should not count
    ])
    msg = _format_message('{violations}', result)
    assert '1 no-vest' in msg
    assert '2' not in msg


def test_violations_empty_when_no_detections():
    result = _make_result([])
    msg = _format_message('{violations}', result)
    assert msg == '—'
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
cd soca-engine
pytest tests/test_action_dispatcher.py::test_violations_placeholder_formats_cls_names -v
```
Expected: **FAIL** — `KeyError: 'violations'`

- [ ] **Step 3: Add `{violations}` to `_format_message()`**

In `action_dispatcher.py`, inside `_format_message()`, add before the `try:` block (after the existing aggregation lines):

```python
    # Violations: in_roi detections grouped by cls_name, sorted by count desc
    _cls_counts: dict[str, int] = {}
    for d in result.detections:
        if d.in_roi:
            _cls_counts[d.cls_name] = _cls_counts.get(d.cls_name, 0) + 1
    violations = ', '.join(
        f"{cnt} {name}"
        for name, cnt in sorted(_cls_counts.items(), key=lambda x: -x[1])
    ) or '—'
```

Then add `violations=violations` to the `template.format(...)` call:

```python
    try:
        return template.format(
            count=len(result.detections),
            in_roi_count=in_roi,
            time=ts_gmt7,
            camera_name=result.camera_name or result.camera_id,
            camera_id=result.camera_id,
            job_id=result.job_id,
            category=result.alert_category or "",
            crowd_count=result.crowd_count or 0,
            crossing_in=total_in,
            crossing_out=total_out,
            plate_number=best_plate,
            plate_expiry=best_expiry,
            lpr_count=len(result.lpr_results or []),
            violations=violations,
        )
```

- [ ] **Step 4: Run tests to verify they pass**

```bash
pytest tests/test_action_dispatcher.py -v
```
Expected: **PASS** all tests including the new ones.

- [ ] **Step 5: Commit**

```bash
git add core/action_dispatcher.py tests/test_action_dispatcher.py
git commit -m "feat: add {violations} placeholder to message templates"
```

---

### Task 3: Store `cls_name_summary` in Engine DB

**Files:**
- Modify: `soca-engine/models/db.py`
- Modify: `soca-engine/workers/detection_worker.py:32-84`
- Modify: `soca-engine/tests/test_worker_helpers.py`

- [ ] **Step 1: Write failing test for the helper**

Add to `soca-engine/tests/test_worker_helpers.py`:

```python
def _det_named(cls_name, in_roi=True, cls_id=1):
    return Detection(cls_id=cls_id, cls_name=cls_name, confidence=0.9,
                     bbox=(0, 0, 10, 10), in_roi=in_roi)


def test_cls_name_summary_counts_in_roi():
    from workers.detection_worker import _build_cls_name_summary
    dets = [
        _det_named('no-vest'), _det_named('no-vest'),
        _det_named('no-gloves'),
        _det_named('no-vest', in_roi=False),  # excluded
    ]
    result = _build_cls_name_summary(dets)
    assert result == {'no-vest': 2, 'no-gloves': 1}


def test_cls_name_summary_empty():
    from workers.detection_worker import _build_cls_name_summary
    assert _build_cls_name_summary([]) == {}
```

- [ ] **Step 2: Run test to verify it fails**

```bash
pytest tests/test_worker_helpers.py::test_cls_name_summary_counts_in_roi -v
```
Expected: **FAIL** — `ImportError: cannot import name '_build_cls_name_summary'`

- [ ] **Step 3: Add `_build_cls_name_summary` to `detection_worker.py`**

Add after the `_filter_cls` function (around line 143):

```python
def _build_cls_name_summary(detections) -> dict:
    """Count in-ROI detections by cls_name."""
    counts: dict[str, int] = {}
    for d in detections:
        if d.in_roi:
            counts[d.cls_name] = counts.get(d.cls_name, 0) + 1
    return counts
```

- [ ] **Step 4: Add `cls_name_summary` column to `db.py`**

In `DBEvent` class, add after `cls_summary`:
```python
cls_name_summary  = Column(JSON,     nullable=False, default=dict)
```

In `init_db()`, add to the migration list:
```python
("cls_name_summary", "ALTER TABLE detection_events ADD COLUMN cls_name_summary TEXT DEFAULT '{}'"),
```

- [ ] **Step 5: Use `_build_cls_name_summary` in `_write_frame()`**

In `_write_frame()`, replace:
```python
            cls_counts = {}
            for d in result.detections:
                cls_counts[str(d.cls_id)] = cls_counts.get(str(d.cls_id), 0) + 1
```
With:
```python
            cls_counts = {}
            for d in result.detections:
                cls_counts[str(d.cls_id)] = cls_counts.get(str(d.cls_id), 0) + 1
            cls_name_counts = _build_cls_name_summary(result.detections)
```

And add `cls_name_summary=cls_name_counts` to the `DBEvent(...)` constructor:
```python
            session.add(DBEvent(
                job_id=result.job_id,
                frame_id=result.frame_id,
                timestamp=result.timestamp,
                rule_name=rr.rule_name,
                actions_fired=rr.actions_fired,
                detection_count=len(result.detections),
                in_roi_count=sum(1 for d in result.detections if d.in_roi),
                cls_summary=cls_counts,
                cls_name_summary=cls_name_counts,
                aging_snapshot=result.aging,
                snapshot_path=result.snapshot_path,
                alert_category=result.alert_category,
                snapshot_message=result.snapshot_message,
                raw_detections=[
                    {"cls_id": d.cls_id, "cls_name": d.cls_name,
                     "confidence": d.confidence, "bbox": list(d.bbox),
                     "track_id": d.track_id, "in_roi": d.in_roi,
                     "plate_number": d.plate_number, "plate_expiry": d.plate_expiry,
                     "plate_confidence": d.plate_confidence}
                    for d in result.detections
                ],
                crossing_counts=result.crossing_counts,
                crowd_count=result.crowd_count,
                lpr_results=result.lpr_results,
            ))
```

- [ ] **Step 6: Run tests**

```bash
pytest tests/test_worker_helpers.py -v
```
Expected: **PASS** all tests.

- [ ] **Step 7: Commit**

```bash
git add models/db.py workers/detection_worker.py tests/test_worker_helpers.py
git commit -m "feat: store cls_name_summary in detection events"
```

---

### Task 4: Publish `cls_name_summary` in Queue Payload

**Files:**
- Modify: `soca-engine/core/output_publisher.py:29-70`

No new tests needed — the existing payload structure is well-tested downstream by soca-control's message_parser tests.

- [ ] **Step 1: Add `cls_name_summary` to `_to_dict()`**

In `output_publisher.py`, after the `cls_counts` loop (line 43), add:

```python
    cls_name_counts: dict[str, int] = {}
    for d in in_roi:
        cls_name_counts[d.cls_name] = cls_name_counts.get(d.cls_name, 0) + 1
```

Then update `roi_summary`:
```python
        "roi_summary": {
            "total_count":      len(result.detections),
            "in_roi_count":     len(in_roi),
            "cls_counts":       cls_counts,
            "cls_name_summary": cls_name_counts,
        },
```

- [ ] **Step 2: Commit**

```bash
git add core/output_publisher.py
git commit -m "feat: include cls_name_summary in queue payload roi_summary"
```

---

### Task 5: soca-dashboard — Show Violations in Alerts List

**Files:**
- Modify: `soca-dashboard/app/views.py:391-442`
- Modify: `soca-dashboard/templates/alerts/list.html:91-101` and `126-146`
- Modify: `soca-dashboard/templates/schedules/_rule_fields.html:178-190`

- [ ] **Step 1: Add `cls_name_summary` to SQL query in `views.py`**

In the `alerts()` view, update the SELECT query (around line 391):
```python
            rows = con.execute(
                f"""SELECT e.id, e.timestamp, e.rule_name, e.detection_count, e.in_roi_count,
                           e.snapshot_path, e.alert_category, e.snapshot_message, e.job_id, j.config,
                           e.crossing_counts, e.crowd_count, e.lpr_results,
                           COALESCE(e.cls_name_summary, '{{}}') as cls_name_summary
                    FROM detection_events e
                    JOIN detection_jobs j ON e.job_id = j.id
                    {where_sql}
                    ORDER BY e.timestamp DESC
                    LIMIT ? OFFSET ?""",
                params + [per_page, offset],
            ).fetchall()
```

In the event-building loop, add after `lpr_plates`:
```python
                try:
                    cls_name_summary = json.loads(row["cls_name_summary"] or "{}")
                except Exception:
                    cls_name_summary = {}

                events.append({
                    "id": row["id"],
                    "timestamp": ts_display,
                    "rule_name": row["rule_name"],
                    "detection_count": row["detection_count"],
                    "in_roi_count": row["in_roi_count"],
                    "snapshot_path": row["snapshot_path"],
                    "alert_category": row["alert_category"] or "",
                    "snapshot_message": row["snapshot_message"] or "",
                    "crossing_counts": crossing_counts,
                    "crowd_count": row["crowd_count"] or 0,
                    "lpr_plates": lpr_plates,
                    "cls_name_summary": cls_name_summary,
                    "camera_name": cam_obj.name if cam_obj else camera_id,
                    "job_id": row["job_id"],
                })
```

- [ ] **Step 2: Add Violations column to `alerts/list.html`**

In the `<thead>` row, add after `<th>In ROI</th>`:
```html
              <th class="text-xs font-semibold text-base-content/60">Violations</th>
```

In the `<tbody>` loop, add the matching `<td>` after the In ROI cell:
```html
              <td class="text-xs">
                {% if ev.cls_name_summary %}
                  {% for cls_name, cnt in ev.cls_name_summary.items %}
                  <span class="inline-block font-mono text-warning mr-1">{{ cnt }} {{ cls_name }}</span>
                  {% endfor %}
                {% else %}
                  <span class="text-base-content/30">—</span>
                {% endif %}
              </td>
```

- [ ] **Step 3: Add `{violations}` badge to `_rule_fields.html`**

In the placeholders row (around line 179), add after `{crossing_out}`:
```html
        <code class="msg-ph">{violations}</code>
```

- [ ] **Step 4: Manual smoke test**

Start soca-dashboard locally, trigger a detection event with PPE classes, open Alerts page — verify the Violations column shows e.g. `3 no-vest 2 no-gloves`.

- [ ] **Step 5: Commit**

```bash
cd soca-dashboard
git add app/views.py templates/alerts/list.html templates/schedules/_rule_fields.html
git commit -m "feat: show cls_name_summary violations column in alerts list"
```

---

### Task 6: soca-control — Store + Display `cls_name_summary`

**Files:**
- Modify: `soca-control/app/models.py`
- Create: `soca-control/app/migrations/0012_add_cls_name_summary.py`
- Modify: `soca-control/app/management/commands/message_parser.py`
- Modify: `soca-control/app/templates/reports/ppe.html:218,227`

- [ ] **Step 1: Add field to `Alert` model**

In `soca-control/app/models.py`, add to `Alert` after `cls_summary`:
```python
    cls_name_summary = models.JSONField(default=dict, blank=True)
```

- [ ] **Step 2: Create migration**

Create `soca-control/app/migrations/0012_add_cls_name_summary.py`:
```python
from django.db import migrations, models


class Migration(migrations.Migration):

    dependencies = [
        ('app', '0011_edge_pubsub'),
    ]

    operations = [
        migrations.AddField(
            model_name='alert',
            name='cls_name_summary',
            field=models.JSONField(blank=True, default=dict),
        ),
    ]
```

- [ ] **Step 3: Run migration**

```bash
cd soca-control
python manage.py migrate
```
Expected: `Applying app.0012_add_cls_name_summary... OK`

- [ ] **Step 4: Update `message_parser.py`**

Add `cls_name_summary` to the return dict (after `cls_summary`):
```python
    return {
        'edge_name':        payload.get('edge_name') or '',
        'camera_id':        payload.get('camera_id') or '',
        'camera_name':      payload.get('camera_name') or '',
        'job_id':           payload.get('job_id') or '',
        'frame_id':         payload.get('frame_id') or '',
        'timestamp':        parse_datetime(payload.get('timestamp') or '') or timezone.now(),
        'alert_category':   payload.get('alert_category') or '',
        'rule_name':        rule_name,
        'snapshot_message': payload.get('snapshot_message') or '',
        'snapshot_path':    payload.get('snapshot_path') or '',
        'detection_count':  roi.get('total_count', 0),
        'in_roi_count':     roi.get('in_roi_count', 0),
        'cls_summary':      roi.get('cls_counts', {}),
        'cls_name_summary': roi.get('cls_name_summary', {}),
        'crossing_counts':  payload.get('crossing_counts') or {},
        'crowd_count':      payload.get('crowd_count') or 0,
        'lpr_plates':       lpr_plates,
        'raw':              payload,
    }
```

- [ ] **Step 5: Add Violations column to PPE report table**

In `ppe.html`, update the table header row (line 218):
```html
        <tr><th>Time</th><th>Site</th><th>Camera</th><th>Rule</th><th>Violations</th><th>Message</th><th>Evidence</th></tr>
```

Add a `<td>` for violations in the table body row (after `<td>` for rule, before message):
```html
          <td class="text-xs">
            {% if alert.cls_name_summary %}
              {% for cls_name, cnt in alert.cls_name_summary.items %}
              <span class="inline-block font-mono text-warning mr-1">{{ cnt }} {{ cls_name }}</span>
              {% endfor %}
            {% else %}
              <span class="text-base-content/30">—</span>
            {% endif %}
          </td>
```

- [ ] **Step 6: Run soca-control tests**

```bash
cd soca-control
python manage.py test app.tests --verbosity 2
```
Expected: all tests pass.

- [ ] **Step 7: Commit**

```bash
git add app/models.py app/migrations/0012_add_cls_name_summary.py \
        app/management/commands/message_parser.py \
        app/templates/reports/ppe.html
git commit -m "feat: store and display cls_name_summary violations in soca-control"
```

---

### Self-Review Checklist

- [x] **Crossing line fix** — Task 1 covers `_draw_roi()` change + test
- [x] **`{violations}` placeholder** — Task 2 covers `_format_message()` + tests
- [x] **`cls_name_summary` in engine DB** — Task 3: `db.py` column + ALTER migration + `_write_frame()`
- [x] **`cls_name_summary` in pub/sub payload** — Task 4: `output_publisher.py`
- [x] **Dashboard alerts view** — Task 5: SQL query + template Violations column + badge
- [x] **soca-control model + migration** — Task 6: field, migration, parser, PPE template
- [x] **No soca-service changes needed** — service only forwards raw payload unchanged
- [x] **Type consistency** — `_build_cls_name_summary` defined in Task 3, used in Task 3 only
- [x] **No TBD/placeholder steps** — all steps have complete code

## Supporting Non-Markdown Artifacts

The following files are part of the documentation package but are not embedded inline in this consolidated Markdown document:

- `soca-online-guidance.html`
- `architecture/SoW Vision AI- IT 2026.docx`
- `deployment/soca-capacity-cost-calculator.xlsx`
- `deployment/generate-cost-calculator.py`
- `deployment/grafana-dashboards/01-fleet-operations-overview.json`
- `deployment/grafana-dashboards/02-object-detection-intrusion.json`
- `deployment/grafana-dashboards/03-people-counting-crowd.json`
- `deployment/grafana-dashboards/04-ppe-safety-compliance.json`
- `deployment/grafana-dashboards/05-lpr-license-plate.json`
- `testing/soca_dashboard_test_report.xlsx`
- `requirements-docs.txt`

### Notes

- `soca-online-guidance.html` is a standalone HTML guidance artifact.
- `.xlsx`, `.docx`, and `.json` files are supporting deliverables or dashboards and remain separate attachments.
- `requirements-docs.txt` captures the Python package requirements used to render the MkDocs site.

```text
mkdocs>=1.6
mkdocs-material>=9.5
pymdown-extensions>=10.0
```
