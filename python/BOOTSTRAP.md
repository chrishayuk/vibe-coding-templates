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

📚 **Use the comprehensive Makefile template: [templates/Makefile](templates/Makefile)**

Copy the Makefile template to your project and replace `{{package_name}}` with your actual package name.

```bash
# Copy the Makefile template
cp ../templates/Makefile .

# Replace placeholder with your package name (example for macOS/Linux)
sed -i '' 's/{{package_name}}/your_package_name/g' Makefile  # macOS
# OR
sed -i 's/{{package_name}}/your_package_name/g' Makefile     # Linux
```

The Makefile includes all common development tasks:
- **Installation**: `make install`, `make dev-install`
- **Testing**: `make test`, `make test-cov`, `make test-watch`
- **Code Quality**: `make lint`, `make format`, `make typecheck`
- **Cleaning**: `make clean`, `make clean-all`
- **CI/CD**: `make qa`, `make ready`, `make ci-test`
- **And many more** - run `make help` to see all available targets

📚 **See also**: [docs/PACKAGE_MANAGEMENT.md#makefile-integration](docs/PACKAGE_MANAGEMENT.md) for detailed documentation.

### .gitignore

Standard Python gitignore including:
- `__pycache__/`, `*.py[cod]`, `.pytest_cache/`
- `.venv/`, `venv/`, `.coverage`, `htmlcov/`
- `.idea/`, `.vscode/`, `.DS_Store`
- `.ruff_cache/`, `.mypy_cache/`
- `uv.lock` (if desired for development)

## Step 4: Create Documentation and Initial Source Files

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
make check        # Run all checks
```
```

### llms.txt (AI Agent Documentation)

📚 **Use the llms.txt template: [templates/llms.txt](templates/llms.txt)**  
📚 **Documentation: [docs/LLMS_TXT.md](docs/LLMS_TXT.md)**

Create an `llms.txt` file to help AI agents understand your project:

```bash
# Copy the template
cp ../templates/llms.txt .

# Edit and replace placeholders with your project details
# See docs/LLMS_TXT.md for detailed guidance on each section
```

The llms.txt file provides AI agents with:
- Project overview and key features
- Quick start code examples
- Project structure visualization
- Common development commands
- Key API references

**Note**: Read [docs/LLMS_TXT.md](docs/LLMS_TXT.md) for best practices and examples.

### CLAUDE.md (Claude Code Instructions)

📚 **Use the CLAUDE.md template: [templates/CLAUDE.md](templates/CLAUDE.md)**

Create a `CLAUDE.md` file with project-specific instructions for Claude Code:

```bash
# Copy the template
cp ../templates/CLAUDE.md .

# Replace placeholders with your project details
sed -i '' 's/{{project_name}}/your_project_name/g' CLAUDE.md  # macOS
sed -i '' 's/{{package_name}}/your_package_name/g' CLAUDE.md
# Add any project-specific notes
```

The CLAUDE.md file helps Claude Code understand:
- Project standards and guidelines
- Common development commands
- Testing requirements
- Code style preferences
- Git workflow

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

### Create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

permissions:
  contents: read

jobs:
  ci:
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
        python-version: ["3.10", "3.11", "3.12"]

    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v5
      with:
        python-version: ${{ matrix.python-version }}
    
    - name: Install uv
      uses: astral-sh/setup-uv@v3
      with:
        enable-cache: true
        cache-dependency-glob: "pyproject.toml"
    
    - name: Install dependencies
      run: |
        uv sync --dev
    
    - name: Run linting checks
      run: |
        uv run ruff check src/ tests/
        uv run black --check src/ tests/
    
    - name: Run type checking
      run: |
        uv run mypy src/
    
    - name: Run tests with coverage
      run: |
        uv run pytest tests/ -v --cov=src/{package_name} --cov-report=xml --cov-report=term-missing
    
    - name: Upload coverage to Codecov
      if: matrix.os == 'ubuntu-latest' && matrix.python-version == '3.11'
      uses: codecov/codecov-action@v4
      with:
        file: ./coverage.xml
        flags: unittests
        name: codecov-umbrella
        fail_ci_if_error: false
```

**Note**: Replace `{package_name}` with your actual package name.

### Optional: Package Publishing Workflow

For packages that will be published to PyPI, create `.github/workflows/publish.yml`:

```yaml
name: Publish to PyPI

on:
  release:
    types: [published]
  workflow_dispatch:
    inputs:
      test_pypi:
        description: 'Publish to TestPyPI instead of PyPI'
        required: false
        type: boolean
        default: true

permissions:
  contents: read
  id-token: write  # Required for trusted publishing

jobs:
  build:
    name: Build distribution packages
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.11'
    
    - name: Install uv
      uses: astral-sh/setup-uv@v3
    
    - name: Build package
      run: |
        uv build
    
    - name: Upload artifacts
      uses: actions/upload-artifact@v4
      with:
        name: python-package-distributions
        path: dist/

  publish-pypi:
    name: Publish to PyPI
    if: github.event_name == 'release'
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: pypi
      url: https://pypi.org/project/{package_name}/
    
    steps:
    - name: Download artifacts
      uses: actions/download-artifact@v4
      with:
        name: python-package-distributions
        path: dist/
    
    - name: Publish to PyPI
      uses: pypa/gh-action-pypi-publish@release/v1
```

For additional workflows (optional):
1. Read `docs/cicd/GITHUB_ACTIONS.md` for detailed setup instructions
2. Copy workflow templates from `templates/cicd/workflows/` as needed:
   - `publish.yml` for PyPI publishing (shown above)
   - `github-actions-coverage.yaml` for coverage reporting
   - `github-actions-lint.yaml` for additional linting

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

### Running the Application

**⚠️ CRITICAL: Always use `uv run python` to execute Python code, NOT plain `python`**

```bash
# ✅ CORRECT - Uses project's virtual environment
uv run python src/{package_name}/main.py
uv run python -m {package_name}.main

# ❌ INCORRECT - May use system Python or wrong environment
python src/{package_name}/main.py  # DO NOT USE
python -m {package_name}.main      # DO NOT USE
```

### Verification Commands

Run these commands to verify setup:

```bash
# Run the application (ALWAYS use uv run python)
uv run python src/{package_name}/main.py

# Package management
uv --version
uv pip list

# Testing with coverage (IMPORTANT: Check coverage meets minimum requirements)
uv run pytest --cov=src/{package_name} --cov-report=term-missing

# ⚠️ Coverage Requirements (from docs/testing/TEST_COVERAGE.md):
# - Minimum overall: 80%
# - Critical modules: 90-100%
# - New code: 95%+
# If coverage is below 80%, add more tests before considering bootstrap complete

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

- **AI Documentation** (Step 4): [docs/LLMS_TXT.md](docs/LLMS_TXT.md)
  - WHEN: Creating llms.txt and CLAUDE.md files
  - WHY: Best practices for AI agent documentation
  - Templates: [templates/llms.txt](templates/llms.txt), [templates/CLAUDE.md](templates/CLAUDE.md)

**DO NOT skip reading these documents - they contain critical implementation details!**

## ⚠️ CRITICAL: Verification Checklist

**DO NOT consider the project complete until ALL of these pass:**

```bash
# 1. Check project structure
ls -la .github/workflows/  # MUST contain ci.yml
ls -la .pre-commit-config.yaml  # MUST exist
ls -la src/ tests/ docs/  # MUST exist

# 2. Verify dependencies install
uv sync --dev  # MUST complete without errors

# 3. Run the application (ALWAYS use uv run python, NOT plain python)
uv run python src/{package_name}/main.py  # MUST run without errors

# 4. Run tests with coverage
uv run pytest --cov=src/{package_name} --cov-report=term-missing  # MUST pass all tests
# MUST have minimum 80% coverage (see docs/testing/TEST_COVERAGE.md)
# If coverage < 80%, bootstrap is INCOMPLETE - add more tests!

# 5. Check code quality
uv run ruff check .  # MUST pass
uv run ruff format --check .  # MUST pass
uv run mypy src/  # SHOULD pass (may need configuration)

# 6. Verify pre-commit hooks
uv run pre-commit run --all-files  # MUST pass

# 7. Check git
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