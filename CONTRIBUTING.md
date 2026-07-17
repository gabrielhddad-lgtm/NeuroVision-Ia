# Contributing to NeuroVision AI

Thank you for your interest in contributing to NeuroVision AI! This document provides guidelines and instructions for contributing.

## Code of Conduct

This project and everyone participating in it is governed by our [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

## How Can I Contribute?

### Reporting Bugs

- **Check existing issues** before reporting a bug
- Use the bug report template
- Include:
  - Clear description
  - Steps to reproduce
  - Expected behavior
  - Actual behavior
  - Screenshots if applicable
  - Environment details (OS, Python version, etc.)

### Suggesting Enhancements

- Use the feature request template
- Clearly describe the enhancement
- Provide use cases and examples
- Explain why this enhancement would be useful

### Pull Requests

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/your-feature-name`
3. **Make your changes** following the coding standards
4. **Write tests** for your changes
5. **Update documentation** as needed
6. **Commit with clear messages**: `git commit -m "Add feature: description"`
7. **Push to your fork**: `git push origin feature/your-feature-name`
8. **Create a Pull Request** with a clear description

## Development Standards

### Python (Backend)

- **Python 3.13+**
- **Code Style**: PEP 8, enforced with `black`
- **Linting**: `pylint`, `flake8`
- **Type Hints**: Required for all functions
- **Docstrings**: Google-style docstrings required
- **Testing**: `pytest` with >80% coverage

```bash
# Format code
black app/ tests/

# Lint
pylint app/ tests/
flake8 app/ tests/

# Type checking
mypy app/ tests/

# Run tests
pytest tests/ --cov=app
```

### TypeScript/React (Frontend)

- **TypeScript 5.0+**
- **React 19+**
- **Code Style**: ESLint + Prettier
- **Component Structure**: Functional components with hooks
- **Type Safety**: Strict mode enabled
- **Testing**: Jest + React Testing Library

```bash
# Format code
prettier --write "src/**/*.{ts,tsx}"

# Lint
eslint src/

# Type checking
tsc --noEmit

# Run tests
npm test -- --coverage
```

### Commit Messages

Follow conventional commits:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types**: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `ci`

**Example**:
```
feat(auth): implement JWT refresh token rotation

Add automatic refresh token rotation for enhanced security.
Tokens are rotated on each refresh request.

Fixes #123
```

### Code Review Guidelines

**Reviewers should check**:
- Code follows project standards
- Tests are adequate (>80% coverage for new code)
- Documentation is updated
- Security best practices are followed
- No hardcoded secrets or sensitive data
- Performance implications are considered
- SOLID principles are respected

**Authors should**:
- Keep PRs focused and reasonably sized
- Respond to review feedback promptly
- Provide context in PR description
- Link related issues

## Branch Naming

- `feature/description` - New features
- `bugfix/description` - Bug fixes
- `docs/description` - Documentation updates
- `refactor/description` - Code refactoring
- `test/description` - Test improvements
- `chore/description` - Maintenance tasks

## Documentation

- Update README.md if adding user-facing features
- Add docstrings to all new classes and functions
- Update API documentation for endpoint changes
- Include architecture decision records (ADRs) for significant changes

## Testing Requirements

- **Unit Tests**: Test individual functions and classes
- **Integration Tests**: Test component interactions
- **API Tests**: Test endpoints with various inputs
- **Coverage**: Maintain >80% code coverage
- **Security Tests**: Test authentication, authorization, input validation

## Local Development

### Setup

```bash
# Clone your fork
git clone https://github.com/your-username/NeuroVision-Ia.git
cd NeuroVision-Ia

# Add upstream remote
git remote add upstream https://github.com/gabrielhddad-lgtm/NeuroVision-Ia.git

# Create feature branch
git checkout -b feature/your-feature

# Setup development environment
docker-compose up -d

# Backend
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
pip install -r requirements-dev.txt

# Frontend
cd ../frontend
npm install
```

### Pre-commit Hooks

```bash
# Install pre-commit
pip install pre-commit

# Install git hooks
pre-commit install
```

## Getting Help

- 📖 Check the [documentation](docs/)
- 💬 Ask in GitHub Discussions
- 🐛 Search existing issues
- 📧 Contact the maintainers

## Recognition

Contributors will be recognized in:
- README.md (Contributors section)
- Release notes
- GitHub contributors page

Thank you for contributing to NeuroVision AI! 🚀
