# SOCA Platform — System Architecture Overview

## What is SOCA?

**SOCA** (Security Operations & Camera Analytics) is an enterprise-grade video analytics platform built on YOLO deep learning models. It transforms existing CCTV infrastructure into intelligent, AI-powered monitoring systems deployed at the edge with centralized management in the cloud or on-premises.

---

## Four Services

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryTextColor': '#ffffff', 'edgeLabelBackground': '#f8fafc'}}}%%
graph TB
    subgraph EDGE["🖥️  EDGE DEVICE"]
        direction TB
        CAM["📷 CCTV Cameras"]
        MTX["MediaMTX Relay\nRTSP :8554 · HLS :8888"]
        DASH["soca-dashboard\nDjango · :8080\nCameras · Schedules\nRules · Settings"]
        ENG["soca-engine\nFastAPI · :8001\nYOLO Inference\nJob Management"]

        CAM -->|RTSP stream| MTX
        MTX -->|rtsp://localhost:8554| ENG
        DASH -->|push config / API key| ENG
    end

    subgraph TRANSPORT["🔀  TRANSPORT LAYER"]
        direction LR
        REDIS["Redis Stream\n(LAN / on-prem)"]
        PUBSUB["Google Pub/Sub\n(cloud / multi-site)"]
        GCS["Google Cloud Storage\nSnapshot images"]
    end

    subgraph CLOUD["🏢  CLOUD / SERVER"]
        direction TB
        SVC["soca-service\nFastAPI · :8010\nAlert consumer\nRedis → HTTP bridge"]
        CTRL["soca-control\nDjango · :8000\nFleet Dashboard\nReports · Analytics"]
    end

    ENG -->|"publish alerts"| REDIS
    ENG -->|"publish alerts"| PUBSUB
    ENG -->|"sync snapshots (10s)"| GCS

    REDIS -->|XREADGROUP| SVC
    SVC -->|"POST /api/v1/ingest"| CTRL
    PUBSUB -->|"direct subscribe"| CTRL
    GCS -->|"public snapshot URLs"| CTRL

    style EDGE fill:#dbeafe,stroke:#3b82f6,stroke-width:2px,color:#1e3a5f
    style TRANSPORT fill:#fef3c7,stroke:#d97706,stroke-width:2px,color:#78350f
    style CLOUD fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d
    style CAM fill:#0369a1,stroke:#075985,color:#ffffff
    style MTX fill:#0284c7,stroke:#0369a1,color:#ffffff
    style DASH fill:#1d4ed8,stroke:#1e40af,color:#ffffff
    style ENG fill:#2563eb,stroke:#1d4ed8,color:#ffffff
    style REDIS fill:#b45309,stroke:#92400e,color:#ffffff
    style PUBSUB fill:#d97706,stroke:#b45309,color:#ffffff
    style GCS fill:#ea580c,stroke:#c2410c,color:#ffffff
    style SVC fill:#15803d,stroke:#166534,color:#ffffff
    style CTRL fill:#16a34a,stroke:#15803d,color:#ffffff
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

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryTextColor': '#ffffff', 'edgeLabelBackground': '#f8fafc'}}}%%
flowchart LR
    ENG["soca-engine"]:::edge -->|XADD| R["Redis Stream"]:::transport
    R -->|XREADGROUP| SVC["soca-service"]:::cloud
    SVC -->|"POST /api/v1/ingest"| CTRL["soca-control"]:::cloud

    classDef edge fill:#2563eb,color:#ffffff,stroke:#1d4ed8,font-color:#ffffff
    classDef transport fill:#b45309,color:#ffffff,stroke:#92400e,font-color:#ffffff
    classDef cloud fill:#15803d,color:#ffffff,stroke:#166534,font-color:#ffffff
```

- Best for: on-premises deployments, LAN environments
- soca-engine pushes detection events to a named Redis stream
- soca-service uses Consumer Groups (`XREADGROUP`/`XACK`) — safe for multiple replicas
- Each edge uses a unique stream name (e.g. `edge-jakarta-01:soca:detections`)

### Option B — Google Pub/Sub (cloud, multi-site)

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryTextColor': '#ffffff', 'edgeLabelBackground': '#f8fafc'}}}%%
flowchart LR
    ENG["soca-engine"]:::edge -->|publish| PS["Google Pub/Sub\ntopic"]:::transport
    PS -->|"direct subscribe\n(consume_pubsub)"| CTRL["soca-control"]:::cloud

    classDef edge fill:#2563eb,color:#ffffff,stroke:#1d4ed8,font-color:#ffffff
    classDef transport fill:#d97706,color:#ffffff,stroke:#b45309,font-color:#ffffff
    classDef cloud fill:#15803d,color:#ffffff,stroke:#166534,font-color:#ffffff
```

- Best for: cloud deployments, geographically distributed edges
- No Redis proxy VM required — fully managed GCP service
- soca-control subscribes directly via `consume_pubsub` management command (no soca-service needed)
- All edges can publish to a single topic; `edge_name` field identifies the source
- Per-edge subscription configured in soca-control Settings → Edges → Edit → Pub/Sub Subscription

---

## Snapshot Storage Options

### Option A — Local (edge-proxied)

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryTextColor': '#ffffff', 'edgeLabelBackground': '#f8fafc'}}}%%
flowchart LR
    ENG["soca-engine"]:::edge -->|"save JPG"| FS["local snapshots/"]:::storage
    FS -->|snapshot_path in alert| CTRL["soca-control"]:::cloud
    CTRL -->|"proxy URL via\nsoca-dashboard"| DASH["soca-dashboard\n{edge.url}/alerts/snapshot/"]:::edge

    classDef edge fill:#2563eb,color:#ffffff,stroke:#1d4ed8,font-color:#ffffff
    classDef storage fill:#475569,color:#ffffff,stroke:#334155,font-color:#ffffff
    classDef cloud fill:#15803d,color:#ffffff,stroke:#166534,font-color:#ffffff
```

- Best for: LAN/on-premises where soca-dashboard is directly reachable
- No cloud storage required

### Option B — Google Cloud Storage (recommended for cloud/multi-edge)

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryTextColor': '#ffffff', 'edgeLabelBackground': '#f8fafc'}}}%%
flowchart LR
    ENG["soca-engine"]:::edge -->|"save JPG"| FS["local snapshots/"]:::storage
    FS -->|"async sync\nevery 10s"| GCS["Google Cloud Storage\ngs://bucket/prefix/snapshots/..."]:::gcs
    GCS -->|"public HTTPS URL"| CTRL["soca-control"]:::cloud

    classDef edge fill:#2563eb,color:#ffffff,stroke:#1d4ed8,font-color:#ffffff
    classDef storage fill:#475569,color:#ffffff,stroke:#334155,font-color:#ffffff
    classDef gcs fill:#ea580c,color:#ffffff,stroke:#c2410c,font-color:#ffffff
    classDef cloud fill:#15803d,color:#ffffff,stroke:#166534,font-color:#ffffff
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

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryTextColor': '#ffffff', 'edgeLabelBackground': '#f8fafc'}}}%%
flowchart LR
    CAM["📷 CCTV Camera"]:::cam -->|"RTSP (on-demand)"| MTX["MediaMTX Relay\nsourceOnDemand: yes"]:::relay
    MTX -->|"rtsp://localhost:8554/camera_name\n(detection)"| ENG["soca-engine"]:::edge
    MTX -->|"http://edge-host:8888/camera_name\n(HLS live view)"| BRW["🌐 Browser"]:::viewer

    classDef cam fill:#0369a1,color:#ffffff,stroke:#075985,font-color:#ffffff
    classDef relay fill:#0284c7,color:#ffffff,stroke:#0369a1,font-color:#ffffff
    classDef edge fill:#2563eb,color:#ffffff,stroke:#1d4ed8,font-color:#ffffff
    classDef viewer fill:#7c3aed,color:#ffffff,stroke:#6d28d9,font-color:#ffffff
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

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryTextColor': '#ffffff', 'edgeLabelBackground': '#f8fafc'}}}%%
flowchart TD
    A["📷 Camera RTSP feed\nvia MediaMTX relay"]:::step
    B["🧠 YOLO Inference\n.pt → track() · .onnx → predict()\nROI filter + rule evaluation"]:::step
    C{"Rule\nfired?"}:::decision
    D["💾 Save snapshot\nto local snapshots/"]:::step
    E["📤 Publish alert\nRedis stream or Pub/Sub"]:::step
    F["☁️ GCS sync\nevery 10s → gs://bucket/..."]:::step
    G["📥 soca-control ingests\nconsume_streams or consume_pubsub\ncreates Alert DB record"]:::step
    H["🔗 Build snapshot_url\nGCS public URL or edge-proxy URL"]:::step
    I["👤 Operator views\nalerts · snapshots · reports\nfleet health"]:::step

    A --> B --> C
    C -->|yes| D
    D --> E
    D --> F
    E --> G
    F --> H
    G --> H
    H --> I
    C -->|no| A

    classDef step fill:#2563eb,color:#ffffff,stroke:#1d4ed8,font-color:#ffffff
    classDef decision fill:#dc2626,color:#ffffff,stroke:#b91c1c,font-color:#ffffff
```

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
