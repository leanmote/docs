# API Overview

The Leanmote API provides programmatic access to engineering metrics, teams, and dashboard data.

## Base URL

```
https://api.leanmote.com/v1
```

## Authentication

All API requests require authentication via bearer token:

```bash
curl -H "Authorization: Bearer YOUR_API_TOKEN" \
     https://api.leanmote.com/v1/organizations
```

### Obtaining an API Token

1. Log in to Leanmote
2. Navigate to Settings > API Tokens
3. Click "Generate New Token"
4. Copy and securely store the token (it won't be shown again)

### Token Security

- Tokens are hashed with SHA256 before storage
- Tokens can have expiration dates
- Last usage is tracked for auditing

## Response Format

All responses follow a standard envelope:

### Success Response

```json
{
  "status": "success",
  "data": [...],
  "meta": {
    "page": 1,
    "per_page": 50,
    "total": 100,
    "total_pages": 2,
    "sort": "created_at",
    "order": "desc"
  }
}
```

### Error Response

```json
{
  "status": "error",
  "error": {
    "code": "NOT_FOUND",
    "message": "Resource not found"
  }
}
```

## Pagination

Use query parameters for pagination:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `page` | Page number | 1 |
| `per_page` | Results per page (max 100) | 50 |

Example:

```bash
GET /v1/teams?page=2&per_page=25
```

## Sorting

Control result ordering:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `sort` | Field to sort by | varies |
| `order` | Sort direction (asc, desc) | desc |

Example:

```bash
GET /v1/users?sort=name&order=asc
```

## Filtering

### Field Selection

Request specific fields:

```bash
GET /v1/users?fields=id,name,email
```

### Date Range Filtering

Filter by timestamps using bracket notation:

```bash
GET /v1/commits?created_at[gte]=2024-01-01&created_at[lte]=2024-12-31
```

Supported operators:
- `[eq]` - Equal to
- `[gte]` - Greater than or equal
- `[lte]` - Less than or equal
- `[gt]` - Greater than
- `[lt]` - Less than

## Rate Limiting

API requests are rate limited:

- **Standard:** 1000 requests/hour
- **Burst:** 100 requests/minute

Rate limit headers are included in responses:

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 950
X-RateLimit-Reset: 1609459200
```

## Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `UNAUTHORIZED` | 401 | Invalid or missing token |
| `FORBIDDEN` | 403 | Insufficient permissions |
| `NOT_FOUND` | 404 | Resource not found |
| `VALIDATION_ERROR` | 400 | Invalid request parameters |
| `RATE_LIMITED` | 429 | Too many requests |
| `SERVER_ERROR` | 500 | Internal server error |

## API Endpoints

### Organizations
- `GET /organizations` - List organizations
- `GET /organizations/{id}` - Get organization
- `GET /organizations/{id}/members` - List members
- `GET /organizations/{id}/teams` - List teams

### Teams
- `GET /teams` - List teams
- `GET /teams/{id}` - Get team
- `GET /teams/{id}/members` - List team members
- `GET /teams/{id}/tasks` - List team tasks

### Users
- `GET /users` - List users
- `GET /users/{id}` - Get user
- `GET /users/{id}/teams` - List user's teams
- `GET /users/{id}/interactions` - List user interactions

### Git
- `GET /git/apps` - List git integrations
- `GET /git/apps/{id}/repositories` - List repositories
- `GET /git/apps/{id}/repositories/{repo}/commits` - List commits
- `GET /git/apps/{id}/repositories/{repo}/pull-requests` - List PRs

### Dashboards
- `GET /dashboard-views` - List dashboards
- `GET /dashboard-views/{id}` - Get dashboard
- `GET /dashboard-views/catalog` - List available dashboard types

## SDKs and Libraries

Official SDKs coming soon:
- Python
- JavaScript/TypeScript
- Go

## Webhooks

Leanmote supports outgoing webhooks for real-time notifications. Configure webhooks in Settings > Integrations > Webhooks.

## Next Steps

- [Authentication](authentication.md) - Detailed auth guide
- [Organizations API](organizations.md) - Organization endpoints
- [Teams API](teams.md) - Team endpoints
- [Users API](users.md) - User endpoints


## Complete Endpoint Catalogs

- [API-LEANMOTE Endpoints](api-leanmote.md)
- [METRICS-TEST Endpoints](metrics-test.md)

