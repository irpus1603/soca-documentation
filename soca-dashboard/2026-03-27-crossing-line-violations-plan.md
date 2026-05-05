# Crossing Line Fix + Violation CLS Summary — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Fix crossing line not drawn in live stream/snapshot, and add per-class violation summary (`cls_name_summary`) recorded in DB, published via queue, and displayed in dashboards.

**Architecture:** Engine computes `cls_name_summary` (in_roi detections grouped by `cls_name`) at event time, stores it in SQLite, publishes it in the queue payload. soca-dashboard reads it directly from the engine DB. soca-control stores it on its `Alert` model via message_parser. `{violations}` template placeholder formats the summary as a one-line string.

**Tech Stack:** Python/FastAPI (soca-engine), Django (soca-dashboard, soca-control), SQLAlchemy (engine DB), SQLite, DaisyUI/Tailwind (templates).

---

## File Map

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

## Task 1: Fix Crossing Line Not Drawn in Stream/Snapshot

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

## Task 2: Add `{violations}` Placeholder to Message Templates

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

## Task 3: Store `cls_name_summary` in Engine DB

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

## Task 4: Publish `cls_name_summary` in Queue Payload

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

## Task 5: soca-dashboard — Show Violations in Alerts List

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

## Task 6: soca-control — Store + Display `cls_name_summary`

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

## Self-Review Checklist

- [x] **Crossing line fix** — Task 1 covers `_draw_roi()` change + test
- [x] **`{violations}` placeholder** — Task 2 covers `_format_message()` + tests
- [x] **`cls_name_summary` in engine DB** — Task 3: `db.py` column + ALTER migration + `_write_frame()`
- [x] **`cls_name_summary` in pub/sub payload** — Task 4: `output_publisher.py`
- [x] **Dashboard alerts view** — Task 5: SQL query + template Violations column + badge
- [x] **soca-control model + migration** — Task 6: field, migration, parser, PPE template
- [x] **No soca-service changes needed** — service only forwards raw payload unchanged
- [x] **Type consistency** — `_build_cls_name_summary` defined in Task 3, used in Task 3 only
- [x] **No TBD/placeholder steps** — all steps have complete code
