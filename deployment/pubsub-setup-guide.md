# Google Cloud Pub/Sub Setup Guide

Complete guide to configure Google Cloud Pub/Sub as the message transport between
**soca-engine** (publisher) and **soca-control** (subscriber).

---

## Architecture

```
soca-engine (edge device)
    │  detects event → rule fires → publish_queue action
    │  PUBLISHER_TYPE=pubsub
    ▼
Google Cloud Pub/Sub
    Topic: projects/<PROJECT>/topics/<TOPIC>
    │
    ▼
    Subscription: projects/<PROJECT>/subscriptions/<SUBSCRIPTION>
    │
    ▼
soca-control (central server)
    │  consume_pubsub management command
    ▼
    Alert saved to database
```

---

## Prerequisites

- Google Cloud project with Pub/Sub API enabled
- A service account key file (JSON) with Pub/Sub permissions
- `google-cloud-pubsub>=2.21` installed in both soca-engine and soca-control

---

## Step 1 — Create Service Account

1. Go to **GCP Console → IAM & Admin → Service Accounts**
2. Click **+ Create Service Account**
   - Name: e.g. `soca-pubsub`
   - ID: `soca-pubsub@<PROJECT>.iam.gserviceaccount.com`
3. Click **Done**
4. Open the service account → **Keys tab → Add Key → Create new key → JSON**
5. Save the downloaded JSON file to a secure location, e.g.:
   ```
   /etc/soca/credentials/soca-pubsub.json
   ```

---

## Step 2 — Grant IAM Roles

Go to **GCP Console → IAM & Admin → IAM → + Grant Access**:

| Principal | Role | Purpose |
|-----------|------|---------|
| `soca-pubsub@<PROJECT>.iam.gserviceaccount.com` | Pub/Sub Publisher | soca-engine publishes to topic |
| `soca-pubsub@<PROJECT>.iam.gserviceaccount.com` | Pub/Sub Subscriber | soca-control pulls from subscription |

Or grant **Pub/Sub Editor** to cover both in a single binding.

**Via gcloud CLI:**
```bash
# Publisher permission (topic level)
gcloud pubsub topics add-iam-policy-binding <TOPIC> \
  --project=<PROJECT> \
  --member="serviceAccount:soca-pubsub@<PROJECT>.iam.gserviceaccount.com" \
  --role="roles/pubsub.publisher"

# Subscriber permission (subscription level)
gcloud pubsub subscriptions add-iam-policy-binding <SUBSCRIPTION> \
  --project=<PROJECT> \
  --member="serviceAccount:soca-pubsub@<PROJECT>.iam.gserviceaccount.com" \
  --role="roles/pubsub.subscriber"
```

> IAM changes can take up to 60 seconds to propagate.

---

## Step 3 — Create Topic and Subscription

**Via GCP Console:**

1. Go to **Pub/Sub → Topics → + Create Topic**
   - Topic ID: e.g. `soca-jakarta-01`
   - Click **Create**

2. Go to **Pub/Sub → Subscriptions → + Create Subscription**
   - Subscription ID: e.g. `soca-jakarta-01`
   - Select topic: `projects/<PROJECT>/topics/soca-jakarta-01`
   - Delivery type: **Pull**
   - Click **Create**

**Via gcloud CLI:**
```bash
# Create topic
gcloud pubsub topics create soca-jakarta-01 --project=<PROJECT>

# Create subscription
gcloud pubsub subscriptions create soca-jakarta-01 \
  --topic=soca-jakarta-01 \
  --project=<PROJECT>
```

**Verify:**
```bash
gcloud pubsub topics list --project=<PROJECT>
gcloud pubsub subscriptions list --project=<PROJECT>
gcloud pubsub topics describe soca-jakarta-01 --project=<PROJECT>
gcloud pubsub subscriptions describe soca-jakarta-01 --project=<PROJECT>
```

---

## Step 4 — Configure soca-engine

Edit `soca-engine/.env`:

```env
PUBLISHER_TYPE=pubsub
PUBSUB_PROJECT_ID=<PROJECT>
PUBSUB_TOPIC=soca-jakarta-01
GOOGLE_APPLICATION_CREDENTIALS=/path/to/soca-pubsub.json
```

> Restart soca-engine after editing `.env` — config values are loaded at startup.

---

## Step 5 — Configure soca-dashboard Edge Settings

1. Go to **soca-dashboard → Settings → Edge**
2. Set **Publisher Transport** to `Google Pub/Sub`
3. Fill in **GCP Project ID** and **Pub/Sub Topic**
4. Upload credentials: **Google Application Credentials → Browse → select .json key file → Save**
   (file is saved to `credentials/` folder on the server and path written to engine `.env`)
5. Click **Save** — this writes `PUBLISHER_TYPE`, `PUBSUB_PROJECT_ID`, `PUBSUB_TOPIC`,
   and `GOOGLE_APPLICATION_CREDENTIALS` to the engine's `.env` file automatically

For each rule that should publish alerts:

1. Go to **Schedules → [schedule] → Rules**
2. Enable the **Publish to Queue** checkbox
   (this adds the `publish_queue` action to the engine job config)

---

## Step 6 — Configure soca-control Edge

1. Go to **soca-control → Settings → Edges → Edit**
2. Set **Subscriber Type** to `Google Pub/Sub`
3. Fill in:
   - **GCP Project ID**: `<PROJECT>`
   - **Pub/Sub Subscription**: `soca-jakarta-01`
   - **GCS Path Prefix**: matching `GCS_PATH_PREFIX` in soca-engine (e.g. `edge-jakarta-01`)
4. Upload credentials: **Settings → Google Cloud Credentials → Browse → select .json key file → Upload & Save**
   (or set manually in soca-control `.env`):
   ```env
   GOOGLE_APPLICATION_CREDENTIALS=/path/to/soca-pubsub.json
   ```

---

## Step 7 — Start the Consumer

```bash
python manage.py consume_pubsub
```

Expected output:
```
Starting consume_pubsub...
Subscribed to projects/<PROJECT>/subscriptions/soca-jakarta-01 for edge <edge-name>
```

---

## Step 8 — Verify End-to-End

Test publish and subscribe with the service account key:

```bash
python3 -c "
import os, time
os.environ['GOOGLE_APPLICATION_CREDENTIALS'] = '/path/to/soca-pubsub.json'
from google.cloud import pubsub_v1

# Publish
pub = pubsub_v1.PublisherClient()
future = pub.publish('projects/<PROJECT>/topics/soca-jakarta-01', b'hello-soca')
print('Published msg id:', future.result())

# Pull
time.sleep(2)
sub = pubsub_v1.SubscriberClient()
resp = sub.pull(request={
    'subscription': 'projects/<PROJECT>/subscriptions/soca-jakarta-01',
    'max_messages': 1
})
for m in resp.received_messages:
    print('Received:', m.message.data)
    sub.acknowledge(request={
        'subscription': 'projects/<PROJECT>/subscriptions/soca-jakarta-01',
        'ack_ids': [m.ack_id]
    })
sub.close()
print('Pipeline OK')
"
```

Expected output:
```
Published msg id: 18200597404376262
Received: b'hello-soca'
Pipeline OK
```

---

## Troubleshooting

### `403 PERMISSION_DENIED: pubsub.topics.publish`

The service account is missing Publisher role on the topic.

```bash
gcloud pubsub topics add-iam-policy-binding <TOPIC> \
  --project=<PROJECT> \
  --member="serviceAccount:<SA_EMAIL>" \
  --role="roles/pubsub.publisher"
```

### `403 PERMISSION_DENIED: pubsub.subscriptions.consume`

The service account is missing Subscriber role on the subscription.

```bash
gcloud pubsub subscriptions add-iam-policy-binding <SUBSCRIPTION> \
  --project=<PROJECT> \
  --member="serviceAccount:<SA_EMAIL>" \
  --role="roles/pubsub.subscriber"
```

### `404 Resource not found (resource=<SUBSCRIPTION>)`

The subscription does not exist. Create it:

```bash
gcloud pubsub subscriptions create <SUBSCRIPTION> \
  --topic=<TOPIC> \
  --project=<PROJECT>
```

### Messages going to DLQ (`soca-engine/dlq/`)

Pub/Sub publish is failing. Check soca-engine logs for the error message.
Common causes:
- Missing publisher IAM role (see above)
- Wrong `PUBSUB_PROJECT_ID` or `PUBSUB_TOPIC` in `.env`
- `GOOGLE_APPLICATION_CREDENTIALS` not set or pointing to wrong file
- Engine not restarted after `.env` changes

### soca-service subscribed but no messages processed (HTTP proxy environment)

**Symptoms:**
- soca-service logs show `Subscribed to ...` but `messages_processed` stays 0
- GCP console shows "Deadline exceeded" on the subscription
- Pull works when `HTTPS_PROXY` is set manually but not from systemd

**Cause:** gRPC (used by `pubsub_v1.SubscriberClient` streaming pull) ignores
`HTTPS_PROXY`, `HTTP_PROXY`, and even `GRPC_PROXY_EXP` environment variables in
some environments. The streaming pull connection silently fails behind an HTTP proxy.

**Fix:** soca-service uses REST polling mode by default (`consumers/pubsub.py`).
Ensure the following environment variables are set in the systemd service or `.env`:

```env
HTTPS_PROXY=http://your-proxy:8080
HTTP_PROXY=http://your-proxy:8080
NO_PROXY=localhost,127.0.0.1,<server-ip>
```

Verify REST polling is active in logs:
```
PubSubConsumer starting (REST poll mode)
Pub/Sub REST session created (proxy-friendly)
```

**Test pull manually** (confirms credentials + proxy work):
```bash
source /path/to/venv/bin/activate
HTTPS_PROXY=http://your-proxy:8080 python3 -c "
from google.cloud import pubsub_v1
from google.oauth2 import service_account
c = service_account.Credentials.from_service_account_file(
    '/path/to/pubsub.json', scopes=['https://www.googleapis.com/auth/pubsub'])
s = pubsub_v1.SubscriberClient(credentials=c)
r = s.pull(request={'subscription': 'projects/<PROJECT>/subscriptions/<SUB>', 'max_messages': 1})
print(len(r.received_messages), 'messages')
"
```

### Only 1 message published, rest go to DLQ

The service account had publish permission initially (or a test message was sent
via gcloud) but the IAM binding was not properly saved. Re-apply the IAM binding
and wait for propagation.

---

## Real-World Configuration (Bekasi Edge)

| Setting | Value |
|---------|-------|
| GCP Project | `soca-video-analytics-dev` |
| Topic | `soca-snapshot-notification` |
| Subscription | configured per soca-control edge |
| Service Account | `soca-snapshot-bucket@soca-video-analytics-dev.iam.gserviceaccount.com` |
| Key file | `key/soca-video-analytics-dev-bucket.json` |
| GCS Bucket | `soca-snapshot-bucket` |
| GCS Path Prefix | `edge-mac-mini-bekasi` |
| Edge name in soca-control | `edge-mac-mini-bekasi` |

---

## Related Guides

- [GCS Snapshot Storage](gcs-snapshot-storage.md) — configure GCS for snapshot image storage
