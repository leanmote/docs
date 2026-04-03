# METRICS-TEST Endpoints

Sources: `lambda/METRICS-TEST/metrics_api/router.py` and `metrics_api/handler.py`.

Core aggregate endpoints:

| Method | Path | Notes |
| --- | --- | --- |
| GET, POST | `/get-data` | Full metrics payload. |
| GET, POST | `/metrics` | Alias for full payload. |
| GET, POST | `/metrics/all` | Alias for full payload. |
| GET, POST | `/metrics/get-data` | Alias for full payload. |
| GET | `/` | Behaves like `/get-data` when routed by API Gateway path. |

Metric endpoints:

| Method | Path | Metrics alias |
| --- | --- | --- |
| GET | `/ai_impact` | `/metrics/ai_impact` |
| GET, POST | `/ai-impact` | `/metrics/ai-impact` |
| GET, POST | `/context` | `/metrics/context` |
| GET | `/dora` | `/metrics/dora` |
| GET | `/dora/allowed_users` | `/metrics/dora/allowed_users` |
| GET | `/dora/change_failure_rate` | `/metrics/dora/change_failure_rate` |
| GET | `/dora/deployment_frequency` | `/metrics/dora/deployment_frequency` |
| GET | `/dora/lead_time_for_changes` | `/metrics/dora/lead_time_for_changes` |
| GET | `/dora/mean_time_to_recover` | `/metrics/dora/mean_time_to_recover` |
| GET | `/dora/pull_request_activity` | `/metrics/dora/pull_request_activity` |
| GET | `/dora/pull_request_activity/created` | `/metrics/dora/pull_request_activity/created` |
| GET | `/dora/pull_request_activity/merged` | `/metrics/dora/pull_request_activity/merged` |
| GET | `/dora/pull_request_activity/open` | `/metrics/dora/pull_request_activity/open` |
| GET, POST | `/dora-metrics` | `/metrics/dora-metrics` |
| GET | `/flow` | `/metrics/flow` |
| GET | `/flow/active_time` | `/metrics/flow/active_time` |
| GET | `/flow/avg_age_wip` | `/metrics/flow/avg_age_wip` |
| GET | `/flow/cycle_time` | `/metrics/flow/cycle_time` |
| GET | `/flow/debug` | `/metrics/flow/debug` |
| GET | `/flow/flow_efficiency` | `/metrics/flow/flow_efficiency` |
| GET | `/flow/issue_type_percentage` | `/metrics/flow/issue_type_percentage` |
| GET | `/flow/sprints_throughput` | `/metrics/flow/sprints_throughput` |
| GET | `/flow/storypoints` | `/metrics/flow/storypoints` |
| GET | `/flow/throughput` | `/metrics/flow/throughput` |
| GET | `/flow/time_to_market` | `/metrics/flow/time_to_market` |
| GET | `/flow/timeliness_estimate` | `/metrics/flow/timeliness_estimate` |
| GET | `/flow/timespent` | `/metrics/flow/timespent` |
| GET | `/flow/unassigned_tasks` | `/metrics/flow/unassigned_tasks` |
| GET | `/flow/unassigned_tasks/all` | `/metrics/flow/unassigned_tasks/all` |
| GET | `/flow/unassigned_tasks/backlog` | `/metrics/flow/unassigned_tasks/backlog` |
| GET | `/flow/unassigned_tasks/no_logs` | `/metrics/flow/unassigned_tasks/no_logs` |
| GET | `/flow/unassigned_tasks/unassigned` | `/metrics/flow/unassigned_tasks/unassigned` |
| GET | `/flow/waiting_time` | `/metrics/flow/waiting_time` |
| GET | `/flow/work_in_progress` | `/metrics/flow/work_in_progress` |
| GET, POST | `/flow-metrics` | `/metrics/flow-metrics` |
| GET | `/interactions` | `/metrics/interactions` |
| GET | `/meeting` | `/metrics/meeting` |
| GET | `/meeting/avg_focus_time_availability` | `/metrics/meeting/avg_focus_time_availability` |
| GET | `/meeting/avg_meeting_cost` | `/metrics/meeting/avg_meeting_cost` |
| GET | `/meeting/avg_meeting_duration` | `/metrics/meeting/avg_meeting_duration` |
| GET | `/meeting/focus_time_details` | `/metrics/meeting/focus_time_details` |
| GET | `/meeting/large_meeting_costs` | `/metrics/meeting/large_meeting_costs` |
| GET | `/meeting/large_meetings_count` | `/metrics/meeting/large_meetings_count` |
| GET | `/meeting/recurrent_meetings_percentage` | `/metrics/meeting/recurrent_meetings_percentage` |
| GET | `/meeting/team_meeting_cost` | `/metrics/meeting/team_meeting_cost` |
| GET | `/meeting/teams_data` | `/metrics/meeting/teams_data` |
| GET | `/meeting/total_meeting_hours` | `/metrics/meeting/total_meeting_hours` |
| GET | `/project-financials` | `/metrics/project-financials` |
| GET, POST | `/project-financials/team-ai-loc-metrics` | `/metrics/project-financials/team-ai-loc-metrics` |
| GET, POST | `/project-financials/team-dora-metrics` | `/metrics/project-financials/team-dora-metrics` |
| GET | `/review_collaboration` | `/metrics/review_collaboration` |
| GET | `/review_collaboration/reviewer/all_merge_requests` | `/metrics/review_collaboration/reviewer/all_merge_requests` |
| GET | `/review_collaboration/reviewer/pickup_time` | `/metrics/review_collaboration/reviewer/pickup_time` |
| GET | `/review_collaboration/reviewer/reaction_time` | `/metrics/review_collaboration/reviewer/reaction_time` |
| GET | `/review_collaboration/reviewer/sharing_index` | `/metrics/review_collaboration/reviewer/sharing_index` |
| GET | `/review_collaboration/reviewer/time_to_first_approval` | `/metrics/review_collaboration/reviewer/time_to_first_approval` |
| GET | `/review_collaboration/submitter/iterated_prs` | `/metrics/review_collaboration/submitter/iterated_prs` |
| GET | `/review_collaboration/submitter/pr_iteration_time` | `/metrics/review_collaboration/submitter/pr_iteration_time` |
| GET | `/review_collaboration/submitter/responsiveness` | `/metrics/review_collaboration/submitter/responsiveness` |
| GET | `/review_collaboration/submitter/time_to_merge` | `/metrics/review_collaboration/submitter/time_to_merge` |
| GET | `/review_collaboration/team_members` | `/metrics/review_collaboration/team_members` |
| GET | `/team_activity` | `/metrics/team_activity` |
| GET | `/team_activity/active_days` | `/metrics/team_activity/active_days` |
| GET | `/team_activity/reviews_count` | `/metrics/team_activity/reviews_count` |
| GET, POST | `/team-activity` | `/metrics/team-activity` |
| GET, POST | `/team-collaboration` | `/metrics/team-collaboration` |

Total metric endpoints: 66
