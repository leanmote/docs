# Leanmote Documentation

Welcome to the Leanmote technical documentation. Leanmote is an enterprise analytics platform that aggregates data from development and collaboration tools to provide real-time metrics, insights, and analytics for engineering teams.

## Platform Overview

Leanmote helps engineering leaders:
- **Track DORA metrics** - Deployment frequency, lead time, MTTR, change failure rate
- **Monitor flow metrics** - Throughput, WIP, cycle time
- **Analyze team collaboration** - Review participation, code review metrics, team activity
- **Detect bottlenecks** - AI-powered identification of process inefficiencies
- **Set and track targets** - Automated KPI target calculation and progress tracking

## Repositories

The Leanmote platform consists of three main repositories:

### [Leanmote Web Application](leanmote-app.md)
CodeIgniter 4-based SaaS application that serves as the main web interface.

**Key Features:**
- Multi-tenant dashboard system
- 20+ integrations (GitHub, Jira, Slack, GitLab, etc.)
- Real-time metrics aggregation
- User and team management
- AWS Cognito authentication

**Tech Stack:** PHP 8.1+, CodeIgniter 4, MySQL, AWS (Elastic Beanstalk, SES, Cognito)

---

### [Lambda Functions](lambda.md)
AWS Lambda packages that back the platform's serverless infrastructure.

**Packages:**
- **API-AUTH** - API Gateway authorizer
- **API-LEANMOTE** - Main REST API
- **DATA-EXTRACTOR** - Git integration data collector
- **SYNC-NEO4J** - Neo4j graph synchronization
- **EMAIL-SENDER** - Transactional email service
- **COPILOT-EXTRACTOR** - GitHub Copilot metrics
- **SCIM-HANDLER** - User provisioning (JumpCloud)
- **CLAUDE-CODE-OTEL-INGEST** - OpenTelemetry ingestion
- **CLAUDE-CODE-NORMALIZE** - OTEL data normalization
- **METRICS-TEST** - Dashboard metrics API
- **UPLOAD-DATA** - Raw data ingestion

**Tech Stack:** Python, AWS Lambda, API Gateway, SQS, MySQL, Neo4j

---

### [Insight Agent](insight-agent.md)
LangGraph-based multi-agent pipeline for investigating development problems.

**Capabilities:**
- Web research using Perplexity and Gemini
- Human-in-the-loop validation
- Neo4j Cypher query generation
- Jira integration for ticket creation

**Tech Stack:** Python, LangGraph, LangChain, FastAPI, OpenAI, Gemini, Perplexity

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                      External Systems                           │
│  GitHub  GitLab  Jira  Slack  Linear  Azure DevOps  ClickUp    │
└─────────────────────────┬───────────────────────────────────────┘
                          │ (webhooks / OAuth / API)
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Leanmote Web Application                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │  Dashboards  │  │  Integrations│  │  User Management     │  │
│  │  - Metrics   │  │  - OAuth     │  │  - Teams             │  │
│  │  - Insights  │  │  - Data Sync │  │  - Organizations     │  │
│  └──────────────┘  └──────────────┘  └──────────────────────┘  │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Lambda Functions                           │
│  ┌────────────┐  ┌────────────────┐  ┌─────────────────────┐   │
│  │  API-AUTH  │  │  API-LEANMOTE  │  │  DATA-EXTRACTOR     │   │
│  │            │  │                │  │                     │   │
│  └────────────┘  └────────────────┘  └─────────────────────┘   │
│  ┌────────────┐  ┌────────────────┐  ┌─────────────────────┐   │
│  │ SYNC-NEO4J │  │ METRICS-TEST   │  │ EMAIL-SENDER        │   │
│  └────────────┘  └────────────────┘  └─────────────────────┘   │
└─────────────────────────┬───────────────────────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
    ┌──────────┐   ┌──────────┐   ┌──────────────┐
    │  MySQL   │   │  Neo4j   │   │ Insight Agent│
    │  (RDS)   │   │  Aura    │   │  (LangGraph) │
    └──────────┘   └──────────┘   └──────────────┘
```

## Supported Integrations

| Category | Platforms |
|----------|-----------|
| **Source Control** | GitHub, GitLab, Bitbucket, Azure DevOps |
| **Project Management** | Jira, Linear, ClickUp, Monday.com, Notion, Azure Boards |
| **Communication** | Slack, Discord |
| **Calendar** | Google Calendar (Enterprise) |
| **AI Tools** | GitHub Copilot, Claude Code |

## Key Metrics

### DORA Metrics
- **Deployment Frequency** - How often code is deployed to production
- **Lead Time for Changes** - Time from commit to production
- **Mean Time to Recovery** - Time to restore service after incident
- **Change Failure Rate** - Percentage of deployments causing failures

### Flow Metrics
- **Throughput** - Number of items completed per time period
- **Work in Progress (WIP)** - Items currently being worked on
- **Cycle Time** - Time from start to completion of work items

### Team Metrics
- **Review Participation** - Code review engagement
- **Collaboration Patterns** - Team interaction analysis
- **AI Impact** - LOC delegation and AI-assisted work

## Getting Started

### For Developers
1. Review the [Leanmote Web Application](leanmote-app.md) documentation for the main codebase
2. Understand the [Lambda Functions](lambda.md) for API and data processing
3. Explore the [Insight Agent](insight-agent.md) for AI-powered analysis

### For DevOps
1. Lambda functions are deployed on AWS
2. Web application runs on Elastic Beanstalk
3. Neo4j Aura for graph analytics
4. MySQL (RDS) for relational data

### For Integration
1. OAuth flows for each supported platform
2. Webhook endpoints for real-time updates
3. Cron jobs for scheduled data synchronization
4. API endpoints for external access

## Documentation Index

- [Leanmote Web Application](leanmote-app.md) - Main web application
- [Lambda Functions](lambda.md) - Serverless backend
- [Insight Agent](insight-agent.md) - AI-powered analysis pipeline

## Support

For questions or issues:
- Review the specific component documentation
- Check the README files in each repository
- Contact the Leanmote team
