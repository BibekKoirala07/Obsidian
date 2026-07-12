

```
Production AI Agent

├── FastAPI
├── LangGraph
├── PostgreSQL Checkpointer
├── Memory
├── Multi-Agent
├── Human Approval
├── Tool Calling
├── Parallel Execution
├── Streaming
├── LangSmith
├── Docker
└── Tests
```

That is something you can confidently put on your resume.

---

# WEEK 1 — Learn How LangGraph Thinks

Forget agents.

Forget RAG.

Forget tools.

Just understand **graphs**.

---

## Day 1 — What is LangGraph?

Learn

- Why LangGraph exists
    
- Workflow vs Agent
    
- DAG vs Graph
    
- Stateful execution
    
- Why state matters
    

Vocabulary

```
Graph
Node
Edge
State
Workflow
Execution
```

Project

```
START

↓

Hello Node

↓

END
```

---

## Day 2 — Nodes

Learn

Nodes are simply functions.

```
def greet(state):
    ...
```

Vocabulary

```
Node

Pure Function

LLM Node

Side Effects

State Update
```

Project

```
Greeting

↓

Uppercase

↓

Print
```

---

## Day 3 — State

This is the MOST IMPORTANT DAY.

Learn

```
TypedDict

MessagesState

Pydantic

Reducers

Annotated
```

Understand

```
state

↓

node

↓

new state

↓

node

↓

new state
```

Project

Conversation state

---

## Day 4 — Edges

Learn

```
add_edge()

START

END
```

Project

```
Collect

↓

Process

↓

Save

↓

END
```

---

## Day 5 — Conditional Edges

Vocabulary

```
Router

Conditional

Branch

Destination
```

Project

```
Intent

↓

Search?

↓

Math?

↓

Weather?
```

---

## Day 6 — Loops

Vocabulary

```
Loop

Retry

Iteration
```

Project

Retry until valid answer.

---

## Day 7 — Review

Build

Order Processing Workflow

```
Validate

↓

Approve

↓

Reject

↓

Notify
```

---

# WEEK 2 — Learn State Deeply

---

## Day 8 — Reducers

Learn

```
Annotated

Reducer

Merge

add_messages
```

Understand why reducers exist.

---

## Day 9 — Message History

Vocabulary

```
MessagesState

Conversation

History
```

Project

Persistent chat.

---

## Day 10 — Commands

Huge topic.

Learn

```
Command

goto

update
```

Project

Navigation workflow.

---

## Day 11 — Interrupt

Learn

```
interrupt()

resume()
```

Project

Approval workflow.

---

## Day 12 — Checkpointing

Vocabulary

```
Thread

Checkpoint

MemorySaver
```

Project

Resume conversation after restart.

---

## Day 13 — SQLite Checkpointer

Project

Persistent memory.

---

## Day 14 — Review

Mini Project

ChatGPT Clone

with

- memory
    
- resume
    
- checkpoint
    

---

# WEEK 3 — Agents

---

## Day 15 — ToolNode

Vocabulary

```
Tool

ToolNode

Tool Calling

Execution
```

Project

Calculator + Search

---

## Day 16 — Multiple Tools

Project

Travel Assistant

Weather

Hotels

Maps

Calculator

---

## Day 17 — Error Handling

Vocabulary

```
Retry

Fallback

Recovery
```

---

## Day 18 — Planner Pattern

Architecture

```
Question

↓

Plan

↓

Execute

↓

Answer
```

---

## Day 19 — Reflection

Architecture

```
Generate

↓

Review

↓

Improve
```

---

## Day 20 — Supervisor

Architecture

```
Supervisor

↓

Researcher

↓

Writer

↓

Reviewer
```

---

## Day 21 — Review

Build

Research Agent

---

# WEEK 4 — Professional LangGraph

---

## Day 22 — Send API

Vocabulary

```
Send

Parallel

Map

Reduce
```

Project

Summarize 20 PDFs simultaneously.

---

## Day 23 — Subgraphs

Architecture

```
Research Graph

Writing Graph

Review Graph
```

---

## Day 24 — Streaming

Vocabulary

```
stream()

astream()

events
```

---

## Day 25 — LangSmith

Learn

Tracing

Execution

Debugging

Latency

---

## Day 26 — FastAPI Integration

Build

```
POST /chat

↓

LangGraph

↓

Streaming
```

---

## Day 27 — PostgreSQL Checkpointer

Production persistence.

---

## Day 28 — Docker

Dockerize everything.

---

## Day 29 — Tests

Write

- Unit Tests
    
- Node Tests
    
- Graph Tests
    

---

## Day 30 — Final Project

Build

# AI Research Assistant

Features

```
FastAPI

↓

Authentication

↓

LangGraph

↓

Planner

↓

Research Agent

↓

Web Search

↓

Summarizer

↓

Reviewer

↓

Human Approval

↓

Memory

↓

Postgres

↓

Streaming

↓

LangSmith

↓

Docker
```

---

# 📚 The Reading Order (Don't Skip Around)

This is the order I'd recommend for the official docs and examples:

1. **Core Concepts**
    
    - Graphs
        
    - StateGraph
        
    - Nodes
        
    - Edges
        
2. **State**
    
    - TypedDict
        
    - MessagesState
        
    - Reducers
        
3. **Execution**
    
    - `compile`
        
    - `invoke`
        
    - `stream`
        
4. **Routing**
    
    - Conditional edges
        
    - `Command`
        
5. **Memory**
    
    - Checkpointers
        
    - Threads
        
6. **Human-in-the-loop**
    
    - `interrupt`
        
    - `resume`
        
7. **ToolNode**
    
8. **Subgraphs**
    
9. **Send (parallelism)**
    
10. **Streaming**
    
11. **LangSmith**
    
12. **Deployment**
    

Notice that **agents come after you understand graphs and state**. That's where many learners go wrong—they jump straight into "build an AI agent" without understanding the execution model.

---

# 📂 GitHub Portfolio (the projects I'd expect)

By the end, your GitHub should include:

```
01-basic-graph
02-routing-workflow
03-state-management
04-memory-chatbot
05-human-approval
06-tool-calling-agent
07-planner-executor
08-supervisor-multi-agent
09-parallel-summarizer
10-production-ai-assistant
```

Each repository should have:

- A clear README explaining the architecture.
    
- A diagram of the graph (Mermaid is fine).
    
- Tests.
    
- Docker support.
    
- Example API requests.
    
- A short demo GIF or video.
    

---

## One last piece of advice

If your goal is **US AI engineering jobs**, don't try to become "the best at LangGraph." LangGraph is a tool. Companies hire people who can **design reliable AI systems**, not people who can recite every API.

I'd pair this roadmap with:

- **MCP (Model Context Protocol)** after Week 3.
    
- **LangSmith** throughout Week 4.
    
- At least **one deployed project** (Render, Railway, Fly.io, or AWS).
    
- Daily GitHub commits and well-documented repositories.
    

That combination is much more compelling to employers than simply listing "LangGraph" on a resume.