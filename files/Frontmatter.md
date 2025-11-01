---
title: YAML Frontmatter Guide
slug: frontmatter
status: living
last_updated: 2025-11-01
tags: [conventions, documentation, best-practices, design-patterns]
summary: "YAML frontmatter specification for adding structured metadata to Markdown documents."
sources:
  - { id: R1, title: "YAML Ain't Markup Language (YAML™) revision 1.2.2", url: "https://yaml.org/spec/1.2.2/", accessed: "2025-11-01" }
  - { id: R2, title: "Jekyll - Front Matter Official Documentation", url: "https://jekyllrb.com/docs/front-matter/", accessed: "2025-11-01" }
  - { id: R3, title: "Hugo - Front Matter Official Documentation", url: "https://gohugo.io/content-management/front-matter/", accessed: "2025-11-01" }
  - { id: R4, title: "Pandoc User Guide - YAML Metadata Blocks", url: "https://pandoc.org/MANUAL.html#metadata-blocks", accessed: "2025-11-01" }
  - { id: R5, title: "Zettlr - YAML Frontmatter Documentation", url: "https://docs.zettlr.com/en/core/yaml-frontmatter/", accessed: "2025-11-01" }
  - { id: R6, title: "GitHub Docs - YAML Front Matter Usage", url: "https://docs.github.com/en/contributing/writing-for-github-docs/using-yaml-frontmatter", accessed: "2025-11-01" }
---

# YAML Frontmatter Guide

> **For Humans**
> This document provides comprehensive guidance on YAML frontmatter, a widely-adopted convention for adding structured metadata to Markdown documents. Use this when setting up documentation systems, static site generators, or any Markdown-based content management workflows.

> **For AI Agents**
> This document defines canonical syntax, data types, and implementation patterns for YAML frontmatter across multiple platforms (Jekyll, Hugo, Pandoc). Apply these patterns when creating or validating Markdown documents with metadata. Cross-reference with SSOT.md for canonical documentation principles.

---

## Table of Contents

- [TL;DR](#tldr)
- [Canonical Definitions](#canonical-definitions)
- [Core Patterns](#core-patterns)
- [Decision Checklist](#decision-checklist)
- [Anti-patterns / Pitfalls](#anti-patterns--pitfalls)
- [Evaluation](#evaluation)
- [Platform-Specific Implementations](#platform-specific-implementations)
- [Update Log](#update-log)
- [See Also](#see-also)
- [References](#references)

---

## TL;DR

- **What**: YAML frontmatter is a metadata block at the beginning of Markdown files, delimited by triple dashes (`---`)
- **Why**: Provides machine-readable metadata for static site generators, documentation tools, and content management systems
- **When**: Use at the start of any Markdown document requiring structured metadata (titles, dates, tags, custom variables)
- **How**: Place valid YAML between `---` delimiters as the first content in the file
- **Watch out**: Never use tab characters for indentation—only spaces; ensure YAML is valid before the closing delimiter

---

## Canonical Definitions

### YAML Frontmatter

**Definition**: An authoring convention popularized by Jekyll that provides a way to add structured metadata to Markdown documents using YAML (YAML Ain't Markup Language) syntax enclosed between triple-dash delimiters [R2][R5].

**Scope**:
- **Includes**: Key-value pairs, arrays, nested objects, predefined and custom variables
- **Excludes**: Document body content, inline metadata, non-YAML formats (unless explicitly supported by the platform)
- **Format**: Must be valid YAML 1.2 specification [R1]

**Related Concepts**:
- **Similar**: Metadata blocks, document headers, properties
- **Opposite**: Inline metadata, unstructured comments
- **Contains**: YAML data types (scalars, sequences, mappings)

**Sources**: [R1][R2][R5]

### Triple-Dash Delimiter

**Definition**: The three-dash sequence (`---`) used to mark the beginning and end of a YAML frontmatter block [R2][R3][R4].

**Scope**:
- Opening delimiter must be on the first line of the file
- Closing delimiter must be on its own line
- Alternative closing: three dots (`...`) in some implementations

**Example**:
```markdown
---
title: Example Document
date: 2025-11-01
---

# Document content begins here
```

**Sources**: [R2][R3][R4]

### Metadata Block

**Definition**: In Pandoc, a YAML metadata block is a section of YAML that can occur anywhere in the document (though typically at the beginning), containing key-value pairs that describe document properties [R4].

**Scope**:
- May contain lists and objects nested arbitrarily
- All string scalars are interpreted as Markdown
- Multiple metadata blocks are allowed; later values override earlier ones

**Sources**: [R4]

---

## Core Patterns

### Pattern 1: Basic Frontmatter Structure

**Intent**: Provide essential metadata for a Markdown document in a machine-readable format.

**Context**: Any Markdown file processed by static site generators, documentation tools, or content management systems.

**Implementation**:

```yaml
---
title: Document Title
date: 2025-11-01
author: Author Name
tags: [tag1, tag2, tag3]
---
```

**Key Principles**:
- **First-line requirement**: Frontmatter must begin on the first line of the file [R2][R3]
- **Valid YAML**: Content between delimiters must conform to YAML 1.2 specification [R1]
- **Whitespace**: Use spaces for indentation, never tabs [R1][R5]
- **Consistency**: Maintain uniform indentation levels (typically 2 or 4 spaces)

**Trade-offs**:
- ✅ **Advantages**: Machine-readable, platform-agnostic, human-friendly syntax
- ⚠️ **Disadvantages**: Requires YAML knowledge, syntax errors can break processing
- 💡 **Alternatives**: TOML frontmatter (Hugo), JSON frontmatter (some generators)

**Sources**: [R1][R2][R3][R5]

### Pattern 2: Nested Structures and Arrays

**Intent**: Organize complex metadata using YAML's nested object and array capabilities.

**Context**: Documents requiring structured metadata like author information, multiple categories, or hierarchical data.

**Implementation**:

```yaml
---
title: Advanced Example
author:
  name: Jane Doe
  email: jane@example.com
  social:
    - platform: GitHub
      url: https://github.com/janedoe
    - platform: Twitter
      url: https://twitter.com/janedoe
tags:
  - documentation
  - yaml
  - metadata
categories:
  - Technical Writing
  - Best Practices
custom_data:
  priority: high
  reviewed: true
  version: 1.2.0
---
```

**Key Principles**:
- **Consistent indentation**: Each nesting level uses the same number of spaces [R1]
- **Array syntax**: Use hyphen-space (`- `) for list items or bracket notation `[item1, item2]` [R1]
- **Object notation**: Use key-value pairs with colon-space (`: `) separation [R1]
- **Type inference**: YAML automatically detects strings, numbers, booleans, and null values [R1]

**Trade-offs**:
- ✅ **Advantages**: Supports complex data structures, maintains readability
- ⚠️ **Disadvantages**: Indentation errors can cause parsing failures
- 💡 **Alternatives**: Flat key-value pairs with naming conventions (e.g., `author_name`, `author_email`)

**Sources**: [R1][R4]

### Pattern 3: Date and Time Handling

**Intent**: Standardize date and time metadata for temporal organization and filtering.

**Context**: Blog posts, documentation with publication dates, content scheduling systems.

**Implementation**:

```yaml
---
title: Time-Sensitive Content
date: 2025-11-01
publishDate: 2025-11-01T09:00:00-05:00
expiryDate: 2026-11-01
lastmod: 2025-11-01T14:30:00Z
---
```

**Key Principles**:
- **ISO 8601 format**: Use `YYYY-MM-DD` for dates, `YYYY-MM-DDTHH:MM:SSZ` for timestamps [R3]
- **Timezone specification**: Include timezone offset or UTC indicator (Z) for precise timestamps [R3]
- **Semantic naming**: Use platform-specific conventions (`publishDate`, `expiryDate` in Hugo) [R3]
- **Backward compatibility**: Simple `YYYY-MM-DD` format is universally supported [R2][R3]

**Trade-offs**:
- ✅ **Advantages**: Precise temporal control, automatic sorting and filtering
- ⚠️ **Disadvantages**: Platform-specific date variables may not transfer across systems
- 💡 **Alternatives**: Unix timestamps (less human-readable), relative dates (platform-dependent)

**Sources**: [R2][R3]

### Pattern 4: Boolean and Numeric Values

**Intent**: Use typed data for configuration flags and numeric metadata.

**Context**: Draft status, feature flags, version numbers, priority levels.

**Implementation**:

```yaml
---
title: Configuration Example
draft: false
featured: true
weight: 10
priority: 1
version: 2.5
rating: 4.8
---
```

**Key Principles**:
- **Boolean values**: Use `true` and `false` (lowercase) for maximum compatibility [R1]
- **YAML 1.2 compliance**: Only `true` and `false` are parsed as booleans (not `yes`, `no`, `on`, `off`) [R1]
- **Numeric types**: Integers and floats are automatically detected [R1]
- **Quoted strings**: Use quotes to force string interpretation of numeric-looking values [R1]

**Trade-offs**:
- ✅ **Advantages**: Type safety, no manual string-to-boolean conversion needed
- ⚠️ **Disadvantages**: YAML 1.1 vs 1.2 compatibility issues with legacy boolean syntax
- 💡 **Alternatives**: String values with conditional logic in templates

**Sources**: [R1][R3]

### Pattern 5: External YAML Files (Pandoc)

**Intent**: Maintain metadata separately from content for reusability and centralized management.

**Context**: Multi-document projects, shared metadata across files, template-based generation.

**Implementation**:

```bash
# metadata.yaml
---
author: Jane Doe
organization: Example Corp
license: MIT
...
```

```bash
# Command to merge external YAML with Markdown
pandoc document.md metadata.yaml -o output.html
```

**Key Principles**:
- **External file structure**: Must begin with `---` and end with `---` or `...` [R4]
- **Merge behavior**: External metadata is combined with inline frontmatter [R4]
- **Precedence**: Later values override earlier ones when conflicts occur [R4]
- **Command-line integration**: Pass YAML files as arguments alongside Markdown sources [R4]

**Trade-offs**:
- ✅ **Advantages**: Reusable metadata, centralized configuration, cleaner content files
- ⚠️ **Disadvantages**: Platform-specific (primarily Pandoc), requires command-line awareness
- 💡 **Alternatives**: Include directives, template inheritance, configuration files

**Sources**: [R4]

---

## Decision Checklist

Use YAML frontmatter when:

- [ ] **Requirement**: You need structured, machine-readable metadata in Markdown documents [R2][R5]
  - Verify: Static site generator or documentation tool supports YAML frontmatter
  - Impact: Without frontmatter, metadata must be embedded in content or managed externally

- [ ] **Constraint**: Your platform supports YAML 1.2 specification [R1]
  - Verify: Check tool documentation for YAML version compatibility
  - Impact: YAML 1.1 parsers may misinterpret boolean values (`yes`, `no`, `on`, `off`)

- [ ] **Goal**: Separate presentation metadata from content [R2][R5]
  - Verify: Content and metadata serve distinct purposes
  - Impact: Inline metadata pollutes content readability and searchability

- [ ] **Goal**: Enable automated content processing and templating [R2][R3]
  - Verify: You have templates or scripts that consume metadata
  - Impact: Manual processing becomes error-prone and inefficient at scale

**Decision Matrix**:

| Scenario | Use YAML Frontmatter | Don't Use Frontmatter | Alternative |
|----------|----------|----------------|-------------|
| Static site generation (Jekyll, Hugo) | ✅ Standard convention [R2][R3] | ❌ N/A | TOML/JSON (Hugo only) |
| Document conversion (Pandoc) | ✅ Flexible metadata [R4] | ⚠️ Simple documents | Command-line metadata flags |
| Version control documentation | ✅ Trackable metadata changes | ⚠️ Frequently changing data | External database |
| Simple notes without metadata | ❌ Unnecessary overhead | ✅ Clean Markdown | Filename conventions |
| Collaborative editing (non-technical) | ⚠️ Requires YAML knowledge | ✅ If users unfamiliar with syntax | GUI-based metadata editors |

---

## Anti-patterns / Pitfalls

### Anti-pattern 1: Using Tab Characters for Indentation

**Symptom**: YAML parsing errors, unexpected behavior, or file processing failures.

**Why It Happens**: Tabs are visually similar to spaces, and text editors may auto-insert tabs when indenting.

**Impact**:
- YAML specification explicitly forbids tabs for indentation [R1][R5]
- Parsing fails silently or produces cryptic error messages
- Different tab width settings cause inconsistent rendering

**Solution**: Configure your text editor to convert tabs to spaces in YAML files.

**Example**:

```yaml
# ❌ Anti-pattern: Tab characters (shown as →)
---
title:→Example
author:
→→name:→Jane Doe
---

# ✅ Correct pattern: Spaces only
---
title: Example
author:
  name: Jane Doe
---
```

**Sources**: [R1][R5]

### Anti-pattern 2: Non-YAML Field Names

**Symptom**: Metadata fields are not recognized or cause parsing errors.

**Why It Happens**: Field names that look like YAML numbers or boolean values are ambiguous.

**Impact**:
- Pandoc specifically warns against field names like `yes`, `True`, `15` [R4]
- Parser may interpret field names as values instead of keys
- Inconsistent behavior across different YAML parsers

**Solution**: Use descriptive, string-safe field names.

**Example**:

```yaml
# ❌ Anti-pattern: Ambiguous field names
---
true: This is confusing
42: Also problematic
yes: Parser interpretation varies
---

# ✅ Correct pattern: Descriptive field names
---
is_featured: true
answer_to_everything: 42
user_confirmed: yes
---
```

**Sources**: [R4]

### Anti-pattern 3: Inconsistent Date Formats

**Symptom**: Dates are not parsed correctly, sorting fails, timezone issues arise.

**Why It Happens**: Different platforms and locales use varying date conventions.

**Impact**:
- `MM/DD/YYYY` vs `DD/MM/YYYY` ambiguity
- Timezone-naive dates cause scheduling errors
- Content may publish at unexpected times

**Solution**: Always use ISO 8601 format (`YYYY-MM-DD` or `YYYY-MM-DDTHH:MM:SSZ`).

**Example**:

```yaml
# ❌ Anti-pattern: Ambiguous date formats
---
date: 03/04/2025  # March 4th or April 3rd?
time: 2pm EST     # Non-standard format
published: next Monday  # Relative date
---

# ✅ Correct pattern: ISO 8601
---
date: 2025-04-03
publishDate: 2025-04-03T14:00:00-05:00
lastmod: 2025-11-01T19:23:45Z
---
```

**Sources**: [R2][R3]

### Pitfall 4: Frontmatter Not at File Start

**Symptom**: Jekyll and most static site generators fail to process frontmatter.

**Why It Happens**: Blank lines or content before the opening `---` delimiter.

**Impact**:
- Entire frontmatter block is treated as document content [R2][R3]
- Metadata is not extracted or processed
- Default values are applied instead of specified values

**Solution**: Ensure the opening `---` is on line 1 with no preceding whitespace or content.

**Example**:

```yaml
# ❌ Anti-pattern: Content before frontmatter

This is a document about...

---
title: My Document
---

# ❌ Anti-pattern: Blank lines before frontmatter


---
title: My Document
---

# ✅ Correct pattern: Frontmatter on line 1
---
title: My Document
---

This is a document about...
```

**Sources**: [R2][R3]

### Pitfall 5: Unquoted Special Characters in Strings

**Symptom**: YAML parsing errors, truncated values, unexpected data structure.

**Why It Happens**: Characters like `:`, `#`, `[`, `]`, `{`, `}`, `|`, `>` have special meaning in YAML.

**Impact**:
- Colons (`:`) create unexpected key-value pairs
- Hashes (`#`) are interpreted as comments
- Brackets and braces trigger array/object parsing

**Solution**: Use quotes (single or double) around strings containing special characters.

**Example**:

```yaml
# ❌ Anti-pattern: Unquoted special characters
---
title: Introduction: A Guide  # Colon creates a key
description: Use #hashtags  # Hash truncates value
url: https://example.com/{id}  # Braces cause issues
---

# ✅ Correct pattern: Quoted strings
---
title: "Introduction: A Guide"
description: "Use #hashtags"
url: "https://example.com/{id}"
---
```

**Sources**: [R1]

---

## Evaluation

### Metrics

**Primary Metrics**:
- **Parse Success Rate**: Percentage of files with valid frontmatter that parse without errors
  - Target: 100% for production content
  - Measurement: Run YAML linter or parser on all files, count failures

- **Schema Compliance**: Percentage of required fields present in frontmatter
  - Target: 100% for mandatory fields (e.g., `title`, `date`)
  - Measurement: Validate against JSON Schema or custom validation script

**Secondary Metrics**:
- **Indentation Consistency**: No tab characters, uniform space count
- **Date Format Compliance**: ISO 8601 format usage
- **Boolean Accuracy**: Only `true`/`false` used (no `yes`/`no`)

### Testing Strategies

**Unit Tests**:
```python
import yaml

def test_frontmatter_parsing():
    """Verify frontmatter parses correctly."""
    content = """---
title: Test Document
date: 2025-11-01
draft: false
---
"""
    metadata = yaml.safe_load(content.split('---')[1])
    assert metadata['title'] == "Test Document"
    assert metadata['date'].year == 2025
    assert metadata['draft'] is False
```

**Integration Tests**:
- **Jekyll build test**: Verify site builds without frontmatter errors
- **Hugo validation**: Check for undefined frontmatter variables in templates
- **Pandoc conversion**: Ensure metadata flows through to output formats

**Validation Scripts**:
```bash
# Validate all Markdown frontmatter
for file in **/*.md; do
  python -c "
import yaml, sys
with open('$file') as f:
  content = f.read()
  if content.startswith('---'):
    try:
      yaml.safe_load(content.split('---', 2)[1])
    except yaml.YAMLError as e:
      print(f'Error in $file: {e}')
      sys.exit(1)
  "
done
```

### Success Criteria

- [ ] All production Markdown files parse without YAML errors
- [ ] Required frontmatter fields are present in 100% of applicable files
- [ ] Date fields use ISO 8601 format consistently
- [ ] No tab characters in any frontmatter blocks
- [ ] Build/generation process completes without frontmatter warnings

**Sources**: [R1][R2][R3]

---

## Platform-Specific Implementations

### Jekyll

**Predefined Variables** [R2]:
- `layout`: Template to use for rendering
- `permalink`: Custom URL pattern for the page
- `published`: Whether to include in site build (boolean)
- `date`: Post date (affects sorting and URL)
- `category` / `categories`: Taxonomy classification
- `tags`: Array of tags for the content

**Custom Variables**: Any additional key-value pairs are accessible in templates via `page.*` variables [R2].

**Example**:
```yaml
---
layout: post
title: "Getting Started with Jekyll"
date: 2025-11-01
categories: [tutorials, web-development]
tags: [jekyll, static-sites, ruby]
author: Jane Doe
---
```

### Hugo

**Predefined Variables** [R3]:
- `title`: Content title
- `date`: Creation date
- `draft`: Build exclusion flag (requires `--buildDrafts` to render)
- `publishDate`: Scheduled publish time (requires `--buildFuture`)
- `expiryDate`: Content expiration (requires `--buildExpired`)
- `description`: Page description (meta tag)
- `aliases`: URL redirects from old paths
- `weight`: Ordering within a section
- `cascade`: Frontmatter values inherited by descendants
- `isCJKLanguage`: CJK language detection for word count

**Supported Formats**: YAML (`---`), TOML (`+++`), JSON (`{ }`) [R3]

**Example**:
```yaml
---
title: "Hugo Tutorial Series"
date: 2025-11-01T10:00:00-05:00
publishDate: 2025-11-01T10:00:00-05:00
draft: false
description: "Comprehensive guide to Hugo static site generator"
tags: [hugo, go, static-sites]
weight: 10
cascade:
  type: docs
  sidebar: true
---
```

### Pandoc

**Flexible Placement**: Metadata blocks can appear anywhere in the document (not just at the start), but must be preceded by a blank line if not at the beginning [R4].

**Multiple Blocks**: Documents may contain multiple YAML metadata blocks; later values override earlier ones [R4].

**External Files**: Metadata can be kept in separate YAML files and passed as command-line arguments [R4].

**String Interpretation**: All string scalars are interpreted as Markdown [R4].

**Example**:
```yaml
---
title: Research Paper
author:
  - name: Jane Doe
    affiliation: University of Example
  - name: John Smith
    affiliation: Example Institute
abstract: |
  This is a multi-line abstract.
  It preserves line breaks and formatting.
keywords: [research, methodology, analysis]
bibliography: references.bib
---
```

**Command-line usage**:
```bash
pandoc article.md metadata.yaml --citeproc -o output.pdf
```

---

## Update Log

### 2025-11-01
- **Initial version**: Comprehensive YAML frontmatter specification and patterns
- **Added**: Core patterns covering basic structure, nested objects, dates, booleans, and external files
- **Added**: Anti-patterns section with 5 common pitfalls and solutions
- **Added**: Platform-specific implementations for Jekyll, Hugo, and Pandoc
- **Added**: Evaluation metrics and testing strategies
- **Sources**: [R1][R2][R3][R4][R5][R6]

---

## See Also

### Prerequisites
- [YAML Specification Index](https://yaml.org/spec/) - Foundation for understanding YAML syntax

### Related Topics
- [SSOT.md](./SSOT.md) - Single Source of Truth principles for canonical documentation
- [STYLE.md](../_meta/STYLE.md) - Writing conventions including frontmatter format for this repository
- [CONTRIBUTING.md](../_meta/CONTRIBUTING.md) - How to add and update documentation with proper frontmatter

### Advanced Topics
- [TAXONOMY.md](../_meta/TAXONOMY.md) - Controlled vocabulary for tags used in frontmatter
- [CHANGELOG.md](./CHANGELOG.md) - Version history format and metadata

### Platform-Specific
- [Jekyll Documentation](https://jekyllrb.com/docs/) - Jekyll-specific frontmatter usage
- [Hugo Documentation](https://gohugo.io/content-management/front-matter/) - Hugo frontmatter reference
- [Pandoc Manual](https://pandoc.org/MANUAL.html) - Pandoc metadata blocks

---

## References

- [R1] YAML.org. "YAML Ain't Markup Language (YAML™) revision 1.2.2." https://yaml.org/spec/1.2.2/ (accessed 2025-11-01)
- [R2] Jekyll. "Front Matter Official Documentation." https://jekyllrb.com/docs/front-matter/ (accessed 2025-11-01)
- [R3] Hugo. "Front Matter Official Documentation." https://gohugo.io/content-management/front-matter/ (accessed 2025-11-01)
- [R4] Pandoc. "Pandoc User Guide - YAML Metadata Blocks." https://pandoc.org/MANUAL.html#metadata-blocks (accessed 2025-11-01)
- [R5] Zettlr. "YAML Frontmatter Documentation." https://docs.zettlr.com/en/core/yaml-frontmatter/ (accessed 2025-11-01)
- [R6] GitHub Docs. "Using YAML frontmatter." https://docs.github.com/en/contributing/writing-for-github-docs/using-yaml-frontmatter (accessed 2025-11-01)

---

**Document ID**: `files/Frontmatter`
**Canonical URL**: `https://github.com/artificial-intelligence-first/ssot/blob/main/files/Frontmatter.md`
**License**: MIT
