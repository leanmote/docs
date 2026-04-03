# Security

- API access is token-based (`Authorization` header) and organization-scoped.
- API-AUTH validates credentials and injects identity context for downstream authorization.
- Sensitive DB access is handled in backend services only; no direct client DB access.
- Production deployments should enforce TLS, least-privilege IAM, and secret rotation.
