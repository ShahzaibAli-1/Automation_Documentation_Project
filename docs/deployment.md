# Deployment Guide

## Overview

This guide covers the deployment of the Automation Documentation Agent across different environments and platforms.

## Prerequisites

- Docker and Docker Compose
- Node.js 18+ and npm
- Python 3.9+ and pip
- Git
- Access to required API keys and services

## Environment Setup

### 1. Local Development

#### Clone Repository
```bash
git clone https://github.com/yourusername/automation-documentation-agent.git
cd automation-documentation-agent
```

#### Environment Variables
Create `.env` file:
```bash
# Telegram Configuration
TELEGRAM_BOT_TOKEN=your_telegram_bot_token
TELEGRAM_WEBHOOK_URL=https://your-domain.com/webhook/telegram

# Notion Configuration
NOTION_API_KEY=your_notion_api_key
NOTION_DATABASE_ID=your_database_id

# GitHub Configuration
GITHUB_TOKEN=your_github_token
GITHUB_OWNER=your_github_username

# AI Configuration
OPENAI_API_KEY=your_openai_api_key
AI_MODEL=gpt-4

# n8n Configuration
N8N_WEBHOOK_URL=https://your-n8n-instance.com/webhook
N8N_API_KEY=your_n8n_api_key

# Database Configuration
DATABASE_URL=postgresql://user:password@localhost:5432/automation_docs

# Redis Configuration
REDIS_URL=redis://localhost:6379

# Application Configuration
NODE_ENV=development
PORT=3000
LOG_LEVEL=debug
```

#### Install Dependencies
```bash
# Install Node.js dependencies
npm install

# Install Python dependencies
pip install -r requirements.txt

# Install n8n globally
npm install -g n8n
```

#### Start Services
```bash
# Start database
docker-compose up -d postgres redis

# Start n8n
n8n start

# Start Telegram bot
npm run start:bot

# Start AI processor
python src/ai-processor/main.py

# Start API server
npm run start:api
```

### 2. Docker Deployment

#### Docker Compose Configuration
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: automation_docs
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  telegram-bot:
    build: .
    environment:
      - NODE_ENV=production
      - TELEGRAM_BOT_TOKEN=${TELEGRAM_BOT_TOKEN}
      - NOTION_API_KEY=${NOTION_API_KEY}
      - GITHUB_TOKEN=${GITHUB_TOKEN}
      - OPENAI_API_KEY=${OPENAI_API_KEY}
    depends_on:
      - postgres
      - redis
    ports:
      - "3000:3000"

  ai-processor:
    build: ./src/ai-processor
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - DATABASE_URL=${DATABASE_URL}
    depends_on:
      - postgres
    ports:
      - "8000:8000"

  n8n:
    image: n8nio/n8n
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=password
      - WEBHOOK_URL=https://your-domain.com
    volumes:
      - n8n_data:/home/node/.n8n
    ports:
      - "5678:5678"

volumes:
  postgres_data:
  n8n_data:
```

#### Deploy with Docker Compose
```bash
# Build and start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

### 3. Cloud Deployment

#### AWS Deployment

##### EC2 Instance Setup
```bash
# Update system
sudo yum update -y

# Install Docker
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Clone repository
git clone https://github.com/yourusername/automation-documentation-agent.git
cd automation-documentation-agent

# Configure environment
cp .env.example .env
# Edit .env with production values

# Start services
docker-compose up -d
```

##### RDS Database Setup
```bash
# Create RDS PostgreSQL instance
aws rds create-db-instance \
  --db-instance-identifier automation-docs-db \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --master-username postgres \
  --master-user-password your-password \
  --allocated-storage 20
```

##### Elastic Beanstalk Deployment
```bash
# Install EB CLI
pip install awsebcli

# Initialize EB application
eb init automation-docs-app

# Create environment
eb create production

# Deploy
eb deploy
```

#### Google Cloud Platform

##### Cloud Run Deployment
```bash
# Build and push image
gcloud builds submit --tag gcr.io/PROJECT_ID/automation-docs

# Deploy to Cloud Run
gcloud run deploy automation-docs \
  --image gcr.io/PROJECT_ID/automation-docs \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated
```

##### Cloud SQL Setup
```bash
# Create Cloud SQL instance
gcloud sql instances create automation-docs-db \
  --database-version=POSTGRES_15 \
  --tier=db-f1-micro \
  --region=us-central1
```

#### Azure Deployment

##### Container Instances
```bash
# Create resource group
az group create --name automation-docs-rg --location eastus

# Deploy container
az container create \
  --resource-group automation-docs-rg \
  --name automation-docs \
  --image your-registry/automation-docs:latest \
  --dns-name-label automation-docs \
  --ports 3000
```

## Configuration Management

### Environment-Specific Configs

#### Development
```bash
NODE_ENV=development
LOG_LEVEL=debug
TELEGRAM_WEBHOOK_URL=http://localhost:3000/webhook/telegram
```

#### Staging
```bash
NODE_ENV=staging
LOG_LEVEL=info
TELEGRAM_WEBHOOK_URL=https://staging.your-domain.com/webhook/telegram
```

#### Production
```bash
NODE_ENV=production
LOG_LEVEL=warn
TELEGRAM_WEBHOOK_URL=https://your-domain.com/webhook/telegram
```

### Secrets Management

#### Using AWS Secrets Manager
```bash
# Store secrets
aws secretsmanager create-secret \
  --name automation-docs/secrets \
  --secret-string '{"telegram_token":"your_token","notion_key":"your_key"}'

# Retrieve secrets in application
const secrets = await secretsManager.getSecretValue({
  SecretId: 'automation-docs/secrets'
}).promise();
```

#### Using Azure Key Vault
```bash
# Create key vault
az keyvault create --name automation-docs-vault --resource-group automation-docs-rg

# Store secrets
az keyvault secret set --vault-name automation-docs-vault --name telegram-token --value your_token
```

## Monitoring and Logging

### Application Monitoring

#### Health Checks
```javascript
// Health check endpoint
app.get('/health', async (req, res) => {
  const health = {
    status: 'ok',
    timestamp: new Date().toISOString(),
    services: {
      database: await checkDatabase(),
      redis: await checkRedis(),
      telegram: await checkTelegram(),
      notion: await checkNotion(),
      github: await checkGitHub()
    }
  };
  
  res.json(health);
});
```

#### Logging Configuration
```javascript
// Winston logger configuration
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
    new winston.transports.Console({
      format: winston.format.simple()
    })
  ]
});
```

### Performance Monitoring

#### Metrics Collection
```javascript
// Prometheus metrics
const prometheus = require('prom-client');

const httpRequestDuration = new prometheus.Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route', 'status']
});

const documentProcessingCounter = new prometheus.Counter({
  name: 'documents_processed_total',
  help: 'Total number of documents processed',
  labelNames: ['type', 'status']
});
```

## Backup and Recovery

### Database Backup
```bash
# Automated backup script
#!/bin/bash
BACKUP_DIR="/backups"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/automation_docs_$DATE.sql"

pg_dump -h localhost -U postgres automation_docs > $BACKUP_FILE

# Compress backup
gzip $BACKUP_FILE

# Upload to S3
aws s3 cp $BACKUP_FILE.gz s3://your-backup-bucket/
```

### Configuration Backup
```bash
# Backup n8n workflows
tar -czf n8n_backup_$(date +%Y%m%d).tar.gz ~/.n8n/

# Backup environment files
cp .env .env.backup.$(date +%Y%m%d)
```

## Security Considerations

### SSL/TLS Configuration
```nginx
# Nginx SSL configuration
server {
    listen 443 ssl;
    server_name your-domain.com;
    
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    
    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Firewall Configuration
```bash
# UFW firewall rules
sudo ufw allow 22/tcp    # SSH
sudo ufw allow 80/tcp    # HTTP
sudo ufw allow 443/tcp   # HTTPS
sudo ufw allow 3000/tcp  # Application
sudo ufw enable
```

## Troubleshooting

### Common Issues

#### Telegram Webhook Issues
```bash
# Check webhook status
curl "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/getWebhookInfo"

# Set webhook
curl -X POST "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/setWebhook" \
  -d "url=https://your-domain.com/webhook/telegram"
```

#### Database Connection Issues
```bash
# Test database connection
psql -h localhost -U postgres -d automation_docs -c "SELECT 1;"

# Check database logs
docker logs automation-docs-postgres
```

#### n8n Workflow Issues
```bash
# Check n8n logs
docker logs automation-docs-n8n

# Restart n8n
docker-compose restart n8n
```

### Performance Optimization

#### Database Optimization
```sql
-- Create indexes for better performance
CREATE INDEX idx_documents_created_at ON documents(created_at);
CREATE INDEX idx_documents_status ON documents(status);
CREATE INDEX idx_documents_user_id ON documents(user_id);
```

#### Caching Strategy
```javascript
// Redis caching
const redis = require('redis');
const client = redis.createClient(process.env.REDIS_URL);

// Cache document processing results
const cacheKey = `doc:${documentId}`;
await client.setex(cacheKey, 3600, JSON.stringify(result));
```
