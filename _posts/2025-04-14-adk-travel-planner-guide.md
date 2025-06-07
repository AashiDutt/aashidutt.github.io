from pathlib import Path

# Define ADK blog metadata
adk_markdown_header = """---
layout: post
title: "Google's Agent Development Kit (ADK): A Guide With Demo Project"
tags:
  - Google ADK
  - Agent2Agent
  - Multi-Agent Systems
  - Travel Planner
  - OpenAI API
  - FastAPI
  - Streamlit
featured_image_thumbnail: "assets/images/posts/adk.png"
featured_image: "assets/images/posts/adk.png"
featured: true
hidden: false
---

**[Read the full article on DataCamp: Google's Agent Development Kit (ADK) – A Guide With Demo Project](https://www.datacamp.com/tutorial/agent-development-kit-adk)**

Learn how to build a multi-agent **travel assistant** using **Google's ADK** and the **Agent2Agent (A2A)** protocol with **FastAPI** and **Streamlit**.

---
"""
# Google's Agent Development Kit (ADK): A Guide With Demo Project

Learn how to build a multi-agent travel assistant using Google's Agent Development Kit (ADK) and the Agent2Agent (A2A) protocol.  
*Apr 14, 2025 · 12 min read*

## Contents
- What Is Google’s Agent Development Kit (ADK)?
- Project Overview: AI-Powered Travel Planner With ADK and A2A
- Step 1: Prerequisites
- Step 2: Shared Schema and Utilities
- Step 3: Building the Multi-Agent System with ADK and A2A
- Step 4: Coordinating with host_agent
- Step 5: Building the UI With Streamlit
- Conclusion

---

## What Is Google’s Agent Development Kit (ADK)?
Google's ADK is a modular, production-ready framework for building LLM-powered agents used in real-world Google products. It enables structured, model-agnostic, multi-agent systems with powerful orchestration, memory, and streaming capabilities.

### Key Features
- **Multi-agent by design**: Compose agents in parallel, sequential, or hierarchical flows.
- **Model-agnostic**: Compatible with Gemini, GPT-4o, Claude, Mistral (via LiteLlm).
- **Streaming-ready**: Supports bidirectional real-time interactions.
- **Deployment-ready**: Easily containerized for platforms like GCP or Railway.

## What is Google’s Agent2Agent (A2A) Protocol?
A vendor-neutral HTTP-based protocol to enable communication between AI agents. Agents expose `/run` endpoints and `.well-known/agent.json` for metadata, supporting interoperability with orchestrators like LangGraph or CrewAI.

---

## Project Overview: AI-Powered Travel Planner With ADK and A2A

This demo uses:
- `flight_agent`: Suggests flights
- `stay_agent`: Suggests hotels
- `activities_agent`: Suggests activities
- `host_agent`: Orchestrates them
- `streamlit_app.py`: Frontend UI

All agents run independently and communicate via HTTP using A2A-compatible endpoints.

---

## Step 1: Prerequisites

```bash
pip install google-adk litellm fastapi uvicorn httpx pydantic openai streamlit
export OPENAI_API_KEY="your_key_here"
```

---

## Step 2: Shared Schema and Utilities

### `shared/schemas.py`
```python
from pydantic import BaseModel
class TravelRequest(BaseModel):
    destination: str
    start_date: str
    end_date: str
    budget: float
```

### `common/a2a_client.py`
```python
import httpx
async def call_agent(url, payload):
    async with httpx.AsyncClient() as client:
        response = await client.post(url, json=payload, timeout=60.0)
        response.raise_for_status()
        return response.json()
```

### `common/a2a_server.py`
```python
from fastapi import FastAPI
def create_app(agent):
    app = FastAPI()
    @app.post("/run")
    async def run(payload: dict):
        return await agent.execute(payload)
    return app
```

---

## Step 3: Building the Multi-Agent System

### Folder Structure
```
agents/
├── host_agent/
├── flight_agent/
├── stay_agent/
└── activities_agent/
```

### Example Agent (`activities_agent/agent.py`)
```python
from google.adk.agents import Agent
from google.adk.models.lite_llm import LiteLlm
activities_agent = Agent(
    name="activities_agent",
    model=LiteLlm("openai/gpt-4o"),
    description="Suggests activities at the destination",
    instruction="Suggest 2-3 activities based on destination, dates, and budget."
)
```

### Runner Setup & `execute()` Logic
```python
from google.adk.runners import Runner
from google.adk.sessions import InMemorySessionService
session_service = InMemorySessionService()
runner = Runner(agent=activities_agent, app_name="activities_app", session_service=session_service)

async def execute(request):
    prompt = f"...construct using request..."
    # Stream and parse structured JSON output
```

---

## Step 4: Coordinating with `host_agent`

### `host_agent/task_manager.py`
```python
from common.a2a_client import call_agent
async def run(payload):
    flights = await call_agent("http://localhost:8001/run", payload)
    stay = await call_agent("http://localhost:8002/run", payload)
    activities = await call_agent("http://localhost:8003/run", payload)
    return {
        "flights": flights.get("flights", "..."),
        "stay": stay.get("stays", "..."),
        "activities": activities.get("activities", "...")
    }
```

---

## Step 5: Building the UI With Streamlit

### `streamlit_app.py`
```python
import streamlit as st, requests
st.title("ADK-Powered Travel Planner")
# Input form ...
if st.button("Plan My Trip ✨"):
    payload = {...}
    response = requests.post("http://localhost:8000/run", json=payload)
    # Display flights, stays, activities
```

---

## Run the Complete System
```bash
uvicorn agents.host_agent.__main__:app --port 8000 &
uvicorn agents.flight_agent.__main__:app --port 8001 &
uvicorn agents.stay_agent.__main__:app --port 8002 &
uvicorn agents.activities_agent.__main__:app --port 8003 &
streamlit run streamlit_app.py
```

---

## Final File Structure
```
ADK_demo/
├── agents/
│   ├── host_agent/
│   ├── flight_agent/
│   ├── stay_agent/
│   └── activities_agent/
├── common/
│   ├── a2a_client.py
│   └── a2a_server.py
├── shared/
│   └── schemas.py
├── streamlit_app.py
├── requirements.txt
└── README.md
```

---

## Conclusion

This demo shows how to use Google’s ADK and A2A protocol to build scalable, modular multi-agent systems using LLMs. Agents run independently, communicate using HTTP and schemas, and are orchestrated into a seamless experience using Streamlit.

🔗 [A2A GitHub](https://github.com/google/a2a)  
🔗 [Build a Weather Bot with ADK](https://github.com/google/a2a/tree/main/examples/weather-bot)  
🔗 [Agent Development Kit](https://www.datacamp.com/tutorial/agent-development-kit-adk)
