# SOCA Platform — Docker Deployment Guide

## Repository Structure

The platform is split into **3 independent repos** aligned with deployment units,
plus **1 ops repo** that orchestrates them all on a single server.

```
~/projects/
├── soca-edge/          ← Repo 1: edge device  (soca-engine + soca-dashboard)
├── soca-control/       ← Repo 2: cloud server (fleet management dashboard)
├── soca-service/       ← Repo 3: cloud server (alert ingestion microservice)
└── soca-deploy/        ← Repo 4: full-stack orchestration (this server)
```

### Why this split?

| Concern | Benefit |
|---------|---------|
| Different deployment targets | Edge device vs cloud server — independent CI/CD |
| Different release cadence | Edge firmware updates are independent of backend releases |
| Dependency isolation | soca-edge has heavy deps (PyTorch, OpenCV, EasyOCR) — no reason to pull them into backend CI |
| Independent scaling | soca-service can be scaled separately from soca-control |

---

## Deployment Topology

```
┌──────────────────────────────────────────────────────────────┐
│                        YOUR SERVER                           │
│                                                              │
│  ┌────────────────────────────┐   ┌──────────────────────┐  │
│  │  soca-edge  (Repo 1)       │   │  soca-control        │  │
│  │  ┌──────────────────────┐  │   │  (Repo 2)            │  │
│  │  │ soca-dashboard       │  │   │  Django · port 8000  │  │
│  │  │ Django · port 8080   │  │   └──────────┬───────────┘  │
│  │  └──────────────────────┘  │              │ receives      │
│  │  ┌──────────────────────┐  │   ┌──────────┴───────────┐  │
│  │  │ soca-engine          │  │   │  soca-service        │  │
│  │  │ FastAPI · port 8001  │  │   │  (Repo 3)            │  │
│  │  └──────────────────────┘  │   │  FastAPI · port 8010 │  │
│  └─────────────┬──────────────┘   └──────────┬───────────┘  │
│                │ publishes detections          │ consumes     │
│  ┌─────────────┴──────────────────────────────┴───────────┐  │
│  │                   Redis · port 6379                    │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                              │
│  Orchestrated by: soca-deploy/ (Repo 4)                     │
└──────────────────────────────────────────────────────────────┘
```

### Container Summary

| Container | Repo | Ports | Role |
|-----------|------|-------|------|
| `soca-edge` | soca-edge | 8080, 8001 | Edge management + YOLO inference |
| `soca-control` | soca-control | 8000 | Fleet dashboard & alert storage |
| `soca-service` | soca-service | 8010 | Alert ingestion from Redis/Pub/Sub |
| `redis` | (image) | 6379 | Detection stream transport |

---

## Prerequisites

- Docker ≥ 24.0 and Docker Compose v2 (`docker compose`)
- ~4 GB RAM for `soca-edge` (YOLO/PyTorch is memory-intensive)
- Git access to all 4 repos

---

## Repo Contents

### soca-edge/

```
soca-edge/
├── soca-engine/              ← FastAPI inference service
├── soca-dashboard/           ← Django edge management UI
├── docker/
│   └── edge/
│       ├── Dockerfile        ← combined image (supervisord)
│       ├── supervisord.conf  ← manages engine + dashboard processes
│       └── entrypoint.sh     ← runs migrations → starts supervisord
├── docker-compose.yml        ← standalone: edge + redis only
└── .env.example
```

### soca-control/

```
soca-control/
├── app/                      ← Django app
├── soca_control/             ← Django project settings
├── Dockerfile
├── docker-compose.yml        ← standalone: control only
└── .env.example
```

### soca-service/

```
soca-service/
├── consumers/                ← Redis + Pub/Sub consumers
├── main.py                   ← FastAPI app
├── Dockerfile
├── docker-compose.yml        ← standalone: service + redis
└── .env.example
```

### soca-deploy/ (orchestration)

```
soca-deploy/
├── docker-compose.yml        ← full-stack (all 3 repos built from ../sibling-dirs)
└── .env.example
```

---

## Splitting the Git Repos

If you're starting from the original monorepo (`soca-client-server/`), run this
once to split into separate repos:

```bash
# 1. Create the soca-edge repo (contains soca-engine + soca-dashboard + docker/)
mkdir ~/projects/soca-edge
cp -r soca-client-server/soca-engine   ~/projects/soca-edge/
cp -r soca-client-server/soca-dashboard ~/projects/soca-edge/
cp -r soca-client-server/docker         ~/projects/soca-edge/
cp    soca-client-server/docker-compose.yml  ~/projects/soca-edge/
cp    soca-client-server/.env.example        ~/projects/soca-edge/

cd ~/projects/soca-edge && git init && git add . && git commit -m "init: soca-edge repo"

# 2. Promote soca-control to its own repo root
cp -r soca-client-server/soca-control ~/projects/soca-control
cd ~/projects/soca-control && git init && git add . && git commit -m "init: soca-control repo"

# 3. Promote soca-service to its own repo root
cp -r soca-client-server/soca-service ~/projects/soca-service
cd ~/projects/soca-service && git init && git add . && git commit -m "init: soca-service repo"

# 4. Create soca-deploy repo
cp -r soca-client-server/soca-deploy ~/projects/soca-deploy
cd ~/projects/soca-deploy && git init && git add . && git commit -m "init: soca-deploy repo"
```

After splitting, push each repo to your Git hosting (GitHub/GitLab):

```bash
cd ~/projects/soca-edge
git remote add origin git@github.com:your-org/soca-edge.git
git push -u origin main

# Repeat for soca-control, soca-service, soca-deploy
```

---

## Full-Stack Deployment (soca-deploy)

Use `soca-deploy/` to run all services together on one server.

### 1. Clone all repos side by side

```bash
mkdir ~/projects && cd ~/projects
git clone git@github.com:your-org/soca-edge.git
git clone git@github.com:your-org/soca-control.git
git clone git@github.com:your-org/soca-service.git
git clone git@github.com:your-org/soca-deploy.git
```

### 2. Configure environment

Each repo ships its own `.env.example` — copy it to `.env` inside that repo:

| Repo | `.env.example` covers |
|------|-----------------------|
| `soca-deploy/` | All services (edge + control + service) — **use this one** |
| `soca-edge/` | Edge variables only (engine + dashboard) |
| `soca-control/` | soca-control variables only |
| `soca-service/` | soca-service variables only |

For full-stack deployment, you only need one `.env` in `soca-deploy/`:

```bash
cd ~/projects/soca-deploy
cp .env.example .env
nano .env    # fill in all required values
```

Required values:

| Variable | Description | Generate with |
|----------|-------------|---------------|
| `EDGE_NAME` | Unique edge identifier | — |
| `ENGINE_API_KEY` | soca-engine API key | `python -c "import secrets; print(secrets.token_hex(16))"` |
| `DASHBOARD_SECRET_KEY` | Django secret (soca-dashboard) | `python -c "import secrets; print(secrets.token_urlsafe(50))"` |
| `CONTROL_SECRET_KEY` | Django secret (soca-control) | same as above |
| `SOCA_CONTROL_INGEST_KEY` | Shared secret (control ↔ service) | `python -c "import secrets; print(secrets.token_hex(16))"` |

### 3. Build and start

```bash
cd ~/projects/soca-deploy

docker compose build
docker compose up -d

# Verify all containers are running
docker compose ps
```

Expected:
```
NAME             STATUS    PORTS
soca-edge        Up        0.0.0.0:8001->8001, 0.0.0.0:8080->8080
soca-control     Up        0.0.0.0:8000->8000
soca-service     Up        0.0.0.0:8010->8010
soca-redis       Up        0.0.0.0:6379->6379
```

### 4. Create admin accounts

```bash
# soca-dashboard admin
docker exec -it soca-edge bash -c "cd /app/soca-dashboard && python manage.py createsuperuser"

# soca-control admin
docker exec -it soca-control python manage.py createsuperuser
```

### 5. Verify health

```bash
curl http://<edge-ip>:8001/health    # soca-engine
curl http://<soca-control-ip>:8010/health    # soca-service
curl -I http://<edge-ip>:8080/       # soca-dashboard
curl -I http://<soca-control-ip>:8000/       # soca-control
```

---

## Standalone Deployment (per-repo)

Each repo can also be deployed independently. Useful for running only one service,
or when soca-control and soca-edge are on different machines.

### soca-edge standalone

Uses `soca-edge/.env.example` (edge variables only: EDGE_NAME, ENGINE_API_KEY,
PUBLISHER_TYPE, GCS settings, inference settings, DASHBOARD_SECRET_KEY).

```bash
cd ~/projects/soca-edge
cp .env.example .env    # source: soca-edge/.env.example
nano .env
docker compose build
docker compose up -d
```

Starts: soca-edge (8080, 8001) + redis (6379)

### soca-control standalone

Uses `soca-control/.env.example` (Django secret, DATABASE_URL, ALLOWED_HOSTS, etc.).

```bash
cd ~/projects/soca-control
cp .env.example .env    # source: soca-control/.env.example
nano .env
docker compose build
docker compose up -d
```

Starts: soca-control (8000) only. Set `SOCA_CONTROL_URL` in soca-service .env to
point at this server's IP/domain.

### soca-service standalone

Uses `soca-service/.env.example` (SOCA_CONTROL_URL, SOCA_CONTROL_INGEST_KEY).

```bash
cd ~/projects/soca-service
cp .env.example .env    # source: soca-service/.env.example
# Set SOCA_CONTROL_URL to your soca-control address, e.g.:
# SOCA_CONTROL_URL=http://your-server-ip:8000
nano .env
docker compose build
docker compose up -d
```

Starts: soca-service (8010) + redis (6379)

---

## Post-Deployment Configuration

### Configure soca-dashboard Edge Settings

1. Open `http://<server>:8080` → log in
2. Go to **Settings → Edge**
3. Set these paths (inside the container):

| Field | Value |
|-------|-------|
| Engine URL | `http://<edge-ip>:8001` |
| Engine API Key | your `ENGINE_API_KEY` |
| Engine DB path | `/app/data/soca_engine.db` |
| Snapshots dir | `/app/soca-engine/snapshots` |
| Engine .env path | `/app/soca-engine/.env` |

### Configure soca-control

1. Open `http://<server>:8000` → log in
2. Go to **Settings → Edges** → Add edge with:

| Field | Value |
|-------|-------|
| Edge URL | `http://soca-edge:8080` (Docker network) |
| Engine URL | `http://soca-edge:8001` (Docker network) |
| Engine API Key | your `ENGINE_API_KEY` |

3. Go to **Settings → Service Ingest Key** → click **Push to soca-service**
   (this sends `SOCA_CONTROL_INGEST_KEY` to soca-service via bootstrap API)

---

## Upload YOLO Models

Models persist in the `edge-models` Docker volume (`/app/soca-engine/yolo/`).

**Via soca-dashboard UI:**
→ `http://<server>:8080` → **Settings → AI Models** → Upload `.pt`, `.onnx`, `.yaml`

**Via CLI (copy from host):**
```bash
docker cp your-model.pt soca-edge:/app/soca-engine/yolo/
```

---

## Environment Variables Reference

### soca-edge

| Variable | Default | Description |
|----------|---------|-------------|
| `EDGE_NAME` | *(required)* | Unique edge identifier |
| `ENGINE_API_KEY` | *(required)* | soca-engine API key |
| `REDIS_URL` | `redis://redis:6379` | Set automatically in compose |
| `PUBLISHER_TYPE` | `redis` | `redis` or `pubsub` |
| `GCS_BUCKET` | — | GCS bucket (blank = no upload) |
| `GCS_KEY_PATH` | — | GCS service account JSON path |
| `PUBSUB_PROJECT_ID` | — | GCP project (Pub/Sub only) |
| `PUBSUB_TOPIC` | `soca-detections` | Pub/Sub topic |
| `INFER_DEVICE` | `cpu` | `cpu` or `cuda:0` |
| `MAX_CONCURRENT_JOBS` | `4` | Parallel inference jobs |
| `DB_PATH` | `/app/data/soca_engine.db` | Engine SQLite path |
| `SECRET_KEY` | *(required)* | Django secret (soca-dashboard) |
| `DEBUG` | `false` | Django debug mode |

### soca-control

| Variable | Default | Description |
|----------|---------|-------------|
| `DJANGO_SECRET_KEY` | *(required)* | Django secret key |
| `DEBUG` | `false` | Django debug mode |
| `DATABASE_URL` | SQLite | PostgreSQL URL (production) |
| `CSRF_TRUSTED_ORIGINS` | `http://<soca-control-ip>:8000` | CSRF trusted origins |

### soca-service

| Variable | Default | Description |
|----------|---------|-------------|
| `SOCA_CONTROL_URL` | `http://soca-control:8000` | Auto-set in compose |
| `SOCA_CONTROL_INGEST_KEY` | *(required)* | Shared auth secret |
| `REDIS_URL` | `redis://redis:6379` | Auto-set in compose |

---

## Volumes

| Volume | Mount point | Contents |
|--------|-------------|----------|
| `edge-models` | `/app/soca-engine/yolo` | YOLO models — **persistent** |
| `edge-snapshots` | `/app/soca-engine/snapshots` | Detection snapshots |
| `edge-dlq` | `/app/soca-engine/dlq` | Failed upload queue |
| `edge-data` | `/app/data` | Engine SQLite DB, config.json |
| `redis-data` | `/data` | Redis persistence |
| `control-db` | `/app/db` | soca-control SQLite (dev only) |

---

## Networking

All containers share `soca-net`. Internal Docker hostnames:

| Container | Hostname | Ports |
|-----------|----------|-------|
| soca-edge | `soca-edge` | 8001, 8080 |
| soca-control | `soca-control` | 8000 |
| soca-service | `soca-service` | 8010 |
| Redis | `redis` | 6379 |

Cross-container calls:
- soca-dashboard → soca-engine: `http://<edge-ip>:8001` (same container)
- soca-control → soca-engine: `http://soca-edge:8001`
- soca-service → soca-control: `http://soca-control:8000`

### Firewall Recommendations

| Port | Expose publicly? |
|------|-----------------|
| 8080 (soca-dashboard) | Yes — edge operators |
| 8000 (soca-control) | Yes — fleet admins |
| 8001 (soca-engine API) | Internal only |
| 8010 (soca-service) | Internal only |
| 6379 (Redis) | Internal only |

---

## Updating Services

```bash
cd ~/projects/soca-deploy

# Pull latest code for each service
cd ../soca-edge     && git pull && cd ../soca-deploy
cd ../soca-control  && git pull && cd ../soca-deploy
cd ../soca-service  && git pull && cd ../soca-deploy

# Rebuild changed images and restart (zero-downtime for unchanged services)
docker compose build
docker compose up -d

# Run migrations after soca-control update
docker exec soca-control python manage.py migrate --noinput

# Run migrations after soca-dashboard update
docker exec soca-edge bash -c "cd /app/soca-dashboard && python manage.py migrate --noinput"
```

---

## GPU Support (Optional)

To enable GPU inference on soca-edge, add to `soca-deploy/docker-compose.yml`
under the `soca-edge` service:

```yaml
soca-edge:
  deploy:
    resources:
      reservations:
        devices:
          - driver: nvidia
            count: 1
            capabilities: [gpu]
  environment:
    INFER_DEVICE: cuda:0
```

Requires [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) on the host.

---

## Useful Commands

```bash
# View logs (all services)
docker compose logs -f

# View logs for a specific service
docker compose logs -f soca-edge
docker compose logs -f soca-control

# View process logs inside soca-edge
docker exec soca-edge tail -f /var/log/supervisor/soca-engine.log
docker exec soca-edge tail -f /var/log/supervisor/soca-dashboard.log

# Restart a single service
docker compose restart soca-edge

# Rebuild only one service (after code change)
docker compose build soca-control
docker compose up -d --no-deps soca-control

# Open a shell
docker exec -it soca-edge bash
docker exec -it soca-control bash

# Django management
docker exec -it soca-control python manage.py shell
docker exec -it soca-edge bash -c "cd /app/soca-dashboard && python manage.py shell"

# Back up the engine SQLite database
docker run --rm \
  -v soca-deploy_edge-data:/data \
  -v $(pwd):/backup alpine \
  cp /data/soca_engine.db /backup/soca_engine_$(date +%Y%m%d).db
```

---

## Troubleshooting

### Container exits immediately

```bash
docker compose logs soca-edge    # check error output
```

Common causes:
- `EDGE_NAME` not set → engine raises `RuntimeError`
- `ENGINE_API_KEY` not set → engine starts but returns 503 on API calls
- `DASHBOARD_SECRET_KEY` or `CONTROL_SECRET_KEY` not set → Django refuses to start

### soca-control CSRF / 403 errors

Add your domain to `.env`:
```env
CSRF_TRUSTED_ORIGINS=https://yourdomain.com,http://<soca-control-ip>:8000
```
Then `docker compose up -d` to apply.

### Models not showing in AI Models tab

1. Verify `ENGINE_API_KEY` matches between soca-dashboard settings and `.env`
2. Test directly: `curl -H "Authorization: Bearer <key>" http://<edge-ip>:8001/models`
3. Restart soca-edge after any engine code change: `docker compose restart soca-edge`

### soca-service not consuming alerts

1. Ensure `SOCA_CONTROL_INGEST_KEY` is set and matches soca-control
2. Push the key: soca-control UI → **Settings → Service Ingest Key → Push**
3. Check: `docker compose logs soca-service`

### Build fails (missing library)

soca-edge requires system libraries for OpenCV. If the build fails:
```bash
docker compose build --no-cache soca-edge
```
