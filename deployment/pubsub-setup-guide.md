# Google Cloud Pub/Sub Setup Guide

Complete guide to configure Google Cloud Pub/Sub as the message transport between
**soca-engine** (publisher) and **soca-service / soca-control** (subscriber).

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
soca-service (PubSubConsumer thread — REST polling, every 5s)
    │  POST /api/v1/ingest/
    ▼
soca-control → Alert saved to database
```

---

## Prerequisites

- Google Cloud project with Pub/Sub API enabled
- A service account key file (JSON) with Pub/Sub Publisher role (for soca-engine)
- A separate service account key (or the same) with Pub/Sub Subscriber role (for soca-service)

---

## Step 1 — Create Service Account

1. Go to **GCP Console → IAM & Admin → Service Accounts**
2. Click **+ Create Service Account**
   - Name: e.g. `soca-pubsub`
   - ID: `soca-pubsub@<PROJECT>.iam.gserviceaccount.com`
3. Click **Done**
4. Open the service account → **Keys tab → Add Key → Create new key → JSON**
5. Save the downloaded JSON file

---

## Step 2 — Grant IAM Roles

Go to **GCP Console → IAM & Admin → IAM → + Grant Access**:

| Principal | Role | Purpose |
|-----------|------|---------|
| `soca-pubsub@<PROJECT>.iam.gserviceaccount.com` | Pub/Sub Publisher | soca-engine publishes to topic |
| `soca-pubsub@<PROJECT>.iam.gserviceaccount.com` | Pub/Sub Subscriber | soca-service pulls from subscription |

Or grant **Pub/Sub Editor** to cover both in a single binding.

**Via gcloud CLI:**
```bash
gcloud pubsub topics add-iam-policy-binding <TOPIC> \
  --project=<PROJECT> \
  --member="serviceAccount:soca-pubsub@<PROJECT>.iam.gserviceaccount.com" \
  --role="roles/pubsub.publisher"

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
gcloud pubsub topics create soca-jakarta-01 --project=<PROJECT>
gcloud pubsub subscriptions create soca-jakarta-01 \
  --topic=soca-jakarta-01 \
  --project=<PROJECT>
```

---

## Step 4 — Configure soca-engine

### Via soca-dashboard UI (recommended)

1. Go to **soca-dashboard → Settings → Edge Config**
2. Set **Publisher Transport** to `Google Pub/Sub`
3. Fill in **GCP Project ID** and **Pub/Sub Topic**
4. Upload the service account JSON under **Pub/Sub Service Account Key**
5. Click **Save Edge Config** — writes `PUBLISHER_TYPE`, `PUBSUB_PROJECT_ID`, `PUBSUB_TOPIC` to engine `.env`
6. Click **Push Cloud Credentials to soca-engine** — sends the key file to the engine and switches the publisher immediately (no restart needed)

The engine switches from Redis to Pub/Sub live when credentials are pushed. The running process updates in memory — a restart is only needed if you edited `.env` manually.

### Manual (`.env` edit)

```env
PUBLISHER_TYPE=pubsub
PUBSUB_PROJECT_ID=<PROJECT>
PUBSUB_TOPIC=soca-jakarta-01
PUBSUB_KEY_PATH=credentials/pubsub.json    # path to service account JSON
```

> Restart soca-engine after editing `.env` manually.

---

## Step 5 — Configure soca-control Edge

1. Go to **soca-control → Settings → Edges → Edit**
2. Set **Subscriber Type** to `Google Pub/Sub`
3. Fill in:
   - **GCP Project ID**: `<PROJECT>`
   - **Pub/Sub Subscription**: `soca-jakarta-01`
4. **Save**

For each rule that should publish alerts:

1. Go to **soca-dashboard → Schedules → [schedule] → Rules**
2. Enable the **Publish to Queue** action

---

## Step 6 — Configure soca-service

soca-service handles Pub/Sub consumption automatically — no dedicated configuration needed beyond its standard setup.

Upload the Pub/Sub service account key via **soca-control → Settings → soca-service** (or push it via the Push Cloud Credentials button in soca-dashboard). soca-service reads the key path from `config.json` (`pubsub_key_path`).

> **Note:** soca-service does **not** use `GOOGLE_APPLICATION_CREDENTIALS`. It reads the key path from `config.json` which is written when soca-control pushes Pub/Sub credentials.

Both consumers (Redis and Pub/Sub) run simultaneously inside soca-service. Each consumer queries soca-control for its edge list every 30 seconds and self-selects based on `subscriber_type`:
- Edges with `subscriber_type=redis` → `StreamsConsumer`
- Edges with `subscriber_type=pubsub` → `PubSubConsumer`

Switching an edge from Redis to Pub/Sub in soca-control takes effect within 30 seconds — no restart of soca-service required.

---

## Step 7 — Verify End-to-End

**Test manually:**
```bash
python3 -c "
from google.cloud import pubsub_v1
from google.oauth2 import service_account
import time, base64

creds = service_account.Credentials.from_service_account_file(
    '/path/to/pubsub.json',
    scopes=['https://www.googleapis.com/auth/pubsub'])

pub = pubsub_v1.PublisherClient(credentials=creds)
future = pub.publish('projects/<PROJECT>/topics/soca-jakarta-01', b'hello-soca')
print('Published msg id:', future.result())
"
```

**Check soca-service is consuming** — look at the `/health` endpoint:
```
GET http://localhost:8010/health
```
`pubsub.active_edges` should be > 0 and `messages_processed` should increment after a detection.

---

## Troubleshooting

### `403 PERMISSION_DENIED: pubsub.topics.publish`

Missing Publisher role on the topic. See Step 2.

### `403 PERMISSION_DENIED: pubsub.subscriptions.consume`

Missing Subscriber role on the subscription. See Step 2.

### `404 Resource not found`

The subscription does not exist. Create it (Step 3).

### Messages going to DLQ (`soca-engine/dlq/`)

Pub/Sub publish is failing. Check soca-engine logs. Common causes:
- Missing publisher IAM role
- Wrong `PUBSUB_PROJECT_ID` or `PUBSUB_TOPIC`
- `PUBSUB_KEY_PATH` not set or file missing
- Engine not restarted after manual `.env` edit (or Push Cloud Credentials not clicked)

### soca-service not picking up messages (HTTP proxy environment)

soca-service uses REST polling mode (not gRPC streaming) which works correctly through HTTP proxies. Set in soca-service `.env`:

```env
HTTPS_PROXY=http://your-proxy:8080
HTTP_PROXY=http://your-proxy:8080
NO_PROXY=localhost,127.0.0.1
```

Verify REST mode is active in logs:
```
PubSubConsumer starting (REST poll mode)
Pub/Sub REST session created (proxy-friendly)
```

### Engine still sending to Redis after switching to Pub/Sub

If you changed `PUBLISHER_TYPE` in soca-dashboard and saved Edge Config but did **not** click **Push Cloud Credentials to soca-engine**, the change was written to `.env` on disk but the running process still uses Redis. Either:
- Click **Push Cloud Credentials to soca-engine** (switches live immediately), or
- Restart soca-engine (re-reads `.env`)

---

## Real-World Configuration (Bekasi Edge)

| Setting | Value |
|---------|-------|
| GCP Project | `soca-video-analytics-dev` |
| Topic | `soca-snapshot-notification` |
| Subscription | configured per soca-control edge |
| Key file | `credentials/pubsub.json` (written by Push Cloud Credentials) |
| GCS Bucket | `soca-snapshot-bucket` |
| GCS Path Prefix | `edge-mac-mini-bekasi` |

---

## Related Guides

- [GCS Snapshot Storage](gcs-snapshot-storage.md) — configure GCS for snapshot image storage
