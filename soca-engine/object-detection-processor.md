# SOCA Object Detection Processor — Technical Specification v3

## Overview

A standalone **FastAPI microservice** (`soca-engine`) that:
1. Accepts a detection job via REST API and immediately starts processing
2. Connects to an RTSP stream via **MediaMTX relay** (TCP transport forced)
3. Runs YOLO inference per frame within a configurable ROI
4. Tracks object presence duration (aging) across frames
5. Evaluates time-aware rules per frame
6. Outputs results to **Redis Streams** and persists to **local SQLite DB**
7. Optionally streams **live annotated video** (MJPEG) via `monitor=true`

---

## Architecture

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

## MediaMTX Integration

MediaMTX is treated as an **external dependency** — assumed to be running and pre-configured.

- soca-engine **does not start or manage** MediaMTX
- soca-engine consumes RTSP URLs that MediaMTX proxies/re-streams
- The `rtsp_url` in `JobConfig` must point to a MediaMTX path (e.g. `rtsp://<edge-ip>:8554/Bardi-Stairs-Front`)
- TCP transport is **forced globally** via `os.environ["OPENCV_FFMPEG_CAPTURE_OPTIONS"] = "rtsp_transport;tcp"`
- MediaMTX config (path rules, auth, source cameras) is managed separately via `mediamtx.yml`
- Camera source URLs live inside `mediamtx.yml` — soca-engine only talks to MediaMTX output paths

If RTSP connection fails, the worker retries with exponential backoff (max 5 retries, 2s → 32s), then marks the job as `error`.

**Example MediaMTX path config (`mediamtx.yml`):**
```yaml
paths:
  Bardi-Stairs-Front:
    source: rtsp://admin:abim@<camera-ip>:8554/Streaming/Channels/101
    rtspTransport: tcp
    sourceOnDemand: no
```

**soca-engine consumes:**
```
rtsp://<edge-ip>:8554/Bardi-Stairs-Front
```

---

## API Contract

### `POST /jobs/start`

**Request Body:**
```json
{
  "job_id": "optional-custom-id",
  "camera_id": "bardi-stairs-front",
  "rtsp_url": "rtsp://<edge-ip>:8554/Bardi-Stairs-Front",
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
          "url": "http://<soca-control-ip>:8000/api/alerts/receive/",
          "message_template": "{in_roi_count} person(s) in zone at {time}"
        },
        {"type": "log", "level": "warning"}
      ]
    }
  ],
  "output": {
    "redis_url": "redis://<soca-control-ip>:6379",
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

### `GET /jobs/{job_id}/monitor`

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

### `GET /jobs/{job_id}`
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

### `POST /jobs/{job_id}/stop`
Signals the worker thread to stop gracefully (max 5s drain). Cleans up monitor buffer if active.

### `GET /jobs/`
Returns list of all jobs (active + recent stopped/error), latest 50.

### `GET /models`
```json
{
  "models": [
    {"name": "yolo11n.pt",         "path": "yolo/yolo11n.pt"},
    {"name": "yolov8s_custom.pt",  "path": "yolo/yolov8s_custom.pt"}
  ]
}
```
Scans `MODELS_DIR` for `.pt`, `.mlpackage`, `.onnx` files.

### `GET /health`
```json
{
  "status":         "ok",
  "edge_name":      "edge-bardi-home",
  "redis":          "connected",
  "active_jobs":    2,
  "uptime_seconds": 3600
}
```

### `GET /cpu`
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

### `GET /system`
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

## Core Components

### 1. `RTSPCapture`

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

### 2. `FrameGate`

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

### 3. `YOLOInference`

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

### 4. `ROIFilter`

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

### 5. `StateTracker`

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

### 6. `RuleEngine`

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

### 7. `ActionDispatcher`

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

### 8. `MonitorStream`

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

### 9. `QueuePublisher` — Redis Streams

Uses `XADD` (Redis Streams), not pub/sub.

```python
redis_client.xadd(stream_name, {"payload": json.dumps(message)},
                  maxlen=10000, approximate=True)
```

If Redis unavailable: writes to **dead-letter file** (`dlq/{job_id}.jsonl`) for manual replay.

---

### 10. `DBWriter`

Async writes via `ThreadPoolExecutor(max_workers=1)` per job. Never blocks frame loop.

---

## Output Message Schema (Redis Stream payload)

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

## Local Database Schema

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

## Job Lifecycle & Recovery

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

## Concurrency & Resource Guards

| Variable | Default | Behavior |
|---|---|---|
| `MAX_CONCURRENT_JOBS` | 4 | `POST /jobs/start` returns `503` if exceeded |
| `MAX_LOADED_MODELS` | 3 | Model load raises `RuntimeError` → `503` |

---

## Project Structure

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

## Processing Flow (per frame)

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

## Dependencies (`requirements.txt`)

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

## Environment Variables (`config.py`)

```
EDGE_NAME               (required — no default; unique name for this edge device)
REDIS_URL               redis://<soca-control-ip>:6379
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

## How to Run

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

## Implementation Status

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

## Change Log

### v3.1 — Path Resolution & Configuration

**`config.py`**
- Added `BASE_DIR = Path(__file__).resolve().parent` — engine's own directory, independent of CWD
- `load_dotenv(BASE_DIR / ".env")` — explicitly loads `.env` from engine directory instead of relying on CWD
- Added `_path(env_key, default)` helper — resolves all path env vars relative to `BASE_DIR` if not absolute
- `DB_PATH`, `MODELS_DIR`, `SNAPSHOTS_DIR`, `DLQ_DIR` all now return absolute paths regardless of where uvicorn is launched from
- `EDGE_NAME` default changed to `"MAC-MINI-EDGE"`; still required (raises `RuntimeError` if empty)

---

### v3.2 — Normalized ROI Coordinates

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

### v3.3 — Action Dispatcher Fixes & Rate Limit Behaviour

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

### v3.4 — Rule Condition Path Fix

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

### v3.5 — Alert Categories, Message Templates, Telegram Action & Redis Schema 2.2

#### New `RuleAction` fields (`models/schemas.py`)

| Field | Type | Used by |
|-------|------|---------|
| `bot_token` | `str \| None` | `telegram` action |
| `chat_id` | `str \| None` | `telegram` action |
| `message_template` | `str \| None` | `telegram` and `webhook` actions |

#### New `Rule` field

| Field | Default | Notes |
|-------|---------|-------|
| `category` | `""` | Label attached to every alert this rule fires (max 255 chars) |

#### New `FrameResult` fields

| Field | Type | Notes |
|-------|------|-------|
| `alert_category` | `str \| None` | Populated from triggered rule's `category` by dispatcher |
| `snapshot_message` | `str \| None` | Formatted text after template substitution |

#### `core/action_dispatcher.py` changes

- Added `_format_message(template, result) -> str` — substitutes `{count}`, `{in_roi_count}`, `{time}`, `{camera_id}`, `{job_id}`, `{category}` in a template string. Timestamp is always formatted in **GMT+7**.
- Added `telegram` action type: builds message text via `_format_message`, then calls `_fire_telegram(bot_token, chat_id, text, snapshot_path)` in a daemon thread. If the snapshot file exists, sends via `POST https://api.telegram.org/bot{token}/sendPhoto` (multipart upload, caption = formatted text). If not, falls back to `sendMessage`.
- Updated `webhook` action: payload now includes `alert_category`, `snapshot_message`, `detection_count`, `in_roi_count` in addition to `job_id`, `frame_id`, `timestamp`.
- `rule.category` propagates to `result.alert_category` when the rule first triggers (does not overwrite if already set from a higher-priority rule).
- `result.snapshot_message` is populated from the formatted template before the webhook/telegram fires.

#### `core/output_publisher.py` changes

- Schema version bumped `2.1` → `2.2`
- `_to_dict()` now includes `alert_category` and `snapshot_message` in the Redis payload

---

### Updated Processing Flow (v3.5)

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

### v3.6 — Snapshot Annotation, Cooldown Fix, Confidence Threshold & Telegram Photo

#### Problem 1 — Snapshot saved every second despite cooldown

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

#### Problem 2 — Only cls_id=0 detected regardless of schedule config

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

#### New `JobConfig` field — `conf_threshold`

| Field | Type | Default | Notes |
|-------|------|---------|-------|
| `conf_threshold` | `float` | `0.5` | YOLO detection confidence threshold (0.01–1.0) |

- Added to `models/schemas.py` `JobConfig`
- `detection_worker.py` passes `cfg.conf_threshold` to `yolo_inference.infer()`
- Configurable per-schedule from the dashboard (0.01–1.0, step 0.05)

---

#### Snapshot rate control unified with cooldown

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

#### Snapshot image annotation

Saved JPEG snapshots now include visual overlays (same as the live monitor stream):

- **ROI overlay** — semi-transparent cyan polygon/rect drawn on the frame copy
- **Detection bounding boxes** — green (inside ROI) / gray (outside ROI) with label `#{track_id} {cls_name} {conf}`

**Implementation (`core/action_dispatcher.py`):**
- Added `_annotate_snapshot(frame, result, roi) -> np.ndarray` — creates a copy of the frame, calls `_draw_roi` and `_draw_detection` helpers (imported from `monitor_stream.py`)
- `dispatch()` now accepts `roi=None` parameter; when `save_snapshot` action fires, it saves the annotated copy rather than the raw frame

---

#### Telegram sends photo instead of text

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

### Updated Processing Flow (v3.6)

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

## Update — Pub/Sub Transport (2026-03-25)

`core/output_publisher.py` now supports two transports, selected by the `PUBLISHER_TYPE` env var:

### Config vars (added to `config.py`)

| Var | Default | Notes |
|-----|---------|-------|
| `PUBLISHER_TYPE` | `"redis"` | `"redis"` or `"pubsub"` |
| `PUBSUB_PROJECT_ID` | `""` | GCP project ID (required when pubsub) |
| `PUBSUB_TOPIC` | `"soca-detections"` | Topic name (not full path) |

### Routing logic in `publish_to_queue()`

```
PUBLISHER_TYPE == "pubsub" and PROJECT_ID + TOPIC set?
    → publish_to_pubsub(f"projects/{PROJECT_ID}/topics/{TOPIC}", payload)
    → on failure → DLQ fallback

PUBLISHER_TYPE == "pubsub" but PROJECT_ID or TOPIC missing?
    → log warning → DLQ fallback

PUBLISHER_TYPE == "redis" (default)?
    → existing Redis XADD path → DLQ fallback on error
```

### `core/pubsub_publisher.py` (new)

- Lazy-initialised `pubsub_v1.PublisherClient` singleton
- `publish_to_pubsub(topic_path, payload)` — serialises payload to JSON bytes, calls `publish()`, awaits `future.result()` for delivery confirmation
- On error: logs and re-raises (caller writes to DLQ)

### `.env` / soca-dashboard integration

`soca-dashboard` Settings → Edge Config → **Publisher Transport** dropdown writes `PUBLISHER_TYPE`, `PUBSUB_PROJECT_ID`, and `PUBSUB_TOPIC` into `soca-engine/.env` on save.

### GCP credentials

Set `GOOGLE_APPLICATION_CREDENTIALS=/path/to/sa.json` (or use ADC on GCE/GKE) before starting soca-engine when using Pub/Sub transport.
