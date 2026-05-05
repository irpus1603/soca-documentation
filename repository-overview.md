# SOCA Platform Documentation

**SOCA** (Security Operations & Camera Analytics) — enterprise video analytics platform with YOLO-based edge processing and centralized fleet management.

---

## Quick Start

→ **[SETUP.md](SETUP.md)** — Complete step-by-step setup for all four services

---

## Documentation Structure

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

## Service Summary

| Service | Port | Location | Purpose |
|---------|------|----------|---------|
| **soca-engine** | 8001 | Edge device | YOLO inference, job management, alert publishing, GCS snapshot sync |
| **soca-dashboard** | 8080 | Edge device | Camera/schedule/rule management UI, edge config, license activation |
| **soca-service** | 8080 | Server | Alert consumer microservice (Redis + Pub/Sub) |
| **soca-control** | 8000 | Server | Fleet dashboard, reporting, alert storage |
| **soca-entitlement** | 8080 | Cloud (`34.101.213.40`) | License key management, activation API |

---

## Transport Options

| Transport | Best for | Key config |
|-----------|---------|------------|
| **Redis Stream** | On-premises, LAN | `PUBLISHER_TYPE=redis`, `REDIS_URL`, `REDIS_STREAM_NAME` |
| **Google Pub/Sub** | Cloud, multi-region | `PUBLISHER_TYPE=pubsub`, `PUBSUB_PROJECT_ID`, `PUBSUB_TOPIC` |

---

## Snapshot Storage Options

| Storage | Best for | Key config |
|---------|---------|------------|
| **Edge Local** | LAN/on-premises | `SiteConfig.snapshot_storage=local` in soca-control |
| **Google Cloud Storage** | Cloud, multi-edge | `GCS_BUCKET`, `GCS_PATH_PREFIX` in soca-engine; `SiteConfig.snapshot_storage=gcs` in soca-control |

---

## Key Features (as of 2026-05-01)

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
