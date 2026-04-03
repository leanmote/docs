# Overview

Leanmote is an engineering analytics platform that aggregates data from 20+ development tools to provide real-time metrics, insights, and analytics for engineering teams.

## What is Leanmote?

Leanmote helps engineering leaders:

- **Track DORA Metrics** - Deployment frequency, lead time, MTTR, change failure rate
- **Monitor Flow Metrics** - Throughput, WIP, cycle time
- **Analyze Team Collaboration** - Review participation, code review metrics, team activity
- **Detect Bottlenecks** - AI-powered identification of process inefficiencies
- **Set and Track Targets** - Automated KPI target calculation and progress tracking

## Key Features

### Multi-Platform Integration
Connect to 20+ development tools including GitHub, GitLab, Jira, Slack, Linear, Azure DevOps, and more. Leanmote automatically aggregates data across all your tools.

### Real-Time Dashboards
Customizable dashboards with real-time metrics. Filter by team, project, date range, and custom criteria. Share dashboards with stakeholders or make them public.

### AI-Powered Insights
Automatic bottleneck detection using graph analytics. Get actionable insights about where your development process is slowing down.

### Target Management
Set KPI targets and track progress. Leanmote can automatically calculate targets based on historical performance.

## Platform Components

| Component | Description |
|-----------|-------------|
| **Web Application** | CodeIgniter 4 PHP application providing the main user interface |
| **Lambda Functions** | AWS Lambda services for API, data extraction, and sync |
| **Insight Agent** | LangGraph-based AI pipeline for bottleneck detection |

## Supported Integrations

### Source Control
- GitHub
- GitLab (including self-hosted)
- Bitbucket
- Azure DevOps Repos

### Project Management
- Jira
- Linear
- ClickUp
- Monday.com
- Notion
- Azure DevOps Boards

### Communication
- Slack
- Discord

### Calendar
- Google Calendar (Enterprise)

### AI Tools
- GitHub Copilot
- Claude Code

## Next Steps

- [Quick Start](quick-start.md) - Get up and running in minutes
- [Configuration](configuration.md) - Configure your environment
- [Architecture Overview](../architecture/overview.md) - Understand the system architecture
