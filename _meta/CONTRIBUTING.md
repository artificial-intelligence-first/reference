# Contributing Guide

> **For Humans**
> This guide explains how to add, update, and maintain knowledge in this repository. Whether you're a human contributor or coordinating with an AI agent, follow these patterns to ensure consistency.

> **For AI Agents**
> When requested to update this repository, follow the workflows and templates defined here. Validate all changes against STYLE.md and TAXONOMY.md before committing.

---

## Table of Contents

- [Philosophy](#philosophy)
- [Adding New Information](#adding-new-information)
- [Updating Existing Topics](#updating-existing-topics)
- [Creating New Topics](#creating-new-topics)
- [Working with AI Agents](#working-with-ai-agents)
- [Quality Checklist](#quality-checklist)
- [Maintenance Workflows](#maintenance-workflows)

---

## Philosophy

This repository follows an **Evergreen Note** approach:

- **1 Topic = 1 File**: Each markdown file is a living document on a single topic
- **Continuous Growth**: New information is integrated into existing documents, not appended
- **Source Attribution**: Every claim links to an authoritative source
- **Progressive Disclosure**: Information is organized from essential to detailed
- **Machine & Human Readable**: Structured for both AI parsing and human comprehension

**Key Principle**: We don't create new files for every new piece of information. Instead, we **enrich existing topics** by integrating new knowledge into the appropriate sections.

---

## Adding New Information

### Workflow: Human-Initiated

1. **Identify the information source** (article, documentation, research paper)
2. **Determine the relevant topic file** (e.g., `topics/AGENTS.md`)
3. **Extract key insights** from the source
4. **Assign a reference ID** (next available [R#])
5. **Integrate into appropriate section** (see Section Mapping below)
6. **Add to References** section with full citation
7. **Update frontmatter** (`last_updated`, add source to `sources` array)
8. **Add to Update Log** with one-line summary
9. **Commit** with descriptive message

### Workflow: AI-Assisted

Provide the AI agent with a structured request:

```markdown
Topic: topics/AGENTS.md
Source: https://example.com/article-title
Key Points:
- Agents should maintain state externally for scalability
- Redis is recommended for session storage
- Include code example of state management

Instructions:
1. Read the source URL
2. Extract relevant information
3. Integrate into "Core Patterns" section
4. Add reference [R#] with proper citation
5. Update frontmatter and Update Log
6. Follow STYLE.md conventions
```

The AI will:
- Analyze the source
- Determine the best section for integration
- Check for conflicts with existing content
- Format according to STYLE.md
- Assign the next available reference ID
- Update all required metadata

---

## Updating Existing Topics

### Section Mapping

Each topic uses a standard structure. Map new information to the appropriate section:

| Information Type | Target Section | Example |
|------------------|----------------|---------|
| Definitions, terminology, scope | Canonical Definitions | "What is an agent?" |
| Core approaches, implementations | Core Patterns | "Stateless agent design" |
| When to apply this pattern | Decision Checklist | "Use when horizontal scaling needed" |
| Common mistakes, pitfalls | Anti-patterns / Pitfalls | "Don't store state in memory" |
| Metrics, testing strategies | Evaluation | "Measure latency at p95" |
| Background, motivation | TL;DR or new subsection | "Why stateless matters" |

### Integration Guidelines

**✅ DO:**
- **Merge with existing content** if the concept is already covered
- **Expand existing bullet points** rather than adding redundant ones
- **Cite multiple sources** for the same point `[R1][R2]`
- **Update definitions** if newer information is more precise
- **Add nuance** to existing patterns (e.g., "However, in high-latency scenarios..." [R5])

**❌ DON'T:**
- Create duplicate sections with similar names
- Add contradictory information without reconciliation
- Append information at the end without organizing by section
- Leave orphaned reference IDs
- Update content without updating `last_updated` in frontmatter

### Handling Conflicts

If new information contradicts existing content:

1. **Evaluate source authority** - Official docs > research papers > blog posts
2. **Check recency** - Newer information may supersede older practices
3. **Consider context** - Both may be valid in different scenarios
4. **Document the nuance**:

```markdown
### Pattern: State Management

**Approach A: In-Memory State** [R1]
- Suitable for single-instance deployments
- Faster access, lower latency
- Limited by instance memory

**Approach B: External State Store** [R5]
- Required for horizontal scaling
- Slight latency overhead
- Recommended for production systems

**Decision Guide**:
- Development/testing → Approach A
- Production with scaling requirements → Approach B
```

---

## Creating New Topics

### When to Create a New Topic

Create a new topic file when:
- The concept is distinct and substantial (>2000 words potential)
- It will be independently referenced by multiple other topics
- It represents a major category (like AGENTS, SSOT, SKILL)
- It addresses a different audience or use case

**Don't create** a new topic for:
- Sub-patterns that belong in an existing topic
- Platform-specific implementations (use `platforms/` directory)
- Minor variations of existing patterns

### New Topic Checklist

- [ ] Copy `_templates/TOPIC_TEMPLATE.md`
- [ ] Fill in all frontmatter fields
- [ ] Write TL;DR (3-5 lines)
- [ ] Complete Canonical Definitions section
- [ ] Add at least one Core Pattern
- [ ] Create initial References section
- [ ] Add to parent directory README.md
- [ ] Link from related topics
- [ ] Add tags to TAXONOMY.md if needed
- [ ] Commit with message: `docs(topics): add new topic 'name'`

### Directory Placement

| Topic Type | Directory | Example |
|------------|-----------|---------|
| Core conventions/patterns | `topics/` | AGENTS.md, SSOT.md |
| Engineering methodology | `engineering/` | context.md, prompt.md |
| Platform-specific guides | `platforms/{vendor}/` | openai/agents-sdk.md |
| Cross-cutting patterns | `topics/` or new category | rate-limiting.md |

---

## Working with AI Agents

### Effective AI Prompts

**Template for Adding Information:**

```markdown
Please update {file_path} with the following information:

**Source**: {URL}
**Section**: {target section name}
**Key Points**:
- {point 1}
- {point 2}

**Instructions**:
1. Read and analyze the source
2. Extract relevant information
3. Integrate into the {section} section
4. Maintain existing structure and style (see _meta/STYLE.md)
5. Assign next available reference ID [R#]
6. Add full citation to References section
7. Update frontmatter: last_updated, add to sources array
8. Add one-line summary to Update Log
9. Preserve all existing content
10. Do not modify heading slugs

**Validation**:
- Verify no duplicate content
- Check for conflicts with existing information
- Ensure tags are from TAXONOMY.md
- Confirm citation format matches STYLE.md
```

**Template for Creating New Topic:**

```markdown
Please create a new topic file: {directory}/{filename}.md

**Topic**: {topic name}
**Summary**: {one-sentence description}
**Initial Sources**:
- {URL 1}
- {URL 2}

**Instructions**:
1. Copy structure from _templates/TOPIC_TEMPLATE.md
2. Fill in frontmatter with appropriate tags (check TAXONOMY.md)
3. Write TL;DR summarizing key points
4. Create Canonical Definitions section with precise terminology
5. Add Core Patterns from sources
6. Include at least one code example if applicable
7. Add all sources to References with proper citations
8. Update {directory}/README.md to include this new topic
9. Link from related topics (see "Related Topics" section)

**Style**: Follow _meta/STYLE.md conventions
```

### AI Agent Guardrails

When working with AI agents, enforce these constraints:

1. **Always reference STYLE.md** before making changes
2. **Validate tags** against TAXONOMY.md
3. **Preserve existing heading slugs** (no renames without HTML anchors)
4. **Never delete content** without explicit instruction
5. **Always update metadata** (frontmatter, Update Log)
6. **Verify links** after adding references
7. **Check for duplicates** before adding new patterns
8. **Maintain section order** as defined in STYLE.md

### AI Review Checklist

After AI makes changes, verify:

- [ ] Frontmatter updated (`last_updated`, `sources`)
- [ ] New content is in appropriate section
- [ ] Reference IDs are sequential and unique
- [ ] Citations follow format: `[ID] Author. "Title." URL (accessed YYYY-MM-DD)`
- [ ] Update Log has entry for this change
- [ ] No duplicate headings or sections
- [ ] Tags are canonical (from TAXONOMY.md)
- [ ] Links are valid and relative paths are correct
- [ ] Code examples are complete and runnable
- [ ] No existing content was accidentally removed

---

## Quality Checklist

### Before Committing

- [ ] **Content Quality**
  - [ ] Information is accurate and sourced
  - [ ] Language is clear and concise
  - [ ] Examples are relevant and tested
  - [ ] No typos or grammatical errors

- [ ] **Structure**
  - [ ] Sections are in standard order
  - [ ] Headings follow hierarchy (no skipped levels)
  - [ ] Lists use consistent formatting
  - [ ] Code blocks specify language

- [ ] **Metadata**
  - [ ] Frontmatter is complete and valid
  - [ ] Tags are from TAXONOMY.md
  - [ ] `last_updated` is today's date
  - [ ] Update Log has new entry

- [ ] **References**
  - [ ] All in-text citations have corresponding References entries
  - [ ] URLs are accessible
  - [ ] Citation format is consistent
  - [ ] Access dates are included

- [ ] **Cross-References**
  - [ ] Links to related topics are present
  - [ ] Relative paths are correct
  - [ ] No broken internal links

### Automated Validation

Run these checks before committing:

```bash
# Markdown linting
npx markdownlint-cli2 "**/*.md"

# Link checking
npx markdown-link-check topics/*.md engineering/*.md

# Tag validation
./scripts/validate-tags.sh

# TOC generation (if needed)
npx doctoc --title "## Table of Contents" topics/*.md
```

---

## Maintenance Workflows

### Monthly: Link Health Check

```bash
# Check all external links
npx markdown-link-check $(find . -name "*.md")

# Update broken links in References sections
# Consider adding archive.org URLs for stability
```

### Quarterly: Content Audit

1. **Review tags** against TAXONOMY.md
   - Remove deprecated tags from documents
   - Add new tags if patterns emerge

2. **Check source freshness**
   - Identify sources accessed >6 months ago
   - Verify they're still relevant and available
   - Update or add archive links

3. **Evaluate document status**
   - Promote `draft` → `living` if mature
   - Mark `living` → `stable` if rarely updated
   - Flag candidates for `deprecated` if superseded

4. **Consolidate duplicates**
   - Search for overlapping content
   - Merge and redirect if appropriate

### Annually: Major Refactoring

1. **Directory structure review**
   - Are topics in the right categories?
   - Should any be split or merged?
   - Is taxonomy still serving its purpose?

2. **Template updates**
   - Has the standard structure evolved?
   - Update TOPIC_TEMPLATE.md if needed
   - Backport improvements to existing docs

3. **Automation improvements**
   - Add new GitHub Actions workflows
   - Update linting rules
   - Improve scripts

---

## Common Scenarios

### Scenario 1: Adding a Blog Post Reference

```markdown
**User Request:**
"Add this blog post to AGENTS.md: https://example.com/stateless-agents"

**AI Workflow:**
1. Fetch and analyze blog post
2. Extract key insights:
   - Stateless design principle
   - Redis for session storage
   - Code example provided
3. Integrate into "Core Patterns" section
4. Assign reference [R7]
5. Add to References:
   [R7] Smith, J. "Stateless Agent Design." Example Blog. https://... (accessed 2025-10-23)
6. Update frontmatter sources array
7. Add to Update Log:
   - **Added**: Stateless design pattern with Redis example [R7]
8. Commit: docs(topics/agents): add stateless pattern from Example Blog [R7]
```

### Scenario 2: Conflicting Information

```markdown
**Situation:**
Existing: "Always use in-memory storage for agent state" [R1]
New source: "Use external state stores for production" [R7]

**Resolution:**
1. Don't delete existing content
2. Add context to distinguish use cases:

### Pattern: State Storage

**Development/Testing: In-Memory Storage** [R1]
- Fast and simple
- Sufficient for single-instance testing
- Not suitable for production scaling

**Production: External State Store** [R7]
- Required for horizontal scaling
- Adds latency overhead (~10ms)
- Recommended: Redis, DynamoDB, or Memcached

**Migration Path**: Start with in-memory for prototyping, migrate to external store before production deployment.
```

### Scenario 3: Creating a New Section

```markdown
**Situation:**
New information doesn't fit existing sections

**Decision:**
1. Check if it's substantial enough (>500 words)
2. Determine if it's a top-level concept or subsection
3. If top-level, add new H2 heading (following standard sections)
4. If subsection, add H3 under relevant H2
5. Update Table of Contents
6. Add to Update Log
```

---

## Getting Help

### Questions About Contributing

- Check `_meta/STYLE.md` for formatting questions
- Check `_meta/TAXONOMY.md` for tag questions
- Check existing topics for structural examples
- Open an issue using `.github/ISSUE_TEMPLATE/topic_update.md`

### Reporting Issues

- **Broken links**: Use issue template or fix directly
- **Outdated information**: Provide new source and suggest update
- **Missing topics**: Use `.github/ISSUE_TEMPLATE/new_topic.md`
- **Structural improvements**: Discuss in issue before making changes

---

## Commit Message Format

Follow conventional commits:

```
docs(scope): description

[optional body]

[optional footer]
```

**Types:**
- `docs`: Documentation changes
- `feat`: New topic or major section
- `fix`: Corrections, broken links
- `refactor`: Restructuring without content changes
- `chore`: Maintenance, automation updates

**Scopes:**
- `topics/agents`, `topics/ssot`, etc.
- `engineering/context`, `engineering/prompt`
- `platforms/openai/*`, `platforms/anthropic/*`
- `_meta`, `_templates`
- `root` for README.md

**Examples:**
```bash
docs(topics/agents): add stateless design pattern [R7]
docs(engineering/context): update retrieval strategies with Anthropic guidance
fix(topics/ssot): correct broken link in references
feat(topics): add new observability.md topic
docs(_meta/taxonomy): add 'observability' tag
```

---

## Update Log

### 2025-10-23
- **Initial version**: Established contributing workflows and AI interaction patterns
- **Added**: Section mapping guide for topic updates
- **Added**: AI prompt templates for common scenarios
- **Added**: Quality checklist and validation procedures

---

**Document ID**: `_meta/CONTRIBUTING`
**Last Updated**: 2025-10-23
**Status**: Living Document
