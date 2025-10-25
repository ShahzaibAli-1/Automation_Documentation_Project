# Contributing Guidelines

Thank you for your interest in contributing to the Automation Documentation Agent! This document provides guidelines for contributing to the project.

## Code of Conduct

This project adheres to a code of conduct that we expect all contributors to follow. Please read and follow our [Code of Conduct](CODE_OF_CONDUCT.md).

## Getting Started

### Prerequisites

- Node.js 18+ and npm
- Python 3.9+ and pip
- Git
- Docker (optional but recommended)
- Access to required API keys for testing

### Development Setup

1. **Fork and Clone**
   ```bash
   git clone https://github.com/yourusername/automation-documentation-agent.git
   cd automation-documentation-agent
   ```

2. **Install Dependencies**
   ```bash
   # Install Node.js dependencies
   npm install
   
   # Install Python dependencies
   pip install -r requirements.txt
   
   # Install development dependencies
   npm install --save-dev
   pip install -r requirements-dev.txt
   ```

3. **Environment Configuration**
   ```bash
   cp .env.example .env
   # Edit .env with your development configuration
   ```

4. **Start Development Services**
   ```bash
   # Start database and Redis
   docker-compose up -d postgres redis
   
   # Start the application
   npm run dev
   ```

## Contribution Process

### 1. Issue Reporting

Before starting work on a new feature or bug fix:

1. Check existing issues to avoid duplicates
2. Create a new issue with:
   - Clear description of the problem/feature
   - Steps to reproduce (for bugs)
   - Expected behavior
   - Environment details

### 2. Branch Strategy

We use GitFlow branching strategy:

- `main`: Production-ready code
- `develop`: Integration branch for features
- `feature/*`: New features
- `bugfix/*`: Bug fixes
- `hotfix/*`: Critical production fixes

### 3. Pull Request Process

1. **Create Feature Branch**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/your-feature-name
   ```

2. **Make Changes**
   - Write clean, readable code
   - Follow existing code style
   - Add tests for new functionality
   - Update documentation as needed

3. **Commit Messages**
   Use conventional commit format:
   ```
   type(scope): description
   
   [optional body]
   
   [optional footer]
   ```
   
   Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

4. **Testing**
   ```bash
   # Run tests
   npm test
   python -m pytest
   
   # Run linting
   npm run lint
   python -m flake8
   
   # Type checking
   npm run type-check
   python -m mypy
   ```

5. **Submit Pull Request**
   - Push your branch: `git push origin feature/your-feature-name`
   - Create PR with:
     - Clear title and description
     - Reference related issues
     - Screenshots (if applicable)
     - Testing instructions

## Coding Standards

### JavaScript/TypeScript

- Use ESLint configuration provided
- Follow Prettier formatting
- Use TypeScript for new code
- Write JSDoc comments for public APIs

```javascript
/**
 * Processes a document using AI
 * @param {string} documentId - The document ID
 * @param {Object} options - Processing options
 * @returns {Promise<Object>} Processing result
 */
async function processDocument(documentId, options) {
  // Implementation
}
```

### Python

- Follow PEP 8 style guide
- Use type hints
- Write docstrings for functions and classes
- Use Black for code formatting

```python
def process_document(document_id: str, options: Dict[str, Any]) -> Dict[str, Any]:
    """
    Process a document using AI.
    
    Args:
        document_id: The document ID
        options: Processing options
        
    Returns:
        Processing result dictionary
        
    Raises:
        DocumentNotFoundError: If document doesn't exist
    """
    # Implementation
```

### Database

- Use migrations for schema changes
- Follow naming conventions:
  - Tables: `snake_case`
  - Columns: `snake_case`
  - Indexes: `idx_table_column`

## Testing Guidelines

### Unit Tests

- Write tests for all new functionality
- Aim for >80% code coverage
- Use descriptive test names
- Mock external dependencies

```javascript
describe('DocumentProcessor', () => {
  it('should extract text from PDF files', async () => {
    const processor = new DocumentProcessor();
    const result = await processor.extractText('test.pdf');
    expect(result).toContain('expected text');
  });
});
```

### Integration Tests

- Test API endpoints
- Test database interactions
- Test external service integrations

```python
def test_document_processing_api(client):
    """Test document processing API endpoint."""
    response = client.post('/api/documents/process', json={
        'file_id': 'test-file-id',
        'processing_type': 'extract'
    })
    assert response.status_code == 200
    assert 'document_id' in response.json
```

### End-to-End Tests

- Test complete workflows
- Use test data and mock services
- Test error scenarios

## Documentation

### Code Documentation

- Document all public APIs
- Include examples in docstrings
- Keep README files updated
- Document configuration options

### API Documentation

- Use OpenAPI/Swagger specifications
- Include request/response examples
- Document error codes and messages

### User Documentation

- Write clear user guides
- Include screenshots for UI changes
- Update changelog for releases

## Performance Considerations

### Code Performance

- Optimize database queries
- Use caching where appropriate
- Minimize external API calls
- Profile performance-critical code

### Resource Usage

- Monitor memory usage
- Optimize file processing
- Use streaming for large files
- Implement rate limiting

## Security Guidelines

### Code Security

- Validate all inputs
- Use parameterized queries
- Sanitize file uploads
- Implement proper authentication

### API Security

- Use HTTPS in production
- Implement rate limiting
- Validate API keys
- Log security events

## Release Process

### Version Numbering

We use Semantic Versioning (SemVer):
- `MAJOR`: Breaking changes
- `MINOR`: New features (backward compatible)
- `PATCH`: Bug fixes (backward compatible)

### Release Checklist

- [ ] All tests passing
- [ ] Documentation updated
- [ ] Changelog updated
- [ ] Version bumped
- [ ] Release notes prepared
- [ ] Security review completed

## Getting Help

### Communication Channels

- **GitHub Issues**: Bug reports and feature requests
- **Discussions**: General questions and ideas
- **Telegram**: Real-time chat (invite only)
- **Email**: security@automation-docs.com (security issues)

### Mentorship

New contributors can request mentorship by:
1. Adding `good first issue` label to issues
2. Asking questions in GitHub Discussions
3. Requesting code review from maintainers

## Recognition

Contributors will be recognized in:
- CONTRIBUTORS.md file
- Release notes
- Project documentation
- Annual contributor awards

## License

By contributing to this project, you agree that your contributions will be licensed under the same license as the project (MIT License).

---

Thank you for contributing to the Automation Documentation Agent! 🚀
