Perfect. If you already know:

- ✅ Python
    
- ✅ FastAPI
    
- ✅ REST APIs
    
- ✅ LangChain
    
- ✅ RAG
    
- ✅ Tool calling
    
- ✅ LLM basics
    

then **don't waste time relearning them**.

US companies hiring for LangGraph aren't looking for "I built a chatbot." They're looking for engineers who can build **reliable, stateful AI systems**.

I'd narrow your LangGraph syllabus to this.

---

# Module 1 — Graph Fundamentals (Must Know)

You should be able to build any workflow from scratch.

Know:

- StateGraph
    
- START
    
- END
    
- Nodes
    
- Edges
    
- Conditional Edges
    
- Command
    
- Send
    
- compile()
    
- invoke()
    
- stream()
    

If I ask,

> Build an order approval workflow

you should immediately think

```
START

↓

Validate

↓

Approve?

↓

YES ---------> Ship

↓

NO ----------> Reject

↓

END
```

---

# Module 2 — State (Very Important)

This is probably the #1 interview topic.

Know:

- TypedDict state
    
- Pydantic state
    
- updating state
    
- reducers
    
- Annotated reducers
    
- MessagesState
    
- custom state
    

Example

```python
class State(TypedDict):
    messages: Annotated[list, add_messages]
    user: str
    documents: list
    plan: list
```

Know exactly

- when state changes
    
- how state merges
    
- why reducers exist
    

---

# Module 3 — Routing

Master

Conditional edges

```python
if state["intent"] == "search":
```

Looping

Retries

Branches

Fallbacks

Dynamic routing

Parallel routing

You should be able to build

```
Intent

↓

Search?

↓

Summarize?

↓

Code?

↓

Math?
```

without looking at docs.

---

# Module 4 — Memory

This is huge.

Know

Short-term memory

Checkpointing

MemorySaver

SQLiteSaver

PostgresSaver

Thread IDs

Conversation persistence

Resume execution

Interrupt/resume

Interview question

> How does LangGraph remember previous execution?

You should answer confidently.

---

# Module 5 — Human In The Loop

Very common.

Know

Interrupt

Resume

Approve

Reject

Manual editing

Example

```
LLM

↓

Generate Invoice

↓

WAIT

↓

Human Approves

↓

Continue
```

---

# Module 6 — ToolNode

Master

ToolNode

tool calling

multiple tools

tool errors

retry

fallback

tool routing

tool selection

parallel tools

Projects

- Search Agent
    
- SQL Agent
    
- Coding Agent
    

---

# Module 7 — Multi-Agent

Probably the hottest topic.

Know architectures.

Supervisor

```
Supervisor

↓

Research Agent

↓

Writer Agent

↓

Reviewer Agent
```

Planner

Executor

Reviewer

Critic

Hierarchical agents

Agent teams

Subgraphs

Handoffs

---

# Module 8 — Command API

Many people skip this.

Know

```
Command

goto

update

resume

interrupt
```

This replaces lots of ugly conditional edges.

Interviewers love asking this.

---

# Module 9 — Send API

Know why Send exists.

Example

```
One document

↓

Split into 30 chunks

↓

30 summarizers

↓

Merge
```

Parallel execution.

---

# Module 10 — Streaming

Know

```
graph.stream()

astream()

astream_events()
```

Streaming messages

Streaming tokens

Streaming state

Streaming tool execution

---

# Module 11 — Subgraphs

Very important for production.

Instead of

```
2000-line graph
```

Know

```
Main Graph

↓

Research Graph

↓

Writing Graph

↓

Review Graph
```

Reusable workflows.

---

# Module 12 — Checkpointing

Know

MemorySaver

SQLite

Postgres

Redis (conceptually)

Execution replay

Recovery

Thread persistence

---

# Module 13 — Observability

Must know

LangSmith

Tracing

Debugging

State inspection

Node inspection

Execution history

Latency

Errors

Production logging

---

# Module 14 — Error Handling

Know

Retries

Fallback nodes

Exception handling

Timeouts

Dead ends

Validation

---

# Module 15 — Production Deployment

Know how to deploy

LangGraph

↓

FastAPI

↓

Docker

↓

Postgres

↓

Redis

↓

Kubernetes (basic understanding)

↓

AWS

---

# Module 16 — Performance

Know

Parallel execution

Caching

Batching

State size optimization

Prompt optimization

Node granularity

Memory optimization

---

# Module 17 — Design Patterns

These are asked surprisingly often.

Patterns

- Router
    
- Supervisor
    
- Planner
    
- Evaluator
    
- Reflection
    
- Self-correction
    
- Retry
    
- Critic
    
- Map-Reduce
    
- Fan-Out/Fan-In
    
- Human Approval
    
- Multi-Agent Collaboration
    

If someone describes a business problem, you should recognize which pattern fits.

---

# Module 18 — Build These Projects

Don't build toy apps. Build portfolio pieces that mirror real products.

### 1. Deep Research Agent ⭐⭐⭐⭐⭐

Like Perplexity or OpenAI Deep Research.

Features:

- planner
    
- web search
    
- retrieval
    
- summarization
    
- reviewer
    
- citations
    

---

### 2. Coding Agent ⭐⭐⭐⭐⭐

Like Cursor.

Features:

- file editing
    
- tool calling
    
- code review
    
- testing
    
- reflection
    

---

### 3. Customer Support Agent

- memory
    
- RAG
    
- ticket routing
    
- escalation
    
- human approval
    

---

### 4. SQL Analytics Agent

- database tools
    
- query planning
    
- validation
    
- chart generation
    

---

### 5. Multi-Agent Research Team

Supervisor

↓

Researcher

↓

Fact Checker

↓

Writer

↓

Reviewer

---

### 6. AI Workflow Platform

A UI where users can define workflows that your backend compiles into LangGraph graphs. This demonstrates graph composition and dynamic execution.

---

## What US companies actually look for

From job descriptions I've seen, they care less about whether you've memorized every API and more about whether you can reason about agent systems. You should be comfortable discussing:

- Why choose LangGraph over plain LangChain?
    
- How do you model state?
    
- How do you recover from failures?
    
- How do you persist long-running workflows?
    
- How do you prevent infinite loops?
    
- How do you coordinate multiple agents?
    
- How do you evaluate agent quality?
    
- How do you debug complex executions with LangSmith?
    
- When do you use conditional edges vs `Command`?
    
- How do you parallelize work with `Send` and merge results?
    

If you can answer those questions and have **3–5 production-quality GitHub projects** with tests, documentation, and deployment, you'll be in a strong position for LangGraph-focused backend or AI engineer roles.

One suggestion: don't stop at LangGraph itself. Many US AI engineering roles now also expect familiarity with **MCP (Model Context Protocol)**, **A2A (Agent-to-Agent communication concepts)**, structured outputs, evaluation frameworks, and observability. Pairing those with strong LangGraph experience will make your profile much more competitive.