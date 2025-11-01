---
title: Git Worktree
slug: git-worktree
status: living
last_updated: 2025-11-01
tags: [git, version-control, workflows, best-practices]
summary: "Git feature for managing multiple working trees attached to the same repository simultaneously."
authors: []
sources:
  - { id: R1, title: "Git - git-worktree Documentation", url: "https://git-scm.com/docs/git-worktree", accessed: "2025-11-01" }
---

# Git Worktree

> **For Humans**: Git worktree enables working on multiple branches simultaneously without stashing or switching, ideal for parallel development workflows.
>
> **For AI Agents**: Use git worktree commands to manage multiple working directories for concurrent development tasks without context switching.

## Overview

Git worktree allows multiple working trees to be attached to the same repository, enabling simultaneous work on different branches without the overhead of cloning or stashing changes.

## Basic Usage

### Create Worktree

```bash
# Create new worktree for existing branch
git worktree add ../project-hotfix hotfix/critical-bug

# Create new worktree with new branch
git worktree add -b feature/new-feature ../project-feature

# Create from specific commit
git worktree add ../project-review abc123
```

### Manage Worktrees

```bash
# List all worktrees
git worktree list

# Remove worktree
git worktree remove ../project-hotfix

# Prune stale worktrees
git worktree prune
```

## Common Patterns

### Parallel Development

```bash
project/
├── main/           # Main development
├── feature/        # Feature branch
├── hotfix/         # Critical fixes
└── review/         # Code review
```

### Workflow Example

```bash
# Main development continues
cd ~/project/main
git pull origin main

# Meanwhile, work on feature
cd ~/project/feature
git commit -am "Add new feature"

# Urgent hotfix without disrupting feature work
cd ~/project
git worktree add hotfix-urgent hotfix/security-patch
cd hotfix-urgent
# Fix and push
```

## Best Practices

1. **Naming Convention**: Use descriptive directory names
2. **Cleanup**: Remove worktrees after merging
3. **Organization**: Keep worktrees in dedicated parent directory
4. **Limit Count**: Avoid too many simultaneous worktrees

## See Also

- [Git Documentation](https://git-scm.com/docs)
- [Workflows](../../workflows/plans.md)

## References

- [R1] Git - git-worktree Documentation. https://git-scm.com/docs/git-worktree (accessed 2025-11-01)