# Advanced Rule Management Design

## Goal

Replace the hardcoded single-rule system in soca-dashboard with a flexible, per-schedule rule management UI that lets operators configure multi-condition detection rules with time-based scheduling, class filtering, dwell duration, and per-rule notification actions.

## Architecture

Rules are stored in a new `Rule` model (FK → Schedule). The existing `Schedule.to_job_config()` iterates all active rules to build the engine payload instead of generating one hardcoded rule. The engine gains three new capabilities: dwell tracking per track_id, per-rule cron-based activation, and cls_operator filtering. All notification destinations (Telegram, Redis) are configured globally in EdgeConfig and referenced by per-rule action flags.

## Tech Stack

- Django 4.x, SQLite — new Rule model, migration
- `croniter` — cron expression parsing (new dependency, dashboard + engine)
- DaisyUI 4 + Tailwind CSS — collapsible rule cards, inline form
- Vanilla JS — card expand/collapse, label fetch, cron preview, dynamic form fields
- soca-engine — dwell tracking, cron rule gating, cls_operator filtering, labels endpoint

---

## Data Models

### Schedule (additions only)

```python
iou_threshold = models.FloatField(default=0.45)
```

Existing fields (`aging_window`, `aging_cooldown`, `min_count`, `alert_category`, `cls_ids`, `redis_stream`) are kept but no longer shown in the form when rules exist. They serve as fallback for schedules with no Rule objects. **`Schedule.redis_stream` is kept in the model but hidden in the form** — the global `EdgeConfig.redis_stream` takes precedence at runtime; `Schedule.redis_stream` is the fallback for legacy schedules.

### Rule (new model)

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

### EdgeConfig (addition)

```python
redis_stream = models.CharField(max_length=200, default='soca:detections')
```

Migration note: add `redis_stream` to EdgeConfig first, pre-populate with `'soca:detections'` as default. Keep `Schedule.redis_stream` intact (do not drop it).

---

## Schedule Form — Sections

### Basic
Fields: `name`, `camera` (FK select), `model_path` (text), `is_active` (checkbox)

### Inference
Fields: `conf_threshold`, `iou_threshold` (new), `frame_interval_ms`

### ROI
Unchanged — polygon/rect canvas with normalized coordinates.

### Rules
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

### Output
Checkboxes: `save_snapshot`, `enable_monitor`
Status row: shows Telegram ● ready / ● not configured, Redis ● ready / ● not configured (derived from EdgeConfig).

---

## Settings → Edge (additions)

Form sections:
1. **Engine** — edge name, engine URL, MediaMTX URL
2. **Telegram** — bot token, chat ID(s) (comma-separated for multiple recipients)
3. **Redis** — stream name (global, used by all rules that publish to Redis)

`EdgeConfig.redis_stream` takes precedence over `Schedule.redis_stream` at job config build time.

---

## API Endpoint (new, engine-side)

```
GET /models/labels/?path=yolo/yolov8n.pt
```

Returns:
```json
[{"id": 0, "name": "person"}, {"id": 2, "name": "car"}, {"id": 7, "name": "truck"}]
```

Engine reads the `.names` list or `data.yaml` adjacent to the model file. Dashboard calls this via `engine_client.get_model_labels(model_path)` → JS populates cls_id checkboxes.

---

## Job Config — Updated Structure

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

## soca-engine Changes

### 1. `iou_threshold` in JobConfig and inference

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

### 2. Dwell Tracking

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

### 3. Processing Mode (in_roi vs detected)

When evaluating a rule:
```python
if rule['processing'] == 'in_roi':
    relevant = [d for d in detections if d.in_roi]
elif rule['processing'] == 'detected':
    relevant = detections   # all detections, ignoring ROI
```

Subsequent cls_operator filtering and duration check apply to `relevant`.

### 4. cls_operator Filtering

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

### 5. Per-rule Cooldown

When the new-rules path is active (job config has `rules` array), each rule tracks its own last-fire timestamp:
```python
rule_last_fired: dict[str, float] = {}  # keyed by rule name

def _in_cooldown(rule, rule_last_fired):
    last = rule_last_fired.get(rule['name'], 0)
    return (time.time() - last) < rule['cooldown_seconds']
```

The global `StateTracker` cooldown is **bypassed** in the new-rules path. The fallback (zero-rule) path continues using the global StateTracker as before.

### 6. New-Rules Evaluation Loop in detection_worker

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

### 7. RuleAction schema and action_dispatcher

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

### 8. cls_ids at job level (infer call)

`to_job_config()` sets the top-level `cls_ids` (used by `infer()` to pre-filter) to the **union of all active rule cls_ids**:
```python
all_cls_ids = list({cid for rule in active_rules for cid in rule.cls_ids})
```
This ensures `infer()` does not discard classes needed by individual rules. Per-rule `cls_operator` filtering happens after inference.

### 9. Cron Rule Gating

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

### 10. Labels Endpoint

New file `api/labels.py`:
```python
# GET /models/labels/?path=<model_path>
# Reads <model>.names file or data.yaml sibling, returns [{id, name}, ...]
```

Register in router alongside existing `/models/` route.

---

## Files Changed

### soca-dashboard
| File | Change |
|------|--------|
| `app/models.py` | Add `Rule` model, add `iou_threshold` to `Schedule`, add `redis_stream` to `EdgeConfig` |
| `app/views.py` | Add `rule_save`, `rule_delete` views; update `schedule_form`, `edge_settings`; rewrite `to_job_config()` logic |
| `app/urls.py` | Add `schedules/<id>/rules/save/` and `schedules/<id>/rules/<rid>/delete/` |
| `app/engine_client.py` | Add `get_model_labels(model_path)` |
| `templates/schedules/form.html` | Full rewrite with new sections + JS |
| `templates/settings/index.html` | Add Telegram + Redis sections to edge settings form |

### soca-engine
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

## Verification

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
