---
title: "AI-Assisted Monitoring: From Zero to Grafana Dashboards in a Day"
date: 2026-04-06
categories:
  - DevOps
  - AI
  - Monitoring
  - Prometheus
---

## The Problem

You're shipping features but can't answer basic questions:
- Is the API slow?
- Are there errors happening right now?
- Is the database struggling?

Without monitoring, you're flying blind. But setting up Prometheus, writing PromQL, and building Grafana dashboards feels like a full-time job.

## The AI Solution

I use AI to generate monitoring queries, alerts, and dashboard panels in hours instead of weeks.

## The Workflow

```
1. Requirements  → What do we need to monitor?
2. AI Generation → Generate PromQL, alerts, JSON
3. Refinement    → Tune thresholds, add labels
4. Testing       → Validate in staging
5. Production   → Deploy to monitoring stack
```

## AI in Action: Example Prompts

### Prompt 1: Latency Queries
```
"Write PromQL to calculate:
- 95th percentile latency from http_request_duration_seconds_bucket
- Show latency by endpoint
- Use 5-minute rate"
```

**AI Output**:
```promql
# p95 latency
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))

# By endpoint
histogram_quantile(0.95, sum by (endpoint) (rate(http_request_duration_seconds_bucket[5m])))
```

**Manual Refinement**: Add `job="flask-api"` filter, adjust time windows.

### Prompt 2: Error Rate Alerts
```
"Create Prometheus alerts for:
- 5xx errors above 5%
- Latency p95 above 2 seconds
- CPU usage above 80%"
```

**AI Output**:
```yaml
groups:
  - name: api_alerts
    rules:
      - alert: APIHighErrorRate
        expr: rate(http_requests_total{status=~"5.."[5m]) > 0.05
        for: 5m
```

**Manual Refinement**: Adjust `for` duration, add annotation summaries.

### Prompt 3: Log Analysis
```
"Python script to:
- Parse log file with timestamp, level, message
- Count errors, warnings by category
- Find top 10 error messages"
```

**AI Output**: Complete log analyzer from [POC #2](https://github.com/irfancode/poc-monitoring-log-analysis)

## Real-World Example: API Dashboard

From [POC #2](https://github.com/irfancode/poc-monitoring-log-analysis):

```json
{
  "panels": [
    {
      "title": "Request Rate",
      "targets": [{"expr": "rate(http_requests_total[5m])"}]
    },
    {
      "title": "Latency p95", 
      "targets": [{"expr": "histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))"}]
    },
    {
      "title": "Error Rate",
      "targets": [{"expr": "rate(http_requests_total{status=~\"5..\"}[5m])"}]
    }
  ]
}
```

## SLO-Based Alerting

Modern monitoring isn't just "is it up" - it's "is it meeting user expectations."

```promql
# Error budget for 99.9% availability
# 0.1% error budget = 86.4 seconds/day

- alert: SLOViolation
  expr: |
    sum(rate(http_requests_total{status=~"5.."[1h])) by (service)
    /
    sum(rate(http_requests_total[1h])) by (service)) > 0.001
  for: 1h
```

## What This Means for Your Organization

- **Immediate Visibility**: Dashboards in hours, not weeks
- **Proactive Operations**: Alerts before customers complain
- **SLO Discipline**: Track error budgets, not just uptime
- **Cost Control**: Monitor resource usage to optimize spend

Need visibility into your APIs? [Let's talk](/#contact) about setting up monitoring.
