# Automation Documentation Agent - Project Structure

This document outlines the project structure and organization of the Automation Documentation Agent.

## Directory Structure

```
automation-documentation-agent/
├── src/                          # Source code
│   ├── telegram-bot/            # Telegram bot implementation
│   │   ├── index.js             # Main bot entry point
│   │   ├── commands/            # Bot command handlers
│   │   ├── middleware/          # Bot middleware
│   │   └── utils/               # Bot utilities
│   ├── ai-processor/            # AI document processing
│   │   ├── main.py              # AI processor entry point
│   │   ├── models/              # AI model implementations
│   │   ├── processors/          # Document processors
│   │   └── utils/               # AI utilities
│   ├── integrations/            # External service integrations
│   │   ├── notion/              # Notion API integration
│   │   ├── github/              # GitHub API integration
│   │   └── telegram/            # Telegram API integration
│   ├── api/                     # REST API server
│   │   ├── server.js            # API server entry point
│   │   ├── routes/              # API routes
│   │   ├── middleware/          # API middleware
│   │   └── controllers/         # API controllers
│   ├── database/                # Database layer
│   │   ├── models/              # Database models
│   │   ├── migrations/          # Database migrations
│   │   └── seeds/               # Database seeds
│   └── utils/                   # Shared utilities
│       ├── logger.js            # Logging utilities
│       ├── config.js            # Configuration management
│       └── validators.js        # Input validation
├── n8n/                         # n8n workflow definitions
│   ├── workflows/               # Workflow JSON files
│   ├── nodes/                   # Custom n8n nodes
│   └── templates/               # Workflow templates
├── docs/                        # Documentation
│   ├── architecture.md          # System architecture
│   ├── api.md                   # API documentation
│   ├── workflows.md             # Workflow documentation
│   ├── deployment.md             # Deployment guide
│   └── user-guide.md            # User guide
├── templates/                   # Document templates
│   ├── markdown/                # Markdown templates
│   ├── notion/                  # Notion page templates
│   └── github/                  # GitHub README templates
├── tests/                       # Test files
│   ├── unit/                    # Unit tests
│   ├── integration/             # Integration tests
│   ├── e2e/                     # End-to-end tests
│   └── fixtures/                # Test data
├── scripts/                     # Utility scripts
│   ├── setup.sh                 # Setup script
│   ├── deploy.sh                # Deployment script
│   └── backup.sh                # Backup script
├── uploads/                     # File upload directory
├── logs/                        # Application logs
├── temp/                        # Temporary files
├── .github/                     # GitHub workflows
│   └── workflows/               # CI/CD pipelines
├── .gitignore                   # Git ignore rules
├── .env.example                 # Environment variables template
├── .eslintrc.js                 # ESLint configuration
├── .prettierrc                  # Prettier configuration
├── jest.config.js               # Jest configuration
├── tsconfig.json                # TypeScript configuration
├── package.json                 # Node.js dependencies
├── requirements.txt             # Python dependencies
├── docker-compose.yml           # Docker Compose configuration
├── Dockerfile                   # Docker configuration
├── README.md                    # Project README
├── CONTRIBUTING.md              # Contributing guidelines
├── CODE_OF_CONDUCT.md           # Code of conduct
└── LICENSE                      # MIT License
```

## File Descriptions

### Source Code (`src/`)

#### Telegram Bot (`src/telegram-bot/`)
- **index.js**: Main entry point for the Telegram bot
- **commands/**: Handlers for different bot commands (`/brainstorm`, `/generate`, `/query`)
- **middleware/**: Bot middleware for authentication, logging, and error handling
- **utils/**: Utility functions for message formatting, file handling, etc.

#### AI Processor (`src/ai-processor/`)
- **main.py**: FastAPI application for AI document processing
- **models/**: AI model implementations (OpenAI, custom models)
- **processors/**: Document processing modules (PDF, DOCX, Markdown)
- **utils/**: AI-specific utilities and helper functions

#### Integrations (`src/integrations/`)
- **notion/**: Notion API client and integration logic
- **github/**: GitHub API client and repository management
- **telegram/**: Telegram API client for advanced features

#### API Server (`src/api/`)
- **server.js**: Express.js API server
- **routes/**: API endpoint definitions
- **middleware/**: API middleware (auth, validation, rate limiting)
- **controllers/**: Business logic for API endpoints

#### Database (`src/database/`)
- **models/**: Database models and schemas
- **migrations/**: Database schema migrations
- **seeds/**: Initial data seeding scripts

### n8n Workflows (`n8n/`)

#### Workflows (`n8n/workflows/`)
- **document-processing.json**: Main document processing workflow
- **telegram-commands.json**: Telegram command handling workflow
- **github-publishing.json**: GitHub publishing workflow
- **error-handling.json**: Error handling and notification workflow

#### Custom Nodes (`n8n/nodes/`)
- **DocumentProcessor**: Custom node for document processing
- **NotionWriter**: Custom node for Notion integration
- **GitHubPublisher**: Custom node for GitHub publishing

### Documentation (`docs/`)

#### Technical Documentation
- **architecture.md**: System architecture and design decisions
- **api.md**: Complete API documentation with examples
- **workflows.md**: n8n workflow configuration and usage
- **deployment.md**: Deployment guides for different environments

#### User Documentation
- **user-guide.md**: End-user guide for using the system
- **troubleshooting.md**: Common issues and solutions
- **faq.md**: Frequently asked questions

### Templates (`templates/`)

#### Document Templates
- **markdown/**: Markdown document templates
- **notion/**: Notion page templates
- **github/**: GitHub README and documentation templates

### Testing (`tests/`)

#### Test Organization
- **unit/**: Unit tests for individual components
- **integration/**: Integration tests for API endpoints
- **e2e/**: End-to-end tests for complete workflows
- **fixtures/**: Test data and mock files

### Scripts (`scripts/`)

#### Utility Scripts
- **setup.sh**: Initial project setup and configuration
- **deploy.sh**: Deployment automation script
- **backup.sh**: Database and file backup script
- **migrate.sh**: Database migration script

## Configuration Files

### Environment Configuration
- **.env.example**: Template for environment variables
- **config.js**: Application configuration management

### Code Quality
- **.eslintrc.js**: ESLint configuration for JavaScript
- **.prettierrc**: Prettier configuration for code formatting
- **tsconfig.json**: TypeScript configuration
- **jest.config.js**: Jest testing configuration

### Docker Configuration
- **Dockerfile**: Docker image configuration
- **docker-compose.yml**: Multi-service Docker setup
- **nginx.conf**: Nginx reverse proxy configuration

## Development Workflow

### Local Development
1. Clone repository and install dependencies
2. Set up environment variables
3. Start development services with Docker Compose
4. Run tests and linting
5. Develop features in appropriate directories

### Testing Strategy
- Unit tests for individual functions and classes
- Integration tests for API endpoints
- End-to-end tests for complete user workflows
- Performance tests for scalability

### Deployment Process
1. Run full test suite
2. Build Docker images
3. Deploy to staging environment
4. Run integration tests
5. Deploy to production
6. Monitor and verify deployment

This structure provides a clear separation of concerns, making the codebase maintainable and scalable for the Automation Documentation Agent project.
