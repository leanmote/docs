# Dashboards API

Endpoints for dashboard view catalogs, view lists, and user-scoped dashboard access.

## Endpoint Summary

| Method | Path | Description |
| --- | --- | --- |
| GET | `/v1/dashboard-views` | List available dashboard views. |
| GET | `/v1/dashboard-views/{view_id}` | Get a dashboard view by id. |
| GET | `/v1/dashboard-views/catalog` | List built-in dashboard templates/types. |
| GET | `/v1/dashboard-views/custom-filters` | List reusable custom filters. |
| GET | `/v1/users/{user_id}/dashboard-views` | List dashboard views for a user. |
| GET | `/v1/users/{user_id}/dashboard-views/{view_id}` | Get one user dashboard view. |

## Authentication

All endpoints require a bearer token.

```bash
Authorization: Bearer YOUR_API_TOKEN
```

Auth-failure behavior:

- Missing or invalid tokens return `401 UNAUTHORIZED`.
- Token without access to the dashboard/user resource returns `403 FORBIDDEN`.

## Pagination and Sorting

List endpoints support pagination and sorting.

| Parameter | Type | Default | Notes |
| --- | --- | --- | --- |
| `page` | integer | `1` | 1-based page number |
| `per_page` | integer | `50` | Max `100` |
| `sort` | string | `created_at` | Typical values: `created_at`, `name` |
| `order` | string | `desc` | `asc` or `desc` |

## Example: List Dashboard Views

### Request

```bash
curl -X GET "https://api.leanmote.com/v1/dashboard-views?page=1&per_page=20" \
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
      "type": "overview",
      "created_at": "2026-02-01T08:00:00Z"
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

## Example: Get Dashboard Catalog

### Request

```bash
curl -X GET "https://api.leanmote.com/v1/dashboard-views/catalog" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

### Success Response (`200 OK`)

```json
{
  "status": "success",
  "data": [
    {
      "key": "overview",
      "name": "Overview Dashboard",
      "description": "High-level health and delivery metrics"
    }
  ]
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
    "message": "You do not have permission to access this dashboard resource"
  }
}
```

### `404 Not Found`

```json
{
  "status": "error",
  "error": {
    "code": "NOT_FOUND",
    "message": "Dashboard view not found"
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

All dashboard endpoints inherit platform rate limits:

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
- [METRICS-TEST Endpoints](metrics-test.md)
