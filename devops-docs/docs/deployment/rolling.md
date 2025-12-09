# Rolling Deployment

## Overview

Rolling deployment gradually replaces instances of the old version with the new version until all instances run the new version.

## Advantages

- No additional infrastructure needed
- Gradual rollout
- Simple to understand
- Lower cost than blue-green
- Easy rollback by reversing the process

## Disadvantages

- Downtime during deployment
- Complexity with database migrations
- Mixed versions running simultaneously
- Session affinity issues

## Deployment Process

### 1. Preparation

```
□ Health check all current instances
□ Prepare load balancer configuration
□ Plan rolling schedule
□ Prepare rollback plan
```

### 2. Rolling Update

```
Phase 1: Update 25% of instances
- Remove instance from load balancer
- Deploy new version
- Wait for health check
- Add back to load balancer
- Monitor metrics

Repeat for remaining instances in phases
```

### 3. Monitoring

```
After each phase:
□ Check error rates
□ Monitor response times
□ Verify functionality
□ Check resource usage
```

### 4. Completion

```
□ All instances updated
□ Full health check passed
□ Performance metrics normal
□ Declare deployment complete
```

## Timeline

- Preparation: 10 minutes
- Rolling (4 phases): 40-60 minutes
- Monitoring: 30 minutes
- **Total: 1.5 - 2 hours**

## Best Practices

1. **Health checks** - Ensure instances are healthy before moving to next
2. **Gradual pace** - Don't rush the update process
3. **Monitor closely** - Watch metrics throughout
4. **Have rollback plan** - Know how to undo quickly
5. **Test in staging** - Always test before production

