# Workspace Configuration

Use this checklist after first login to ensure your Leanmote workspace is reliable at scale.

## Organization baseline

- Configure organization name, timezone, and reporting defaults.
- Confirm admin and manager access levels.
- Review who can edit integrations, teams, and settings.

## Integration baseline

For each connected provider:

- Authorize with a stable service account.
- Scope sync to active repositories/projects/boards.
- Validate webhook or polling health.
- Confirm expected data backfill period for your plan.

## Mapping baseline

- Configure status mapping for board workflows.
- Verify user identity mapping across tools.
- Validate team ownership for repositories and projects.

## Dashboard baseline

- Save default filters (time range, teams, scope).
- Define standard KPI views for managers and executives.
- Share dashboard presets by role.

## Governance baseline

- Rotate API tokens on a defined schedule.
- Audit role/access changes regularly.
- Use SCIM/SSO for enterprise identity governance when required.

## Continue

- [Administration Overview](../administration/overview.md)
- [Dashboards Overview](../dashboards/overview.md)
- [API Overview](../api/overview.md)
