# Taxonomy

> **For Humans**
> This document defines the controlled vocabulary for tags used across all documentation. Using consistent terminology improves discoverability and prevents fragmentation.

> **For AI Agents**
> When adding or updating documents, use only canonical tags from this list. If a needed tag doesn't exist, add it here first with a clear definition.

---

## Tag Categories

### Canonical Tags

Tags are organized by category. Each tag includes:
- **Canonical name** - The preferred term
- **Definition** - Clear scope and boundaries
- **Synonyms** - Alternative terms (use canonical name instead)
- **Related tags** - Associated concepts

---

## Development Practices

### agents
**Definition**: Agent architectures, orchestration, tool use, and multi-agent systems
**Synonyms**: agent-systems, agentic, autonomous-agents
**Related**: orchestration, tool-calling, reasoning

### orchestration
**Definition**: Coordination and workflow management of AI systems and components
**Synonyms**: workflow, coordination, task-management
**Related**: agents, planning, execution

### tool-calling
**Definition**: Enabling AI models to invoke external functions, APIs, or tools
**Synonyms**: function-calling, tool-use, api-integration
**Related**: agents, integrations, apis

### planning
**Definition**: Task decomposition, goal setting, and execution strategies for AI systems
**Synonyms**: task-planning, strategic-planning, goal-decomposition
**Related**: agents, reasoning, orchestration

### reasoning
**Definition**: Logical inference, chain-of-thought, and decision-making processes
**Synonyms**: inference, logic, cognitive-processes
**Related**: planning, agents, prompting

---

## Engineering Methodologies

### context
**Definition**: Context construction, retrieval, and management for AI systems
**Synonyms**: context-engineering, context-design, contextual-ai
**Related**: rag, retrieval, prompt-engineering

### prompt-engineering
**Definition**: Designing, testing, and optimizing prompts for AI models
**Synonyms**: prompting, prompt-design, prompt-optimization
**Related**: context, reasoning, evaluation

### rag
**Definition**: Retrieval-Augmented Generation patterns and implementations
**Synonyms**: retrieval-augmented-generation, retrieval, document-retrieval
**Related**: context, embeddings, vector-search

### evaluation
**Definition**: Testing, benchmarking, and quality assessment of AI systems
**Synonyms**: testing, benchmarking, quality-assurance, metrics
**Related**: observability, monitoring, debugging

### observability
**Definition**: Monitoring, logging, and telemetry for AI systems
**Synonyms**: monitoring, telemetry, logging, instrumentation
**Related**: evaluation, debugging, production

---

## Model Capabilities

### code-generation
**Definition**: AI-assisted code writing, completion, and synthesis
**Synonyms**: codegen, code-synthesis, autocomplete
**Related**: copilot, ide-integration, refactoring

### multimodal
**Definition**: Processing or generating multiple modalities (text, image, audio, video)
**Synonyms**: vision, image-understanding, audio-processing
**Related**: embeddings, generation

### embeddings
**Definition**: Vector representations of text, images, or other data
**Synonyms**: vectors, vector-embeddings, semantic-vectors
**Related**: rag, similarity-search, clustering

### fine-tuning
**Definition**: Training or adapting models on custom datasets
**Synonyms**: training, model-adaptation, custom-models
**Related**: evaluation, datasets, optimization

---

## Infrastructure & Platforms

### openai
**Definition**: OpenAI models, APIs, and SDKs
**Synonyms**: gpt, chatgpt, openai-api
**Related**: apis, sdk, platforms

### anthropic
**Definition**: Anthropic Claude models and APIs
**Synonyms**: claude, claude-api
**Related**: apis, sdk, platforms

### google
**Definition**: Google Gemini, Vertex AI, and related services
**Synonyms**: gemini, vertex-ai, palm
**Related**: apis, sdk, platforms

### sdk
**Definition**: Software development kits and client libraries
**Synonyms**: client-library, api-client, sdk-integration
**Related**: apis, integrations, development

### apis
**Definition**: Application programming interfaces and REST endpoints
**Synonyms**: api, rest-api, endpoints
**Related**: sdk, integrations, http

---

## Governance & Documentation

### ssot
**Definition**: Single Source of Truth principles and patterns
**Synonyms**: single-source-of-truth, canonical-reference, authoritative-source
**Related**: documentation, governance, standards

### changelog
**Definition**: Version history, release notes, and change documentation
**Synonyms**: release-notes, version-history, change-log
**Related**: versioning, documentation, releases

### conventions
**Definition**: Standards, patterns, and agreed-upon practices
**Synonyms**: standards, practices, guidelines, protocols
**Related**: governance, best-practices, patterns

### documentation
**Definition**: Technical writing, guides, and reference materials
**Synonyms**: docs, technical-writing, guides
**Related**: knowledge-management, ssot, reference

---

## Patterns & Practices

### best-practices
**Definition**: Proven approaches and recommended patterns
**Synonyms**: recommended-practices, guidelines, standards
**Related**: conventions, patterns, anti-patterns

### anti-patterns
**Definition**: Common mistakes, pitfalls, and approaches to avoid
**Synonyms**: pitfalls, mistakes, bad-practices
**Related**: best-practices, debugging, troubleshooting

### design-patterns
**Definition**: Reusable solutions to common design problems
**Synonyms**: patterns, architectural-patterns, software-patterns
**Related**: architecture, best-practices, conventions

### security
**Definition**: Security considerations, threat models, and safe practices
**Synonyms**: safety, security-practices, threat-modeling
**Related**: production, compliance, governance

---

## Operational Concerns

### production
**Definition**: Deployment, scaling, and production readiness
**Synonyms**: deployment, operations, prod
**Related**: scaling, reliability, monitoring

### performance
**Definition**: Latency, throughput, and optimization
**Synonyms**: optimization, speed, efficiency, latency
**Related**: scaling, production, benchmarking

### cost
**Definition**: Pricing, budget management, and cost optimization
**Synonyms**: pricing, budget, cost-optimization
**Related**: production, scaling, optimization

### reliability
**Definition**: Fault tolerance, error handling, and system stability
**Synonyms**: stability, fault-tolerance, resilience
**Related**: production, monitoring, error-handling

---

## Use Cases

### chatbots
**Definition**: Conversational AI, chat interfaces, and dialogue systems
**Synonyms**: conversational-ai, chat, dialogue-systems
**Related**: agents, nlp, user-interfaces

### search
**Definition**: Information retrieval, semantic search, and search systems
**Synonyms**: information-retrieval, semantic-search, search-engines
**Related**: rag, embeddings, retrieval

### summarization
**Definition**: Text summarization, document compression, and synthesis
**Synonyms**: text-summarization, content-synthesis
**Related**: nlp, text-processing

### translation
**Definition**: Language translation and cross-lingual applications
**Synonyms**: machine-translation, localization, i18n
**Related**: nlp, multimodal

---

## Special Tags

### living
**Definition**: Documents that are actively maintained and updated
**Synonyms**: active, maintained, current
**Related**: documentation, changelog

### deprecated
**Definition**: Obsolete or superseded content
**Synonyms**: obsolete, legacy, archived
**Related**: changelog, migration

### experimental
**Definition**: Cutting-edge or unstable features and patterns
**Synonyms**: beta, preview, unstable
**Related**: research, innovation

---

## Tag Usage Guidelines

### Adding Tags

1. **Check existing tags first** - Use canonical names from this document
2. **Maximum 5 tags per document** - Focus on most relevant categories
3. **Prefer specific over general** - Use `rag` instead of generic `ai`
4. **Include category diversity** - Mix practice, platform, and use-case tags

### Proposing New Tags

When a concept isn't covered:

1. **Add entry to this document** in appropriate category
2. **Define clearly** with boundaries and related concepts
3. **List synonyms** to prevent duplicate tags
4. **Update existing documents** that should use this tag
5. **Commit with message**: `docs(_meta/taxonomy): add tag 'new-tag'`

### Deprecating Tags

When a tag becomes obsolete:

1. **Do not remove** from this document
2. **Mark as deprecated**: `**Status**: DEPRECATED - Use 'replacement-tag' instead`
3. **Update all documents** using the old tag
4. **Add to synonyms** of replacement tag

Example:
```markdown
### old-tag
**Status**: DEPRECATED - Use 'new-tag' instead
**Definition**: [Original definition]
**Deprecated**: 2025-10-23
**Replacement**: new-tag
```

---

## Tag Validation

### Automated Checks

A script in `scripts/validate-tags.sh` can verify:
- All tags used in documents exist in TAXONOMY.md
- No deprecated tags are in active use
- Tag consistency across related documents

### Manual Review

During quarterly audits:
- Remove unused tags from taxonomy
- Merge near-duplicate tags
- Update definitions for clarity
- Add emerging concepts

---

## Cross-Reference Index

### By Platform
- OpenAI: `openai`, `gpt`, `sdk`
- Anthropic: `anthropic`, `claude`, `sdk`
- Google: `google`, `gemini`, `vertex-ai`

### By Practice
- Development: `agents`, `tool-calling`, `planning`
- Engineering: `context`, `prompt-engineering`, `rag`
- Operations: `production`, `monitoring`, `reliability`

### By Concern
- Quality: `evaluation`, `testing`, `observability`
- Governance: `ssot`, `conventions`, `documentation`
- Performance: `optimization`, `cost`, `scaling`

---

## Update Log

### 2025-10-23
- **Initial version**: Established canonical tags across 7 categories
- **Added**: 50+ tags with definitions and relationships
- **Defined**: Tag validation and deprecation processes

---

**Document ID**: `_meta/TAXONOMY`
**Last Updated**: 2025-10-23
