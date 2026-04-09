# Integrations

Integrations are the foundation of Leanmote. The better your integration setup, the more trustworthy your engineering intelligence.

## Productivity Tools in practice

From **Settings > Productivity Tools**, connect providers used by your engineering organization:

- Source control (GitHub, GitLab, Azure DevOps)
- Planning and boards (Jira, Linear, ClickUp, Azure Boards)
- Collaboration tools (Slack and others)

Start with your highest-signal systems, then expand coverage.

## Permissions and security commitments

Leanmote follows a least-privilege model:

- Request only required scopes for analytics.
- Use role-based controls to limit who can manage integrations.
- Encrypt credentials and tokens at rest.
- Keep auditability through sync logs and access activity.

For security architecture context, see [Architecture Security](../architecture/security.md).

## Backfill and historical sync limits

Historical sync depth depends on your plan and provider constraints.

- Smaller plans prioritize fast operational onboarding.
- Expanded plans support deeper historical trend analysis.
- Initial import duration varies by repository/project volume.

## Status mapping for board tools

Status mapping is essential for flow metrics like cycle time, WIP age, and throughput.

When mapping statuses (especially in Jira-like workflows):

- Group statuses into consistent lifecycle stages.
- Normalize equivalent states across teams.
- Review mappings whenever workflows change.

A clean mapping model improves dashboard comparability across squads.

## Integration rollout checklist

1. Connect provider and authorize scopes.
2. Select repositories/projects/boards in scope.
3. Configure status mapping where applicable.
4. Validate first sync and data freshness.
5. Confirm user identity mapping in administration.

## Related docs

- [Integrations Overview](../integrations/overview.md)
- [Jira Integration](../integrations/jira.md)
- [Users and Teams Management](users-and-teams.md)
