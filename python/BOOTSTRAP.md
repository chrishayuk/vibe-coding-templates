# Python Project Bootstrap Guide for AI Agents

This guide helps AI agents bootstrap new Python projects using the vibe-coding-templates.

## ⚠️ REQUIRED Components

**Every Python project MUST include:**
1. ✅ Project structure with src/ and tests/
2. ✅ Package management with uv
3. ✅ Testing with pytest
4. ✅ **GitHub Actions workflows** (.github/workflows/test.yml)
5. ✅ **Pre-commit hooks** (.pre-commit-config.yaml)
6. ✅ Git repository initialization

## Quick Start Checklist

```markdown
☐ 1. Create project structure (src/, tests/, docs/, .github/workflows/)
☐ 2. Create pyproject.toml with uv configuration
☐ 3. Create source and test files
☐ 4. Create Makefile and .gitignore
☐ 5. CREATE GitHub Actions workflow (.github/workflows/test.yml) - REQUIRED
☐ 6. CREATE pre-commit configuration (.pre-commit-config.yaml) - REQUIRED
☐ 7. Initialize git and install dependencies
☐ 8. Run ALL verification tests
```

## Step 1: Gather Project Information

Required information:
- **Project name**: Directory name (e.g., "my-awesome-project")
- **Package name**: Python package name (e.g., "my_awesome_project")
- **Description**: Brief project description
- **Python version**: Target Python version (default: 3.11)

## Step 2: Create Project Structure

```bash
mkdir -p {project_name}
cd {project_name}
mkdir -p src/{package_name} tests docs scripts .github/workflows
```

## Step 3: Create Core Configuration Files

### pyproject.toml

⚠️ **IMPORTANT: Read `docs/PACKAGE_MANAGEMENT.md` AND `docs/testing/CODE_QUALITY.md` BEFORE implementing!**
These documents contain critical details about:
- Package management with uv
- Code quality tools configuration (Black, isort, Ruff, MyPy)
- Best practices for tool integration

**Dev Dependencies Installation Guidance**:
- ⚠️ **ALWAYS use the latest versions** - Don't use the example versions below
- Run `uv add --dev <package>` to get the latest version automatically
- Or check PyPI for current versions before specifying

Use the template structure from `docs/PACKAGE_MANAGEMENT.md` with these essential sections:

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "{package_name}"
version = "0.1.0"
description = "{description}"
readme = "README.md"
requires-python = ">={python_version}"
dependencies = []

[tool.uv]
# ⚠️ IMPORTANT: These are example versions - use latest versions!
# Run: uv add --dev pytest pytest-cov mypy ruff black isort pre-commit
# This will automatically get the latest versions
dev-dependencies = [
    "pytest>=8.0.0",      # Testing framework
    "pytest-cov>=6.0.0",  # Coverage plugin for pytest
    "mypy>=1.0.0",        # Static type checker
    "ruff>=0.8.0",        # Fast linter and formatter
    "black>=24.0.0",      # Code formatter
    "isort>=5.13.0",      # Import sorter
    "pre-commit>=3.5.0",  # Git hook manager
]
```

**REQUIRED ACTIONS**: 
1. Read `docs/PACKAGE_MANAGEMENT.md` for package management details
2. Read `docs/testing/CODE_QUALITY.md` for tool configuration (Black, isort, Ruff)
3. Use `uv add --dev` to get latest versions instead of hardcoding versions

### Makefile

Create a Makefile with these essential targets:

```makefile
.PHONY: help install dev-install test test-cov lint format typecheck clean

# Default target
help:
	@echo "Available targets:"
	@echo "  install      Install production dependencies"
	@echo "  dev-install  Install all dependencies including dev"
	@echo "  test         Run tests"
	@echo "  test-cov     Run tests with coverage"
	@echo "  lint         Run linting checks"
	@echo "  format       Format code"
	@echo "  typecheck    Run type checking"
	@echo "  clean        Clean up generated files"

# Install dependencies
install:
	uv sync

dev-install:
	uv sync --dev

# Testing
test:
	uv run pytest

test-cov:
	uv run pytest --cov=src/{package_name} --cov-report=term-missing

# Code quality
lint:
	uv run ruff check .

format:
	# Run isort first (with black profile), then black
	# See docs/testing/CODE_QUALITY.md for why order matters
	uv run isort . --profile black
	uv run black .
	uv run ruff format .

typecheck:
	uv run mypy src/

# Cleanup
clean:
	rm -rf .pytest_cache .coverage htmlcov .mypy_cache
	find . -type d -name "__pycache__" -exec rm -rf {} +
	find . -type f -name "*.pyc" -delete
```

**Note**: Replace `{package_name}` with your actual package name. See `docs/PACKAGE_MANAGEMENT.md` for additional Makefile patterns and advanced usage.

### .gitignore

Standard Python gitignore including:
- `__pycache__/`, `*.py[cod]`, `.pytest_cache/`
- `.venv/`, `venv/`, `.coverage`, `htmlcov/`
- `.idea/`, `.vscode/`, `.DS_Store`
- `uv.lock` (if desired for development)

## Step 4: Create README and Initial Source Files

### README.md
```markdown
# {project_name}

{description}

## Installation

```bash
uv sync --dev
```

## Usage

```bash
uv run {package_name}
```

## Development

```bash
make test         # Run tests
make lint         # Run linting
make format       # Format code
make all          # Run all checks
```
```

### src/{package_name}/__init__.py
```python
"""{package_name} package."""
__version__ = "0.1.0"
```

### src/{package_name}/main.py
Create a minimal main module with at least one function to test.

## Step 5: Set Up Testing

**BEFORE PROCEEDING**: Read BOTH documents:
1. `docs/testing/TEST_COVERAGE.md` - Testing setup and coverage targets
2. `docs/testing/CODE_QUALITY.md` - Code quality tools configuration

These documents cover:
- Testing best practices and coverage configuration
- Black and isort setup for consistent formatting
- Ruff configuration for linting
- MyPy setup for type checking

### tests/test_main.py
Create basic tests that import and test your package:
```python
from src.{package_name}.main import your_function

def test_your_function():
    assert your_function() is not None
```

### tests/conftest.py
**ACTION**: Read `docs/testing/TEST_COVERAGE.md` and `docs/testing/UNIT_TESTING.md` for pytest fixtures and testing patterns.
Add pytest fixtures as needed based on the documentation.

## Step 6: Configure GitHub Actions (REQUIRED)

**⚠️ IMPORTANT: Always create GitHub Actions workflows for CI/CD**

### Create `.github/workflows/test.yml`:

```yaml
name: Test Suite

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.9', '3.10', '3.11', '3.12']
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Install uv
      uses: astral-sh/setup-uv@v3
      
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v5
      with:
        python-version: ${{ matrix.python-version }}
        
    - name: Install dependencies
      run: uv sync --dev
        
    - name: Run tests
      run: uv run pytest tests/ --cov=src/{package_name}
```

**Note**: Replace `{package_name}` with your actual package name.

For additional workflows (optional):
1. Read `docs/cicd/GITHUB_ACTIONS.md` for detailed setup instructions
2. Copy workflow templates from `templates/cicd/workflows/` as needed:
   - `github-actions-coverage.yaml` for coverage reporting
   - `github-actions-lint.yaml` for additional linting
   - `github-actions-test.yaml` for comprehensive testing

⚠️ The documentation contains important details about matrix testing, caching, and workflow optimization.

## Step 7: Configure Pre-commit Hooks (REQUIRED)

**⚠️ IMPORTANT: Always set up pre-commit hooks for code quality**

### Create `.pre-commit-config.yaml`:

```yaml
repos:
  # Ruff - Fast Python linter and formatter
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.8.0
    hooks:
      - id: ruff
        args: [--fix, --exit-non-zero-on-fix]
      - id: ruff-format

  # Mypy - Type checking
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.11.2
    hooks:
      - id: mypy
        # Add project-specific type stubs as needed, NOT types-all
        additional_dependencies: []  # Add specific stubs like: [types-requests]
        args: [--ignore-missing-imports]
        files: ^src/  # Only check src directory

  # Standard hooks
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
      - id: check-merge-conflict
      - id: check-toml

  # Local pytest hook (runs on push)
  - repo: local
    hooks:
      - id: pytest
        name: Run pytest
        entry: uv run pytest
        language: system
        pass_filenames: false
        stages: [pre-push]
```

**Note about optional hooks:**
- **Detect-secrets**: If using, run `detect-secrets scan > .secrets.baseline` first
- **Markdown linting**: Add language specifiers to code blocks (e.g., ` ```python` not just ` ``` `)

For additional hook configurations (optional):
1. Read `docs/cicd/PRE_COMMIT.md` for hook configuration details
2. Check `templates/cicd/hooks/` for additional hook examples:
   - `pre-commit-mypy-hook.yaml` for type checking configuration
   - `pre-commit-coverage-hook.yaml` for coverage thresholds
   - `pre-commit-secrets-hook.yaml` for secret detection

The documentation explains hook stages, custom hooks, and troubleshooting.

## Step 8: Initialize and Install

```bash
# Navigate to project directory
cd {project_name}

# Initialize git FIRST (required for pre-commit)
git init

# Check if uv is installed, install if not present
command -v uv >/dev/null 2>&1 || curl -LsSf https://astral.sh/uv/install.sh | sh

# Install dependencies
uv sync --dev

# Install pre-commit hooks
uv run pre-commit install

# Fix any initial linting and formatting issues
# Order matters: isort -> black -> ruff (see docs/testing/CODE_QUALITY.md)
uv run isort . --profile black
uv run black .
uv run ruff check . --fix
uv run ruff format .

# Stage and verify with pre-commit
git add .
uv run pre-commit run --all-files  # May need to run twice if files are fixed

# If pre-commit made changes, stage them again
git add .

# Initial commit
git commit -m "Initial project structure"
```

## Step 9: Verification

**IMPORTANT**: These commands are from `docs/PACKAGE_MANAGEMENT.md` - read that document if any command fails!

Run these commands to verify setup:

```bash
# Package management
uv --version
uv pip list

# Testing
uv run pytest

# Code quality (see docs/testing/CODE_QUALITY.md for details)
uv run isort . --check-only --profile black
uv run black . --check
uv run ruff check .
uv run ruff format --check .
uv run mypy src/

# Pre-commit
uv run pre-commit run --all-files
```

## Template Variables Reference

| Placeholder | Description | Example |
|------------|-------------|---------|
| `{project_name}` | Project directory | `my-project` |
| `{package_name}` | Python package | `my_package` |
| `{description}` | Project description | `Data processing tool` |
| `{python_version}` | Python version | `3.11` |

## ⚠️ CRITICAL: Read Referenced Documentation

**AI AGENTS MUST READ these documents when performing the related steps:**

- **Package Management** (Step 3, 8, 9): [docs/PACKAGE_MANAGEMENT.md](docs/PACKAGE_MANAGEMENT.md)
  - WHEN: Creating pyproject.toml, Makefile, running uv commands
  - WHY: Contains complete templates, command reference, troubleshooting
  - ⚠️ CRITICAL: Use `uv add --dev` to get latest package versions
  
- **Code Quality** (Step 3, 5, 8, 9): [docs/testing/CODE_QUALITY.md](docs/testing/CODE_QUALITY.md)
  - WHEN: Setting up Black, isort, Ruff, MyPy
  - WHY: Tool configuration, integration, and order of operations
  - ⚠️ CRITICAL: Read sections on Black+isort integration to avoid conflicts
  
- **GitHub Actions** (Step 6): [docs/cicd/GITHUB_ACTIONS.md](docs/cicd/GITHUB_ACTIONS.md)
  - WHEN: Setting up CI/CD workflows
  - WHY: Explains matrix testing, caching, workflow optimization
  
- **Pre-commit Hooks** (Step 7): [docs/cicd/PRE_COMMIT.md](docs/cicd/PRE_COMMIT.md)
  - WHEN: Configuring pre-commit hooks
  - WHY: Details hook stages, custom hooks, troubleshooting
  
- **Testing** (Step 5): 
  - [docs/testing/TEST_COVERAGE.md](docs/testing/TEST_COVERAGE.md) - Coverage setup and targets
  - [docs/testing/UNIT_TESTING.md](docs/testing/UNIT_TESTING.md) - Testing patterns and fixtures
  - WHEN: Setting up tests and coverage
  - WHY: Best practices, fixtures, coverage configuration

**DO NOT skip reading these documents - they contain critical implementation details!**

## ⚠️ CRITICAL: Verification Checklist

**DO NOT consider the project complete until ALL of these pass:**

```bash
# 1. Check project structure
ls -la .github/workflows/  # MUST contain test.yml
ls -la .pre-commit-config.yaml  # MUST exist
ls -la src/ tests/ docs/  # MUST exist

# 2. Verify dependencies install
uv sync --dev  # MUST complete without errors

# 3. Run tests
uv run pytest  # MUST pass all tests

# 4. Check code quality
uv run ruff check .  # MUST pass
uv run ruff format --check .  # MUST pass
uv run mypy src/  # SHOULD pass (may need configuration)

# 5. Verify pre-commit hooks
uv run pre-commit run --all-files  # MUST pass

# 6. Check git
git status  # MUST show initialized repository
```

## Troubleshooting Guide

### Bootstrap Order Issues

The correct order is critical for successful bootstrap:

1. Create project directory structure
2. Create ALL files (including README.md) BEFORE running uv sync
3. Initialize git repository
4. Install dependencies with uv sync --dev
5. Install pre-commit hooks
6. Run pre-commit to fix formatting
7. Commit changes

### Common Bootstrap Failures and Solutions

#### Missing README.md Error
**Problem**: `uv sync` fails with "README.md not found" when readme is referenced in pyproject.toml

**Solution**: Create README.md before running uv sync:
```bash
cat > README.md << 'EOF'
# Project Name
Project description
EOF
```

#### Pre-commit Hook Installation Failures
**Problem**: Pre-commit hooks fail to install or run

**Solutions**:
1. Ensure git is initialized BEFORE installing pre-commit
2. Run `uv sync --dev` to install all dev dependencies first
3. If mypy hook fails, remove `types-all` from additional_dependencies
4. Update deprecated stage names: use `pre-push` instead of `push`

#### Dev Dependencies Not Installing
**Problem**: Tools like ruff, mypy, pytest not available after uv sync

**Solution**: Ensure dev dependencies are properly specified:

**Option 1 - Get latest versions automatically (RECOMMENDED):**
```bash
# Add all dev dependencies with latest versions
uv add --dev pytest pytest-cov mypy ruff black isort pre-commit
```

**Option 2 - Specify in pyproject.toml:**
```toml
[tool.uv]
dev-dependencies = [
    "pytest>=8.0.0",
    "pytest-cov>=6.0.0",
    "mypy>=1.0.0",
    "ruff>=0.8.0",
    "black>=24.0.0",
    "isort>=5.13.0",
    "pre-commit>=3.5.0",
]
```

⚠️ **IMPORTANT**: Always prefer using `uv add --dev` to get the latest versions

#### Directory Navigation Issues
**Problem**: Commands fail with "no such file or directory"

**Solution**: Always cd into project directory after creation:
```bash
mkdir my-project
cd my-project  # Don't forget this!
```

## Common Issues & Fixes

### README.md not found during bootstrap
```bash
# Create README.md before running uv sync
cat > README.md << 'EOF'
# Project Name
Project description
EOF
```

### Linting and formatting errors on first run
```bash
# Fix automatically - order matters!
# 1. Organize imports with isort (Black profile)
uv run isort . --profile black

# 2. Format with Black
uv run black .

# 3. Fix linting issues with Ruff
uv run ruff check . --fix
uv run ruff format .
```

**Note**: See [docs/testing/CODE_QUALITY.md](docs/testing/CODE_QUALITY.md) for why tool order matters

### Pre-commit hook failures
```bash
# Let pre-commit fix what it can
git add -A
uv run pre-commit run --all-files
# Then commit the fixes
```

### Code Quality Tool Issues

#### Black and isort Conflicts
**Problem**: Black and isort format imports differently

**Solution**: 
- **ALWAYS use isort with Black profile**: `isort . --profile black`
- Run tools in order: isort → black → ruff
- See [docs/testing/CODE_QUALITY.md](docs/testing/CODE_QUALITY.md) for complete integration guide

#### Mypy Type Stub Issues
**Problem**: Mypy pre-commit hook fails with "types-all" package error

**Solution**: 
- **NEVER use `types-all`** - it's deprecated and causes installation failures
- Only add specific type stubs (packages starting with 'types-')
- Example: `additional_dependencies: [types-requests]`
- See [docs/testing/CODE_QUALITY.md](docs/testing/CODE_QUALITY.md) for full mypy configuration guidance

### Markdown linting errors
- Use language specifiers in code blocks: ` ```python` not ` ``` `
- Or use ` ```text` for non-code content

## Success Criteria

✅ **Project is ONLY complete when:**
- All directories exist (including .github/workflows/)
- GitHub Actions workflow file exists (.github/workflows/test.yml)
- Pre-commit config exists (.pre-commit-config.yaml)
- All dependencies install successfully
- All tests pass
- All linting passes (after running `ruff --fix`)
- Pre-commit hooks are installed and working
- Git repository is initialized

**Missing any of these means the bootstrap is INCOMPLETE!**