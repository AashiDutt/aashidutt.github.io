---
layout: post
title: "LangManus: A Guide With Demo Project"
tags:
  - AI Agents
  - LangManus
  - GitHub
  - Streamlit
  - Multi-Agent
  - Python
  - Visualization
featured_image_thumbnail: "assets/images/posts/langmanus_guide/langmanus_thumbnail.png"
featured_image: "assets/images/posts/langmanus_guide/langmanus_thumbnail.png"
featured: true
hidden: false
---

**[Read the full article on DataCamp: LangManus - A Guide With Demo Project](https://www.datacamp.com/tutorial/langmanus-github-analyzer)**

Learn how to build a multi-agent system using **LangManus** to analyze a trending GitHub repository, scrape its commit history, visualize activity trends, and generate a markdown report.

---

## Overview

**LangManus** is an open-source, community-driven AI automation framework designed for building structured, multi-agent pipelines powered by LLMs. It orchestrates tools like LangGraph, LiteLLM, and browser-based agents to perform tasks such as planning, research, scraping, code analysis, and reporting.

---

## Project Overview: GitHub Repository Analyzer

We’ll build an interactive **LangManus-powered assistant** that:

- Finds a trending open-source GitHub repo
- Scrapes its commit activity
- Analyzes feature updates and contributions
- Visualizes trends through charts
- Generates a markdown report

### Structure

```
LangManus-GitHub-Demo/
├── main.py
├── agent.py
├── planner.py
├── streamlit_app.py
├── agents/
│   ├── researcher.py
│   ├── browser.py
│   ├── coder.py
│   └── reporter.py
├── commit_chart.png
├── category_chart.png
├── topics_chart.png
```

---

## Step-by-Step Implementation

### Step 1: Prerequisites

```bash
python3 --version  # Ensure Python 3.8+
pip install requests beautifulsoup4 matplotlib streamlit
export GITHUB_TOKEN=your_personal_token
```

You’ll need a GitHub token to avoid rate limits.

---

### Step 2: Creating the Planner and Agent Controller

#### `planner.py`

```python
def plan_task(user_query):
    return [
        {'agent': 'researcher', 'task': 'Find trending repo'},
        {'agent': 'browser', 'task': 'Scrape GitHub activity'},
        {'agent': 'coder', 'task': 'Analyze recent commits and features'},
        {'agent': 'reporter', 'task': 'Generate Markdown report'}
    ]
```

#### `agent.py`

```python
class LangManusAgent:
    def __init__(self, task):
        ...
    def run(self):
        ...
    def run_and_return(self):
        ...
```

It uses the planner to execute a chain of agents with shared context.

---

### Step 3: Implementing LangManus Agents

#### `agents/researcher.py`

Scrapes the trending GitHub Python repo using BeautifulSoup.

#### `agents/browser.py`

Fetches recent commit history via GitHub REST API and extracts messages, authors, and timestamps.

#### `agents/coder.py`

Analyzes the commit history and generates:

- 📈 Commit frequency chart
- 🧩 Commits by category
- 🔥 Most frequent words in messages

#### `agents/reporter.py`

Generates a markdown report combining:

- Repository link
- Recent commit messages
- Summary by commit type

---

## Step 4: Streamlit UI

#### `streamlit_app.py`

```python
if st.button("🔍 Run Analysis on Trending Repo"):
    ...
    st.markdown(report)
    for path in chart_paths:
        st.image(Image.open(path))
```

Streamlit dashboard to trigger agents and display the markdown + charts.

---

## Step 5: Run the App

```bash
streamlit run streamlit_app.py
```

---

## Demo Screenshots

- 📋 Markdown report
- 📊 Commit activity over 30 days
- 🔍 Category and topic analysis
- 🖼 Charts generated using matplotlib

---

## Conclusion

In this project, we demonstrated the power of **LangManus** to orchestrate a chain of agents to perform real-world analysis on a trending GitHub repo.

We:

- 🔎 Researched trending projects
- 📤 Scraped commit data
- 📊 Visualized contribution patterns
- 📝 Generated structured markdown summaries

LangManus is ideal for building agents that research, analyze, and report from real-world data pipelines.

---

**Explore the full tutorial on DataCamp**  
👉 [Click here to read it](https://www.datacamp.com/tutorial/langmanus-github-analyzer)
