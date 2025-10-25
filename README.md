# Automation Documentation Agent

An AI-driven system capable of autonomously generating, reviewing, and publishing structured documentation through an integrated workflow across Telegram, n8n, Notion, and GitHub.

## 🚀 Overview

The Automation Documentation Agent is a comprehensive solution that streamlines the entire documentation lifecycle from ideation to publication. Users interact through Telegram to brainstorm, query, or generate new document versions, while the system leverages uploaded PDFs and prior document data to provide accurate, contextually aligned outputs.

## ✨ Key Features

- **Multi-Modal Interaction**: Support for text, voice commands, and PDF uploads via Telegram
- **Intelligent Document Processing**: AI-powered analysis of PDFs, DOCX, and Markdown files
- **Automated Workflow**: Seamless integration between Telegram, n8n, Notion, and GitHub
- **Version Control**: Complete document lifecycle management with Git integration
- **Context-Aware Generation**: Leverages existing documentation for consistent outputs

## 🏗️ Architecture

The system consists of several integrated components:

- **Telegram Bot**: User interface for interaction and file uploads
- **n8n Workflows**: Automation engine for processing and routing
- **Notion Integration**: Document storage and collaboration
- **GitHub Integration**: Version control and publishing
- **AI Processing Engine**: Document analysis and generation

## 📋 Prerequisites

- Node.js 18+ 
- Python 3.9+
- Docker (optional)
- Telegram Bot Token
- Notion API Key
- GitHub Personal Access Token
- n8n instance

## 🛠️ Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/automation-documentation-agent.git
cd automation-documentation-agent
```

2. Install dependencies:
```bash
npm install
pip install -r requirements.txt
```

3. Configure environment variables:
```bash
cp .env.example .env
# Edit .env with your configuration
```

4. Start the services:
```bash
npm run start
```

## 📖 Documentation

- [Architecture Overview](docs/architecture.md)
- [API Documentation](docs/api.md)
- [Workflow Guide](docs/workflows.md)
- [Deployment Guide](docs/deployment.md)
- [Contributing Guidelines](CONTRIBUTING.md)

## 🔧 Configuration

### Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `TELEGRAM_BOT_TOKEN` | Telegram bot token | Yes |
| `NOTION_API_KEY` | Notion integration API key | Yes |
| `GITHUB_TOKEN` | GitHub personal access token | Yes |
| `N8N_WEBHOOK_URL` | n8n webhook endpoint | Yes |
| `OPENAI_API_KEY` | OpenAI API key for AI processing | Yes |

## 🚀 Quick Start

1. **Setup Telegram Bot**:
   - Create a bot via [@BotFather](https://t.me/botfather)
   - Configure commands: `/brainstorm`, `/generate`, `/query`

2. **Configure n8n**:
   - Import workflows from `n8n/workflows/`
   - Set up webhook endpoints

3. **Initialize Notion**:
   - Create a workspace
   - Set up database templates

4. **Test the System**:
   - Send `/start` to your Telegram bot
   - Upload a PDF document
   - Try generating documentation

## 📁 Project Structure

```
automation-documentation-agent/
├── src/
│   ├── telegram-bot/          # Telegram bot implementation
│   ├── ai-processor/          # AI document processing
│   ├── integrations/          # External service integrations
│   └── utils/                 # Utility functions
├── n8n/
│   ├── workflows/             # n8n workflow definitions
│   └── nodes/                 # Custom n8n nodes
├── docs/                      # Documentation files
├── templates/                 # Document templates
├── tests/                     # Test files
└── scripts/                   # Deployment and utility scripts
```

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🆘 Support

- 📧 Email: support@automation-docs.com
- 💬 Telegram: [@AutomationDocsBot](https://t.me/AutomationDocsBot)
- 🐛 Issues: [GitHub Issues](https://github.com/yourusername/automation-documentation-agent/issues)

## 🗺️ Roadmap

- [ ] Enhanced AI model integration
- [ ] Multi-language support
- [ ] Advanced document templates
- [ ] Real-time collaboration features
- [ ] Mobile app development

---

**Made with ❤️ by the Automation Documentation Team**
