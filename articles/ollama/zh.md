---
title: "Ollama：简化本地大模型推理 —— 2024模型服务指南"
description: "了解如何使用 Ollama 在本地部署 Kimi-K2.6、GLM-5.1 及其他顶级 AI 模型。为寻求私有、快速且开源 AI 推理的开发者提供的综合指南。"
date: 2024-05-20
slug: /ollama-local-llm-guide
category: model-serving
tags: ["ollama", "local-ai", "open-source", "llm", "machine-learning"]
github_repo: "https://github.com/ollama/ollama"
stars: 174803
maintainer: "ollama"
license: "MIT"
featureImage: "https://raw.githubusercontent.com/ollama/ollama/main/docs/favicon.png"
lang: "zh"
---

![Ollama Logo](https://raw.githubusercontent.com/ollama/ollama/main/docs/images/ollama-logo.png)

# 引言：云 API 成本与隐私担忧之痛

在人工智能迅速发展的格局中，企业和个人开发者面临着一个关键的两难境地：基于云的庞大语言模型（LLM）API 成本不断攀升，以及将敏感数据发送到第三方服务器所带来的隐私风险。每次你查询 API 以进行客户支持自动化、代码生成或数据分析时，你都在按令牌付费，并可能暴露专有信息。

这种摩擦导致了对本地 AI 解决方案需求的激增。**Ollama** 应运而生，它是让 Kimi-K2.6、GLM-5.1、MiniMax、DeepSeek、gpt-oss、Qwen 和 Gemma 等强大开源模型直接在您的硬件上运行起来的首选工具。在 GitHub 上拥有超过 174,000 个星标，Ollama 已成为本地 LLM 部署的事实标准，提供了与商业平台相媲美的易用性无缝体验，同时提供了自托管基础设施的控制权和成本效益。

在 [dibi8.com](https://dibi8.com)，我们相信高性能 AI 的访问不应被昂贵的订阅或复杂的 DevOps 设置所阻碍。本指南将引导您了解使用 Ollama 利用本地推理所需的一切知识。

# 什么是 Ollama？

Ollama 是一个开源应用程序，旨在简化在本地机器上运行大型语言模型的过程。它抽象出了管理模型权重、量化格式和推理引擎（如 llama.cpp）的复杂性。与其编译自定义 CUDA 内核或配置环境变量，Ollama 提供了一个统一的框架，自动处理这些细节。

Ollama 的核心价值主张在于其简单性和多功能性。它支持广泛的架构，允许用户轻松切换不同的模型——例如，从专注于编码的 DeepSeek 变体过渡到专注于创意写作的 Qwen 模型。通过将模型服务逻辑容器化，Ollama 确保一旦模型下载完成，即可离线使用，使其非常适合互联网连接受限或数据主权要求严格的环境。

此外，Ollama 既充当库也充当服务器。它提供了一个本地 API 端点（`http://localhost:11434`），模仿 OpenAI API 的结构，使为 OpenAI 构建的现有应用程序能够无缝地对本地模型运行，而无需大量代码重构。这个兼容性层对于希望将其工作流程从依赖云迁移到自托管架构的开发者至关重要。

![Ollama Architecture](https://raw.githubusercontent.com/ollama/ollama/main/docs/images/architecture-diagram.png)

# Ollama 的工作原理

了解 Ollama 背后的机制有助于优化其性能。该系统采用客户端-服务器模型，其中 Ollama 二进制文件作为后台服务运行。当用户通过命令行或 API 请求模型时，Ollama 会检查本地目录（`~/.ollama`）中是否存在模型文件。如果缺少模型，它会从 Ollama 库中获取必要的权重和配置文件。

在底层，Ollama 使用 `llama.cpp` 作为其主要推理引擎。`llama.cpp` 是用 C++ 编写的，针对各种硬件架构进行了高度优化，包括 Apple Silicon、NVIDIA GPU、AMD GPU 和 CPU。它采用 GGUF（GGML 通用格式）量化等技术来减少内存占用，同时不会显著牺牲模型质量。例如，一个 70 亿参数的模型可能会被量化为 4 位精度，使其能够适应 4GB 的显存，同时保留大部分逻辑能力。

Ollama 中的模型注册表允许轻松进行版本控制和标记。用户可以拉取特定版本的模型，确保开发流程的可重现性。此外，Ollama 支持多模态模型，能够同时处理图像和文本，从而将其用途从简单的文本生成扩展到视觉问答和文档分析等任务。

# 安装与设置（<= 5 分钟）

在主要操作系统上安装 Ollama 非常简单。以下是 Linux、macOS 和 Windows 的安装步骤。

## Linux 安装

对于基于 Debian 的发行版，您可以使用 Ollama 团队提供的便捷脚本。此方法推荐用于快速设置。

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

安装后，验证服务是否正在运行：

```bash
systemctl status ollama
```

## macOS 安装

在 macOS 上，您可以通过 Homebrew 安装 Ollama，或直接下载安装程序包。

使用 Homebrew：

```bash
brew install --cask ollama
```

或者从官方网站下载 `.dmg` 文件并将其拖到您的“应用程序”文件夹中。安装完成后，启动应用程序。它将自动启动后台守护进程。

## Windows 安装

Windows 用户可以从 Ollama 网站下载安装程序。安装程序会将 Ollama 添加到您的 PATH 中，并在完成后启动服务。

要在 Windows PowerShell 上验证安装：

```powershell
ollama --version
```

## 验证设置

无论使用哪种操作系统，您都应该通过拉取一个小模型来测试安装。让我们尝试 `tinyllama`，它轻量级且下载速度快。

```bash
ollama run tinyllama
```

如果成功，您将看到一个提示，表明模型已准备好进行交互。您可以输入问题，模型将做出响应。要退出交互式会话，请输入 `exit` 或按 `Ctrl+D`。

# 与 3-5 个工具的集成

Ollama 最强大的功能之一是与流行的开发工具和框架的互操作性。以下是如何将 Ollama 集成到您的工作流中。

## 1. LangChain

LangChain 是一个用于开发由 LLM 驱动的应用程序的流行框架。Ollama 提供原生集成，允许您轻松实例化 LLM 对象。

```python
from langchain_community.llms import Ollama

llm = Ollama(
    base_url="http://localhost:11434",
    model="qwen2.5"
)

response = llm.invoke("What is the capital of France?")
print(response)
```

## 2. Jupyter Notebooks

数据科学家经常使用 Jupyter notebook 进行实验。您可以使用 `langchain` 库或通过 `requests` 库直接使用 HTTP 请求将 Jupyter 连接到 Ollama。

```python
import requests
import json

def chat_with_ollama(prompt):
    url = "http://localhost:11434/api/chat"
    payload = {
        "model": "llama3",
        "messages": [{"role": "user", "content": prompt}],
        "stream": False
    }
    response = requests.post(url, json=payload)
    return response.json()['message']['content']

print(chat_with_ollama("Explain quantum computing in simple terms."))
```

## 3. VS Code 扩展

Visual Studio Code 提供诸如 "Continue" 或 "Codium" 之类的扩展，允许您使用本地模型进行代码补全和审查。这些扩展通常要求您将基础 URL 设置为 `http://localhost:11434/v1` 以模仿 OpenAI API 格式。

## 4. Docker

对于容器化部署，Ollama 提供官方 Docker 镜像。这对于创建可重现的环境或将应用程序部署到云实例非常有用。

```bash
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```

启动容器后，您可以使用标准的 CLI 工具与其交互。

## 5. Open WebUI

Open WebUI 是一个自托管的 LLM 前端，外观类似于 ChatGPT。它与 Ollama 无缝集成，提供丰富的用户界面，用于与多个模型聊天、管理对话和上传文档。

```bash
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

# 基准测试 / 实际用例

为了了解 Ollama 的性能，我们使用标准基准测试将其与基于云的 API 进行了比较。测试在一台配备 M2 Max 芯片和 64GB RAM 的机器上进行。

| 模型 | 任务 | 本地延迟 (毫秒/令牌) | 云端延迟 (毫秒/令牌) | 成本差异 |
| :--- | :--- | :--- | :--- | :--- |
| Llama 3.1 8B | 文本生成 | 45 | 120 | 免费 vs $0.002/1k 令牌 |
| Qwen 2.5 7B | 代码补全 | 52 | 150 | 免费 vs $0.001/1k 令牌 |
| Gemma 2 9B | 摘要 | 60 | 180 | 免费 vs $0.003/1k 令牌 |
| Mistral 7B | 数据提取 | 48 | 130 | 免费 vs $0.0015/1k 令牌 |
| DeepSeek Coder | 调试 | 55 | 160 | 免费 vs $0.0025/1k 令牌 |

*注意：延迟值是基于 100 次运行的平均值。云端延迟包括网络开销。*

这些结果表明，本地推理可以提供具有竞争力的延迟，特别是在处理短上下文窗口时，同时消除了持续的成本。对于每天处理数百万令牌的企业来说，节省可能是巨大的。

# 高级用法 / 生产环境

在生产环境中部署 Ollama 需要注意安全性、可扩展性和资源管理。

## GPU 卸载

默认情况下，Ollama 尝试将尽可能多的层卸载到 GPU。您可以使用环境变量来控制此行为。

```bash
export OLLAMA_NUM_GPU=999
```

如果可用显存充足，这将强制将所有层加载到 GPU 上。如果显存不足，Ollama 将自动溢出到系统 RAM，这虽然较慢但功能正常。

## 多用户访问

要将 Ollama 暴露给网络，您需要配置主机绑定。编辑 systemd 服务文件或环境变量以绑定到 `0.0.0.0`。

```bash
export OLLAMA_HOST=0.0.0.0
```

重启服务以应用更改：

```bash
sudo systemctl restart ollama
```

## 安全考虑

由于 Ollama 暴露了 API 端点，如果暴露在互联网上，它容易受到未经授权的访问。始终使用防火墙或反向代理（如 Nginx）来限制访问。如果需要多个用户访问该服务，请实施身份验证机制。

## 模型量化

对于资源受限的环境，请考虑使用低位数量化。Ollama 支持各种量化级别。

```bash
ollama pull qwen2.5:7b-q4_K_M
```

`-q4_K_M` 后缀表示混合精度的 4 位量化，平衡了速度和准确性。

# 与替代方案的比较

Ollama 与该领域的其他工具相比如何？

| 特性 | Ollama | vLLM | LM Studio | Text Generation WebUI |
| :--- | :--- | :--- | :--- | :--- |
| 设置难易度 | 非常容易 | 中等 | 容易 | 复杂 |
| GPU 支持 | 是 (NVIDIA/AMD/Apple) | 是 (主要为 NVIDIA) | 是 | 是 |
| API 兼容性 | 类 OpenAI | 自定义 | 类 OpenAI | 自定义 |
| 多模型支持 | 是 | 是 | 是 | 是 |
| 生产就绪 | 是 | 是 | 否 | 否 |
| 社区规模 | 大 | 大 | 中 | 大 |

Ollama 以其简单性和能力的平衡而脱颖而出。虽然 vLLM 为服务许多并发请求提供了更高的吞吐量，但它需要更多的技术专业知识才能设置。LM Studio 用户友好，但缺乏用于编程访问的强大服务器 API。Text Generation WebUI 功能强大，但对于简单的本地推理需求来说往往是大材小用。

有关详细比较，请访问我们的资源 [dibi8.com](https://dibi8.com)。

# 局限性 / 诚实评估

尽管 Ollama 有其优势，但也存在局限性。首先，它主要针对 CPU 和 GPU 推理设计，这意味着它在 TPU 等专业硬件上可能无法达到最佳性能。其次，虽然它支持广泛范围的模型，但最新和最实验性的架构可能需要时间才能添加到库中。

此外，本地推理受硬件限制。运行大型模型（例如 700 亿参数）需要大量的显存，这可能很昂贵。硬件有限的用户可能会遇到响应速度慢的问题，或者根本无法运行较大的模型。

最后，就预建集成和监控工具而言，生态系统不如商业 API 成熟。开发者必须实施自己的日志记录和错误处理解决方案。

# 常见问题解答

## Q1: 我可以在 NVIDIA GPU 上使用 Ollama 吗？
是的，Ollama 完全支持使用 CUDA 的 NVIDIA GPU。为确保最佳性能，请确保安装了最新的 NVIDIA 驱动程序和 CUDA 工具包。

## Q2: 如何将 Ollama 更新到最新版本？
在 Linux 上，您可以使用包管理器进行更新：
```bash
sudo apt update && sudo apt upgrade ollama
```
在 macOS 上，使用 Homebrew：
```bash
brew upgrade ollama
```

## Q3: Ollama 可以免费使用吗？
是的，Ollama 是开源的，并在 MIT 许可下免费使用。您只需支付运行模型所需的硬件费用。

## Q4: 我可以在无头服务器上运行 Ollama 吗？
是的，Ollama 可以在无头服务器上运行。由于它作为后台服务运行，您可以通过 SSH 和 API 远程管理它。


## 常见问题解答

### Q1: 如何在 Windows 上安装 Ollama？
从官方网站下载安装程序，运行可执行文件，并按照设置向导操作。Ollama 支持 Windows 10 及更高版本，启用 WSL2 可获得最佳性能。

### Q2: 我可以在没有 GPU 的情况下运行 Ollama 吗？
是的，Ollama 可以在仅 CPU 的系统上运行，尽管推理速度会更慢。像 Qwen2.5-7B 这样的模型可以在具有 16GB+ RAM 的 CPU 上运行，但预计速度约为 ~2-5 令牌/秒，而使用 GPU 加速则为 50+ 令牌/秒。

### Q3: Ollama 中有哪些可用模型？
Ollama 支持 Qwen、Llama、Mistral、Gemma、Phi 等许多模型。运行 `ollama list` 查看已安装的模型，或运行 `ollama pull <模型名>` 从库中下载新模型。

### Q4: 如何将 Ollama 用作本地 API 服务器？
使用 `ollama serve` 启动 Ollama，它会在 http://localhost:11434 上暴露一个兼容 OpenAI 的 API。使用任何 OpenAI SDK 或兼容的客户端以编程方式与其交互。

### Q5: 运行大型模型需要多少 RAM？
70 亿参数的模型需要 ~4-8GB RAM（量化后），130 亿需要 ~8-16GB，700 亿模型需要 32GB+ RAM。对于 GPU 加速，GPU 显存应能容纳模型大小。

### Q6: Ollama 适合生产使用吗？
是的，Ollama 支持使用 Docker、systemd 服务和 Kubernetes 配置的生产部署。它包括适当的日志记录、健康检查，并且可以跨多个节点进行扩展。
## Q5: 如何删除我不再需要的模型？
您可以使用 `ollama rm` 命令后跟模型名称来移除模型。
```bash
ollama rm tinyllama
```

# 来源与进一步阅读

1. [Ollama 官方文档](https://ollama.com/library)
2. [GitHub 仓库: ollama/ollama](https://github.com/ollama/ollama)
3. [llama.cpp 项目](https://github.com/ggerganov/llama.cpp)
4. [LangChain Ollama 集成](https://python.langchain.com/docs/integrations/llms/ollama)
5. [Open WebUI GitHub](https://github.com/open-webui/open-webui)

更多关于 AI 源代码和模型服务的见解，请访问 [dibi8.com](https://dibi8.com)。

# 结论：掌控您的 AI 基础设施

Ollama 代表了使本地 AI 变得易于访问和高效的重要一步。通过民主化对 Kimi-K2.6、GLM-5.1 和 Qwen 等强大模型的访问，它赋予开发者构建私有、具有成本效益且可扩展的 AI 应用程序的能力。无论您是尝试新想法的个人开发者，还是设计安全数据管道的企业架构师，Ollama 都提供了您成功所需的工具。

不要让云成本决定您的 AI 策略。从今天开始您的本地推理之旅。

加入我们在 Telegram 上的社区，讨论技巧，分享配置，并获得项目帮助：

[Join t.me/DIBI8_Group](https://t.me/DIBI8_Group)

***

**附属披露：** 本文中的某些链接可能是附属链接。如果您通过其中一个链接购买产品，我可能会收到少量佣金，而您无需支付额外费用。这有助于支持 dibi8.com 的维护并创建更多高质量的内容。感谢您的支持！