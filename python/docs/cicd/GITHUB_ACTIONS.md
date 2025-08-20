# GitHub Actions Setup Guide

## Overview

GitHub Actions provides continuous integration and deployment automation. This guide helps you set up workflows based on your project's requirements.

## Quick Start

1. **Check Project Requirements**
   ```yaml
   # Review manifests/project-workflows.yaml for required workflows
   cat manifests/manifests/project-workflows.yaml
   ```

2. **Create Workflow Directory**
   ```bash
   mkdir -p .github/workflows
   ```

3. **Install Required Workflows**
   Use the templates specified in `manifests/project-workflows.yaml` to create your workflow files.

## Project Workflow Configuration

Your project's workflow requirements are defined in `manifests/project-workflows.yaml`. This file specifies:
- Which workflows are required vs optional
- Template URLs for each workflow
- Configuration parameters
- Required secrets
- Trigger conditions

### Example Workflow Manifest
```yaml
workflows:
  - id: test-suite
    name: Test Suite
    template: <template-url>
    required: true
    config:
      python_versions: ['3.11']
      package_name: {package_name}
```

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

### Step 1: Read Project Requirements
```bash
# Check which workflows your project needs
cat manifests/project-workflows.yaml | grep "required: true"
```

### Step 2: Download Templates
For each required workflow in `manifests/project-workflows.yaml`:
```bash
# Example: Download test workflow template
curl -o .github/workflows/test.yml <template-url>
```

### Step 3: Customize Configuration
Replace placeholders in downloaded workflows:
- `{package_name}` → Your package name
- `{python_version}` → Your Python version(s)
- Update triggers based on your branch strategy

### Step 4: Configure Secrets
Add required secrets to your GitHub repository:
1. Go to Settings → Secrets → Actions
2. Add secrets listed in `manifests/project-workflows.yaml`

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

Add badges from `manifests/project-workflows.yaml` to your README:

```markdown
![Tests](badge-url)
![Coverage](badge-url)
![Python](badge-url)
```

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
1. Always check `manifests/project-workflows.yaml` first
2. Download only required workflows
3. Customize based on project needs
4. Set up secrets before running workflows
5. Test workflows on feature branches first