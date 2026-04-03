# Leanmote Lambda Functions

AWS Lambda packages that back the Leanmote platform. Each package is scoped to a specific concern and relies on the shared relational schema in `database/`.

## Overview

The Lambda repository contains 11 specialized serverless functions that handle authentication, API operations, data extraction, synchronization, and telemetry processing.

## Lambda Packages

### API-AUTH
Custom API Gateway authorizer for token validation.

**Purpose:**
- Validates database-backed API tokens against MySQL
- Enforces organization scoping
- Enriches requests with identity metadata (user ID, organization, role)

**Security:**
- SHA256 hashing with static salt for token validation
- Updates `last_used_at` timestamp for audit trails

**Key File:** `lambda_function.py`

---

### API-LEANMOTE
Main REST API entrypoint (v1.0).

**Purpose:**
- Normalizes incoming requests and resolves routes to domain controllers
- Queries MySQL backend and formats responses
- Supports pagination, field filtering, timestamp comparisons, and sorting

**Controllers (12 domains):**
- `organizations_controller.py` - Organization and member queries
- `teams_controller.py` - Team management and membership
- `users_controller.py` - User profile and relationships
- `tasks_controller.py` - Task aggregation and worklog
- `boards_controller.py` - Board and task queries
- `git_controller.py` - Git repository queries
- `calendar_controller.py` - Calendar and event queries
- `dashboard_views_controller.py` - Dashboard and filter management
- `interactions_controller.py` - Communication analytics
- `apps_controller.py` - Application queries
- `admin_controller.py` - API credential management
- `meta_controller.py` - Health checks

**Response Envelope:**
```json
{
  "status": "success",
  "data": [...],
  "meta": {
    "page": 1,
    "per_page": 50,
    "total": 100,
    "total_pages": 2,
    "sort": "created_at",
    "order": "desc"
  }
}
```

**Query Features:**
- `page`, `per_page` - Pagination
- `sort`, `order` - Sorting
- `fields` - Field projection with dot-path support
- Bracket operators: `created_at[gte]=...`, `created_at[lte]=...`

---

### DATA-EXTRACTOR
Event-driven collector for source-control integrations.

**Purpose:**
- Dispatches operations to GitHub, GitLab, Bitbucket, and generic Git routers
- Tracks progress via database using ProcessTracker
- Enqueues follow-up work on SQS FIFO queues

**Supported Providers:**
- GitHub - Repositories, commits, PRs, reviews
- GitLab - Projects, merge requests, discussions
- Bitbucket - Repositories, commits, pull requests
- Generic Git - Raw Git operations

**Key Features:**
- Multi-provider router pattern
- Webhook and direct invocation support
- User ID mapping across platforms
- Interaction recording (comments, approvals)

**Directory Structure:**
```
DATA-EXTRACTOR/
├── lambda_function.py
├── routers/
│   ├── github_router.py
│   ├── gitlab_router.py
│   ├── bitbucket_router.py
│   └── git_router.py
├── functions/
│   ├── github/
│   ├── gitlab/
│   └── bitbucket/
└── services/
```

---

### EMAIL-SENDER
Utility for sending transactional emails.

**Purpose:**
- Supports SES/SMTP backends
- Reads credentials from AWS Secrets Manager
- Validates payloads before sending
- Reacts to SQS notifications

**Key File:** `mail_sender.py`

---

### SYNC-NEO4J
Orchestrates synchronization between Leanmote API and Neo4j Aura.

**Purpose:**
- Prepares graph schema (constraints and indexes)
- Runs generated Cypher queries
- Intelligent scheduling (planning phase determines sync workload)
- Window-based incremental syncing

**Sync Domains:**
- Organizations, Teams, Users
- Boards, Repositories
- Commits, Pull Requests
- Calendar Events
- Person Metrics (WIP, throughput, review counts)

**Key Features:**
- Scheduler determines sync workload (plan/execute phases)
- Cursor tracking for resumable syncs
- Support for full backfills and incremental updates

---

### COPILOT-EXTRACTOR
Extracts GitHub Copilot metrics.

**Purpose:**
- Collects org-level, user-level, and billing seat data
- Persists raw payloads to MySQL
- Normalizes metrics for dashboards
- Supports batch processing all organizations

**Data Collected:**
- Daily org reports
- User-level reports
- Billing seats information

---

### SCIM-HANDLER
SCIM 2.0 Service Provider for user/group provisioning.

**Purpose:**
- JumpCloud integration for user management
- Served behind HTTP API Gateway at `/scim/v2`
- Bidirectional user/group synchronization

**Endpoints:**
- `POST/PATCH/DELETE /scim/v2/Users`
- `POST/PATCH/DELETE /scim/v2/Groups`

**Key Files:**
```
SCIM-HANDLER/
├── scim_handler.py
├── lambda_function.py
├── services/
└── models/
```

---

### CLAUDE-CODE-OTEL-INGEST
Receives OpenTelemetry (OTLP) metrics and logs from Claude Code.

**Purpose:**
- OTLP HTTP protocol v1.0 ingestion
- Persists raw payloads to MySQL
- Provides managed settings configuration for customers

**Endpoints:**
- `GET /health` - Health check
- `POST /v1/logs` - Log ingestion
- `POST /v1/metrics` - Metrics ingestion

**Key Features:**
- Token-based tenant resolution via SHA256 hash
- Raw payload persistence for later analysis

---

### CLAUDE-CODE-NORMALIZE
Normalizes raw Claude Code OTLP data into fact tables.

**Purpose:**
- Reads from `claude_code_otel_ingest`
- Writes to `claude_code_fact_metrics`
- Supports direct invocation and SQS batch processing

---

### METRICS-TEST
Provides comprehensive metrics APIs for dashboards.

**Endpoints:**
- `/get-data` - General data retrieval
- `/context` - Context information
- `/dora-metrics` - DORA metrics
- `/flow-metrics` - Flow metrics
- `/team-activity` - Team activity
- `/project-financials` - Project financials

**Metrics Types:**
- DORA metrics
- Flow metrics
- Team activity
- Interactions
- Calendar
- AI impact

---

### UPLOAD-DATA
Ingests raw app payloads via API Gateway.

**Endpoint:** `POST /upload-data/{app_slug}`

**Features:**
- Token-based authentication
- Stores payloads in `upload_data_events` table
- Supports Claude Code (app_slug: "claude-code")

## Database Schema

**Total Tables: 33+**

### Core Domain Tables

| Category | Tables |
|----------|--------|
| Organizations & Users | organizations, users, user_organizations, timezones |
| Teams | teams, team_users, team_managers |
| APIs & Credentials | api_tokens, user_apps_credentials, user_apps_mapping, apps |
| Boards & Tasks | boards, board_tasks, board_task_statuses, board_task_statuses_log |
| Git/Repositories | repositories, repo_commits, repo_merge_requests |
| Calendar | calendars, calendar_events |
| Interactions | user_interactions, communication_channels |
| Dashboards | dashboards, dashboard_custom_filters, user_dashboard_filters |
| Metrics & Insights | metrics, insights, targets, variables |
| OTEL & Telemetry | claude_code_otel_ingest, claude_code_fact_metrics |

### Key Relationships
- Users belong to multiple Organizations (many-to-many)
- Teams contain Users (many-to-many)
- Boards contain Tasks with hierarchical parent-child relationships
- Repositories contain Commits and Merge Requests
- API Tokens reference Users with expiry and usage tracking

## API Endpoints Reference

### Organizations
```
GET /organizations
GET /organizations/{id}
GET /organizations/{id}/members
GET /organizations/{id}/teams
GET /organizations/{id}/apps
```

### Teams
```
GET /teams
GET /teams/{id}
GET /teams/{id}/members
GET /teams/{id}/managers
GET /teams/{id}/tasks
GET /teams/{id}/interactions
```

### Users
```
GET /users
GET /users/{id}
GET /users/{id}/organizations
GET /users/{id}/teams
GET /users/{id}/apps
GET /users/{id}/interactions
GET /users/{id}/dashboard-views
GET /users/{id}/git/apps
```

### Git Integrations
```
GET /git/apps
GET /git/apps/{id}/repositories
GET /git/apps/{id}/repositories/{repo_id}
GET /git/apps/{id}/repositories/all/commits
GET /git/apps/{id}/repositories/{repo_id}/commits
GET /git/apps/{id}/repositories/all/pull-requests
GET /git/apps/{id}/repositories/{repo_id}/pull-requests
```

### Dashboard Views
```
GET /dashboard-views
GET /dashboard-views/catalog
GET /dashboard-views/custom-filters
GET /dashboard-views/{id}
```

### Admin
```
GET /admin/users/{id}/api-credentials
POST /admin/users/{id}/api-credentials
```

## Data Flow

```
External Systems (GitHub, GitLab, etc.)
    │ (webhooks / API pulls)
    ▼
┌─────────────────────────┐
│   DATA-EXTRACTOR        │
│   (Event-driven, SQS)   │
└───────────┬─────────────┘
            ▼
┌─────────────────────────┐
│  MySQL Database         │
│  (33+ tables)           │
└───────────┬─────────────┘
            ▼
┌─────────────────────────┐
│   SYNC-NEO4J            │
│   (Scheduled hourly)    │
└───────────┬─────────────┘
            ▼
┌─────────────────────────┐
│  Neo4j Aura Graph       │
└─────────────────────────┘

Claude Code Editor
    │ (OTLP POST)
    ▼
┌─────────────────────────┐
│ CLAUDE-CODE-OTEL-INGEST │
└───────────┬─────────────┘
            ▼
┌─────────────────────────┐
│ CLAUDE-CODE-NORMALIZE   │
└───────────┬─────────────┘
            ▼
┌─────────────────────────┐
│ claude_code_fact_*      │
│ (Normalized tables)     │
└─────────────────────────┘
```

## Security Features

1. **Token Security**
   - SHA256 hashing with salt
   - Token preview for user recognition
   - Expiry tracking
   - Usage auditing (`last_used_at`)

2. **Multi-tenancy**
   - Organization-scoped queries
   - User membership validation
   - Admin override capability

3. **Data Validation**
   - Bearer token extraction with strict parsing
   - HTTP method validation
   - Content-type enforcement

4. **Observability**
   - Structured JSON logging
   - Request context injection
   - Distributed tracing (trace_id in SQS messages)
   - Process tracking with operation metadata

## Deployment

All Lambda functions are deployed on AWS with:
- API Gateway for HTTP endpoints
- SQS FIFO queues for ordered processing
- Secrets Manager for credentials
- RDS MySQL for data storage
- Neo4j Aura for graph database
