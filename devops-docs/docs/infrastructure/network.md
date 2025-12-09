# Network Architecture

## Overview

Network design ensures secure, scalable, and reliable communication between systems and users.

## Network Components

### Virtual Private Cloud (VPC)

Isolated network environment with configurable IP address range and subnets.

```
VPC (10.0.0.0/16)
├── Public Subnet (10.0.1.0/24)
│   └── Load Balancers
│       └── NAT Gateway
├── Private Subnet (10.0.2.0/24)
│   └── Application Servers
└── Private Subnet (10.0.3.0/24)
    └── Database Servers
```

### Subnets

Divide VPC into logical segments:
- **Public**: Internet-facing resources
- **Private**: Internal resources without internet access
- **Database**: Isolated database tier

### Security Groups

Firewall rules controlling inbound/outbound traffic:

```
Web Tier:
- Inbound: 80, 443 from internet
- Outbound: To app tier on app ports

App Tier:
- Inbound: From web tier
- Outbound: To database on 5432

Database Tier:
- Inbound: From app tier on 5432
- Outbound: None required
```

### Network ACLs

Stateless firewall at subnet level for additional security.

## High Availability

### Load Balancing

Distribute traffic across multiple instances:
- Application Load Balancer (ALB)
- Network Load Balancer (NLB)
- Cross-zone load balancing

### Auto Scaling

Automatically adjust capacity:
- Scale up when demand increases
- Scale down during low usage
- Health check-based replacement

## Disaster Recovery

### Multi-Region

Primary region with failover region:
- Data replication
- DNS failover
- RTO: 1-5 minutes
- RPO: Real-time or near real-time

### Multi-AZ

Within region redundancy:
- Cross-availability zone deployment
- Automatic failover
- RTO: < 1 minute

## Security Best Practices

1. **VPC Isolation** - Separate production and non-production
2. **Security Groups** - Least privilege inbound rules
3. **NACLs** - Additional layer of defense
4. **VPN** - Encrypt remote access
5. **Flow Logs** - Monitor network traffic
6. **DDoS Protection** - CloudFront, WAF

