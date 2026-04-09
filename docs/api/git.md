# Git API

Endpoints for retrieving configured source-control integrations.

## Endpoint Summary

| Method | Path | Description |
| --- | --- | --- |
| GET | `/v1/git/apps` | List git integrations available to the authenticated user. |
| GET | `/v1/users/{user_id}/git/apps` | List git integrations linked to a specific user. |

## Authentication

All endpoints require a bearer token.

```bash
Authorization: Bearer YOUR_API_TOKEN
```

Auth-failure behavior:

- Missing or invalid token returns `401 UNAUTHORIZED`.
- Accessing another user's integrations without permission returns `403 FORBIDDEN`.

## Pagination and Sorting

List endpoints support pagination and sorting.

| Parameter | Type | Default | Notes |
| --- | --- | --- | --- |
| `page` | integer | `1` | 1-based page number |
| `per_page` | integer | `50` | Max `100` |
| `sort` | string | `created_at` | Typical values: `created_at`, `name` |
| `order` | string | `desc` | `asc` or `desc` |

## Example: List Git Integrations

### Request

```bash
curl -X GET "https://api.leanmote.com/v1/git/apps?page=1&per_page=25" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

### Success Response (`200 OK`)

```json
{
  "status": "success",
  "data": [
    {
      "id": "app_gh_1",
      "provider": "github",
      "name": "GitHub Enterprise",
      "is_active": true
    }
  ],
  "meta": {
    "page": 1,
    "per_page": 25,
    "total": 1,
    "total_pages": 1
  }
}
```

## Example: List User Git Integrations

### Request

```bash
curl -X GET "https://api.leanmote.com/v1/users/usr_42/git/apps?page=1&per_page=25" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

### Success Response (`200 OK`)

```json
{
  "status": "success",
  "data": [
    {
      "id": "app_gh_1",
      "provider": "github",
      "name": "GitHub Enterprise",
      "user_id": "usr_42"
    }
  ],
  "meta": {
    "page": 1,
    "per_page": 25,
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
    "message": "You do not have permission to access this git integration resource"
  }
}
```

### `404 Not Found`

```json
{
  "status": "error",
  "error": {
    "code": "NOT_FOUND",
    "message": "Git integration resource not found"
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

All git endpoints inherit platform rate limits:

- `1000` requests/hour
- `100` requests/minute burst

Responses include:

- `X-RateLimit-Limit`
- `X-RateLimit-Remaining`
- `X-RateLimit-Reset`

## Related Reference

- [API Overview](overview.md)
- [Authentication](authentication.md)
- [Users API](users.md)
- [API-LEANMOTE Endpoints](api-leanmote.md)
