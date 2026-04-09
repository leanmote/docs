# Users API

Endpoints for listing users and retrieving user-scoped apps, dashboards, organizations, and teams.

## Endpoint Summary

| Method | Path | Description |
| --- | --- | --- |
| GET | `/v1/users` | List users visible to the authenticated user. |
| GET | `/v1/users/{user_id}` | Get one user by id. |
| GET | `/v1/users/{user_id}/apps` | List apps linked to a user. |
| GET | `/v1/users/{user_id}/apps/{app_id}` | Get one user app binding. |
| GET | `/v1/users/{user_id}/dashboard-views` | List dashboard views available to a user. |
| GET | `/v1/users/{user_id}/dashboard-views/{view_id}` | Get one user dashboard view. |
| GET | `/v1/users/{user_id}/interactions` | List interaction activity for a user. |
| GET | `/v1/users/{user_id}/organizations` | List organizations linked to a user. |
| GET | `/v1/users/{user_id}/teams` | List teams linked to a user. |
| GET | `/v1/users/{user_id}/teams/{team_id}` | Get one user/team relation record. |
| GET | `/v1/users/{user_id}/git/apps` | List git integrations linked to a user. |

## Authentication

All endpoints require a bearer token.

```bash
Authorization: Bearer YOUR_API_TOKEN
```

Auth-failure behavior:

- Missing, invalid, expired, or inactive tokens return `401 UNAUTHORIZED`.
- Token is valid but unauthorized for the user record returns `403 FORBIDDEN`.

## Pagination and Sorting

List endpoints support pagination and sorting.

| Parameter | Type | Default | Notes |
| --- | --- | --- | --- |
| `page` | integer | `1` | 1-based page number |
| `per_page` | integer | `50` | Max `100` |
| `sort` | string | `created_at` | Typical values: `created_at`, `name`, `email` |
| `order` | string | `desc` | `asc` or `desc` |

## Example: List Users

### Request

```bash
curl -X GET "https://api.leanmote.com/v1/users?page=1&per_page=25&sort=name&order=asc" \
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
      "created_at": "2026-02-01T08:00:00Z"
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

## Example: Get User Dashboard Views

### Request

```bash
curl -X GET "https://api.leanmote.com/v1/users/usr_42/dashboard-views?page=1&per_page=20" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

### Success Response (`200 OK`)

```json
{
  "status": "success",
  "data": [
    {
      "id": "view_21",
      "name": "Engineering Summary",
      "visibility": "private"
    }
  ],
  "meta": {
    "page": 1,
    "per_page": 20,
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
    "message": "You do not have permission to access this user"
  }
}
```

### `404 Not Found`

```json
{
  "status": "error",
  "error": {
    "code": "NOT_FOUND",
    "message": "User not found"
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

All user endpoints inherit platform rate limits:

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
- [Dashboards API](dashboards.md)
- [API-LEANMOTE Endpoints](api-leanmote.md)
