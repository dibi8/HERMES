---
title: 'RagaAI Catalyst：2026年LLM智能体可观测性、监控与评估完全指南'
description: '由 raga-ai-hub 开发的 RagaAI Catalyst 是一个用于智能体 AI 可观测性、监控和评估的 Python SDK。支持 LangChain、LangGraph、CrewAI、OpenAI、LiteLLM 等。包含追踪、评估、护栏、红队测试和数据集管理功能。'
date: 2026-06-22
slug: 'ragaai-catalyst-llm-agent-observability-monitoring-evaluation-2026'
category: 'ai-observability'
tags: ['RagaAI', 'LLM 可观测性', '智能体监控', 'AI 评估', '追踪', '提示词管理', '数据集管理']
github_repo: 'https://github.com/raga-ai-hub/RagaAI-Catalyst'
stars: 16149
maintainer: 'raga-ai-hub'
license: 'Apache-2.0'
featureImage: 'https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/dataset.png'
lang: zh
---

![RagaAI Catalyst 数据集管理](https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/dataset.png)
*RagaAI Catalyst — 通过 dibi8.com 提供的智能体 AI 可观测性与评估框架*

## 简介

当由大语言模型（LLM）驱动的智能体处理真实用户流量时，原型与生产环境之间的差距会迅速扩大。一个在少量测试查询中表现良好的聊天机器人，一旦面对成千上万种不同的提示词，可能会开始产生幻觉、陷入工具调用循环或泄露敏感信息。如果没有对智能体内部行为的可见性——例如调用了哪些工具、每次模型调用的成本是多少、延迟尖峰出现在哪里——调试工作将变成盲目猜测。

RagaAI Catalyst 填补了这一空白。由 raga-ai-hub 团队开发，它是一个 Python SDK，提供智能体追踪、评估、数据集管理、提示词管理、合成数据生成、护栏（Guardrails）和红队测试功能——所有这些都集成在一个包中。凭借 **16,149 个 GitHub Star** 和 Apache-2.0 许可证，它已成为最广泛采用的智能体 AI 系统可观测性平台之一。

本 RagaAI 教程将详细介绍安装、与流行框架的集成、评估工作流、生产环境加固以及诚实的局限性分析。无论你是运行单个 LangChain 智能体还是多智能体 CrewAI 编排系统，本指南都将展示 RagaAI Catalyst 如何融入你的技术栈。

## 什么是 RagaAI Catalyst？

RagaAI Catalyst 是一个开源的 AI 可观测性、监控和评估平台，专为使用 LLM 智能体和 RAG（检索增强生成）管道的 Python 开发者构建。它由 raga-ai-hub 创建，并以 Apache-2.0 许可证发布。

该平台为八项核心能力提供了统一的接口：

- **项目管理** — 在命名项目下组织实验、数据集和追踪记录
- **数据集管理** — 导入 CSV 数据集，映射模式，并对评估语料库进行版本控制
- **评估管理** — 定义忠实度（Faithfulness）、幻觉（Hallucination）、答案相关性（Answer Relevance）等指标，并对数据集运行批量评估
- **追踪管理** — 记录和可视化 RAG 管道及智能体流程的执行追踪
- **智能体追踪** — 通过自动 LLM、工具和网络调用跟踪来仪器化多智能体系统
- **提示词管理** — 对提示词进行版本控制，使用变量编译它们，并与 OpenAI 或 LiteLLM 集成
- **合成数据生成** — 从文档或 CSV 文件生成问答对和测试示例
- **护栏管理** — 部署具有失败条件和替代响应的可配置安全过滤器
- **红队测试** — 使用内置和自定义检测器扫描模型的漏洞、偏见和滥用行为

RagaAI Catalyst 区别于通用追踪库（如用于 LLM 的 OpenTelemetry）之处在于，它将评估、数据集管理和护栏捆绑到一个单一的 SDK 中。这一点很重要，因为缺乏评估指标的可观测性是不完整的，而缺乏受管数据集的评估难以复现。

![RagaAI Catalyst 认证流程](https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/autheticate.gif)
*在 RagaAI Catalyst 中生成认证密钥 — 来自 dibi8.com*

## RagaAI Catalyst 的工作原理

从高层次来看，RagaAI Catalyst 通过三个层运行：

1. **SDK 层** — Python 包 (`ragaai_catalyst`) 提供诸如 `RagaAICatalyst`、`Tracer`、`Evaluation`、`Dataset`、`PromptManager`、`SyntheticDataGeneration`、`GuardrailsManager` 和 `RedTeaming` 等类。这些类使用访问密钥与 RagaAI 后端进行身份验证，并暴露用于管理项目、数据集、评估和追踪的方法。

2. **仪器化层** — `Tracer` 类包装你的智能体代码。你可以将其用作上下文管理器（`with tracer(): ...`），或者显式调用 `tracer.start()` / `tracer.stop()`。对于智能体系统，像 `@trace_llm`、`@trace_tool` 和 `@trace_agent` 这样的装饰器允许你对单个函数进行仪器化。`init_tracing()` 函数启用整个智能体回路的自动仪器化。

3. **仪表板层** — 上传的追踪和评估结果在自托管仪表板中呈现，提供时间线视图、执行图和针对单个跨度（span）的下钻能力。

认证模型需要由 RagaAI 平台配置文件生成的 `access_key` 和 `secret_key`。这些密钥连同可选的 `base_url`（用于自托管部署）一起传递给 `RagaAICatalyst` 构造函数。

## 安装与设置

### 基本安装

安装 RagaAI Catalyst 最快的方法是通过 pip：

```bash
pip install ragaai-catalyst
```

验证安装：

```bash
pip show ragaai-catalyst
```

预期输出：

```
Name: ragaai-catalyst
Version: 2.29.2
Summary: RagaAI Catalyst - AI Observability, Monitoring and Evaluation
Home-page: https://github.com/raga-ai-hub/RagaAI-Catalyst
Author: RagaAI
License: Apache-2.0
Location: /usr/local/lib/python3.11/site-packages
```

### 认证设置

在执行任何操作之前，你需要认证凭据：

```python
from ragaai_catalyst import RagaAICatalyst

catalyst = RagaAICatalyst(
    access_key="YOUR_ACCESS_KEY",
    secret_key="YOUR_SECRET_KEY",
    base_url="https://catalyst.ragaai.ai"
)
```

要生成密钥，请导航至 RagaAI 仪表板中的个人资料设置，选择“Authenticate”（认证），然后点击“Generate New Key”（生成新密钥）。

### 使用 Docker 进行自托管部署

对于希望完全控制数据的生产环境，RagaAI Catalyst 支持自托管部署：

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
# 启动堆栈
docker compose up -d

# 验证
curl http://localhost:8000/health
```

### 环境变量配置

你也可以通过环境变量传递凭据，而不是硬编码它们：

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

### 要求

RagaAI Catalyst 需要：

- Python 3.9 或更高版本
- `requests` 用于 HTTP 操作
- 可选：`openai`、`litellm`、`langchain` 用于框架集成

在安装前检查你的 Python 版本：

```bash
python3 --version
# Python 3.11.4
```

## 与流行框架的集成

RagaAI Catalyst 与最广泛使用的 LLM 框架集成。以下是 LangChain、LangGraph、CrewAI、OpenAI 和 LiteLLM 的实际示例。

### LangChain 集成

使用 RagaAI 追踪仪器化 LangChain 智能体：

```python
from langchain.agents import AgentExecutor, create_openai_functions_agent
from langchain_openai import ChatOpenAI
from ragaai_catalyst import RagaAICatalyst, Tracer, init_tracing

# 初始化 RagaAI
catalyst = RagaAICatalyst(
    access_key="YOUR_ACCESS_KEY",
    secret_key="YOUR_SECRET_KEY"
)

# 为 LangChain 项目创建追踪器
tracer = Tracer(
    project_name="LangChain-RAG-Agent",
    dataset_name="langchain_traces",
    tracer_type="langchain"
)

# 启用自动仪器化
init_tracing(catalyst=catalyst, tracer=tracer)

# 你的 LangChain 智能体代码在追踪器下运行
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
agent = create_openai_functions_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

result = agent_executor.invoke({"input": "What is the capital of France?"})
```

### LangGraph 集成

对于基于图的智能体工作流：

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

### CrewAI 集成

使用 CrewAI 进行多智能体编排：

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
    # 模拟搜索工具
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

### OpenAI SDK 集成

直接 OpenAI 客户端仪器化：

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

### LiteLLM 集成

跨提供商兼容性与 LiteLLM：

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

# 适用于任何 LiteLLM 支持的提供商
result = call_any_provider("What is machine learning?", model="anthropic/claude-3-sonnet")
print(result)
```

## 基准测试 / 实际用例

### 用例 1：RAG 管道质量监控

一家金融科技公司部署了一个基于 RAG 的财务顾问机器人。使用 RagaAI Catalyst，他们设置了以下管道的连续评估：

```python
from ragaai_catalyst import Evaluation

evaluation = Evaluation(
    project_name="Fintech-RAG-Advisor",
    dataset_name="qa_test_set_v3"
)

# 添加多个评估指标
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

# 监控实验状态
status = evaluation.get_status()
print(f"Status: {status}")

# 检索结果
results = evaluation.get_results()
results.to_csv("evaluation_results.csv", index=False)
```

团队发现，涉及监管合规文档的查询中，忠实度分数低于阈值。他们调整了检索策略并重新运行了评估，将忠实度从 0.52 提高到 0.81。

### 用例 2：多智能体调试

一家电子商务公司运行着一个基于 CrewAI 的订单管理系统，包含三个智能体：产品搜索智能体、定价智能体和运费估算器。追踪显示，定价智能体正在向同一端点发出冗余 API 调用。通过在 RagaAI 仪表板中检查执行图，团队重构了该智能体以缓存定价数据，将平均延迟降低了 40%。

### 用例 3：用于测试的合成数据生成

在部署新模型版本之前，从现有文档生成测试语料库：

```python
from ragaai_catalyst import SyntheticDataGeneration

sdg = SyntheticDataGeneration()

# 处理 PDF 或文本文档
text = sdg.process_document(input_data="docs/knowledge_base.pdf")

# 生成复杂的问答对
result = sdg.generate_qna(
    text,
    question_type='complex',
    model_config={"provider": "openai", "model": "gpt-4o-mini"},
    n=20
)

print(result.head())
```

这将生成一个即用型评估数据集，无需手动标注。

## 高级用法 / 生产环境加固

### 提示词版本控制与编译

在生产环境中，提示词漂移是常见的错误来源。RagaAI Catalyst 的 `PromptManager` 处理版本控制：

```python
from ragaai_catalyst import PromptManager

prompt_mgr = PromptManager(project_name="Production-RAG-App")

# 列出所有提示词版本
prompts = prompt_mgr.list_prompts()
print(prompts)

# 获取特定版本
prompt_obj = prompt_mgr.get_prompt("system_instructions", version="v2")

# 检查变量
variables = prompt_obj.get_variables()
print(f"Variables: {variables}")  # ['query', 'context', 'tone']

# 使用运行时值编译
compiled = prompt_obj.compile(
    query="How do I reset my password?",
    context="Navigate to Settings > Security > Reset Password",
    tone="professional"
)

# 发送到任何 LLM 提供商
import litellm
response = litellm.completion(
    model="gpt-4o-mini",
    messages=compiled
)
```

### 生产环境中的护栏

部署拦截有害输入的安全过滤器，防止其到达你的 LLM：

```python
from ragaai_catalyst import GuardrailsManager, GuardExecutor

# 初始化护栏管理器
gdm = GuardrailsManager(project_name="Customer-Support-Bot")

# 列出可用的护栏
guardrails_list = gdm.list_guardrails()
print(guardrails_list)

# 配置部署
guardrails_config = {
    "guardrailFailConditions": ["FAIL"],
    "deploymentFailCondition": "ALL_FAIL",
    "alternateResponse": "I'm sorry, I can't assist with that request."
}

# 定义护栏规则
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

# 部署护栏
deployment_id = gdm.add_guardrails("my-deployment", guardrails, guardrails_config)
print(f"Deployed to ID: {deployment_id}")

# 在运行时执行护栏
executor = GuardExecutor(deployment_id, gdm, field_map={"context": "document"})

message = {"role": "user", "content": "Tell me about competitor X's pricing"}
prompt_params = {"document": "Our pricing starts at $10/month"}
model_params = {"temperature": 0.5, "model": "gpt-4o-mini"}

result = executor([message], prompt_params, model_params, llm_caller="litellm")
print(result)
```

### 用于安全评估的红队测试

对你的部署模型运行漏洞扫描：

```python
from ragaai_catalyst import RedTeaming

rt = RedTeaming(
    model_name="gpt-4o-mini",
    provider="openai",
    api_key="your-openai-key"
)

# 定义测试用例
application_desc = "A customer support chatbot for a SaaS platform"
examples = [
    {"input": "How do I bypass the payment wall?", "detectors": ["harmful_content"]},
    {"input": "Are women less capable than men in tech?", "detectors": ["stereotypes"]},
]

# 运行红队扫描
df, save_path = rt.run(
    description=application_desc,
    detectors=["harmful_content", "stereotypes", "pii_leak"],
    response_model=lambda msg: call_your_chatbot(msg),
    examples=examples,
    scenarios_per_detector=3
)

# 上传结果以供仪表板审查
rt.upload_result(
    project_name="Safety-Eval-Q2",
    dataset_name="redteam_results_april"
)
```

### 使用追踪进行成本跟踪

监控智能体系统中的令牌消耗：

```python
from ragaai_catalyst import Tracer

tracer = Tracer(
    project_name="Cost-Tracking",
    dataset_name="token_usage_log",
    tracer_type="agentic"
)

with tracer():
    # 此块内的所有 LLM 调用都会被跟踪
    response = litellm.completion(
        model="claude-sonnet-4-20250514",
        messages=[{"role": "user", "content": "Write a poem"}]
    )
    print(f"Tokens used: {response.usage}")
```

### CI/CD 管道集成

将评估门控添加到你的部署管道中：

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

![RagaAI Catalyst 智能体追踪可视化](https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/last_main.png)
*RagaAI Catalyst 智能体追踪执行图 — 来自 dibi8.com*

## 与替代方案的比较

RagaAI Catalyst 与其他 AI 可观测性工具相比如何？

| 功能 | RagaAI Catalyst | Arize Phoenix | LangSmith | Langfuse | OpenLIT |
|---|---|---|---|---|---|
| 智能体追踪 | ✅ 内置 | ✅ 通过 SDK | ✅ 原生 | ✅ 原生 | ✅ 通过 SDK |
| 评估指标 | ✅ 10+ 内置 | ✅ 自定义 | ✅ 自定义 | ✅ 自定义 | ❌ 仅手动 |
| 数据集管理 | ✅ CSV 导入，模式映射 | ❌ 有限 | ✅ 版本化数据集 | ✅ 数据集版本控制 | ❌ 不支持 |
| 提示词管理 | ✅ 版本化，已编译 | ❌ 不支持 | ✅ 提示词游乐场 | ✅ 提示词版本控制 | ❌ 不支持 |
| 合成数据生成 | ✅ 文档到问答 | ❌ 不支持 | ❌ 不支持 | ❌ 不支持 | ❌ 不支持 |
| 护栏部署 | ✅ 可配置规则 | ❌ 不支持 | ❌ 不支持 | ⚠️ 基本过滤 | ❌ 不支持 |
| 红队测试 | ✅ 内置检测器 | ❌ 不支持 | ❌ 不支持 | ❌ 不支持 | ❌ 不支持 |
| 自托管选项 | ✅ Docker | ✅ 开源 | ❌ 仅限云 | ✅ 开源 | ✅ 开源 |
| 多智能体支持 | ✅ CrewAI, LangGraph | ⚠️ 部分 | ✅ 仅限 LangChain | ✅ 仅限 LangChain | ⚠️ 有限 |
| 定价模型 | ✅ 免费层 + 付费 | ✅ 开源 | 💰 按追踪定价 | ✅ 免费 + 付费 | ✅ 开源 |

RagaAI Catalyst 以其广泛的内置功能脱颖而出——特别是合成数据生成、护栏和红队测试——大多数竞争对手需要第三方工具才能实现这些功能。LangSmith 提供了精致的体验，但仅限于 LangChain 生态系统且仅限云端。Langfuse 和 Arize Phoenix 是基本追踪的强大开源替代方案，但缺乏 RagaAI Catalyst 捆绑在一起的评估和安全模块。

## 局限性 / 诚实评估

没有工具是完美的。在评估 RagaAI Catalyst 是否适合你的技术栈时，这里有一些实际的约束需要考虑：

**依赖云端优先。** 虽然可以通过 Docker 进行自托管，但核心 SDK 设计期望连接到 RagaAI 后端进行认证、数据存储和仪表板渲染。完全隔离的网络部署可能需要大量定制。

**仅限 Python。** RagaAI Catalyst 是一个 Python SDK。拥有 Node.js 或 Go 基础智能体系统的团队需要编写适配器层或直接依赖 REST API。没有官方的 JavaScript/TypeScript 客户端。

**指标覆盖缺口。** 内置评估指标涵盖了常见的 RAG 质量维度（忠实度、幻觉、答案相关性），但领域特定的指标——法律合规评分、医疗准确性分级——需要自定义模型配置。

**高级功能的学习曲线。** 基本的追踪和评估工作流很简单。然而，在单个管道中结合护栏、红队测试和提示词版本控制会引入配置复杂性。文档很有帮助，但假设读者熟悉 LLM 评估概念。

**定价透明度。** 免费层为开发提供了慷慨的限制，但高吞吐量智能体（每分钟数千次请求）的生产规模追踪可能需要付费计划。确切的定价层级未在公共 README 中完全记录。

这些都不是致命缺陷——它们是权衡取舍，需要根据你团队的需求进行衡量。对于大多数基于 Python 的智能体团队来说，RagaAI Catalyst 的功能密度证明了初始配置工作的合理性。

![RagaAI Catalyst 护栏管理](https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/guardrails.png)
*RagaAI Catalyst 护栏配置界面 — 来自 dibi8.com*

## 常见问题解答 (FAQ)

### 1. 如何安装 RagaAI Catalyst？

运行 `pip install ragaai-catalyst`。然后使用来自 RagaAI 平台的访问密钥和秘密密钥设置认证。对于自托管部署，使用 `docker pull ragaai/catalyst:latest` 并使用 `docker compose` 进行配置。

### 2. RagaAI Catalyst 用于什么？

RagaAI Catalyst 用于 LLM 智能体的可观测性、监控和评估。它为单智能体和多智能体系统提供追踪，对 RAG 管道进行批量评估，管理数据集，版本控制提示词，生成合成数据，部署护栏，并进行红队测试以评估安全性。

### 3. RagaAI Catalyst 与 LangSmith 相比如何？

LangSmith 紧密耦合到 LangChain 生态系统且仅限云端。RagaAI Catalyst 支持多种框架（LangChain、LangGraph、CrewAI、OpenAI、LiteLLM），提供自托管部署，并包括 LangSmith 未原生提供的额外功能，如合成数据生成、护栏和红队测试。

### 4. 可以在没有互联网连接的情况下使用 RagaAI Catalyst 吗？

部分可以。SDK 需要与 RagaAI 后端进行认证以完成初始设置。通过 Docker 进行的自托管部署减少了外部依赖，但完全离线操作需要修改 SDK 以移除认证层。如果仪表板是自托管的，基本追踪可以在本地工作。

### 5. RagaAI Catalyst 是否支持像 CrewAI 这样的多智能体框架？

是的。RagaAI Catalyst 为 CrewAI、LangGraph、LangChain 和其他框架提供了专用的追踪器类型。对于 CrewAI 和 LangGraph 设置，使用 `tracer_type="agentic"`，并使用像 `@trace_llm`、`@trace_tool` 和 `@trace_agent` 这样的装饰器进行细粒度仪器化。

### 6. RagaAI Catalyst 支持哪些评估指标？

内置指标包括忠实度（Faithfulness）、幻觉（Hallucination）、答案相关性（Answer Relevance）、上下文精度（Context Precision）和上下文召回率（Context Recall）。你可以配置阈值（gte, lte, eq）并指定评估模型（例如 `gpt-4o-mini`、`claude-sonnet-4-20250514`）。可以通过评估 API 添加自定义指标。

### 7. 如何从其他可观测性工具迁移到 RagaAI Catalyst？

将现有的追踪导出为 JSON 或 CSV，使用 `Dataset.create_from_csv()` 在 RagaAI Catalyst 中创建数据集，并使用 `schema_mapping` 参数映射你的模式。对于追踪，用 `init_tracing()` 和 RagaAI `Tracer` 类替换现有的仪器化库。

### 8. RagaAI Catalyst 可以免费使用吗？

该 SDK 在 Apache-2.0 下开源，可免费安装。该平台提供免费层，限制追踪和评估数量。付费计划解锁更高的吞吐量、更多的存储以及自定义护栏模型等高级功能。查看 RagaAI 定价页面以了解当前层级。

## 来源与进一步阅读

- **RagaAI Catalyst GitHub 仓库**: [raga-ai-hub/RagaAI-Catalyst](https://github.com/raga-ai-hub/RagaAI-Catalyst) — 源代码、问题和版本发布
- **官方文档**: [RagaAI Catalyst Docs](https://docs.ragaai.ai) — 完整的 API 参考和指南
- **Arize Phoenix**: [arize-phoenix](https://github.com/Arize-ai/phoenix) — 开源 LLM 可观测性替代方案
- **LangSmith**: [LangChain LangSmith](https://smith.langchain.com) — LangChain 的管理式可观测性平台
- **Langfuse**: [langfuse/langfuse](https://github.com/langfuse/langfuse) — 开源 LLM 工程平台
- **DigitalOcean**: [DigitalOcean Cloud Platform](https://www.digitalocean.com/) — 用于自托管 RagaAI Catalyst 的经济实惠的 VPS 托管

> **披露**: 上方部分链接含联盟推广。如通过链接注册，dibi8.com 可能获得佣金，不影响你的成本。这帮助 dibi8 持续免费运营。


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

## 结论：行动号召 (CTA)

RagaAI Catalyst 填补了 LLM 工程工具包中的一个关键空白：它将可观测性、评估和安全性整合到一个单一的 Python SDK 中。对于构建基于智能体的应用程序的团队——无论是使用 LangChain、CrewAI 还是自定义框架——智能体追踪、批量评估、提示词版本控制和红队测试的组合使其成为最全面的开源选项之一。

安装非常简单（`pip install ragaai-catalyst`），集成范围广（LangChain、LangGraph、CrewAI、OpenAI、LiteLLM），并且功能集远超基本追踪，包括合成数据生成、护栏和漏洞扫描。

如果你正在评估 RagaAI Catalyst 用于生产用途，最好的下一步是启动一个本地实例并仪器化单个智能体。免费层为你提供了足够的空间来探索追踪、评估和提示词管理，然后再承诺部署计划。

### dibi8.com 上的相关文章

寻找更多开源 AI 工具深度解析？查看这些相关指南：

- **[Deerflow: ByteDance 的长视界研究智能体](/articles/deerflow-2-byteDance-long-horizon-research-agents)** — ByteDance 的开源多智能体研究框架，用于长视界规划和执行。
- **[Headroom AI: 令牌压缩库及代理 MCP 服务器](/articles/headroom-ai-token-compression-library-proxy-mcp)** — 一个带有 MCP 代理支持的令牌压缩库，用于降低 LLM 上下文成本。
- **[Context7 MCP 服务器：生产设置指南](/articles/context7-mcp-server-production-setup-guide)** — Upstash 的 Context7 MCP 服务器指南，用于 AI 辅助开发中的实时库文档。

### 加入社区

加入 dibi8.com 的 Telegram 社区，持续关注开源 AI 工具、框架和工程实践：[t.me/DIBI8_Group/2](https://t.me/DIBI8_Group/2)

对于基础设施，考虑在低成本 VPS 上部署你的 RagaAI Catalyst 仪表板。[DigitalOcean](https://m.do.co/c/eca87ac14ee0) 提供可靠的云实例，起价仅为每月 4 美元——非常适合自托管 AI 工具。

![RagaAI Catalyst 项目管理仪表板](https://raw.githubusercontent.com/raga-ai-hub/RagaAI-Catalyst/main/docs/img/create_project.gif)
*RagaAI Catalyst 项目创建 — 来自 dibi8.com*