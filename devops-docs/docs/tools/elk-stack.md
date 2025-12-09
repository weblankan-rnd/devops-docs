# ELK Stack

## Overview

ELK Stack (Elasticsearch, Logstash, Kibana) is a comprehensive logging and analytics platform.

## Components

### Elasticsearch

Distributed search and analytics engine:
- Full-text search
- Near real-time analysis
- Scalable to petabytes
- RESTful API

### Logstash

Data processing pipeline:
- Collect from multiple sources
- Parse and structure data
- Enrich with context
- Ship to Elasticsearch

### Kibana

Data visualization platform:
- Dashboards
- Searches
- Graphs and charts
- Alerting

## Architecture

```
Data Sources
    ↓
Logstash (Collection & Processing)
    ↓
Elasticsearch (Storage & Indexing)
    ↓
Kibana (Visualization)
```

## Logstash Pipeline

```
input {
  syslog {
    port => 514
  }
}

filter {
  grok {
    match => { "message" => "%{SYSLOGLINE}" }
  }
  date {
    match => [ "timestamp", "MMM  d HH:mm:ss", "MMM dd HH:mm:ss" ]
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "syslog-%{+YYYY.MM.dd}"
  }
}
```

## Index Management

### Index Naming

```
logstash-app-2025.12.09
logstash-app-2025.12.10
logstash-app-2025.12.11
```

### Index Lifecycle

```
Hot → Warm → Cold → Delete

- Hot: 1 day, fully searchable
- Warm: 30 days, slow storage
- Cold: 90 days, archived
- Delete: After retention period
```

## Kibana Dashboards

### Application Monitoring

- Request rate over time
- Error rate by endpoint
- Response time distribution
- Top slow queries

### System Monitoring

- CPU usage
- Memory consumption
- Disk I/O
- Network throughput

### Security Monitoring

- Authentication failures
- Unusual access patterns
- Configuration changes
- Security events

## Common Queries

```
# Count errors in last hour
level:ERROR AND timestamp:[now-1h TO now]

# Average response time by endpoint
endpoint:* | stats avg(response_time) by endpoint

# Most frequent error messages
level:ERROR | stats count by message | sort count desc
```

## Performance Tuning

- **Shard size** - Optimal ~30-50GB
- **Refresh interval** - 30s default
- **Merge policy** - Balance indexing vs search
- **Heap allocation** - 50% of available RAM
- **Circuit breaker** - Prevent OOM

## Alerting

```
Configure alerts on:
- High error rates
- Slow response times
- Low disk space
- Service unavailability
- Security events
```

## Best Practices

1. **Index rotation** - Daily indices
2. **Retention policy** - Defined schedule
3. **Field mapping** - Consistent schema
4. **Security** - Encrypt at rest and transit
5. **Monitoring** - Monitor ELK itself
6. **Capacity planning** - Growth estimation

