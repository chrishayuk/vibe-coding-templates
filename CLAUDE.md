# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) and other AI agents when working with this template repository.

## Repository Overview

Template repository providing comprehensive documentation and templates for bootstrapping professional projects with modern tooling, CI/CD, and best practices.

## Available Templates

### Python Projects
Complete Python project templates with:
- Package management using `uv` (10-100x faster than pip)
- Testing with pytest and coverage
- CI/CD with GitHub Actions
- Pre-commit hooks for code quality
- Type checking with mypy
- Linting/formatting with ruff

📚 **Python Documentation:**
- [python/BOOTSTRAP.md](python/BOOTSTRAP.md) - Step-by-step project bootstrap guide
- [python/AI_AGENT_GUIDE.md](python/AI_AGENT_GUIDE.md) - Common scenarios and quick reference
- [python/docs/](python/docs/) - Comprehensive documentation for all components

## How to Use This Repository

### When a user asks to create a Python project:

1. Navigate to the Python templates: `python/`
2. Follow [python/BOOTSTRAP.md](python/BOOTSTRAP.md) systematically
3. Use templates from `python/templates/`
4. Reference documentation in `python/docs/`

### Key Python Resources:
- **Bootstrap Guide**: [python/BOOTSTRAP.md](python/BOOTSTRAP.md)
- **Package Management**: [python/docs/PACKAGE_MANAGEMENT.md](python/docs/PACKAGE_MANAGEMENT.md)
- **GitHub Actions**: [python/docs/cicd/GITHUB_ACTIONS.md](python/docs/cicd/GITHUB_ACTIONS.md)
- **Pre-commit Hooks**: [python/docs/cicd/PRE_COMMIT.md](python/docs/cicd/PRE_COMMIT.md)
- **Testing**: [python/docs/testing/TEST_COVERAGE.md](python/docs/testing/TEST_COVERAGE.md)

## Important Principles for AI Agents

1. **Use language-specific guides** - Each language has its own folder with tailored documentation
2. **Follow existing documentation** - Don't recreate what's already documented
3. **Use templates as starting points** - Customize based on user needs
4. **Verify everything works** - Always run verification commands before declaring success

## Common Commands for Python Projects

All Python projects use `uv` for package management:

```bash
# Install dependencies
uv sync --dev

# Run tests
uv run pytest

# Linting and formatting
uv run ruff check .
uv run ruff format .

# Type checking
uv run mypy src/
```

See [python/docs/PACKAGE_MANAGEMENT.md](python/docs/PACKAGE_MANAGEMENT.md) for complete command reference.

## Template Placeholders

Common placeholders to replace:
- `{project_name}` - Project directory name
- `{package_name}` - Language-specific package name
- `{description}` - Project description
- `{author}` - Author name
- `{email}` - Author email
- `{python_version}` or `{language_version}` - Target language version

## Future Templates

This repository structure supports adding templates for other languages:
- `javascript/` - Node.js/TypeScript projects (future)
- `rust/` - Rust projects (future)
- `go/` - Go projects (future)

Each language folder will contain its own:
- `BOOTSTRAP.md` - Language-specific bootstrap guide
- `docs/` - Language-specific documentation
- `templates/` - Language-specific templates