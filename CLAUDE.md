# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) and other AI agents when working with this template repository.

## Repository Overview

Professional project template repository designed for AI-assisted development, providing comprehensive documentation and templates for bootstrapping modern software projects with best practices, automated tooling, and production-ready configurations.

## Available Templates

### Python Projects
Complete Python project templates with:
- **Package Management**: `uv` (10-100x faster than pip, Rust-based)
- **Testing**: pytest with coverage reporting and fixtures
- **Code Quality**: ruff (linting/formatting), black (formatting), mypy (type checking)
- **CI/CD**: GitHub Actions with matrix testing
- **Pre-commit**: Automated hooks for code quality
- **Documentation**: Comprehensive guides and best practices

📚 **Python Documentation:**
- [python/BOOTSTRAP.md](python/BOOTSTRAP.md) - Step-by-step project bootstrap guide
- [python/AI_AGENT_GUIDE.md](python/AI_AGENT_GUIDE.md) - Common scenarios and quick reference
- [python/docs/](python/docs/) - Comprehensive documentation for all components
- [llms.txt](llms.txt) - AI agent overview and quick start

## How to Use This Repository

### When a user asks to create a Python project:

1. Navigate to the Python templates: `python/`
2. Follow [python/BOOTSTRAP.md](python/BOOTSTRAP.md) systematically
3. **CRITICAL**: Read the referenced documentation when performing each step:
   - **Step 3 (pyproject.toml, Makefile)**: READ [python/docs/PACKAGE_MANAGEMENT.md](python/docs/PACKAGE_MANAGEMENT.md)
   - **Step 5 (Testing)**: READ [python/docs/testing/TEST_COVERAGE.md](python/docs/testing/TEST_COVERAGE.md)
   - **Step 6 (GitHub Actions)**: READ [python/docs/cicd/GITHUB_ACTIONS.md](python/docs/cicd/GITHUB_ACTIONS.md)
   - **Step 7 (Pre-commit)**: READ [python/docs/cicd/PRE_COMMIT.md](python/docs/cicd/PRE_COMMIT.md)
4. Use templates from `python/templates/`
5. Always run Step 9 verification commands to ensure completeness

### Key Python Resources:
- **Bootstrap Guide**: [python/BOOTSTRAP.md](python/BOOTSTRAP.md)
- **Package Management**: [python/docs/PACKAGE_MANAGEMENT.md](python/docs/PACKAGE_MANAGEMENT.md)
- **GitHub Actions**: [python/docs/cicd/GITHUB_ACTIONS.md](python/docs/cicd/GITHUB_ACTIONS.md)
- **Pre-commit Hooks**: [python/docs/cicd/PRE_COMMIT.md](python/docs/cicd/PRE_COMMIT.md)
- **Testing**: [python/docs/testing/TEST_COVERAGE.md](python/docs/testing/TEST_COVERAGE.md)
- **llms.txt Guide**: [python/docs/LLMS_TXT.md](python/docs/LLMS_TXT.md)

### Key Python Templates:
- **Makefile**: [python/templates/Makefile](python/templates/Makefile)
- **llms.txt**: [python/templates/llms.txt](python/templates/llms.txt)
- **CLAUDE.md**: [python/templates/CLAUDE.md](python/templates/CLAUDE.md)

## Important Principles for AI Agents

1. **READ DOCUMENTATION FIRST** - When BOOTSTRAP.md references a doc (hyperlink or path), READ IT before implementing that step
2. **Use language-specific guides** - Each language has its own folder with tailored documentation
3. **Follow existing documentation** - Don't recreate what's already documented, use the templates provided
4. **Use templates as starting points** - Customize based on user needs
5. **Verify everything works** - Always run verification commands (Step 9 in BOOTSTRAP.md) before declaring success
6. **Package Management is Critical** - ALWAYS read [python/docs/PACKAGE_MANAGEMENT.md](python/docs/PACKAGE_MANAGEMENT.md) to understand uv commands, lock files, and best practices
7. **Test Coverage Matters** - Aim for minimum 80% coverage, critical modules should have 90-100%
8. **Use Modern Tools** - Prefer uv over pip, ruff over pylint/flake8, use type hints everywhere
9. **CI/CD is Essential** - Always set up GitHub Actions for automated testing and quality checks

## Common Commands for Python Projects

⚠️ **IMPORTANT**: These are quick reference commands. For complete details, AI agents MUST read [python/docs/PACKAGE_MANAGEMENT.md](python/docs/PACKAGE_MANAGEMENT.md).

All Python projects use `uv` for package management:

```bash
# Install uv (if not installed)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Initialize project
uv init

# Install dependencies
uv sync --dev

# Add a new dependency
uv add package-name
uv add --dev package-name  # for dev dependencies

# Run tests with coverage
uv run pytest --cov=src --cov-report=term-missing

# Linting and formatting
uv run ruff check . --fix
uv run ruff format .
uv run black .

# Type checking
uv run mypy src/

# Pre-commit hooks
uv run pre-commit install
uv run pre-commit run --all-files

# Build package
uv build

# Update lock file
uv lock
```

## Makefile Integration

Python projects include a comprehensive Makefile for common development tasks, configured to use `uv` commands.

📚 **See the complete Makefile template: [python/templates/Makefile](python/templates/Makefile)**  
📚 **Documentation: [python/docs/PACKAGE_MANAGEMENT.md#makefile-integration](python/docs/PACKAGE_MANAGEMENT.md)**

Common make targets:
- `make help` - Show all available targets
- `make install` - Install project dependencies
- `make dev-install` - Install with dev dependencies  
- `make test` - Run tests
- `make test-cov` - Run tests with coverage
- `make lint` - Run linting checks
- `make format` - Format code
- `make qa` - Run all quality checks
- `make clean` - Clean build artifacts

**AI AGENTS**: You MUST read [python/docs/PACKAGE_MANAGEMENT.md](python/docs/PACKAGE_MANAGEMENT.md) for:
- Complete command reference
- Makefile integration details
- Lock file management (`uv.lock`)
- Troubleshooting common issues
- Best practices and DO's/DON'Ts
- Migration from pip
- Publishing to PyPI

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