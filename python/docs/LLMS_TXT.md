# llms.txt Documentation

## Overview

The `llms.txt` file is a specialized documentation format designed to help AI agents (like Claude, GPT-4, Gemini, etc.) quickly understand your project's structure, purpose, and development workflow. It serves as a concise project overview optimized for LLM consumption.

## Purpose

AI agents need context to provide effective assistance. The `llms.txt` file provides:
- Quick project overview and key features
- Essential code examples
- Project structure visualization
- Development workflow commands
- API reference highlights

## Template Usage

📚 **Use the llms.txt template: [python/templates/llms.txt](../templates/llms.txt)**

### Creating Your llms.txt

1. Copy the template:
```bash
cp python/templates/llms.txt ./llms.txt
```

2. Replace placeholders with your project details:
   - `{{project_name}}` - Your project's directory name
   - `{{package_name}}` - Python package name (underscores, not hyphens)
   - `{{description}}` - Brief project description
   - `{{project_type}}` - Type of project (e.g., "library", "CLI tool", "web API")
   - `{{key_functionality}}` - What the project does in one sentence
   - `{{feature_1/2/3}}` - Main features (keep concise)
   - `{{main_class_or_function}}` - Primary entry point
   - `{{python_version}}` - Minimum Python version required
   - `{{license}}` - License type (e.g., "MIT", "Apache 2.0")

## Content Guidelines

### 1. Overview Section
Keep it brief but informative:
- One-sentence description
- 3-5 key features with emoji indicators
- Focus on what makes the project unique

### 2. Quick Start
Provide the simplest working example:
```python
from package_name import main_function

# Minimal working example
result = main_function()
```

### 3. Project Structure
Show only essential directories and files:
```
project/
├── src/package/     # Core code
├── tests/           # Test suite
├── docs/            # Documentation
└── pyproject.toml   # Configuration
```

### 4. Development Commands
Include only the most common commands:
- Installation: `uv sync --dev`
- Testing: `uv run pytest`
- Quality checks: `make qa`
- Help: `make help`

### 5. Key APIs
List 3-5 most important functions/classes:
- Include brief descriptions
- Focus on public APIs
- Mention configuration options

## Best Practices

### DO's
✅ Keep it under 150 lines  
✅ Use concrete examples  
✅ Include actual command syntax  
✅ Reference other documentation files  
✅ Update when APIs change  
✅ Use emoji sparingly for visual structure  

### DON'Ts
❌ Don't duplicate README content extensively  
❌ Don't include implementation details  
❌ Don't list every function  
❌ Don't include lengthy explanations  
❌ Don't forget to update placeholders  

## Example: Real Project

Here's an example for a data processing library:

```markdown
# DataFlow

> High-performance data pipeline library for Python

## Overview

DataFlow is a Python library that simplifies building efficient data processing pipelines.

Key features:
- 🚀 Parallel processing with async support
- 📊 Built-in data validation
- 🔧 Extensible transformer system

## Quick Start for AI Agents

The simplest way to use DataFlow:

```python
from dataflow import Pipeline, Transform

# Create and run a pipeline
pipeline = Pipeline()
pipeline.add(Transform.normalize())
result = pipeline.run(data)
```

## Project Structure

```
dataflow/
├── src/dataflow/     # Core pipeline code
│   ├── pipeline.py   # Main pipeline class
│   └── transforms/   # Built-in transformers
├── tests/            # Comprehensive test suite
└── examples/         # Usage examples
```

[Rest of content following template...]
```

## Integration with AI Tools

### GitHub Copilot
Place `llms.txt` in the root directory for automatic context.

### Claude Projects
Upload `llms.txt` as a project knowledge file.

### Custom AI Assistants
Include `llms.txt` in system prompts or context windows.

## Maintenance

### When to Update
- API changes
- New major features
- Project structure changes
- Development workflow updates

### Review Schedule
- Monthly for active projects
- Before major releases
- When onboarding new AI tools

## Relationship to Other Files

- **README.md**: User-facing documentation
- **CLAUDE.md**: Claude-specific project instructions  
- **llms.txt**: Generic AI agent context
- **pyproject.toml**: Technical project configuration

The `llms.txt` file complements but doesn't replace these files.

## Common Mistakes

1. **Too Much Detail**: Keep it high-level
2. **Outdated Examples**: Test code examples regularly
3. **Missing Structure**: Always include project layout
4. **No Commands**: Include actual runnable commands
5. **Wrong Audience**: Write for AI agents, not humans

## Template Variables Reference

| Variable | Description | Example |
|----------|-------------|---------|
| `{{project_name}}` | Project directory name | `my-awesome-project` |
| `{{package_name}}` | Python package name | `my_awesome_project` |
| `{{description}}` | One-line description | `Fast data processing library` |
| `{{project_type}}` | Type of project | `library`, `CLI tool`, `API` |
| `{{key_functionality}}` | Main purpose | `processes data pipelines efficiently` |
| `{{feature_1/2/3}}` | Key features | `Async support`, `Type safety` |
| `{{main_class_or_function}}` | Entry point | `Pipeline`, `process_data` |
| `{{python_version}}` | Min Python version | `3.10`, `3.11` |
| `{{license}}` | License type | `MIT`, `Apache 2.0`, `GPL-3.0` |

## Related Documentation

- [Project Templates](../templates/)
- [BOOTSTRAP.md](../BOOTSTRAP.md) - Full project setup guide
- [AI_AGENT_GUIDE.md](../AI_AGENT_GUIDE.md) - AI agent best practices
- [PACKAGE_MANAGEMENT.md](PACKAGE_MANAGEMENT.md) - Package management details

## Template Metadata

- **Repository**: [vibe-coding-templates](https://github.com/chrishayuk/vibe-coding-templates)
- **Path**: python/docs/LLMS_TXT.md
- **Version**: 1.0.0
- **Date**: 2025-01-24
- **Author**: chrishayuk