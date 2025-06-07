---
layout: post
title: "O4-Mini API: Step-by-Step Tutorial With Demo Project"
tags:
  - O4-Mini
  - Research Assistant
  - OpenAI API
  - Tool Use
  - Paper Reviewer
  - Statistics
  - LLM Reasoning
featured_image_thumbnail: "assets/images/posts/o4_mini.png"
featured_image: "assets/images/posts/o4_mini.png"
featured: true
hidden: false
---

**[Read the full article on DataCamp: O4-Mini API – A Step-by-Step Tutorial With Demo Project](https://www.datacamp.com/tutorial/o4-mini-api)**

Learn how to use OpenAI's **o4-mini API** to build a **research paper reviewer**, enhanced with statistical tools like p-value, confidence interval, and effect size calculators.

---

## Why Use O4-Mini?

OpenAI’s **o4-mini** is a reasoning-first, low-latency model ideal for complex evaluations. It provides:

- Strong math and logic performance
- Cost-effective reasoning API calls
- Fast generation—perfect for iterative research tools

---

## Project Overview: Research Paper Reviewer With O4-Mini

This tutorial walks you through building a local tool that:

- Parses a research paper (PDF)
- Highlights flaws, weak arguments, or unsupported claims
- Performs real-time statistical calculations with tool calls
- Outputs a Markdown review summary

---

## Step 1: Get Access to O4-Mini via OpenAI API

- Visit [OpenAI API Keys](https://platform.openai.com/account/api-keys)
- Create a new API key
- Add billing to your account
- Set it as an environment variable:

```bash
export OPENAI_API_KEY="your_key_here"
```

---

## Step 2: Install Dependencies

```bash
pip install openai PyMuPDF tiktoken numpy
```

---

## Step 3: Statistics Helper Code

These helper functions will support reasoning by calculating:

- **p-values** via Welch’s t-test
- **Cohen’s d** for effect size
- **Confidence Intervals**
- **Descriptive stats**

```python
from scipy.stats import ttest_ind, sem, t
import numpy as np

def recalculate_p_value(group1, group2):
    t_stat, p_value = ttest_ind(group1, group2, equal_var=False)
    return {"p_value": round(p_value, 4)}

def compute_cohens_d(group1, group2):
    mean1, mean2 = np.mean(group1), np.mean(group2)
    std1, std2 = np.std(group1, ddof=1), np.std(group2, ddof=1)
    pooled_std = np.sqrt((std1**2 + std2**2) / 2)
    return {"cohens_d": round((mean1 - mean2) / pooled_std, 4)}

def compute_confidence_interval(data, confidence=0.95):
    data = np.array(data)
    mean = np.mean(data)
    margin = sem(data) * t.ppf((1 + confidence) / 2., len(data)-1)
    return {
        "mean": round(mean, 4),
        "confidence_interval": [round(mean - margin, 4), round(mean + margin, 4)],
        "confidence": confidence
    }

def describe_group(data):
    data = np.array(data)
    return {
        "mean": round(np.mean(data), 4),
        "std_dev": round(np.std(data, ddof=1), 4),
        "n": len(data)
    }
```

---

## Step 4: Research Paper Reviewer With Tool Support

### 4.1: PDF Text Extraction

```python
import fitz
def extract_text_from_pdf(path):
    doc = fitz.open(path)
    return "\n".join(page.get_text() for page in doc)
```

---

### 4.2: Chunking Long Texts

```python
import tiktoken
def chunk_text(text, max_tokens=12000):
    encoding = tiktoken.get_encoding("cl100k_base")
    tokens = encoding.encode(text)
    return [encoding.decode(tokens[i:i+max_tokens]) for i in range(0, len(tokens), max_tokens)]
```

---

### 4.3: Tool Mapping and Function Registration

```python
tool_function_map = {
    "recalculate_p_value": recalculate_p_value,
    "compute_cohens_d": compute_cohens_d,
    "compute_confidence_interval": compute_confidence_interval,
    "describe_group": describe_group,
}
```

---

### 4.4: Tool Schema for API Usage

Define the tools so o4-mini knows how to invoke them.

```python
tools = [
    {
        "type": "function",
        "name": "recalculate_p_value",
        "description": "Calculate p-value between two sample groups",
        "parameters": {
            "type": "object",
            "properties": {
                "group1": {"type": "array", "items": {"type": "number"}},
                "group2": {"type": "array", "items": {"type": "number"}}
            },
            "required": ["group1", "group2"]
        }
    },
    ...
]
```

---

### 4.5: Core Review Logic

```python
from openai import OpenAI
client = OpenAI()

def review_text_chunk(chunk):
    response = client.responses.create(
        model="o4-mini",
        reasoning={"effort": "high"},
        input=[
            {"role": "system", "content": "...instructions..."},
            {"role": "user", "content": chunk}
        ],
        tools=tools,
    )
    for item in response.output:
        if getattr(item, "type", None) == "function_call":
            fn = item.function_call
            result = tool_function_map[fn.name](**fn.arguments)
            tool_response = client.responses.create(
                model="o4-mini",
                input=[*response.output, {"role": "tool", "name": fn.name, "content": str(result)}]
            )
            return tool_response.output_text.strip()
    return response.output_text.strip()
```

---

### 4.6: Full Paper Review Pipeline

```python
def review_full_pdf(pdf_path):
    raw_text = extract_text_from_pdf(pdf_path)
    chunks = chunk_text(raw_text)
    results = [review_text_chunk(chunk) for chunk in chunks]
    return "\n\n".join(f"### Chunk {i+1}\n{r}" for i, r in enumerate(results))
```

---

### 4.7: Entry Point

```python
if __name__ == "__main__":
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument("pdf_path", help="Path to the PDF")
    args = parser.parse_args()
    output = review_full_pdf(args.pdf_path)
    with open("paper_review_output.md", "w") as f:
        f.write(output)
```

---

## Conclusion

We built a tool that not only reads and critiques a paper using OpenAI’s **o4-mini API**, but also validates claims using statistical tools like **p-values**, **effect sizes**, and **confidence intervals**. This makes your assistant more rigorous and fact-driven.

For more OpenAI-powered projects, check out:

- [O3 API: Multi-step Reasoning With Images](https://www.datacamp.com/tutorial/o3-api)
- [GPT-4.1 Guide: Keyword Code Search](https://www.datacamp.com/tutorial/gpt-4.1)
- [Codex CLI Assistant for Devs](https://www.datacamp.com/tutorial/codex-cli)

