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

⚠️ **IMPORTANT: Read `docs/PACKAGE_MANAGEMENT.md` BEFORE implementing!**
The linked documentation contains critical details about uv commands, lock files, and best practices.

Use the template structure from `docs/PACKAGE_MANAGEMENT.md` with these essential sections:

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "{package_name}"
version = "0.1.0"
description = "{description}"
requires-python = ">={python_version}"
dependencies = []

[tool.uv]
dev-dependencies = [
    "pytest>=8.0.0",
    "pytest-cov>=6.0.0",
    "mypy>=1.0.0",
    "ruff>=0.8.0",
    "pre-commit>=3.5.0",
]
```

**REQUIRED ACTION**: Open and read the full configuration options in `docs/PACKAGE_MANAGEMENT.md` to understand:
- Complete pyproject.toml structure
- Lock file management
- Common uv commands
- Best practices and troubleshooting

### Makefile

**REQUIRED ACTION**: Read the "Makefile Integration" section in `docs/PACKAGE_MANAGEMENT.md` first!
This section contains the complete Makefile template with all necessary targets.

Create a Makefile using the template from `docs/PACKAGE_MANAGEMENT.md` (Section: Makefile Integration).

### .gitignore

Standard Python gitignore including:
- `__pycache__/`, `*.py[cod]`, `.pytest_cache/`
- `.venv/`, `venv/`, `.coverage`, `htmlcov/`
- `.idea/`, `.vscode/`, `.DS_Store`
- `uv.lock` (if desired for development)

## Step 4: Create Initial Source Files

### src/{package_name}/__init__.py
```python
"""{package_name} package."""
__version__ = "0.1.0"
```

### src/{package_name}/main.py
Create a minimal main module with at least one function to test.

## Step 5: Set Up Testing

**BEFORE PROCEEDING**: If testing setup is new to you, read `docs/testing/TEST_COVERAGE.md` for:
- Testing best practices
- Coverage configuration
- pytest fixtures and patterns

### tests/test_main.py
Create basic tests that import and test your package:
```python
from src.{package_name}.main import your_function

def test_your_function():
    assert your_function() is not None
```

### tests/conftest.py
**ACTION**: Read `docs/testing/TEST_COVERAGE.md` for pytest fixtures and testing patterns.
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

For additional workflows:
1. **FIRST**: Read `docs/cicd/GITHUB_ACTIONS.md` for detailed setup instructions
2. **THEN**: Copy from `templates/cicd/workflows/`:
   - `github-actions-coverage.yaml` → `.github/workflows/coverage.yml`
   - `github-actions-lint.yaml` → `.github/workflows/lint.yml`

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
        # Add project-specific type stubs as needed
        additional_dependencies: []  # Add deps like: [types-requests, click]
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
        stages: [push]
```

**Note about optional hooks:**
- **Detect-secrets**: If using, run `detect-secrets scan > .secrets.baseline` first
- **Markdown linting**: Add language specifiers to code blocks (e.g., ` ```python` not just ` ``` `)

For additional hook configurations:
1. **REQUIRED**: Read `docs/cicd/PRE_COMMIT.md` for hook configuration details
2. **THEN**: Check `templates/cicd/hooks/` for additional hook examples

The documentation explains hook stages, custom hooks, and troubleshooting.

## Step 8: Initialize and Install

```bash
# Initialize git FIRST (required for pre-commit)
git init

# Install uv (if not present)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install dependencies
uv sync --dev

# Install pre-commit hooks
uv run pre-commit install

# Fix any initial linting issues
uv run ruff check . --fix
uv run ruff format .

# Stage and verify with pre-commit
git add .
pre-commit run --all-files  # May need to run twice if files are fixed

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

# Code quality
uv run ruff check .
uv run ruff format --check .
uv run mypy src/

# Pre-commit
pre-commit run --all-files
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
  
- **GitHub Actions** (Step 6): [docs/cicd/GITHUB_ACTIONS.md](docs/cicd/GITHUB_ACTIONS.md)
  - WHEN: Setting up CI/CD workflows
  - WHY: Explains matrix testing, caching, workflow optimization
  
- **Pre-commit Hooks** (Step 7): [docs/cicd/PRE_COMMIT.md](docs/cicd/PRE_COMMIT.md)
  - WHEN: Configuring pre-commit hooks
  - WHY: Details hook stages, custom hooks, troubleshooting
  
- **Testing** (Step 5): [docs/testing/TEST_COVERAGE.md](docs/testing/TEST_COVERAGE.md)
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
pre-commit run --all-files  # MUST pass

# 6. Check git
git status  # MUST show initialized repository
```

## Common Issues & Fixes

### Linting errors on first run
```bash
# Fix automatically with ruff
uv run ruff check . --fix
uv run ruff format .
```

### Pre-commit hook failures
```bash
# Let pre-commit fix what it can
git add -A
pre-commit run --all-files
# Then commit the fixes
```

### Mypy additional_dependencies error
- Don't use `types-all` (it's deprecated)
- Add specific type stubs for your dependencies
- Example: `additional_dependencies: [types-requests, click]`

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