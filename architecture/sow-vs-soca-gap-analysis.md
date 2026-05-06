# Gap Analysis: SoW Vision AI-IT 2026 vs. Current SOCA Platform

**Document date:** 2026-05-04  
**Source 1:** `SoW Vision AI- IT 2026.docx` — Statement of Work for VMX–NX VMS Exploration, Safety Compliance Analytics & Dashboard  
**Source 2:** Current SOCA platform as documented in `system-overview.md`, `object-detection-processor.md`, `soca-service/overview.md`, `soca-control/design-spec.md`, `2026-04-16-nxvms-integration.md`, `licensing-model.md`  
**Scope:** Phase-1 (Safety Compliance)

---

## Summary Scorecard

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

## Current SOCA Platform Architecture (Four Services + Control Plane)

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

## Section 1 — VMX / NX VMS Exploration & Integration

> **Correction:** NX VMS integration is **fully implemented** in `soca-edge-nx/` — a purpose-built variant of the edge stack. The original `soca-edge/` uses MediaMTX; `soca-edge-nx/` replaces it with NX VMS as the stream source and adds NX bookmark/analytics push. The implementation plan in `2026-04-16-nxvms-integration.md` was the design document; `soca-edge-nx/` is the delivered code.

### What SoW Requires

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

### What SOCA Currently Has (`soca-edge-nx/`)

| Capability | Status | Notes |
|---|---|---|
| NX VMS architecture understanding | ✅ Done | Tested against NX Meta 6.1.1 at `<nx-vms-ip>:7001`; auth, device, and bookmark APIs fully explored |
| NX VMS as RTSP stream source | ✅ Implemented | `stream_source = nx_vms` in EdgeConfig; soca-engine connects to `rtsp://<nx-host>:7001/<camera-guid>?stream=secondary` or HTTPS MPJPEG endpoint |
| Camera GUID discovery from NX | ✅ Implemented | `GET /cameras/nx-cameras/` fetches device list from NX REST v2 (Bearer token) with fallback to EC2 + Digest/Basic auth (NX v4 compat) |
| Per-camera NX GUID assignment | ✅ Implemented | Camera form in `soca-edge-nx` has NX camera GUID field + "Fetch from NX VMS" live picker modal |
| NX bookmark push on alert trigger | ✅ Working | `nx_publisher.push_bookmark()` creates timeline bookmarks in NX Desktop (`POST /rest/v2/devices/{guid}/bookmarks`); tested and confirmed working on NX 6.1.1 |
| NX analytics metadata push (bboxes) | ⚠️ No-op | `push_detection()` and `push_best_shot()` target `ec2/analyticsMetadataPackets` and `ec2/analyticsObjectBestShot` — both removed in NX 6.x; kept as graceful no-ops |
| NX analytics engine registration | ⚠️ No-op | `register_engine()` targets `ec2/saveAnalyticsEngine` — removed in NX 6.x; graceful no-op |
| NX VMS settings UI tab | ✅ Implemented | Dedicated "NX VMS" tab in Settings with `stream_source` toggle, NX server URL, credentials, camera ID, bookmark push toggle |
| NX fields in DB (migration) | ✅ Done | Migrations `0026_nx_vms`, `0027_camera_nx_rtsp_url`, `0028_alter_camera_nx_rtsp_url` applied; `stream_source`, `nx_url`, `nx_username`, `nx_password`, `nx_camera_id`, `nx_rtsp_url` all in schema |
| Action dispatcher wired to NX | ✅ Done | `action_dispatcher.py` calls `push_bookmark`, `push_detection`, `push_best_shot` on rule triggers; `NXPushConfig` injected from job config |
| NX VMS installed and running (server infra) | ✅ Done | NX Meta 6.1.1 confirmed running at `<nx-vms-ip>:7001` (tested in code comments) |
| Historical event extraction from NX | ❌ Not Done | No pipeline to pull NX's own stored historical events into soca-control |
| Architecture diagram / installation guide | ⚠️ Partial | Integration plan doc exists; no formal deployment diagram or step-by-step installation guide for NX client–server topology |
| Working PoC formal documentation | ⚠️ Partial | Code tested (bookmark push confirmed working), but no formal PoC report or verification checklist completed |

### NX VMS Integration Architecture (actual, in `soca-edge-nx/`)

```
CCTV camera ──► NX VMS Server (<nx-vms-ip>:7001)
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

### Gap Summary — Section 1

> **NX VMS code integration is delivered in `soca-edge-nx/`.** RTSP ingestion from NX, camera GUID discovery, and bookmark push on AI triggers all work. The main remaining gaps are: (1) **Analytics metadata visualization in NX** is not possible because NX 6.x removed the `ec2` analytics endpoints — a NX SDK v4 upgrade or alternative approach is needed; (2) **Historical event extraction** from NX's own event store into soca-control is not implemented; (3) formal documentation deliverables (architecture diagram, installation guide) are not completed.

**Remaining work:**
1. Investigate NX REST v4 analytics SDK (replacement for removed `ec2` endpoints) to restore bounding-box overlay in NX Desktop
2. Implement NX historical event extraction pipeline (pull NX bookmarks/events → normalize → push to soca-control)
3. Produce formal architecture diagram and NX installation/configuration guide
4. Complete formal PoC verification checklist

---

## Section 2 — Dashboard Development (Safety Compliance)

### What SoW Requires

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

### What SOCA Currently Has

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

### Gap Summary — Section 2

> **Core data infrastructure is solid.** Events flow from cameras through AI inference to the dashboard in real time. Per-category report pages exist for Safety Compliance use cases (PPE, Intrusion). However, the **analytical KPI layer** specified in the SoW is missing: there is no compliance rate calculation, no location heatmap, no compliance score, and no confirmed trend charting. The dashboard shows raw alert events, not processed safety KPIs.

**What needs to happen:**
1. Implement PPE compliance rate = `(frames with PPE violations) / (frames with personnel present)` — requires counting "clean" frames as denominator
2. Build location-based risk heatmap — needs camera location metadata and alert density aggregation per location
3. Implement compliance score — define formula (e.g. weighted average of compliance rates across KPI categories)
4. Confirm/build incident frequency trend charts (daily/weekly time-series views)
5. Produce KPI definition document
6. Produce dashboard wireframe and technical documentation

---

## Section 3 — AI Model & Analytics Development (Safety Compliance)

### What SoW Requires

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

### What SOCA Currently Has

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
| **Working PoC with NX** | ✅ Available | Bookmark push confirmed working on NX Meta 6.1.1 at `<nx-vms-ip>:7001` |

### Gap Summary — Section 3

> **AI detection capabilities and the NX integration path are both operational in `soca-edge-nx/`.** The end-to-end flow Camera → NX VMS → soca-engine (RTSP pull) → YOLO inference → NX bookmark + soca-control alert is implemented and tested. The remaining gap is **bounding-box overlay visualization inside NX Desktop** — this requires the NX analytics SDK which was removed in NX 6.x. "Unsafe behavior" as a concept is covered by configurable ROI rules; a purpose-trained specialized model has not been developed.

**Remaining work:**
1. Investigate NX REST v4 analytics SDK to restore detection metadata push (bboxes visible in NX Desktop)
2. Evaluate whether a custom-trained "unsafe behavior" model is needed or if YOLO rules with ROI zones are sufficient
3. Produce formal model documentation (inputs/outputs/architecture) as a standalone deliverable

---

## What Is Built and Working (Not in SoW — Bonus Capabilities)

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

## Priority Action Plan to Close Gaps

### Priority 1 — Close NX VMS Remaining Gaps

| Action | Effort | Dependency |
|---|---|---|
| Investigate NX REST v4 analytics SDK — restore bounding-box overlay in NX Desktop | Research — Medium | NX server upgrade or SDK access |
| Implement NX historical event extraction (pull NX bookmarks/events → normalize → soca-control) | Dev — Medium | NX VMS API access |
| Produce NX architecture diagram (deployment topology, port map) | Docs — Small | — |
| Produce NX installation & configuration guide | Docs — Medium | — |
| Complete formal PoC verification checklist (recording, playback, bookmark, RTSP pull) | QA — Small | NX running |

### Priority 2 — Safety KPI Analytics Layer (closes Section 2 gaps)

| Action | Effort | Dependency |
|---|---|---|
| Define PPE compliance rate formula + data model (requires denominator: person-present frames) | Design | Agreement on formula |
| Implement compliance rate calculation in soca-control | Dev — Medium | Formula defined |
| Build location-based risk heatmap (requires camera location metadata) | Dev — Large | Camera location data |
| Implement compliance score composite metric | Dev — Medium | KPI formulas defined |
| Build/confirm incident frequency trend charts (time-series bars) | Dev — Small-Medium | — |
| Produce KPI definition document | Docs | — |
| Produce dashboard wireframe & technical documentation | Docs | — |

### Priority 3 — Model & Documentation Deliverables (closes Section 3 gaps)

| Action | Effort | Dependency |
|---|---|---|
| Evaluate unsafe behavior detection: generic YOLO rules vs. custom model | Research | — |
| Produce AI model documentation (inputs, outputs, architecture) | Docs | — |
| Validate end-to-end Camera → NX → AI → Dashboard flow | QA | Priority 1 complete |

---

## Conclusion

The SOCA platform significantly covers the SoW Phase-1 scope. Two separate edge variants exist:

- **`soca-edge/`** — MediaMTX-based, production-ready, fully operational
- **`soca-edge-nx/`** — NX VMS-integrated variant; RTSP ingestion from NX, camera GUID discovery, bookmark push on AI trigger, and the full Camera → NX → AI → Dashboard pipeline are all implemented and tested against NX Meta 6.1.1

**Remaining gaps fall into three areas:**

1. **NX analytics overlay in NX Desktop** is blocked because NX 6.x removed the `ec2` analytics metadata endpoints that SOCA relies on for bounding-box visualization. Needs investigation of the NX REST v4 analytics SDK.

2. **Safety KPI analytics layer is raw counts, not processed metrics.** The SoW requires PPE compliance rate (%), location heatmap, and compliance score — none are computed. Alert events are stored and categorized correctly, but the aggregation layer is missing.

3. **Documentation deliverables** (NX architecture diagram, installation guide, KPI definition doc, dashboard wireframe) have not been produced as formal standalone artifacts.

The detection pipeline, NX integration code, real-time event flow, fleet dashboard, and per-category reporting collectively cover the majority of Phase-1 requirements.
