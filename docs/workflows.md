# Workflow Documentation

## n8n Workflow Overview

The Automation Documentation Agent uses n8n workflows to orchestrate the entire document processing pipeline. This document outlines the key workflows and their configurations.

## Core Workflows

### 1. Document Processing Workflow

**Purpose**: Main workflow for processing uploaded documents and generating new content.

**Trigger**: Webhook from Telegram bot

**Nodes Configuration**:

#### Input Validation Node
```json
{
  "name": "Validate Input",
  "type": "n8n-nodes-base.function",
  "parameters": {
    "functionCode": "// Validate file type and size\nconst allowedTypes = ['pdf', 'docx', 'md'];\nconst maxSize = 10 * 1024 * 1024; // 10MB\n\nif (!allowedTypes.includes($input.item.json.file_type)) {\n  throw new Error('Invalid file type');\n}\n\nif ($input.item.json.file_size > maxSize) {\n  throw new Error('File too large');\n}\n\nreturn $input.item.json;"
  }
}
```

#### AI Processing Node
```json
{
  "name": "AI Document Processor",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "url": "{{ $env.AI_PROCESSOR_URL }}/process",
    "method": "POST",
    "headers": {
      "Authorization": "Bearer {{ $env.AI_API_KEY }}",
      "Content-Type": "application/json"
    },
    "body": {
      "file_id": "{{ $json.file_id }}",
      "processing_type": "{{ $json.processing_type }}",
      "context": {
        "project": "{{ $json.project }}",
        "template": "{{ $json.template }}"
      }
    }
  }
}
```

#### Notion Integration Node
```json
{
  "name": "Save to Notion",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "url": "{{ $env.NOTION_API_URL }}/documents",
    "method": "POST",
    "headers": {
      "Authorization": "Bearer {{ $env.NOTION_API_KEY }}",
      "Content-Type": "application/json"
    },
    "body": {
      "title": "{{ $json.title }}",
      "content": "{{ $json.processed_content }}",
      "template": "{{ $json.template }}",
      "database_id": "{{ $env.NOTION_DATABASE_ID }}"
    }
  }
}
```

### 2. Telegram Command Handler Workflow

**Purpose**: Handles different Telegram commands and routes them to appropriate workflows.

**Trigger**: Telegram webhook

**Command Routing**:

#### Brainstorm Command
```json
{
  "name": "Brainstorm Handler",
  "type": "n8n-nodes-base.switch",
  "parameters": {
    "rules": [
      {
        "conditions": {
          "string": [
            {
              "value1": "{{ $json.message.text }}",
              "operation": "startsWith",
              "value2": "/brainstorm"
            }
          ]
        },
        "outputIndex": 0
      }
    ]
  }
}
```

#### Generate Command
```json
{
  "name": "Generate Handler",
  "type": "n8n-nodes-base.switch",
  "parameters": {
    "rules": [
      {
        "conditions": {
          "string": [
            {
              "value1": "{{ $json.message.text }}",
              "operation": "startsWith",
              "value2": "/generate"
            }
          ]
        },
        "outputIndex": 1
      }
    ]
  }
}
```

### 3. GitHub Publishing Workflow

**Purpose**: Automatically commits processed documents to GitHub repositories.

**Trigger**: Completion of document processing

**Nodes Configuration**:

#### GitHub Commit Node
```json
{
  "name": "Commit to GitHub",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "url": "{{ $env.GITHUB_API_URL }}/repos/{{ $env.GITHUB_OWNER }}/{{ $json.repository }}/contents/{{ $json.file_path }}",
    "method": "PUT",
    "headers": {
      "Authorization": "token {{ $env.GITHUB_TOKEN }}",
      "Content-Type": "application/json"
    },
    "body": {
      "message": "{{ $json.commit_message }}",
      "content": "{{ $json.file_content }}",
      "branch": "{{ $json.branch || 'main' }}"
    }
  }
}
```

## Workflow Templates

### Document Generation Template

```json
{
  "name": "Document Generation Template",
  "nodes": [
    {
      "name": "Start",
      "type": "n8n-nodes-base.webhook",
      "parameters": {
        "path": "document-generation",
        "httpMethod": "POST"
      }
    },
    {
      "name": "Extract Context",
      "type": "n8n-nodes-base.function",
      "parameters": {
        "functionCode": "// Extract context from input\nconst context = {\n  topic: $input.item.json.topic,\n  audience: $input.item.json.audience,\n  format: $input.item.json.format,\n  length: $input.item.json.length\n};\n\nreturn { json: context };"
      }
    },
    {
      "name": "Generate Content",
      "type": "n8n-nodes-base.httpRequest",
      "parameters": {
        "url": "{{ $env.AI_PROCESSOR_URL }}/generate",
        "method": "POST",
        "body": {
          "context": "{{ $json }}",
          "template": "{{ $env.DEFAULT_TEMPLATE }}"
        }
      }
    },
    {
      "name": "Review Content",
      "type": "n8n-nodes-base.function",
      "parameters": {
        "functionCode": "// Basic content review\nconst content = $input.item.json.content;\nconst wordCount = content.split(' ').length;\nconst hasTitle = content.includes('# ');\nconst hasStructure = content.includes('## ');\n\nreturn {\n  json: {\n    ...$input.item.json,\n    review: {\n      wordCount,\n      hasTitle,\n      hasStructure,\n      quality: wordCount > 100 && hasTitle && hasStructure ? 'good' : 'needs_improvement'\n    }\n  }\n};"
      }
    },
    {
      "name": "Save to Notion",
      "type": "n8n-nodes-base.httpRequest",
      "parameters": {
        "url": "{{ $env.NOTION_API_URL }}/documents",
        "method": "POST",
        "body": {
          "title": "{{ $json.title }}",
          "content": "{{ $json.content }}",
          "status": "{{ $json.review.quality }}"
        }
      }
    }
  ],
  "connections": {
    "Start": {
      "main": [
        [
          {
            "node": "Extract Context",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Extract Context": {
      "main": [
        [
          {
            "node": "Generate Content",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Generate Content": {
      "main": [
        [
          {
            "node": "Review Content",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Review Content": {
      "main": [
        [
          {
            "node": "Save to Notion",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}
```

## Error Handling Workflows

### Retry Logic
```json
{
  "name": "Retry Failed Request",
  "type": "n8n-nodes-base.function",
  "parameters": {
    "functionCode": "// Implement exponential backoff\nconst maxRetries = 3;\nconst baseDelay = 1000; // 1 second\n\nconst retryCount = $input.item.json.retry_count || 0;\n\nif (retryCount >= maxRetries) {\n  throw new Error('Max retries exceeded');\n}\n\nconst delay = baseDelay * Math.pow(2, retryCount);\n\nreturn {\n  json: {\n    ...$input.item.json,\n    retry_count: retryCount + 1,\n    delay: delay\n  }\n};"
  }
}
```

### Error Notification
```json
{
  "name": "Send Error Notification",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "url": "{{ $env.TELEGRAM_API_URL }}/sendMessage",
    "method": "POST",
    "body": {
      "chat_id": "{{ $json.user_id }}",
      "text": "❌ Error processing your request: {{ $json.error_message }}",
      "parse_mode": "Markdown"
    }
  }
}
```

## Environment Variables

Required environment variables for n8n workflows:

```bash
# API Keys
TELEGRAM_BOT_TOKEN=your_telegram_bot_token
NOTION_API_KEY=your_notion_api_key
GITHUB_TOKEN=your_github_token
AI_API_KEY=your_openai_api_key

# Service URLs
AI_PROCESSOR_URL=http://localhost:8000
NOTION_API_URL=https://api.notion.com/v1
GITHUB_API_URL=https://api.github.com
TELEGRAM_API_URL=https://api.telegram.org/bot

# Configuration
NOTION_DATABASE_ID=your_database_id
GITHUB_OWNER=your_github_username
DEFAULT_TEMPLATE=standard_document_template
```

## Workflow Monitoring

### Health Check Workflow
```json
{
  "name": "Health Check",
  "type": "n8n-nodes-base.cron",
  "parameters": {
    "rule": {
      "interval": [
        {
          "field": "minutes",
          "minutesInterval": 5
        }
      ]
    }
  }
}
```

### Performance Metrics
- Workflow execution time
- Success/failure rates
- Queue processing times
- API response times
- Error frequency and types
