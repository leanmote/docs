# Architecture Overview

Leanmote follows a hybrid serverless architecture, combining traditional web hosting with serverless functions for scalability and cost efficiency.

## High-Level Architecture

```
                    ┌─────────────────────────────┐
                    │     External Systems        │
                    │  GitHub, GitLab, Jira, etc. │
                    └─────────────┬───────────────┘
                                  │
                    ┌─────────────▼───────────────┐
                    │     Application Layer       │
          ┌─────────┴─────────┬──────────────────┴─────────┐
          │                   │                            │
    ┌─────▼─────┐      ┌──────▼──────┐              ┌──────▼──────┐
    │   Web     │      │   Lambda    │              │   Insight   │
    │   App     │      │   APIs      │              │   Agent     │
    │  (PHP)    │      │  (Python)   │              │  (Python)   │
    └─────┬─────┘      └──────┬──────┘              └──────┬──────┘
          │                   │                            │
          └─────────┬─────────┴────────────────────────────┘
                    │
          ┌─────────▼─────────┐
          │   Data Layer      │
    ┌─────┴─────┐      ┌──────┴──────┐
    │   MySQL   │      │   Neo4j     │
    │   (RDS)   │      │   Aura      │
    └───────────┘      └─────────────┘
```

## Components

### Web Application (CodeIgniter 4)

The main user-facing application running on AWS Elastic Beanstalk.

**Responsibilities:**
- User authentication (AWS Cognito)
- Dashboard rendering
- Integration management
- Team and user administration
- Cron job execution

**Key Characteristics:**
- PHP 8.1+
- 84 controllers, 80+ models
- Multi-tenant architecture
- Session-based authentication

### Lambda Functions

Serverless functions handling API requests and background processing.

**Packages:**
| Function | Purpose |
|----------|---------|
| API-AUTH | Token validation and request enrichment |
| API-LEANMOTE | REST API for data access |
| DATA-EXTRACTOR | Git provider data synchronization |
| SYNC-NEO4J | Graph database synchronization |
| EMAIL-SENDER | Transactional email delivery |
| COPILOT-EXTRACTOR | GitHub Copilot metrics |
| SCIM-HANDLER | User provisioning (JumpCloud) |
| CLAUDE-CODE-OTEL-INGEST | OpenTelemetry ingestion |
| CLAUDE-CODE-NORMALIZE | OTEL data normalization |
| METRICS-TEST | Dashboard metrics calculation |
| UPLOAD-DATA | Raw data ingestion |

### Insight Agent (LangGraph)

AI-powered pipeline for bottleneck detection and insight generation.

**Capabilities:**
- Web research using Perplexity and Gemini
- Human-in-the-loop validation
- Cypher query generation
- Jira ticket creation

## Data Storage

### MySQL (Amazon RDS)

Primary relational database storing:
- Organizations and users
- Teams and memberships
- Integration credentials
- Boards, tasks, and history
- Repositories, commits, PRs
- Dashboards and filters

**Key Design Decisions:**
- Multi-tenant with `id_organization` scoping
- InnoDB for transactional integrity
- JSON columns for flexible data (columns_data, filter_values)

### Neo4j Aura

Graph database for relationship-heavy analytics:
- Team collaboration patterns
- Code review networks
- Bottleneck detection
- Path analysis

**Sync Strategy:**
- Hourly incremental sync from MySQL
- Window-based cursor tracking
- Full backfill support

## Data Flow

### Integration Data Pipeline

```
1. OAuth Authorization
   User → Web App → Provider → Callback → Tokens stored

2. Initial Sync
   Web App → Cron Queue → Provider API → MySQL

3. Ongoing Sync (Cron)
   Cron → Pick from queue → Fetch data → Transform → Store

4. Real-time (Webhooks)
   Provider → Lambda → Validate → Store → Notify
```

### Dashboard Data Flow

```
1. User requests dashboard
2. Web App checks permissions (DashboardAccessService)
3. Filters applied (date range, teams, custom)
4. Metrics Lambda calculates aggregates
5. JSON response to frontend
6. Charts rendered
```

### Bottleneck Detection

```
1. Weekly cron triggers analysis
2. Insight Agent researches patterns
3. Neo4j queries identify bottlenecks
4. Results compared to thresholds
5. Insights stored and displayed
6. Optional: Create Jira tickets
```

## Security Architecture

### Authentication Layers

| Layer | Mechanism |
|-------|-----------|
| Web Users | AWS Cognito + magic links |
| API Clients | Bearer tokens (SHA256 hashed) |
| Integrations | OAuth 2.0 per provider |
| Lambda-to-Lambda | IAM roles |

### Authorization Model

- **Organization-scoped:** All data filtered by organization
- **Role-based:** Admin (1), Manager (2), Collaborator (3)
- **Team-scoped:** Optional team-level filtering

### Data Isolation

- Foreign keys enforce organization boundaries
- Query filters automatically applied
- No cross-tenant data access possible

## Scalability

### Current Capabilities

| Component | Scaling |
|-----------|---------|
| Web App | Horizontal (Elastic Beanstalk) |
| Lambda | Automatic |
| MySQL | Vertical |
| Neo4j | Managed (Aura) |

### Future Considerations

- Read replicas for MySQL
- Data partitioning by date
- Caching layer (Redis/ElastiCache)
- Event sourcing for audit trails

## Monitoring

### Application Monitoring
- Sentry for error tracking
- CloudWatch for Lambda logs
- Cron execution logging

### Infrastructure Monitoring
- CloudWatch metrics
- RDS Performance Insights
- Lambda X-Ray tracing

## Deployment

### Web Application
- AWS Elastic Beanstalk
- Rolling deployments
- Environment-specific configurations

### Lambda Functions
- AWS SAM or Serverless Framework
- Versioned deployments
- Alias-based routing

### Database
- RDS automated backups
- Point-in-time recovery
- Multi-AZ for production

## Next Steps

- [Data Model](data-model.md) - Detailed schema documentation
- [Security](security.md) - Security implementation details
- [System Architecture](../architecture-analysis.md) - Complete technical analysis
