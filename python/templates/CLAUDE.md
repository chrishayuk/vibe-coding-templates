# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with this project.

## Project Overview

{{project_name}} - {{description}}

## Development Guidelines

1. **Package Management**: Use `uv` for all dependency management
2. **Testing**: Maintain minimum 80% code coverage
3. **Code Quality**: Run `make check` before committing
4. **Documentation**: Update docstrings and README when adding features

## Common Commands

```bash
# Install dependencies
uv sync --dev

# Run tests with coverage
uv run pytest --cov={{package_name}}

# Format and lint code
make format
make lint

# Run all checks
make check

# Build package
uv build

# Clean artifacts
make clean-all
```

## Project Standards

- Python {{python_version}}+
- Type hints for all functions
- Docstrings for all public APIs
- Tests for all new features
- Pre-commit hooks must pass

## Key Files

- `src/{{package_name}}/main.py` - Main implementation
- `tests/test_main.py` - Test suite
- `pyproject.toml` - Project configuration
- `Makefile` - Common tasks
- `.github/workflows/ci.yml` - CI/CD pipeline

## Testing Requirements

- Minimum 80% overall coverage
- Critical modules: 90-100% coverage
- All new code must include tests
- Run `make test-cov` to verify coverage

## Code Style

- Use `ruff` for linting
- Use `black` for formatting
- Use `mypy` for type checking
- Run `make format` before committing

## Git Workflow

1. Create feature branch from `main`
2. Make changes and add tests
3. Run `make check` to verify all checks pass
4. Commit with descriptive messages
5. Open PR with description of changes

## CI/CD

GitHub Actions runs on all pushes and PRs:
- Tests on Python {{python_version}} across multiple OS
- Linting and formatting checks
- Type checking
- Coverage reporting

## Common Tasks

### Adding a new dependency
```bash
uv add package-name           # Production dependency
uv add --dev package-name      # Development dependency
```

### Running specific tests
```bash
uv run pytest tests/test_specific.py
uv run pytest -k "test_function_name"
```

### Debugging
```bash
uv run python -m pdb src/{{package_name}}/main.py
uv run ipython  # Interactive Python shell
```

## Project-Specific Notes

{{project_specific_notes}}

---
Generated from vibe-coding-templates