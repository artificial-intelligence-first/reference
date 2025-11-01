---
title: SSOT
slug: ssot
status: living
last_updated: 2025-11-01
tags: [ssot, governance, data-contracts]
summary: "Single Source of Truth principle for maintaining authoritative, consistent documentation and canonical definitions."
authors: []
sources:
  - { id: R1, title: "Single source of truth - Wikipedia", url: "https://en.wikipedia.org/wiki/Single_source_of_truth", accessed: "2025-10-23" }
  - { id: R2, title: "The Twelve-Factor App - III. Config", url: "https://12factor.net/config", accessed: "2025-10-23" }
  - { id: R3, title: "Documentation as Code - Write the Docs", url: "https://www.writethedocs.org/guide/docs-as-code/", accessed: "2025-10-23" }
---

# SSOT

> **For Humans**: This guide explains the Single Source of Truth principle for maintaining authoritative, consistent documentation across your project.
>
> **For AI Agents**: SSOT designates one canonical location for each dataset, document, or policy. Always reference the SSOT when encountering conflicting information. Update the SSOT first before propagating changes elsewhere.

## Glossary

### Core Terms

**SSOT (Single Source of Truth)**: The authoritative data source for a given data element or piece of information. All other occurrences of this data element should reference back to this single source.

**Canonical Definition**: The official, agreed-upon definition of a term, stored in exactly one location.

**Data Contract**: A formal agreement between producers and consumers of data that defines the structure, format, semantics, quality, and SLA.

**Governance Model**: The framework of rules, practices, and processes by which documentation is directed and controlled.

## Data Contracts

### Schema Standards

All data contracts must specify:
- **Version**: Semantic versioning (MAJOR.MINOR.PATCH)
- **Format**: JSON Schema, OpenAPI, Protocol Buffers, or GraphQL SDL
- **Validation**: Required fields, data types, constraints
- **Evolution**: Backward compatibility requirements

### Contract Locations

| Data Type | SSOT Location | Format |
|-----------|---------------|---------|
| API Schemas | `/schemas/api/` | OpenAPI 3.1 |
| Event Schemas | `/schemas/events/` | JSON Schema |
| Database Schemas | `/schemas/db/` | SQL DDL |
| Configuration | `/config/` | YAML with JSON Schema |

## Policies

### Change Management

1. **Proposal**: Submit changes via pull request with rationale
2. **Review**: Technical review by maintainers
3. **Validation**: Automated schema validation and compatibility checks
4. **Approval**: Requires 2+ maintainer approvals for breaking changes
5. **Migration**: Provide migration path for breaking changes

### Versioning Policy

- **Major**: Breaking changes to contracts or removal of fields
- **Minor**: New optional fields or backward-compatible additions
- **Patch**: Documentation fixes, clarifications, examples

### Deprecation Policy

- **Notice Period**: 90 days minimum for public APIs
- **Migration Guide**: Required for all deprecations
- **Sunset Date**: Must be specified and communicated
- **Fallback**: Maintain deprecated version for transition period

## Workflows

### Finding the SSOT

```bash
# 1. Check index file
cat SSOT_INDEX.md | grep "topic-name"

# 2. Use designated paths
ls -la /schemas/   # For data contracts
ls -la /policies/  # For governance docs
ls -la /glossary/  # For definitions

# 3. Follow references
grep -r "canonical:" . --include="*.md"
```

### Updating the SSOT

```bash
# 1. Create branch
git checkout -b update-ssot-topic

# 2. Make changes
vi /path/to/canonical/file.md

# 3. Update references
find . -name "*.md" -exec grep -l "old-reference" {} \; | xargs sed -i 's/old-reference/new-reference/g'

# 4. Submit PR
git add -A
git commit -m "docs(ssot): update topic with rationale"
git push origin update-ssot-topic
```

### Conflict Resolution

When conflicts arise between sources:

1. **Identify**: Document all conflicting sources
2. **Trace**: Find the designated SSOT location
3. **Verify**: Check last_updated and version
4. **Reconcile**: Update non-SSOT sources to match
5. **Prevent**: Add validation rules to CI/CD

## Anti-patterns

### Multiple Sources of Truth
❌ **Wrong**: Same information maintained in README.md, Wiki, and inline comments
✅ **Right**: Single location with references from other locations

### Outdated Copies
❌ **Wrong**: Copy-pasting schemas across services
✅ **Right**: Reference shared schema repository

### Implicit Truth
❌ **Wrong**: "Everyone knows that..."
✅ **Right**: Documented in designated SSOT location

## Evaluation

### Metrics

- **Uniqueness**: Each fact has exactly one authoritative source
- **Discoverability**: Time to find canonical information < 2 minutes
- **Consistency**: Zero conflicts between sources
- **Currency**: All references point to latest version
- **Compliance**: 100% of changes follow governance process

### Validation

```bash
# Run SSOT validation
./scripts/validate-ssot.sh

# Check for duplicates
./scripts/find-duplicates.sh

# Verify references
./scripts/check-references.sh
```

## See Also

- [AGENTS.md](./AGENTS.md) - AI agent operational documentation
- [SSOT Guide](./docs/core/ssot-guide.md) - Comprehensive SSOT implementation guide
- [Data Contracts](./docs/data-contracts.md) - Detailed contract specifications
- [Governance](./docs/governance.md) - Full governance framework

## References

- [R1] Single source of truth - Wikipedia. https://en.wikipedia.org/wiki/Single_source_of_truth (accessed 2025-10-23)
- [R2] The Twelve-Factor App - III. Config. https://12factor.net/config (accessed 2025-10-23)
- [R3] Documentation as Code - Write the Docs. https://www.writethedocs.org/guide/docs-as-code/ (accessed 2025-10-23)
