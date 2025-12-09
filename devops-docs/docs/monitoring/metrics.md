# Metrics

## Overview

Metrics are quantitative measurements of system performance and behavior.

## Types of Metrics

### RED Metrics (for Services)
- **Rate**: Requests per second
- **Errors**: Request failures per second
- **Duration**: Request latency

### USE Metrics (for Resources)
- **Utilization**: Percentage of resource in use
- **Saturation**: Queueing or backlog
- **Errors**: Error count

## Key Application Metrics

### Request Metrics
```
- Requests per second (RPS)
- Request latency (p50, p95, p99, p99.9)
- Error rate (errors per second)
- Error percentage
- Request size distribution
```

### Business Metrics
```
- Active users
- Signups per day
- Transaction volume
- Revenue per transaction
- User retention rate
```

### Performance Metrics
```
- Cache hit rate
- Database query time
- API response time
- Page load time
- Conversion rate
```

## Key Infrastructure Metrics

### Compute
- CPU utilization
- CPU throttling
- Memory usage
- Memory pressure
- Swap usage

### Storage
- Disk utilization
- Disk I/O operations
- Disk read/write latency
- Free space remaining
- Inode usage

### Network
- Network throughput
- Network latency
- Packet loss rate
- Connection count
- Bandwidth utilization

### Database
- Query latency
- Query throughput
- Lock wait time
- Connection pool utilization
- Replication lag

## Metric Collection

### Tools
- Prometheus for metrics collection
- StatsD for application metrics
- CloudWatch for cloud metrics
- Custom agents for specialized metrics

### Collection Interval
- Critical metrics: Every 10 seconds
- Important metrics: Every 30 seconds
- Regular metrics: Every 1 minute
- Historical metrics: Every 5 minutes

## Metric Storage

- **Hot storage**: Last 30 days (high resolution)
- **Warm storage**: Last 1 year (lower resolution)
- **Cold storage**: Historical data (archived)
- **Retention**: Minimum 2 years

## Alerting on Metrics

Set alerts for:
- Sudden spikes
- Sustained degradation
- Threshold breaches
- Anomalies
- Missing data

## SLO Metrics

**Service Level Objectives** define target performance:
- 99.9% availability
- < 200ms p99 latency
- < 0.1% error rate
- 99% success rate

