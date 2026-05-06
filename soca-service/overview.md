# soca-service — Alert Consumer Microservice

## Overview

`soca-service` is a standalone FastAPI microservice that consumes detection alerts from soca-engine (via Redis streams or Google Pub/Sub) and forwards them to soca-control via HTTP. It runs both consumer types simultaneously and self-selects which edges to handle based on each edge's `subscriber_type` in soca-control.

---

## Why a Separate Microservice?

- **Separation of concerns** — soca-control is a pure web app; soca-service is a pure consumer
- **Independent scaling** — run 1 to N replicas without touching the web tier
- **Fault isolation** — consumer crashes do not affect the soca-control web UI
- **Deployment flexibility** — can run on a different host or container

---

## Architecture

```
soca-engine ──Redis XADD──────────────────┐
                                           ▼
                              soca-service (FastAPI, port 8010)
                                  │  StreamsConsumer thread  (subscriber_type=redis edges)
soca-engine ──Pub/Sub publish──►  │  PubSubConsumer thread   (subscriber_type=pubsub edges)
                                  │
                                  │ POST /api/v1/ingest/
                                  ▼
                              soca-control (Django, port 8000)
```

---

## Consumer Behavior

Both consumers start at boot and run permanently. Every 30 seconds each consumer calls `GET /api/v1/edges/` from soca-control and filters by `subscriber_type`:

- `StreamsConsumer` handles edges where `subscriber_type == "redis"`
- `PubSubConsumer` handles edges where `subscriber_type == "pubsub"`

Switching an edge's transport type in soca-control takes effect within 30 seconds — no restart of soca-service is needed.

### Redis Consumer (StreamsConsumer)

- Uses **Redis Consumer Groups** (`XREADGROUP` / `XACK`)
- Group name: `soca-ingest` (shared across all replicas)
- Consumer name: hostname (unique per replica)
- `XREADGROUP '>'` — only fetches undelivered messages for this consumer
- `XACK` only after successful HTTP 201 from soca-control
- If POST fails, message is NOT acked — Redis redelivers to another consumer

### Pub/Sub Consumer (PubSubConsumer)

- Uses **synchronous REST polling** against the Pub/Sub HTTP API (not gRPC streaming)
- Polls every 5 seconds using `POST https://pubsub.googleapis.com/v1/{subscription}:pull`
- Authenticates using `google.auth.transport.requests.AuthorizedSession` (respects `HTTPS_PROXY`)
- Acknowledges message only after HTTP 201 from soca-control — unacked messages are redelivered by Pub/Sub
- Message `data` field is base64-decoded before JSON parsing (Pub/Sub REST API always base64-encodes payload)
- Reads credentials from `config.json` → `pubsub_key_path` (written by soca-control's Push Cloud Credentials)

> **Why REST polling instead of gRPC streaming?**
> gRPC ignores standard `HTTPS_PROXY` / `HTTP_PROXY` environment variables. In
> environments behind an HTTP proxy, the gRPC streaming pull silently fails.
> REST polling works reliably through any standard HTTP proxy.

---

## Configuration

### Environment Variables (`.env`)

| Variable | Required | Description |
|----------|----------|-------------|
| `SOCA_CONTROL_URL` | Yes | Base URL of soca-control, e.g. `http://<soca-control-ip>:8000` |
| `SOCA_CONTROL_INGEST_KEY` | Yes | Shared bearer token for ingest API — pushed automatically by soca-control |
| `PORT` | No | HTTP port (default: 8010) |

> `GOOGLE_APPLICATION_CREDENTIALS` is **not used** by soca-service. Pub/Sub credentials are read from `config.json` → `pubsub_key_path`, which is written when soca-control pushes Pub/Sub credentials via its Settings UI.

### `config.json` (written by soca-control)

| Key | Description |
|-----|-------------|
| `pubsub_key_path` | Absolute path to Pub/Sub service account JSON key file |
| `pubsub_project_id` | GCP project ID (informational — edge-level config is fetched from soca-control) |
| `pubsub_subscription` | Default subscription name |

`config.json` is written by soca-control's **Push Cloud Credentials** button (`POST /api/config`). Do not edit it manually.

---

## Health Endpoint

```
GET /health
```

Response:
```json
{
  "status": "ok",
  "consumer_name": "hostname",
  "uptime_seconds": 3600,
  "pubsub": {
    "status": "running",
    "active_edges": 2,
    "messages_processed": 145,
    "last_message_at": "2026-03-26T10:00:00Z",
    "last_error": null
  },
  "streams": {
    "status": "running",
    "active_edges": 1,
    "messages_processed": 87,
    "last_message_at": "2026-03-26T10:00:00Z",
    "last_error": null
  }
}
```

The health endpoint is monitored from soca-control Settings → soca-service card (live, auto-refreshes every 15 seconds).

---

## API Endpoints

| Endpoint | Auth | Description |
|----------|------|-------------|
| `GET /health` | None | Service status and consumer metrics |
| `POST /api/config` | Bearer (ingest key) | Push Pub/Sub credentials and config from soca-control |
| `POST /setup/ingest-key` | None | Bootstrap: write `SOCA_CONTROL_INGEST_KEY` to `.env` |

---

## File Structure

```
soca-service/
├── main.py              # FastAPI app: lifespan, /health, /api/config, /setup/ingest-key
├── consumers/
│   ├── pubsub.py        # PubSubConsumer — REST polling
│   └── streams.py       # StreamsConsumer — Redis Consumer Groups
├── requirements.txt
├── start.sh
├── stop.sh
├── .env                 # SOCA_CONTROL_URL, SOCA_CONTROL_INGEST_KEY
├── .env.example
└── config.json          # written by soca-control Push Cloud Credentials
```
