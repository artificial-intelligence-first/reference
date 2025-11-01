# System Tools & Utilities

> **For Humans**
> This directory contains documentation for development tools, version control systems, and utilities that support AI-first development workflows.

> **For AI Agents**
> Reference these guides when working with development infrastructure, version control operations, and system-level tooling. These documents provide canonical patterns for tool usage in development contexts.

---

## Directory Structure

```
system/
├── README.md           # This file - system tools overview
└── git/                # Git version control system
    └── worktree.md     # Git worktree patterns and workflows
```

---

## Git Tools

### [Git Worktree](git/worktree.md)
**Managing multiple working trees for simultaneous branch work**

- Multiple working trees from single repository
- Commands: add, list, remove, prune, lock, move, repair
- Parallel development patterns
- Hotfix workflows during feature development
- Code review without context switching

**Use when**: Working on multiple branches simultaneously, urgent hotfixes during feature work, code reviews, parallel builds.

---

## Future Topics

Planned additions to this directory:

### Version Control
- **`git/hooks.md`** - Git hooks for automation and validation
- **`git/submodules.md`** - Submodule management patterns
- **`git/lfs.md`** - Git Large File Storage best practices

### Development Tools
- **`docker/`** - Container-based development environments
- **`editors/`** - Editor and IDE configuration for AI workflows
- **`build-tools/`** - Build systems and dependency management

### System Integration
- **`shell/`** - Shell scripting patterns
- **`automation/`** - Task automation and scripting
- **`ci-cd/`** - Continuous integration and deployment patterns

---

## Contributing

When adding documentation to this directory:

1. **Check `_meta/CONTRIBUTING.md`** for workflow guidance
2. **Follow `_meta/STYLE.md`** for formatting rules
3. **Use `_meta/TAXONOMY.md`** for appropriate tags
4. **Reference `_templates/TOPIC_TEMPLATE.md`** for structure

---

## Organization Principles

This directory follows these principles:

- **Tool-focused**: Each subdirectory represents a specific tool or tool category
- **Practical patterns**: Focus on real-world usage, not just feature documentation
- **AI-optimized**: Structure content for both human learning and AI agent reference
- **Cross-referenced**: Link to related topics in `files/`, `engineering/`, and `platforms/`

---

**Last Updated**: 2025-11-01
