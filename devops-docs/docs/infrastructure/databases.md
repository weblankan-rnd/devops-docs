# Databases

## Overview

Database management, maintenance, replication, and backup procedures.

## Database Types

### Relational (SQL)

For structured data with relationships:
- PostgreSQL
- MySQL
- Oracle
- SQL Server

### NoSQL

For unstructured or semi-structured data:
- MongoDB (document)
- DynamoDB (key-value)
- Cassandra (time-series)
- Redis (in-memory)

## High Availability

### Replication

Primary-replica setup:
- Master handles writes
- Replicas handle reads
- Automatic failover
- Monitoring replication lag

### Clustering

Multiple nodes sharing data:
- Load balancing
- Automatic failover
- Data distribution
- Consensus protocols

## Backup Procedures

### Backup Types

```
- Physical backups: Full database copy
- Logical backups: SQL dumps
- Continuous: Transaction logs
- Point-in-time: Recovery to specific moment
```

### Backup Frequency

```
- Full backup: Weekly
- Incremental: Daily
- Transaction logs: Every 5 minutes
- Off-site copy: Daily
```

## Database Maintenance

### Regular Tasks

- Index optimization
- Statistics update
- Query analysis
- Bloat cleanup
- Vacuum (for PostgreSQL)

### Performance Tuning

- Query optimization
- Connection pooling
- Cache tuning
- Lock management
- Resource allocation

## Scaling Strategies

### Vertical Scaling

- Increase instance size
- More CPU/Memory
- Limited by hardware
- Requires downtime

### Horizontal Scaling

- Sharding data across instances
- Read replicas
- No single point of failure
- Complex application logic

## Security

- Encryption at rest
- Encryption in transit
- Strong authentication
- Access control
- Audit logging
- Regular backups
- Vulnerability scanning

## Monitoring

Key metrics:
- Connection count
- Query latency
- Replication lag
- Disk usage
- CPU/Memory
- Lock wait times
- Transaction rate

