# Alerts

## Overview

Alert configuration and management for timely incident response.

## Alert Types

### Threshold Alerts

Trigger when metric crosses threshold:
- CPU > 80%
- Memory > 90%
- Error rate > 1%
- Disk usage > 85%

### Anomaly Alerts

Detect unusual behavior:
- Deviation from baseline
- Machine learning models
- Statistical analysis
- Historical comparison

### Composite Alerts

Multiple conditions:
- AND logic: All conditions must be true
- OR logic: Any condition true
- Complex expressions
- Cross-metric correlation

## Alert Routing

### Alert Groups

Organize by severity:
```
Critical
├── PagerDuty (immediate)
├── SMS notification
├── Call escalation
└── War room

High
├── Slack #incidents
├── Email
└── Digest summary

Medium
├── Email
└── Daily digest

Low
└── Weekly digest
```

### Escalation

```
Initial response: 5 minutes
→ Manager escalation: 15 minutes
→ Director escalation: 30 minutes
→ VP escalation: 60 minutes
```

## Alert Fatigue Prevention

### Threshold Tuning

- Avoid hair-trigger alerts
- Account for normal variation
- Use percentiles (p95 vs p100)
- Regular review and adjustment

### Deduplication

- Deduplicate similar alerts
- Group related incidents
- Silence repeated alerts
- Aggregate notifications

### Business Hours

- Different thresholds for on-call
- Quieter hours alerting
- Batch non-critical alerts
- Weekend vs weekday

## Alert Configuration

### Example: High Error Rate

```yaml
Alert: HighErrorRate
Condition: error_rate > 1% for 5 minutes
Severity: High
Routing: Slack #incidents
Message: "{{service}} error rate {{value}}%"
Runbook: "docs/runbooks/high-error-rate"
```

### Example: Low Disk Space

```yaml
Alert: LowDiskSpace
Condition: disk_usage > 85% for 10 minutes
Severity: Medium
Routing: Email
Action: Auto-cleanup if possible
```

## Monitoring Alerts

### Meta-monitoring

Track alert system health:
- Alert delivery success
- Processing latency
- Duplicate rate
- False positive rate

### Alert Quality

- False positive rate < 5%
- True positive rate > 95%
- Mean time to acknowledge < 5 minutes
- Mean time to resolve < 1 hour

## On-Call Management

### Schedule

- Weekly rotation
- 24/7 coverage
- Escalation paths
- Handoff documentation

### Preparation

- Review runbooks
- Understand systems
- Test alert channels
- Update contact info

### After-Call

- Capture learnings
- Update runbooks
- Improve alerts
- Share knowledge

## Tools

- **PagerDuty** - Incident management
- **Opsgenie** - Alert management
- **Alertmanager** - Prometheus alerting
- **CloudWatch** - AWS alerting
- **Datadog** - Monitoring and alerting

## Best Practices

1. **Alert on symptoms, not causes** - What users see
2. **Actionable alerts only** - Can someone act on it?
3. **Clear runbooks** - How to respond?
4. **Regular testing** - Verify alert delivery
5. **Post-incident review** - Learn from alerts
6. **Tune continuously** - Reduce noise

