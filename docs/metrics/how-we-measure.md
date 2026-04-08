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

Calculation:

```
deployment_frequency = total_successful_production_deployments / period_length
```

- Numerator: production deployments marked successful in CI/CD or deployment events.
- Denominator: explicit period length (day, week, month) for comparability.
- Suggested views: per team, per service, and global.

Practical utility:

- Detects delivery bottlenecks when frequency drops while backlog stays high.
- Validates release process improvements (for example, smaller batch sizes or better automation).
- Helps align release risk: higher frequency with stable quality usually means healthier flow.

### Lead time for changes
Elapsed time from code change to production deployment.

Calculation:

```
lead_time_for_change = deployed_at - commit_or_merge_at
```

- Compute per change unit (commit, PR, or merge depending on source-of-truth choice).
- Use median and P75/P90 in addition to average to avoid outlier distortion.
- Optional decomposition:
  - coding_time = first_commit_at - issue_started_at
  - review_time = merged_at - opened_at
  - release_wait_time = deployed_at - merged_at

Practical utility:

- Reveals where delivery delay is concentrated (review, test, or release queue).
- Supports realistic SLA targets by percentile (for example, "80% under 2 days").
- Prevents false confidence from fast-path averages.

### Mean time to recovery (MTTR)
Average time to restore normal operation after a production incident.

Calculation:

```
mttr = sum(resolved_at - incident_started_at) / incident_count
```

- Include incidents tied to service degradation, outage, rollback, or urgent hotfix.
- Track median MTTR alongside mean to separate frequent minor incidents from major outliers.
- Keep severity labels (`sev1`, `sev2`, etc.) to analyze recovery quality by impact.

Practical utility:

- Measures operational resilience and incident response readiness.
- Highlights where runbooks, alert quality, or ownership clarity are weak.
- Often improves after better observability and escalation paths.

### Change failure rate
Percentage of deployments that lead to incidents, rollbacks, or hotfixes.

Calculation:

```
change_failure_rate = (failed_or_degraded_deployments / total_deployments) * 100
```

- Count a deployment as failed if it triggers incident creation, rollback, or emergency fix in the defined window.
- Define a fixed failure attribution window (for example, 24h or 7d after deploy).
- Track by service and team to isolate hotspots.

Practical utility:

- Balances speed metrics to avoid rewarding unsafe throughput.
- Shows where quality gates or test coverage are insufficient.
- Enables risk-based release policy tuning.

## Flow metrics

### Throughput
Number of completed work items over a defined time period.

Calculation:

```
throughput = count(items_completed_in_period)
```

- Completion should use explicit workflow transitions (for example, `done` state timestamp).
- Segment by issue type (feature, bug, chore) to avoid mixed-signal totals.
- Use rolling windows (7/14/30 days) to reduce calendar noise.

Practical utility:

- Baseline team delivery capacity and trend direction.
- Helps forecast near-term output when work mix is stable.
- Useful leading indicator when paired with cycle-time distribution.

### Cycle time
Time from work start to completion.

Calculation:

```
cycle_time = completed_at - in_progress_at
```

- Measure only active delivery period (not backlog wait time).
- Track percentile distribution, not only mean.
- Split by size class (`S`, `M`, `L`) or story points when available.

Practical utility:

- Identifies queueing/coordination problems when cycle time rises without throughput gains.
- Supports WIP policy design and pull-based workflow tuning.
- Improves predictability for stakeholder commitments.

### Work in progress (WIP)
Number of items actively in progress during the selected window.

Calculation:

```
wip(t) = count(items where status in active_states at time t)
average_wip = avg(wip(t)) over period
```

- Active states should be explicit (`in_progress`, `in_review`, `qa`, etc.).
- Report both point-in-time WIP and period average WIP.
- Use per-team and per-stream WIP caps for meaningful interpretation.

Practical utility:

- Early warning for multitasking overload and context-switch cost.
- High WIP with flat throughput typically indicates blocked flow.
- Supports Little's Law reasoning: `cycle_time ~ wip / throughput`.

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

## Data quality and guardrails

- Keep event-source precedence explicit (for example, deployment system over manual notes).
- Normalize timestamps to UTC and preserve original source timezone metadata.
- Exclude known bad states from aggregates (duplicate deploy events, reopened incidents without new impact).
- Publish metric definitions in change logs when formulas evolve to preserve historical comparability.

## Recommended review cadence

- Weekly: team-level operational review
- Monthly: leadership trend and risk review
- Quarterly: target reset using rolling historical performance

## Related docs

- [Using app.leanmote.com](../leanmote-app.md)
- [Architecture Overview](../architecture/overview.md)
- [API Overview](../api/overview.md)
