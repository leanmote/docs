# Data Model

Leanmote data is centered on organizations, teams, users, tasks, interactions, repositories, and metric aggregates.

- Organization is the top-level tenant boundary.
- Teams and users are scoped by organization.
- Tasks, boards, interactions, calendars, and git artifacts feed metrics pipelines.
- API services read normalized tables and return domain envelopes with pagination metadata.
