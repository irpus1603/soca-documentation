# Deploying the SOCA Documentation Site

This guide covers how to build and serve the MkDocs documentation site on a production server.

---

## Prerequisites

Install the required Python packages on the build machine:

```bash
pip install -r requirements-docs.txt
```

`requirements-docs.txt` installs:

| Package | Version | Purpose |
|---------|---------|---------|
| `mkdocs` | ≥ 1.6 | Static site builder |
| `mkdocs-material` | ≥ 9.5 | Theme with tabs, search, Mermaid support |
| `pymdown-extensions` | ≥ 10.0 | Admonitions, code highlighting, Mermaid fences |

---

## Option 1 — Nginx on a Linux Server

### 1. Build the static site

```bash
cd /path/to/soca-documentation
mkdocs build
```

Output is written to the `site/` folder — a fully static HTML site, no server-side runtime needed.

### 2. Copy files to the server

```bash
rsync -avz site/ user@your-server:/var/www/soca-docs/
```

Or use `scp`:

```bash
scp -r site/ user@your-server:/var/www/soca-docs/
```

### 3. Configure Nginx

Create `/etc/nginx/sites-available/soca-docs`:

```nginx
server {
    listen 80;
    server_name docs.yourdomain.com;

    root /var/www/soca-docs;
    index index.html;

    location / {
        try_files $uri $uri/ $uri.html =404;
    }

    # Cache static assets
    location ~* \.(css|js|png|jpg|svg|woff2)$ {
        expires 30d;
        add_header Cache-Control "public, no-transform";
    }
}
```

Enable the site and reload:

```bash
sudo ln -s /etc/nginx/sites-available/soca-docs /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### 4. (Optional) Enable HTTPS with Certbot

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d docs.yourdomain.com
```

---

## Option 2 — Docker

### 1. Create a `Dockerfile` in the project root

```dockerfile
FROM python:3.11-slim AS builder
WORKDIR /docs
COPY requirements-docs.txt .
RUN pip install --no-cache-dir -r requirements-docs.txt
COPY . .
RUN mkdocs build

FROM nginx:alpine
COPY --from=builder /docs/site /usr/share/nginx/html
EXPOSE 80
```

### 2. Build and run

```bash
# Build the image
docker build -t soca-docs .

# Run on port 8090 (avoids conflict with soca-control on 8000)
docker run -d -p 8090:80 --name soca-docs soca-docs
```

Access at `http://your-server:8090`

### 3. Docker Compose (recommended)

Create `docker-compose.docs.yml`:

```yaml
services:
  soca-docs:
    build: .
    ports:
      - "8090:80"
    restart: unless-stopped
```

```bash
docker compose -f docker-compose.docs.yml up -d
```

---

## Option 3 — GitHub Pages (free, zero-server)

### 1. Add the `mike` plugin for versioning (optional)

```bash
pip install mike
```

### 2. Deploy with the built-in MkDocs command

```bash
mkdocs gh-deploy --force
```

This builds the site and pushes it to the `gh-pages` branch automatically. Enable GitHub Pages in your repository settings pointing to the `gh-pages` branch.

!!! tip
    Set `site_url` in `mkdocs.yml` to your GitHub Pages URL before deploying:
    ```yaml
    site_url: https://your-org.github.io/soca-documentation
    ```

---

## Option 4 — GCP Cloud Run (serverless)

### 1. Build and push the Docker image to Artifact Registry

```bash
PROJECT_ID=your-gcp-project
IMAGE=asia-southeast2-docker.pkg.dev/$PROJECT_ID/soca/soca-docs

docker build -t $IMAGE .
docker push $IMAGE
```

### 2. Deploy to Cloud Run

```bash
gcloud run deploy soca-docs \
  --image $IMAGE \
  --platform managed \
  --region asia-southeast2 \
  --allow-unauthenticated \
  --port 80
```

Cloud Run serves the docs at a generated HTTPS URL. Use a custom domain mapping for a clean URL.

---

## Local Preview (development)

To preview the docs locally before deploying:

```bash
mkdocs serve --dev-addr 127.0.0.1:8001
```

!!! warning "Port conflict"
    Port `8000` is used by soca-control. Always use `--dev-addr 127.0.0.1:8001` or another free port.

Open `http://127.0.0.1:8001` in your browser. The site hot-reloads on every file save.

---

## Updating the Site

Whenever documentation is updated, rebuild and redeploy:

=== "Nginx"
    ```bash
    mkdocs build
    rsync -avz site/ user@your-server:/var/www/soca-docs/
    ```

=== "Docker"
    ```bash
    docker build -t soca-docs .
    docker compose -f docker-compose.docs.yml up -d --build
    ```

=== "GitHub Pages"
    ```bash
    mkdocs gh-deploy --force
    ```

=== "Cloud Run"
    ```bash
    docker build -t $IMAGE . && docker push $IMAGE
    gcloud run deploy soca-docs --image $IMAGE --region asia-southeast2
    ```

---

## Troubleshooting

| Problem | Cause | Fix |
|---------|-------|-----|
| `mkdocs build` fails with missing file | A page listed in `nav:` doesn't exist | Check `mkdocs.yml` nav entries match actual files in `docs/` |
| Mermaid diagrams not rendering | Missing superfences config | Verify `pymdownx.superfences` has the `mermaid` custom fence in `mkdocs.yml` |
| Nginx returns 404 on refresh | Missing `try_files` with `$uri.html` | Add `try_files $uri $uri/ $uri.html =404;` to the nginx location block |
| Images not showing | Wrong relative path | All screenshots must be under `docs/` and referenced with a relative path from the markdown file |
| Docker build slow | Rebuilding dependencies each time | Use multi-stage build (as shown above) — pip install only reruns when `requirements-docs.txt` changes |
