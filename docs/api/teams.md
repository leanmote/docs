# Teams API

Endpoints for team lists, team details, and team-scoped members, tasks, and interactions.

## Endpoint Summary

| Method | Path | Description |
| --- | --- | --- |
| GET | `/v1/teams` | List teams visible to the authenticated user. |
| GET | `/v1/teams/{team_id}` | Get one team by id. |
| GET | `/v1/teams/{team_id}/members` | List team members. |
| GET | `/v1/teams/{team_id}/members/{user_id}` | Get one team member record. |
| GET | `/v1/teams/{team_id}/managers` | List team managers. |
| GET | `/v1/teams/{team_id}/tasks` | List tasks associated with a team. |
| GET | `/v1/teams/{team_id}/interactions` | List interaction activity for a team. |
| GET | `/v1/users/{user_id}/teams` | List teams linked to a user. |
| GET | `/v1/users/{user_id}/teams/{team_id}` | Get a user-to-team relation record. |

## Authentication

All endpoints require a bearer token.

```bash
Authorization: Bearer YOUR_API_TOKEN
```

Auth-failure behavior:

- Missing or invalid token returns `401 UNAUTHORIZED`.
- Access outside allowed organizations/teams returns `403 FORBIDDEN`.

## Pagination and Sorting

List endpoints support pagination and sorting.

| Parameter | Type | Default | Notes |
| --- | --- | --- | --- |
| `page` | integer | `1` | 1-based page number |
| `per_page` | integer | `50` | Max `100` |
| `sort` | string | `created_at` | Common values: `created_at`, `name` |
| `order` | string | `desc` | `asc` or `desc` |

## Example: List Teams

### Request

```bash
curl -X GET "https://api.leanmote.com/v1/teams?page=1&per_page=25" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

### Success Response (`200 OK`)

```json
{
  "status": "success",
  "data": [
    {
      "id": "team_77",
      "organization_id": "org_123",
      "name": "Platform",
      "created_at": "2026-02-01T08:00:00Z"
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

## Example: List Team Members

### Request

```bash
curl -X GET "https://api.leanmote.com/v1/teams/team_77/members?page=1&per_page=50" \
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
      "title": "Engineering Manager"
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
    "message": "You do not have permission to access this team"
  }
}
```

### `404 Not Found`

```json
{
  "status": "error",
  "error": {
    "code": "NOT_FOUND",
    "message": "Team not found"
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

All team endpoints inherit platform rate limits:

- `1000` requests/hour
- `100` requests/minute burst

Headers included in responses:

- `X-RateLimit-Limit`
- `X-RateLimit-Remaining`
- `X-RateLimit-Reset`

## Related Reference

- [API Overview](overview.md)
- [Authentication](authentication.md)
- [Users API](users.md)
- [API-LEANMOTE Endpoints](api-leanmote.md)
