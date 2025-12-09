# Storage

## Overview

Storage solutions for persistent data, backups, and archives.

## Storage Types

### Object Storage

For files, documents, archives, and media:
- S3-compatible storage
- High availability
- Lifecycle policies
- Versioning support

### Block Storage

For databases and high-performance workloads:
- Persistent volumes
- Point-in-time snapshots
- Replication
- Encryption

### File Storage

For shared access across instances:
- NFS-compatible
- POSIX compliance
- Multi-instance access
- Scalable capacity

## Backup Strategy

### Backup Schedule

```
- Hourly: Transaction logs
- Daily: Full database backup
- Weekly: Full filesystem backup
- Monthly: Archive copy
```

### Retention Policy

```
- Daily backups: 30 days
- Weekly backups: 12 weeks
- Monthly backups: 36 months
- Compliance holds: As required
```

### Testing

- Monthly backup restoration test
- Verify data integrity
- Document recovery time
- Update runbooks

## Disaster Recovery

### RTO/RPO Targets

- **RTO**: 4 hours
- **RPO**: 1 hour
- **Backup retention**: 90 days minimum

### Offsite Backups

- Cross-region replication
- Geographic diversity
- Encryption in transit and at rest
- Air-gapped copy for ransomware protection

## Storage Optimization

### Cost Reduction

- Tiered storage (hot/warm/cold)
- Compression
- Deduplication
- Unused volume cleanup

### Performance

- SSD for databases
- HDD for archives
- Caching layer
- Content delivery network

## Security

- Encryption at rest (AES-256)
- Encryption in transit (TLS)
- Access control (IAM)
- Audit logging
- Data classification

