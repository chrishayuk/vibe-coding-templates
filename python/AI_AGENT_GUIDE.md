# AI Agent Guide for Python Projects

Quick reference for AI agents using vibe-coding-templates to bootstrap Python projects.

## Essential References

⚠️ **CRITICAL FOR AI AGENTS**: You MUST read these documents when performing related tasks!

- **Bootstrap Steps**: [BOOTSTRAP.md](BOOTSTRAP.md) - Follow systematically, step by step
- **Package Management**: [docs/PACKAGE_MANAGEMENT.md](docs/PACKAGE_MANAGEMENT.md) - **READ BEFORE** using any uv commands
- **GitHub Actions**: [docs/cicd/GITHUB_ACTIONS.md](docs/cicd/GITHUB_ACTIONS.md) - **READ BEFORE** creating workflows
- **Pre-commit Hooks**: [docs/cicd/PRE_COMMIT.md](docs/cicd/PRE_COMMIT.md) - **READ BEFORE** configuring hooks
- **Testing**: [docs/testing/TEST_COVERAGE.md](docs/testing/TEST_COVERAGE.md) - **READ BEFORE** setting up tests

## ⚠️ IMPORTANT: Required Components

**Every Python project MUST include:**
- ✅ GitHub Actions workflow (.github/workflows/test.yml)
- ✅ Pre-commit configuration (.pre-commit-config.yaml)
- ✅ Complete test suite in tests/
- ✅ Proper package structure in src/

## Common Scenarios

### Scenario 1: Create New Python Project

**User**: "Create a Python project called data-processor"

**Actions**:
1. Follow [BOOTSTRAP.md](BOOTSTRAP.md) ALL steps (1-9)
2. **READ [docs/PACKAGE_MANAGEMENT.md](docs/PACKAGE_MANAGEMENT.md)** when creating pyproject.toml and Makefile (Step 3)
3. **MUST create .github/workflows/test.yml** (Step 6) - read GitHub Actions docs first
4. **MUST create .pre-commit-config.yaml** (Step 7) - read Pre-commit docs first
5. Replace placeholders: `{project_name}` → `data-processor`, `{package_name}` → `data_processor`
6. Run ALL verification commands from Step 9 to ensure completeness

### Scenario 2: Add CI/CD to Existing Project

**User**: "Set up GitHub Actions"

**Actions**:
1. **FIRST**: Read [docs/cicd/GITHUB_ACTIONS.md](docs/cicd/GITHUB_ACTIONS.md) completely
2. **THEN**: Copy workflows from `templates/cicd/workflows/`
3. Customize for project needs based on the documentation
4. Set up required secrets in GitHub
5. Verify workflows run successfully

### Scenario 3: Configure Pre-commit Hooks

**User**: "Add pre-commit hooks"

**Actions**:
1. **FIRST**: Read [docs/cicd/PRE_COMMIT.md](docs/cicd/PRE_COMMIT.md) completely
2. **THEN**: Copy hook configs from `templates/cicd/hooks/`
3. Ensure git is initialized first: `git init`
4. Run `uv run pre-commit install`
5. Test with `pre-commit run --all-files` (may need to run twice)

### Scenario 4: Convert from pip to uv

**User**: "Convert my project to use uv"

**Actions**:
1. **FIRST**: Read entire [docs/PACKAGE_MANAGEMENT.md](docs/PACKAGE_MANAGEMENT.md)
2. **THEN**: Follow migration guide section specifically
3. Create `pyproject.toml` with `[tool.uv]` section using template from docs
4. Run `uv sync --dev` to install dependencies
5. Update all scripts and documentation to use `uv run` commands
6. Verify with commands from the Package Management doc

## Project Type Templates

### FastAPI Web Service
Add to dependencies:
```toml
dependencies = [
    "fastapi>=0.100.0",
    "uvicorn>=0.23.0",
    "pydantic>=2.0.0",
]
```

### Data Science Project
Add to dependencies:
```toml
dependencies = [
    "pandas>=2.0.0",
    "numpy>=1.24.0",
    "scikit-learn>=1.3.0",
    "jupyter>=1.0.0",
]
```

### CLI Application
Add to dependencies and scripts:
```toml
dependencies = [
    "click>=8.1.0",
    "rich>=13.0.0",
]

[project.scripts]
my-cli = "my_package.cli:main"
```

## Quick Command Reference

⚠️ **IMPORTANT**: Read [docs/PACKAGE_MANAGEMENT.md](docs/PACKAGE_MANAGEMENT.md) for complete command details!

All commands use `uv run` prefix:

```bash
# Development
uv sync --dev          # Install dependencies
uv run pytest          # Run tests
uv run ruff check .    # Lint code
uv run ruff format .   # Format code
uv run mypy src/       # Type check

# Pre-commit
pre-commit install     # Set up hooks
pre-commit run --all-files  # Run all hooks
```

## Troubleshooting

See troubleshooting sections in:
- [docs/PACKAGE_MANAGEMENT.md#troubleshooting](docs/PACKAGE_MANAGEMENT.md#troubleshooting) - Package issues
- [docs/cicd/GITHUB_ACTIONS.md#troubleshooting](docs/cicd/GITHUB_ACTIONS.md#troubleshooting) - CI/CD issues
- [docs/cicd/PRE_COMMIT.md](docs/cicd/PRE_COMMIT.md) - Hook issues

## Best Practices for AI Agents

1. **READ DOCUMENTATION FIRST** - Always read referenced docs before implementing
2. **Always use uv** - Never mix pip and uv (see Package Management doc)
3. **Follow existing docs** - Don't recreate what's already documented
4. **Test incrementally** - Verify each step works before proceeding
5. **Use templates** - Start from `templates/` directory
6. **Check existing patterns** - Look at how things are done in the codebase
7. **Fix linting first** - Run `uv run ruff check . --fix` before committing
8. **Initialize git early** - Required for pre-commit hooks to work
9. **Run pre-commit twice** - First run may fix issues, second run verifies
10. **Verify completeness** - Use Step 9 verification checklist from BOOTSTRAP.md

## Common Bootstrap Gotchas

- **Mypy types-all**: Don't use it, add specific type stubs instead
- **Markdown code blocks**: Always specify language (` ```python`, ` ```yaml`, ` ```text`)
- **Pre-commit stages**: Some hooks warn about deprecated stage names - this is OK
- **Initial linting**: New code often needs `ruff --fix` on first run
- **Git init timing**: Must init git before installing pre-commit hooks