# Leanmote Architecture Analysis

Comprehensive technical analysis of the Leanmote platform architecture, components, data models, and integration points.

## Executive Summary

Leanmote is a multi-tenant SaaS platform that aggregates engineering metrics from 20+ development tools. The architecture follows a hybrid serverless pattern with:

- **Frontend**: CodeIgniter 4 PHP web application (Elastic Beanstalk)
- **Backend**: AWS Lambda functions for API, data extraction, and sync
- **Data Storage**: MySQL (RDS) for relational data, Neo4j Aura for graph analytics
- **AI Pipeline**: LangGraph-based multi-agent system for insight generation

## System Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           External Systems Layer                            │
├─────────────────────────────────────────────────────────────────────────────┤
│  GitHub  GitLab  Bitbucket  Jira  Linear  Slack  Azure DevOps  ClickUp     │
│  Monday  Notion  Discord  Google Calendar  GitHub Copilot  Claude Code     │
└──────────────────────────────┬──────────────────────────────────────────────┘
                               │ OAuth / Webhooks / REST APIs
                               ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         Application Layer                                   │
├──────────────────────────┬──────────────────────────────────────────────────┤
│   Web Application        │   Serverless Functions                           │
│   (Elastic Beanstalk)    │   (AWS Lambda)                                   │
│   ┌──────────────────┐   │   ┌────────────────┬────────────────────┐       │
│   │ CodeIgniter 4    │   │   │  API-AUTH      │  API-LEANMOTE      │       │
│   │ - 84 Controllers │   │   │  (Authorizer)  │  (REST API)        │       │
│   │ - 80+ Models     │   │   ├────────────────┼────────────────────┤       │
│   │ - 6 Services     │   │   │  DATA-EXTRACTOR│  SYNC-NEO4J        │       │
│   │ - Cron Jobs      │   │   │  (Git Sync)    │  (Graph Sync)      │       │
│   └──────────────────┘   │   ├────────────────┼────────────────────┤       │
│                          │   │ COPILOT-EXTRACTOR│ CLAUDE-CODE-*    │       │
│                          │   │ METRICS-TEST   │  SCIM-HANDLER      │       │
│                          │   └────────────────┴────────────────────┘       │
└──────────────────────────┴──────────────────────────────────────────────────┘
                               │
          ┌────────────────────┼────────────────────┐
          ▼                    ▼                    ▼
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│   MySQL (RDS)    │  │   Neo4j Aura     │  │  Insight Agent   │
│   45+ Tables     │  │   Graph DB       │  │  (LangGraph)     │
│   Multi-tenant   │  │   Analytics      │  │  AI Pipeline     │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

## Repository Overview

### 1. leanmote (Web Application)
**Path:** `C:\Users\Usuario\Desktop\Leanmote\leanmote`
**Framework:** CodeIgniter 4 (PHP 8.1+)
**Deployment:** AWS Elastic Beanstalk

**Purpose:** Main user-facing application providing dashboards, integration management, and team administration.

### 2. lambda (Serverless Functions)
**Path:** `C:\Users\Usuario\Desktop\Leanmote\lambda`
**Runtime:** Python 3.11+
**Deployment:** AWS Lambda

**Purpose:** API services, data extraction, synchronization, and telemetry processing.

### 3. insight_agent (AI Pipeline)
**Path:** `C:\Users\Usuario\Desktop\Leanmote\insight_agent`
**Framework:** LangGraph + LangChain
**Runtime:** Python 3.11+

**Purpose:** AI-powered research, problem discovery, and Cypher query generation.

## Data Model

### Core Domain Tables (45+ tables)

#### Organization & Users
| Table | Purpose |
|-------|---------|
| `organizations` | Multi-tenant root entity |
| `users` | User accounts with Cognito integration |
| `user_organizations` | Many-to-many user-org mapping |
| `timezones` | Timezone reference data |

#### Teams & Access
| Table | Purpose |
|-------|---------|
| `teams` | Team definitions within organizations |
| `team_users` | Team membership |
| `team_managers` | Team leadership assignments |

#### Integrations & Credentials
| Table | Purpose |
|-------|---------|
| `apps` | Integration platform definitions |
| `user_apps_credentials` | OAuth tokens and API keys |
| `user_apps_mapping` | User ID mappings across platforms |
| `api_tokens` | API access tokens for external access |

#### Boards & Tasks (Project Management)
| Table | Purpose |
|-------|---------|
| `boards` | Jira/Linear/ClickUp board configurations |
| `board_tasks` | Tasks/issues with hierarchy |
| `board_task_statuses` | Workflow status definitions |
| `board_task_statuses_log` | Status change history (for cycle time) |
| `board_issue_types` | Issue type mappings |
| `board_task_logged_hours` | Time tracking data |
| `sprints` | Sprint/iteration definitions |

#### Git & Source Control
| Table | Purpose |
|-------|---------|
| `repositories` | GitHub/GitLab/Bitbucket repos |
| `repo_commits` | Commit history |
| `repo_merge_requests` | Pull requests / Merge requests |
| `repo_merge_request_notes` | PR/MR comments and reviews |

#### Communication
| Table | Purpose |
|-------|---------|
| `communication_channels` | Slack/Discord channels |
| `communication_channel_members_log` | Channel membership history |
| `user_interactions` | Cross-platform interaction records |

#### Calendar
| Table | Purpose |
|-------|---------|
| `calendars` | Google Calendar configurations |
| `calendar_events` | Meeting and event data |

#### Dashboards & Analytics
| Table | Purpose |
|-------|---------|
| `dashboards` | User dashboard definitions |
| `user_dashboard_filters` | Saved filter configurations |
| `user_dashboard_sections_enabled` | Section visibility preferences |
| `dashboard_custom_filters` | Organization-level custom filters |

#### Metrics & Insights
| Table | Purpose |
|-------|---------|
| `targets` | KPI target definitions |
| `insights` | AI-generated insights |
| `metrics` | General metrics tracking |

#### AI Agents
| Table | Purpose |
|-------|---------|
| `agents` | AI agent configurations |
| `agent_threads` | Conversation threads |
| `agent_usage_stats` | Token usage and cost tracking |
| `agent_vector_stores` | RAG vector store references |
| `agent_files` | Agent-uploaded files |

#### Cron & Operations
| Table | Purpose |
|-------|---------|
| `cron_queue` | Distributed task queue |
| `cron_log` | Execution logging |

### Entity Relationships

```
organizations (1) ──┬── (N) users (via user_organizations)
                    ├── (N) teams ── (N) users (via team_users)
                    ├── (N) boards ── (N) board_tasks
                    ├── (N) repositories ── (N) repo_commits
                    │                    └── (N) repo_merge_requests
                    ├── (N) communication_channels
                    ├── (N) calendars ── (N) calendar_events
                    ├── (N) dashboards
                    └── (N) user_interactions

users (1) ──┬── (N) user_apps_credentials
            ├── (N) user_apps_mapping
            ├── (N) api_tokens
            ├── (N) agents
            └── (N) user_dashboard_filters
```

## API Architecture

### Lambda API Endpoints (API-LEANMOTE)

**Base URL:** API Gateway → Lambda

#### Organizations
```
GET  /organizations
GET  /organizations/{id}
GET  /organizations/{id}/members
GET  /organizations/{id}/teams
GET  /organizations/{id}/apps
```

#### Teams
```
GET  /teams
GET  /teams/{id}
GET  /teams/{id}/members
GET  /teams/{id}/managers
GET  /teams/{id}/tasks
GET  /teams/{id}/interactions
```

#### Users
```
GET  /users
GET  /users/{id}
GET  /users/{id}/organizations
GET  /users/{id}/teams
GET  /users/{id}/apps
GET  /users/{id}/interactions
GET  /users/{id}/dashboard-views
GET  /users/{id}/git/apps
```

#### Git
```
GET  /git/apps
GET  /git/apps/{id}/repositories
GET  /git/apps/{id}/repositories/{repo_id}
GET  /git/apps/{id}/repositories/all/commits
GET  /git/apps/{id}/repositories/{repo_id}/commits
GET  /git/apps/{id}/repositories/all/pull-requests
```

#### Dashboard Views
```
GET  /dashboard-views
GET  /dashboard-views/catalog
GET  /dashboard-views/custom-filters
GET  /dashboard-views/{id}
```

### Web Application Endpoints (CodeIgniter)

#### Dashboard Data
```
POST /dashboard/get-data
POST /dashboard/get-filters
POST /dashboard/context
POST /dashboard/ai-impact
POST /dashboard/dora-metrics
POST /dashboard/flow-metrics
POST /dashboard/team-collaboration
POST /dashboard/team-activity
```

#### Dashboard CRUD
```
POST /dashboard/create
POST /dashboard/update
POST /dashboard/duplicate
POST /dashboard/share/save
POST /dashboard/public-link/toggle
```

#### Integrations (per platform)
```
GET  /{platform}/oauth/start
GET  /{platform}/oauth/callback
GET  /{platform}/integration/settings
POST /{platform}/integration/save
```

## Integration Points

### Supported Platforms

| Platform | OAuth | Webhooks | API Pull | Data Types |
|----------|-------|----------|----------|------------|
| GitHub | Yes | Yes | Yes | Repos, commits, PRs, reviews |
| GitLab | Yes | Yes | Yes | Projects, MRs, discussions |
| Bitbucket | Yes | No | Yes | Repos, commits, PRs |
| Jira | Yes | Yes | Yes | Projects, issues, sprints |
| Linear | Yes | Yes | Yes | Teams, issues, cycles |
| Slack | Yes | Yes | Yes | Channels, messages |
| Discord | Yes | No | Yes | Servers, channels, messages |
| Azure DevOps | Yes | Yes | Yes | Repos, boards, work items |
| ClickUp | Yes | Yes | Yes | Spaces, lists, tasks |
| Monday.com | Yes | Yes | Yes | Boards, items |
| Notion | Yes | Yes | Yes | Databases, pages |
| Google Calendar | Yes | No | Yes | Events, availability |

### Data Flow Patterns

#### OAuth Integration Flow
```
1. User initiates connection → /oauth/start
2. Redirect to provider OAuth consent
3. Callback with auth code → /oauth/callback
4. Exchange code for tokens
5. Store tokens in user_apps_credentials
6. Initial data sync triggered
```

#### Cron-based Data Sync
```
1. Cron job picks from cron_queue
2. Fetch data via provider API
3. Transform and normalize data
4. Store in respective tables
5. Record interactions in user_interactions
6. Update last_update timestamp
```

#### Real-time Webhook Processing
```
1. Receive webhook payload
2. Validate signature
3. Parse event type
4. Update relevant entities
5. Enqueue follow-up tasks if needed
```

## Security Architecture

### Authentication
- **Web Application:** AWS Cognito with magic link support
- **API:** Token-based (SHA256 hashed, stored in `api_tokens`)
- **OAuth:** Per-platform OAuth 2.0 flows

### Authorization
- **Role-based:** Admin (1), Manager (2), Collaborator (3)
- **Organization-scoped:** All queries filtered by organization
- **Team-scoped:** Optional team-level filtering

### Token Security
```
Token Generation:
1. Generate 36-char UUID
2. Store SHA256(token + salt) as token_hash
3. Store first 5 chars as token_preview
4. Return full token to user (once)

Token Validation:
1. Receive token in Authorization header
2. Compute SHA256(token + salt)
3. Query api_tokens by hash
4. Validate is_active and expires_at
5. Update last_used_at
```

## Scalability Considerations

### Current Architecture
- **Horizontal scaling:** Elastic Beanstalk auto-scaling
- **Serverless:** Lambda scales automatically
- **Database:** Single RDS instance (vertical scaling)
- **Graph:** Neo4j Aura (managed scaling)

### Bottlenecks & Solutions
| Component | Bottleneck | Solution |
|-----------|------------|----------|
| MySQL | Single instance | Read replicas, Aurora |
| Cron jobs | Sequential processing | Distributed queue (SQS) |
| Large orgs | Data volume | Partitioning by date |
| API calls | Rate limits | Per-provider throttling |

## Monitoring & Observability

### Error Tracking
- **Sentry:** PHP application errors
- **CloudWatch:** Lambda function logs

### Metrics
- `cron_log` table: Execution history
- `metrics` table: Application metrics
- `user_logs` table: User activity

### Distributed Tracing
- `trace_id` in SQS messages
- Process tracking via `cron_queue`

## Dependencies

### PHP (Web Application)
```json
{
  "aws/aws-sdk-php": "^3.368.0",
  "sentry/sentry": "^4.17",
  "laminas/laminas-escaper": "^2.13",
  "psr/log": "^3.0"
}
```

### Python (Lambda)
- boto3 (AWS SDK)
- mysql-connector-python
- requests
- PyJWT

### Python (Insight Agent)
```
langgraph==0.2.75
langchain-core==0.3.51
langchain-openai==0.3.11
langchain-google-genai==2.1.4
fastapi==0.115.6
sqlalchemy==2.0.35
```

## Key Architectural Decisions

### 1. Multi-tenant by Organization
All data is scoped to `id_organization`, enabling strict data isolation.

### 2. Hybrid Serverless
Web app on Elastic Beanstalk for stateful operations, Lambda for stateless API and processing.

### 3. Separate Graph Database
Neo4j for complex relationship queries (bottleneck detection, collaboration patterns).

### 4. Event-driven Data Sync
Webhooks for real-time updates, cron for batch processing.

### 5. AI Pipeline Isolation
Insight Agent runs independently, persisting results to MySQL.

## Future Architecture Considerations

1. **Event Sourcing:** Full audit trail of all changes
2. **CQRS:** Separate read/write paths for dashboards
3. **GraphQL:** Flexible querying for dashboard customization
4. **Kubernetes:** Container orchestration for compute-heavy workloads
5. **Data Lake:** Historical data archival and advanced analytics
