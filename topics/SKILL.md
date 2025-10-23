---
title: SKILL.md Guide (Agent Skills)
slug: skill
status: living
last_updated: 2025-10-23
tags: [skill, anthropic, agents, domain-expertise, progressive-disclosure]
summary: "Anthropic's Agent Skills specification for packaging domain expertise that AI agents can load on-demand."
sources:
  - { id: R1, title: "Anthropic Skills - Official Announcement", url: "https://www.anthropic.com/news/skills", accessed: "2025-10-23" }
  - { id: R2, title: "anthropics/skills - Official GitHub Repository", url: "https://github.com/anthropics/skills", accessed: "2025-10-23" }
  - { id: R3, title: "Claude Skills Documentation", url: "https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview", accessed: "2025-10-23" }
  - { id: R4, title: "Claude Skills are awesome, maybe a bigger deal than MCP - Simon Willison", url: "https://simonwillison.net/2025/Oct/16/claude-skills/", accessed: "2025-10-23" }
---

# SKILL.md Guide (Agent Skills)

> **For Humans**: This guide explains Anthropic's Agent Skills specification for packaging domain expertise that Claude can load on-demand.
>
> **For AI Agents**: Agent Skills are filesystem packages containing YAML metadata, Markdown instructions, and optional resources. Load Skills when their descriptions match user tasks to access specialized knowledge without consuming excessive context.

## Overview

Agent Skills are modular packages that provide AI agents with domain-specific knowledge, procedures, and tooling on-demand. Developed by Anthropic and **officially launched in October 2025** [R1], Skills enable:

- **Progressive disclosure** - Load expertise only when relevant to the current task
- **Context efficiency** - Keep base context lightweight while accessing deep knowledge when needed
- **Reusability** - Share domain expertise across projects and teams
- **Specialization** - Package complex workflows (document processing, financial analysis, code generation) into accessible units

**Key Innovation**: Skills deliver knowledge in a three-layer structure—metadata (about 100 tokens), instructions (several thousand tokens), and resources (unbounded as needed)—so agents can load only the information required at each stage<sup>[1](#footnote-anthropic-limits)</sup>.

### Official Skills Repository

Anthropic maintains an open-source repository at **[github.com/anthropics/skills](https://github.com/anthropics/skills)** [R2] containing:

- **Document Skills** (`document-skills/`) - Official skills for creating and manipulating document formats:
  - `.docx` (Word documents) with complex formatting
  - `.pdf` generation and manipulation
  - `.pptx` (PowerPoint presentations)
  - `.xlsx` (Excel spreadsheets) with formulas and advanced features

- **Example Skills** - Community-contributed and starter skills including [R2]:
  - `algorithmic-art` - Generate algorithmic art and creative visualizations
  - `canvas-design` - Design and layout assistance
  - `slack-gif-creator` - Create GIFs for Slack communications
  - `artifacts-builder` - Build Claude artifacts programmatically
  - `mcp-server` - MCP server development patterns
  - `webapp-testing` - Web application testing workflows
  - `brand-guidelines` - Brand consistency enforcement
  - `internal-comms` - Internal communication templates

**Availability**: Skills are available to **Claude Pro, Max, Team, and Enterprise users** [R1][R4]

**Claude Code Integration**: Skills can be installed as plugins via `/plugin install document-skills@anthropic-agent-skills` [R2]

## Skill Structure

### Directory Layout

```
my-skill/
├── SKILL.md           # Required: Instructions and procedures
└── skill.yaml         # Required: Metadata defining the skill
```

### Extended Structure (Optional)

```
my-skill/
├── SKILL.md
├── skill.yaml
├── FORMS.md           # Additional reference documentation
├── examples/          # Sample inputs/outputs
├── scripts/           # Executable helpers (Python, etc.)
└── data/             # Reference data files
```

## Core Components

### 1. skill.yaml (Required Metadata)

```yaml
name: document-forms-filler
description: |
  Fill out structured forms (tax forms, applications, contracts) using source documents.
  Use this skill when the user asks to complete forms or transfer information from documents into templates.
```

**Required Fields**:
- `name` (≤64 chars) - Unique identifier, kebab-case recommended
- `description` (≤1024 chars) - Capability statement + trigger conditions

**Critical**: The description must clearly state:
1. **What** the skill does
2. **When** to use it (trigger conditions)

### 2. SKILL.md (Required Instructions)

Main instruction document containing:
- Procedures and workflows
- Examples and patterns
- Links to additional resources
- Tool usage guidance
- Domain-specific knowledge

**Best Practice**: Keep under 5,000 tokens when possible. Claude reads this only after metadata matches.

### 3. Additional Resources (Optional)

- **Markdown files** - Extended documentation (FORMS.md, GUIDELINES.md, etc.)
- **Scripts** - Python/bash helpers that output results (not source code) to context
- **Data files** - Reference data, templates, schemas

### Security Considerations

- When adding scripts or data, omit API keys and personal information.
- Restrict execution permissions to the minimum required and verify `chmod` settings before running anything.
- If you depend on external libraries, check their licenses and vulnerability advisories.
- Keep alignment with the security policies documented in SSOT.md.

## Progressive Disclosure Model

Skills load in three tiers:

### Tier 1: Metadata (Session Start)
```yaml
name: financial-analyzer
description: Analyze financial statements and calculate key metrics...
```
- Loaded at session start (~100 tokens)
- Claude evaluates all installed Skills' metadata
- Determines which Skills are relevant to user's task

### Tier 2: Instructions (On Match)
```markdown
# Financial Analysis Workflow
1. Parse financial statements
2. Calculate ratios (P/E, ROE, debt-to-equity)
3. Generate summary report
...
```
- Loaded only when description matches task (~5k tokens)
- Provides detailed procedures and examples
- References additional resources if needed

### Tier 3: Resources (On Demand)
```python
# scripts/calculate_ratios.py
# Called via bash when needed
# Only output appears in context, not source code
```
- Accessed via bash commands when referenced
- Unlimited size (stays on disk until called)
- Scripts output results, not full source

## Creating Effective Skills

### skill.yaml Best Practices

**Good Description** ✅
```yaml
description: |
  Generate API client code from OpenAPI specifications.
  Use when the user provides an OpenAPI/Swagger file and needs client libraries in Python, TypeScript, or Go.
```

**Poor Description** ❌
```yaml
description: API code generator
```

### SKILL.md Best Practices

**Structure**:
```markdown
# [Skill Name]

## Purpose
Brief summary of what this skill accomplishes

## When to Use
Clear trigger conditions and use cases

## Prerequisites
- Required tools or dependencies
- Expected input formats
- Environment assumptions

## Procedures

### Procedure 1: [Action Name]
1. Step-by-step instructions
2. With concrete examples
3. Including expected outputs

### Procedure 2: [Action Name]
...

## Examples

### Example 1: [Scenario]
Input:
```
[sample input]
```

Process:
- Step 1...
- Step 2...

Output:
```
[expected result]
```

## Additional Resources
- [FORMS.md](FORMS.md) - Form templates and schemas
- [scripts/helper.py](scripts/helper.py) - Calculation utilities

## Troubleshooting
Common issues and solutions
```

## Platform Support

### Claude API

```python
from anthropic import Anthropic

client = Anthropic()
response = client.messages.create(
    model="claude-3-5-sonnet-latest",  # Confirm the latest version in the API docs
    max_tokens=4096,
    tools=[{
        "type": "code_execution",
        "skill_id": "my-skill"
    }],
    messages=[...]
)
# NOTE: Review the documentation for beta flags or additional options as they change.
```

### Claude Code (CLI)

**Installation**:
```bash
# User-level skills
~/.claude/skills/my-skill/

# Project-level skills
.claude/skills/my-skill/
```

**Plugin Marketplace**:
- Install curated skill packs via built-in marketplace
- Pre-built: `document-skills`, `example-skills`
- Auto-discovery of filesystem Skills

### Claude.ai (Web)

- **Pre-built Skills**: Available to all users (document processing)
- **Custom Skills**: Upload zip packages (Pro/Max/Team/Enterprise with code execution)
- **Per-user**: Skills don't sync across accounts/platforms

## Skill Development Workflow

### 1. Plan

```markdown
## Skill Concept
- Domain: Financial analysis
- Trigger: User provides financial statements
- Capabilities: Calculate ratios, generate reports
- Dependencies: Python, pandas
```

### 2. Create Structure

```bash
mkdir financial-analyzer
cd financial-analyzer
touch skill.yaml SKILL.md
mkdir scripts examples
```

### 3. Write Metadata

```yaml
name: financial-analyzer
description: |
  Analyze financial statements (balance sheet, income statement, cash flow) to calculate key financial ratios and generate summary reports.
  Use when the user provides financial data and needs ratio analysis, trend identification, or comparative metrics.
```

### 4. Write Instructions

```markdown
# Financial Analyzer

## Purpose
Calculate financial ratios and generate analysis reports from financial statements.

## Procedures

### Calculate Financial Ratios
1. Parse financial statements (accept CSV, JSON, or structured text)
2. Extract key figures: assets, liabilities, equity, revenue, net income
3. Calculate ratios:
   - Liquidity: Current ratio, Quick ratio
   - Profitability: ROE, ROA, Profit margin
   - Leverage: Debt-to-equity, Interest coverage
4. Generate summary report

### Example
[Detailed example with sample data]

## Scripts
- `scripts/calculate_ratios.py` - Ratio calculation engine
```

### 5. Add Resources

```python
# scripts/calculate_ratios.py
import sys
import json

def calculate_ratios(data):
    # Implementation
    return {
        "current_ratio": data['current_assets'] / data['current_liabilities'],
        # ... more ratios
    }

if __name__ == "__main__":
    data = json.loads(sys.argv[1])
    results = calculate_ratios(data)
    print(json.dumps(results, indent=2))
```

### 6. Test

```bash
# Test script execution
python scripts/calculate_ratios.py '{"current_assets": 1000, "current_liabilities": 500}'

# Test with Claude
# Provide financial data and verify Claude uses the skill correctly
```

### 7. Package & Distribute

```bash
# For Claude.ai
zip -r financial-analyzer.zip financial-analyzer/

# For Claude Code
cp -r financial-analyzer ~/.claude/skills/

# For team sharing
# Publish to GitHub, shared drive, or internal registry
```

## Security & Governance

### Security Considerations

⚠️ **Treat Skills like third-party software**

1. **Audit before installation**
   - Review all files, especially scripts
   - Check for unexpected network calls
   - Verify instructions align with description

2. **Malicious Skill risks**
   - Data exfiltration via network requests
   - Harmful bash commands
   - Misleading instructions that cause unintended actions

3. **Source verification**
   - Only install Skills from trusted sources
   - Verify Anthropic-official Skills via GitHub
   - Review community Skills carefully before use

### Runtime Constraints

**Code Execution Environment**:
- No outbound network access
- No ad-hoc package installation (pip install, npm install)
- Pre-installed dependencies only
- Isolated container execution

**Implications**:
- Skills must work with shipped runtime
- Cannot fetch external data at runtime
- Must bundle all required resources

### Governance Best Practices

1. **Lifecycle management**
   - Track installed Skills and versions
   - Monitor usage patterns
   - Review and update Skills regularly
   - Remove unused Skills to reduce surface area

2. **Testing before production**
   - Validate behavior in controlled environment
   - Test edge cases and error handling
   - Verify output quality and accuracy
   - Compare with Anthropic's managed Skills when available

3. **Access control**
   - Limit Skill installation to authorized users
   - Review custom Skills before team-wide deployment
   - Maintain inventory of active Skills

## Pre-Built Skills

### Anthropic Official Skills

**Document Skills** (`document-skills/`) [R2]
Available on all platforms (Claude API, Claude Code, Claude.ai):
- **Form filling** - Extract information from source documents to complete forms
- **Document transformation** - Convert between formats (.docx, .pdf, .pptx, .xlsx)
- **Structured data extraction** - Parse documents for specific data patterns
- **Complex formatting** - Handle advanced document features (formulas, layouts, styles)

**Example Skills** (GitHub repository: `anthropics/skills`) [R2]
Community-contributed and starter patterns:
- **algorithmic-art** - Algorithmic art generation
- **canvas-design** - Design layout and composition
- **slack-gif-creator** - GIF creation for communications
- **artifacts-builder** - Programmatic Claude artifact creation
- **mcp-server** - MCP server development
- **webapp-testing** - Web app testing workflows
- **brand-guidelines** - Brand consistency enforcement
- **internal-comms** - Internal communication templates

### Community Skills

- Check [Anthropic's GitHub](https://github.com/anthropics/skills) [R2] and community forums
- Third-party collections: [claude-skills-collection](https://github.com/abubakarsiddik31/claude-skills-collection)
- Always review source before installation
- Report issues and contribute improvements via Pull Requests

## Integration Patterns

### Skills + AGENTS.md

```markdown
## AGENTS.md

### Skills Usage
- Check available Skills with `/skills list`
- Use `document-forms-filler` for all form completion tasks
- Use `financial-analyzer` for ratio calculations
- Custom Skills located in `.claude/skills/`
```

### Skills + ExecPlans (PLANS.md)

```markdown
## ExecPlan: Generate API Client

### Concrete Steps
1. Load `openapi-generator` Skill
2. Provide OpenAPI spec to Skill
3. Generate TypeScript client
4. Validate generated code
```

### Skills + SSOT.md

- Define canonical terms in SSOT.md
- Reference SSOT.md from Skill instructions for consistency
- Update both when domain terminology evolves

## Examples

### Example 1: Document Form Filler

**skill.yaml**:
```yaml
name: document-forms-filler
description: |
  Fill out structured forms using information from source documents.
  Use when the user provides a blank form and source documents (PDFs, Word docs, emails) containing the information needed to complete the form.
```

**SKILL.md** (excerpt):
```markdown
# Document Forms Filler

## Procedure
1. Analyze the blank form to identify required fields
2. Extract relevant information from source documents
3. Map extracted data to form fields
4. Fill form with accurate, properly formatted data
5. Highlight any missing information

## Example
Form: W-2 Tax Form
Source: Employment records PDF

Process:
- Extract employer name, address, EIN from records
- Extract employee wages from paystubs
- Map to W-2 boxes 1-16
- Flag missing Box 12 codes (requires clarification)
```

### Example 2: Code Review Assistant

**skill.yaml**:
```yaml
name: code-reviewer
description: |
  Perform comprehensive code reviews focusing on security, performance, and best practices.
  Use when the user asks for code review, security audit, or wants feedback on implementation quality.
```

**SKILL.md** (excerpt):
```markdown
# Code Review Assistant

## Review Checklist
1. Security vulnerabilities (SQL injection, XSS, CSRF)
2. Performance issues (N+1 queries, inefficient algorithms)
3. Code style and conventions
4. Error handling and edge cases
5. Test coverage gaps

## Scripts
- `scripts/security_scan.py` - Static analysis for common vulnerabilities
- `scripts/complexity_check.py` - Cyclomatic complexity calculator
```

## Troubleshooting

### Skill Not Loading

**Check**:
1. Metadata syntax (valid YAML)
2. File naming (skill.yaml, SKILL.md exactly)
3. Directory location (correct path for platform)
4. Description clarity (Claude must understand when to trigger)

### Script Execution Fails

**Check**:
1. Script has execute permissions
2. Dependencies available in runtime
3. Script outputs to stdout (not just side effects)
4. Error messages returned to Claude

### Skill Triggered Incorrectly

**Fix**:
- Refine description to be more specific
- Add explicit trigger conditions
- Differentiate from similar Skills

## Resources

### Official Resources

- [Claude Skills Documentation](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) [R3]
- [Anthropic Skills GitHub](https://github.com/anthropics/skills) [R2] - Apache 2.0 licensed examples
- [Claude Skills Announcement](https://www.anthropic.com/news/skills) [R1] - Official product launch
- [Claude Cookbooks: Skills](https://github.com/anthropics/claude-cookbooks/tree/main/skills) - Tutorials and patterns

### Tutorials

1. [Skills Introduction Notebook](https://github.com/anthropics/claude-cookbooks/blob/main/skills/notebooks/01_skills_introduction.ipynb)
2. [Financial Applications](https://github.com/anthropics/claude-cookbooks/blob/main/skills/notebooks/02_skills_financial_applications.ipynb)
3. [Custom Skill Development](https://github.com/anthropics/claude-cookbooks/blob/main/skills/notebooks/03_skills_custom_development.ipynb)

### Blog Posts & Community

- [Equipping Agents for the Real World](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) - Engineering blog on Skills architecture
- [Claude Skills are awesome, maybe a bigger deal than MCP](https://simonwillison.net/2025/Oct/16/claude-skills/) [R4] - Community perspective by Simon Willison

### Third-Party Collections

- [simonw/claude-skills](https://github.com/simonw/claude-skills) - Contents of /mnt/skills in Claude's code interpreter
- [claude-skills-collection](https://github.com/abubakarsiddik31/claude-skills-collection) - Curated collection of official and community Skills

## Adoption Checklist

- [ ] Identify domain-specific tasks suitable for Skills
- [ ] Review pre-built Skills from Anthropic
- [ ] Plan custom Skill structure and metadata
- [ ] Create skill.yaml with clear description
- [ ] Write comprehensive SKILL.md instructions
- [ ] Add examples and test cases
- [ ] Include necessary scripts and resources
- [ ] Test Skill with various inputs
- [ ] Security review all scripts and instructions
- [ ] Document Skill in project AGENTS.md
- [ ] Train team on Skill usage
- [ ] Establish Skill update and maintenance process

## References

- [R1] [Anthropic Skills - Official Announcement](https://www.anthropic.com/news/skills) - Claude Skills product launch (October 16, 2025)
- [R2] [anthropics/skills GitHub Repository](https://github.com/anthropics/skills) - Official open-source Skills repository with document-skills and examples
- [R3] [Claude Skills Documentation](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) - Official technical documentation
- [R4] [Claude Skills are awesome, maybe a bigger deal than MCP](https://simonwillison.net/2025/Oct/16/claude-skills/) - Community analysis by Simon Willison (October 16, 2025)

## Update Log

- 2025-10-23: Added official GitHub repository (anthropics/skills), launch date (October 2025), official skills list (document-skills, example-skills), availability information (Pro/Max/Team/Enterprise), Claude Code plugin integration, third-party collections, and comprehensive references.
- 2025-10-20: Published the initial edition (reorganized from Anthropic Skills documentation and public samples).

---

**Remember**: Skills are about giving agents specialized expertise when they need it, without overwhelming every conversation. Design clear, focused Skills with precise trigger conditions and comprehensive instructions.

---

<a name="footnote-anthropic-limits">1</a>: Reference: See Anthropic's official "Agent Skills" documentation for the latest token limits.
