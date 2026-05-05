# Google Pub/Sub Transport Implementation Plan

> **Status: COMPLETED 2026-03-25** — All 11 tasks implemented and committed. See commits: soca-control `7042f52`, soca-dashboard `c10ccf6`, soca-engine `843a2e9`.

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add Google Cloud Pub/Sub as an alternative message transport alongside Redis in soca-engine (publisher) and soca-control (subscriber/consumer microservice), with the choice configurable per-edge.

**Architecture:** soca-engine writes to either a Redis stream or a Pub/Sub topic based on `PUBLISHER_TYPE` env var (written by soca-dashboard's EdgeConfig save). soca-control has two independent management commands — existing `consume_streams` for Redis, new `consume_pubsub` for Pub/Sub — each run as a separate process/microservice. Both share the same `parse_message()` utility.

**Tech Stack:** `google-cloud-pubsub` (both soca-engine and soca-control), Django model migrations, DaisyUI settings UI.

---

## File Map

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

## Task 1: soca-engine — Pub/Sub publisher

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

## Task 2: soca-engine — Add config vars

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

## Task 3: soca-engine — Route output_publisher.py

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

## Task 4: soca-dashboard — EdgeConfig model + migration

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

## Task 5: soca-dashboard — Settings UI (Edge Config tab)

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

## Task 6: soca-dashboard — views.py save + .env write

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

## Task 7: soca-control — Edge model + migration

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

## Task 8: soca-control — Edge edit UI

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

## Task 9: soca-control — Shared message_parser module

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

## Task 10: soca-control — consume_pubsub.py microservice

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

## Task 11: Integration verification

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

## Running as separate microservices

Each consumer runs as its own process:

```bash
# Terminal 1 — Redis consumer (existing)
python manage.py consume_streams

# Terminal 2 — Pub/Sub consumer (new)
python manage.py consume_pubsub
```

For production, configure as separate systemd units or Docker containers. Only start the one matching your configured transport.

**GCP credentials:** Set `GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json` env var before running `consume_pubsub` and before running soca-engine (when using Pub/Sub publisher). Or use Workload Identity / ADC on GCE/GKE.
