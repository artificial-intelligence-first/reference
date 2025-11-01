---
title: LangChain
slug: langchain
status: living
last_updated: 2025-11-01
tags: [agents, orchestration, rag, sdk, framework]
summary: "Open-source framework for building LLM applications with modular components and memory management."
authors: []
sources:
  - { id: R1, title: "LangChain Official Website", url: "https://www.langchain.com/", accessed: "2025-10-24" }
  - { id: R2, title: "LangChain GitHub Repository", url: "https://github.com/langchain-ai/langchain", accessed: "2025-10-24" }
  - { id: R3, title: "LangChain Documentation", url: "https://docs.langchain.com", accessed: "2025-10-24" }
---

# LangChain

> **For Humans**: LangChain is an open-source framework for building LLM applications with features like memory, tool integration, RAG, and multi-agent orchestration.
>
> **For AI Agents**: Use LangChain patterns for chain composition, memory management, tool integration, and retrieval-augmented generation in LLM applications.

## Overview

LangChain provides a modular framework for building applications powered by language models, offering abstractions for common patterns like chains, agents, memory, and retrieval.

## Core Components

### Chains
Sequential or parallel composition of LLM calls and transformations.

```python
from langchain import LLMChain, PromptTemplate

chain = LLMChain(
    llm=llm,
    prompt=PromptTemplate(
        template="Translate to {language}: {text}"
    )
)
result = chain.run(language="Spanish", text="Hello world")
```

### Agents
Autonomous decision-makers that use tools to accomplish tasks.

```python
from langchain.agents import initialize_agent, Tool

agent = initialize_agent(
    tools=[Tool(name="search", func=search_func)],
    llm=llm,
    agent="zero-shot-react-description"
)
```

### Memory
State management across conversation turns.

```python
from langchain.memory import ConversationBufferMemory

memory = ConversationBufferMemory()
chain = LLMChain(llm=llm, memory=memory)
```

### RAG (Retrieval-Augmented Generation)
Combine retrieval with generation for grounded responses.

```python
from langchain.vectorstores import FAISS
from langchain.chains import RetrievalQA

vectorstore = FAISS.from_documents(documents, embeddings)
qa = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=vectorstore.as_retriever()
)
```

## See Also

- [LangGraph](./langgraph.md) - Stateful multi-agent orchestration
- [AGENTS.md](../../../AGENTS.md) - Agent operations
- [RAG Patterns](../../engineering/context-engineering.md) - Context engineering

## References

- [R1] LangChain Official Website. https://www.langchain.com/ (accessed 2025-10-24)
- [R2] LangChain GitHub Repository. https://github.com/langchain-ai/langchain (accessed 2025-10-24)
- [R3] LangChain Documentation. https://docs.langchain.com (accessed 2025-10-24)
