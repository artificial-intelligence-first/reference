---
title: LangChain
slug: langchain
status: living
last_updated: 2025-10-24
tags: [agents, orchestration, rag, sdk, best-practices]
summary: "Open-source framework for building LLM-powered applications with modular components, memory management, and extensive integrations."
sources:
  - { id: R1, title: "LangChain Official Website", url: "https://www.langchain.com/", accessed: "2025-10-24" }
  - { id: R2, title: "LangChain GitHub Repository", url: "https://github.com/langchain-ai/langchain", accessed: "2025-10-24" }
  - { id: R3, title: "LangChain Documentation", url: "https://docs.langchain.com", accessed: "2025-10-24" }
  - { id: R4, title: "LangChain Framework 2025 Guide", url: "https://latenode.com/blog/langchain-framework-2025-complete-features-guide-real-world-use-cases-for-developers", accessed: "2025-10-24" }
---

# LangChain

> **For Humans**
> This document explains LangChain, an open-source framework for building applications powered by large language models. Use this when developing LLM applications with features like memory, tool integration, retrieval-augmented generation (RAG), and multi-agent orchestration.

> **For AI Agents**
> This document defines LangChain capabilities, architecture patterns, and best practices. Apply these patterns when implementing LLM-powered applications, building autonomous agents, or creating RAG systems. Cross-reference with AGENTS.md for operational procedures and SSOT.md for canonical definitions.

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

- **What**: Open-source framework for building LLM-powered applications with modular components, memory, and extensive integrations
- **Why**: Simplifies development of complex LLM applications with reusable components, standardized interfaces, and production-ready patterns
- **When**: Building chatbots, document analysis systems, autonomous agents, RAG applications, or any LLM-powered workflow
- **How**: Install via pip/npm, compose modular components (LLMs, prompts, chains, agents), leverage integrations for vector stores and APIs
- **Watch out**: Framework abstraction overhead, version compatibility issues, potential performance impact from extensive layering

---

## Canonical Definitions

### LangChain

**Definition**: An open-source framework that streamlines the development of applications powered by large language models (LLMs), providing a suite of modular tools and integrations for natural language processing, data retrieval, and interaction with various data sources [R1][R2][R3].

**Scope**:
- **Includes**: LLM integrations, prompt templates, chains, agents, memory systems, vector store integrations, tool calling, retrieval-augmented generation (RAG), LCEL (LangChain Expression Language)
- **Excludes**: Hosted LLM services, vector database implementations, custom model training

**Supported Languages**:
- **Python**: Primary implementation with full feature set
- **JavaScript/TypeScript**: Parallel implementation for Node.js and browser environments

**Key Components** [R3][R4]:
1. **LCEL (LangChain Expression Language)**: Declarative syntax for defining LLM workflows
2. **LangGraph**: Graph-based orchestration for multi-agent systems (see separate documentation)
3. **LangServe**: Expose agents and chains as RESTful APIs
4. **LangSmith**: Observability and evaluation platform

**Related Concepts**:
- **Similar**: AutoGen, CrewAI, Semantic Kernel
- **Complementary**: Vector databases (Chroma, Pinecone, Weaviate), LLM providers (OpenAI, Anthropic, Google)
- **Built with**: Used by companies including Replit, Clay, Rippling, Cloudflare, and Workday [R1]

**Sources**: [R1][R2][R3][R4]

### Chains

**Definition**: Sequences of calls to LLMs, tools, or data processing functions that can be composed together to create complex workflows [R3].

**Scope**: Simple sequential chains, transformation chains, router chains, and custom chain compositions.

**Example**:
```python
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4")
prompt = PromptTemplate.from_template("Translate {text} to {language}")
chain = LLMChain(llm=llm, prompt=prompt)

result = chain.run(text="Hello world", language="Spanish")
```

**Sources**: [R3]

### Agents

**Definition**: LLM-powered systems that use reasoning to determine which actions to take and in what order, with access to a suite of tools and the ability to dynamically decide their workflow [R3][R4].

**Scope**: Tool-using agents, conversational agents, ReAct agents, plan-and-execute agents, multi-agent systems.

**Key Capabilities** [R4]:
- Perceive environment through observations
- Reason about goals and constraints
- Take actions to achieve objectives
- Maintain context across interactions

**Sources**: [R3][R4]

### Memory

**Definition**: Mechanisms for maintaining state and context across multiple interactions, enabling agents to remember previous conversations and learned information [R3][R4].

**Types**:
- **Short-term memory**: Conversation history within a session
- **Long-term memory**: Persistent storage across sessions using vector databases
- **Entity memory**: Track specific entities mentioned in conversations
- **Summary memory**: Compressed representations of long conversations

**Sources**: [R3][R4]

---

## Core Patterns

### Pattern 1: Retrieval-Augmented Generation (RAG)

**Intent**: Connect LLMs to external knowledge sources to provide accurate, up-to-date information beyond the model's training data.

**Context**: When building question-answering systems, document analysis tools, or knowledge bases that require factual accuracy and source attribution.

**Implementation**:

```python
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_community.vectorstores import Chroma
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.chains import RetrievalQA
from langchain_community.document_loaders import TextLoader

# Load and split documents
loader = TextLoader("knowledge_base.txt")
documents = loader.load()
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200
)
texts = text_splitter.split_documents(documents)

# Create vector store
embeddings = OpenAIEmbeddings()
vectorstore = Chroma.from_documents(
    documents=texts,
    embedding=embeddings
)

# Create retrieval chain
llm = ChatOpenAI(model="gpt-4")
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    chain_type="stuff",
    retriever=vectorstore.as_retriever(search_kwargs={"k": 3})
)

# Query the knowledge base
response = qa_chain.run("What is the company's refund policy?")
```

**Key Principles**:
- Chunk documents appropriately (typically 500-1500 tokens) [R3]
- Use overlap between chunks to maintain context
- Choose appropriate embedding models for your domain
- Configure retrieval parameters (number of results, similarity threshold)
- Implement source attribution in responses

**Trade-offs**:
- ✅ **Advantages**: Accurate, sourced responses; easily updatable knowledge; reduced hallucination
- ⚠️ **Disadvantages**: Additional latency from retrieval; requires vector database infrastructure; chunk size optimization needed
- 💡 **Alternatives**: Fine-tuning models with domain data, prompt engineering with few-shot examples, hybrid approaches combining RAG and fine-tuning

**Sources**: [R3][R4]

### Pattern 2: Conversational Agents with Memory

**Intent**: Build chatbots that maintain context across multiple turns and can use tools to accomplish tasks.

**Context**: When creating customer service bots, personal assistants, or any conversational interface requiring stateful interactions.

**Implementation**:

```python
from langchain.agents import AgentExecutor, create_openai_functions_agent
from langchain_openai import ChatOpenAI
from langchain.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain.memory import ConversationBufferMemory
from langchain.tools import Tool

# Define tools
def search_knowledge_base(query: str) -> str:
    """Search the knowledge base for information."""
    # Implementation here
    return f"Results for: {query}"

def create_ticket(description: str) -> str:
    """Create a support ticket."""
    # Implementation here
    return f"Ticket created: {description}"

tools = [
    Tool(
        name="SearchKnowledge",
        func=search_knowledge_base,
        description="Search the knowledge base for information"
    ),
    Tool(
        name="CreateTicket",
        func=create_ticket,
        description="Create a support ticket when user needs help"
    )
]

# Create agent with memory
llm = ChatOpenAI(model="gpt-4", temperature=0)
memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful customer service assistant."),
    MessagesPlaceholder(variable_name="chat_history"),
    ("human", "{input}"),
    MessagesPlaceholder(variable_name="agent_scratchpad")
])

agent = create_openai_functions_agent(llm, tools, prompt)
agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    memory=memory,
    verbose=True
)

# Have a conversation
response1 = agent_executor.invoke(
    {"input": "What is your return policy?"}
)
response2 = agent_executor.invoke(
    {"input": "I'd like to return an item I bought last week"}
)
```

**Key Principles**:
- Choose appropriate memory type based on conversation length [R3]
- Implement memory pruning for long conversations
- Provide clear tool descriptions for agent reasoning
- Set temperature=0 for deterministic agent behavior
- Log agent reasoning steps for debugging

**Trade-offs**:
- ✅ **Advantages**: Natural conversational flow; context-aware responses; tool integration for actions
- ⚠️ **Disadvantages**: Token usage grows with conversation length; agent may make incorrect tool choices; requires careful prompt engineering
- 💡 **Alternatives**: Stateless request-response patterns, rule-based chatbots for simple flows, specialized agents for specific tasks

**Sources**: [R3][R4]

### Pattern 3: Multi-Agent Orchestration

**Intent**: Coordinate multiple specialized agents to handle complex workflows requiring diverse capabilities.

**Context**: When tasks require different expertise areas, parallel processing, or hierarchical decision-making.

**Implementation**:

```python
from langchain.agents import AgentExecutor, create_openai_functions_agent
from langchain_openai import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from typing import Dict, List

class MultiAgentOrchestrator:
    """Orchestrate multiple specialized agents."""

    def __init__(self):
        self.llm = ChatOpenAI(model="gpt-4", temperature=0)
        self.agents = {
            "research": self._create_research_agent(),
            "writer": self._create_writer_agent(),
            "reviewer": self._create_reviewer_agent()
        }

    def _create_research_agent(self) -> AgentExecutor:
        """Agent specialized in information gathering."""
        # Create agent with search and retrieval tools
        pass

    def _create_writer_agent(self) -> AgentExecutor:
        """Agent specialized in content creation."""
        # Create agent with writing and formatting tools
        pass

    def _create_reviewer_agent(self) -> AgentExecutor:
        """Agent specialized in quality assurance."""
        # Create agent with review and validation tools
        pass

    def execute_workflow(self, task: str) -> Dict:
        """Execute multi-agent workflow."""
        # Research phase
        research_results = self.agents["research"].invoke({
            "input": f"Research information needed for: {task}"
        })

        # Writing phase
        draft = self.agents["writer"].invoke({
            "input": f"Write content based on: {research_results['output']}"
        })

        # Review phase
        final_output = self.agents["reviewer"].invoke({
            "input": f"Review and improve: {draft['output']}"
        })

        return {
            "research": research_results,
            "draft": draft,
            "final": final_output
        }

# Use orchestrator
orchestrator = MultiAgentOrchestrator()
result = orchestrator.execute_workflow(
    "Create a blog post about AI safety"
)
```

**Key Principles**:
- Assign clear roles and responsibilities to each agent [R4]
- Use LangGraph for complex control flows (see LangGraph documentation)
- Implement state management across agents
- Handle agent failures gracefully with fallbacks
- Monitor and log inter-agent communication

**Trade-offs**:
- ✅ **Advantages**: Specialized expertise; parallel processing; modular and maintainable; handles complex workflows
- ⚠️ **Disadvantages**: Increased complexity and cost; coordination overhead; debugging challenges
- 💡 **Alternatives**: Single general-purpose agent, sequential chains, LangGraph for advanced orchestration

**Sources**: [R4]

### Pattern 4: LCEL (LangChain Expression Language)

**Intent**: Define LLM workflows using a clear, declarative, composable syntax.

**Context**: When building complex chains that require transformation, routing, or parallel execution.

**Implementation**:

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableParallel

# Define components
llm = ChatOpenAI(model="gpt-4")
prompt = ChatPromptTemplate.from_template(
    "Summarize this text in {style} style:\n\n{text}"
)
output_parser = StrOutputParser()

# Compose chain using LCEL
chain = (
    {"text": RunnablePassthrough(), "style": lambda _: "professional"}
    | prompt
    | llm
    | output_parser
)

# Execute chain
result = chain.invoke("Long text to summarize...")

# Parallel execution
parallel_chain = RunnableParallel(
    summary=chain,
    translation=prompt | llm | output_parser,
    keywords=prompt | llm | output_parser
)
```

**Key Principles**:
- Use `|` operator for sequential composition [R3]
- Leverage `RunnableParallel` for concurrent execution
- Apply `RunnablePassthrough` for data routing
- Implement custom runnables for specialized logic
- Enable streaming with `.stream()` method

**Trade-offs**:
- ✅ **Advantages**: Clean, readable syntax; built-in streaming; parallel execution; type safety
- ⚠️ **Disadvantages**: Learning curve for LCEL syntax; debugging can be challenging
- 💡 **Alternatives**: Traditional chain composition, custom Python functions

**Sources**: [R3][R4]

---

## Decision Checklist

### Use LangChain when:

- ✅ Building LLM-powered applications requiring structured workflows
- ✅ Need extensive integrations with LLM providers, vector databases, or tools
- ✅ Implementing RAG systems with document retrieval
- ✅ Creating conversational agents with memory and tool use
- ✅ Developing multi-agent systems with orchestration
- ✅ Want production-ready patterns and best practices
- ✅ Team has Python or TypeScript/JavaScript expertise
- ✅ Need observability and evaluation tools (LangSmith)
- ✅ Building prototypes that may scale to production

### Consider alternatives when:

- ❌ Building simple LLM API calls without complex workflows
- ❌ Extremely latency-sensitive applications (framework overhead)
- ❌ Need maximum control over every aspect of LLM interaction
- ❌ Working with languages other than Python or JavaScript
- ❌ Team already invested in alternative frameworks (AutoGen, Semantic Kernel)
- ❌ Minimal dependencies are critical requirement
- ❌ Building custom agentic frameworks from scratch

**Framework Comparison**:

| Framework | Best For | Strengths | Considerations |
|-----------|----------|-----------|----------------|
| **LangChain** | General LLM apps, RAG, agents | Extensive integrations, mature ecosystem | Abstraction overhead |
| **LangGraph** | Complex multi-agent workflows | Graph-based orchestration, state management | Steeper learning curve |
| **AutoGen** | Multi-agent conversations | Agent communication patterns | Microsoft ecosystem |
| **CrewAI** | Role-based agent teams | Simple multi-agent setup | Newer, smaller ecosystem |

---

## Anti-patterns / Pitfalls

### Anti-pattern 1: Over-Abstraction

**Problem**: Using complex chain and agent structures for simple LLM calls.

**Why it's problematic**: Adds unnecessary latency, complexity, and debugging overhead when direct LLM API calls would suffice.

**Example of anti-pattern**:
```python
# Overcomplicated for a simple task
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate

prompt = PromptTemplate.from_template("Translate {text} to Spanish")
chain = LLMChain(llm=llm, prompt=prompt)
result = chain.run(text="Hello")
```

**Better approach**:
```python
# Direct API call for simple tasks
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4")
result = llm.invoke("Translate 'Hello' to Spanish")
```

**When to use the pattern**: Use LangChain abstractions when you need composition, memory, tool calling, or plan to extend functionality later.

**Sources**: Best practices from production usage

### Anti-pattern 2: Ignoring Memory Limits

**Problem**: Allowing conversation memory to grow unbounded, leading to token limit errors and high costs.

**Why it's problematic**: Long conversations can exceed model context windows and consume excessive tokens.

**Example of anti-pattern**:
```python
# Unbounded memory growth
memory = ConversationBufferMemory()
# After 100 turns, memory contains entire conversation history
```

**Better approach**:
```python
# Implement memory management
from langchain.memory import ConversationSummaryBufferMemory

# Summarize old messages to save tokens
memory = ConversationSummaryBufferMemory(
    llm=llm,
    max_token_limit=2000
)

# Or use sliding window
from langchain.memory import ConversationBufferWindowMemory
memory = ConversationBufferWindowMemory(k=5)  # Keep last 5 turns
```

**When to use the pattern**: Always implement memory limits for production conversational agents.

**Sources**: [R3]

### Anti-pattern 3: Insufficient Error Handling

**Problem**: Not handling LLM API failures, tool execution errors, or agent reasoning failures.

**Why it's problematic**: Agents can fail in unexpected ways, and unhandled errors lead to poor user experience.

**Example of anti-pattern**:
```python
# No error handling
result = agent_executor.invoke({"input": user_query})
return result["output"]
```

**Better approach**:
```python
# Comprehensive error handling
from langchain.schema import OutputParserException

try:
    result = agent_executor.invoke(
        {"input": user_query},
        {"max_iterations": 5, "early_stopping_method": "generate"}
    )
    return result["output"]
except OutputParserException as e:
    # Handle parsing errors
    return "I had trouble understanding the response. Please try again."
except Exception as e:
    # Log error and provide user-friendly message
    logger.error(f"Agent execution failed: {e}")
    return "I encountered an error. Please contact support."
```

**When to use the pattern**: Always implement error handling in production systems.

**Sources**: Best practices from production usage

### Anti-pattern 4: Hardcoding Prompts

**Problem**: Embedding prompts directly in code without version control or testing.

**Why it's problematic**: Makes it difficult to iterate on prompts, A/B test, or maintain prompt quality.

**Example of anti-pattern**:
```python
# Hardcoded prompts
chain = LLMChain(
    llm=llm,
    prompt=PromptTemplate.from_template("You are helpful assistant. Answer: {question}")
)
```

**Better approach**:
```python
# Store prompts separately with versioning
from langchain.prompts import load_prompt

# Load from file: prompts/assistant_v1.yaml
prompt = load_prompt("prompts/assistant_v1.yaml")
chain = LLMChain(llm=llm, prompt=prompt)

# Use LangSmith for prompt management and A/B testing
```

**When to use the pattern**: Store prompts in separate files for all production applications.

**Sources**: [R3]

---

## Evaluation

### Success Metrics

**Application-Level Metrics**:
- **Response Quality**: Accuracy, relevance, and coherence of outputs
- **Task Completion Rate**: Percentage of user intents successfully handled
- **Response Time**: End-to-end latency (target: <3 seconds for most applications)
- **Tool Call Success Rate**: Percentage of correct tool selections and executions
- **User Satisfaction**: Explicit feedback and implicit signals (conversation length, completion rate)

**Cost Metrics**:
- **Token Usage**: Input and output tokens per interaction
- **API Costs**: Dollar cost per conversation or task
- **Vector Store Costs**: Storage and query costs for embeddings
- **Infrastructure Costs**: Server, database, and monitoring costs

**Reliability Metrics**:
- **Error Rate**: Percentage of failed requests
- **Availability**: Uptime of LLM APIs and dependent services
- **Retry Rate**: Frequency of required retries
- **Fallback Rate**: How often fallback mechanisms are triggered

### Evaluation Strategies

**1. LangSmith Integration** [R3]:
```python
from langsmith import Client
from langsmith.evaluation import evaluate

client = Client()

# Define evaluator
def accuracy_evaluator(run, example):
    """Evaluate if output matches expected result."""
    prediction = run.outputs["output"]
    reference = example.outputs["expected"]
    return {"score": int(prediction == reference)}

# Run evaluation
results = evaluate(
    lambda inputs: chain.invoke(inputs),
    data="my-evaluation-dataset",
    evaluators=[accuracy_evaluator],
    experiment_prefix="chain-v1"
)
```

**2. RAG Evaluation**:
- **Retrieval Quality**: Precision@K, Recall@K, MRR (Mean Reciprocal Rank)
- **Answer Faithfulness**: Whether answers are grounded in retrieved documents
- **Context Relevance**: Whether retrieved documents are relevant to the question

**3. Agent Evaluation**:
- **Tool Selection Accuracy**: Correct tool chosen for the task
- **Reasoning Quality**: Logical coherence of agent's thought process
- **Action Success Rate**: Whether actions achieve intended outcomes

**4. A/B Testing**:
```python
# Test different prompt versions
import random

def get_chain(version="A"):
    if version == "A":
        prompt = load_prompt("prompts/v1.yaml")
    else:
        prompt = load_prompt("prompts/v2.yaml")
    return LLMChain(llm=llm, prompt=prompt)

# Route traffic
version = "A" if random.random() < 0.5 else "B"
chain = get_chain(version)
result = chain.invoke({"input": user_query})
```

### Performance Benchmarks

**Typical Latencies** (GPT-4, production workloads):
- Simple chain: 1-2 seconds
- RAG with retrieval: 2-4 seconds
- Agent with 2-3 tool calls: 5-10 seconds
- Multi-agent workflow: 10-30 seconds

**Optimization Strategies**:
- Use streaming for better perceived latency
- Cache frequent queries and embeddings
- Parallel execution with LCEL
- Choose appropriate model sizes (GPT-4 vs GPT-3.5, Claude Sonnet vs Haiku)
- Implement request batching where possible

**Sources**: [R3] and production usage patterns

---

## Update Log

### 2025-10-24
- **Initial version**: Created comprehensive LangChain documentation
- **Added**: Canonical definitions for LangChain, chains, agents, and memory
- **Added**: Core patterns for RAG, conversational agents, multi-agent orchestration, and LCEL
- **Added**: Decision checklist and framework comparison
- **Added**: Anti-patterns covering over-abstraction, memory limits, error handling, and prompt management
- **Added**: Evaluation strategies and success metrics with LangSmith integration

---

## See Also

### Related Topics
- **[LangGraph](./langgraph.md)** - Graph-based orchestration for complex multi-agent workflows (companion to LangChain)
- **[AGENTS.md](../../topics/AGENTS.md)** - General agent operational documentation and conventions
- **[SSOT.md](../../topics/SSOT.md)** - Single Source of Truth principles for canonical definitions
- **[Context Engineering](../../engineering/context.md)** - Best practices for RAG and retrieval systems
- **[Prompt Engineering](../../engineering/prompt.md)** - Prompt design patterns applicable to LangChain

### Platform Comparisons
- **[OpenAI Agents SDK](../openai/agents-sdk.md)** - OpenAI's native agent framework
- **[Claude Code](../anthropic/claude-code.md)** - Anthropic's agentic coding tool
- **[Claude Agent SDK](../anthropic/claude-agent-sdk.md)** - SDK for building custom agents with Claude

---

## References

- [R1] LangChain. "LangChain Official Website." https://www.langchain.com/ (accessed 2025-10-24)
- [R2] LangChain AI. "LangChain GitHub Repository." https://github.com/langchain-ai/langchain (accessed 2025-10-24)
- [R3] LangChain. "LangChain Documentation." https://docs.langchain.com (accessed 2025-10-24)
- [R4] Latenode. "LangChain Framework 2025: Complete Features Guide + Real-World Use Cases for Developers." https://latenode.com/blog/langchain-framework-2025-complete-features-guide-real-world-use-cases-for-developers (accessed 2025-10-24)

---

**Document ID**: `platforms/langchain/langchain`
**Last Updated**: 2025-10-24
