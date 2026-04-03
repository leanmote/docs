# Quick Start

Get Leanmote running locally in minutes.

## Prerequisites

- PHP 8.1+
- Composer
- MySQL 5.7+ or 8.0+
- Node.js 18+ (for frontend assets)
- Python 3.11+ (for Lambda functions and Insight Agent)

## Installation

### 1. Clone the Repositories

```bash
# Main web application
git clone https://github.com/leanmote/leanmote.git
cd leanmote

# Lambda functions (optional, for local development)
git clone https://github.com/leanmote/lambda.git

# Insight Agent (optional, for AI features)
git clone https://github.com/leanmote/insight_agent.git
```

### 2. Configure Environment

Copy the example environment file:

```bash
cp env.example .env
```

Edit `.env` with your settings:

```bash
# Application
CI_ENVIRONMENT = development
app.baseURL = 'http://localhost:8080'

# Database
database.default.hostname = localhost
database.default.database = leanmote
database.default.username = your_username
database.default.password = your_password
database.default.port = 3306

# Email (for magic link login)
email.protocol = smtp
email.SMTPHost = smtp.example.com
email.SMTPUser = your_email@example.com
email.SMTPPass = your_password
email.SMTPPort = 587
email.SMTPCrypto = tls
```

### 3. Install Dependencies

```bash
composer install
```

### 4. Set Up Database

Create the database:

```bash
mysql -u root -p -e "CREATE DATABASE leanmote CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"
```

Import the schema:

```bash
mysql -u root -p leanmote < database/model.sql
```

### 5. Start the Server

```bash
php spark serve
```

Access the application at `http://localhost:8080`.

## First Steps

### 1. Create an Organization

After logging in, create your first organization:

1. Navigate to Settings > Organizations
2. Click "Create Organization"
3. Enter your organization name and details

### 2. Connect Your First Integration

Connect a data source:

1. Go to Settings > Integrations
2. Select a platform (e.g., GitHub)
3. Click "Connect" and authorize access
4. Select repositories to track

### 3. View Your Dashboard

Once data starts syncing:

1. Navigate to Dashboards
2. Select "Performance & Delivery"
3. View your DORA and flow metrics

## Running Cron Jobs

Leanmote uses cron jobs to sync data from external sources. Run them manually:

```bash
# Sync GitHub repositories
php public/index.php github/get-repositories/all

# Sync Jira issues
php public/index.php jira/get-cron-project-issues/0/0/0/0

# Distribute execution queue
php public/index.php cron/distribute-executions/100
```

For production, set up cron jobs to run these commands periodically.

## Next Steps

- [Configuration Guide](configuration.md) - Advanced configuration options
- [Integrations](../integrations/overview.md) - Connect more data sources
- [API Reference](../api/overview.md) - Integrate with external systems
