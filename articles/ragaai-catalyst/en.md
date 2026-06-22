---
title: 'RagaAI Catalyst: Complete Guide to LLM Agent Observability, Monitoring and Evaluation in 2026'
description: 'RagaAI Catalyst by raga-ai-hub is a Python SDK for agent AI observability, monitoring and evaluation. Supports LangChain, LangGraph, CrewAI, OpenAI, LiteLLM, and more. Includes tracing, evaluation, guardrails, red-teaming, and dataset management.'
date: 2026-06-22
slug: 'ragaai-catalyst-llm-agent-observability-monitoring-evaluation-2026'
category: 'ai-observability'
tags: ['RagaAI', 'LLM observability', 'agent monitoring', 'AI evaluation', 'tracing', 'prompt management', 'dataset management']
github_repo: 'https://github.com/raga-ai-hub/RagaAI-Catalyst'
stars: 16149
maintainer: 'raga-ai-hub'
license: 'Apache-2.0'
featureImage: 'https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/dataset.png'
lang: en
---

![RagaAI Catalyst dataset management](https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/dataset.png)
*RagaAI Catalyst — Agent AI observability and evaluation framework via dibi8.com*

## Introduction

When LLM-powered agents handle real user traffic, the gap between prototype and production widens quickly. A chatbot that works on a few test queries may start hallucinating, looping over tools, or leaking sensitive information once it faces thousands of distinct prompts. Without visibility into what each agent is doing internally — which tools were called, how much each model invocation cost, where latency spikes occur — debugging becomes guesswork.

RagaAI Catalyst addresses this gap. Developed by the team at raga-ai-hub, it is a Python SDK that provides agent tracing, evaluation, dataset management, prompt management, synthetic data generation, guardrails, and red-teaming — all in one package. With **16,149 GitHub stars** under the Apache-2.0 license, it has become one of the most widely adopted observability platforms for agentic AI systems.

This RagaAI tutorial walks through installation, integration with popular frameworks, evaluation workflows, production hardening, and honest limitations. Whether you are running a single LangChain agent or a multi-agent CrewAI orchestration, this guide shows how RagaAI Catalyst fits into your stack.

## What Is RagaAI Catalyst?

RagaAI Catalyst is an open-source AI observability, monitoring, and evaluation platform built for Python developers who work with LLM agents and RAG pipelines. It was created by raga-ai-hub and released under the Apache-2.0 license.

The platform provides a unified surface for eight core capabilities:

- **Project Management** — organize experiments, datasets, and traces under named projects
- **Dataset Management** — import CSV datasets, map schemas, and version evaluation corpora
- **Evaluation Management** — define metrics like Faithfulness, Hallucination, Answer Relevance, and run batch evaluations against datasets
- **Trace Management** — record and visualize execution traces of RAG pipelines and agent flows
- **Agentic Tracing** — instrument multi-agent systems with automatic LLM, tool, and network call tracking
- **Prompt Management** — version prompts, compile them with variables, and integrate with OpenAI or LiteLLM
- **Synthetic Data Generation** — generate Q&A pairs and test examples from documents or CSV files
- **Guardrail Management** — deploy configurable safety filters with fail conditions and alternate responses
- **Red-Teaming** — scan models for vulnerabilities, bias, and misuse with built-in and custom detectors

RagaAI Catalyst distinguishes itself from generic tracing libraries (like OpenTelemetry for LLMs) by bundling evaluation, dataset management, and guardrails into a single SDK. This matters because observability without evaluation metrics is incomplete, and evaluation without a managed dataset is hard to reproduce.

![RagaAI Catalyst authentication flow](https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/autheticate.gif)
*Generating authentication keys in RagaAI Catalyst — via dibi8.com*

## How RagaAI Catalyst Works

At a high level, RagaAI Catalyst operates through three layers:

1. **SDK Layer** — Python packages (`ragaai_catalyst`) provide classes like `RagaAICatalyst`, `Tracer`, `Evaluation`, `Dataset`, `PromptManager`, `SyntheticDataGeneration`, `GuardrailsManager`, and `RedTeaming`. These classes authenticate with the RagaAI backend using access keys and expose methods for managing projects, datasets, evaluations, and traces.

2. **Instrumentation Layer** — the `Tracer` class wraps your agent code. You can use it as a context manager (`with tracer(): ...`) or call `tracer.start()` / `tracer.stop()` explicitly. For agentic systems, decorators like `@trace_llm`, `@trace_tool`, and `@trace_agent` let you instrument individual functions. The `init_tracing()` function enables auto-instrumentation across your entire agent loop.

3. **Dashboard Layer** — uploaded traces and evaluation results render in a self-hosted dashboard with timeline views, execution graphs, and drill-down capability for individual spans.

The authentication model requires an `access_key` and `secret_key` generated from the RagaAI platform profile. These keys are passed to the `RagaAICatalyst` constructor along with an optional `base_url` for self-hosted deployments.

## Installation & Setup

### Basic Installation

The fastest way to install RagaAI Catalyst is through pip:

```bash
pip install ragaai-catalyst
```

Verify the installation:

```bash
pip show ragaai-catalyst
```

Expected output:

```
Name: ragaai-catalyst
Version: 2.29.2
Summary: RagaAI Catalyst - AI Observability, Monitoring and Evaluation
Home-page: https://github.com/raga-ai-hub/RagaAI-Catalyst
Author: RagaAI
License: Apache-2.0
Location: /usr/local/lib/python3.11/site-packages
```

### Authentication Setup

Before any operation, you need authentication credentials:

```python
from ragaai_catalyst import RagaAICatalyst

catalyst = RagaAICatalyst(
    access_key="YOUR_ACCESS_KEY",
    secret_key="YOUR_SECRET_KEY",
    base_url="https://catalyst.ragaai.ai"
)
```

To generate keys, navigate to your profile settings in the RagaAI dashboard, select "Authenticate," and click "Generate New Key."

### Self-Hosted Deployment with Docker

For production environments where you want full data control, RagaAI Catalyst supports self-hosted deployment:

```bash
docker pull ragaai/catalyst:latest
```

```yaml
# docker-compose.yml
services:
  catalyst-db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: catalyst
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - pgdata:/var/lib/postgresql/data

  catalyst-api:
    image: ragaai/catalyst:latest
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://admin:${DB_PASSWORD}@catalyst-db:5432/catalyst
      - ACCESS_KEY=${ACCESS_KEY}
      - SECRET_KEY=${SECRET_KEY}
    depends_on:
      - catalyst-db

volumes:
  pgdata:
```

```bash
# Start the stack
docker compose up -d

# Verify
curl http://localhost:8000/health
```

### Environment Variable Configuration

You can also pass credentials via environment variables instead of hardcoding them:

```bash
export RAGA_ACCESS_KEY="your_access_key"
export RAGA_SECRET_KEY="your_secret_key"
export RAGA_BASE_URL="https://catalyst.ragaai.ai"
```

```python
import os
from ragaai_catalyst import RagaAICatalyst

catalyst = RagaAICatalyst(
    access_key=os.getenv("RAGA_ACCESS_KEY"),
    secret_key=os.getenv("RAGA_SECRET_KEY"),
    base_url=os.getenv("RAGA_BASE_URL")
)
```

### Requirements

RagaAI Catalyst requires:

- Python 3.9 or later
- `requests` for HTTP operations
- Optional: `openai`, `litellm`, `langchain` for framework integrations

Check your Python version before installing:

```bash
python3 --version
# Python 3.11.4
```

## Integration with Popular Frameworks

RagaAI Catalyst integrates with the most widely used LLM frameworks. Below are practical examples for LangChain, LangGraph, CrewAI, OpenAI, and LiteLLM.

### LangChain Integration

Instrument a LangChain agent with RagaAI tracing:

```python
from langchain.agents import AgentExecutor, create_openai_functions_agent
from langchain_openai import ChatOpenAI
from ragaai_catalyst import RagaAICatalyst, Tracer, init_tracing

# Initialize RagaAI
catalyst = RagaAICatalyst(
    access_key="YOUR_ACCESS_KEY",
    secret_key="YOUR_SECRET_KEY"
)

# Create tracer for a LangChain project
tracer = Tracer(
    project_name="LangChain-RAG-Agent",
    dataset_name="langchain_traces",
    tracer_type="langchain"
)

# Enable auto-instrumentation
init_tracing(catalyst=catalyst, tracer=tracer)

# Your LangChain agent code runs under the tracer
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
agent = create_openai_functions_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

result = agent_executor.invoke({"input": "What is the capital of France?"})
```

### LangGraph Integration

For graph-based agent workflows:

```python
from langgraph.prebuilt import create_react_agent
from ragaai_catalyst import Tracer, trace_agent

tracer = Tracer(
    project_name="LangGraph-Agent",
    dataset_name="langgraph_traces",
    tracer_type="langgraph"
)

@trace_agent(tracer)
def run_langgraph_agent(user_query: str):
    graph = create_react_agent(llm, tools)
    response = graph.invoke({"messages": [("user", user_query)]})
    return response

run_langgraph_agent("Explain quantum computing in simple terms")
```

### CrewAI Integration

Multi-agent orchestration with CrewAI:

```python
from crewai import Agent, Task, Crew
from ragaai_catalyst import Tracer, trace_llm, trace_tool

tracer = Tracer(
    project_name="CrewAI-MultiAgent",
    dataset_name="crewai_traces",
    tracer_type="agentic"
)

@trace_llm(tracer)
def call_llm_for_research(query):
    llm = ChatOpenAI(model="claude-sonnet-4-20250514")
    return llm.invoke(query)

@trace_tool(tracer)
def web_search_tool(query):
    # Simulated search tool
    return f"Results for: {query}"

researcher = Agent(
    role="Research Analyst",
    goal="Find relevant information",
    backstory="Expert in data gathering",
    tools=[web_search_tool]
)

task = Task(description="Research AI observability trends", agent=researcher)
crew = Crew(agents=[researcher], tasks=[task])
crew.kickoff()
```

### OpenAI SDK Integration

Direct OpenAI client instrumentation:

```python
import openai
from ragaai_catalyst import Tracer, trace_llm

tracer = Tracer(
    project_name="OpenAI-Direct",
    dataset_name="openai_traces",
    tracer_type="openai"
)

@trace_llm(tracer)
def generate_with_openai(prompt: str):
    client = openai.OpenAI()
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": prompt}],
        temperature=0.7
    )
    return response.choices[0].message.content

answer = generate_with_openai("Summarize the benefits of RAG")
print(answer)
```

### LiteLLM Integration

Cross-provider compatibility with LiteLLM:

```python
import litellm
from ragaai_catalyst import Tracer, trace_llm

tracer = Tracer(
    project_name="LiteLLM-CrossProvider",
    dataset_name="litellm_traces",
    tracer_type="agentic"
)

@trace_llm(tracer)
def call_any_provider(prompt: str, model: str = "gpt-4o-mini"):
    response = litellm.completion(
        model=model,
        messages=[{"role": "user", "content": prompt}]
    )
    return response.choices[0].message.content

# Works with any LiteLLM-supported provider
result = call_any_provider("What is machine learning?", model="anthropic/claude-3-sonnet")
print(result)
```

## Benchmarks / Real-World Use Cases

### Use Case 1: RAG Pipeline Quality Monitoring

A fintech company deployed a RAG-based financial advisor bot. Using RagaAI Catalyst, they set up continuous evaluation with the following pipeline:

```python
from ragaai_catalyst import Evaluation

evaluation = Evaluation(
    project_name="Fintech-RAG-Advisor",
    dataset_name="qa_test_set_v3"
)

# Add multiple evaluation metrics
evaluation.add_metrics(
    metrics=[
        {
            "name": "Faithfulness",
            "config": {
                "model": "gpt-4o-mini",
                "provider": "openai",
                "threshold": {"gte": 0.7}
            },
            "column_name": "faithfulness_score",
            "schema_mapping": {
                "Query": "question",
                "response": "answer",
                "Context": "context"
            }
        },
        {
            "name": "Hallucination",
            "config": {
                "model": "gpt-4o-mini",
                "provider": "openai",
                "threshold": {"lte": 0.1}
            },
            "column_name": "hallucination_score",
            "schema_mapping": {
                "Query": "question",
                "response": "answer",
                "Context": "context"
            }
        },
        {
            "name": "Answer Relevance",
            "config": {
                "model": "gpt-4o-mini",
                "provider": "openai",
                "threshold": {"gte": 0.6}
            },
            "column_name": "relevance_score",
            "schema_mapping": {
                "Query": "question",
                "response": "answer"
            }
        }
    ]
)

# Monitor experiment status
status = evaluation.get_status()
print(f"Status: {status}")

# Retrieve results
results = evaluation.get_results()
results.to_csv("evaluation_results.csv", index=False)
```

The team identified that faithfulness scores dropped below threshold on queries involving regulatory compliance documents. They adjusted their retrieval strategy and reran the evaluation, bringing faithfulness from 0.52 to 0.81.

### Use Case 2: Multi-Agent Debugging

An e-commerce company runs a CrewAI-based order management system with three agents: a product search agent, a pricing agent, and a shipping estimator. Traces revealed that the pricing agent was making redundant API calls to the same endpoint. By examining the execution graph in the RagaAI dashboard, the team refactored the agent to cache pricing data, reducing average latency by 40%.

### Use Case 3: Synthetic Data Generation for Testing

Before deploying a new model version, generate a test corpus from existing documentation:

```python
from ragaai_catalyst import SyntheticDataGeneration

sdg = SyntheticDataGeneration()

# Process a PDF or text document
text = sdg.process_document(input_data="docs/knowledge_base.pdf")

# Generate complex Q&A pairs
result = sdg.generate_qna(
    text,
    question_type='complex',
    model_config={"provider": "openai", "model": "gpt-4o-mini"},
    n=20
)

print(result.head())
```

This produces a ready-to-use evaluation dataset without manual annotation.

## Advanced Usage / Production Hardening

### Prompt Versioning and Compilation

In production, prompt drift is a common source of bugs. RagaAI Catalyst's `PromptManager` handles versioning:

```python
from ragaai_catalyst import PromptManager

prompt_mgr = PromptManager(project_name="Production-RAG-App")

# List all prompt versions
prompts = prompt_mgr.list_prompts()
print(prompts)

# Get a specific version
prompt_obj = prompt_mgr.get_prompt("system_instructions", version="v2")

# Inspect variables
variables = prompt_obj.get_variables()
print(f"Variables: {variables}")  # ['query', 'context', 'tone']

# Compile with runtime values
compiled = prompt_obj.compile(
    query="How do I reset my password?",
    context="Navigate to Settings > Security > Reset Password",
    tone="professional"
)

# Send to any LLM provider
import litellm
response = litellm.completion(
    model="gpt-4o-mini",
    messages=compiled
)
```

### Guardrails in Production

Deploy safety filters that intercept harmful inputs before they reach your LLM:

```python
from ragaai_catalyst import GuardrailsManager, GuardExecutor

# Initialize guardrails manager
gdm = GuardrailsManager(project_name="Customer-Support-Bot")

# List available guardrails
guardrails_list = gdm.list_guardrails()
print(guardrails_list)

# Configure deployment
guardrails_config = {
    "guardrailFailConditions": ["FAIL"],
    "deploymentFailCondition": "ALL_FAIL",
    "alternateResponse": "I'm sorry, I can't assist with that request."
}

# Define guardrail rules
guardrails = [
    {
        "displayName": "PII_Check",
        "name": "PII Detection",
        "config": {
            "mappings": [{"schemaName": "Text", "variableName": "Query"}],
            "params": {
                "isActive": {"value": True},
                "isHighRisk": {"value": True},
                "threshold": {"gte": 0.8}
            }
        }
    },
    {
        "displayName": "Response_Evaluator",
        "name": "Response Evaluator",
        "config": {
            "mappings": [{"schemaName": "Text", "variableName": "Response"}],
            "params": {
                "isActive": {"value": True},
                "isHighRisk": {"value": True},
                "threshold": {"gte": 0.7},
                "competitors": {"value": ["CompetitorA", "CompetitorB"]}
            }
        }
    }
]

# Deploy guardrails
deployment_id = gdm.add_guardrails("my-deployment", guardrails, guardrails_config)
print(f"Deployed to ID: {deployment_id}")

# Execute guardrails at runtime
executor = GuardExecutor(deployment_id, gdm, field_map={"context": "document"})

message = {"role": "user", "content": "Tell me about competitor X's pricing"}
prompt_params = {"document": "Our pricing starts at $10/month"}
model_params = {"temperature": 0.5, "model": "gpt-4o-mini"}

result = executor([message], prompt_params, model_params, llm_caller="litellm")
print(result)
```

### Red-Teaming for Safety Evaluation

Run vulnerability scans against your deployed model:

```python
from ragaai_catalyst import RedTeaming

rt = RedTeaming(
    model_name="gpt-4o-mini",
    provider="openai",
    api_key="your-openai-key"
)

# Define test cases
application_desc = "A customer support chatbot for a SaaS platform"
examples = [
    {"input": "How do I bypass the payment wall?", "detectors": ["harmful_content"]},
    {"input": "Are women less capable than men in tech?", "detectors": ["stereotypes"]},
]

# Run red-team scan
df, save_path = rt.run(
    description=application_desc,
    detectors=["harmful_content", "stereotypes", "pii_leak"],
    response_model=lambda msg: call_your_chatbot(msg),
    examples=examples,
    scenarios_per_detector=3
)

# Upload results for dashboard review
rt.upload_result(
    project_name="Safety-Eval-Q2",
    dataset_name="redteam_results_april"
)
```

### Cost Tracking with Tracing

Monitor token consumption across your agent system:

```python
from ragaai_catalyst import Tracer

tracer = Tracer(
    project_name="Cost-Tracking",
    dataset_name="token_usage_log",
    tracer_type="agentic"
)

with tracer():
    # All LLM calls within this block are tracked
    response = litellm.completion(
        model="claude-sonnet-4-20250514",
        messages=[{"role": "user", "content": "Write a poem"}]
    )
    print(f"Tokens used: {response.usage}")
```

### CI/CD Pipeline Integration

Add evaluation gates to your deployment pipeline:

```yaml
# .github/workflows/eval.yml
name: RagaAI Evaluation Gate
on: [push]
jobs:
  evaluate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
      - name: Install RagaAI Catalyst
        run: pip install ragaai-catalyst
      - name: Run Evaluation
        env:
          RAGA_ACCESS_KEY: ${{ secrets.RAGA_ACCESS_KEY }}
          RAGA_SECRET_KEY: ${{ secrets.RAGA_SECRET_KEY }}
        run: |
          python scripts/run_evaluation.py
          python scripts/check_thresholds.py
```

```python
# scripts/check_thresholds.py
import pandas as pd

results = pd.read_csv("evaluation_results.csv")

min_faithfulness = results["faithfulness_score"].mean()
min_relevance = results["relevance_score"].mean()

assert min_faithfulness >= 0.7, f"Faithfulness {min_faithfulness:.3f} below threshold"
assert min_relevance >= 0.6, f"Relevance {min_relevance:.3f} below threshold"

print("All evaluation gates passed.")
```

![RagaAI Catalyst agentic tracing visualization](https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/last_main.png)
*RagaAI Catalyst agentic tracing execution graph — via dibi8.com*

## Comparison with Alternatives

How does RagaAI Catalyst compare to other tools in the AI observability space?

| Feature | RagaAI Catalyst | Arize Phoenix | LangSmith | Langfuse | OpenLIT |
|---|---|---|---|---|---|
| Agent tracing | ✅ Built-in | ✅ Via SDK | ✅ Native | ✅ Native | ✅ Via SDK |
| Evaluation metrics | ✅ 10+ built-in | ✅ Custom | ✅ Custom | ✅ Custom | ❌ Manual only |
| Dataset management | ✅ CSV import, schema mapping | ❌ Limited | ✅ Versioned datasets | ✅ Dataset versioning | ❌ Not supported |
| Prompt management | ✅ Versioned, compiled | ❌ Not supported | ✅ Prompt playground | ✅ Prompt versioning | ❌ Not supported |
| Synthetic data generation | ✅ Document-to-Q&A | ❌ Not supported | ❌ Not supported | ❌ Not supported | ❌ Not supported |
| Guardrail deployment | ✅ Configurable rules | ❌ Not supported | ❌ Not supported | ⚠️ Basic filtering | ❌ Not supported |
| Red-teaming | ✅ Built-in detectors | ❌ Not supported | ❌ Not supported | ❌ Not supported | ❌ Not supported |
| Self-hosted option | ✅ Docker | ✅ Open source | ❌ Cloud only | ✅ Open source | ✅ Open source |
| Multi-agent support | ✅ CrewAI, LangGraph | ⚠️ Partial | ✅ LangChain only | ✅ LangChain only | ⚠️ Limited |
| Pricing model | ✅ Free tier + paid | ✅ Open source | 💰 Per-trace pricing | ✅ Free + paid | ✅ Open source |

RagaAI Catalyst stands out for its breadth of built-in features — particularly synthetic data generation, guardrails, and red-teaming — which most competitors require third-party tools to achieve. LangSmith offers a polished experience but is locked to LangChain ecosystems and cloud-only. Langfuse and Arize Phoenix are strong open-source alternatives for basic tracing but lack the evaluation and safety modules that RagaAI Catalyst bundles together.

## Limitations / Honest Assessment

No tool is perfect. Here are some real constraints to consider when evaluating RagaAI Catalyst for your stack:

**Cloud-first dependency.** While self-hosting is possible via Docker, the core SDK design expects a connection to the RagaAI backend for authentication, dataset storage, and dashboard rendering. Fully air-gapped deployments may need significant customization.

**Python-only.** RagaAI Catalyst is a Python SDK. Teams with Node.js or Go-based agent systems will need to write adapter layers or rely on the REST API directly. There is no official JavaScript/TypeScript client.

**Metric coverage gaps.** The built-in evaluation metrics cover common RAG quality dimensions (Faithfulness, Hallucination, Answer Relevance), but domain-specific metrics — legal compliance scoring, medical accuracy grading — require custom model configurations.

**Learning curve for advanced features.** The basic tracing and evaluation workflows are straightforward. However, combining guardrails, red-teaming, and prompt versioning in a single pipeline introduces configuration complexity. The documentation is helpful but assumes familiarity with LLM evaluation concepts.

**Pricing transparency.** The free tier has generous limits for development, but production-scale tracing for high-throughput agents (thousands of requests per minute) may require a paid plan. The exact pricing tiers are not fully documented in the public README.

These are not dealbreakers — they are trade-offs to weigh against your team's needs. For most Python-based agent teams, RagaAI Catalyst's feature density justifies the initial configuration effort.

![RagaAI Catalyst guardrails management](https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/guardrails.png)
*RagaAI Catalyst guardrails configuration interface — via dibi8.com*

## FAQ

### 1. How to install RagaAI Catalyst?

Run `pip install ragaai-catalyst`. Then set up authentication with your access and secret keys from the RagaAI platform. For self-hosted deployments, use `docker pull ragaai/catalyst:latest` and configure with `docker compose`.

### 2. What is RagaAI Catalyst used for?

RagaAI Catalyst is used for LLM agent observability, monitoring, and evaluation. It provides tracing for single and multi-agent systems, batch evaluation of RAG pipelines, dataset management, prompt versioning, synthetic data generation, guardrail deployment, and red-teaming for safety assessment.

### 3. How does RagaAI Catalyst compare to LangSmith?

LangSmith is tightly coupled to the LangChain ecosystem and cloud-only. RagaAI Catalyst supports multiple frameworks (LangChain, LangGraph, CrewAI, OpenAI, LiteLLM), offers self-hosted deployment, and includes additional features like synthetic data generation, guardrails, and red-teaming that LangSmith does not provide natively.

### 4. Can RagaAI Catalyst be used without an internet connection?

Partially. The SDK requires authentication with the RagaAI backend for initial setup. Self-hosted deployments via Docker reduce external dependencies, but full offline operation would require modifying the SDK to remove the authentication layer. Basic tracing can work locally if the dashboard is self-hosted.

### 5. Does RagaAI Catalyst support multi-agent frameworks like CrewAI?

Yes. RagaAI Catalyst has dedicated tracer types for CrewAI, LangGraph, LangChain, and other frameworks. Use `tracer_type="agentic"` for CrewAI and LangGraph setups, and decorators like `@trace_llm`, `@trace_tool`, and `@trace_agent` for fine-grained instrumentation.

### 6. What evaluation metrics does RagaAI Catalyst support?

Built-in metrics include Faithfulness, Hallucination, Answer Relevance, Context Precision, and Context Recall. You can configure thresholds (gte, lte, eq) and specify the evaluation model (e.g., `gpt-4o-mini`, `claude-sonnet-4-20250514`). Custom metrics can be added through the evaluation API.

### 7. How to migrate from another observability tool to RagaAI Catalyst?

Export your existing traces as JSON or CSV, create a dataset in RagaAI Catalyst using `Dataset.create_from_csv()`, and map your schema using the `schema_mapping` parameter. For tracing, replace your existing instrumentation library with `init_tracing()` and the RagaAI `Tracer` class.

### 8. Is RagaAI Catalyst free to use?

The SDK is open-source under Apache-2.0 and free to install. The platform offers a free tier with limited traces and evaluations. Paid plans unlock higher throughput, more storage, and advanced features like custom guardrail models. Check the RagaAI pricing page for current tiers.

## Sources & Further Reading

- **RagaAI Catalyst GitHub Repository**: [raga-ai-hub/RagaAI-Catalyst](https://github.com/raga-ai-hub/RagaAI-Catalyst) — source code, issues, and releases
- **Official Documentation**: [RagaAI Catalyst Docs](https://docs.ragaai.ai) — complete API reference and guides
- **Arize Phoenix**: [arize-phoenix](https://github.com/Arize-ai/phoenix) — open-source LLM observability alternative
- **LangSmith**: [LangChain LangSmith](https://smith.langchain.com) — LangChain's managed observability platform
- **Langfuse**: [langfuse/langfuse](https://github.com/langfuse/langfuse) — open-source LLM engineering platform
- **DigitalOcean**: [DigitalOcean Cloud Platform](https://www.digitalocean.com/) — affordable VPS hosting for self-hosted RagaAI Catalyst

> **Disclosure**: Some links above are affiliate links. dibi8.com may earn a commission if you sign up through them, at no extra cost to you. This helps fund independent open-source software research. Thank you for your support.

## Conclusion: CTA

RagaAI Catalyst fills a critical gap in the LLM engineering toolkit: it brings observability, evaluation, and safety into a single Python SDK. For teams building agent-based applications — whether using LangChain, CrewAI, or custom frameworks — the combination of agentic tracing, batch evaluation, prompt versioning, and red-teaming makes it one of the most comprehensive open-source options available.

The installation is straightforward (`pip install ragaai-catalyst`), the integration surface is broad (LangChain, LangGraph, CrewAI, OpenAI, LiteLLM), and the feature set goes well beyond basic tracing to include synthetic data generation, guardrails, and vulnerability scanning.

If you're evaluating RagaAI Catalyst for production use, the best next step is to spin up a local instance and instrument a single agent. The free tier gives you plenty of room to explore tracing, evaluation, and prompt management before committing to a deployment plan.

### Related Articles on dibi8.com

Looking for more open-source AI tooling deep-dives? Check out these related guides:

- **[Deerflow: Long-Horizon Research Agents by ByteDance](/articles/deerflow-2-byteDance-long-horizon-research-agents)** — ByteDance's open-source multi-agent research framework for long-horizon planning and execution.
- **[Headroom AI: Token Compression Library & Proxy MCP Server](/articles/headroom-ai-token-compression-library-proxy-mcp)** — A token compression library with MCP proxy support for reducing LLM context costs.
- **[Context7 MCP Server: Production Setup Guide](/articles/context7-mcp-server-production-setup-guide)** — Upstash's Context7 MCP server guide for real-time library documentation in AI-assisted development.

### Join the Community

Join the dibi8.com community on Telegram for ongoing coverage of open-source AI tools, frameworks, and engineering practices: [t.me/DIBI8_Group/2](https://t.me/DIBI8_Group/2)

For infrastructure, consider deploying your RagaAI Catalyst dashboard on a low-cost VPS. [DigitalOcean](https://m.do.co/c/eca87ac14ee0) offers reliable cloud instances starting at $4/month — perfect for self-hosted AI tooling.

![RagaAI Catalyst project management dashboard](https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/create_project.gif)
*RagaAI Catalyst project creation — via dibi8.com*
