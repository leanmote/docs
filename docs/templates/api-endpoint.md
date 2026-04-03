# {Endpoint Name}

{Brief description of what this endpoint does.}

## Endpoint

```
{METHOD} /v1/{path}
```

## Authentication

This endpoint requires authentication.

```bash
Authorization: Bearer YOUR_API_TOKEN
```

## Parameters

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The resource ID |

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `page` | integer | 1 | Page number |
| `per_page` | integer | 50 | Results per page (max 100) |
| `sort` | string | created_at | Field to sort by |
| `order` | string | desc | Sort order (asc, desc) |

### Request Body

```json
{
  "field_name": "value",
  "another_field": 123
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `field_name` | string | Yes | Description |
| `another_field` | integer | No | Description |

## Response

### Success Response

**Code:** 200 OK

```json
{
  "status": "success",
  "data": {
    "id": "abc123",
    "name": "Example",
    "created_at": "2024-01-01T00:00:00Z"
  }
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique identifier |
| `name` | string | Resource name |
| `created_at` | datetime | Creation timestamp |

### Error Responses

**Code:** 401 Unauthorized

```json
{
  "status": "error",
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid or missing authentication token"
  }
}
```

**Code:** 404 Not Found

```json
{
  "status": "error",
  "error": {
    "code": "NOT_FOUND",
    "message": "Resource not found"
  }
}
```

## Examples

### cURL

```bash
curl -X GET "https://api.leanmote.com/v1/{path}" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json"
```

### Python

```python
import requests

response = requests.get(
    "https://api.leanmote.com/v1/{path}",
    headers={"Authorization": "Bearer YOUR_API_TOKEN"}
)
data = response.json()
```

### JavaScript

```javascript
const response = await fetch('https://api.leanmote.com/v1/{path}', {
  headers: {
    'Authorization': 'Bearer YOUR_API_TOKEN'
  }
});
const data = await response.json();
```

## Related Endpoints

- [Related Endpoint 1](related-1.md)
- [Related Endpoint 2](related-2.md)
