---
title: "LogFlow vs Loki: Why Your Log Aggregation Doesn't Need to Be Complex"
date: 2026-04-05
category: "DevOps"
tags: ["Docker", "Logging", "Loki", "Open Source", "Self-Hosted"]
---

## The Loki Problem

Grafana Loki has **28,000+ GitHub stars** and is the go-to log aggregation system for Kubernetes. But it has real problems for most teams:

### 1. Distributed Architecture Overkill

Loki requires:
- **Ingester** — Receives and buffers logs
- **Querier** — Executes queries against stored logs
- **Gateway** — Routes requests
- **Object Storage** — S3, GCS, or Azure Blob for log storage
- **Index Storage** — DynamoDB, Bigtable, or BoltDB

That's **5 components** minimum. For a team with 50 containers.

### 2. No Built-in UI

Loki is headless. You **must** run Grafana to view logs. That's another container, another configuration, another thing to maintain.

### 3. Storage Grows Without Bound

This is a [known issue](https://github.com/grafana/loki/issues/17817) in Loki. The Docker plugin storage can grow indefinitely. Teams have reported disks filling up unexpectedly.

### 4. LogQL Learning Curve

```logql
{container="web"} |= "error" | json | status >= 500 | line_format "{{ '{{' }}.message{{ '}}' }}"
```

You need to learn a new query language just to search logs.

### 5. Docker Driver Bugs

- [Docker driver client log grows indefinitely](https://github.com/grafana/loki/issues/16401)
- [Rate limit misconfiguration](https://github.com/grafana/loki/issues/17536)
- [Logs not collected from non-running containers](https://github.com/grafana/alloy/issues/3408)

## Enter LogFlow

LogFlow is a **single container** that does everything most teams need from Loki.

### One Command Setup

```bash
docker run -d --name logflow \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -v logflow-data:/data/logs \
  -p 3101:3101 \
  ghcr.io/irfancode/logflow:latest
```

Open http://localhost:3101 and you have:
- **Full-text search** — No query language needed
- **Filter by level** — Info, warn, error
- **Filter by source** — Per-container logs
- **Live tail** — Real-time log streaming
- **Auto-retention** — Configurable cleanup

### What You Get

**Built-in UI:**
- Search across all container logs
- Filter by log level (info/warn/error)
- Filter by container/source
- Live tail mode
- Storage usage display

**Loki-Compatible API:**
- Port 3100 speaks the Loki API
- Existing Grafana dashboards work unchanged
- Promtail agents can send logs without modification

**Automatic Retention:**
- Configurable retention period (default: 7 days)
- Maximum storage limit (default: 10GB)
- Automatic cleanup of old logs
- Never runs out of disk space

### Comparison

| Feature | Grafana Loki | LogFlow |
|---------|-------------|---------|
| Components | 5+ | 1 |
| External Storage | Required (S3/GCS) | Local volume |
| Built-in UI | No (needs Grafana) | Yes |
| Query Language | LogQL | Full-text search |
| Auto Retention | Manual setup | Built-in |
| Storage Limits | None (grows unbounded) | Configurable max |
| Docker Integration | Promtail sidecar | Direct socket |
| Setup Complexity | High | Zero |

### Architecture Comparison

**Loki Stack:**
```
Promtail → Loki Gateway → Ingester → S3/GCS
                        → Querier → Grafana
```

**LogFlow:**
```
Docker Socket → LogFlow → UI
```

That's the difference.

## Getting Started

```bash
# Basic usage
docker run -d --name logflow \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -v logflow-data:/data/logs \
  -p 3101:3101 \
  ghcr.io/irfancode/logflow:latest

# With custom retention
docker run -d --name logflow \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -v logflow-data:/data/logs \
  -p 3101:3101 \
  -e RETENTION_DAYS=30 \
  -e MAX_STORAGE_GB=50 \
  ghcr.io/irfancode/logflow:latest
```

### Using with Docker Compose

```yaml
services:
  logflow:
    image: ghcr.io/irfancode/logflow:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - logflow-data:/data/logs
    ports:
      - "3101:3101"
    environment:
      - RETENTION_DAYS=14
      - MAX_STORAGE_GB=20

  app:
    image: your-app:latest
    logging:
      driver: fluentd
      options:
        fluentd-address: logflow:3100
        tag: "{{ '{{' }}.Name{{ '}}' }}"

volumes:
  logflow-data:
```

## The Bottom Line

Loki is designed for companies with thousands of containers and dedicated platform teams. If you're running 10-500 containers and just want to search logs, LogFlow is simpler, safer (no unbounded storage growth), and includes a UI out of the box.

---

**GitHub:** [github.com/irfancode/docker-toolkit/logflow](https://github.com/irfancode/docker-toolkit/tree/main/logflow)
**License:** MIT
