# AI Agent Guide for Python Projects

Quick reference for AI agents using vibe-coding-templates to bootstrap Python projects.

## Essential References

- **Bootstrap Steps**: [BOOTSTRAP.md](BOOTSTRAP.md)
- **Package Management**: [docs/PACKAGE_MANAGEMENT.md](docs/PACKAGE_MANAGEMENT.md)
- **GitHub Actions**: [docs/cicd/GITHUB_ACTIONS.md](docs/cicd/GITHUB_ACTIONS.md)
- **Pre-commit Hooks**: [docs/cicd/PRE_COMMIT.md](docs/cicd/PRE_COMMIT.md)
- **Testing**: [docs/testing/TEST_COVERAGE.md](docs/testing/TEST_COVERAGE.md)

## Common Scenarios

### Scenario 1: Create New Python Project

**User**: "Create a Python project called data-processor"

**Actions**:
1. Follow [BOOTSTRAP.md](BOOTSTRAP.md) steps 1-9
2. Use templates from `templates/` directory
3. Replace placeholders: `{project_name}` → `data-processor`, `{package_name}` → `data_processor`
4. Run verification commands from [docs/PACKAGE_MANAGEMENT.md](docs/PACKAGE_MANAGEMENT.md)

### Scenario 2: Add CI/CD to Existing Project

**User**: "Set up GitHub Actions"

**Actions**:
1. Read [docs/cicd/GITHUB_ACTIONS.md](docs/cicd/GITHUB_ACTIONS.md)
2. Copy workflows from `templates/cicd/workflows/`
3. Customize for project needs
4. Set up required secrets in GitHub

### Scenario 3: Configure Pre-commit Hooks

**User**: "Add pre-commit hooks"

**Actions**:
1. Read [docs/cicd/PRE_COMMIT.md](docs/cicd/PRE_COMMIT.md)
2. Copy hook configs from `templates/cicd/hooks/`
3. Run `uv run pre-commit install`
4. Test with `pre-commit run --all-files`

### Scenario 4: Convert from pip to uv

**User**: "Convert my project to use uv"

**Actions**:
1. Follow migration guide in [docs/PACKAGE_MANAGEMENT.md](docs/PACKAGE_MANAGEMENT.md#migration-from-pip)
2. Create `pyproject.toml` with `[tool.uv]` section
3. Run `uv sync --dev`
4. Update documentation to use `uv run` commands

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

All commands use `uv run` prefix (see [docs/PACKAGE_MANAGEMENT.md](docs/PACKAGE_MANAGEMENT.md)):

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

## Best Practices

1. **Always use uv** - Never mix pip and uv
2. **Follow existing docs** - Don't recreate what's already documented
3. **Test incrementally** - Verify each step works
4. **Use templates** - Start from `templates/` directory
5. **Check existing patterns** - Look at how things are done in the codebase