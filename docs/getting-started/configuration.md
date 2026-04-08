# Configuration

Complete guide to configuring Leanmote for your environment.

## Environment Variables

### Application Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `CI_ENVIRONMENT` | Environment mode (development, production) | production |
| `app.baseURL` | Base URL for the application | https://app.leanmote.com |
| `WORKER_ENVIRONMENT` | Enable worker mode for cron jobs | false |

### Database Configuration

| Variable | Description | Required |
|----------|-------------|----------|
| `database.default.hostname` | MySQL host | Yes |
| `database.default.database` | Database name | Yes |
| `database.default.username` | Database user | Yes |
| `database.default.password` | Database password | Yes |
| `database.default.port` | MySQL port | 3306 |
| `database.default.charset` | Character set | utf8mb4 |

### Email Configuration (AWS SES)

For production, use AWS SES credentials:

```bash
# Option 1: Direct credentials
email.protocol = smtp
email.SMTPHost = email-smtp.us-east-1.amazonaws.com
email.SMTPUser = YOUR_SES_USER
email.SMTPPass = YOUR_SES_PASSWORD
email.SMTPPort = 587
email.SMTPCrypto = tls
email.fromEmail = noreply@notify.leanmote.com
email.fromName = Leanmote
```

For AWS deployments, use Secrets Manager:

```bash
# Option 2: AWS Secrets Manager (JSON)
SES_CREDENTIALS_JSON = {"user_iam":"leanmote-app-ses-user","user_name":"AKIA...","user_password":"..."}
```

### Authentication (AWS Cognito)

| Variable | Description |
|----------|-------------|
| `COGNITO_REGION` | AWS region for Cognito |
| `COGNITO_USER_POOL_ID` | User pool ID |
| `COGNITO_CLIENT_ID` | App client ID |
| `COGNITO_CLIENT_SECRET` | App client secret |
| `COGNITO_REDIRECT_URI` | OAuth callback URL |

### Neo4j Configuration

For bottleneck insights:

| Variable | Description |
|----------|-------------|
| `NEO4J_CLIENT_ID` | OAuth client ID |
| `NEO4J_CLIENT_SECRET` | OAuth client secret |
| `NEO4J_AUTH_URL` | Token endpoint URL |
| `NEO4J_AUDIENCE` | API audience |

### Dashboard Data API

| Variable | Description |
|----------|-------------|
| `DASHBOARD_DATA_URL` | Lambda URL for metrics API |
| `DASHBOARD_DATA_AUTH_TOKEN` | API authentication token |

## Integration OAuth Credentials

Each integration requires OAuth credentials. Register your application with each provider and configure:

### GitHub

```bash
GITHUB_CLIENT_ID = your_client_id
GITHUB_CLIENT_SECRET = your_client_secret
```

### GitLab

```bash
GITLAB_CLIENT_ID = your_client_id
GITLAB_CLIENT_SECRET = your_client_secret
```

### Jira

```bash
JIRA_CLIENT_ID = your_client_id
JIRA_CLIENT_SECRET = your_client_secret
```

### Slack

```bash
SLACK_CLIENT_ID = your_client_id
SLACK_CLIENT_SECRET = your_client_secret
```

### Azure DevOps

```bash
AZURE_DEVOPS_CLIENT_ID = your_client_id
AZURE_DEVOPS_CLIENT_SECRET = your_client_secret
```

### Linear

```bash
LINEAR_CLIENT_ID = your_client_id
LINEAR_CLIENT_SECRET = your_client_secret
```

### ClickUp

```bash
CLICKUP_CLIENT_ID = your_client_id
CLICKUP_CLIENT_SECRET = your_client_secret
```

### Monday.com

```bash
MONDAY_CLIENT_ID = your_client_id
MONDAY_CLIENT_SECRET = your_client_secret
```

### Notion

```bash
NOTION_CLIENT_ID = your_client_id
NOTION_CLIENT_SECRET = your_client_secret
```

### Discord

```bash
DISCORD_CLIENT_ID = your_client_id
DISCORD_CLIENT_SECRET = your_client_secret
```

### Google Calendar

```bash
GOOGLE_CLIENT_ID = your_client_id
GOOGLE_CLIENT_SECRET = your_client_secret
```

## Production Configuration

### AWS Elastic Beanstalk

Environment variables are set via `.ebextensions/`:

```yaml
# .ebextensions/01-environment.config
option_settings:
  aws:elasticbeanstalk:application:environment:
    CI_ENVIRONMENT: production
    WORKER_ENVIRONMENT: "true"
```

### Secrets Management

Use AWS Secrets Manager for sensitive values:

```yaml
# .ebextensions/05-secrets.config
commands:
  01_get_secrets:
    command: |
      aws secretsmanager get-secret-value --secret-id leanmote/production --query SecretString --output text > /tmp/secrets.json
```

### Health Check

Configure the health check endpoint:

```
/health.php
```

This endpoint returns HTTP 200 when the application is healthy.

## Cron Configuration

### AWS Elastic Beanstalk Worker

For worker environments, configure cron in `cron.yaml`:

```yaml
version: 1
cron:
  - name: "distribute-executions"
    url: "/cron/distribute-executions/100"
    schedule: "*/5 * * * *"
  - name: "github-sync"
    url: "/github/get-repositories-data"
    schedule: "0 */2 * * *"
  - name: "jira-sync"
    url: "/jira/get-cron-project-issues/0/0/0/0"
    schedule: "0 */4 * * *"
```

### Standard Cron

For traditional cron setup:

```bash
# /etc/cron.d/leanmote
*/5 * * * * www-data php /var/www/html/public/index.php cron/distribute-executions/100
0 */2 * * * www-data php /var/www/html/public/index.php github/get-repositories-data
0 */4 * * * www-data php /var/www/html/public/index.php jira/get-cron-project-issues/0/0/0/0
```

## Security Configuration

### CORS

Configure allowed origins in your web server or application.

### Rate Limiting

API rate limits are enforced at the API Gateway level.

### Token Security

API tokens are hashed with SHA256 and a static salt. Never store plaintext tokens.

## Next Steps

- [Integrations Overview](../integrations/overview.md) - Set up data source connections
- [API Reference](../api/overview.md) - Use the Leanmote API
