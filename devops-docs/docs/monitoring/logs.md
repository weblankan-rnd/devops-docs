# Logs

## Overview

Log aggregation and management for debugging, auditing, and compliance.

## Log Types

### Application Logs

Logs from application code:
- Info, warning, error, debug levels
- Structured logging (JSON)
- Request IDs for tracing
- Performance metrics

### System Logs

Operating system and service logs:
- Kernel messages
- Service startup/shutdown
- System errors
- Authentication events

### Access Logs

Request logs from services:
- HTTP access logs
- Database query logs
- API request logs
- Timestamps and response times

### Security Logs

Security-related events:
- Authentication attempts
- Authorization failures
- Configuration changes
- Suspicious activities

## Log Collection

### Agents

Collectors that send logs to central system:
- Filebeat for files
- Fluentd for multiple sources
- Logstash for processing
- Custom agents

### Processing

- Parsing and structuring
- Filtering and sampling
- Enrichment with context
- Anomaly detection

### Storage

- Elasticsearch for indexing
- S3 for archival
- Long-term retention (90+ days)
- Tiered storage by age

## Log Analysis

### Searching

- Full-text search
- Field-based queries
- Time range filtering
- Boolean operators

### Aggregation

- Count events
- Group by field
- Statistical analysis
- Trend detection

### Visualization

- Dashboard creation
- Graphs and charts
- Heatmaps
- Real-time monitoring

## Retention Policy

```
- Hot (searchable): 30 days
- Warm (slow query): 90 days
- Cold (archived): 365+ days
- Deletion: Based on retention schedule
```

## Security & Compliance

- Encryption in transit and at rest
- Access control (RBAC)
- Audit logging
- Data masking (PII)
- Compliance requirements (GDPR, HIPAA)
- Immutable logs for forensics

## Common Queries

```
# Count errors in last hour
status:error AND timestamp:[now-1h TO now]

# Response times by endpoint
endpoint:* | stats avg(response_time) by endpoint

# Failed login attempts
event:authentication AND status:failed | stats count by user

# Database query performance
component:database | stats p95(query_time) by query_type
```

## Tools

- **Elasticsearch** - Distributed search and analytics
- **Kibana** - Data visualization
- **Logstash** - Data processing pipeline
- **ELK Stack** - Complete logging solution
- **Datadog** - Cloud-based logging
- **Splunk** - Enterprise logging platform

