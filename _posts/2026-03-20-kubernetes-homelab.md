---
title: "Kubernetes at Home: Lessons from Production"
date: 2025-08-05
category: "Kubernetes"
tags: ["Kubernetes", "K8s", "Homelab", "DevOps", "Self-Hosted"]
---

When people ask why I run Kubernetes at home, I tell them: you can't truly understand a tool until you run it in production. And what better production than your home lab? After years of managing Kubernetes clusters professionally and personally, here are my hard-earned lessons.

## Why Kubernetes at Home?

### Professional Development
- Stay current with K8s trends
- Test new features in a safe environment
- Practice incident response

### Personal Projects
- Host multiple applications
- Learn infrastructure-as-code
- Build home automation

## My Home Lab Setup

### Hardware
- 3x Raspberry Pi 4 (4GB) or old laptops
- Synology NAS for persistent storage
- Ubiquiti Dream Machine for networking

### Software
- k3s (lightweight Kubernetes)
- Metallb for load balancing
- Longhorn for storage
- Traefik for ingress

## Architecture

```
┌─────────────────────────────────────────┐
│           Home Network                   │
├─────────────────────────────────────────┤
│  ┌───────────────────────────────────┐  │
│  │    k3s Cluster (3 nodes)          │  │
│  │  ┌─────┐  ┌─────┐  ┌─────┐       │  │
│  │  │ Pi1 │  │ Pi2 │  │ Pi3 │       │  │
│  │  └─────┘  └─────┘  └─────┘       │  │
│  └───────────────────────────────────┘  │
│              ↓ ↓ ↓                      │
│  ┌───────────────────────────────────┐  │
│  │  Services                         │  │
│  │  - Plex                           │  │
│  │  - Home Assistant                 │  │
│  │  - GitLab                         │  │
│  │  - Portfolio sites                │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

## Key Lessons

### Lesson 1: Storage is Hard

**Problem:** Container storage is ephemeral
**Solution:** Use Longhorn or NFS for persistent volumes

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: data-pvc
spec:
  storageClassName: longhorn
  resources:
    requests:
      storage: 10Gi
```

### Lesson 2: Networking Takes Time

**Problem:** Pods can't communicate
**Solution:** CNI plugins matter. Use Calico or Flannel.

```bash
# Install k3s with flannel
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--flannel=wireguard" sh
```

### Lesson 3: Backups are Essential

**Problem:** Lost data is lost forever
**Solution:** Velero for cluster backups

```bash
velero backup create home-cluster-backup
velero schedule create daily-backup --schedule="0 0 * * *"
```

### Lesson 4: Resource Limits Save You

**Problem:** One app consumes everything
**Solution:** Always set resource requests and limits

```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "250m"
  limits:
    memory: "512Mi"
    cpu: "500m"
```

### Lesson 5: GitOps Changes Everything

**Problem:** Manual deployments are error-prone
**Solution:** ArgoCD or Flux

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
spec:
  source:
    repoURL: https://github.com/irfancode/manifests
    path: apps/my-app
  destination:
    server: https://kubernetes.default.svc
  project: default
```

## Useful Tools

### CLI Tools
- **k9s** — Terminal UI for Kubernetes
- **kubectl** — Official CLI
- **kubectx** — Switch clusters easily

### Monitoring
- **Prometheus + Grafana** — Metrics and visualization
- **Loki** — Log aggregation
- **Alertmanager** — Alerts

### Security
- **Kyverno** — Policy engine
- **Trivy** — Vulnerability scanning

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| No resource limits | Always set requests/limits |
| Running as root | Use security contexts |
| No backups | Velero + Longhorn snapshots |
| No monitoring | Prometheus from day one |
| Manual deployments | GitOps from day one |

## What's Running at Home

My current home lab runs:
- Plex media server
- Home Assistant for smart home
- GitLab for version control
- Portfolio websites
- Pi-hole for DNS ad-blocking
- Vault for secrets management

## Conclusion

Kubernetes at home isn't about flexing—it's about learning. The principles you learn managing a homelab directly translate to production environments.

Start small. One node. Add complexity as you learn. And remember: the goal isn't perfection—it's understanding.

What's in your homelab? Let's connect and share experiences!

---

**Connect**: [LinkedIn](https://linkedin.com/in/sirfan98cs) | [GitHub](https://github.com/irfancode)
