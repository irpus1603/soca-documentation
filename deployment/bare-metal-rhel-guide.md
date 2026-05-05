# SOCA Platform — Bare-Metal Deployment Guide (RHEL / Rocky Linux)

This guide covers deploying soca-engine, soca-dashboard, soca-control, and
soca-service directly on a RHEL (or Rocky Linux) server without Docker, using
Python virtualenvs and systemd services.

---

## Architecture on a Single Server

```
Internet / Browser
        │  port 80 (nginx)
        ▼
     nginx  ──► soca-control   (gunicorn, port 8000)
                soca-dashboard (gunicorn, port 8000 on separate vhost or path)
                soca-engine    (uvicorn,  port 8001)
                soca-service   (uvicorn,  port 8080)
```

---

## Prerequisites

```bash
sudo dnf install python3 python3-pip nginx git -y
sudo pip3 install virtualenv
```

Create a shared virtualenv (all services share one to save disk space):

```bash
python3 -m venv /home/ca-admin/Apps/venv
source /home/ca-admin/Apps/venv/bin/activate
```

---

## Directory Layout

```
/home/ca-admin/Apps/
├── venv/               ← shared Python virtualenv
├── soca-control/       ← git clone of soca-control repo
├── soca-service/       ← git clone of soca-service repo
├── soca-engine/        ← git clone / copy of soca-engine
└── soca-dashboard/     ← git clone / copy of soca-dashboard
```

---

## 1. soca-control

### Install

```bash
cd /home/ca-admin/Apps/soca-control
source /home/ca-admin/Apps/venv/bin/activate
pip install -r requirements.txt
```

### Configure

Edit `db.conf` (soca-control uses this instead of `.env`):

```json
{
  "url": "",
  "pg_host": "localhost",
  "pg_port": "5432",
  "pg_name": "myappdb",
  "pg_user": "postgres",
  "pg_password": "yourpassword",
  "gac_path": "/home/ca-admin/Apps/soca-control/credentials/gcs.json",
  "ingest_key": "<generate with: openssl rand -hex 32>",
  "soca_service_url": "http://127.0.0.1:8080",
  "gcs_key_path": "/home/ca-admin/Apps/soca-control/credentials/gcs.json",
  "pubsub_key_path": "/home/ca-admin/Apps/soca-control/credentials/pubsub.json",
  "pubsub_project_id": "<GCP_PROJECT>",
  "pubsub_subscription": "<SUBSCRIPTION_NAME>"
}
```

> Leave `"url": ""` to use SQLite. Set a PostgreSQL URL for production.

### Create `.env`

```bash
cat > /home/ca-admin/Apps/soca-control/.env << 'EOF'
DJANGO_SECRET_KEY=<generate with: python3 -c "import secrets; print(secrets.token_urlsafe(50))">
DEBUG=false
ALLOWED_HOSTS=localhost,127.0.0.1,<SERVER_IP>
CSRF_TRUSTED_ORIGINS=http://<SERVER_IP>
DATABASE_URL=
GCS_BUCKET=<your-bucket>
EOF
```

### Migrate and collect static

```bash
cd /home/ca-admin/Apps/soca-control
source /home/ca-admin/Apps/venv/bin/activate
python manage.py migrate
python manage.py collectstatic --noinput
python manage.py createsuperuser
```

### systemd service

`/etc/systemd/system/soca-control.service`:

```ini
[Unit]
Description=SOCA Control
After=network.target

[Service]
User=ca-admin
WorkingDirectory=/home/ca-admin/Apps/soca-control
Environment=PATH=/home/ca-admin/Apps/venv/bin:/usr/bin:/bin
Environment=HTTPS_PROXY=http://your-proxy:8080
Environment=HTTP_PROXY=http://your-proxy:8080
Environment=NO_PROXY=localhost,127.0.0.1,<SERVER_IP>
ExecStart=/home/ca-admin/Apps/venv/bin/gunicorn soca_control.wsgi:application \
    --bind 127.0.0.1:8000 --workers 3
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now soca-control
```

---

## 2. nginx Reverse Proxy

Install and configure nginx to expose soca-control on port 80:

`/etc/nginx/conf.d/soca-control.conf`:

```nginx
server {
    listen 80;
    server_name _;

    location /static/ {
        alias /home/ca-admin/Apps/soca-control/staticfiles/;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $server_addr;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

> **Important:** Use `proxy_set_header Host $server_addr` (not `$host`) to prevent
> Django `DisallowedHost` errors when browsers send requests with a hostname prefix.

```bash
sudo nginx -t
sudo systemctl enable --now nginx
```

---

## 3. soca-service

### Install

```bash
cd /home/ca-admin/Apps/soca-service
source /home/ca-admin/Apps/venv/bin/activate
pip install -r requirements.txt
```

### Configure `.env`

```env
SOCA_CONTROL_URL=http://127.0.0.1:8000
SOCA_CONTROL_INGEST_KEY=<same value as ingest_key in soca-control db.conf>
PORT=8080
GOOGLE_APPLICATION_CREDENTIALS=/home/ca-admin/Apps/soca-service/credentials/pubsub.json
LOG_LEVEL=INFO
```

> **Note:** Use `http://127.0.0.1:8000` (not the server's external IP) for
> `SOCA_CONTROL_URL`. gunicorn binds to `127.0.0.1` only — external IP won't work.

### Credentials

```bash
mkdir -p /home/ca-admin/Apps/soca-service/credentials
# Copy GCP service account JSON files from your local machine:
# scp pubsub.json ca-admin@<SERVER_IP>:/home/ca-admin/Apps/soca-service/credentials/
```

### systemd service

`/etc/systemd/system/soca-service.service`:

```ini
[Unit]
Description=SOCA Service
After=network.target

[Service]
User=ca-admin
WorkingDirectory=/home/ca-admin/Apps/soca-service
Environment=PATH=/home/ca-admin/Apps/venv/bin:/usr/bin:/bin
Environment=HTTPS_PROXY=http://your-proxy:8080
Environment=HTTP_PROXY=http://your-proxy:8080
Environment=NO_PROXY=localhost,127.0.0.1,<SERVER_IP>
ExecStart=/home/ca-admin/Apps/venv/bin/python -m uvicorn main:app \
    --host 0.0.0.0 --port 8080
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now soca-service
```

---

## 4. soca-engine

### Install

```bash
cd /home/ca-admin/Apps/soca-engine
source /home/ca-admin/Apps/venv/bin/activate
pip install -r requirements.txt
```

### Configure `.env`

```env
EDGE_NAME=edge-1
ENGINE_API_KEY=<generate with: python3 -c "import secrets; print(secrets.token_hex(16))">
PUBLISHER_TYPE=pubsub
PUBSUB_PROJECT_ID=<GCP_PROJECT>
PUBSUB_TOPIC=<TOPIC_NAME>
GOOGLE_APPLICATION_CREDENTIALS=/home/ca-admin/Apps/soca-engine/credentials/pubsub.json
GCS_BUCKET=<your-bucket>
GCS_KEY_PATH=/home/ca-admin/Apps/soca-engine/credentials/gcs.json
GCS_PATH_PREFIX=edge-1
INFER_DEVICE=cpu
```

### systemd service

`/etc/systemd/system/soca-engine.service`:

```ini
[Unit]
Description=SOCA Engine
After=network.target

[Service]
User=ca-admin
WorkingDirectory=/home/ca-admin/Apps/soca-engine
Environment=PATH=/home/ca-admin/Apps/venv/bin:/usr/bin:/bin
Environment=HTTPS_PROXY=http://your-proxy:8080
Environment=HTTP_PROXY=http://your-proxy:8080
Environment=NO_PROXY=localhost,127.0.0.1,<SERVER_IP>
ExecStart=/home/ca-admin/Apps/venv/bin/python -m uvicorn main:app \
    --host 0.0.0.0 --port 8001
Restart=always

[Install]
WantedBy=multi-user.target
```

> **Note:** If soca-engine requires MediaMTX (RTSP relay), start it separately before
> uvicorn. Use a wrapper script:
> ```bash
> # /home/ca-admin/Apps/soca-engine/start.sh
> #!/bin/bash
> cd /home/ca-admin/Apps/soca-engine
> ./mediamtx &
> exec /home/ca-admin/Apps/venv/bin/python -m uvicorn main:app --host 0.0.0.0 --port 8001
> ```
> Then in systemd: `ExecStart=/bin/bash /home/ca-admin/Apps/soca-engine/start.sh`

---

## 5. soca-dashboard

### Install

```bash
cd /home/ca-admin/Apps/soca-dashboard
source /home/ca-admin/Apps/venv/bin/activate
pip install -r requirements.txt gunicorn
python manage.py migrate
python manage.py collectstatic --noinput
```

### systemd service

`/etc/systemd/system/soca-dashboard.service`:

```ini
[Unit]
Description=SOCA Dashboard
After=network.target

[Service]
User=ca-admin
WorkingDirectory=/home/ca-admin/Apps/soca-dashboard
Environment=PATH=/home/ca-admin/Apps/venv/bin:/usr/bin:/bin
Environment=HTTPS_PROXY=http://your-proxy:8080
Environment=HTTP_PROXY=http://your-proxy:8080
Environment=NO_PROXY=localhost,127.0.0.1,<SERVER_IP>
ExecStart=/home/ca-admin/Apps/venv/bin/gunicorn soca_dashboard.wsgi:application \
    --bind 0.0.0.0:8000 --workers 3
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## Firewall

Open required ports:

```bash
sudo firewall-cmd --permanent --add-port=80/tcp      # nginx (soca-control)
sudo firewall-cmd --permanent --add-port=8080/tcp    # soca-dashboard (if on separate port)
sudo firewall-cmd --permanent --add-port=8001/tcp    # soca-engine API (if externally accessible)
sudo firewall-cmd --reload
```

---

## Troubleshooting

### Port 80 access denied

Linux restricts ports < 1024 to root. Use nginx as a reverse proxy (port 80 → 8000)
instead of binding gunicorn directly to port 80.

### systemd ModuleNotFoundError

The venv is not activated in systemd. Set `Environment=PATH` explicitly:
```ini
Environment=PATH=/home/ca-admin/Apps/venv/bin:/usr/bin:/bin
```
And use the full venv path in `ExecStart`:
```ini
ExecStart=/home/ca-admin/Apps/venv/bin/gunicorn ...
```

### DisallowedHost error

nginx is forwarding the Host header with a backslash prefix. Fix with:
```nginx
proxy_set_header Host $server_addr;
```

### soca-service connection refused to soca-control

gunicorn binds to `127.0.0.1:8000` (loopback only) when behind nginx.
Use `SOCA_CONTROL_URL=http://127.0.0.1:8000` — not the server's external IP.

### Pub/Sub messages not received (HTTP proxy environment)

gRPC streaming pull does not respect `HTTPS_PROXY`. soca-service uses REST polling
by default which does work through HTTP proxies. Ensure `HTTPS_PROXY` is set in the
systemd service and `NO_PROXY` includes `localhost,127.0.0.1`.

### GCS credentials pointing to wrong path

soca-control reads credential paths from `db.conf`. After copying credentials to the
server, update `gcs_key_path` and `pubsub_key_path` in `db.conf` to the server paths.
Do not copy `db.conf` from a development machine without updating these paths.

### Verifying services

```bash
# Check all soca services
sudo systemctl status soca-control soca-service soca-engine soca-dashboard

# View live logs
sudo journalctl -u soca-control -f
sudo journalctl -u soca-service -f
sudo journalctl -u soca-engine -f

# Test endpoints
curl http://127.0.0.1:8000          # soca-control (redirect to /login/)
curl http://127.0.0.1:8080/health   # soca-service
curl http://127.0.0.1:8001/health   # soca-engine

# Check soca-service pipeline status
curl http://127.0.0.1:8080/health | python3 -m json.tool
```
