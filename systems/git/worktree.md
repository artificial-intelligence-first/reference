---
title: Git Worktree
slug: git-worktree
status: living
last_updated: 2025-11-01
tags: [git, version-control, workflows, best-practices]
summary: "Git worktree enables managing multiple working trees attached to the same repository, allowing simultaneous work on different branches."
sources:
  - { id: R1, title: "Git - git-worktree Documentation", url: "https://git-scm.com/docs/git-worktree", accessed: "2025-11-01" }
  - { id: R2, title: "git-worktree(1)", url: "https://www.kernel.org/pub/software/scm/git/docs/git-worktree.html", accessed: "2025-11-01" }
---

# Git Worktree

> **For Humans**
> This document explains Git worktree and provides practical guidance for working with multiple branches simultaneously. Use this when you need to work on multiple features, hotfixes, or code reviews without constant branch switching.

> **For AI Agents**
> This document defines canonical patterns for Git worktree usage. Apply these patterns when managing multiple concurrent development contexts. Cross-reference with version control workflows and development environment setup.

---

## Table of Contents

- [TL;DR](#tldr)
- [Canonical Definitions](#canonical-definitions)
- [Core Patterns](#core-patterns)
- [Decision Checklist](#decision-checklist)
- [Anti-patterns / Pitfalls](#anti-patterns--pitfalls)
- [Evaluation](#evaluation)
- [Update Log](#update-log)
- [See Also](#see-also)
- [References](#references)

---

## TL;DR

- **What**: Git worktree manages multiple working trees attached to the same repository, enabling simultaneous checkout of different branches [R1]
- **Why**: Eliminates context switching overhead and allows parallel work on features, hotfixes, and reviews without stashing or committing incomplete work
- **When**: Use for parallel development, urgent hotfixes during feature work, code reviews, or running multiple build configurations
- **How**: Create linked worktrees with `git worktree add`, work independently in each, and clean up with `git worktree remove`
- **Watch out**: Cannot check out the same branch in multiple worktrees simultaneously; manual directory deletion causes metadata corruption [R1]

---

## Canonical Definitions

### Worktree

**Definition**: A working tree is a directory containing a checked-out version of the repository files. The main worktree is created when cloning a repository, while additional worktrees (linked worktrees) can be created to check out different branches simultaneously [R1].

**Scope**:
- **Includes**: File system directory, checked-out branch, working directory state, Git metadata
- **Excludes**: The `.git` directory is shared (linked worktrees use `.git` file pointing to the main repository)

**Related Concepts**:
- **Main worktree**: The primary working directory created during `git clone` or `git init`
- **Linked worktree**: Additional working trees created with `git worktree add`
- **Branch**: Each worktree can have a different branch checked out, but the same branch cannot be checked out in multiple worktrees [R1]

### Working Tree vs Working Directory

**Definition**: In Git terminology, "working tree" and "working directory" are often used interchangeably to refer to the actual files you edit. The worktree feature extends this to allow multiple working directories for one repository [R1].

**Scope**: The worktree feature creates multiple working directories that all connect to the same Git repository (`.git` directory).

---

## Core Patterns

### Pattern 1: Creating a New Worktree

**Intent**: Create an additional working directory to work on a different branch without affecting your current work.

**Context**: When you need to work on multiple branches simultaneously without stashing or committing incomplete work.

**Implementation**:

```bash
# Create a new worktree for a new branch
git worktree add ../feature-login -b feature/login

# Create a worktree for an existing branch
git worktree add ../hotfix-auth hotfix/auth-bug

# Create a worktree from a specific commit
git worktree add ../review-pr main~3

# Create with specific path structure
git worktree add -b feature/payment ../worktrees/payment
```

**Key Principles**:
- Worktrees are typically created as sibling directories to the main repository to maintain clean organization
- Use `-b` flag to create a new branch in the worktree
- The worktree path can be absolute or relative
- Each worktree has its own index and working directory but shares the same repository [R1]

**Trade-offs**:
- ✅ **Advantages**: No context switching, preserve uncommitted work, parallel builds/tests
- ⚠️ **Disadvantages**: Uses additional disk space, can be confusing if not properly organized
- 💡 **Alternatives**: Use `git stash` for simple context switches, or clone the repository multiple times (uses more disk space)

**Sources**: [R1][R2]

### Pattern 2: Listing Active Worktrees

**Intent**: View all worktrees associated with the repository and their status.

**Context**: When you need to understand the current worktree landscape or find a specific worktree.

**Implementation**:

```bash
# List all worktrees with basic info
git worktree list

# Example output:
# /home/user/myproject        abc123d [main]
# /home/user/feature-login    def456e [feature/login]
# /home/user/hotfix-auth      789ghij [hotfix/auth-bug]

# Verbose output with more details
git worktree list --verbose

# Porcelain format for scripting
git worktree list --porcelain
```

**Key Principles**:
- Shows path, HEAD commit, and branch name for each worktree
- Locked worktrees are indicated with additional information
- Porcelain format is stable for scripting and automation [R1]

**Trade-offs**:
- ✅ **Advantages**: Quick overview of all active development contexts
- ⚠️ **Disadvantages**: Does not show detailed status (use `git status` in each worktree)
- 💡 **Alternatives**: Navigate file system manually, but this doesn't show branch associations

**Sources**: [R1][R2]

### Pattern 3: Removing Worktrees

**Intent**: Clean up worktrees that are no longer needed.

**Context**: When feature work is complete, merged, or abandoned.

**Implementation**:

```bash
# Remove a worktree (must be clean or use -f)
git worktree remove ../feature-login

# Force remove even with uncommitted changes
git worktree remove -f ../feature-login

# Remove from within the main repository
cd /home/user/myproject
git worktree remove ../feature-login

# Prune stale worktree metadata
git worktree prune

# Dry run to see what would be pruned
git worktree prune --dry-run
```

**Key Principles**:
- Always use `git worktree remove` instead of manually deleting directories [R1]
- The worktree must not have uncommitted changes unless `-f` is used
- `prune` removes metadata for worktrees that were manually deleted or moved
- Regular pruning keeps repository metadata clean

**Trade-offs**:
- ✅ **Advantages**: Properly cleans up Git metadata, prevents orphaned references
- ⚠️ **Disadvantages**: Requires explicit command (not automatic)
- 💡 **Alternatives**: Lock worktrees that should be permanent to prevent accidental removal

**Sources**: [R1][R2]

### Pattern 4: Locking and Unlocking Worktrees

**Intent**: Prevent a worktree from being removed or pruned, especially on removable media or network drives.

**Context**: When a worktree is on a temporarily unavailable mount point or should be protected from cleanup.

**Implementation**:

```bash
# Lock a worktree with optional reason
git worktree lock ../feature-login
git worktree lock ../feature-login --reason "Work in progress, do not remove"

# Unlock a worktree
git worktree unlock ../feature-login

# Check lock status
git worktree list
# Output shows: /home/user/feature-login  abc123d [feature/login] locked
```

**Key Principles**:
- Locked worktrees are protected from `git worktree remove` and `git worktree prune`
- Useful for worktrees on removable drives or network shares [R1]
- Lock reason is stored and displayed to help understand why it's locked
- Must unlock before removing

**Trade-offs**:
- ✅ **Advantages**: Prevents accidental removal, useful for temporary unavailability
- ⚠️ **Disadvantages**: Can cause confusion if lock reason not documented
- 💡 **Alternatives**: Document worktree purposes in team documentation

**Sources**: [R1][R2]

### Pattern 5: Moving Worktrees

**Intent**: Relocate a worktree to a different path without recreating it.

**Context**: When reorganizing file structure or moving to a different drive.

**Implementation**:

```bash
# Move a worktree to a new location
git worktree move ../feature-login ../worktrees/feature-login

# Move with path normalization
git worktree move ../old-path /absolute/new/path
```

**Key Principles**:
- Updates internal Git references to the new path [R1]
- Preserves all working directory state and uncommitted changes
- Both source and destination paths can be relative or absolute
- Safer than manually moving and running `git worktree repair`

**Trade-offs**:
- ✅ **Advantages**: Maintains all Git metadata and working state
- ⚠️ **Disadvantages**: Must use Git command, cannot simply drag-and-drop
- 💡 **Alternatives**: Create new worktree at desired location and copy changes manually

**Sources**: [R1][R2]

### Pattern 6: Repairing Worktrees

**Intent**: Fix worktree administrative files that have become corrupted or out of sync.

**Context**: After manual moves, repository reorganization, or file system issues.

**Implementation**:

```bash
# Repair all worktrees
git worktree repair

# Repair from within a specific worktree
cd /home/user/feature-login
git worktree repair

# Repair with verbose output
git worktree repair --verbose
```

**Key Principles**:
- Fixes broken administrative files in `.git/worktrees/` [R2]
- Can be run from main worktree or any linked worktree
- Automatically detects and repairs common issues
- Run after manual file system operations that affected worktree paths

**Trade-offs**:
- ✅ **Advantages**: Recovers from manual errors without recreating worktrees
- ⚠️ **Disadvantages**: May not fix all corruption scenarios
- 💡 **Alternatives**: Remove corrupted worktree and create fresh one

**Sources**: [R1][R2]

---

## Decision Checklist

Use Git worktree when:

- [ ] **Parallel Development**: Working on multiple features simultaneously [R1]
  - Verify: You need to switch between different development contexts frequently
  - Impact: Without worktrees, you must commit/stash incomplete work or lose context

- [ ] **Urgent Hotfixes**: Need to fix production bugs while feature work is in progress
  - Verify: You have uncommitted changes you don't want to stash
  - Impact: Hotfix requires clean working directory from production branch

- [ ] **Code Review**: Checking out pull requests without disrupting current work
  - Verify: You want to test/review code in isolation
  - Impact: Switching branches would require committing or stashing current work

- [ ] **Multiple Build Configurations**: Running different builds/tests in parallel
  - Verify: Build artifacts or configuration files differ between branches
  - Impact: Branch switching would trigger full rebuilds

- [ ] **Long-running Processes**: Different worktrees can run long processes independently
  - Verify: You need to start a long process (build, test suite) and continue other work
  - Impact: Without worktrees, you must wait or use separate clones

**Decision Matrix**:

| Scenario | Use Worktree | Don't Use Worktree | Alternative |
|----------|--------------|---------------------|-------------|
| Quick branch switch for simple check | ❌ Overkill | ✅ Too simple | `git switch` or `git checkout` |
| Parallel feature development | ✅ Ideal use case | ❌ Context switching overhead | Multiple clones (more disk space) |
| Code review while working | ✅ Preserves context | ❌ Disrupts work | `git stash` (less convenient) |
| Single developer, single task | ❌ Unnecessary | ✅ Simple workflow | Standard Git workflow |
| CI/CD parallel builds | ✅ Efficient | ❌ Sequential builds slower | Separate clones per job |

---

## Anti-patterns / Pitfalls

### Anti-pattern 1: Same Branch in Multiple Worktrees

**Symptom**: Error when trying to check out a branch already checked out in another worktree.

**Why It Happens**: Developers expect worktrees to behave like separate clones.

**Impact**:
- Git prevents checking out the same branch in multiple worktrees [R1]
- Error message: "fatal: 'branch-name' is already checked out at '/path/to/worktree'"
- Can lead to confusion about worktree state

**Solution**: Use `--force` only if you understand the implications, or create a new branch from the same commit.

**Example**:

```bash
# ❌ Anti-pattern: Try to checkout same branch
git worktree add ../worktree2 main
# Error: 'main' is already checked out at '/home/user/myproject'

# ✅ Correct pattern: Create new branch or use detached HEAD
git worktree add ../worktree2 -b main-copy main
# Or for read-only work:
git worktree add --detach ../worktree2 main
```

**Sources**: [R1]

### Anti-pattern 2: Manual Directory Deletion

**Symptom**: Worktree directory deleted with `rm -rf` instead of `git worktree remove`.

**Why It Happens**: Treating worktree directories like regular folders.

**Impact**:
- Leaves orphaned metadata in `.git/worktrees/` [R1]
- `git worktree list` shows stale entries
- Can cause confusion and errors
- Wastes disk space with orphaned Git metadata

**Solution**: Always use `git worktree remove`, then run `git worktree prune` if manual deletion occurred.

**Example**:

```bash
# ❌ Anti-pattern: Manual deletion
rm -rf ../feature-login
# Metadata remains in .git/worktrees/

# ✅ Correct pattern: Use Git command
git worktree remove ../feature-login

# 🔧 Recovery: If already manually deleted
git worktree prune
# Or with dry-run first:
git worktree prune --dry-run
```

**Sources**: [R1][R2]

### Anti-pattern 3: Forgetting Worktree Locations

**Symptom**: Multiple worktrees created over time with forgotten locations.

**Why It Happens**: No naming convention or organization strategy.

**Impact**:
- Disk space wasted on forgotten worktrees
- Confusion about which worktree contains which work
- Potential for conflicting changes

**Solution**: Use consistent naming and location patterns, regular cleanup, and document active worktrees.

**Example**:

```bash
# ❌ Anti-pattern: Random locations
git worktree add ~/Desktop/fix
git worktree add /tmp/test123
git worktree add ../something

# ✅ Correct pattern: Organized structure
git worktree add ../worktrees/feature-login -b feature/login
git worktree add ../worktrees/hotfix-auth -b hotfix/auth
git worktree add ../worktrees/review-pr123 -b review/pr-123

# Regular cleanup
git worktree list
git worktree remove ../worktrees/completed-feature
```

**Sources**: [R1]

### Pitfall 4: Excessive Disk Space Usage

**Symptom**: Multiple worktrees consuming significant disk space.

**Why It Happens**: Creating many worktrees without cleanup, or keeping large build artifacts.

**Impact**:
- Each worktree contains a full working directory (not just differences)
- Build artifacts, node_modules, etc. are duplicated
- Can quickly consume available disk space

**Solution**: Regular cleanup, `.gitignore` for build artifacts, and selective worktree creation.

**Example**:

```bash
# ❌ Problem: Multiple worktrees with dependencies
# worktree1/node_modules/  (500MB)
# worktree2/node_modules/  (500MB)
# worktree3/node_modules/  (500MB)
# Total: 1.5GB for dependencies alone

# ✅ Better: Share dependencies or clean up
# Use symlinks for dependencies (advanced)
# Or clean build artifacts when switching worktrees
cd ../worktree1 && rm -rf node_modules
cd ../worktree2 && npm install

# Best: Remove unused worktrees promptly
git worktree list
git worktree remove ../worktree1
```

**Sources**: [R1]

---

## Evaluation

### Metrics

**Primary Metrics**:
- **Worktree Count**: Number of active worktrees per repository
  - Target: 2-5 active worktrees (main + linked)
  - Measurement: `git worktree list | wc -l`

- **Context Switch Time**: Time saved by avoiding branch switching
  - Target: >30 seconds saved per switch (for projects with large dependencies)
  - Measurement: Time to switch branches with rebuild vs. using worktree

**Secondary Metrics**:
- Disk space used by worktrees: `du -sh ../worktrees/*`
- Number of stale worktrees: `git worktree prune --dry-run`
- Time to create new worktree: Should be <5 seconds for most projects

### Testing Strategies

**Unit Tests**: N/A (Git feature, not code)

**Integration Tests**:
- **Parallel builds test**: Create worktrees for multiple branches, run builds simultaneously, verify no conflicts
- **Hotfix workflow test**: Start feature work, create worktree for hotfix, verify both can be worked on independently

**Performance Benchmarks**:
```bash
# Measure worktree creation time
time git worktree add ../test-worktree -b test-branch

# Measure context switching savings
time (git stash && git switch main && npm install)
# vs
time cd ../main-worktree  # Already has npm modules
```

### Success Criteria

- [ ] Can create worktrees for different branches without errors
- [ ] Can work in multiple worktrees simultaneously without conflicts
- [ ] Worktree removal properly cleans up all metadata
- [ ] No stale worktree entries after cleanup (`git worktree prune`)
- [ ] Team members understand worktree naming conventions
- [ ] Disk space usage is reasonable for team's needs

**Sources**: [R1]

---

## Update Log

### 2025-11-01
- **Initial version**: Created comprehensive Git worktree documentation
- **Added**: Core patterns for create, list, remove, lock, move, and repair operations
- **Added**: Anti-patterns section covering common mistakes
- **Added**: Decision checklist for when to use worktrees
- **Sources**: [R1][R2]

---

## See Also

### Prerequisites
- Basic Git concepts: branches, commits, working directory
- Understanding of Git repository structure (`.git` directory)

### Related Topics
- **Git Branches**: Worktrees are closely tied to branch management
- **Git Stash**: Alternative for simple context switching
- **Version Control Workflows**: Worktrees enable advanced workflows

### Advanced Topics
- **CI/CD Integration**: Using worktrees in automated build pipelines
- **Submodules with Worktrees**: Managing submodules across multiple worktrees

---

## References

- [R1] Git Project. "Git - git-worktree Documentation." Official Git Documentation. https://git-scm.com/docs/git-worktree (accessed 2025-11-01)
- [R2] The Linux Kernel Archives. "git-worktree(1)." Git Manual Pages. https://www.kernel.org/pub/software/scm/git/docs/git-worktree.html (accessed 2025-11-01)

---

**Document ID**: `system/git/worktree`
**Canonical URL**: `https://github.com/artificial-intelligence-first/ssot/blob/main/system/git/worktree.md`
**License**: MIT
