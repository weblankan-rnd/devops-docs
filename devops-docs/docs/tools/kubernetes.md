# Kubernetes

## Overview

Kubernetes (K8s) is an open-source container orchestration platform for automating deployment, scaling, and management of containerized applications.

## Key Concepts

**Pod**: Smallest deployable unit containing one or more containers

**Deployment**: Desired state specification for running applications

**Service**: Network abstraction for accessing pods

**Namespace**: Virtual cluster within a physical cluster

## Common kubectl Commands

```bash
# View clusters
kubectl cluster-info

# Create deployment
kubectl apply -f deployment.yaml

# View deployments
kubectl get deployments

# View pods
kubectl get pods

# View services
kubectl get services

# Scale deployment
kubectl scale deployment myapp --replicas=3

# Rolling update
kubectl set image deployment/myapp myapp=myapp:2.0

# View logs
kubectl logs <pod-name>

# Execute command in pod
kubectl exec -it <pod-name> -- /bin/bash
```

## Best Practices

1. **Resource Limits** - Always set CPU and memory limits
2. **Liveness Probes** - Monitor container health
3. **Readiness Probes** - Control traffic routing
4. **Rolling Updates** - Gradual deployment strategy
5. **Namespace Isolation** - Separate environments
6. **RBAC** - Role-based access control

## Deployment Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:1.0
        ports:
        - containerPort: 8000
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 30
          periodSeconds: 10
```

## Troubleshooting

**Pod not starting**: Check logs with `kubectl logs`  
**Service not accessible**: Verify service selector and labels  
**Deployment stuck**: Check resource quotas and node capacity  
**Performance issues**: Review resource utilization and scaling
