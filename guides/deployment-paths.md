# Deployment Paths

Use this page to choose the shortest path for your environment before diving into the lower-level setup guides.

## Recommended Options

### 1. Docker deployment

Use [Docker Deployment](../deployment/docker-deployment.md) when you want the fastest repeatable setup across development, demo, or controlled production environments.

Best for:

- Internal demos
- Pilot deployments
- Small environments with predictable infrastructure

### 2. Bare metal on RHEL or Rocky Linux

Use [Bare Metal RHEL](../deployment/bare-metal-rhel-guide.md) when the target environment is on-premises and managed like traditional enterprise infrastructure.

Best for:

- Factory or facility deployments
- Locked-down enterprise hosts
- Environments with OS-level operational controls

### 3. GCP and Cloud Run

Use [GCP Cloud Run](../deployment/gcp-cloud-run-guide.md) when the control-plane services need managed cloud hosting and easier scaling.

Best for:

- Cloud-first deployments
- Distributed multi-edge installations
- Teams already using Google Cloud

## Integration Decisions

### Alert transport

- Use [Pub/Sub Setup](../deployment/pubsub-setup-guide.md) for cloud or multi-region distribution
- Use Redis Stream when the deployment is local or on-premises and cloud messaging is unnecessary

### Snapshot storage

- Use [GCS Snapshot Storage](../deployment/gcs-snapshot-storage.md) when multiple edges need centralized media access
- Keep snapshots local on the edge when the environment is isolated and cloud sync is not required

### Reporting

- Use [Metabase Reporting](../deployment/metabase-reporting-guide.md) when you want SQL-driven reporting and dashboard creation
- Use [Grafana Reporting](../deployment/grafana-reporting-guide.md) when the team already standardizes on Grafana for operational dashboards
