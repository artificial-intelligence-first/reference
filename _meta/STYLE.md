# Style Guide

> **For Humans**
> This document defines writing conventions, formatting rules, and structural patterns for all documentation in this repository.

> **For AI Agents**
> Follow these rules when creating or updating any markdown file. These conventions ensure consistency, machine-readability, and long-term maintainability.

---

## Document Structure

### Required Sections (in order)

1. **YAML Frontmatter** - Machine-readable metadata
2. **Dual-Audience Statement** - "For Humans" and "For AI Agents" blocks
3. **TL;DR** - 3-5 line executive summary
4. **Table of Contents** - Auto-generated or manual
5. **Core Content** - Organized by standard sections (see below)
6. **Update Log** - Chronological additions to this topic
7. **See Also** - Cross-references to related topics
8. **References** - Numbered citations with URLs

### Standard Content Sections

For topic documents, use these sections in order:

- **Canonical Definitions** - Precise terminology, scope, and boundaries
- **Core Patterns** - Fundamental approaches and implementations
- **Decision Checklist** - When to apply this pattern
- **Anti-patterns / Pitfalls** - Common mistakes and how to avoid them
- **Evaluation** - Metrics, testing strategies, and success criteria

## YAML Frontmatter Format

```yaml
---
title: Topic Name
slug: topic-name
status: living | stable | draft | deprecated
last_updated: YYYY-MM-DD
tags: [tag1, tag2, tag3]
summary: "One-sentence description of this document's purpose."
sources:
  - { id: R1, title: "Source Title", url: "https://...", accessed: "YYYY-MM-DD" }
  - { id: R2, title: "Another Source", url: "https://...", accessed: "YYYY-MM-DD" }
---
```

**Field Definitions:**

- `title`: Human-readable topic name
- `slug`: URL-friendly identifier (kebab-case)
- `status`:
  - `living` - Actively updated, canonical reference
  - `stable` - Mature content, infrequent updates
  - `draft` - Work in progress
  - `deprecated` - Superseded or obsolete
- `last_updated`: ISO 8601 date (YYYY-MM-DD)
- `tags`: Array of taxonomy terms (see TAXONOMY.md)
- `summary`: Single-sentence description (< 160 chars)
- `sources`: Array of reference objects with IDs used in citations

## Heading Conventions

### Hierarchy Rules

- Use ATX-style headings (`#`, `##`, `###`, etc.)
- Maximum depth: 4 levels (`####`)
- One `#` (H1) per document - matches frontmatter `title`
- Maintain consistent heading hierarchy (no skipped levels)

### Heading IDs

- **Do not change heading slugs** once published
- GitHub auto-generates slugs from heading text
- If renaming necessary, add HTML anchor for old slug:
  ```markdown
  ## New Heading Name {#old-slug}
  ```

### Heading Format

- Use Title Case for H1, H2
- Use Sentence case for H3, H4
- No trailing punctuation
- No emojis in headings (use in content if needed)

## Citation and Reference Format

### In-Text Citations

Use footnote-style references with IDs from frontmatter:

```markdown
The agent should remain stateless [R1] to enable horizontal scaling.
Multiple sources can be cited together [R2][R3].
```

### References Section

At document end, list all sources:

```markdown
## References

- [R1] Smith, J. (2024). "Stateless Agent Design." Company Blog. https://example.com/article (accessed 2025-10-23)
- [R2] OpenAI Documentation. "Agents SDK Guide." https://platform.openai.com/docs/agents (accessed 2025-10-23)
- [R3] Anthropic. "Claude Prompt Engineering." https://docs.anthropic.com/prompting (accessed 2025-10-23)
```

**Format:**
```
- [ID] Author/Organization. "Title." Publication. URL (accessed YYYY-MM-DD)
```

**Archive URLs:**
For stability, consider adding archive.org links:
```
- [R1] Original URL (accessed YYYY-MM-DD) | Archive: https://web.archive.org/...
```

## Code Blocks

### Fenced Code Blocks

Always specify language for syntax highlighting:

```markdown
​```python
def example():
    return "Always specify language"
​```
```

### Inline Code

Use backticks for:
- Function names: `calculate_score()`
- Variables: `max_tokens`
- File paths: `/path/to/file.md`
- CLI commands: `git commit`
- API endpoints: `/v1/agents`

## Links

### Internal Links (Cross-References)

Use relative paths from current file:

```markdown
See [SSOT Principles](./SSOT.md) for canonical definitions.
See [Context Engineering](../engineering/context.md) for retrieval strategies.
```

### External Links

Use reference-style links for better readability:

```markdown
See the [OpenAI documentation][openai-docs] for details.

[openai-docs]: https://platform.openai.com/docs
```

Or inline for single-use links:
```markdown
See [this article](https://example.com/article) for background.
```

## Lists

### Unordered Lists

- Use `-` (dash) for consistency
- Maintain 2-space indentation for nested items
- Use parallel structure (all items same grammatical form)

### Ordered Lists

1. Use `1.` for all items (Markdown auto-numbers)
2. Or use sequential numbers if order is critical
3. Maintain consistent punctuation (all end with period, or none)

### Task Lists

For checklists:

```markdown
- [ ] Incomplete task
- [x] Completed task
```

## Tables

### Format

Use GitHub-flavored Markdown tables:

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Value A  | Value B  | Value C  |
| Value D  | Value E  | Value F  |
```

### Alignment

```markdown
| Left-aligned | Center-aligned | Right-aligned |
|:-------------|:--------------:|--------------:|
| Text         | Text           | Text          |
```

## Admonitions and Callouts

Use blockquotes with emoji prefixes:

```markdown
> ⚠️ **Warning**: Critical information that could cause issues if ignored.

> 💡 **Tip**: Helpful suggestion or best practice.

> ℹ️ **Note**: Additional context or clarification.

> ✅ **Best Practice**: Recommended approach.

> ❌ **Anti-pattern**: Approach to avoid.
```

## Code Examples

### Minimal, Runnable Examples

Prefer complete, copy-pasteable examples:

```python
# ✅ Good: Complete, runnable example
import openai

client = openai.OpenAI(api_key="sk-...")
response = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "Hello"}]
)
print(response.choices[0].message.content)
```

```python
# ❌ Bad: Incomplete, requires context
response = client.chat.completions.create(...)
```

### Annotated Examples

Use comments to explain non-obvious parts:

```python
# Initialize client with timeout for long-running requests
client = openai.OpenAI(
    api_key=os.getenv("OPENAI_API_KEY"),
    timeout=60.0  # 60 second timeout
)
```

## Update Log Format

Reverse chronological order (newest first):

```markdown
## Update Log

### 2025-10-23
- **Added**: Evaluation metrics section with latency benchmarks [R5]
- **Updated**: Core Patterns with streaming examples [R6]
- **Fixed**: Typo in Decision Checklist

### 2025-10-15
- **Added**: Anti-patterns section with 3 common pitfalls [R3][R4]
- **Updated**: Canonical Definitions to clarify scope

### 2025-10-01
- **Initial version**: Core structure and foundational patterns [R1][R2]
```

**Change Types:**
- `Added` - New content
- `Updated` - Modified existing content
- `Removed` - Deleted content
- `Fixed` - Corrections, typos, broken links
- `Deprecated` - Marked content as obsolete

## Language and Tone

### General Principles

- **Clarity over cleverness** - Direct, unambiguous language
- **Active voice** - "The agent processes requests" not "Requests are processed"
- **Present tense** - "The function returns..." not "The function will return..."
- **Concise** - Eliminate unnecessary words
- **Objective** - Avoid subjective language ("obviously", "simply", "just")

### Technical Writing

- **Define before use** - Introduce terms before using them
- **Consistent terminology** - Use same term for same concept (check TAXONOMY.md)
- **Expand acronyms** - First use: "Natural Language Processing (NLP)"
- **Precision** - "may", "should", "must" have specific meanings (RFC 2119)

### Audience Awareness

- **For Humans**: Explain *why* and *when*, provide context
- **For AI Agents**: Specify *what* and *how*, provide exact instructions

## Formatting Conventions

### Emphasis

- **Bold** for key terms on first use
- *Italics* for emphasis or technical terms
- `Code formatting` for technical literals

### Spacing

- One blank line between sections
- Two blank lines before major headings (H2)
- No trailing whitespace
- Files end with single newline

### Line Length

- Soft wrap at 120 characters (not enforced, but preferred)
- Hard wrap for tables to maintain readability
- No line length limit for URLs

## File Naming

### Markdown Files

- Use `.md` extension
- All uppercase for canonical governance docs: `AGENTS.md`, `SSOT.md`
- Lowercase for technical guides: `context.md`, `prompt.md`
- Kebab-case for multi-word files: `agent-kit.md`, `code-generation.md`

### Asset Files

- Lowercase with kebab-case: `architecture-diagram.png`
- Include descriptive names: `agent-workflow-2025-10.svg`
- Organize by topic: `_assets/topics/agents/state-machine.png`

## Markdown Linting

All files should pass `markdownlint` with this configuration:

```json
{
  "default": true,
  "MD013": false,
  "MD033": { "allowed_elements": ["a", "img", "br", "details", "summary"] },
  "MD041": false
}
```

## Version Control

### Commit Messages

Format: `docs(scope): description`

```bash
docs(topics/agents): add evaluation metrics from OpenAI blog [R5]
docs(engineering/context): update retrieval strategies
docs(_meta): fix typo in STYLE.md
```

**Scopes:**
- `topics/*` - Topic documents
- `engineering/*` - Engineering guides
- `platforms/*` - Platform-specific docs
- `_meta` - Governance documents
- `_templates` - Templates
- `root` - README.md or repository-level files

## Maintenance

### Regular Reviews

- **Monthly**: Check References for link rot
- **Quarterly**: Review tags against TAXONOMY.md
- **Annually**: Audit `status` field (stable vs living)

### Deprecation Process

1. Update `status: deprecated` in frontmatter
2. Add deprecation notice at top of document
3. Link to replacement document
4. Retain file for historical reference (do not delete)

Example:
```markdown
> ⚠️ **Deprecated**: This document is superseded by [New Topic](./new-topic.md).
> Last updated: 2025-10-23 | Deprecated: 2025-11-01
```

---

**Document ID**: `_meta/STYLE`
**Last Updated**: 2025-10-23
**Status**: Living Document
