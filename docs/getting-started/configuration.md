# Workspace Configuration

This page covers the product-level configuration required for teams using `app.leanmote.com`.

## Organization setup checklist

- Organization profile and timezone are configured
- Team structure reflects your real operating model
- Role permissions are assigned (admin, manager, collaborator)
- Default dashboard access is reviewed

## Integration configuration

For each connected provider:

- Authorize with a service account that has stable access
- Limit scope to relevant repositories/projects/boards
- Validate webhook or sync permissions
- Confirm repository/project ownership mapping

## Data quality controls

To keep metrics trustworthy:

- Standardize workflow status mapping (for cycle/flow accuracy)
- Confirm pull request and issue identities map to the correct users
- Review inactive repositories and archive if no longer tracked
- Keep team membership current

## Dashboard configuration best practices

- Define a default date window for operational reviews
- Save team-level dashboard variants
- Share executive views separately from team-operational views
- Keep a standard KPI set across all engineering teams

## Access and governance

- Use least-privilege access for integrations
- Rotate API tokens regularly
- Audit new users and role changes monthly
- Review public-share links and disable unused ones

## API access

If your team consumes Leanmote metrics programmatically:

1. Generate API tokens in **Settings > API Tokens**
2. Store tokens in your secrets manager
3. Use [API Overview](../api/overview.md) and endpoint docs for integration

## Next

- [Using app.leanmote.com](../leanmote-app.md)
- [Architecture Overview](../architecture/overview.md)
- [Metrics Methodology](../metrics/how-we-measure.md)
