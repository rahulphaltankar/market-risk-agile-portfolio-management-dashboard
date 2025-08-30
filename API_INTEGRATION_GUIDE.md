# API Integration Guide - Agile Portfolio Management Dashboard

## Overview

This guide provides comprehensive instructions for integrating the Agile Portfolio Management Dashboard with enterprise systems commonly used in banking and financial services environments. The dashboard is designed to work with existing toolchains and data sources.

## Integration Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Dashboard     │◄──►│  API Gateway     │◄──►│  Enterprise     │
│   Frontend      │    │  & Auth Layer    │    │  Systems        │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                              │                         │
                              ▼                         ▼
                       ┌──────────────┐         ┌──────────────┐
                       │  Caching     │         │  Data        │
                       │  Layer       │         │  Warehouse   │
                       └──────────────┘         └──────────────┘
```

## Primary Integration Targets

### 1. JIRA Integration

#### Epic and Story Management
```javascript
class JiraIntegration {
    constructor(baseUrl, credentials) {
        this.baseUrl = baseUrl;
        this.auth = credentials;
    }

    // Fetch epics for market risk projects
    async getMarketRiskEpics() {
        const jql = 'project = "Market Risk" AND issuetype = Epic';
        return await this.searchIssues(jql);
    }

    // Update epic status
    async updateEpicStatus(epicKey, status) {
        return await this.transitionIssue(epicKey, status);
    }

    // Get ART velocity data
    async getARTVelocity(artName, piNumber) {
        const jql = `fixVersion = "${piNumber}" AND labels = "${artName}"`;
        return await this.calculateVelocity(jql);
    }
}
```

#### Configuration Example
```json
{
  "jira": {
    "baseUrl": "https://yourbank.atlassian.net",
    "authentication": {
      "type": "bearer",
      "token": "${JIRA_API_TOKEN}"
    },
    "projectKeys": ["MR", "RISK", "CCR"],
    "epicCustomFields": {
      "businessValue": "customfield_10101",
      "effortEstimate": "customfield_10102", 
      "regulatoryDriver": "customfield_10103"
    }
  }
}
```

### 2. Azure DevOps Integration

#### Work Item and Sprint Management
```javascript
class AzureDevOpsIntegration {
    constructor(organization, project, pat) {
        this.baseUrl = `https://dev.azure.com/${organization}/${project}`;
        this.headers = {
            'Authorization': `Basic ${Buffer.from(':' + pat).toString('base64')}`
        };
    }

    // Get work items by iteration
    async getIterationWorkItems(iterationPath) {
        const wiql = `
            SELECT [System.Id], [System.Title], [System.State]
            FROM WorkItems 
            WHERE [System.IterationPath] = '${iterationPath}'
        `;
        return await this.queryWorkItems(wiql);
    }

    // Get team capacity
    async getTeamCapacity(teamId, iterationId) {
        const url = `${this.baseUrl}/_apis/work/teamsettings/iterations/${iterationId}/capacities`;
        return await fetch(url, { headers: this.headers });
    }
}
```

### 3. ServiceNow Integration

#### Change Management and ITSM
```javascript
class ServiceNowIntegration {
    constructor(instance, credentials) {
        this.baseUrl = `https://${instance}.service-now.com`;
        this.auth = credentials;
    }

    // Create change request for production deployment
    async createChangeRequest(piDeployment) {
        const changeRequest = {
            short_description: `PI ${piDeployment.number} Market Risk Deployment`,
            description: piDeployment.description,
            category: 'Software',
            priority: this.mapPriorityFromRisk(piDeployment.riskLevel),
            planned_start_date: piDeployment.startDate,
            planned_end_date: piDeployment.endDate
        };
        
        return await this.createRecord('change_request', changeRequest);
    }

    // Track deployment incidents
    async getDeploymentIncidents(deploymentId) {
        const query = `correlation_id=${deploymentId}^state=New^ORstate=In Progress`;
        return await this.getRecords('incident', query);
    }
}
```

### 4. Confluence Integration

#### Documentation and Knowledge Management
```javascript
class ConfluenceIntegration {
    constructor(baseUrl, credentials) {
        this.baseUrl = baseUrl;
        this.auth = credentials;
    }

    // Create PI planning documentation
    async createPIPlanningPage(piData) {
        const content = this.generatePIPlanningTemplate(piData);
        return await this.createPage({
            space: 'RISK',
            title: `PI ${piData.number} Planning - Market Risk`,
            body: content,
            parent: 'PI Planning Home'
        });
    }

    // Update ART retrospective notes
    async updateRetrospective(artName, piNumber, retrospectiveData) {
        const pageId = await this.findPage(`${artName} PI ${piNumber} Retrospective`);
        return await this.updatePage(pageId, retrospectiveData);
    }
}
```

## Data Synchronization Patterns

### 1. Real-time Updates via Webhooks

```javascript
class WebhookHandler {
    constructor(dashboardApi) {
        this.dashboard = dashboardApi;
    }

    // Handle JIRA epic updates
    async handleJiraWebhook(payload) {
        if (payload.issue.fields.issuetype.name === 'Epic') {
            await this.dashboard.updateEpic({
                id: payload.issue.key,
                status: payload.issue.fields.status.name,
                businessValue: payload.issue.fields.customfield_10101
            });
        }
    }

    // Handle Azure DevOps work item updates
    async handleAzureDevOpsWebhook(payload) {
        if (payload.eventType === 'workitem.updated') {
            await this.dashboard.updateFeature({
                id: payload.resource.id,
                state: payload.resource.fields['System.State']
            });
        }
    }
}
```

### 2. Scheduled Data Refresh

```javascript
class DataRefreshService {
    constructor(integrations, dashboard) {
        this.integrations = integrations;
        this.dashboard = dashboard;
    }

    // Daily refresh of portfolio metrics
    async dailyRefresh() {
        try {
            // Fetch epic updates from JIRA
            const epics = await this.integrations.jira.getMarketRiskEpics();
            await this.dashboard.syncEpics(epics);

            // Update ART performance metrics
            const artMetrics = await this.fetchARTMetrics();
            await this.dashboard.updateARTPerformance(artMetrics);

            // Refresh PI planning data
            const piData = await this.fetchPIPlanningData();
            await this.dashboard.updatePIPlanning(piData);

        } catch (error) {
            console.error('Daily refresh failed:', error);
            await this.notifyAdministrators(error);
        }
    }
}
```

## Authentication and Security

### OAuth 2.0 Integration
```javascript
class AuthenticationService {
    constructor(config) {
        this.config = config;
    }

    // Azure AD integration for enterprise SSO
    async authenticateWithAzureAD(code) {
        const tokenResponse = await fetch('https://login.microsoftonline.com/tenant/oauth2/v2.0/token', {
            method: 'POST',
            headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
            body: new URLSearchParams({
                client_id: this.config.clientId,
                client_secret: this.config.clientSecret,
                code: code,
                grant_type: 'authorization_code'
            })
        });
        
        return await tokenResponse.json();
    }

    // SAML integration for enterprise authentication
    async validateSAMLResponse(samlResponse) {
        return await this.validateSignature(samlResponse);
    }
}
```

### API Security Headers
```javascript
const securityHeaders = {
    'Content-Security-Policy': "default-src 'self'; script-src 'self' 'unsafe-inline'",
    'X-Frame-Options': 'DENY',
    'X-Content-Type-Options': 'nosniff',
    'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
};
```

## Data Transformation and Mapping

### JIRA to Dashboard Mapping
```javascript
const jiraToEpicMapping = {
    id: 'key',
    title: 'fields.summary', 
    businessValue: 'fields.customfield_10101',
    effortEstimate: 'fields.customfield_10102',
    status: 'fields.status.name',
    owner: 'fields.assignee.displayName',
    regulatoryDriver: 'fields.customfield_10103'
};

function transformJiraEpic(jiraIssue) {
    return Object.keys(jiraToEpicMapping).reduce((epic, key) => {
        const path = jiraToEpicMapping[key];
        epic[key] = getNestedProperty(jiraIssue, path);
        return epic;
    }, {});
}
```

### Azure DevOps to Dashboard Mapping
```javascript
function transformAzureDevOpsWorkItem(workItem) {
    return {
        id: workItem.id,
        title: workItem.fields['System.Title'],
        state: workItem.fields['System.State'],
        assignedTo: workItem.fields['System.AssignedTo']?.displayName,
        storyPoints: workItem.fields['Microsoft.VSTS.Scheduling.StoryPoints'],
        iterationPath: workItem.fields['System.IterationPath']
    };
}
```

## Performance Optimization

### Caching Strategy
```javascript
class CacheManager {
    constructor(redisClient) {
        this.cache = redisClient;
    }

    // Cache epic data with TTL
    async cacheEpics(epics, ttl = 300) {
        const key = 'dashboard:epics';
        await this.cache.setex(key, ttl, JSON.stringify(epics));
    }

    // Get cached data with fallback
    async getEpicsWithFallback() {
        const cached = await this.cache.get('dashboard:epics');
        if (cached) {
            return JSON.parse(cached);
        }
        
        // Fallback to API call
        const epics = await this.fetchEpicsFromAPI();
        await this.cacheEpics(epics);
        return epics;
    }
}
```

### Rate Limiting
```javascript
class RateLimiter {
    constructor(maxRequests = 100, windowMs = 60000) {
        this.requests = new Map();
        this.maxRequests = maxRequests;
        this.windowMs = windowMs;
    }

    async checkLimit(apiKey) {
        const now = Date.now();
        const windowStart = now - this.windowMs;
        
        if (!this.requests.has(apiKey)) {
            this.requests.set(apiKey, []);
        }
        
        const apiRequests = this.requests.get(apiKey);
        const recentRequests = apiRequests.filter(time => time > windowStart);
        
        if (recentRequests.length >= this.maxRequests) {
            throw new Error('Rate limit exceeded');
        }
        
        recentRequests.push(now);
        this.requests.set(apiKey, recentRequests);
    }
}
```

## Deployment Configuration

### Environment-specific Configuration
```yaml
# config/production.yml
integrations:
  jira:
    baseUrl: https://bankprod.atlassian.net
    maxRetries: 3
    timeout: 30000
  
  azureDevOps:
    organization: bank-prod
    project: MarketRisk
    maxConcurrentRequests: 10
  
  serviceNow:
    instance: bankprod
    requestTimeout: 45000

caching:
  redis:
    host: redis-cluster.internal
    port: 6379
    ttl: 300

monitoring:
  enableMetrics: true
  logLevel: info
  alerting:
    enabled: true
    thresholds:
      apiResponseTime: 2000
      errorRate: 0.05
```

### Container Configuration
```dockerfile
# Dockerfile for API integration service
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 8080
CMD ["npm", "start"]
```

### Kubernetes Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: portfolio-dashboard-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: portfolio-dashboard-api
  template:
    metadata:
      labels:
        app: portfolio-dashboard-api
    spec:
      containers:
      - name: api
        image: portfolio-dashboard-api:latest
        ports:
        - containerPort: 8080
        env:
        - name: JIRA_API_TOKEN
          valueFrom:
            secretKeyRef:
              name: integration-secrets
              key: jira-token
```

## Monitoring and Alerting

### Health Check Endpoints
```javascript
class HealthChecker {
    async checkIntegrationHealth() {
        const checks = await Promise.allSettled([
            this.checkJiraConnectivity(),
            this.checkAzureDevOpsConnectivity(),
            this.checkServiceNowConnectivity()
        ]);
        
        return {
            status: checks.every(c => c.status === 'fulfilled') ? 'healthy' : 'degraded',
            checks: checks.map((check, index) => ({
                service: ['jira', 'azureDevOps', 'serviceNow'][index],
                status: check.status,
                error: check.reason?.message
            }))
        };
    }
}
```

### Metrics Collection
```javascript
// Prometheus metrics integration
const prometheus = require('prom-client');

const apiRequestDuration = new prometheus.Histogram({
    name: 'api_request_duration_seconds',
    help: 'Duration of API requests in seconds',
    labelNames: ['integration', 'endpoint', 'status']
});

const integrationErrors = new prometheus.Counter({
    name: 'integration_errors_total',
    help: 'Total number of integration errors',
    labelNames: ['integration', 'error_type']
});
```

## Troubleshooting Guide

### Common Issues and Solutions

**Issue**: JIRA API rate limiting
**Solution**: Implement exponential backoff and request queueing

**Issue**: Azure DevOps authentication failures  
**Solution**: Refresh PAT tokens and verify permissions

**Issue**: ServiceNow timeout errors
**Solution**: Increase timeout values and implement retry logic

**Issue**: Data synchronization delays
**Solution**: Optimize queries and implement incremental updates

For additional support and advanced integration scenarios, consult the enterprise architecture team and review the API documentation for each integrated system.