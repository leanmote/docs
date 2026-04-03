# {Platform} Integration

Connect your {Platform} account to Leanmote for {benefit description}.

## Overview

The {Platform} integration allows Leanmote to:

- {Capability 1}
- {Capability 2}
- {Capability 3}

## Prerequisites

Before connecting {Platform}:

1. You must be a Leanmote admin or have integration permissions
2. You need a {Platform} account with appropriate access
3. {Any other prerequisites}

## Connection Steps

### Step 1: Initiate Connection

1. Navigate to **Settings > Integrations**
2. Find **{Platform}** in the list
3. Click **Connect**

### Step 2: Authorize Access

1. You'll be redirected to {Platform}'s authorization page
2. Review the permissions requested:
   - `scope_1` - Description of what this allows
   - `scope_2` - Description of what this allows
3. Click **Authorize** to grant access

### Step 3: Select Resources

After authorization:

1. Select which {resources} to track
2. Configure sync options if available
3. Click **Save Configuration**

### Step 4: Verify Connection

1. Check the integration status shows "Connected"
2. Wait for initial data sync to complete
3. Navigate to Dashboards to see your data

## Data Collected

| Data Type | Description | Update Frequency |
|-----------|-------------|------------------|
| {Type 1} | {Description} | {Frequency} |
| {Type 2} | {Description} | {Frequency} |
| {Type 3} | {Description} | {Frequency} |

## Permissions Required

| Permission | Reason |
|------------|--------|
| `permission_1` | {Why Leanmote needs this} |
| `permission_2` | {Why Leanmote needs this} |

## Configuration Options

### {Option Category 1}

{Description of configuration options}

### {Option Category 2}

{Description of configuration options}

## Webhook Configuration (Optional)

For real-time updates, configure webhooks in {Platform}:

1. Go to {Platform} settings
2. Navigate to Webhooks
3. Add webhook URL: `https://hooks.leanmote.com/{platform}`
4. Select events to send
5. Save configuration

## Troubleshooting

### Connection Failed

**Symptoms:** Unable to complete OAuth flow

**Solutions:**
1. Check {Platform} credentials are correct
2. Verify your {Platform} account has admin access
3. Clear browser cache and retry
4. Check {Platform} status page for outages

### Data Not Syncing

**Symptoms:** Connected but no data appearing

**Solutions:**
1. Check Integration Status for error messages
2. Verify selected resources still exist
3. Check if {Platform} API is responding
4. Wait for next sync cycle (usually every {X} hours)

### Expired Credentials

**Symptoms:** Integration shows "Needs Reconnection"

**Solutions:**
1. Click "Reconnect" in Integration Settings
2. Re-authorize access
3. Your previous configuration will be preserved

## Frequently Asked Questions

### How often does data sync?

{Answer about sync frequency}

### What historical data is imported?

{Answer about historical data limits}

### Can I connect multiple {Platform} accounts?

{Answer about multiple accounts}

### How do I disconnect the integration?

1. Go to Settings > Integrations
2. Find {Platform}
3. Click "Disconnect"
4. Confirm disconnection

Note: Historical data will be retained.

## Security

### Data Access

Leanmote only requests read-only access to:
- {Data type 1}
- {Data type 2}

### Token Storage

- OAuth tokens are encrypted at rest
- Tokens are refreshed automatically
- Tokens are revoked on disconnection

### Data Handling

- Data is stored in your organization's tenant
- No data is shared between organizations
- Data can be deleted on request

## Related Documentation

- [Integrations Overview](overview.md)
- [API Reference](../api/overview.md)
- [Troubleshooting Guide](../operations/troubleshooting.md)
