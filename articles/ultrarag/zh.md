---
title: 'UltraRAG：用于复杂 RAG 流水线的低代码 MCP 框架 — 2026 指南'
description: 'OpenBMB 出品的 UltraRAG 是一个基于 MCP 的低代码 RAG 开发框架。支持 YAML 编排、可视化 UI 构建器、Deep Research 演示以及生产环境部署指南。'
date: 2026-06-22
slug: 'ultrarag-low-code-mcp-rag-framework-innovative-pipelines-2026'
category: 'advanced-rag'
tags: ['UltraRAG', 'RAG', 'MCP', 'OpenBMB', 'low-code', 'pipeline', 'retrieval-augmented-generation', 'LLM']
github_repo: 'https://github.com/OpenBMB/UltraRAG'
stars: 5603
maintainer: 'OpenBMB'
license: 'MIT'
featureImage: 'https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/architecture.png'
lang: zh
---

![UltraRAG 架构](https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/architecture.png)
*UltraRAG 架构 — 通过 dibi8.com 可视化 MCP 服务器编排*

## 简介

构建一个能够处理条件分支、迭代深化和多跳推理的检索增强生成（RAG）流水线，通常意味着要编写数百行 Python 胶水代码。每个分支点都需要错误处理、状态管理，以及在组件之间仔细传递变量。随着流水线变得愈发复杂，复杂度呈非线性增长。

UltraRAG 由 OpenBMB 与清华大学 THUNLP、东北大学 NEUIR 合作开发，采用了不同的方法。它没有要求开发者以命令式方式将所有组件连接在一起，而是将 RAG 组件标准化为独立的 Model Context Protocol（MCP）服务器，并通过声明式的 YAML 配置文件进行编排。结果是一个框架，其中复杂的流程——包括循环、条件分支和多阶段推理链——可以用几十行 YAML 来表达，而不是数百行 Python。

在 MIT 许可证下，UltraRAG 已获得了 **5,603 个 GitHub Star**，吸引了大量需要在快速原型设计 RAG 架构或无需样板代码即可交付生产流水线的研究人员和工程师。本指南涵盖 UltraRAG 是什么、其基于 MCP 的架构如何工作、如何安装配置、与流行工具的集成、性能评估，以及其权衡取舍。

## 什么是 UltraRAG？

UltraRAG 是一个基于 Model Context Protocol 架构的**低代码 RAG 开发框架**。它于 2025 年 1 月首次发布，并经历了多个主要版本的迭代，其中 UltraRAG 3.0 于 2026 年 1 月推出，引入了"无黑盒"设计，使得每一行推理逻辑都能在流水线定义中透明可见。

核心思路很简单：将每个 RAG 基础操作——检索、生成、重排序、提示、路由、记忆管理——分解为独立的 MCP 服务器。然后使用支持顺序执行、循环和条件分支的 YAML 流水线定义将这些服务器组合成工作流。

关于 UltraRAG 的关键信息：

- **仓库**：[OpenBMB/UltraRAG](https://github.com/OpenBMB/UltraRAG)（GitHub）
- **Stars**：**5,603**（截至 2026 年 6 月）
- **许可证**：MIT
- **维护者**：OpenBMB
- **语言**：Python（需要 Python 3.11–3.12）
- **MCP 依赖**：`fastmcp>=3.3.1`
- **当前版本**：0.3.0.2
- **首次发布**：2025 年 1 月
- **贡献者**：THUNLP、NEUIR、OpenBMB、AI9stars
- **社区**：Discord、微信群、飞书群

与将 LLM 视为单一整体步骤的框架不同，UltraRAG 将每个操作建模为一个离散的可调用服务器。检索服务器暴露了 `retriever_init`、`retriever_search` 和 `retriever_websearch` 等方法。生成服务器提供了 `generation_init`、`generate`、`multimodal_generate` 和 `multiturn_generate`。路由服务器则使能基于 LLM 辅助分类的条件分支。

![UltraRAG 架构图](https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/ultrarag.svg)
*UltraRAG 基于 MCP 的架构概览 — 通过 dibi8.com*

![UltraRAG UI 画布](https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/chat_menu.png)
*UltraRAG UI — 可视化流水线构建器和 IDE — 通过 dibi8.com*

![UltraRAG Star 增长](https://api.star-history.com/svg?repos=OpenBMB/UltraRAG&type=Date)
*UltraRAG GitHub Star 增长图 — 通过 dibi8.com*

UltraRAG 还附带了 **UltraRAG UI**，一个用于 RAG 的可视化集成开发环境。该 UI 包含一个 Pipeline Builder，能够在基于画布的可视化编辑器和代码编辑器之间双向同步。你可以以可视化方式调整流水线参数、修改提示词并构建知识库工作流，然后通过一条命令将生成的流水线部署到 Web 界面。

## UltraRAG 的工作原理

UltraRAG 的架构建立在两个概念之上：**MCP 服务器**和 **MCP 客户端流水线**。

### 作为原子组件的 MCP 服务器

每个 RAG 功能都实现为一个 MCP 服务器。MCP 服务器注册可由客户端调用的工具（函数）。在 UltraRAG 中，服务器位于 `servers/` 目录下，遵循一致的结构：

```python
# servers/retriever/src/retriever.py (简化版)
from ultrarag.server import UltraRAG_MCP_Server

app = UltraRAG_MCP_Server("retriever")

class Retriever:
    def __init__(self, mcp_inst: UltraRAG_MCP_Server):
        mcp_inst.tool(self.retriever_init)
        mcp_inst.tool(self.retriever_embed)
        mcp_inst.tool(self.retriever_index)
        mcp_inst.tool(self.retriever_search)
        mcp_inst.tool(self.retriever_websearch)
```

每次 `tool()` 注册都会使该方法可从流水线 YAML 中调用。服务器独立管理自身的状态、依赖关系和资源分配。

### 作为编排层的 MCP 客户端流水线

流水线 YAML 文件定义了要加载哪些服务器以及按什么顺序调用它们的工具。以下是一个最小示例：

```yaml
# examples/experiments/sayhello.yaml
servers:
  sayhello: servers/sayhello

pipeline:
- sayhello.greet
```

以及一个更真实的 RAG 流水线：

```yaml
# examples/demos/RAG.yaml
servers:
  benchmark: servers/benchmark
  retriever: servers/retriever
  prompt: servers/prompt
  generation: servers/generation
  custom: servers/custom

pipeline:
- benchmark.get_data
- retriever.retriever_init
- generation.generation_init
- retriever.retriever_search
- custom.assign_citation_ids
- prompt.qa_rag_boxed
- generation.generate
```

流水线从上到下依次执行。每一步引用一个服务器和一个工具。可以使用 `output:` 和 `input:` 指令将一个步骤的输出变量作为输入传递给后续步骤。

### 条件分支和循环

UltraRAG 在 YAML 中原生支持控制流。以下是 LightResearch 演示中使用条件分支和路由器的代码片段：

```yaml
- loop:
    times: 10
    steps:
    - branch:
        router:
        - router.webnote_check_page
        branches:
          incomplete:
          - prompt.webnote_gen_subq
          - generation.generate:
              output:
                ans_ls: subq_ls
          - retriever.retriever_search:
              input:
                query_list: subq_ls
              output:
                ret_psg: psg_ls
          - prompt.webnote_fill_page
          - generation.generate:
              output:
                ans_ls: page_ls
          complete: []
```

`branch` 指令会评估路由器的输出，并将执行分发到 `incomplete` 或 `complete` 分支。`loop` 指令重复执行内部步骤固定的次数。这使得研究导向型 RAG 系统中常见的迭代深化模式成为可能。

### 步骤间变量传递

步骤通过命名输出变量进行通信。一个步骤声明其输出：

```yaml
- retriever.retriever_search:
    input:
      q_ls: instruction_ls
    output:
      ret_psg: retrieved_passages
```

下游步骤随后消费这些输出：

```yaml
- prompt.qa_rag_boxed:
    input:
      passages: retrieved_passages
```

这种显式的数据流声明使得流水线比隐式全局状态更容易调试和推理。

## 安装与配置

UltraRAG 支持两种安装方式：使用 `uv` 的源码安装（推荐）和 Docker 部署。

### 方法一：使用 uv 安装源码

如果你尚未安装 `uv`，请先安装：

```shell
pip install uv
```

克隆仓库并进入项目目录：

```shell
git clone https://github.com/OpenBMB/UltraRAG.git --depth 1
cd UltraRAG
```

安装依赖。你可以根据需求选择性安装：

```shell
# 仅核心功能（UltraRAG UI）
uv sync

# 完整安装（检索、生成、评估、语料处理）
uv sync --all-extras

# 仅检索模块
uv sync --extra retriever

# 仅生成模块
uv sync --extra generation
```

激活虚拟环境：

```shell
source .venv/bin/activate
```

### 方法二：安装到现有环境中

如果你已经设置了 Python 3.11 或 3.12 环境：

```shell
# 核心依赖
uv pip install -e .

# 完整安装
uv pip install -e ".[all]"

# 选择性安装扩展
uv pip install -e ".[retriever]"
uv pip install -e ".[generation]"
uv pip install -e ".[evaluation]"
```

### 方法三：Docker 部署

拉取预构建镜像：

```shell
docker pull hdxin2002/ultrarag:v0.3.0-base-cpu   # 纯 CPU 版本
docker pull hdxin2002/ultrarag:v0.3.0-base-gpu   # GPU 基础版本
docker pull hdxin2002/ultrarag:v0.3.0             # 完整版（GPU）
```

或在本地构建：

```shell
git clone https://github.com/OpenBMB/UltraRAG.git --depth 1
cd UltraRAG
docker build -t ultrarag:v0.3.0 .
```

启动容器：

```shell
docker run -it --gpus all -p 5050:5050 hdxin2002/ultrarag:v0.3.0
```

UltraRAG UI 将自动启动。访问 `http://localhost:5050` 即可使用。

### 验证安装

运行内置演示以确认一切正常：

```shell
ultrarag run examples/experiments/sayhello.yaml
```

预期输出：

```
Hello, UltraRAG v3!
```

### 使用 .env 配置

UltraRAG 从 `.env` 文件读取 API 密钥和模型端点：

```shell
# .env.example
OPENAI_API_KEY=your_openai_key_here
OPENAI_API_BASE=https://api.openai.com/v1
VLLM_MODEL_PATH=/path/to/local/model
MILVUS_URI=http://localhost:19530
```

复制并自定义：

```shell
cp .env.dev .env
```

## 与流行工具的集成

UltraRAG 与 RAG 生态系统中几种广泛使用的工具有着良好的集成。以下是它与各工具的对接方式。

### Milvus 向量数据库

Milvus 是 UltraRAG 默认的向量存储。初始化和索引你的语料库：

```yaml
# milvus_index.yaml
servers:
  retriever: servers/retriever

pipeline:
- retriever.retriever_init
- retriever.retriever_embed
- retriever.retriever_index
```

在服务器参数中配置 Milvus 连接：

```yaml
# servers/retriever/parameter.yaml
collection_name: my_knowledge_base
backend_configs:
  milvus:
    uri: http://localhost:19530
    collection_name: my_knowledge_base
```

### vLLM 本地生成

UltraRAG 支持使用 vLLM 进行高吞吐量的本地推理：

```yaml
# servers/generation/parameter.yaml
generation:
  backend: vllm
  backend_configs:
    model_name_or_path: /path/to/Qwen2.5-7B-Instruct
    tensor_parallel_size: 1
    gpu_memory_utilization: 0.9
```

初始化和生成：

```yaml
pipeline:
- generation.generation_init
- generation.generate
```

### OpenAI 兼容 API

对于基于云的生成，UltraRAG 可以与任何 OpenAI 兼容的端点配合使用：

```yaml
generation:
  backend: openai
  backend_configs:
    model_name_or_path: gpt-4o
    api_base: https://api.openai.com/v1
    api_key: ${OPENAI_API_KEY}
```

### Sentence Transformers 嵌入

UltraRAG 支持使用 sentence-transformers 进行嵌入计算：

```yaml
# 安装扩展
uv sync --extra retriever

# 在 parameter.yaml 中配置
retriever:
  backend_configs:
    embedding_model: sentence-transformers/all-MiniLM-L6-v2
```

### 基于 Flask 的 Web UI

UltraRAG UI 基于 Flask 构建，提供完整的 RAG IDE：

```shell
# 启动 UltraRAG UI
python ui/app.py
```

UI 默认在 5050 端口运行，并提供：
- 支持拖拽的可视化流水线构建器
- 实时代码同步
- 知识库管理
- 一键部署到交互式 Web 演示

## 基准测试与实际应用场景

UltraRAG 自带一个内置的评估框架，支持标准化基准测试。`benchmark` 服务器加载评估数据集并在流水线运行期间跟踪指标。

### 运行基准测试

```yaml
# 运行基准测试工作流
servers:
  benchmark: servers/benchmark
  retriever: servers/retriever
  generation: servers/generation
  prompt: servers/prompt

pipeline:
- benchmark.get_data
- retriever.retriever_init
- generation.generation_init
- retriever.retriever_search
- prompt.qa_rag_boxed
- generation.generate
- benchmark.evaluate
```

### Deep Research 流水线示例

UltraRAG 的旗舰演示是基于 AgentCPM-Report 模型的 Deep Research 流水线。该流水线执行多步检索和综合，生成全面的调研报告：

```yaml
# examples/demos/AgentCPM-Report.yaml
servers:
  benchmark: servers/benchmark
  generation: servers/generation
  retriever: servers/retriever
  prompt: servers/prompt
  router: servers/router
  custom: servers/custom

pipeline:
- benchmark.get_data
- retriever.retriever_init
- generation.generation_init
- custom.surveycpm_init_citation_registry
- custom.surveycpm_state_init
- loop:
    times: 140
    steps:
    - branch:
        router:
        - router.surveycpm_state_router
        branches:
          search:
          - prompt.surveycpm_search
          - generation.generate
          - retriever.retriever_batch_search
          - prompt.surveycpm_summarize
          - generation.generate
          expand: []
          finish: []
- prompt.webnote_gen_answer
- generation.generate
```

该流水线最多迭代 140 次，根据路由器的评估动态决定是搜索、扩展主题、总结发现还是得出结论。这种多跳推理在传统框架中通常需要复杂的 Python 状态机来实现。

### 语料处理流水线

UltraRAG 包含专门用于语料摄入和分块的服务器：

```yaml
# examples/demos/build_text_corpus.yaml
servers:
  corpus: servers/corpus

pipeline:
- corpus.build_corpus
- corpus.chunk
```

corpus 服务器支持多种文档格式（PDF、DOCX、文本），并与 Chonkie 集成以实现智能分块策略。

## 高级用法 / 生产环境加固

在生产环境中部署 UltraRAG 需要在基本设置之外关注多个方面。

### 使用 Milvus 和 LLM 后端的生产部署

UltraRAG 的部署指南涵盖了完整生产栈的设置：

```shell
# 1. 启动 Milvus 单机版
docker run -d \
  --name milvus-standalone \
  --security-opt seccomp:unconfined \
  -p 19530:19530 \
  -p 9091:9091 \
  milvusdb/milvus:v2.4.0 \
  milvus run standalone

# 2. 设置环境变量
export MILVUS_URI=http://localhost:19530
export OPENAI_API_KEY=sk-xxx
export OPENAI_API_BASE=https://api.openai.com/v1

# 3. 启动 UltraRAG UI
python ui/app.py
```

### 自定义服务器开发

你可以通过在 `servers/` 下创建新目录来添加自己的 MCP 服务器：

```
servers/
  my_custom_server/
    __init__.py
    parameter.yaml
    src/
```

服务器遵循相同的模式：

```python
# servers/my_custom_server/src/custom_tool.py
from ultrarag.server import UltraRAG_MCP_Server

app = UltraRAG_MCP_Server("my_custom_server")

class CustomTools:
    def __init__(self, mcp_inst: UltraRAG_MCP_Server):
        mcp_inst.tool(self.my_custom_method)

    async def my_custom_method(self, input_text: str) -> str:
        # 在此处编写自定义逻辑
        return f"Processed: {input_text}"
```

在流水线中注册：

```yaml
servers:
  my_custom_server: servers/my_custom_server

pipeline:
- my_custom_server.my_custom_method
```

### 跨循环迭代的状态操作

对于需要在迭代之间保持状态的流水线，UltraRAG 提供了有状态输出变量：

```yaml
- custom.assign_citation_ids_stateful:
    input:
      ret_psg: psg_ls
    output:
      ret_psg: psg_ls
```

`_stateful` 后缀表示输出在循环迭代中是累积的，而非被覆盖。

### 调试流水线

UltraRAG 提供了一个结构化的调试指南，涵盖四个层面：

1. **输入与检索** — 验证文档解析和嵌入质量
2. **推理与规划** — 检查路由器决策和分支逻辑
3. **状态与上下文** — 检查流水线步骤间的变量传递
4. **部署与运行时** — 监控服务器健康和资源利用率

使用 UltraRAG UI 中的 Case Study 界面来可视化每个流水线步骤的中间输出。

### 性能调优

生产环境中的关键配置参数：

```yaml
# 参数调优示例
retriever:
  batch_size: 64
  top_k: 20
  retrieve_thread_num: 4

generation:
  sampling_params:
    temperature: 0.7
    top_p: 0.9
    max_tokens: 2048
  backend_configs:
    gpu_memory_utilization: 0.85
    max_model_len: 8192
```

## 与替代方案的对比

UltraRAG 在 RAG 框架生态中占据着独特的位置。以下是它与三种主要替代方案的对比。

| 特性 | UltraRAG | Haystack | LangChain | LlamaIndex |
|------|----------|----------|-----------|------------|
| **主要范式** | YAML + MCP 服务器 | 组件流水线 | 链/图 | 数据为中心索引 |
| **低代码编排** | 是（YAML 流水线） | 部分（Pipeline 类） | 否（Python 代码） | 否（Python 代码） |
| **条件分支** | 原生（YAML `branch`） | 有限 | 需自定义代码 | 需自定义代码 |
| **循环支持** | 原生（YAML `loop`） | 不支持 | 需自定义代码 | 需自定义代码 |
| **可视化流水线构建器** | 是（UltraRAG UI） | 否 | 否 | 否 |
| **内置评估** | 是（benchmark 服务器） | 是（EvaluationPipeline） | 通过 LangSmith | 通过 GPTEvaluator |
| **向量数据库支持** | Milvus、FAISS、网页搜索 | 10+（Elasticsearch、Pinecone 等） | 15+ | 15+ |
| **LLM 后端** | OpenAI、vLLM、自定义 | OpenAI、Ollama、vLLM、自定义 | OpenAI、Anthropic、自定义 | OpenAI、Anthropic、自定义 |
| **多模态支持** | 是（视觉检索器） | 有限 | 通过集成 | 通过集成 |
| **许可证** | MIT | Apache-2.0 | MIT | MIT |
| **Python 版本** | 3.11–3.12 | 3.9+ | 3.9+ | 3.9+ |
| **MCP 架构** | 是 | 否 | 否 | 否 |

UltraRAG 以 YAML 优先的原生控制流是其主要的差异化优势。当 LangChain 和 LlamaIndex 需要命令式 Python 代码来处理分支和循环时，UltraRAG 以声明式方式表达这些模式。Haystack 在理念上更接近，但缺少 MCP 分解和可视化流水线构建器。

对于寻找强调可视化的 **UltraRAG 替代方案**的开发者来说，UltraRAG UI 是开源领域最接近的选择。对于优先考虑广泛向量数据库兼容性的团队，Haystack 或 LlamaIndex 可能提供更多的开箱即用连接器。

## 局限性 / 客观评估

没有哪个框架是完美的。在评估 UltraRAG 是否适合你的项目时，以下是一些需要考虑的局限性。

### 生态系统成熟度

UltraRAG 于 2025 年 1 月发布，与 Haystack（2022）或 LangChain（2023）相比相对年轻。`servers/` 下的服务器目录覆盖了常见的 RAG 基础操作，但尚未包含像成熟框架那样多的专用工具。如果你需要非标准的集成（例如特定的企业搜索引擎），可能需要自行编写服务器。

### 对 MCP 和 fastmcp 的依赖

UltraRAG 依赖于 Model Context Protocol 和 `fastmcp` 库。虽然 MCP 正在获得采用，但它仍然是一个不断演进的标准。MCP 规范的变更可能需要更新 UltraRAG 服务器。如果你的团队对这种依赖风险感到不安，选择更成熟的框架可能更合适。

### 全功能 GPU 需求

使用本地生成（vLLM）和嵌入模型运行 UltraRAG 需要大量的 GPU 内存。Docker 镜像体积较大，完整安装（包含所有扩展）会引入 PyTorch、transformers 和 vLLM。如果你在纯 CPU 基础设施上部署，则只能使用云 API 后端进行生成。

### 文档语言

虽然 [ultrarag.openbmb.cn](https://ultrarag.openbmb.cn) 上有英文文档，但主要的开发社区通过微信、飞书和 Discord 进行交流。中文资源和博客文章数量更多。非中文开发者可能会偶尔遇到翻译文档的空白。

### 评估覆盖范围

内置的评估框架支持常见指标（精确率、召回率、F1、上下文精确率/召回率），但尚未涵盖专门评估平台所提供的那广度场景特定的基准测试。对于生产级评估流水线，你可能希望在 UltraRAG 的 benchmark 服务器之外补充使用 RAGAS 或 DeepEval 等工具。

## 常见问题

### 如何在纯 CPU 机器上安装 UltraRAG？

使用 CPU Docker 镜像或以最小扩展安装：

```shell
git clone https://github.com/OpenBMB/UltraRAG.git --depth 1
cd UltraRAG
uv sync --extra retriever
# 使用 OpenAI 兼容 API 进行生成，而非 vLLM
```

`retriever` 扩展包含 FAISS，可以在无需 GPU 的情况下进行本地向量搜索。

### UltraRAG 支持哪些 Python 版本？

UltraRAG 需要 Python 3.11 或 3.12。由于 `fastmcp` 和 `vllm` 的依赖要求，它不支持 Python 3.10 及更早版本。使用 `uv` 自动管理虚拟环境：

```shell
uv python install 3.12
uv sync
```

### UltraRAG 能否与非 Milvus 向量数据库配合使用？

可以。检索服务器支持通过 `parameter.yaml` 配置的多种后端。FAISS 可通过 `retriever` 扩展获得，你也可以通过扩展 `index_backends` 模块添加自定义索引后端：

```yaml
backend_configs:
  faiss:
    index_type: IVF_FLAT
    nlist: 128
```

### UltraRAG 与 Haystack 在生产 RAG 方面有何区别？

Haystack 在广泛的连接器覆盖方面表现出色，并且多年来已在生产中经过充分验证。UltraRAG 的优势在于其基于 YAML 的编排，原生支持循环和条件分支——这些模式在 Haystack 的组件流水线中表达起来较为繁琐。对于简单的线性 RAG 流水线，Haystack 可能更简单。对于复杂的迭代或多路径工作流，UltraRAG 的声明式方法能显著减少样板代码。参见我们的 [Haystack 生产指南](haystack-ai-production-rag-framework-llm-applications-2026)以获取更深入对比。

### UltraRAG 适合生产部署吗？

UltraRAG 3.0 的设计考虑到了生产环境。MCP 服务器架构使得检索、生成和路由组件能够独立扩展。Docker 支持简化了容器化部署。然而，由于该框架比其他一些替代方案更年轻，在部署到关键负载之前，你应该计划增加额外的可靠性、监控和回滚程序测试。

### 如何在 UltraRAG 中添加自定义 LLM 提供商？

创建一个包装你 LLM 提供商的新生成服务器。`servers/generation/` 中现有的 OpenAI 兼容服务器展示了这一模式：

```python
from ultrarag.server import UltraRAG_MCP_Server

app = UltraRAG_MCP_Server("custom_llm")

class CustomLLM:
    def __init__(self, mcp_inst: UltraRAG_MCP_Server):
        mcp_inst.tool(self.custom_generate)

    async def custom_generate(self, prompt_ls: List[str]) -> List[str]:
        responses = []
        for prompt in prompt_ls:
            resp = await my_llm_client.generate(prompt)
            responses.append(resp.text)
        return responses
```

### UltraRAG v2 和 v3 有什么区别？

UltraRAG v2 引入了 MCP 服务器分解和 YAML 流水线编排。UltraRAG v3（2026 年 1 月发布）增加了带有双向画布-代码同步的可视化 UI，改进了跨循环迭代的有状态变量传递，增强了调试工具，并使推理逻辑完全透明——消除了中间流水线状态难以检查的"黑盒"问题。

### 我可以在 UltraRAG 中使用本地开源模型吗？

可以。UltraRAG 支持与通过 OpenAI 兼容 API 或 vLLM、Ollama 等本地推理引擎访问的任何模型配合使用。对于本地模型，配置生成服务器：

```yaml
generation:
  backend: openai
  backend_configs:
    model_name_or_path: ollama/qwen2.5:7b
    api_base: http://localhost:11434/v1
```

### 如何评估我的 UltraRAG 流水线？

使用内置的 benchmark 服务器和评估数据集：

```shell
# 下载基准数据集
ultrarag download-data --dataset hotpotqa

# 运行评估
ultrarag run examples/demos/RAG.yaml --eval
```

评估服务器计算标准指标，并生成不同流水线配置之间的对比报告。

## 参考资料与延伸阅读

- [UltraRAG 官方文档](https://ultrarag.openbmb.cn/pages/en/getting_started/introduction)
- [UltraRAG GitHub 仓库](https://github.com/OpenBMB/UltraRAG)
- [UltraRAG 3.0 发布博客](https://github.com/OpenBMB/UltraRAG/blob/page/project/blog/en/ultrarag3_0.md)
- [Model Context Protocol (MCP) 规范](https://modelcontextprotocol.io/docs/getting-started/intro)
- [UltraRAG 基准数据集](https://modelscope.cn/datasets/UltraRAG/UltraRAG_Benchmark)
- [VisRAG 论文 (ICLR 2025)](https://arxiv.org/abs/2410.10594)
- [RAG-DDR 论文 (ICLR 2025)](https://arxiv.org/abs/2410.13509)
- [AgentCPM-Report 模型 (Hugging Face)](https://huggingface.co/openbmb/AgentCPM-Report)
- [如何安装 UltraRAG — 完整教程](https://ultrarag.openbmb.cn/pages/en/getting_started/quick_start)
- [UltraRAG Deep Research 流水线指南](https://ultrarag.openbmb.cn/pages/en/demo/deepresearch)
- [UltraRAG 生产部署指南](https://ultrarag.openbmb.cn/pages/en/ui/prepare)
- [相关：[Haystack 生产 RAG 指南]](haystack-ai-production-rag-framework-llm-applications-2026)
- [相关：[RAG 架构实现指南]](rag-architecture-implementation-guide)
- [相关：[Context7 MCP 服务器生产部署指南]](context7-mcp-server-production-setup-guide)


## FAQ

### How to install UltraRAG on a CPU-only machine?

Use the CPU Docker image or install with minimal extras:

```shell
git clone https://github.com/OpenBMB/UltraRAG.git --depth 1
cd UltraRAG
uv sync --extra retriever
# Use OpenAI-compatible API for generation instead of vLLM
```

The `retriever` extra includes FAISS for local vector search without GPU requirements.

### What Python versions does UltraRAG support?

UltraRAG requires Python 3.11 or 3.12. It does not support Python 3.10 or earlier due to dependency requirements from `fastmcp` and `vllm`. Use `uv` to manage the virtual environment automatically:

```shell
uv python install 3.12
uv sync
```

### Can UltraRAG work with non-Milvus vector databases?

Yes. The retriever server supports multiple backends configured through `parameter.yaml`. FAISS is available via the `retriever` extra, and you can add custom index backends by extending the `index_backends` module:

```yaml
backend_configs:
  faiss:
    index_type: IVF_FLAT
    nlist: 128
```

### How does UltraRAG compare to Haystack for production RAG?

Haystack excels at broad connector coverage and has been battle-tested in production for years. UltraRAG's advantage lies in its YAML-based orchestration with native support for loops and conditional branches — patterns that are cumbersome to express in Haystack's component pipeline. For simple linear RAG pipelines, Haystack may be simpler. For complex iterative or multi-path workflows, UltraRAG's declarative approach reduces boilerplate significantly. See our [Haystack production guide](haystack-ai-production-rag-framework-llm-applications-2026) for a deeper comparison.

### Is UltraRAG suitable for production deployment?

UltraRAG 3.0 was designed with production in mind. The MCP server architecture enables independent scaling of retrieval, generation, and routing components. Docker support simplifies containerized deployment. However, because the framework is younger than some alternatives, you should plan additional testing around reliability, monitoring, and rollback procedures before deploying to critical workloads.

### How do I add a custom LLM provider to UltraRAG?

Create a new generation server that wraps your LLM provider. The existing OpenAI-compatible server in `servers/generation/` demonstrates the pattern:

```python
from ultrarag.server import UltraRAG_MCP_Server

app = UltraRAG_MCP_Server("custom_llm")

class CustomLLM:
    def __init__(self, mcp_inst: UltraRAG_MCP_Server):
        mcp_inst.tool(self.custom_generate)

    async def custom_generate(self, prompt_ls: List[str]) -> List[str]:
        responses = []
        for prompt in prompt_ls:
            resp = await my_llm_client.generate(prompt)
            responses.append(resp.text)
        return responses
```

### What is the difference between UltraRAG v2 and v3?

UltraRAG v2 introduced the MCP server decomposition and YAML pipeline orchestration. UltraRAG v3 (released January 2026) added the visual UI with bidirectional canvas-code synchronization, improved stateful variable passing across loop iterations, enhanced debugging tools, and made the reasoning logic fully transparent — eliminating the "black box" problem where intermediate pipeline states were difficult to inspect.

### Can I use UltraRAG with local open-source models?

Yes. UltraRAG supports any model accessible through OpenAI-compatible APIs or local inference engines like vLLM and Ollama. For local models, configure the generation server:

```yaml
generation:
  backend: openai
  backend_configs:
    model_name_or_path: ollama/qwen2.5:7b
    api_base: http://localhost:11434/v1
```

### How do I evaluate my UltraRAG pipeline?

Use the built-in benchmark server with evaluation datasets:

```shell
# Download benchmark datasets
ultrarag download-data --dataset hotpotqa

# Run evaluation
ultrarag run examples/demos/RAG.yaml --eval
```

The evaluation server computes standard metrics and generates comparison reports across different pipeline configurations.

## 结论：今天就开始部署你的第一个 UltraRAG 流水线

UltraRAG 填补了 RAG 生态中的一个真实空白：**复杂检索工作流的声明式编排**。如果你厌倦了在 RAG 流水线中编写命令式 Python 代码来处理条件分支、迭代深化和多跳推理，UltraRAG 的 YAML 优先 MCP 架构提供了一种更清晰的替代方案。

该框架的优势——低代码流水线定义、可视化 UI 构建器、内置评估和原生循环/分支支持——使其对于研究新 RAG 架构原型的研究人员和构建迭代检索系统（如 Deep Research 代理）的团队尤为有价值。

要开始使用，克隆仓库、用 `uv` 安装并运行内置演示。如果你熟悉 YAML，学习曲线相当平缓，而可视化 UI 甚至消除了这一门槛。

**为你的 UltraRAG 部署需要云基础设施？** [DigitalOcean](https://m.do.co/c/eca87ac14ee0) 提供实惠的 GPU Droplet 和管理式数据库，与 UltraRAG 的 Milvus 和 vLLM 后端搭配使用效果很好。

> 上方部分链接含联盟推广。如通过链接注册，dibi8.com 可能获得佣金，不影响你的成本。这帮助 dibi8 持续免费运营。

加入 dibi8.com 的 Telegram 社区，参与关于 RAG 框架、MCP 架构和生产部署策略的持续讨论：[t.me/DIBI8_Group/2](https://t.me/DIBI8_Group/2)

有关于 UltraRAG 的问题或想分享你的流水线设计？在我们的 Telegram 群组留言，与其他使用 UltraRAG 的开发者交流。
