---
layout: post
title: "Google's Agent2Agent Protocol (A2A): A Guide With Examples"
tags:
  - Agent2Agent
  - Google ADK
  - Multi-Agent Systems
  - A2A vs MCP
  - JSON-RPC
  - Server Sent Events
featured_image_thumbnail: "assets/images/posts/a2a.png"
featured_image: "assets/images/posts/a2a.png"
featured: true
hidden: false
---

**[Read the full article on DataCamp: Agent2Agent Protocol (A2A) – A Guide With Examples](https://www.datacamp.com/blog/a2a-agent2agent)**

Learn about **Google’s Agent2Agent (A2A)**, an open protocol that enables multi-agent collaboration via structured tasks, messaging, and streaming – powering scalable, cross-platform AI agent ecosystems.

---

# Contents
- [What Is Agent2Agent (A2A)?](#what-is-agent2agent-a2a)
- [How Does A2A Work?](#how-does-a2a-work)
- [A2A Example: IT Helpdesk Ticket Resolution](#a2a-example-it-helpdesk-ticket-resolution)
- [A2A vs MCP: When to Use What?](#a2a-vs-mcp-when-to-use-what)
- [Conclusion](#conclusion)

---

## What Is Agent2Agent (A2A)?

Agent2Agent (A2A) is an **open, vendor-neutral protocol** developed by Google that standardizes collaboration among AI agents. It allows agents to:

- Discover each other
- Exchange structured tasks
- Handle streaming responses and multi-turn conversations
- Interoperate across text, image, video, and structured data

> 🔍 Think of A2A as HTTP for agents — enabling structured, stateless, cross-platform interactions.

### A2A Design Principles
1. **Agentic autonomy** — no memory/tool sharing required.
2. **Built on open standards** — HTTP, JSON-RPC, SSE.
3. **Secure by default** — OpenAPI auth support.
4. **Supports long-running tasks** — async + streaming ready.
5. **Modality agnostic** — handles PDFs, JSON, HTML, images, and more.

---

## How Does A2A Work?

A2A defines a lifecycle and architecture that enables agents to communicate as black boxes.

### 🎭 Actors in A2A

- **User** — The end user initiating the task
- **Client Agent** — Sends the task
- **Remote Agent** — Executes the task

### 🧭 Agent Discovery
Agents expose an `.well-known/agent.json` file called an **Agent Card**, containing:

- Name, description, URL
- Authentication methods
- Supported input/output formats
- Skills and usage examples

### 🧱 Core A2A Concepts

| Object     | Description                                             |
|------------|---------------------------------------------------------|
| Task       | Atomic unit of work                                     |
| Message    | Conversational exchange with optional inputs            |
| Artifact   | Immutable result (e.g., report, JSON file)              |
| Part       | Building block of message/artifact (text, blob, etc.)   |

---

## A2A Workflow Example

Let’s consider a task: _“Schedule a laptop replacement.”_

1. **User submits** the request to a Client Agent.
2. **Client Agent discovers** a Remote Agent using the `.well-known/agent.json` card.
3. **Client sends** a `task/send` request over HTTP+JSON-RPC.
4. **Remote Agent responds**:
   - Completes task (`artifact`)
   - Streams partial updates
   - Requests additional input (`input-required`)
5. **Task state transitions**: submitted → working → input-required → completed

### 🧪 Communication Flow

```plaintext
Client Agent → task/send → Remote Agent
Remote Agent → task/update/task/artifact → Client Agent
```

A2A is stateful via Task objects and async-ready using **Server-Sent Events (SSE)** or **webhooks**.

---

## A2A Example: IT Helpdesk Ticket Resolution

Imagine a user types:

> “My laptop isn’t turning on after the last software update.”

Here’s how agents resolve it using A2A:

1. **Client Agent** initiates task
2. **Hardware Diagnostic Agent** runs checks
3. **Software Rollback Agent** investigates updates
4. **Device Replacement Agent** schedules delivery

✅ Each agent:
- Publishes an Agent Card
- Handles its own logic independently
- Uses artifacts and messages to report results

---

## A2A vs MCP: When to Use What?

A2A and MCP serve **complementary roles** in agent-based systems.

| Feature              | A2A                              | MCP                              |
|----------------------|----------------------------------|----------------------------------|
| Purpose              | Agent-to-agent collaboration     | Agent-to-tool interaction        |
| Based on             | JSON-RPC, SSE                    | JSON Schema + function calling   |
| Handles              | Messages, artifacts              | APIs, tools, functions           |
| Example              | Agents resolving helpdesk ticket | LLM calling OCR, API, DB query   |

> A2A is for agent **coordination**.  
> MCP is for agent **tool usage**.

---

## Conclusion

Agent2Agent (A2A) is the **missing glue** in modern agent systems. It brings:

- Modular and stateless coordination
- Inter-agent messaging and artifact passing
- Discovery via `.well-known` cards
- Vendor-agnostic, secure, and async-ready foundation

Whether you’re building with **Google ADK**, **CrewAI**, or **LangGraph**, A2A helps agents talk to each other — in a structured, scalable, and resilient way.

---

## 📚 Further Reading

- [Google's A2A GitHub](https://github.com/google/a2a)
- [Agent Development Kit (ADK) Travel Planner Guide](https://www.datacamp.com/tutorial/agent-development-kit-adk)
- [CrewAI: Multi-Agent AI with Tools](https://www.datacamp.com/blog/crewai-multi-agent)
