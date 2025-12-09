# Traces

## Overview

Distributed tracing tracks requests across services to understand system behavior and performance.

## Tracing Concepts

### Spans

Individual unit of work:
- Operation name
- Start and end time
- Duration
- Status (success/failure)
- Tags and logs

### Traces

Complete request flow:
- Unique trace ID
- Parent-child span relationships
- End-to-end timing
- Service map

### Context Propagation

Passing trace ID across services:
- HTTP headers
- Message queues
- RPC calls
- Ensures trace continuity

## Instrumentation

### Auto-instrumentation

Framework-based tracing:
- Library integration
- Minimal code changes
- HTTP client/server
- Database queries

### Manual Instrumentation

Explicit span creation:
- Custom operations
- Business logic
- Complex workflows
- Precise control

## Trace Collection

### Agents

Collect and forward traces:
- Jaeger agents
- Zipkin collectors
- DataDog agents
- Custom collectors

### Processing

- Sampling decisions
- Filtering
- Enrichment
- Batching

### Storage

- Trace database
- Retention (7-30 days typical)
- Indexing by trace/span ID
- Archival for long-term

## Analysis & Visualization

### Flame Graphs

Visualize call stacks:
- Time spent in each function
- Call hierarchy
- Bottleneck identification
- Performance optimization

### Service Map

Visualize service relationships:
- Service dependencies
- Error paths
- Latency between services
- Traffic patterns

### Latency Analysis

Identify slow requests:
- Breakdown by span
- Compare percentiles (p50, p95, p99)
- Trend analysis
- SLO tracking

## Use Cases

- **Performance Optimization** - Find slow operations
- **Error Investigation** - Trace failures across services
- **Dependency Mapping** - Understand system topology
- **Capacity Planning** - Identify bottlenecks
- **SLO Monitoring** - Track latency objectives

## Tools

- **Jaeger** - Open-source distributed tracing
- **Zipkin** - Distributed tracing platform
- **DataDog** - APM and tracing
- **Lightstep** - Enterprise tracing
- **AWS X-Ray** - AWS-native tracing

## Best Practices

1. **Sample appropriately** - Balance detail vs cost
2. **Trace critical paths** - Focus on important operations
3. **Include context** - Add relevant tags
4. **Monitor collectors** - Track tracing system health
5. **Retention policy** - Balance storage and cost
6. **Error tracking** - Ensure errors are traced

