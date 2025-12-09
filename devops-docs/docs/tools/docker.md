# Docker

## Overview

Docker is a containerization platform that packages applications and their dependencies into containers.

## Key Concepts

**Container**: Lightweight, standalone executable package with everything needed to run an application

**Image**: Blueprint for creating containers

**Registry**: Repository for storing and sharing Docker images

## Common Commands

```bash
# Build an image
docker build -t myapp:1.0 .

# Run a container
docker run -d -p 8080:8080 myapp:1.0

# View running containers
docker ps

# View all containers
docker ps -a

# Stop a container
docker stop <container_id>

# Remove a container
docker rm <container_id>

# Push to registry
docker push myregistry/myapp:1.0

# Pull from registry
docker pull myregistry/myapp:1.0
```

## Best Practices

1. **Use specific image versions** - Never use `latest` in production
2. **Minimize image size** - Use multi-stage builds
3. **Security scanning** - Scan images for vulnerabilities
4. **Non-root user** - Run containers with limited privileges
5. **Health checks** - Include health check instructions

## Dockerfile Example

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=3s \
  CMD python -c "import http.client; http.client.HTTPConnection('localhost:8000').request('GET', '/health')"

CMD ["gunicorn", "app:app"]
```

## Security Considerations

- Scan images for vulnerabilities
- Use minimal base images
- Don't run as root
- Use secrets management for credentials
- Keep images updated
