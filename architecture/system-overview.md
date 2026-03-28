# SOCA Platform — System Architecture Overview

## What is SOCA?

**SOCA** (Security Operations & Camera Analytics) is an enterprise-grade video analytics platform built on YOLO deep learning models. It transforms existing CCTV infrastructure into intelligent, AI-powered monitoring systems deployed at the edge with centralized management in the cloud or on-premises.

---

## Four Services

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

## Service Roles

| Service | Type | Port | Role |
|---------|------|------|------|
| **soca-engine** | FastAPI | 8001 | YOLO video processing, job management, alert publishing, GCS snapshot sync |
| **soca-dashboard** | Django | 8080 | Per-edge UI: cameras, schedules, rules, settings |
| **soca-service** | FastAPI | 8010 | Alert consumer microservice; bridges edge transports to soca-control |
| **soca-control** | Django | 8000 | Fleet dashboard: all edges, alerts, reports, user management |

---

## Alert Transport Options

### Option A — Redis Stream (default, LAN/local)

```
soca-engine ──XADD──► Redis stream ──XREADGROUP──► soca-service ──POST──► soca-control
```

- Best for: on-premises deployments, LAN environments
- soca-engine pushes detection events to a named Redis stream
- soca-service uses Consumer Groups (`XREADGROUP`/`XACK`) — safe for multiple replicas
- Each edge uses a unique stream name (e.g. `edge-jakarta-01:soca:detections`)

### Option B — Google Pub/Sub (cloud, multi-site)

```
soca-engine ──publish──► Pub/Sub topic ──subscribe──► soca-control (consume_pubsub)
```

- Best for: cloud deployments, geographically distributed edges
- No Redis proxy VM required — fully managed GCP service
- soca-control subscribes directly via `consume_pubsub` management command (no soca-service needed)
- All edges can publish to a single topic; `edge_name` field identifies the source
- Per-edge subscription configured in soca-control Settings → Edges → Edit → Pub/Sub Subscription

---

## Snapshot Storage Options

### Option A — Local (edge-proxied)

```
soca-engine → saves JPG to local snapshots/ → snapshot_path in alert
soca-control → builds URL via soca-dashboard: {edge.url}/alerts/snapshot/{rel_path}
```

- Best for: LAN/on-premises where soca-dashboard is directly reachable
- No cloud storage required

### Option B — Google Cloud Storage (recommended for cloud/multi-edge)

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

## YOLO Inference Engine

### Supported Model Formats

| Format | Inference method | Tracking | Device selection |
|--------|-----------------|----------|-----------------|
| `.pt` (PyTorch) | `model.track()` | Yes — `track_id` populated | `model.to(device)` at load |
| `.onnx` (ONNX) | `model.predict()` | No — `track_id` is None | Device passed per-call |

### Device Configuration

| Device | Config value | Notes |
|--------|-------------|-------|
| CPU | `INFER_DEVICE=cpu` | Default — works everywhere |
| CUDA GPU | `INFER_DEVICE=cuda:0` | NVIDIA GPU required |

> MPS (Apple Silicon) was removed — pass device explicitly via `INFER_DEVICE`.

### Performance Configuration (soca-engine `.env`)

```env
INFER_DEVICE=cpu          # cpu | cuda:0
INFER_IMGSZ=640           # inference resolution (YOLO native = 640)
INFER_HALF=false          # FP16 half-precision — opt-in, test before enabling
```

Per-job `imgsz` override available via `JobConfig.imgsz`.

### Performance Tiers (implemented)

| Tier | Change | Impact |
|------|--------|--------|
| 1 | `INFER_DEVICE`, `INFER_IMGSZ`, `INFER_HALF` env vars | 30–60% faster inference |
| 2 | Dedicated RTSP reader thread (shared-slot pattern) | Decouples network I/O from inference |
| 3 | Conditional LPR — skip EasyOCR when no vehicles (COCO 2/5/7) | Eliminates wasted 50–200ms OCR calls |

---

## Rule Engine

### Detection Modes

| Mode | Description |
|------|-------------|
| `detection` | Triggers when objects are detected in/out of ROI |
| `people_count` | Counts crossings across a virtual line (in/out) |
| `crowd` | Triggers when in-ROI person count exceeds threshold |

### Line Crossing Direction

| Direction | Counts as IN when... |
|-----------|---------------------|
| `any` | Every crossing (both directions count as IN) |
| `left_to_right` | Centroid x increasing across line |
| `right_to_left` | Centroid x decreasing across line |
| `top_to_bottom` | Centroid y increasing across line |
| `bottom_to_top` | Centroid y decreasing across line |

> Note: `any` mode counts ALL crossings as IN regardless of line orientation — use directional modes for entrance/exit counting.

### Alert Categories

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

## MediaMTX Relay (RTSP Ingestion)

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

## Data Flow Summary

1. **soca-engine** captures RTSP video frames via MediaMTX relay (`rtsp://localhost:8554/<camera_name>`) — or direct camera RTSP as fallback when relay is not configured
2. YOLO model runs inference (`.pt` → `track()`, `.onnx` → `predict()`); ROI filtering and rule evaluation applied
3. When a rule fires: snapshot saved locally + alert published to Redis stream or Pub/Sub topic
4. **GCS sync** (background task, every 10s): new snapshots uploaded to `gs://{bucket}/{edge_prefix}/snapshots/...`
5. **soca-control** (via `consume_streams` or `consume_pubsub`): parses payload, creates `Alert` DB record
6. `Alert.snapshot_url` builds either a GCS public URL or edge-proxy URL depending on `SiteConfig.snapshot_storage`
7. Operators view alerts, snapshots, reports, and monitor fleet health via **soca-control** web UI

---

## Security Model

- `SOCA_CONTROL_INGEST_KEY` — shared bearer token between soca-service and soca-control
- All ingest API calls require `Authorization: Bearer <key>` header
- Key generated and stored in `db.conf`; configurable via soca-control Settings UI
- soca-dashboard API key — per-edge key for soca-control to call soca-dashboard/soca-engine APIs
- GCS credentials — service account JSON key uploaded via soca-dashboard or soca-control Settings UI (stored in `credentials/` folder)
- GCS bucket — publicly readable (`allUsers:objectViewer`) for direct browser access to snapshot images

---

## Horizontal Scaling

| Component | How to scale |
|---|---|
| **soca-engine** | Independent per edge device |
| **soca-dashboard** | Independent per edge device |
| **soca-service (Redis)** | Run N instances — Consumer Groups ensure each message processed once |
| **soca-control (Pub/Sub)** | Direct subscription per edge — no soca-service needed |
| **soca-control** | Stateless Django — scale with gunicorn workers or multiple instances behind a load balancer |

---

## Ports Summary

| Service | Default Port | Configurable via |
|---------|-------------|-----------------|
| soca-engine | 8001 | `PORT` env var |
| soca-dashboard | 8080 | `PORT` env var |
| soca-service | 8010 | `PORT` env var |
| soca-control | 8000 | `PORT` env var |
| Redis | 6379 | Redis config |
| MediaMTX RTSP relay | 8554 | mediamtx.yml |
| MediaMTX HLS viewer | 8888 | mediamtx.yml |
