# GitHub Actions Setup Guide

## Overview

GitHub Actions provides continuous integration and deployment automation. This guide helps you set up workflows based on your project's requirements.

## Quick Start

1. **Create Workflow Directory**
   ```bash
   mkdir -p .github/workflows
   ```

2. **Choose Your Workflows**
   Select from the available templates based on your project needs:
   - **Required**: Test suite for all projects
   - **Recommended**: Coverage reporting and linting
   - **Optional**: Benchmarks, releases, documentation

3. **Copy and Customize Templates**
   Copy the relevant templates from `templates/cicd/workflows/` and customize for your project.

## Choosing Workflows for Your Project

### Essential Workflows (Required)
- **Test Suite**: Run tests across Python versions
  - Template: `github-actions-test.yaml`
  - Purpose: Ensure code works on supported platforms

### Recommended Workflows
- **Coverage**: Track test coverage metrics
  - Template: `github-actions-coverage.yaml`
  - Purpose: Maintain code quality standards
- **Linting**: Automated code quality checks
  - Template: `github-actions-lint.yaml`
  - Purpose: Enforce coding standards

### Optional Workflows
- **Benchmarks**: Performance testing
- **Release**: PyPI publishing automation
- **Documentation**: Auto-generate docs

## Available Workflow Templates

### Testing Workflows
- **Test Suite**: [github-actions-test.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/workflows/github-actions-test.yaml)
- **Coverage**: [github-actions-coverage.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/workflows/github-actions-coverage.yaml)
- **Benchmarks**: [github-actions-benchmark.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/workflows/github-actions-benchmark.yaml)

### Quality Workflows
- **Linting**: [github-actions-lint.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/workflows/github-actions-lint.yaml)
- **Security**: [github-actions-security.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/workflows/github-actions-security.yaml)
- **Dependencies**: [github-actions-deps.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/workflows/github-actions-deps.yaml)

### Release Workflows
- **PyPI Release**: [github-actions-release.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/workflows/github-actions-release.yaml)
- **GitHub Release**: [github-actions-gh-release.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/workflows/github-actions-gh-release.yaml)
- **Documentation**: [github-actions-docs.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/workflows/github-actions-docs.yaml)

## Setting Up Workflows

### Step 1: Choose Your Workflows
Decide which workflows you need:
- **Minimum**: Test suite (`github-actions-test.yaml`)
- **Recommended**: Add coverage and linting
- **Full CI/CD**: Include all relevant workflows

### Step 2: Copy Templates
```bash
# Copy test workflow (required)
cp templates/cicd/workflows/github-actions-test.yaml .github/workflows/test.yml

# Copy coverage workflow (recommended)
cp templates/cicd/workflows/github-actions-coverage.yaml .github/workflows/coverage.yml

# Copy other workflows as needed
```

### Step 3: Customize Configuration
Replace placeholders in your workflow files:
- `{package_name}` → Your package name
- `{python_version}` → Your Python version(s)
- Update branch names in triggers
- Adjust matrix strategy for your needs

### Step 4: Configure Secrets (if needed)
For workflows requiring secrets:
1. Go to Settings → Secrets → Actions
2. Add required secrets (e.g., `PYPI_API_TOKEN` for releases)

## Workflow Triggers

### Common Trigger Patterns
```yaml
# On push to specific branches
on:
  push:
    branches: [main, develop]
    
# On pull request
on:
  pull_request:
    branches: [main]
    
# On release
on:
  release:
    types: [published]
    
# On schedule
on:
  schedule:
    - cron: '0 0 * * 0'  # Weekly
    
# Manual trigger
on:
  workflow_dispatch:
```

## Using uv in Workflows

Most Python workflows should use `uv` for package management:

```yaml
- name: Install uv
  uses: astral-sh/setup-uv@v3
  
- name: Install dependencies
  run: uv sync --dev
  
- name: Run tests
  run: uv run pytest
```

## Workflow Best Practices

### 1. Use Matrix Strategy
Test across multiple environments:
```yaml
strategy:
  matrix:
    python-version: ['3.9', '3.10', '3.11']
    os: [ubuntu-latest, macos-latest]
```

### 2. Cache Dependencies
Speed up workflow runs:
```yaml
- uses: actions/cache@v3
  with:
    path: ~/.cache/uv
    key: uv-${{ hashFiles('**/uv.lock') }}
```

### 3. Use Concurrency Control
Prevent duplicate runs:
```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

### 4. Set Timeouts
Prevent hanging workflows:
```yaml
jobs:
  test:
    timeout-minutes: 30
```

## Status Badges

Add badges to your README to show workflow status:

```markdown
![Tests](https://github.com/{username}/{repo}/workflows/Test%20Suite/badge.svg)
![Coverage](https://codecov.io/gh/{username}/{repo}/branch/main/graph/badge.svg)
![Python](https://img.shields.io/badge/python-3.9%20%7C%203.10%20%7C%203.11%20%7C%203.12-blue)
```

Replace `{username}` and `{repo}` with your GitHub details.

## Troubleshooting

### Common Issues

**Workflow not triggering**
- Check branch names in triggers
- Verify file location (`.github/workflows/`)
- Ensure YAML syntax is valid

**Permission errors**
```yaml
permissions:
  contents: read
  pull-requests: write
```

**Secret not found**
- Verify secret name matches exactly
- Check secret is set in repository settings
- Ensure secret scope (repository/organization)

## Related Documentation

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Pre-commit Setup](./PRE_COMMIT.md)
- [Package Management](./PACKAGE_MANAGEMENT.md)
- [Test Coverage](./TEST_COVERAGE.md)

## Template Information

- **Source**: [vibe-coding-templates](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/docs/cicd/GITHUB_ACTIONS.md)
- **Version**: 1.0.0
- **Date**: 2025-08-19
- **Author**: chrishayuk
- **Template**: Generic Python Project

### Customization Notes

When using this guide:
1. Start with the test workflow (required for all projects)
2. Add additional workflows based on project needs
3. Customize templates for your specific requirements
4. Set up secrets before running workflows that need them
5. Test workflows on feature branches first