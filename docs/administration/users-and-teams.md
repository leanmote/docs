# Users and Teams Management

This section explains how Leanmote keeps cross-tool identities coherent so metrics are attributed to the right people and teams.

## User invitations and onboarding

Admins can invite users directly to the organization and assign roles based on responsibilities.

Recommended onboarding pattern:

1. Invite user.
2. Assign access level.
3. Confirm team membership.
4. Validate identity mapping after first sync.

## What user mapping means

User mapping is Leanmote's method for unifying one person across multiple tools.

Example: the same engineer may appear with different identifiers in GitHub, Jira, and Slack. Mapping links those identities into one analytics profile.

Strong mapping improves:

- Team throughput attribution
- Review collaboration analysis
- Capacity and ownership visibility

## LinkedIn role scraping

Leanmote can enrich organizational context by scraping role/title signals from LinkedIn profiles when enabled.

Use cases:

- Segment reporting by role cohorts.
- Improve manager-level visibility.
- Support executive strategic views.

This enrichment should be enabled according to your privacy and compliance policies.

## Access levels

Typical access layers include:

- Admin: full configuration and governance controls.
- Manager: team and dashboard operational ownership.
- Member/Contributor: dashboard consumption and limited interaction.

Design access so teams can operate quickly without overexposing administration controls.

## Teams management

Creating and maintaining teams unlocks clearer dashboards and accountability:

- Group metrics by delivery ownership.
- Compare squads with consistent scope.
- Route dashboard reviews to the right leaders.

Maintain team membership whenever org structure changes.

## Enterprise identity: SCIM and SSO

Enterprise teams can enable SCIM and SSO for stronger identity governance.

- SCIM supports user provisioning/deprovisioning automation.
- SSO centralizes authentication and policy enforcement.

See [SCIM Handler](../api/scim-handler.md) for integration direction.
