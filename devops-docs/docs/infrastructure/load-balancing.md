# Load Balancing

## Overview

Load balancers distribute incoming traffic across multiple backend servers to improve reliability and performance.

## Load Balancer Types

### Layer 4 (Transport Layer)

Network Load Balancer - operates on TCP/UDP:
- Ultra-high performance
- Millions of requests per second
- Low latency
- Connection-based routing

### Layer 7 (Application Layer)

Application Load Balancer - operates on HTTP/HTTPS:
- Path-based routing
- Host-based routing
- Header-based routing
- SSL termination

## Routing Algorithms

### Round Robin
- Distribute equally across all backends
- Simple and fair
- Works best with identical servers

### Least Connections
- Route to server with fewest connections
- Better for long-lived connections
- Good for uneven load

### IP Hash
- Same client always goes to same server
- Sticky sessions
- Good for session affinity

### Weighted
- Distribute based on server capacity
- Different server sizes
- Configurable weights

## Health Checks

```
Passive checks:
- Monitor connection success
- Connection timeout detection

Active checks:
- HTTP GET requests
- TCP connection attempts
- Interval: 10-30 seconds
- Failure threshold: 2-3 consecutive failures
```

## Features

- **SSL/TLS Termination** - Decrypt at load balancer
- **Connection Pooling** - Reuse backend connections
- **Sticky Sessions** - Route requests from same client to same server
- **Rate Limiting** - Prevent abuse
- **DDoS Protection** - Block malicious traffic
- **Logging** - Track all connections

## Configuration Example

```yaml
Listener: Port 443 (HTTPS)
- SSL Certificate: example.com
- Backend Pool:
  - Server 1: 10.0.1.10:8080 (Weight: 1)
  - Server 2: 10.0.1.11:8080 (Weight: 1)
  - Server 3: 10.0.1.12:8080 (Weight: 1)
- Health Check:
  - Path: /health
  - Interval: 15s
  - Timeout: 5s
  - Healthy: 2 consecutive passes
  - Unhealthy: 2 consecutive failures
```

## Monitoring

Key metrics:
- Request rate
- Error rate
- Response time
- Backend health status
- Active connections
- Data transferred

## Best Practices

1. **Health Checks** - Frequent and reliable
2. **Multiple Backends** - At least 3 for HA
3. **Geographic Distribution** - Across AZs
4. **SSL/TLS** - Always use encryption
5. **Connection Limits** - Prevent resource exhaustion
6. **Logging** - Audit all traffic
7. **Capacity Planning** - Monitor and adjust

