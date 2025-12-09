# Slack

## Overview

Slack integration for team communication and notifications.

## Integration Setup

### Webhooks

For sending messages:

```bash
# Send message
curl -X POST -H 'Content-type: application/json' \
  --data '{"text":"Hello World"}' \
  https://hooks.slack.com/services/YOUR/WEBHOOK/URL
```

### Bots

For advanced interactions:
- Oauth authentication
- Command handling
- Event listening
- Interactive buttons

## Common Integrations

### Monitoring Alerts

Send alerts from monitoring systems:
- Prometheus
- Grafana
- CloudWatch
- DataDog

### CI/CD Notifications

Build and deployment status:
- Jenkins
- GitHub Actions
- GitLab CI
- CircleCI

### Incident Management

Alert escalation and status:
- PagerDuty
- OpsGenie
- StatusPage

## Message Formatting

### Simple Message

```json
{
  "text": "Deployment completed successfully"
}
```

### Rich Formatting

```json
{
  "blocks": [
    {
      "type": "header",
      "text": {
        "type": "plain_text",
        "text": "Deployment Status"
      }
    },
    {
      "type": "section",
      "fields": [
        {
          "type": "mrkdwn",
          "text": "*Status:*\nSuccess"
        },
        {
          "type": "mrkdwn",
          "text": "*Time:*\n2025-12-09 10:30 UTC"
        }
      ]
    }
  ]
}
```

## Channel Organization

```
#general - General discussion
#incidents - Active incidents
#alerts - Monitoring alerts
#deployments - Deployment notifications
#devops - DevOps team channel
#random - Off-topic discussion
```

## Bot Commands

Common command patterns:

```
/deploy app=myapp environment=production
/status service=auth
/logs service=api filter=error
/alert add rule=HighErrorRate threshold=5%
```

## Best Practices

1. **Appropriate channels** - Route messages correctly
2. **Concise messages** - Get to the point
3. **Threading** - Keep conversations organized
4. **Do not disturb** - Respect quiet hours
5. **Archive important** - Save decisions and info
6. **Avoid @channel** - Use sparingly

## Notification Strategy

| Priority | Channel | Format |
|----------|---------|--------|
| Critical | Thread + @oncall | Full details |
| High | #incidents | Summary + link |
| Medium | #alerts | Alert only |
| Low | Digest | Daily summary |

## Security

- Rotate webhooks regularly
- Use IAM roles for auth
- Mask sensitive data
- Audit bot activities
- Limit bot permissions

