# Metrics Methodology

This page explains how Leanmote measures engineering performance at a practical level.

## Measurement principles

- Use system-of-record events from connected tools
- Preserve event timestamps to calculate lead and cycle durations
- Keep metrics filterable by team, repository/project, and date window
- Prioritize consistency over vanity metrics

## DORA metrics

### Deployment frequency
How often production deployments occur in the selected period.

### Lead time for changes
Elapsed time from code change to production deployment.

### Mean time to recovery (MTTR)
Average time to restore normal operation after a production incident.

### Change failure rate
Percentage of deployments that lead to incidents, rollbacks, or hotfixes.

## Flow metrics

### Throughput
Number of completed work items over a defined time period.

### Cycle time
Time from work start to completion.

### Work in progress (WIP)
Number of items actively in progress during the selected window.

## Team execution and collaboration metrics

Leanmote also tracks operational behaviors that explain performance outcomes:

- Review participation and review latency patterns
- Activity distribution across teams and repositories
- Execution consistency across planning and delivery systems

## Good interpretation habits

- Compare trends, not isolated single-week numbers
- Segment by team to avoid mixed-signal conclusions
- Investigate sharp metric changes with workflow context
- Pair speed metrics with quality metrics to avoid local optimization

## Recommended review cadence

- Weekly: team-level operational review
- Monthly: leadership trend and risk review
- Quarterly: target reset using rolling historical performance

## Related docs

- [Using app.leanmote.com](../leanmote-app.md)
- [Architecture Overview](../architecture/overview.md)
- [API Overview](../api/overview.md)
