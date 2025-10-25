# API Documentation

## Overview

The Automation Documentation Agent provides several API endpoints for integration with external systems and internal service communication.

## Base URL

```
Production: https://api.automation-docs.com/v1
Development: http://localhost:3000/v1
```

## Authentication

All API requests require authentication using API keys or JWT tokens.

### Headers

```http
Authorization: Bearer <your-api-key>
Content-Type: application/json
```

## Endpoints

### Telegram Bot API

#### Send Message
```http
POST /telegram/send-message
```

**Request Body:**
```json
{
  "chat_id": "string",
  "message": "string",
  "parse_mode": "Markdown",
  "reply_markup": {
    "inline_keyboard": [
      [
        {
          "text": "Generate Doc",
          "callback_data": "generate_doc"
        }
      ]
    ]
  }
}
```

**Response:**
```json
{
  "success": true,
  "message_id": 123,
  "timestamp": "2024-01-01T00:00:00Z"
}
```

#### Upload File
```http
POST /telegram/upload-file
```

**Request Body:**
```json
{
  "chat_id": "string",
  "file": "base64-encoded-file",
  "filename": "document.pdf",
  "file_type": "pdf"
}
```

**Response:**
```json
{
  "success": true,
  "file_id": "string",
  "processing_status": "queued"
}
```

### Document Processing API

#### Process Document
```http
POST /documents/process
```

**Request Body:**
```json
{
  "file_id": "string",
  "processing_type": "extract|summarize|generate",
  "context": {
    "project": "string",
    "template": "string",
    "language": "en"
  }
}
```

**Response:**
```json
{
  "success": true,
  "document_id": "string",
  "status": "processing",
  "estimated_completion": "2024-01-01T00:05:00Z"
}
```

#### Get Document Status
```http
GET /documents/{document_id}/status
```

**Response:**
```json
{
  "success": true,
  "status": "completed",
  "progress": 100,
  "result": {
    "extracted_text": "string",
    "summary": "string",
    "generated_content": "string"
  }
}
```

### Notion Integration API

#### Create Document
```http
POST /notion/documents
```

**Request Body:**
```json
{
  "title": "string",
  "content": "string",
  "template": "string",
  "database_id": "string",
  "properties": {
    "category": "string",
    "priority": "high|medium|low",
    "tags": ["string"]
  }
}
```

**Response:**
```json
{
  "success": true,
  "page_id": "string",
  "url": "string",
  "created_at": "2024-01-01T00:00:00Z"
}
```

#### Update Document
```http
PUT /notion/documents/{page_id}
```

**Request Body:**
```json
{
  "content": "string",
  "properties": {
    "status": "updated"
  }
}
```

### GitHub Integration API

#### Create Repository
```http
POST /github/repositories
```

**Request Body:**
```json
{
  "name": "string",
  "description": "string",
  "private": false,
  "template": "documentation-template"
}
```

**Response:**
```json
{
  "success": true,
  "repository_url": "string",
  "clone_url": "string"
}
```

#### Commit Document
```http
POST /github/commit
```

**Request Body:**
```json
{
  "repository": "string",
  "branch": "main",
  "files": [
    {
      "path": "docs/document.md",
      "content": "string",
      "message": "Update documentation"
    }
  ],
  "commit_message": "string"
}
```

## Webhook Endpoints

### n8n Webhooks

#### Document Processing Webhook
```http
POST /webhooks/n8n/document-processed
```

**Payload:**
```json
{
  "document_id": "string",
  "status": "completed|failed",
  "result": "object",
  "error": "string"
}
```

#### Telegram Event Webhook
```http
POST /webhooks/telegram/events
```

**Payload:**
```json
{
  "update_id": 123,
  "message": {
    "message_id": 123,
    "from": {
      "id": 123,
      "username": "string"
    },
    "chat": {
      "id": 123,
      "type": "private"
    },
    "text": "string",
    "document": {
      "file_id": "string",
      "file_name": "string",
      "mime_type": "string"
    }
  }
}
```

## Error Handling

### Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid file format",
    "details": {
      "field": "file_type",
      "expected": ["pdf", "docx", "md"],
      "received": "txt"
    }
  },
  "timestamp": "2024-01-01T00:00:00Z"
}
```

### Error Codes

| Code | Description | HTTP Status |
|------|-------------|-------------|
| `VALIDATION_ERROR` | Invalid input data | 400 |
| `AUTHENTICATION_ERROR` | Invalid API key | 401 |
| `AUTHORIZATION_ERROR` | Insufficient permissions | 403 |
| `NOT_FOUND` | Resource not found | 404 |
| `RATE_LIMIT_EXCEEDED` | Too many requests | 429 |
| `INTERNAL_ERROR` | Server error | 500 |
| `SERVICE_UNAVAILABLE` | External service down | 503 |

## Rate Limiting

- **Free Tier**: 100 requests/hour
- **Pro Tier**: 1000 requests/hour
- **Enterprise**: Custom limits

Rate limit headers:
```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
```

## SDKs and Libraries

### JavaScript/Node.js
```bash
npm install automation-docs-sdk
```

```javascript
const AutomationDocs = require('automation-docs-sdk');

const client = new AutomationDocs({
  apiKey: 'your-api-key',
  environment: 'production'
});

// Process document
const result = await client.documents.process({
  fileId: 'file-123',
  processingType: 'extract'
});
```

### Python
```bash
pip install automation-docs-python
```

```python
from automation_docs import AutomationDocsClient

client = AutomationDocsClient(
    api_key='your-api-key',
    environment='production'
)

# Process document
result = client.documents.process(
    file_id='file-123',
    processing_type='extract'
)
```
