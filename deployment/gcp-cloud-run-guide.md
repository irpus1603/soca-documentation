# soca-control — GCP Cloud Run Deployment Guide

## Architecture

### Option A — Redis Stream transport (default)

```
                    ┌────────────────────────────────────────────────────┐
                    │                  Google Cloud                       │
                    │                                                     │
  Browser ──HTTPS──►│  Cloud Run: soca-control-web                        │
                    │  (gunicorn, min 1 instance)                         │
                    │           │ Cloud SQL Auth Proxy                    │
                    │           ▼                                         │
                    │  Cloud SQL (PostgreSQL)                              │
                    │           ▲                                         │
                    │           │ Cloud SQL Auth Proxy                    │
                    │  Cloud Run: soca-control-worker                     │
                    │  (manage.py consume_streams)                        │
                    │           │ VPC connector                           │
                    │           ▼                                         │
                    │  Cloud Memorystore (Redis 7, private IP)            │
                    │                                                     │
                    │  Compute Engine: redis-proxy (e2-micro, public IP)  │
                    │           │ rinetd 0.0.0.0:6379 → Memorystore:6379  │
                    └───────────┼────────────────────────────────────────┘
                                ▲
                                │ each edge pushes detection events
                                │ redis://PROXY_PUBLIC_IP:6379
                    On-premises edge devices
                    soca-engine → XADD → edge-NAME:soca:detections
```

### Option B — Google Pub/Sub transport

```
                    ┌────────────────────────────────────────────────────┐
                    │                  Google Cloud                       │
                    │                                                     │
  Browser ──HTTPS──►│  Cloud Run: soca-control-web                        │
                    │           │ Cloud SQL Auth Proxy                    │
                    │           ▼                                         │
                    │  Cloud SQL (PostgreSQL)                              │
                    │           ▲                                         │
                    │           │ Cloud SQL Auth Proxy                    │
                    │  Cloud Run: soca-control-pubsub-worker              │
                    │  (manage.py consume_pubsub)                         │
                    │           │ streaming pull                          │
                    │           ▼                                         │
                    │  Cloud Pub/Sub topic: soca-detections               │
                    │           ▲                                         │
                    └───────────┼────────────────────────────────────────┘
                                │ each edge publishes detection events
                                │ (GOOGLE_APPLICATION_CREDENTIALS or ADC)
                    On-premises edge devices
                    soca-engine → Pub/Sub publish → projects/PROJECT/topics/soca-detections
```

**Pub/Sub advantages over Redis:** no proxy VM required, no VPC connector needed, fully managed delivery, dead-letter support, scales to any number of edges.

**Two Cloud Run services, one Docker image (Redis path):**
- `soca-control-web` — HTTP server (gunicorn via `start.sh`)
- `soca-control-worker` — always-on stream consumer (`manage.py consume_streams`)

**Three Cloud Run services for Pub/Sub path:**
- `soca-control-web` — HTTP server
- `soca-control-pubsub-worker` — Pub/Sub subscriber (`manage.py consume_pubsub`)
- Both workers can run simultaneously if you have a mix of Redis and Pub/Sub edges.

**Stream naming (Redis):** All edges share the same Redis (Memorystore) but use unique stream names so `consume_streams` can distinguish them:

| Edge | `redis_url` | `redis_stream` |
|---|---|---|
| edge-jakarta-01 | `redis://MEMORYSTORE_IP:6379` | `edge-jakarta-01:soca:detections` |
| edge-bali-02 | `redis://MEMORYSTORE_IP:6379` | `edge-bali-02:soca:detections` |

---

## Prerequisites

- `gcloud` CLI installed and authenticated (`gcloud auth login`)
- GCP project created with billing enabled
- Docker installed locally (for the first manual build)

---

## Step 1 — Set project and enable APIs

```bash
gcloud config set project YOUR_PROJECT_ID

gcloud services enable \
  run.googleapis.com \
  sqladmin.googleapis.com \
  artifactregistry.googleapis.com \
  cloudbuild.googleapis.com \
  secretmanager.googleapis.com \
  storage.googleapis.com \
  redis.googleapis.com \
  vpcaccess.googleapis.com \
  compute.googleapis.com
```

---

## Step 2 — Create VPC and Serverless VPC connector

Cloud Memorystore only has a private VPC IP. Cloud Run needs a VPC connector to reach it.

```bash
# Create a VPC (skip if you want to reuse the default VPC)
gcloud compute networks create soca-vpc --subnet-mode=auto

# Create the connector (Cloud Run ↔ VPC)
gcloud compute networks vpc-access connectors create soca-connector \
  --region=asia-southeast2 \
  --network=soca-vpc \
  --range=10.8.0.0/28 \
  --min-instances=2 \
  --max-instances=3
```

---

## Step 3 — Create Cloud Memorystore (Redis)

```bash
gcloud redis instances create soca-redis \
  --size=1 \
  --region=asia-southeast1 \
  --network=soca-vpc \
  --redis-version=redis_7_0 \
  --tier=basic
```

> **basic** tier is single-node (cheapest). Use `--tier=standard` for HA with automatic failover.

Get the private IP — you'll need it later:

```bash
gcloud redis instances describe soca-redis \
  --region=asia-southeast2 \
  --format="value(host)" 
# get IP <memorystore-ip>
# e.g. <memorystore-ip>  →  save this as MEMORYSTORE_IP
```

---

## Step 4 — Expose Redis to edge devices (proxy VM)

Memorystore has no public IP. Run a tiny Compute Engine VM as a TCP proxy so on-premises edge devices can push their detection events to it.

```bash
# Create e2-micro VM in the same VPC (free tier eligible)
gcloud compute instances create redis-proxy \
  --zone=asia-southeast1-b \
  --machine-type=e2-micro \
  --network=soca-vpc \
  --subnet=soca-vpc \
  --tags=redis-proxy \
  --image-family=debian-12 \
  --image-project=debian-cloud

# Allow port 6379 only from your edge device public IPs
gcloud compute firewall-rules create allow-redis-from-edges \
  --network=soca-vpc \
  --allow=tcp:6379 \
  --source-ranges=EDGE_PUBLIC_IP_1/32,EDGE_PUBLIC_IP_2/32 \
  --target-tags=redis-proxy
```

SSH into the VM and install `rinetd` as a TCP forwarder:

```bash
gcloud compute ssh redis-proxy --zone=asia-southeast1-b

# On the VM:
sudo apt-get update && sudo apt-get install -y rinetd
echo "0.0.0.0 6379 MEMORYSTORE_IP 6379" | sudo tee /etc/rinetd.conf
sudo systemctl enable rinetd && sudo systemctl restart rinetd
exit
```

Get the proxy VM's external IP:

```bash
gcloud compute instances describe redis-proxy \
  --zone=asia-southeast1-b \
  --format="value(networkInterfaces[0].accessConfigs[0].natIP)"
# e.g. 34.101.x.x  →  save as PROXY_PUBLIC_IP
```

Edge devices will connect to `redis://PROXY_PUBLIC_IP:6379`.

---

## Step 5 — Create Artifact Registry repository

```bash
gcloud artifacts repositories create soca \
  --repository-format=docker \
  --location=asia-southeast1 \
  --description="SOCA container images"
```

---

## Step 6 — Create Cloud SQL (PostgreSQL)

```bash
gcloud sql instances create soca-control-db \
  --database-version=POSTGRES_16 \
  --tier=db-f1-micro \
  --region=asia-southeast1 \
  --storage-auto-increase \
  --backup-start-time=02:00

gcloud sql databases create soca_control --instance=soca-control-db

gcloud sql users create soca_user \
  --instance=soca-control-db \
  --password=CHOOSE_A_STRONG_PASSWORD
```

Connection string (save for Step 7):
```
postgres://soca_user:CHOOSE_A_STRONG_PASSWORD@/soca_control?host=/cloudsql/YOUR_PROJECT_ID:asia-southeast1:soca-control-db
```

---

## Step 7 — Create GCS bucket for media (logos)

```bash
gcloud storage buckets create gs://soca-control-media-YOUR_PROJECT_ID \
  --location=asia-southeast1 \
  --uniform-bucket-level-access

# Allow public read so logo images are accessible in the browser
gcloud storage buckets add-iam-policy-binding \
  gs://soca-control-media-YOUR_PROJECT_ID \
  --member=allUsers \
  --role=roles/storage.objectViewer
```

---

## Step 8 — Store secrets in Secret Manager

```bash
# Generate a strong random Django secret key
echo -n "$(python3 -c "import secrets; print(secrets.token_urlsafe(50))")" | \
  gcloud secrets create soca-control-secret-key --data-file=-

# Database URL (Cloud SQL socket format)
echo -n "postgres://soca_user:CHOOSE_A_STRONG_PASSWORD@/soca_control?host=/cloudsql/YOUR_PROJECT_ID:asia-southeast1:soca-control-db" | \
  gcloud secrets create soca-control-db-url --data-file=-
```

---

## Step 9 — Grant service account permissions

```bash
SA="$(gcloud projects describe YOUR_PROJECT_ID --format='value(projectNumber)')-compute@developer.gserviceaccount.com"

# Cloud SQL
gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
  --member="serviceAccount:$SA" --role=roles/cloudsql.client

# Secret Manager
gcloud secrets add-iam-policy-binding soca-control-secret-key \
  --member="serviceAccount:$SA" --role=roles/secretmanager.secretAccessor
gcloud secrets add-iam-policy-binding soca-control-db-url \
  --member="serviceAccount:$SA" --role=roles/secretmanager.secretAccessor

# GCS
gcloud storage buckets add-iam-policy-binding \
  gs://soca-control-media-YOUR_PROJECT_ID \
  --member="serviceAccount:$SA" --role=roles/storage.objectAdmin
```

---

## Step 9b — (Option B) Set up Google Pub/Sub

Skip this step if you are using Redis transport only.

### Create topic and subscription

```bash
# Create the topic soca-engine publishes to
gcloud pubsub topics create soca-detections --project=YOUR_PROJECT_ID

# Create a pull subscription for soca-control-pubsub-worker
gcloud pubsub subscriptions create soca-detections-sub \
  --topic=soca-detections \
  --project=YOUR_PROJECT_ID \
  --ack-deadline=60 \
  --message-retention-duration=7d
```

> Use one topic per project. Multiple edges publish to the same topic; the `edge_name` field inside each message identifies the source.

### Grant IAM permissions

```bash
SA="$(gcloud projects describe YOUR_PROJECT_ID --format='value(projectNumber)')-compute@developer.gserviceaccount.com"

# soca-control-pubsub-worker needs to subscribe (pull + ack)
gcloud pubsub subscriptions add-iam-policy-binding soca-detections-sub \
  --member="serviceAccount:$SA" --role=roles/pubsub.subscriber

# soca-engine on each edge device needs to publish
# (use a separate service account for edge devices in production)
gcloud pubsub topics add-iam-policy-binding soca-detections \
  --member="serviceAccount:$SA" --role=roles/pubsub.publisher
```

### Configure soca-engine on each edge device

Set these env vars in `soca-engine/.env` (or via soca-dashboard Settings → Edge Config → Publisher Transport):

```env
PUBLISHER_TYPE=pubsub
PUBSUB_PROJECT_ID=YOUR_PROJECT_ID
PUBSUB_TOPIC=soca-detections
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json
```

### Configure soca-control edges

In soca-control Settings → Edit Edge, set:
- **Subscriber Transport**: Google Pub/Sub
- **GCP Project ID**: `YOUR_PROJECT_ID`
- **Pub/Sub Subscription**: `soca-detections-sub`

---

## Step 10 — Build and push Docker image

```bash
gcloud auth configure-docker asia-southeast1-docker.pkg.dev

docker build -t asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest .
docker push asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest
```

---

## Step 11 — Deploy Cloud Run services

### Web service

The web service only connects to Cloud SQL and GCS. It does **not** talk to Redis, so no VPC connector is needed here.

```bash
gcloud run deploy soca-control-web \
  --image=asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest \
  --region=asia-southeast1 \
  --platform=managed \
  --allow-unauthenticated \
  --min-instances=1 \
  --max-instances=4 \
  --command="./start.sh" \
  --update-secrets="DJANGO_SECRET_KEY=soca-control-secret-key:latest,DATABASE_URL=soca-control-db-url:latest" \
  --set-env-vars="GCS_BUCKET=soca-control-media-YOUR_PROJECT_ID,ALLOWED_HOSTS=YOUR_SERVICE_URL_HOST,CSRF_TRUSTED_ORIGINS=https://YOUR_SERVICE_URL" \
  --add-cloudsql-instances=YOUR_PROJECT_ID:asia-southeast1:soca-control-db
```

After the first deploy you'll get the service URL (e.g. `https://soca-control-web-xxxx-as.a.run.app`).
Update `ALLOWED_HOSTS` and `CSRF_TRUSTED_ORIGINS` with that URL and redeploy once.

### Worker service

```bash
gcloud run deploy soca-control-worker \
  --image=asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest \
  --region=asia-southeast1 \
  --platform=managed \
  --no-allow-unauthenticated \
  --min-instances=1 \
  --max-instances=1 \
  --command="python" \
  --args="manage.py,consume_streams" \
  --vpc-connector=soca-connector \
  --vpc-egress=private-ranges-only \
  --update-secrets="DJANGO_SECRET_KEY=soca-control-secret-key:latest,DATABASE_URL=soca-control-db-url:latest" \
  --set-env-vars="GCS_BUCKET=soca-control-media-YOUR_PROJECT_ID" \
  --add-cloudsql-instances=YOUR_PROJECT_ID:asia-southeast1:soca-control-db
```

> `--min-instances=1` is required on the worker so it always runs and never cold-starts. Cloud Run will keep one instance alive continuously.

### Pub/Sub worker service (Option B)

No VPC connector is needed — Pub/Sub is a public GCP endpoint.

```bash
gcloud run deploy soca-control-pubsub-worker \
  --image=asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest \
  --region=asia-southeast1 \
  --platform=managed \
  --no-allow-unauthenticated \
  --min-instances=1 \
  --max-instances=1 \
  --command="python" \
  --args="manage.py,consume_pubsub" \
  --update-secrets="DJANGO_SECRET_KEY=soca-control-secret-key:latest,DATABASE_URL=soca-control-db-url:latest" \
  --set-env-vars="GCS_BUCKET=soca-control-media-YOUR_PROJECT_ID" \
  --add-cloudsql-instances=YOUR_PROJECT_ID:asia-southeast1:soca-control-db
```

> You can run both `soca-control-worker` (Redis) and `soca-control-pubsub-worker` (Pub/Sub) simultaneously if you have a mix of edge transport types.

---

## Step 12 — Create superuser

```bash
gcloud run jobs create soca-control-setup \
  --image=asia-southeast1-docker.pkg.dev/YOUR_PROJECT_ID/soca/soca-control:latest \
  --region=asia-southeast1 \
  --command="python" \
  --args="manage.py,createsuperuser,--noinput" \
  --vpc-connector=soca-connector \
  --set-env-vars="DJANGO_SUPERUSER_USERNAME=admin,DJANGO_SUPERUSER_EMAIL=admin@example.com,DJANGO_SUPERUSER_PASSWORD=CHANGE_ME" \
  --update-secrets="DJANGO_SECRET_KEY=soca-control-secret-key:latest,DATABASE_URL=soca-control-db-url:latest" \
  --add-cloudsql-instances=YOUR_PROJECT_ID:asia-southeast1:soca-control-db

gcloud run jobs execute soca-control-setup --region=asia-southeast1 --wait
```

---

## Step 13 — Configure edges

### soca-control (Settings page)

**Redis transport:** Register each edge with:
- **Redis URL**: `redis://MEMORYSTORE_IP:6379` ← private Memorystore IP; the worker reaches it via VPC connector
- **Stream Name**: `edge-NAME:soca:detections` (unique per edge)
- **Subscriber Transport**: Redis Stream

Only the **worker** service uses this Redis URL. The web service does not connect to Redis.

**Pub/Sub transport:** Register each edge with:
- **Subscriber Transport**: Google Pub/Sub
- **GCP Project ID**: your GCP project ID
- **Pub/Sub Subscription**: `soca-detections-sub` (or per-edge subscriptions)

### soca-engine on each edge device

**Redis transport** — Update `.env` so each engine pushes to GCP Redis via the proxy VM:

```env
PUBLISHER_TYPE=redis
REDIS_URL=redis://PROXY_PUBLIC_IP:6379
REDIS_STREAM=edge-NAME:soca:detections
```

**Pub/Sub transport** — Update `.env` so each engine publishes to Pub/Sub:

```env
PUBLISHER_TYPE=pubsub
PUBSUB_PROJECT_ID=YOUR_PROJECT_ID
PUBSUB_TOPIC=soca-detections
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json
```

These values can also be set via soca-dashboard Settings → Edge Config → Publisher Transport and will be written automatically to `soca-engine/.env` on Save.

Replace `edge-NAME` with the same identifier used in soca-control (e.g. `edge-jakarta-01`).

---

## Step 14 — (Optional) CI/CD with Cloud Build

```bash
gcloud builds submit \
  --config=cloudbuild.yaml \
  --substitutions=\
_REGION=asia-southeast1,\
_REPO=soca,\
_GCS_BUCKET=soca-control-media-YOUR_PROJECT_ID,\
_ALLOWED_HOSTS=soca-control-web-xxxx-as.a.run.app,\
_CSRF_ORIGINS=https://soca-control-web-xxxx-as.a.run.app
```

For automated deploys, create a Cloud Build trigger on the `main` branch using the same substitutions.

---

## Useful commands

```bash
# View logs
gcloud run services logs read soca-control-web   --region=asia-southeast1 --limit=50
gcloud run services logs read soca-control-worker --region=asia-southeast1 --limit=50

# Update an env var without full redeploy
gcloud run services update soca-control-web \
  --region=asia-southeast1 --update-env-vars=DEBUG=false

# Roll back to a previous revision
gcloud run services update-traffic soca-control-web \
  --region=asia-southeast1 --to-revisions=PREV_REVISION=100

# Test Redis connectivity from the proxy VM
gcloud compute ssh redis-proxy --zone=asia-southeast1-b
redis-cli -h MEMORYSTORE_IP ping
```
