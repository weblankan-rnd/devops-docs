# Prometheus

## Overview

Prometheus is an open-source monitoring and alerting system.

## Key Concepts

**Metric**: Named quantity to measure

**Time Series**: Sequence of data points with timestamp

**Target**: System being monitored

**Scrape**: Collect metrics from target

**Exporter**: Service exposing metrics

## Configuration

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - localhost:9093

rule_files:
  - "/etc/prometheus/rules/*.yml"

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "node"
    static_configs:
      - targets:
          - localhost:9100
          - localhost:9101

  - job_name: "docker"
    static_configs:
      - targets: ["localhost:9323"]
```

## Metric Types

### Counter

Cumulative metric that only increases:
```
http_requests_total
```

### Gauge

Metric that can go up or down:
```
memory_usage_bytes
cpu_usage_percent
```

### Histogram

Distribution of observations:
```
http_request_duration_seconds
```

### Summary

Similar to histogram with quantiles:
```
http_request_duration_seconds_sum
http_request_duration_seconds_count
```

## PromQL Queries

```promql
# Current value
node_memory_MemFree_bytes

# Rate of change
rate(http_requests_total[5m])

# Aggregation
sum(http_requests_total) by (instance)

# Comparison
node_memory_MemFree_bytes < 1073741824

# Alerts
http_error_rate > 0.05 for 5m
```

## Alerting Rules

```yaml
groups:
  - name: example
    interval: 30s
    rules:
      - alert: HighErrorRate
        expr: rate(errors_total[5m]) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"
          description: "Error rate is {{ $value }}"
```

## Exporters

- **Node Exporter** - System metrics
- **cAdvisor** - Container metrics
- **PostgreSQL Exporter** - Database metrics
- **Nginx Exporter** - Nginx metrics
- **Custom Exporters** - Application metrics

## Common Metrics

- `up` - Target availability
- `rate` - Per-second rate
- `histogram_quantile` - Percentiles
- `increase` - Total increase
- `topk` - Highest values

## Best Practices

1. **Metric naming** - Meaningful and consistent
2. **Labels** - Organize metrics
3. **Retention** - Balance storage
4. **Recording rules** - Pre-compute expensive queries
5. **Scrape optimization** - Avoid excessive scraping
6. **Alert tuning** - Avoid false positives

