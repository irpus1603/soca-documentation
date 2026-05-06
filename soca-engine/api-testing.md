# API Testing Guide

Base URL: `http://<edge-ip>:8001`

**Test Camera:** Bardi-Stairs-Front — `rtsp://<edge-ip>:8554/Bardi-Stairs-Front`

---

## 1. Health Check

```bash
curl http://<edge-ip>:8001/health
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

## 2. List Available Models

```bash
curl http://<edge-ip>:8001/models
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

## 3. Start a Job — Basic (no ROI, no rules)

```bash
curl -X POST http://<edge-ip>:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://<edge-ip>:8554/Bardi-Stairs-Front",
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

## 4. Start a Job — With Monitor (live stream analytics)

```bash
curl -X POST http://<edge-ip>:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "job_id": "job-stairs-monitor",
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://<edge-ip>:8554/Bardi-Stairs-Front",
    "model_path": "yolo/yolo11n.pt",
    "cls_ids": [0],
    "frame_interval_ms": 1000,
    "monitor": true
  }'
```

Then open in browser:
```
http://<edge-ip>:8001/jobs/job-stairs-monitor/monitor
```

Stream shows:
- Bounding boxes — green (inside ROI) / gray (outside ROI)
- Top bar: job id, camera, detection count, ROI count, rule status
- Bottom bar: aging duration per detected class

---

## 5. Start a Job — With ROI Polygon + Monitor

```bash
curl -X POST http://<edge-ip>:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "job_id": "job-stairs-roi",
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://<edge-ip>:8554/Bardi-Stairs-Front",
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
http://<edge-ip>:8001/jobs/job-stairs-roi/monitor
```

---

## 6. Start a Job — With ROI Rectangle

```bash
curl -X POST http://<edge-ip>:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://<edge-ip>:8554/Bardi-Stairs-Front",
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

## 7. Start a Job — With Rules (person count threshold)

```bash
curl -X POST http://<edge-ip>:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "job_id": "job-stairs-rules",
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://<edge-ip>:8554/Bardi-Stairs-Front",
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

## 8. Start a Job — With Aging Rule (person present > 30 seconds)

```bash
curl -X POST http://<edge-ip>:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "job_id": "job-stairs-aging",
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://<edge-ip>:8554/Bardi-Stairs-Front",
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

## 9. Start a Job — With Webhook Action

```bash
curl -X POST http://<edge-ip>:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://<edge-ip>:8554/Bardi-Stairs-Front",
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
          {"type": "webhook", "url": "http://<soca-control-ip>:8000/api/alerts/receive/"},
          {"type": "log", "level": "error"}
        ]
      }
    ],
    "output": {
      "redis_url": "redis://<soca-control-ip>:6379",
      "stream_name": "soca:detections",
      "max_snapshot_per_minute": 10,
      "snapshot_dir": "snapshots/"
    },
    "monitor": true
  }'
```

---

## 10. Start a Job — Full Example (all options)

```bash
curl -X POST http://<edge-ip>:8001/jobs/start \
  -H "Content-Type: application/json" \
  -d '{
    "job_id": "job-stairs-01",
    "camera_id": "bardi-stairs-front",
    "rtsp_url": "rtsp://<edge-ip>:8554/Bardi-Stairs-Front",
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
          {"type": "webhook", "url": "http://<soca-control-ip>:8000/api/alerts/receive/"},
          {"type": "log", "level": "error"}
        ]
      }
    ],
    "output": {
      "redis_url": "redis://<soca-control-ip>:6379",
      "stream_name": "soca:detections",
      "max_snapshot_per_minute": 5,
      "snapshot_dir": "snapshots/"
    },
    "monitor": true
  }'
```

Monitor URL:
```
http://<edge-ip>:8001/jobs/job-stairs-01/monitor
```

---

## 11. View Live Monitor Stream

```bash
# Open in browser — shows annotated video with detection overlays
http://<edge-ip>:8001/jobs/JOB_ID/monitor
```

Returns `400` if job was started without `monitor: true`.
Returns `404` if job is not running.

---

## 12. Get Job Status

```bash
curl http://<edge-ip>:8001/jobs/JOB_ID
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

## 13. List All Jobs

```bash
curl http://<edge-ip>:8001/jobs/
```

---

## 14. Stop a Job

```bash
curl -X POST http://<edge-ip>:8001/jobs/JOB_ID/stop
```

Expected:
```json
{
  "job_id": "job-stairs-01",
  "status": "stopped"
}
```

---

## Rule Condition Reference

| path | type | description |
|---|---|---|
| `detections.count` | int | total detections in frame |
| `detections.in_roi_count` | int | detections inside ROI |
| `detections.cls_ids` | list | list of all detected cls_ids |
| `detections.cls_counts.0` | int | count of cls_id 0 |
| `detections.cls_counts.1` | int | count of cls_id 1 |
| `aging.cls_0.duration_seconds` | float | seconds cls_id 0 has been present continuously |
| `aging.cls_0.in_cooldown` | bool | true if cls_id 0 is in cooldown |

## Operators Reference

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

## Publisher Transport Reference

Set in `soca-engine/.env` (or via soca-dashboard Settings → Edge Config → Publisher Transport).

| Var | Value | Behaviour |
|-----|-------|-----------|
| `PUBLISHER_TYPE` | `redis` (default) | XADD to Redis stream via `REDIS_STREAM_NAME` |
| `PUBLISHER_TYPE` | `pubsub` | Publish to Pub/Sub topic; also set `PUBSUB_PROJECT_ID` and `PUBSUB_TOPIC` |
| `PUBSUB_PROJECT_ID` | e.g. `my-gcp-project` | Required when `PUBLISHER_TYPE=pubsub` |
| `PUBSUB_TOPIC` | e.g. `soca-detections` | Topic name only (not full path) |

> When `PUBLISHER_TYPE=pubsub` and credentials/topic are missing, events fall back to the DLQ directory.

---

## `monitor` Attribute Reference

| value | behavior |
|---|---|
| `false` (default) | No live stream, detection runs headless |
| `true` | Annotated MJPEG stream available at `/jobs/{job_id}/monitor` |

## cls_id Reference (yolo11n default)

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
