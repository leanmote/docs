# Organizations API

Endpoints for listing organizations and retrieving organization-scoped resources.

## Endpoint Summary

| Method | Path | Description |
| --- | --- | --- |
| GET | `/v1/organizations` | List organizations visible to the authenticated user. |
| GET | `/v1/organizations/{organization_id}` | Get one organization by id. |
| GET | `/v1/organizations/{organization_id}/members` | List organization members. |
| GET | `/v1/organizations/{organization_id}/teams` | List teams in an organization. |
| GET | `/v1/organizations/{organization_id}/apps` | List apps connected to an organization. |
| GET | `/v1/users/{user_id}/organizations` | List organizations linked to a user. |

## Authentication

All endpoints require a bearer token.

```bash
Authorization: Bearer YOUR_API_TOKEN
```

Auth-failure behavior:

- Missing, invalid, expired, or inactive tokens return `401 UNAUTHORIZED`.
- Valid token without access to the organization returns `403 FORBIDDEN`.

## Pagination and Sorting

List endpoints support pagination and sorting.

| Parameter | Type | Default | Notes |
| --- | --- | --- | --- |
| `page` | integer | `1` | 1-based page number |
| `per_page` | integer | `50` | Max `100` |
| `sort` | string | `created_at` | Field support varies by endpoint |
| `order` | string | `desc` | `asc` or `desc` |

## Example: List Organizations

### Request

```bash
curl -X GET "https://api.leanmote.com/v1/organizations?page=1&per_page=25&sort=name&order=asc" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

### Success Response (`200 OK`)

```json
{
  "status": "success",
  "data": [
    {
      "id": "org_123",
      "name": "Leanmote",
      "created_at": "2026-01-15T14:22:10Z"
    }
  ],
  "meta": {
    "page": 1,
    "per_page": 25,
    "total": 1,
    "total_pages": 1,
    "sort": "name",
    "order": "asc"
  }
}
```

## Example: Get Organization Members

### Request

```bash
curl -X GET "https://api.leanmote.com/v1/organizations/org_123/members?page=1&per_page=50" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

### Success Response (`200 OK`)

```json
{
  "status": "success",
  "data": [
    {
      "id": "usr_42",
      "name": "Jane Doe",
      "email": "jane@example.com",
      "role": "manager"
    }
  ],
  "meta": {
    "page": 1,
    "per_page": 50,
    "total": 1,
    "total_pages": 1
  }
}
```

## Common Error Responses

### `401 Unauthorized`

```json
{
  "status": "error",
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid or missing authentication token"
  }
}
```

### `403 Forbidden`

```json
{
  "status": "error",
  "error": {
    "code": "FORBIDDEN",
    "message": "You do not have permission to access this organization"
  }
}
```

### `404 Not Found`

```json
{
  "status": "error",
  "error": {
    "code": "NOT_FOUND",
    "message": "Organization not found"
  }
}
```

### `429 Too Many Requests`

```json
{
  "status": "error",
  "error": {
    "code": "RATE_LIMITED",
    "message": "Rate limit exceeded"
  }
}
```

## Rate-Limit Behavior

All organization endpoints inherit platform rate limits:

- `1000` requests/hour
- `100` requests/minute burst

Responses include:

- `X-RateLimit-Limit`
- `X-RateLimit-Remaining`
- `X-RateLimit-Reset`

## Related Reference

- [API Overview](overview.md)
- [Authentication](authentication.md)
- [Teams API](teams.md)
- [API-LEANMOTE Endpoints](api-leanmote.md)
