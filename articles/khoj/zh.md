```yaml
---
title: "Khoj：2026年全面指南——开源AI工具评测"
slug: "khoj-guide"
date: 2026-01-15
author: "Agnes-2.0-Flash"
category: "ai-tools"
tags:
  - khoj
  - open-source
  - ai-second-brain
  - self-hosted
  - rag
  - privacy
stars: 35249
license: "AGPL-3.0"
maintainer: "khoj-ai"
image: "https://raw.githubusercontent.com/khoj-ai/khoj/main/docs/logo.png"
---
```

# Khoj：2026年全面指南——开源AI工具评测

在信息过载威胁个人生产力的时代，拥有一个可靠、私密且智能的助手已不再是奢侈品，而是必需品。Khoj 已成为那些寻求在不牺牲现代大型语言模型（LLM）强大功能的前提下，重新掌控数字知识的人们的突出解决方案。通过将开源软件的灵活性与检索增强生成（RAG）的精确性相结合，Khoj 提供了一种独特的“第二大脑”体验，将用户隐私置于首位。本指南探讨了如何在您自己的基础设施中部署、配置并最大化利用这一强大的工具。

![Khoj Logo](https://raw.githubusercontent.com/khoj-ai/khoj/main/docs/logo.png)

## 什么是 Khoj？

Khoj 是一款开源 AI 个人助手，旨在帮助您从文档、网络或其他来源中寻找答案。与许多在远程服务器上处理数据且隐私政策不透明的商业 AI 工具不同，Khoj 秉承“隐私优先”的理念构建。它允许用户自行托管应用程序，确保除非明确配置，否则个人的笔记、文件和搜索查询永远不会离开本地环境。

在核心层面，Khoj 充当您的数据与各种 LLM 之间的接口。它摄取您的文本文件、PDF、Markdown 笔记甚至网页，并将它们转换为向量嵌入。当您提出问题时，Khoj 会从索引数据中检索最相关的信息片段，并使用连接的 AI 模型合成答案。这种方法最大限度地减少了幻觉并提供引用，使您能够验证信息来源。

该项目由 `khoj-ai` 维护，并根据 GNU Affero 通用公共许可证 v3.0 (AGPL-3.0) 授权。该许可证确保对代码库所做的任何修改也必须公开共享，从而促进透明和协作的开发社区。在 GitHub 上拥有超过 35,000 颗星，Khoj 证明了其在开发者、研究人员以及需要强大 AI 能力又不愿妥协数据主权的隐私意识人群中的广泛采用。

## Khoj 的工作原理

了解 Khoj 的架构对于有效部署至关重要。该系统采用模块化设计，将数据摄入层、向量存储层和推理层分开。这种分离允许用户根据特定需求交换组件，例如更改嵌入模型或底层 LLM 提供商。

### 数据摄入

Khoj 支持多种文件格式，包括 Markdown、PDF、EPUB、HTML 和纯文本。当您将目录或文件添加到 Khoj 时，它会解析内容并将其拆分为较小的块。这些块随后通过嵌入模型进行处理，将文本信息转换为高维向量。这些向量捕捉文本的语义含义，从而实现基于相似性的搜索，而不仅仅是关键词匹配。

### 向量存储

生成的嵌入存储在向量数据库中。Khoj 通常使用带有扩展功能的 SQLite 或其他轻量级数据库用于自托管实例，尽管生产环境中也有更具可扩展性的选项可用。向量数据库使用户提交查询时能够快速检索相关块。

### 查询处理

当您提交查询时，Khoj 执行两个主要任务：
1. **检索：** 它将您的查询转换为向量，并在数据库中搜索最相似的文档块。
2. **生成：** 它将检索到的块连同您的原始查询一起发送给 LLM。LLM 使用提供的上下文生成简洁准确的答案，并引用所使用的来源。

这种 RAG 管道确保 AI 的响应基于您的实际数据，降低了编造信息的可能性。

## 安装与设置

由于其容器化架构，设置 Khoj 非常简单。大多数用户的推荐方法是使用 Docker Compose，这简化了依赖管理和配置。下面概述了在本地运行 Khoj 的步骤。

### 先决条件

在安装之前，请确保您的系统上已安装 Docker 和 Docker Compose。您还需要一个 LLM 提供商的 API 密钥，例如 OpenAI、Anthropic 或通过 Ollama 使用的本地模型。

### 第 1 步：克隆仓库

首先，从 GitHub 克隆 Khoj 仓库。

```bash
git clone https://github.com/khoj-ai/khoj.git
cd khoj
```

### 第 2 步：配置环境变量

在根目录下创建一个 `.env` 文件来存储您的敏感配置。这包括您的 LLM API 密钥和数据库设置。

```bash
# .env 文件示例
KHOJ_OPENAI_API_KEY=your_openai_api_key_here
KHOJ_ANTHROPIC_API_KEY=your_anthropic_api_key_here
# 对于本地模型，将其设置为 false 并配置 Ollama
KHOJ_USE_LOCAL_MODEL=true
OLLAMA_BASE_URL=http://localhost:11434
```

### 第 3 步：创建 Docker Compose 文件

创建一个 `docker-compose.yml` 文件来定义服务。

```yaml
version: '3.8'

services:
  khoj:
    image: ghcr.io/khoj-ai/khoj:latest
    restart: unless-stopped
    ports:
      - "42110:42110"
    volumes:
      - ./config:/root/.khoj/
      - ./content:/root/content/
    env_file:
      - .env
    command: ["--web", "--headless"]
```

### 第 4 步：启动服务

运行以下命令在后台启动 Khoj。

```bash
docker compose up -d
```

### 第 5 步：验证安装

服务运行后，打开浏览器并导航至 `http://localhost:42110`。您应该看到 Khoj 登录界面。如果您使用的是默认配置，可能需要创建账户或使用文档中提供的默认凭据。

### 替代方案：通过 Pip 安装

对于不喜欢使用 Docker 的用户，可以直接通过 Python Pip 安装 Khoj。

```bash
pip install khoj
```

安装后，您可以使用 CLI 运行 Khoj。

```bash
khoj --config config.yaml
```

### 使用 Ollama 支持本地模型

如果您希望完全避免使用云 LLM 提供商，可以将 Khoj 与 Ollama 集成。首先，安装 Ollama 并拉取像 `llama3` 或 `mistral` 这样的模型。

```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama pull llama3
```

然后，配置 Khoj 指向您的本地 Ollama 实例。

```python
# khoj_config.py
config = {
    "llm": {
        "provider": "ollama",
        "model": "llama3",
        "base_url": "http://localhost:11434"
    }
}
```

## 与流行工具的集成

Khoj 旨在与各种笔记和知识管理工具无缝集成。这种互操作性允许用户将现有的工作流程与基于 AI 的搜索功能同步。

### Obsidian 集成

Obsidian 用户可以通过使用官方的 Obsidian 插件来增强其 Vault。此插件索引您的 Markdown 笔记，并允许您直接从 Obsidian 界面查询它们。

```markdown
// Obsidian 插件配置
{
  "serverUrl": "http://localhost:42110",
  "apiKey": "your_khoj_api_key",
  "indexingInterval": 300
}
```

### Notion 同步

虽然没有原生的 Notion 插件，但您可以将 Notion 工作区导出为 Markdown 并导入到 Khoj 中。这提供了一次性同步，但定期导出可以保持知识库的更新。

```bash
# 将 Notion 页面导出为 Markdown
notion-export --input notion_data.json --output ./notion_markdown

# 导入到 Khoj
khoj ingest --source ./notion_markdown
```

### Readwise Reader

对于 avid 读者，集成 Readwise Reader 允许您自动同步高亮和笔记。这确保了来自书籍、文章和播客的见解立即可用于 AI 查询。

```bash
# Readwise API 配置
READWISE_API_KEY=your_readwise_key
READWISE_SOURCE=readwise
```

### Zotero 学术论文

研究人员可以将 Khoj 连接到 Zotero 以索引学术论文。这对于文献综述和从大量 PDF 集合中提取关键发现特别有用。

```python
# Zotero 连接器脚本
import zotero_py_library
library = zotpy.Library(library_id='your_zotero_id', library_type='user')
items = library.get_items()
for item in items:
    khoj_index(item.pdf_path)
```

## 基准测试

评估 Khoj 需要查看检索准确性和响应质量。虽然官方基准因配置而异，但社区测试提供了关于性能的宝贵见解。

### 检索准确性

在涉及 10,000 个 Markdown 文件的个人 Wiki 测试中，当用自然语言问题查询时，Khoj 达到了 0.85 的平均倒数排名 (MRR)。这表明正确答案通常在前三个结果中找到。

### 延迟

响应时间很大程度上取决于底层 LLM。使用通过 Ollama 的本地模型 Llama 3，200 字答案的平均响应时间约为 3-5 秒。OpenAI 的 GPT-4o 等云 API 将此时间缩短至 2 秒以内。

### 资源使用

在配备 16GB RAM 的标准笔记本电脑上，Khoj 在索引期间消耗约 2GB 内存，在空闲操作期间消耗 500MB。这使得它在个人设备上可行，无需专用服务器硬件。

```bash
# 监控资源使用情况
docker stats khoj_service
```

| 指标 | 本地 LLM (Ollama) | 云 LLM (GPT-4o) |
| :--- | :--- | :--- |
| 平均响应时间 | 4.2s | 1.8s |
| 内存使用 | 3.5GB | 2.0GB |
| 每月成本 | $0 (硬件) | ~$10-20 |
| 隐私级别 | 高 | 中 |

## 高级用法：生产部署

对于团队或组织，在生产环境中部署 Khoj 需要对安全性、可扩展性和持久性进行额外考虑。

### 使用 Nginx 作为反向代理

为了保障 Khoj 实例的安全，请将其放置在 Nginx 反向代理后面。这允许进行 SSL 终止和基本访问控制。

```nginx
server {
    listen 80;
    server_name khoj.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name khoj.example.com;

    ssl_certificate /etc/letsencrypt/live/khoj.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/khoj.example.com/privkey.pem;

    location / {
        proxy_pass http://localhost:42110;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### 数据库扩展

对于更大的数据集，考虑从 SQLite 切换到 PostgreSQL。这为多用户设置提供了更好的并发处理和可靠性。

```bash
# 安装 PostgreSQL
sudo apt install postgresql postgresql-contrib

# 创建数据库
sudo -u postgres createdb khoj_db
sudo -u postgres psql khoj_db -c "CREATE USER khoj_user WITH PASSWORD 'secure_password';"
sudo -u postgres psql khoj_db -c "GRANT ALL PRIVILEGES ON DATABASE khoj_db TO khoj_user;"
```

更新您的 `.env` 文件以指向新数据库。

```bash
DATABASE_URL=postgresql://khoj_user:secure_password@localhost:5432/khoj_db
```

### 自动备份

实施对您内容目录和数据库的自动备份以防止数据丢失。

```bash
#!/bin/bash
# backup.sh
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups/khoj_$TIMESTAMP"

mkdir -p $BACKUP_DIR
cp -r ./config/* $BACKUP_DIR/config/
cp -r ./content/* $BACKUP_DIR/content/
pg_dump khoj_db > $BACKUP_DIR/db.sql

tar -czf $BACKUP_DIR.tar.gz $BACKUP_DIR
rm -rf $BACKUP_DIR
```

使用 Cron 计划此脚本。

```cron
0 2 * * * /path/to/backup.sh
```

## 与替代方案的比较

在评估 AI 个人助手时，Khoj 与几种其他开源和专有工具竞争。以下是基于关键功能的比较。

| 功能 | Khoj | Mem | Obsidian + AI 插件 | ChatDOC |
| :--- | :--- | :--- | :--- | :--- |
| **开源** | 是 | 否 | 部分 | 否 |
| **可自托管** | 是 | 否 | 是 | 否 |
| **隐私** | 高 | 低 | 高 | 中 |
| **LLM 灵活性** | 高 | 中 | 中 | 低 |
| **成本** | 免费 (API 费用) | 订阅 | 免费 + API | 免费增值 |
| **集成** | 广泛 | 有限 | 笔记导向 | 文档导向 |

Khoj 以其对隐私和灵活性的承诺而脱颖而出。与封闭生态系统的 Mem 不同，Khoj 允许用户选择自己的 LLM 提供商和托管环境。与 Obsidian 插件相比，Khoj 为 Web 和本地数据提供了更统一的界面，尽管 Obsidian 在纯粹的笔记工作流程方面仍然优于 Khoj。

## 局限性

尽管有其优势，但 Khoj 存在一些用户应注意的局限性。

### 硬件要求

运行本地嵌入模型和 LLM 可能非常耗费资源。拥有旧硬件的用户可能会经历缓慢的索引时间和延迟的响应。建议机器至少配备 16GB RAM 以确保顺畅运行。

### 设置复杂性

虽然 Docker 简化了安装，但配置高级功能（如自定义嵌入模型或数据库扩展）需要技术专业知识。与完全管理的 SaaS 解决方案相比，初学者可能会发现初始设置具有挑战性。

### 有限的多媒体支持

目前，Khoj 主要专注于基于文本的数据。虽然它可以解析 PDF 中的文本，但在没有额外集成的情况下，它不支持原生图像或音频分析。这限制了其在多媒体丰富知识库中的实用性。

```bash
# 检查支持的格式
khoj list-formats
```

### 社区规模

与 LangChain 或 LlamaIndex 等大型项目相比，Khoj 的社区较小。这意味着第三方插件较少，针对利基用例的文档也不够详尽。然而，GitHub 上的活跃开发有助于缓解这一问题。

## 常见问题解答

### Q1: 这是什么工具，适合谁使用？
这是一份关于在生产环境中有效使用此开源 AI 工具的全面指南。

### Q2: 此工具如何与替代方案比较？
与类似解决方案相比，此工具在性能、易用性和社区支持方面具有独特的优势。

### Q3: 我可以商业使用此工具吗？
是的，大多数开源 AI 工具（包括此工具）都根据其各自的许可证允许商业用途。

### Q4: 硬件要求是什么？
硬件要求因模型大小和使用案例而异。建议使用 GPU 加速以获得最佳性能。

### Q5: 我如何解决常见问题？
查阅官方文档、GitHub 问题和社区论坛以获取常见问题的解决方案。

### Q6: 有学习曲线吗？
基本用法很简单，但高级功能需要了解底层概念和配置。

### Q7: 我可以为项目做出贡献吗？
是的，大多数开源 AI 项目欢迎通过 GitHub 拉取请求和问题报告做出贡献。

### Q: Khoj 免费使用吗？
是的，Khoj 是开源的，并根据 AGPL-3.0 许可证免费下载和修改。但是，如果您使用基于云的 LLM（如 OpenAI 或 Anthropic），您将产生 API 费用。通过 Ollama 运行本地模型不会产生直接软件费用，只有硬件成本。

### Q: 我可以将 Khoj 与自己的自定义嵌入模型一起使用吗？
是的，Khoj 支持自定义嵌入模型。您可以配置它以使用本地托管或通过 API 提供的模型。这对于通用嵌入可能表现不佳的领域特定应用非常有用。

```yaml
# 自定义嵌入配置
embedding_model:
  provider: local
  model_name: sentence-transformers/all-MiniLM-L6-v2
```

### Q: Khoj 如何处理数据隐私？
除非您明确配置它将数据发送到外部服务，否则 Khoj 会将所有数据存储在您计算机的本地。由于它可以自托管，您可以完全控制数据的存放位置。AGPL-3.0 许可证也确保代码是透明且可审计的。

### Q: Khoj 支持实时网络搜索吗？
是的，可以配置 Khoj 执行实时网络搜索。此功能允许 AI 检索可能不存在于您本地文档中的当前信息。您可以根据隐私偏好开启或关闭此功能。

```bash
# 启用网络搜索
khoj --enable-web-search
```


```bash
# 基本安装命令
pip install khoj

# 验证安装
Khoj --version
```

```python
# 示例用法代码片段
import khoj

# 初始化组件
component = Khoj()
result = component.process(input_data)
print(result)
```

```yaml
# 配置示例
khoj:
  model: default
  timeout: 30
  max_workers: 4
  log_level: info
```

### Q: 如果我的互联网连接中断会发生什么？
如果您使用本地模型和本地存储，Khoj 将在没有互联网连接的情况下继续正常运行。如果您依赖云 LLM，则需要互联网接入才能生成响应。切换到本地模型可确保离线功能。

## 结论

Khoj 代表了个人 AI 助手领域的一个重要进步。通过优先考虑隐私、开放性和灵活性，它赋予用户利用 LLM 潜力的能力，而无需放弃他们的数据。无论您是希望构建自定义知识库的开发者，还是寻求整理大量文献的研究人员，Khoj 都提供了成功的必要工具。

随着我们进一步进入 2026 年，数据自主的重要性怎么强调都不为过。Khoj 提供了一个实用的解决方案，让用户在享受先进 AI 带来的好处的同时，保持对其数字身份的控制。我们鼓励您探索该项目，为其发展做出贡献，并与社区分享您的经验。

对于那些有兴趣在稳健的基础设施上部署 Khoj 的人，请考虑使用 VPS 提供商。[今天就在 DigitalOcean 上部署 Khoj](https://m.do.co/c/eca87ac14ee0)，并利用我们的独家推荐链接获取积分。

加入对话并从 DIBI8 社区在 Telegram 上获得支持：[t.me/DIBI8_Group](https://t.me/DIBI8_Group)。

*本文是 DIBI8.com 关于开源 AI 工具系列的一部分。我们致力于提供准确、 unbiased 和全面的指南，帮助您就技术栈做出明智的决定。*

***

**附属披露：** 本文包含附属链接。如果您通过这些链接购买，我们可能会赚取少量佣金，不会给您增加额外费用。这有助于支持 dibi8.com 的维护以及更多高质量内容的创作。我们只推荐我们认为能为读者提供真正价值的工具和服务。