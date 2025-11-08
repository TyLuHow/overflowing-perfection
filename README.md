# Overflowing Perfection 🐝

Bee toxicity prediction project with automated Railway deployment monitoring and service recovery.

## Overview

This repository contains automated workflows for monitoring and managing the `bee-toxicity-predictor` service deployed on Railway. The system automatically restarts the service when failures are detected and provides manual restart capabilities.

## Features

- ✅ **Automatic Recovery**: Responds to Railway webhook events to restart failed services
- ✅ **Manual Restart**: Trigger service restarts manually through GitHub Actions
- ✅ **Comprehensive Logging**: Detailed logs for debugging and monitoring
- ✅ **Error Handling**: Production-ready error handling and validation
- ✅ **Status Verification**: Pre and post-restart status checks
- ✅ **Extensible Notifications**: Ready for integration with Slack, Discord, email, etc.

## Setup Instructions

### 1. Required GitHub Secrets

Add the following secrets to your repository (`Settings > Secrets and variables > Actions`):

| Secret Name | Description | How to Get |
|-------------|-------------|------------|
| `RAILWAY_API_TOKEN` | Railway API authentication token | [Railway Account Tokens](https://railway.app/account/tokens) |
| `RAILWAY_SERVICE_ID` | Service ID for bee-toxicity-predictor | Railway service URL or dashboard |
| `RAILWAY_PROJECT_ID` | Project ID (optional, for links) | Railway project URL |

#### Getting Your Railway API Token

1. Go to https://railway.app/account/tokens
2. Click "Create Token"
3. Give it a descriptive name (e.g., "GitHub Actions Restart Token")
4. Copy the generated token
5. Add it as a GitHub secret named `RAILWAY_API_TOKEN`

#### Getting Your Service ID

1. Go to your Railway project dashboard
2. Click on your service (bee-toxicity-predictor)
3. The Service ID can be found in:
   - The URL: `railway.app/project/{PROJECT_ID}/service/{SERVICE_ID}`
   - Service Settings > Service ID
4. Add it as a GitHub secret named `RAILWAY_SERVICE_ID`

### 2. Configure Railway Webhook (Optional - for Automatic Triggers)

To enable automatic restarts when Railway detects failures:

1. **Create a GitHub Personal Access Token (PAT)**:
   - Go to GitHub Settings > Developer settings > Personal access tokens > Tokens (classic)
   - Generate new token with `repo` scope
   - Copy the token

2. **Configure Railway Webhook**:
   - In Railway project settings, go to "Webhooks"
   - Create a new webhook:
     - **URL**: `https://api.github.com/repos/TyLuHow/overflowing-perfection/dispatches`
     - **Method**: POST
     - **Headers**: 
       - `Authorization: token YOUR_GITHUB_PAT`
       - `Content-Type: application/json`
     - **Body**: 
       ```json
       {
         "event_type": "railway-failure"
       }
       ```
   - Set trigger conditions (e.g., deployment failures)

### 3. Manual Restart

To manually restart the service:

1. Go to the "Actions" tab in this repository
2. Select "Railway Service Restart" workflow
3. Click "Run workflow"
4. Optionally add a reason for the restart
5. Click "Run workflow" to execute

## Workflow Details

### Triggers

- **Automatic**: `repository_dispatch` event with type `railway-failure`
- **Manual**: `workflow_dispatch` from GitHub Actions UI

### Steps

1. **Validate Environment**: Checks all required secrets are present
2. **Log Restart Context**: Records trigger type, timestamp, and context
3. **Get Service Status**: Queries Railway API for current service status
4. **Restart Service**: Initiates service redeployment via Railway GraphQL API
5. **Wait for Deployment**: Allows time for deployment to initialize
6. **Verify Deployment**: Confirms deployment is progressing
7. **Report Results**: Provides success/failure summary with troubleshooting info

### Error Handling

The workflow includes:
- ✅ Secret validation before execution
- ✅ HTTP status code checking
- ✅ Railway API error response parsing
- ✅ Graceful handling of status check failures
- ✅ Detailed error messages and troubleshooting steps
- ✅ Timeout protection (5 minutes max)

## Monitoring

View workflow execution:
- **GitHub Actions**: Repository > Actions tab
- **Railway Dashboard**: Direct links provided in workflow output

## Extending the Workflow

### Add Notifications

The workflow includes a placeholder for notifications. You can integrate:

**Slack Example**:
```yaml
- name: Send Slack Notification
  if: always()
  run: |
    curl -X POST -H 'Content-type: application/json' \
      --data '{"text":"🔄 Railway restart ${{ job.status }}: bee-toxicity-predictor"}' \
      ${{ secrets.SLACK_WEBHOOK_URL }}
```

**Discord Example**:
```yaml
- name: Send Discord Notification
  if: always()
  run: |
    curl -X POST -H 'Content-type: application/json' \
      --data '{"content":"🔄 Railway restart ${{ job.status }}: bee-toxicity-predictor"}' \
      ${{ secrets.DISCORD_WEBHOOK_URL }}
```

## Troubleshooting

### Workflow Fails with "RAILWAY_API_TOKEN secret is not set"

- Verify the secret is added to the repository
- Check the secret name is exactly `RAILWAY_API_TOKEN`
- Ensure you have admin access to add secrets

### Workflow Fails with "HTTP request failed"

- Verify your Railway API token is valid and not expired
- Check the token has sufficient permissions
- Confirm the service ID is correct

### Service Doesn't Restart

- Manually check Railway dashboard for service status
- Verify the service name matches `bee-toxicity-predictor`
- Check Railway API status: https://railway.app/status

## Resources

- [Railway API Documentation](https://docs.railway.app/reference/api)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Railway GraphQL Playground](https://railway.app/graphql)

## License

MIT License - feel free to use and modify for your projects.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
