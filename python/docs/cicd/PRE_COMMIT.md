# Pre-commit Hooks Setup Guide

## Overview

Pre-commit hooks automatically check your code before commits, ensuring code quality and preventing common issues. This guide helps you set up hooks based on your project's requirements.

## Quick Start

1. **Check Project Requirements**
   ```yaml
   # Review manifests/project-hooks.yaml for required hooks
   cat manifests/project-hooks.yaml
   ```

2. **Install Pre-commit**
   ```bash
   uv add --dev pre-commit
   ```

3. **Install Hooks**
   ```bash
   pre-commit install
   pre-commit install --hook-type pre-push  # For pre-push hooks
   ```

## Project Hook Configuration

Your project's hook requirements are defined in `manifests/project-hooks.yaml`. This file specifies:
- Which hooks are required vs optional
- Template URLs for each hook
- Configuration parameters
- Hook stages (pre-commit, pre-push, manual)

### Example Hook Manifest
```yaml
hooks:
  - id: ruff-check
    name: Ruff Linter
    template: <template-url>
    required: true
    config:
      package_name: {package_name}
```

## Available Hook Templates

### Code Quality
- **Ruff**: [pre-commit-ruff-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-ruff-hook.yaml)
- **Black**: [pre-commit-black-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-black-hook.yaml)
- **isort**: [pre-commit-isort-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-isort-hook.yaml)

### Type Checking
- **MyPy**: [pre-commit-mypy-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-mypy-hook.yaml)
- **Pyright**: [pre-commit-pyright-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-pyright-hook.yaml)

### Testing
- **Coverage**: [pre-commit-coverage-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-coverage-hook.yaml)
- **Pytest**: [pre-commit-pytest-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-pytest-hook.yaml)

### Security
- **Secrets**: [pre-commit-secrets-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-secrets-hook.yaml)
- **Bandit**: [pre-commit-bandit-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-bandit-hook.yaml)
- **Safety**: [pre-commit-safety-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-safety-hook.yaml)

### Documentation
- **Markdown**: [pre-commit-markdown-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-markdown-hook.yaml)
- **YAML**: [pre-commit-yaml-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-yaml-hook.yaml)

## Setting Up Hooks

### Step 1: Create Configuration File

Create `.pre-commit-config.yaml`:

```yaml
# See manifests/project-hooks.yaml for required hooks
repos:
  # Add repo configurations here
  - repo: local
    hooks:
      # Local hooks from manifests/project-hooks.yaml
```

### Step 2: Add Required Hooks

For each required hook in `manifests/project-hooks.yaml`:
1. Copy the hook configuration from the template URL
2. Add to `.pre-commit-config.yaml`
3. Customize configuration parameters

### Step 3: Configure Hook Stages

```yaml
# Default stage (pre-commit)
- id: ruff-check
  stages: [commit]

# Pre-push stage
- id: test-coverage
  stages: [push]

# Manual stage
- id: full-test-suite
  stages: [manual]
```

### Step 4: Install and Test

```bash
# Install hooks
pre-commit install
pre-commit install --hook-type pre-push

# Test all hooks
pre-commit run --all-files

# Test specific hook
pre-commit run ruff-check --all-files

# Run manual stage hooks
pre-commit run --hook-stage manual
```

## Hook Stages

### Pre-commit Stage
Runs before each commit:
- Fast checks (< 5 seconds)
- Formatting fixes
- Basic linting

### Pre-push Stage
Runs before pushing:
- Type checking
- Test coverage
- Security scans

### Manual Stage
Run on demand:
- Full test suite
- Performance benchmarks
- Deep security analysis

## Using uv with Pre-commit

Configure hooks to use `uv`:

```yaml
- repo: local
  hooks:
    - id: pytest
      name: Run tests
      entry: uv run pytest
      language: system
      pass_filenames: false
```

## Common Hook Patterns

### Auto-fixing Hooks
```yaml
- id: ruff-format
  name: Format with ruff
  entry: uv run ruff format
  language: system
  types: [python]
  # Files are automatically staged after fixes
```

### Conditional Hooks
```yaml
- id: test-changed
  name: Test changed files
  entry: uv run pytest
  language: system
  files: \.py$
  pass_filenames: true
```

### Exclude Patterns
```yaml
- id: mypy
  name: Type check
  entry: uv run mypy
  language: system
  types: [python]
  exclude: ^(tests/|docs/|examples/)
```

## Best Practices

### 1. Start Small
Begin with essential hooks and add more gradually:
- Format checking
- Basic linting
- Secret detection

### 2. Keep Hooks Fast
Pre-commit hooks should complete quickly:
- Use `--hook-stage` for slow checks
- Run comprehensive tests in CI/CD
- Cache results when possible

### 3. Auto-fix When Possible
Enable auto-fixing for formatting:
```yaml
- id: ruff-format
  args: [--fix]
```

### 4. Document Hook Requirements
Keep `manifests/project-hooks.yaml` updated with:
- Why each hook is needed
- Configuration rationale
- Override instructions

## Troubleshooting

### Common Issues

**Hook Installation Failed**
```bash
# Clear and reinstall
pre-commit clean
pre-commit uninstall
pre-commit install
```

**Hook Timeout**
```yaml
# Increase timeout for slow hooks
- id: slow-hook
  timeout: 300  # 5 minutes
```

**File Not Found**
```bash
# Ensure dependencies are installed
uv sync --dev
```

**Skipping Hooks (Emergency)**
```bash
# Skip hooks for emergency commit
git commit --no-verify -m "Emergency fix"

# Skip specific hook
SKIP=hook-id git commit -m "Message"
```

## CI/CD Integration

### Validate Hooks in CI
```yaml
# GitHub Actions example
- name: Run pre-commit
  run: |
    uv add --dev pre-commit
    pre-commit run --all-files
```

### Pre-commit CI Service
```yaml
# .pre-commit-config.yaml
ci:
  autofix_commit_msg: 'Auto-fix from pre-commit.ci'
  autofix_prs: true
  autoupdate_schedule: weekly
```

## Related Documentation

- [Pre-commit Documentation](https://pre-commit.com/)
- [GitHub Actions Setup](./GITHUB_ACTIONS.md)
- [Package Management](./PACKAGE_MANAGEMENT.md)
- [Test Coverage](./TEST_COVERAGE.md)

## Template Information

- **Source**: [vibe-coding-templates](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/docs/cicd/PRE_COMMIT.md)
- **Version**: 1.0.0
- **Date**: 2025-08-19
- **Author**: chrishayuk
- **Template**: Generic Python Project

### Customization Notes

When using this guide:
1. Always check `manifests/project-hooks.yaml` first
2. Install only required hooks initially
3. Add optional hooks based on team needs
4. Test hooks locally before committing config
5. Document any hook overrides or exceptions