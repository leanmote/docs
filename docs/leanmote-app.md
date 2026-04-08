# Leanmote Web Application

CodeIgniter 4-based SaaS application that aggregates data from multiple development and collaboration tools to provide real-time metrics, insights, and analytics for engineering teams.

## Overview

**Framework:** CodeIgniter 4 (PHP 8.1+)
**Database:** MySQL with multi-tenant architecture
**Authentication:** AWS Cognito with magic link/token-based login
**Deployment:** AWS Elastic Beanstalk
**Monitoring:** Sentry for error tracking

## Directory Structure

```
leanmote/
├── app/
│   ├── Controllers/     # 84 controller files
│   ├── Models/          # 80+ model classes
│   ├── Config/          # Configuration (43 files)
│   ├── Services/        # Business logic (6 services)
│   ├── Filters/         # HTTP middleware (3 filters)
│   ├── Libraries/       # API clients and utilities
│   ├── Views/           # Templates and layouts
│   └── Language/        # Localization files
├── public/
│   ├── index.php        # Main entry point
│   └── assets/          # Frontend assets
├── writable/            # Logs and cache
├── tests/               # PHPUnit tests
└── composer.json
```

## Supported Integrations (20+)

### Source Control
- **GitHub** - Repositories, commits, PRs, reviews
- **GitLab** - Projects, merge requests (self-hosted supported)
- **Bitbucket** - Repositories, commits, PRs
- **Azure DevOps** - Repos and boards

### Project Management
- **Jira** - Projects, issues, sprints, history
- **Linear** - Teams, issues, cycles
- **ClickUp** - Spaces, lists, tasks
- **Monday.com** - Boards, items
- **Notion** - Databases, pages
- **Azure DevOps Boards** - Work items

### Communication
- **Slack** - Channels, messages, interactions
- **Discord** - Servers, channels, messages

### Calendar
- **Google Calendar** (Enterprise) - Events, availability

## Key Features

### Dashboard System
Multiple dashboard types with dynamic filtering and sharing capabilities.

**Dashboard Types:**
- Performance/Delivery Dashboard
- Project Financials Dashboard
- Custom dashboards

**Features:**
- Dynamic date range and metric filtering
- Section visibility preferences per user
- Real-time data aggregation
- Dashboard sharing with granular access control
- Public view links with lead capture

**Endpoints:**
```
POST /dashboard/save-dashboard-sections
POST /dashboard/save-metric-filters
POST /dashboard/get-data
POST /dashboard/get-filters
POST /dashboard/create
POST /dashboard/update
POST /dashboard/duplicate
POST /dashboard/share/save
GET  /dashboard/public-link/status
POST /dashboard/public-link/toggle
```

### Metrics & Analytics

**DORA Metrics:**
- Deployment frequency
- Lead time for changes
- Mean time to recovery (MTTR)
- Change failure rate

**Flow Metrics:**
- Throughput
- Work in Progress (WIP)
- Cycle time

**Team Metrics:**
- Review participation
- Code review metrics
- Team activity and engagement
- Collaboration patterns

**AI Impact:**
- LOC delegation
- AI-assisted work percentage

**Data Endpoints:**
```
POST /dashboard/context
POST /dashboard/ai-impact
POST /dashboard/dora-metrics
POST /dashboard/flow-metrics
POST /dashboard/team-collaboration
POST /dashboard/team-activity
```

### Bottleneck Insights
AI-driven detection of development process bottlenecks.

**Features:**
- Pull request "black hole" analysis
- Cycle time bottleneck detection
- Neo4j graph database integration
- Jira ticket creation from insights
- Weekly automated refresh

### Strategic Overview (Cockpit)
High-level KPI tracking and target management.

**Features:**
- Automated KPI target calculation
- Primary target progress tracking
- Throughput trajectory analysis
- Team contribution breakdown
- 3-month rolling window with statistical outlier detection

### User & Team Management

**Authorization Levels:**
- Role 1: Admin (full platform access)
- Role 2: Manager/Team Lead (team and board management)
- Role 3: Collaborator (read-only dashboard access)

**Endpoints:**
```
POST /users/add
POST /users/delete
POST /users/update-access-level
POST /users/org-role-scraper/preview
POST /users/org-role-scraper/fetch-roles
POST /users/org-role-scraper/apply
```

## Controllers Reference

### Authentication
- **CognitoAuthController** - OAuth flow with AWS Cognito, magic links

### Dashboards
- **DashboardController** - Main dashboard CRUD and data
- **CockpitController** - Strategic overview and targets
- **ProjectInvestmentController** - Team allocation and financials
- **BottleneckInsightsController** - AI-powered bottleneck detection

### Integrations
Each integration follows a pattern:
- `OAuthController*` - Handles OAuth authorization
- `DataExtractionController*` - Syncs data via API
- `IntegrationController*` - Configuration UI

**Examples:**
- `DataExtractionControllerGH` - GitHub data sync
- `IntegrationControllerJira` - Jira configuration
- `DataExtractionControllerSlack` - Slack data extraction

## Models Reference

### User & Organization
- `UsersModel` - User management (cognito_sub, roles, timezones)
- `OrganizationsModel` - Multi-tenant organization data
- `UserOrganizationsModel` - User-organization mapping
- `TeamUsersModel` - Team membership

### Metrics & Performance
- `BoardTasksModel` - Task/issue tracking
- `BoardTaskStatusesLogModel` - Status history for cycle time
- `RepositoriesModel` - Code repository metadata
- `CommitsModel` - Commit history and stats
- `MergeRequestsModel` - PR/MR data
- `UserInteractionsModel` - Collaboration interactions

### Dashboards & Views
- `DashboardsModel` - User dashboards
- `DashboardSharesModel` - Dashboard sharing
- `DashboardPublicLinksModel` - Public dashboard links
- `UserDashboardFiltersModel` - Saved filters per user

### Targets & Goals
- `TargetsModel` - Target definitions
- `AutomatedTargetsModel` - AI-calculated targets
- `PrimaryTargetModel` - Strategic goals
- `PrimaryTargetMetricsModel` - Target progress tracking

## Services

### DashboardAccessService
Manages dashboard access control with owned vs. shared resolution.

### SubscriptionAccessService
Checks organization subscription tier and enforces feature restrictions.

### OrganizationDashboardSettingsService
Manages organization-level dashboard configuration.

### BottleneckInsightsRunnerService
Core engine for bottleneck detection with Neo4j integration.

### OrgRoleScraperService
LinkedIn profile scraping for role auto-detection.

### EstimatedVsExecutedService
Calculates estimated vs. executed hours variance.

## Cron Jobs

### Execution Method
Routes are CLI-callable without external scheduler:
```bash
php public/index.php <route>
```

### Key Cron Jobs

**Distributed Queue:**
```bash
php public/index.php cron/distribute-executions/100
```

**GitHub:**
```bash
php public/index.php github/refresh-user-ids
php public/index.php github/get-repositories/{segment}
php public/index.php github/get-repositories-data
```

**Jira:**
```bash
php public/index.php jira/get-projects-data/{segment}
php public/index.php jira/get-project-statuses
php public/index.php jira/get-cron-project-issues/0/0/0/0
php public/index.php jira/delete-non-existent-tasks-cron/180/7
```

**GitLab:**
```bash
php public/index.php gitlab/refresh-tokens
php public/index.php gitlab/get-repositories/{segment}
php public/index.php gitlab/get-repositories-data
```

**Slack:**
```bash
php public/index.php slack/get-channels-data/{segment}
php public/index.php slack/get-interactions/{num}
php public/index.php slack/get-user-profile-data/{num}/{num}
```

**Analytics:**
```bash
php public/index.php cockpit/calculate-targets
php public/index.php bottleneck-insights/refresh-weekly
```

**Maintenance:**
```bash
php public/index.php auth/expire-login-tokens
php public/index.php google-calendar/refresh-tokens
```

## Filters (Middleware)

### CronExecutionLoggingFilter
Logs all CLI/cron executions with output and error capture.

### EnsureOrganizationFilter
Validates organization context for requests.

### SyncSessionRoleFilter
Updates session role from database for synchronization.

## Configuration

### Environment Variables (.env)

```bash
CI_ENVIRONMENT = development
WORKER_ENVIRONMENT = true

# Database
database.default.hostname = localhost
database.default.port = 13306
database.default.database = leanmote
database.default.username = leanmoteroot
database.default.password = [REDACTED]
database.default.charset = utf8mb4

# Email (AWS SES)
email.protocol = smtp
email.SMTPHost = email-smtp.us-east-1.amazonaws.com
email.SMTPUser = [IAM User]
email.SMTPPass = [REDACTED]
email.fromEmail = noreply@notify.leanmote.com

# OAuth (examples)
SLACK_CLIENT_ID = ...
GITLAB_CLIENT_ID = ...
NOTION_CLIENT_ID = ...
GITHUB_CLIENT_ID = ...
JIRA_CLIENT_ID = ...

# Neo4j (for bottleneck insights)
NEO4J_CLIENT_ID = ...
NEO4J_CLIENT_SECRET = ...

# Dashboard Data
DASHBOARD_DATA_URL = https://...lambda-url.../metrics
DASHBOARD_DATA_AUTH_TOKEN = ...
```

### AWS SES Configuration
Email settings are populated from `SES_CREDENTIALS_JSON` secret on AWS:
```json
{
  "user_iam": "leanmote-app-ses-user",
  "user_name": "AKIA...",
  "user_password": "wxyz..."
}
```

## Authentication Flow

1. **Cognito OAuth** - Primary authentication mechanism
2. **Magic Links** - Token-based login via email
3. **Session-based** - CodeIgniter session management

**Routes:**
- `/login` - Cognito redirect
- `/auth/cognito/callback` - Token exchange and user sync
- `/auth/verify-token/{segment}` - Magic link validation

## Dependencies

**Composer Packages:**
- `aws/aws-sdk-php` (^3.368.0) - AWS services
- `sentry/sentry` (^4.17) - Error tracking
- `laminas/laminas-escaper` (^2.13) - Security escaping
- `psr/log` (^3.0) - PSR logging

## Workflows

### Data Integration (Example: GitHub)
1. User authorizes via OAuth
2. Tokens stored in `user_apps_credentials`
3. Cron fetches repositories using token
4. User selects repositories to track
5. Cron syncs commits, PRs, reviews periodically
6. Interactions extracted and aggregated
7. Metrics calculated for dashboards

### Dashboard Rendering
1. User selects dashboard type and ID
2. DashboardAccessService checks permissions
3. Filters applied (date range, metrics, custom)
4. Data aggregated from synced sources
5. Metrics calculated using MetricCalculator
6. JSON response sent to frontend
7. Charts render with real-time data

### Bottleneck Detection
1. Weekly cron triggers analysis
2. Service calls Neo4j for graph analysis
3. Results compared against thresholds
4. Insights stored in database
5. Dashboard displays detected bottlenecks
6. Users can create Jira tickets directly
7. Feedback loop for model improvement
