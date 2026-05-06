# Advanced Rule Management — soca-dashboard Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a `Rule` model and inline rule card UI to soca-dashboard, replacing the hardcoded single-rule system in `Schedule.to_job_config()` with flexible per-schedule rules supporting cls filtering, dwell duration, cron scheduling, and per-rule actions.

**Architecture:** New `Rule` model (FK → Schedule) stores all rule conditions and action flags. `Schedule.to_job_config()` iterates active rules when present, falling back to legacy single-rule for backward compatibility. The schedule form gains collapsible inline rule cards with label-aware cls_id checkboxes fetched from the engine. `EdgeConfig` gains a global `redis_stream` field.

**Tech Stack:** Django 4.x, SQLite, DaisyUI 4 + Tailwind CSS CDN, vanilla JS, Python `requests`

**Spec:** `docs/superpowers/specs/2026-03-22-advanced-rule-management-design.md`

---

## File Map

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

## Task 1: Models — Rule, iou_threshold, redis_stream, to_job_config

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

## Task 2: engine_client — get_model_labels()

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

## Task 3: Rule views + URLs

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

## Task 4: Schedule form template rewrite

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

1. Open `http://<soca-control-ip>:8000/schedules/new/`
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

## Task 5: Edge settings — add redis_stream field

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
            'engine_url': 'http://<edge-ip>:8001',
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

## Verification Checklist

1. `python manage.py test app.tests` — all tests pass
2. `python manage.py runserver` — no startup errors
3. Create a schedule → add 2 rules → save → `to_job_config()` output shows both rules
4. Start a schedule → engine receives `iou_threshold` in job config
5. Inactive rule → excluded from `to_job_config()` output
6. Schedule with no rules → legacy single-rule fallback works
7. Settings → Edge → save `redis_stream` → used in job config `publish_queue.stream`
8. Telegram/Redis flags per rule → actions included/excluded correctly
