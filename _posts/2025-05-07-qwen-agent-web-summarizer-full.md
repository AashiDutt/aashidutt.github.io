---
layout: post
title: "Qwen-Agent: A Guide With Demo Project"
tags:
  - Qwen-Agent
  - Qwen3
  - Ollama
  - FastAPI
  - Chrome Extension
  - Summarizer Agent
  - Real-Time LLM
featured_image_thumbnail: "assets/images/posts/qwen_agent_extension/qwen_agent_extension_thumbnail.png"
featured_image: "assets/images/posts/qwen_agent_extension/qwen_agent_extension_thumbnail.png"
featured: true
hidden: false
---

**[Read the full article on DataCamp: Qwen-Agent – A Guide With Demo Project](https://www.datacamp.com/tutorial/qwen-agent)**

In this tutorial, you’ll learn how to build a real-time browser extension that summarizes any webpage using Qwen-Agent and Qwen3, all running locally with Ollama. No cloud APIs needed.

---

## 🔍 What Is Qwen-Agent?

Qwen-Agent is Alibaba’s lightweight agent framework for building powerful LLM applications using Qwen3 models. Key features include:

- Tool/function calling (e.g., code interpreter)
- Multi-turn planning and memory
- Streaming support
- Local and API-based model backends

You can combine it with FastAPI and Ollama to build offline, modular, and fast agents.

---

## ⚙️ Project Overview: Real-Time Summarizer

We’ll build a browser extension that:

- Extracts visible text from any webpage
- Sends it to a FastAPI backend powered by Qwen3
- Streams a clean summary back into the popup UI

### Tech Stack

- **Backend**: Qwen-Agent + FastAPI + Ollama (Qwen3:1.7B)
- **Frontend**: Chrome Extension (manifest v3)
- **Streaming UI**: JavaScript with `ReadableStream`

---

## 🚀 Step 1: Backend Setup

### 1.1 Dependencies

Create `requirements.txt`:

```text
fastapi
uvicorn
python-dateutil
python-dotenv
qwen-agent[code_interpreter]
matplotlib
```

Install:

```bash
pip install -r requirements.txt
```

### 1.2 (Optional) Dockerfile

```dockerfile
FROM python:3.10
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 7864
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "7864"]
```

### 1.3 Run Qwen3 with Ollama

```bash
ollama pull qwen3:1.7b
ollama serve
```

### 1.4 FastAPI Server with Streaming

Example `app.py`:

```python
from fastapi import FastAPI, Request
from fastapi.responses import StreamingResponse
from pydantic import BaseModel
from qwen_agent.agents import Assistant
import re

app = FastAPI()

class RequestData(BaseModel):
    content: str

bot = Assistant(
    llm={"model": "qwen3:1.7b", "model_server": "http://localhost:11434/v1", "api_key": "EMPTY"},
    function_list=["code_interpreter"],
    system_message="You are a summarization assistant. Output only a clean final summary."
)

@app.post("/summarize_stream_status")
async def summarize_stream_status(data: RequestData):
    user_input = data.content

    def stream():
        yield "🔍 Reading content...
"
        messages = [
            {"role": "system", "content": bot.system_message},
            {"role": "user", "content": f"<nothink>
Summarize this:

{user_input}
</nothink>"}
        ]
        yield "🧠 Generating summary...
"
        result = bot.run(messages)
        result_list = list(result)
        summary = None
        for item in reversed(result_list):
            if isinstance(item, list):
                for sub in reversed(item):
                    if isinstance(sub, dict) and "content" in sub:
                        summary = sub["content"]
                        break
            if summary:
                break
        summary = re.sub(r"</?think>", "", summary or "").strip()
        yield f"
📄 Summary:
{summary}
"

    return StreamingResponse(stream(), media_type="text/plain")
```

---

## 🧩 Step 2: Chrome Extension

Folder structure:

```
extension/
├── background.js
├── content.js
├── icon.png
├── manifest.json
├── popup.html
└── popup.js
```

### 2.1 manifest.json

```json
{
  "manifest_version": 3,
  "name": "Web Summarizer",
  "version": "1.0",
  "description": "Summarize webpage content using local Qwen3.",
  "permissions": ["scripting", "activeTab", "storage"],
  "host_permissions": ["<all_urls>"],
  "action": {
    "default_popup": "popup.html",
    "default_icon": "icon.png"
  },
  "background": {
    "service_worker": "background.js"
  },
  "content_scripts": [
    {
      "matches": ["<all_urls>"],
      "js": ["content.js"]
    }
  ]
}
```

### 2.2 popup.html

```html
<!DOCTYPE html>
<html>
<head><meta charset="UTF-8"><title>Web Summarizer</title></head>
<body>
  <h2>Summarize This Page</h2>
  <button id="summarizeBtn">Summarize</button>
  <pre id="output">Summary will appear here...</pre>
  <script src="popup.js"></script>
</body>
</html>
```

### 2.3 popup.js

```javascript
document.getElementById('summarizeBtn').addEventListener('click', async () => {
  const [tab] = await chrome.tabs.query({ active: true, currentWindow: true });
  chrome.scripting.executeScript(
    {
      target: { tabId: tab.id },
      func: () => document.body?.innerText || 'EMPTY'
    },
    async (results) => {
      const pageText = results?.[0]?.result || '';
      const res = await fetch('http://127.0.0.1:7864/summarize_stream_status', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ content: pageText })
      });
      const reader = res.body.getReader();
      const decoder = new TextDecoder();
      let resultText = '';
      while (true) {
        const { done, value } = await reader.read();
        if (done) break;
        resultText += decoder.decode(value, { stream: true });
        document.getElementById('output').innerText = resultText;
      }
    }
  );
});
```

### 2.4 content.js

```javascript
chrome.runtime.onMessage.addListener((request, sender, sendResponse) => {
  if (request.type === "GET_PAGE_TEXT") {
    sendResponse({ text: document.body.innerText.trim() || null });
  }
});
```

---

## 🧪 Step 3: Running the App

Run FastAPI backend locally:

```bash
uvicorn app:app --host 0.0.0.0 --port 7864
```

Or using Docker:

```bash
docker build -t qwen-agent-backend .
docker run -p 7864:7864 qwen-agent-backend
```

Load the extension in Chrome via “Load Unpacked” and select the `extension/` folder.

---

## ✅ Conclusion

You now have a fully offline, real-time webpage summarization extension using Qwen-Agent + Qwen3. This architecture can be adapted for:

- Document readers
- Browser copilots
- Local research agents

For more tutorials:

- [Types of AI Agents](https://www.datacamp.com/tutorial/agent-types)
- [Qwen3 with Ollama](https://www.datacamp.com/tutorial/qwen3-ollama)
- [LLM Agent Patterns](https://www.datacamp.com/tutorial/agentic-patterns)
