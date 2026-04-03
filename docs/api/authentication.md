# Authentication

All Leanmote API requests require authentication using bearer tokens.

## Overview

Leanmote uses token-based authentication for API access. Tokens are:

- Generated through the web application
- Hashed with SHA256 before storage
- Can have optional expiration dates
- Tracked for last usage (audit trail)

## Obtaining an API Token

### Via Web Application

1. Log in to Leanmote at `https://app.leanmote.com`
2. Navigate to **Settings > API Tokens**
3. Click **Generate New Token**
4. Optionally set an expiration date
5. Copy the token immediately (it won't be shown again)

### Via Admin API

Administrators can generate tokens for users:

```
POST /v1/admin/users/{user_id}/api-credentials
```

## Using Your Token

Include the token in the `Authorization` header:

```bash
curl -H "Authorization: Bearer YOUR_API_TOKEN" \
     https://api.leanmote.com/v1/organizations
```

### Header Format

```
Authorization: Bearer {token}
```

**Important:** The `Bearer` prefix is required. Tokens without this prefix will be rejected.

## Token Validation Process

When a request is received:

1. Extract token from `Authorization` header
2. Compute `SHA256(token + salt)`
3. Query `api_tokens` table by hash
4. Verify `is_active = true`
5. Check `expires_at` (if set) has not passed
6. Update `last_used_at` timestamp
7. Enrich request with user context

## Token Properties

| Property | Type | Description |
|----------|------|-------------|
| `token_hash` | string | SHA256 hash (stored) |
| `token_preview` | string | First 5 characters (for identification) |
| `is_active` | boolean | Whether token is active |
| `expires_at` | datetime | Optional expiration date |
| `last_used_at` | datetime | Last usage timestamp |
| `created_at` | datetime | Creation timestamp |

## Error Responses

### Missing Token

**Status:** 401 Unauthorized

```json
{
  "status": "error",
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Missing authentication token"
  }
}
```

### Invalid Token

**Status:** 401 Unauthorized

```json
{
  "status": "error",
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid authentication token"
  }
}
```

### Expired Token

**Status:** 401 Unauthorized

```json
{
  "status": "error",
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication token has expired"
  }
}
```

### Inactive Token

**Status:** 401 Unauthorized

```json
{
  "status": "error",
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication token is inactive"
  }
}
```

## Token Management

### List User Tokens

Administrators can list tokens for a user:

```
GET /v1/admin/users/{user_id}/api-credentials
```

**Response:**

```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "token_preview": "abc12",
      "is_active": true,
      "expires_at": "2025-12-31T23:59:59Z",
      "last_used_at": "2024-06-15T10:30:00Z",
      "created_at": "2024-01-01T00:00:00Z"
    }
  ]
}
```

### Create Token

```
POST /v1/admin/users/{user_id}/api-credentials
```

**Request Body:**

```json
{
  "expires_at": "2025-12-31T23:59:59Z"
}
```

**Response:**

```json
{
  "status": "success",
  "data": {
    "token": "full-token-only-shown-once",
    "token_preview": "full-",
    "expires_at": "2025-12-31T23:59:59Z",
    "created_at": "2024-06-15T10:30:00Z"
  }
}
```

**Important:** The full token is only returned in the creation response. Store it securely.

### Revoke Token

To deactivate a token, update its `is_active` status via the web application.

## Security Best Practices

### Token Storage

- **Never** commit tokens to version control
- Use environment variables or secrets managers
- Rotate tokens periodically
- Use separate tokens for different environments

### Token Scope

- Tokens inherit the user's permissions
- Create dedicated service accounts for automated access
- Use the minimum required permissions

### Monitoring

- Review `last_used_at` for unusual activity
- Set expiration dates for temporary access
- Revoke tokens when no longer needed

## Rate Limiting

Authenticated requests are subject to rate limiting:

| Limit Type | Value |
|------------|-------|
| Per Hour | 1000 requests |
| Per Minute | 100 requests (burst) |

Rate limit headers are included in responses:

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 950
X-RateLimit-Reset: 1609459200
```

## Examples

### cURL

```bash
# Set token as environment variable
export LEANMOTE_TOKEN="your-api-token"

# Make authenticated request
curl -H "Authorization: Bearer $LEANMOTE_TOKEN" \
     https://api.leanmote.com/v1/organizations
```

### Python

```python
import requests
import os

token = os.environ.get('LEANMOTE_TOKEN')

response = requests.get(
    'https://api.leanmote.com/v1/organizations',
    headers={'Authorization': f'Bearer {token}'}
)

data = response.json()
```

### JavaScript

```javascript
const token = process.env.LEANMOTE_TOKEN;

const response = await fetch('https://api.leanmote.com/v1/organizations', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});

const data = await response.json();
```

## Next Steps

- [API Overview](overview.md) - General API information
- [Organizations API](organizations.md) - Organization endpoints
- [Error Handling](overview.md#error-codes) - Error code reference
