---
title: Prompt Engineering for AI-First Development
slug: prompt-engineering
status: living
last_updated: 2025-10-23
tags: [prompt-engineering, prompting, ai-first, best-practices]
summary: "Cross-platform prompt engineering best practices from Anthropic, Google, and OpenAI for reliable AI outputs."
sources:
  - { id: R1, title: "Anthropic Prompt Engineering Overview", url: "https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview", accessed: "2025-10-20" }
  - { id: R2, title: "Anthropic Prompt Engineering Interactive Tutorial", url: "https://github.com/anthropics/prompt-eng-interactive-tutorial", accessed: "2025-10-20" }
  - { id: R3, title: "Google Vertex AI Prompt Design", url: "https://cloud.google.com/vertex-ai/generative-ai/docs/learn/prompts/introduction-prompt-design", accessed: "2025-10-20" }
  - { id: R4, title: "DeepMind Prompting Research", url: "https://deepmind.google/research/publications/83400/", accessed: "2025-10-20" }
  - { id: R5, title: "DeepMind Prompting Research", url: "https://deepmind.google/research/publications/90773/", accessed: "2025-10-20" }
  - { id: R6, title: "OpenAI Prompt Engineering Guide", url: "https://platform.openai.com/docs/guides/prompt-engineering", accessed: "2025-10-20" }
---

# Prompt Engineering for AI-First Development

> **For Humans**: This guide consolidates prompt engineering best practices from major AI platforms (Anthropic, Google, OpenAI). Use it to design effective prompts that produce reliable, high-quality outputs from AI coding assistants.
>
> **For AI Agents**: Review this guide to understand how humans should structure prompts for you, and to recognize patterns that lead to better collaboration. When generating documentation or examples, follow these established patterns.

## Overview

Prompt engineering is the practice of crafting inputs to AI models that reliably produce desired outputs. This document synthesizes guidance from Anthropic Claude, Google Gemini/PaLM, and OpenAI GPT/Codex to provide platform-agnostic best practices while highlighting vendor-specific techniques.

**Key Philosophy**: Start with prompt iteration before considering fine-tuning. Most behaviors can be controlled through careful prompt design.

## Universal Principles

These principles apply across all major AI platforms:

### 1. Clarity and Specificity

- **Be explicit** - State exactly what you want, don't rely on implicit understanding
- **Provide concrete examples** - Show the model what good outputs look like
- **Define constraints upfront** - List allowed/disallowed actions, formatting requirements, and safety rules
- **Specify output format** - Describe structure, style, and level of detail expected

### 2. Context and Instructions

Effective prompts typically include:

1. **Role or persona** (optional) - "You are an expert Python developer..."
2. **Task description** - Clear statement of what needs to be done
3. **Context and background** - Relevant information the model needs
4. **Examples** (when helpful) - Demonstrations of desired behavior
5. **Output specification** - Format, length, style requirements
6. **Constraints and guardrails** - What to avoid, safety considerations

### 3. Iterative Refinement

- Start with simple prompts and add complexity as needed
- Test with varied inputs to identify edge cases
- Log intermediate outputs to observe behavior shifts
- Capture successful variants for reuse and sharing

## Platform-Specific Techniques

### Anthropic Claude

**Core Strengths**: Long context, structured reasoning, safety-conscious

**Best Practices**:
- **Iterative prompt design** - Anthropic strongly encourages prompt iteration over fine-tuning
- **Separate context blocks** - Use XML-style tags to delineate different information types
- **Explicit instructions** - Place clear task instructions, often at the end after context
- **Guardrails in prompt** - Include safety guidelines and prohibited content directly
- **Interactive tutorials** - Anthropic provides hands-on exercises for prompt refinement
- **Chain-of-thought scaffolding** - Ask Claude to think step-by-step for complex reasoning

**Example Structure**:
```markdown
<context>
[Background information]
</context>

<examples>
[Demonstrations of desired output]
</examples>

<instructions>
Given the context above, please [specific task].
Follow these guidelines:
- [Guideline 1]
- [Guideline 2]
Do not:
- [Prohibited action 1]
- [Prohibited action 2]
</instructions>
```

**Resources**:
- [Claude Prompt Engineering Overview](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview)
- [Interactive Tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial)

### Google Gemini / Vertex AI

**Core Strengths**: Multimodal capabilities, advanced reasoning, research-backed techniques

**Best Practices**:
- **Structured components** - Organize prompts with clear sections: instructions, context, output specs, examples
- **Multimodal prompts** - Combine text, images, and other modalities when appropriate
- **Chain-of-thought prompting** - Leverage DeepMind research on structured problem-solving
- **Self-consistency techniques** - Generate multiple reasoning paths and select the most consistent answer
- **Prompt optimization tools** - Use Vertex AI features for systematic prompt improvement
- **Testing with varied inputs** - Evaluate prompts across diverse scenarios and capture metrics

**Advanced Techniques (from DeepMind Research)**:
- **Few-shot learning** - Provide examples to guide behavior without fine-tuning
- **Prompt tuning** - Optimize prompt components systematically
- **Reasoning chains** - Break complex problems into intermediate steps

**Resources**:
- [Vertex AI Prompt Design Introduction](https://cloud.google.com/vertex-ai/generative-ai/docs/learn/prompts/introduction-prompt-design)
- DeepMind Research: Papers 83400, 90773 (advanced prompting methods, chain-of-thought)

### OpenAI GPT / Codex

**Core Strengths**: Code generation, broad knowledge, extensive tooling ecosystem

**Best Practices**:
- **Point to concrete code** - Name files, modules, identifiers; paste relevant snippets
- **State verification upfront** - Specify exact commands (pytest, npm test) and success criteria
- **Declare constraints early** - List allowed/disallowed files, tools, formatting rules at the start
- **Break work into steps** - Plan → implement → test in small, manageable chunks
- **Use logs and traces directly** - Paste full error output for accurate debugging
- **Keep prompts tight** - GPT-5 Codex responds best to concise instructions; avoid excessive preambles
- **Avoid forced prefixes** - Let the model start solutions immediately; forced prefaces may truncate output
- **Limit tool exposure** - Expose only required tools to reduce misfires
- **Tune reasoning sparingly** - Default reasoning is fast; increase only for complex problems

**Codex-Specific Patterns**:
- **Mix in exploratory prompts** - Occasionally request cleanup, refactoring, or doc improvements
- **Provide repro steps** - Include exact commands to reproduce issues
- **Name the verification** - Be explicit about how to validate the solution

**Example for Code Tasks**:
```markdown
File: src/utils/validator.ts
Issue: Function validateEmail returns false for valid emails with + symbols

Stack trace:
[paste full trace]

Constraints:
- Do not modify other files in src/utils/
- Must maintain backward compatibility
- Run `npm test` to verify

Steps:
1. Review validateEmail implementation
2. Fix regex to handle + in email addresses
3. Add test case for emails with + symbols
4. Verify all tests pass
```

**Resources**:
- [OpenAI Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering)

## Prompt Patterns and Templates

### Pattern: Chain-of-Thought

Ask the model to reason step-by-step before providing final answer.

```markdown
Please solve this problem step by step:
1. First, analyze the requirements
2. Then, identify potential approaches
3. Next, evaluate trade-offs
4. Finally, recommend a solution with justification
```

**When to use**: Complex reasoning, multi-step problems, debugging

### Pattern: Few-Shot Learning

Provide examples of input-output pairs to demonstrate desired behavior.

```markdown
Convert natural language to SQL queries.

Example 1:
Input: Show me all users who signed up last week
Output: SELECT * FROM users WHERE signup_date >= NOW() - INTERVAL '7 days'

Example 2:
Input: Count active subscriptions by plan type
Output: SELECT plan_type, COUNT(*) FROM subscriptions WHERE status = 'active' GROUP BY plan_type

Now convert this:
Input: [your query]
Output:
```

**When to use**: Specific formatting, domain-specific transformations, style consistency

### Pattern: Role-Based Prompting

Assign the model a specific role or expertise.

```markdown
You are a senior security engineer reviewing code for vulnerabilities.
Analyze the following code and identify potential security issues:

[code here]

For each issue found, provide:
- Severity (Critical/High/Medium/Low)
- Description of the vulnerability
- Recommended fix
```

**When to use**: Specialized analysis, adopting particular perspectives, focused expertise

### Pattern: Constraint-First

Lead with constraints and requirements before the task.

```markdown
Constraints:
- Must use Python 3.9+
- No external dependencies beyond standard library
- Maximum 50 lines of code
- Must include type hints
- Must handle edge case: empty input

Task: Write a function to validate email addresses
```

**When to use**: Code generation, adherence to specific requirements, safety-critical tasks

### Pattern: Progressive Elaboration

Start simple, then add complexity in stages.

```markdown
Step 1: Write a basic HTTP server that responds to GET requests
[review output]

Step 2: Add POST request handling with JSON parsing
[review output]

Step 3: Add error handling and input validation
[review output]

Step 4: Add logging and monitoring
```

**When to use**: Complex implementations, learning/teaching, incremental validation

## Testing and Evaluation

### Test Your Prompts

Effective prompt engineering requires systematic testing:

1. **Edge cases** - Test with unusual, extreme, or boundary inputs
2. **Varied contexts** - Try different scenarios and use cases
3. **Consistency checks** - Run same prompt multiple times to verify reproducibility
4. **Ablation testing** - Remove prompt components to understand their impact
5. **Cross-model validation** - Test across different models/versions when possible

### Evaluation Criteria

Assess prompts based on:

- **Accuracy** - Does output match expected results?
- **Consistency** - Do similar inputs produce similar outputs?
- **Completeness** - Does output address all aspects of the prompt?
- **Safety** - Does output avoid harmful, biased, or prohibited content?
- **Efficiency** - Does prompt use tokens effectively?
- **Maintainability** - Is prompt clear enough for others to understand and modify?

### Logging and Observability

For production prompts:
- Log prompt versions alongside outputs
- Track performance metrics (latency, token usage, success rate)
- Capture user feedback and corrections
- Monitor for prompt injection attempts
- Version control prompt templates

## Advanced Techniques

### Prompt Chaining

Break complex tasks into multiple prompts with intermediate steps.

```markdown
Prompt 1: Analyze requirements → Output: requirement list
Prompt 2: Design solution using requirements → Output: design doc
Prompt 3: Implement design → Output: code
Prompt 4: Generate tests for code → Output: test suite
```

**Benefits**: Better control, easier debugging, reusable components

### Meta-Prompting

Ask the model to improve or critique prompts.

```markdown
Here's a prompt I'm using:
"[original prompt]"

Please suggest improvements to make it:
1. More specific and clear
2. Less ambiguous
3. More likely to produce consistent outputs
```

**Benefits**: Discover blind spots, optimize clarity, learn prompt patterns

### Negative Prompting

Explicitly state what NOT to do.

```markdown
Write a user authentication function.

Do NOT:
- Store passwords in plain text
- Use deprecated crypto algorithms
- Skip input validation
- Return detailed error messages to users
```

**Benefits**: Prevent common mistakes, enforce safety rules, guide away from pitfalls

### Structured Output

Request specific formats for reliable parsing.

```markdown
Analyze this code and return results in JSON format:

{
  "issues": [
    {
      "line": number,
      "severity": "critical|high|medium|low",
      "description": "string",
      "suggestion": "string"
    }
  ],
  "summary": {
    "total_issues": number,
    "critical_count": number,
    "high_count": number
  }
}
```

**Benefits**: Easier downstream processing, validation, integration with systems

## Common Pitfalls

Avoid these prompt engineering mistakes:

### Over-Specification
- **Problem**: Prompts become so detailed they confuse rather than clarify
- **Solution**: Start simple, add detail only where needed

### Under-Specification
- **Problem**: Vague prompts lead to inconsistent outputs
- **Solution**: Be explicit about requirements, format, and constraints

### Assumption of Context
- **Problem**: Assuming model knows project-specific details
- **Solution**: Provide necessary context explicitly in each prompt

### Ignoring Model Limitations
- **Problem**: Asking for capabilities beyond model's design
- **Solution**: Understand what each model does well, adjust tasks accordingly

### No Validation Loop
- **Problem**: Not testing prompts lead to unreliable outputs in production
- **Solution**: Implement systematic testing and evaluation

### Prompt Injection Vulnerabilities
- **Problem**: User inputs can manipulate model behavior
- **Solution**: Sanitize inputs, use delimiters, validate outputs

## Integration with Development Workflow

### With AGENTS.md
- Reference prompt patterns in operational procedures
- Include prompt templates for common tasks
- Document prompt versioning and testing requirements

### With SSOT.md
- Use canonical terminology consistently in prompts
- Reference schemas and data contracts
- Link to authoritative sources for domain knowledge

### With SKILL.md
- Package proven prompts as reusable Skills
- Document prompt patterns for domain-specific tasks
- Share successful templates across projects

### With PLANS.md
- Document prompt strategies in ExecPlans
- Track prompt evolution during complex initiatives
- Record learnings about prompt effectiveness

## Decision Guide

| Scenario | Recommended Approach |
|----------|---------------------|
| Code generation | OpenAI Codex patterns: concrete references, tight prompts, verification upfront |
| Long context analysis | Claude: structured tags, progressive disclosure, explicit instructions |
| Multimodal tasks | Google Gemini: combine text and images, leverage multimodal capabilities |
| Complex reasoning | Chain-of-thought, self-consistency, step-by-step elaboration |
| Consistent formatting | Few-shot examples, structured output templates, explicit format specs |
| Safety-critical | Constraint-first, negative prompting, validation loops |
| Rapid prototyping | Start simple, iterate based on outputs, gradually add complexity |
| Production deployment | Version prompts, add logging, implement testing, monitor performance |

## Prompting vs. Fine-Tuning

**Start with prompting when**:
- You need rapid iteration
- Requirements are still evolving
- You want to maintain flexibility
- Task can be described clearly in natural language

**Consider fine-tuning when**:
- Prompts cannot reliably control behavior
- You have substantial high-quality training data
- You need consistent behavior across many similar tasks
- You want to optimize for latency or cost at scale

**Best Practice**: Exhaust prompt iteration possibilities before investing in fine-tuning. Most use cases can be solved with well-designed prompts.

## Resources and Cross-References

### Platform Documentation
- **Anthropic**: [Claude Prompt Engineering](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering)
- **Google**: [Vertex AI Prompt Design](https://cloud.google.com/vertex-ai/generative-ai/docs/learn/prompts/introduction-prompt-design)
- **OpenAI**: [Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering)

### Research and Tutorials
- Anthropic Interactive Tutorial: [GitHub Repository](https://github.com/anthropics/prompt-eng-interactive-tutorial)
- DeepMind Research on Prompting: Papers 83400, 90773
- OpenAI Codex Cookbook: [Examples](https://cookbook.openai.com/)

### Related Documentation
- `engineering/prompt.md` - Vendor-specific prompt engineering notes
- `engineering/context.md` - Context engineering best practices
- `platforms/` - SDK-specific implementation guidance
- `topics/AGENTS.md` - Operational procedures for AI agents
- `topics/SKILL.md` - Packaging prompts as reusable Skills

## Update Log

- 2025-10-20: Initial creation consolidating prompt/ directory guidance for engineering reference
