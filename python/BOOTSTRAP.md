# Python Project Bootstrap Guide for AI Agents

This guide helps AI agents bootstrap new Python projects using the vibe-coding-templates.

## Quick Start Checklist

When bootstrapping a Python project:

```markdown
☐ 1. Create project structure
☐ 2. Set up package management with uv (see docs/PACKAGE_MANAGEMENT.md)
☐ 3. Configure testing with pytest  
☐ 4. Set up GitHub Actions (see docs/cicd/GITHUB_ACTIONS.md)
☐ 5. Configure pre-commit hooks (see docs/cicd/PRE_COMMIT.md)
☐ 6. Initialize git repository
☐ 7. Run verification tests
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

See full configuration options in `docs/PACKAGE_MANAGEMENT.md`.

### Makefile

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

### tests/test_main.py
Create basic tests that import and test your package:
```python
from src.{package_name}.main import your_function

def test_your_function():
    assert your_function() is not None
```

### tests/conftest.py
Add pytest fixtures as needed. See `docs/testing/TEST_COVERAGE.md` for testing best practices.

## Step 6: Configure GitHub Actions

Copy and customize workflow templates from `templates/cicd/workflows/`:
- `github-actions-test.yaml` → `.github/workflows/test.yml`
- Additional workflows as needed

See `docs/cicd/GITHUB_ACTIONS.md` for detailed setup instructions.

## Step 7: Configure Pre-commit Hooks

Copy and customize hook configurations from `templates/cicd/hooks/`:
- Use `pre-commit-ruff-hook.yaml` for linting/formatting
- Use `pre-commit-mypy-hook.yaml` for type checking
- Use `pre-commit-pytest-hook.yaml` for test execution

See `docs/cicd/PRE_COMMIT.md` for detailed configuration.

## Step 8: Initialize and Install

```bash
# Initialize git
git init
git add .
git commit -m "Initial project structure"

# Install uv (if not present)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install dependencies
uv sync --dev

# Install pre-commit hooks
uv run pre-commit install
```

## Step 9: Verification

Run these commands to verify setup (from `docs/PACKAGE_MANAGEMENT.md`):

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

## Using Existing Documentation

This bootstrap guide references:
- **Package Management**: [docs/PACKAGE_MANAGEMENT.md](docs/PACKAGE_MANAGEMENT.md)
- **GitHub Actions**: [docs/cicd/GITHUB_ACTIONS.md](docs/cicd/GITHUB_ACTIONS.md)
- **Pre-commit Hooks**: [docs/cicd/PRE_COMMIT.md](docs/cicd/PRE_COMMIT.md)
- **Testing**: [docs/testing/TEST_COVERAGE.md](docs/testing/TEST_COVERAGE.md)

## Success Criteria

Bootstrap is successful when:
- ✅ All directories created
- ✅ `uv sync --dev` completes
- ✅ `uv run pytest` passes
- ✅ `uv run ruff check .` passes
- ✅ `pre-commit run --all-files` passes
- ✅ Git repository initialized