# Integrations Overview

Leanmote integrates with 20+ development and collaboration tools to provide comprehensive engineering analytics.

## Supported Integrations

### Source Control

| Platform | OAuth | Webhooks | Data Collected |
|----------|-------|----------|----------------|
| GitHub | Yes | Yes | Repos, commits, PRs, reviews, comments |
| GitLab | Yes | Yes | Projects, MRs, discussions, approvals |
| Bitbucket | Yes | No | Repos, commits, PRs |
| Azure DevOps | Yes | Yes | Repos, commits, PRs |

### Project Management

| Platform | OAuth | Webhooks | Data Collected |
|----------|-------|----------|----------------|
| Jira | Yes | Yes | Projects, issues, sprints, history |
| Linear | Yes | Yes | Teams, issues, cycles |
| ClickUp | Yes | Yes | Spaces, lists, tasks |
| Monday.com | Yes | Yes | Boards, items, updates |
| Notion | Yes | Yes | Databases, pages |
| Azure DevOps Boards | Yes | Yes | Work items, sprints |

### Communication

| Platform | OAuth | Webhooks | Data Collected |
|----------|-------|----------|----------------|
| Slack | Yes | Yes | Channels, messages, reactions |
| Discord | Yes | No | Servers, channels, messages |

### Calendar

| Platform | OAuth | Webhooks | Data Collected |
|----------|-------|----------|----------------|
| Google Calendar | Yes | No | Events, attendees, duration |

### AI Tools

| Platform | OAuth | API | Data Collected |
|----------|-------|-----|----------------|
| GitHub Copilot | N/A | Yes | Usage metrics, completions |
| Claude Code | N/A | OTLP | Sessions, tokens, commands |

## Integration Architecture

### OAuth Flow

```
1. User clicks "Connect {Platform}"
2. Redirect to provider's OAuth consent screen
3. User authorizes Leanmote
4. Provider redirects to callback URL with auth code
5. Leanmote exchanges code for access/refresh tokens
6. Tokens encrypted and stored in user_apps_credentials
7. Initial data sync triggered
```

### Data Synchronization

**Pull-based (Cron):**
- Scheduled jobs fetch data from provider APIs
- Incremental sync using `last_update` timestamps
- Results stored in normalized tables

**Push-based (Webhooks):**
- Provider sends events in real-time
- Lambda validates and processes events
- Immediate database updates

### Token Refresh

OAuth tokens are automatically refreshed:

- Access tokens: Short-lived (1 hour typically)
- Refresh tokens: Long-lived (30-90 days)
- Automatic refresh before expiration
- Failed refresh triggers re-authorization prompt

## Connecting an Integration

### Step 1: Navigate to Integrations

1. Log in to Leanmote
2. Go to Settings > Integrations
3. Find the platform you want to connect

### Step 2: Authorize Access

1. Click "Connect"
2. Authorize Leanmote on the provider's consent screen
3. Select the resources to share (repos, projects, etc.)
4. Click "Authorize"

### Step 3: Configure Data Sources

1. After authorization, select which resources to track
2. For GitHub: Choose repositories
3. For Jira: Choose projects
4. For Slack: Choose channels
5. Click "Save Configuration"

### Step 4: Initial Sync

1. Leanmote queues initial data extraction
2. Progress shown in Integration Status
3. Historical data imported based on plan limits
4. Ongoing sync begins automatically

## Data Mapping

### User Identity Mapping

Leanmote maps user identities across platforms:

| Platform | User Identifier |
|----------|-----------------|
| GitHub | username, email |
| GitLab | username, email |
| Jira | account ID, email |
| Slack | user ID, email |

Mapping is stored in `user_apps_mapping` table.

### Interaction Recording

Cross-platform interactions are normalized:

| Source | Interaction Type |
|--------|------------------|
| PR comment | Code review |
| MR approval | Code review |
| Slack mention | Communication |
| Jira assignment | Task assignment |

## Rate Limits

Each provider has API rate limits:

| Provider | Rate Limit | Reset |
|----------|------------|-------|
| GitHub | 5000/hour | Hourly |
| GitLab | 1000/hour | Hourly |
| Jira | 1000/hour | Hourly |
| Slack | Varies | Per endpoint |

Leanmote throttles requests to stay within limits.

## Troubleshooting

### Connection Failed

1. Check OAuth credentials are correct
2. Verify redirect URI matches exactly
3. Ensure required scopes are granted
4. Check provider status page

### Data Not Syncing

1. Check Integration Status for errors
2. Verify credentials haven't expired
3. Check cron job logs
4. Ensure selected resources exist

### Missing Historical Data

1. Initial sync may take time
2. Check plan limits for history depth
3. Verify resource was active during period

## Security

### Token Storage

- Tokens encrypted at rest
- Stored in `user_apps_credentials` table
- Accessed only during sync operations

### Permissions

Each integration requests minimal required scopes:

| Platform | Scopes |
|----------|--------|
| GitHub | repo (read), user (read) |
| GitLab | read_api, read_user |
| Jira | read:jira-work, read:jira-user |
| Slack | channels:read, users:read |

### Data Retention

- Active integrations: Data synced continuously
- Disconnected integrations: Historical data retained
- Account deletion: All data permanently removed

## Platform-Specific Guides

- [GitHub Integration](github.md)
- [GitLab Integration](gitlab.md)
- [Jira Integration](jira.md)
- [Slack Integration](slack.md)
- [Azure DevOps Integration](azure-devops.md)
