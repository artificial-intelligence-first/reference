---
title: Google Agent Development Kit (ADK)
slug: google-adk
status: living
last_updated: 2025-10-31
tags: [google, agents, sdk, adk, multi-agent, orchestration]
summary: "Google's Agent Development Kit (ADK) for building multi-agent applications with code-first development, hierarchical agent systems, and flexible deployment options."
sources:
  - { id: R1, title: "Google ADK Documentation", url: "https://google.github.io/adk-docs/", accessed: "2025-10-31" }
  - { id: R2, title: "Google ADK Python Repository", url: "https://github.com/google/adk-python", accessed: "2025-10-31" }
  - { id: R3, title: "Google ADK Java Repository", url: "https://github.com/google/adk-java", accessed: "2025-10-31" }
  - { id: R4, title: "Google Developers Blog: Agent Development Kit", url: "https://developers.googleblog.com/en/agent-development-kit-easy-to-build-multi-agent-applications/", accessed: "2025-10-31" }
---

# Google Agent Development Kit (ADK)

> **For Humans**
> This document explains Google's Agent Development Kit (ADK), a framework for building multi-agent applications with hierarchical structures, flexible tool integration, and code-first development. Use this when building complex agent systems that need specialization, delegation, and scalable deployment.

> **For AI Agents**
> This document defines the canonical Google ADK specification for building multi-agent systems. Apply these patterns when implementing hierarchical agent architectures, integrating with Google Cloud services, or developing scalable agent applications. Cross-reference with AGENTS.md for operational procedures.

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

- **What**: Google's framework for building multi-agent applications with hierarchical structures, supporting both Python and Java
- **Why**: Enables code-first agent development, multi-agent coordination, flexible tool integration, and deployment on Google Cloud infrastructure
- **When**: Building complex agent systems requiring specialization, hierarchical delegation, or integration with Google Cloud services
- **How**: Define agents programmatically with models, instructions, tools, and sub-agents; deploy to Cloud Run or Vertex AI Agent Engine
- **Watch out**: While optimized for Gemini models, ADK supports multiple LLM providers—choose models based on task requirements

---

## Canonical Definitions

### Google Agent Development Kit (ADK)

**Definition**: A framework for building multi-agent applications where specialized agents are composed into hierarchical structures, enabling complex problem-solving through agent coordination and delegation [R2][R3].

**Scope**:
- **Includes**: Code-first agent definition, hierarchical multi-agent systems, tool integration (functions, OpenAPI, MCP), model-agnostic design, deployment to Google Cloud (Cloud Run, Vertex AI), development UI for testing
- **Excludes**: Pre-built agents (requires custom development), agent-to-agent communication protocols (see A2A), training or fine-tuning capabilities

**Languages**:
- **Python**: `google-adk` via pip (stable releases every ~2 weeks)
- **Java**: Available via Maven Central (version 0.3.0+)

**Related Concepts**:
- **Complements**: Agent2Agent (A2A) protocol for cross-framework communication
- **Similar**: LangChain multi-agent systems, AutoGen frameworks
- **Integrates with**: Model Context Protocol (MCP) for tool standardization

**Sources**: [R2][R3]

### Hierarchical Multi-Agent System

**Definition**: An architecture pattern where parent agents coordinate and delegate tasks to specialized child agents (sub-agents), creating a tree structure of agent capabilities [R2][R3].

**Scope**: Enables complex problem decomposition, parallel task execution, and specialized agent roles.

**Example Architecture**:
```
Root Agent (Coordinator)
├── Research Agent
│   ├── Web Search Sub-Agent
│   └── Document Analysis Sub-Agent
├── Data Analysis Agent
│   ├── Statistics Sub-Agent
│   └── Visualization Sub-Agent
└── Writing Agent
    ├── Draft Generation Sub-Agent
    └── Editing Sub-Agent
```

**Sources**: [R2][R3]

### Code-First Development

**Definition**: A design philosophy where agent behavior, orchestration, and tool usage are defined directly in code (Python or Java), enabling version control, testing, and programmatic agent generation [R2][R3].

**Scope**: Contrasts with configuration-based or no-code agent builders; emphasizes developer control and reproducibility.

**Sources**: [R2][R3]

---

## Core Patterns

### Pattern 1: Single Agent with Tools

**Intent**: Create a single autonomous agent with access to custom tools and functions.

**Context**: When you need a specialized agent for a specific domain or task.

**Implementation**:

**Python**:
```python
from google.adk.agents import Agent
from google.adk.tools import tool

@tool
def search_products(query: str, category: str = "all") -> dict:
    """
    Search the product catalog.

    Args:
        query: Search query string
        category: Product category to filter by

    Returns:
        Dictionary containing matching products
    """
    # Implementation here
    return {
        "results": [...],
        "count": 10
    }

# Create agent with tool
product_agent = Agent(
    name="product_assistant",
    model="gemini-2.5-flash",
    instruction="""You are a helpful product search assistant.
    You can search our catalog and provide product recommendations
    based on customer needs.""",
    tools=[search_products]
)

# Use the agent
response = product_agent.run("Find wireless headphones under $100")
print(response)
```

**Java**:
```java
import com.google.adk.agents.LlmAgent;
import com.google.adk.tools.Tool;

public class ProductAgent {
    @Tool(description = "Search the product catalog")
    public ProductSearchResult searchProducts(
        String query,
        String category
    ) {
        // Implementation here
        return new ProductSearchResult(...);
    }

    public LlmAgent createAgent() {
        return LlmAgent.builder()
            .name("product_assistant")
            .model("gemini-2.0-flash")
            .instruction("You are a helpful product search assistant...")
            .tools(this::searchProducts)
            .build();
    }
}
```

**Key Principles**:
- Define tools as functions with clear docstrings [R2][R3]
- Use type hints (Python) or strong typing (Java) for parameters
- Provide descriptive instructions to guide agent behavior
- Model selection: `gemini-2.5-flash` for speed, `gemini-2.5-pro` for complexity

**Trade-offs**:
- ✅ **Advantages**: Simple to develop, focused capabilities, easy to test
- ⚠️ **Disadvantages**: Limited to single domain, no task delegation
- 💡 **Alternatives**: Use multi-agent pattern for complex workflows

**Sources**: [R2][R3]

### Pattern 2: Hierarchical Multi-Agent System

**Intent**: Compose specialized agents into a hierarchy where parent agents coordinate and delegate to child agents.

**Context**: When tasks require multiple specialized capabilities or parallel execution.

**Implementation**:

**Python**:
```python
from google.adk.agents import Agent

# Specialized sub-agents
web_researcher = Agent(
    name="web_researcher",
    model="gemini-2.5-flash",
    instruction="You specialize in web research. Search and extract relevant information.",
    tools=[google_search, extract_content]
)

document_analyzer = Agent(
    name="document_analyzer",
    model="gemini-2.5-pro",
    instruction="You analyze documents and extract key insights.",
    tools=[read_pdf, extract_tables]
)

data_visualizer = Agent(
    name="data_visualizer",
    model="gemini-2.5-flash",
    instruction="You create data visualizations and charts.",
    tools=[create_chart, generate_graph]
)

# Root coordinator agent
research_coordinator = Agent(
    name="research_coordinator",
    model="gemini-2.5-pro",
    instruction="""You coordinate research tasks by delegating to specialized agents.
    - Use web_researcher for online information
    - Use document_analyzer for document processing
    - Use data_visualizer for creating visualizations
    Synthesize results from all agents into comprehensive reports.""",
    sub_agents=[web_researcher, document_analyzer, data_visualizer]
)

# Execute complex research task
result = research_coordinator.run(
    "Research AI safety trends in 2024 and create a visual summary"
)
```

**Java**:
```java
import com.google.adk.agents.LlmAgent;

public class ResearchSystem {
    public LlmAgent buildResearchCoordinator() {
        // Build specialized sub-agents
        LlmAgent webResearcher = LlmAgent.builder()
            .name("web_researcher")
            .model("gemini-2.0-flash")
            .instruction("You specialize in web research...")
            .tools(new GoogleSearchTool())
            .build();

        LlmAgent documentAnalyzer = LlmAgent.builder()
            .name("document_analyzer")
            .model("gemini-2.0-pro")
            .instruction("You analyze documents...")
            .tools(new DocumentAnalysisTool())
            .build();

        // Build coordinator with sub-agents
        return LlmAgent.builder()
            .name("research_coordinator")
            .model("gemini-2.0-pro")
            .instruction("You coordinate research tasks...")
            .subAgents(webResearcher, documentAnalyzer)
            .build();
    }
}
```

**Key Principles**:
- Assign specialized roles to sub-agents [R2][R3]
- Coordinator agents use higher-capability models for orchestration
- Sub-agents can run in parallel for efficiency
- Clear instruction hierarchies prevent confusion

**Trade-offs**:
- ✅ **Advantages**: Scalable, parallel execution, clear separation of concerns, specialized expertise
- ⚠️ **Disadvantages**: More complex architecture, coordination overhead, debugging challenges
- 💡 **Alternatives**: Single agent with many tools for simpler use cases

**Sources**: [R2][R3]

### Pattern 3: Tool Integration (OpenAPI, MCP, Custom Functions)

**Intent**: Integrate diverse tools and external services using multiple integration approaches.

**Context**: When agents need access to APIs, databases, or standardized tool protocols.

**Implementation**:

**Python**:
```python
from google.adk.agents import Agent
from google.adk.tools import tool, openapi_tool, mcp_tool

# Custom function tool
@tool
def query_database(sql: str) -> list:
    """Execute SQL query against database."""
    # Implementation
    return results

# OpenAPI tool integration
stripe_tool = openapi_tool(
    name="stripe_api",
    spec_url="https://api.stripe.com/openapi.yaml",
    auth={"api_key": "sk_..."}
)

# MCP tool integration
github_mcp = mcp_tool(
    name="github",
    server_command="npx",
    server_args=["-y", "@modelcontextprotocol/server-github"],
    env={"GITHUB_TOKEN": "ghp_..."}
)

# Agent with mixed tools
business_agent = Agent(
    name="business_assistant",
    model="gemini-2.5-pro",
    instruction="""You help with business operations:
    - Use query_database for internal data
    - Use stripe_api for payment operations
    - Use github for code repository tasks""",
    tools=[query_database, stripe_tool, github_mcp]
)
```

**Key Principles**:
- Use custom functions for simple, Python-native logic [R2]
- Use OpenAPI specs for standardized REST APIs [R2]
- Use MCP for ecosystem tool compatibility [R2]
- Provide clear tool descriptions for agent understanding

**Trade-offs**:
- ✅ **Advantages**: Flexible integration, standardized protocols, broad ecosystem support
- ⚠️ **Disadvantages**: Different configuration approaches, authentication complexity
- 💡 **Alternatives**: Implement all tools as custom functions for consistency

**Sources**: [R2]

### Pattern 4: Development and Testing with UI

**Intent**: Use the built-in development UI for interactive agent testing and debugging.

**Context**: During development when you need to test agent behavior, tool calling, and responses.

**Implementation**:

**Python**:
```python
from google.adk.agents import Agent
from google.adk.dev_ui import start_ui

# Define agent
my_agent = Agent(
    name="test_agent",
    model="gemini-2.5-flash",
    instruction="You are a helpful assistant for testing.",
    tools=[tool1, tool2]
)

# Start development UI
start_ui(
    agent=my_agent,
    port=8080,
    debug=True
)

# Access at http://localhost:8080
# - Test prompts interactively
# - Inspect tool calls
# - View agent reasoning (if available)
# - Adjust parameters in real-time
```

**Key Principles**:
- Use development UI for rapid iteration [R2][R3]
- Test edge cases and failure modes interactively
- Inspect tool invocations and agent decisions
- Debug before deploying to production

**Trade-offs**:
- ✅ **Advantages**: Fast feedback loop, visual debugging, easy testing
- ⚠️ **Disadvantages**: Development-only feature, not for production
- 💡 **Alternatives**: Unit tests with mocked responses for CI/CD

**Sources**: [R2][R3]

### Pattern 5: Production Deployment (Cloud Run, Vertex AI)

**Intent**: Deploy agents to Google Cloud infrastructure for scalable production use.

**Context**: When moving from development to production with reliability and scale requirements.

**Implementation**:

**Python - Cloud Run Deployment**:
```python
# agent_server.py
from google.adk.agents import Agent
from google.adk.deploy import create_fastapi_app
import os

# Define production agent
production_agent = Agent(
    name="production_assistant",
    model="gemini-2.5-pro",
    instruction="Production agent instructions...",
    tools=[...]
)

# Create FastAPI app
app = create_fastapi_app(production_agent)

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=int(os.getenv("PORT", 8080)))
```

**Dockerfile**:
```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY agent_server.py .

CMD ["python", "agent_server.py"]
```

**Deploy to Cloud Run**:
```bash
# Build and deploy
gcloud run deploy agent-service \
  --source . \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --set-env-vars GOOGLE_API_KEY=...

# Or deploy to Vertex AI Agent Engine for managed scaling
gcloud ai agents deploy my-agent \
  --region us-central1 \
  --agent-file agent_config.yaml
```

**Key Principles**:
- Containerize agents for Cloud Run deployment [R2][R3]
- Use Vertex AI Agent Engine for managed infrastructure [R2][R3]
- Configure environment variables for API keys and secrets
- Implement health checks and monitoring
- Use autoscaling for variable workloads

**Trade-offs**:
- ✅ **Advantages**: Scalable, managed infrastructure, integrated with Google Cloud, autoscaling
- ⚠️ **Disadvantages**: Cloud-specific deployment, requires Google Cloud account, potential vendor lock-in
- 💡 **Alternatives**: Deploy to Kubernetes or other cloud providers using containers

**Sources**: [R2][R3]

---

## Decision Checklist

Use Google ADK when:

- [ ] **Multi-Agent Coordination**: You need hierarchical agent systems with specialized roles [R2][R3]
  - Verify: Tasks require delegation and specialization across multiple domains
  - Impact: Single-agent systems may be simpler for focused tasks

- [ ] **Code-First Development**: You prefer programmatic agent definition with version control [R2][R3]
  - Verify: Team has Python or Java expertise
  - Impact: No-code platforms may be easier for non-developers

- [ ] **Google Cloud Integration**: You're deploying on Google Cloud Platform [R2][R3]
  - Verify: Using Cloud Run, Vertex AI, or other GCP services
  - Impact: Other SDKs may be better for AWS or Azure

- [ ] **Model Flexibility**: You need multi-provider model support [R2]
  - Verify: Requirements include non-Gemini models
  - Impact: Provider-specific SDKs lock you to single vendor

- [ ] **Rapid Iteration**: You need development UI for testing [R2][R3]
  - Verify: Development workflow requires interactive debugging
  - Impact: CLI-only workflows may not need UI

**Decision Matrix**:

| Scenario | Use Google ADK | Alternative | Reason |
|----------|---------------|-------------|--------|
| Multi-agent hierarchies | ✅ Yes | LangChain, AutoGen | ADK optimized for hierarchical systems |
| Google Cloud deployment | ✅ Yes | Provider-agnostic frameworks | Native GCP integration |
| Single-agent applications | ⚠️ Optional | Direct API calls | ADK may be overkill |
| Cross-framework communication | ⚠️ Partial | A2A Protocol implementation | ADK focuses on internal coordination |
| Enterprise Java projects | ✅ Yes | Python-only frameworks | Java support with Maven integration |

---

## Anti-patterns / Pitfalls

### Anti-pattern 1: Over-Nesting Agent Hierarchies

**Symptom**: Creating deeply nested agent hierarchies (4+ levels) with unclear delegation paths.

**Why It Happens**: Attempting to model every possible specialization as a separate agent.

**Impact**:
- Coordination overhead increases exponentially
- Debugging becomes extremely difficult
- Latency accumulates across delegation layers
- Context gets lost through multiple handoffs

**Solution**: Limit hierarchies to 2-3 levels; combine related capabilities into single agents [R2][R3].

**Example**:

```python
# ❌ Anti-pattern: Over-nested hierarchy
root = Agent(
    sub_agents=[
        Agent(  # Level 1
            sub_agents=[
                Agent(  # Level 2
                    sub_agents=[
                        Agent(...),  # Level 3
                        Agent(...)
                    ]
                )
            ]
        )
    ]
)

# ✅ Correct pattern: Flatter hierarchy with focused agents
root = Agent(
    name="coordinator",
    sub_agents=[
        Agent(name="research", tools=[search, extract, analyze]),
        Agent(name="data", tools=[query, visualize, report]),
        Agent(name="communication", tools=[email, slack, format])
    ]
)
```

**Sources**: [R2][R3]

### Anti-pattern 2: Vague Agent Instructions

**Symptom**: Generic or minimal instruction strings that don't specify agent role, capabilities, or constraints.

**Why It Happens**: Assuming the model will infer appropriate behavior from tool availability.

**Impact**:
- Inconsistent agent behavior
- Inappropriate tool usage
- Poor delegation decisions
- Suboptimal results

**Solution**: Write detailed, specific instructions defining role, workflow, and decision criteria [R2][R3].

**Example**:

```python
# ❌ Anti-pattern: Vague instructions
agent = Agent(
    name="helper",
    instruction="You are a helpful assistant.",
    tools=[...]
)

# ✅ Correct pattern: Detailed instructions
agent = Agent(
    name="customer_support_specialist",
    instruction="""You are a customer support specialist for TechCorp.

**Your role:**
- Resolve customer issues related to billing, technical problems, and account management
- Escalate complex issues to human agents

**Your workflow:**
1. Greet the customer and identify their issue
2. Check account status using check_account tool
3. Attempt resolution using available tools
4. If unresolved, escalate using create_ticket tool
5. Always confirm resolution with customer

**Constraints:**
- Never modify billing without explicit approval
- Always verify customer identity before account changes
- Use professional, empathetic tone

**Decision criteria:**
- Escalate if issue requires >15 minutes or manual intervention
- Prioritize account security over convenience""",
    tools=[check_account, process_refund, create_ticket, send_email]
)
```

**Sources**: [R2][R3]

### Anti-pattern 3: Ignoring Model Selection

**Symptom**: Using the same model (often the most capable/expensive) for all agents regardless of task complexity.

**Why It Happens**: Defaulting to "best" model without considering cost-performance trade-offs.

**Impact**:
- Unnecessarily high API costs
- Slower response times for simple tasks
- Inefficient resource utilization

**Solution**: Match model capability to task complexity [R2][R3].

**Example**:

```python
# ❌ Anti-pattern: Using expensive model everywhere
coordinator = Agent(model="gemini-2.5-pro", ...)
simple_search = Agent(model="gemini-2.5-pro", ...)  # Overkill
data_formatter = Agent(model="gemini-2.5-pro", ...)  # Overkill

# ✅ Correct pattern: Model selection by task complexity
coordinator = Agent(
    model="gemini-2.5-pro",  # Complex orchestration
    sub_agents=[
        Agent(
            name="search",
            model="gemini-2.5-flash",  # Simple search task
            ...
        ),
        Agent(
            name="analysis",
            model="gemini-2.5-pro",  # Complex analysis
            ...
        ),
        Agent(
            name="format",
            model="gemini-2.5-flash",  # Simple formatting
            ...
        )
    ]
)
```

**Model Selection Guide**:
- `gemini-2.5-flash`: Simple tasks, routing, formatting, extraction
- `gemini-2.5-pro`: Complex reasoning, coordination, synthesis
- Consider cost: Flash is significantly cheaper than Pro

**Sources**: [R2][R3]

### Anti-pattern 4: Missing Error Handling

**Symptom**: No error handling for tool failures, API errors, or agent failures.

**Why It Happens**: Focusing on happy-path development without considering failure scenarios.

**Impact**:
- Agent crashes on errors
- Poor user experience
- No recovery mechanisms
- Difficult debugging

**Solution**: Implement comprehensive error handling with retries and fallbacks [R2].

**Example**:

```python
# ❌ Anti-pattern: No error handling
@tool
def api_call(endpoint: str) -> dict:
    response = requests.get(endpoint)
    return response.json()  # Will crash on errors

# ✅ Correct pattern: Robust error handling
from typing import Optional
import time

@tool
def api_call(endpoint: str, max_retries: int = 3) -> Optional[dict]:
    """
    Call external API with retry logic.

    Returns None if all retries fail.
    """
    for attempt in range(max_retries):
        try:
            response = requests.get(endpoint, timeout=10)
            response.raise_for_status()
            return response.json()
        except requests.RequestException as e:
            if attempt == max_retries - 1:
                print(f"API call failed after {max_retries} attempts: {e}")
                return None
            time.sleep(2 ** attempt)  # Exponential backoff
    return None

# Agent with error-aware instructions
agent = Agent(
    instruction="""When tools return None or fail:
    1. Inform the user about the issue
    2. Attempt alternative approaches if available
    3. Request human assistance if critical""",
    tools=[api_call]
)
```

**Sources**: [R2]

---

## Evaluation

### Metrics

**Primary Metrics**:
- **Task Completion Rate**: Percentage of tasks completed successfully by agent system
  - Target: >85% completion rate
  - Measurement: `(completed_tasks / total_tasks) * 100`

- **Agent Coordination Efficiency**: Ratio of sub-agent invocations to successful task completions
  - Target: <3 sub-agent calls per task on average
  - Measurement: `total_sub_agent_calls / completed_tasks`

- **Development Velocity**: Time to develop and deploy agent system
  - Target: <50% time vs. custom implementation
  - Measurement: Track development hours from specification to production

**Secondary Metrics**:
- API cost per task execution
- Average task latency
- Tool invocation success rate
- Agent error recovery rate

### Testing Strategies

**Unit Tests**:
```python
import pytest
from google.adk.agents import Agent
from unittest.mock import Mock

def test_agent_with_tools():
    """Test agent successfully uses tools."""
    mock_tool = Mock(return_value="Tool result")

    agent = Agent(
        name="test_agent",
        model="gemini-2.5-flash",
        instruction="Use the tool when asked.",
        tools=[mock_tool]
    )

    response = agent.run("Use the tool")

    assert mock_tool.called
    assert "Tool result" in response

def test_multi_agent_coordination():
    """Test sub-agent delegation."""
    sub_agent = Agent(
        name="specialist",
        model="gemini-2.5-flash",
        instruction="You are a specialist.",
        tools=[]
    )

    coordinator = Agent(
        name="coordinator",
        model="gemini-2.5-pro",
        instruction="Delegate to the specialist agent.",
        sub_agents=[sub_agent]
    )

    response = coordinator.run("Task requiring specialist")
    assert response is not None
```

**Integration Tests**:
```python
@pytest.mark.integration
def test_agent_with_real_api():
    """Test agent with actual API calls."""
    agent = Agent(
        name="api_agent",
        model="gemini-2.5-flash",
        instruction="Call the API to get data.",
        tools=[real_api_tool]
    )

    response = agent.run("Get data from API")
    assert response is not None
    assert "error" not in response.lower()
```

**Performance Benchmarks**:
```bash
# Measure agent response latency
python benchmark_agent.py --iterations 100 --task "simple_query"

# Measure multi-agent coordination overhead
python benchmark_hierarchy.py --depth 3 --agents 5

# Measure tool invocation latency
python benchmark_tools.py --tool-count 10
```

### Success Criteria

- [ ] Task completion rate >85%
- [ ] Agent coordination efficiency <3 calls per task
- [ ] Development time reduced by >50% vs custom implementation
- [ ] API costs within budget constraints (track $/task)
- [ ] Average task latency <10 seconds
- [ ] Tool invocation success rate >95%
- [ ] Zero critical security incidents with proper permissions

**Sources**: [R2][R3]

---

## Update Log

### 2025-10-31
- **Initial version**: Comprehensive Google ADK documentation
- **Added**: Canonical definitions for ADK, hierarchical multi-agent systems, and code-first development
- **Added**: Core patterns for single agents, multi-agent hierarchies, tool integration, development UI, and production deployment
- **Added**: Decision checklist, anti-patterns, and evaluation criteria
- **Added**: Python and Java code examples throughout
- **Sources**: [R2][R3]

---

## See Also

### Prerequisites
- [AGENTS.md](../../files/AGENTS.md): Operational documentation for AI agents
- [Agent2Agent Protocol](./a2a.md): Cross-framework agent communication

### Related Topics
- [Context Engineering](../../engineering/context.md): Managing context in AI interactions
- [Prompt Engineering](../../engineering/prompt.md): Effective prompt design patterns

### Related Platforms
- [OpenAI Agents SDK](../openai/agents-sdk.md): OpenAI's approach to agent systems
- [Claude Agent SDK](../anthropic/claude-agent-sdk.md): Anthropic's agent framework

### Cloud Deployment
- **Google Cloud Run**: Serverless container deployment
- **Vertex AI Agent Engine**: Managed agent infrastructure
- **Model Context Protocol (MCP)**: Standardized tool integration

---

## References

- [R1] Google. "Agent Development Kit Documentation." https://google.github.io/adk-docs/ (accessed 2025-10-31)
- [R2] Google. "Agent Development Kit for Python." GitHub. https://github.com/google/adk-python (accessed 2025-10-31)
- [R3] Google. "Agent Development Kit for Java." GitHub. https://github.com/google/adk-java (accessed 2025-10-31)
- [R4] Google Developers. "Agent Development Kit: Easy to Build Multi-Agent Applications." Google Developers Blog. https://developers.googleblog.com/en/agent-development-kit-easy-to-build-multi-agent-applications/ (accessed 2025-10-31)

---

**Document ID**: `platforms/google/adk`
**Canonical URL**: `https://github.com/artificial-intelligence-first/ssot/blob/main/platforms/google/adk.md`
**License**: MIT
