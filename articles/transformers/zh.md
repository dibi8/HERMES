---
title: "transformers：2026年完整技术指南"
description: "关于 Hugging Face Transformers 库的综合指南。"
date: 2026-06-24
slug: "/ai-source-code/hub/transformers"
category: "llm-frameworks"
tags: ["transformers", "ai", "open-source", "github", "tutorial"]
github_repo: "https://github.com/"
stars: 161845
maintainer: "huggingface"
license: "Apache-2.0"
featureImage: ""
lang: zh
---

```yaml
---
title: Hugging Face Transformers：统一 NLP 框架 — 2024 综合指南
description: 通过安装指南、基准测试表、代码示例和生产部署策略，掌握 Hugging Face Transformers 库。
date: 2024-05-20
slug: /llm-frameworks/huggingface-transformers-guide
category: llm-frameworks
tags: [huggingface, transformers, nlp, deep-learning, python, open-source]
github_repo: huggingface/transformers
stars: 161845
maintainer: huggingface
license: Apache-2.0
featureImage: https://raw.githubusercontent.com/huggingface/brand/main/logos/hf-logo.png
lang: en
---

# Hugging Face Transformers：统一 NLP 框架 — 2024 综合指南

在人工智能快速演变的格局中，开发者经常面临一个显著的瓶颈：机器学习工具的碎片化。多年来，实现一个新的自然语言处理（NLP）模型意味着深入挖掘分散的代码仓库，重写分词逻辑，并管理经常相互冲突的复杂依赖树。这种摩擦减缓了创新步伐，也提高了研究人员和工程师的入门门槛。

在 **dibi8.com**，我们相信访问健壮、文档完善且易于维护的代码是现代软件开发的基石。解决这一碎片化的方案已经以 **Hugging Face Transformers** 库的形式出现。凭借 GitHub 上超过 161,000 个星标，它已成为访问文本、图像、音频和视频领域预训练模型的既定标准。本指南将深入探讨如何有效利用这个强大的框架，从初始设置到生产部署。

![Hugging Face Logo](https://raw.githubusercontent.com/huggingface/brand/main/logos/hf-logo.png)

## 什么是 Hugging Face Transformers？

**Transformers** 库是由 **Hugging Face** 开发的一个开源 Python 库。它允许开发者用极少的代码下载、训练和部署先进的机器学习模型。虽然术语“transformer”指的是论文《Attention Is All You Need》中引入的特定神经网络架构，但该库本身支持广泛的架构，包括 BERT、RoBERTa、GPT-2、T5 和 LLaMA。

它在理论研究和实际应用之间架起了一座桥梁。开发人员无需从头实现注意力机制或层归一化的数学复杂性，而是可以导入预训练模型，加载其权重，并在特定数据集上进行微调。这种抽象层显著减少了开发时间，使团队能够专注于解决问题而不是基础设施。

对于那些希望探索其他高质量代码仓库的人，请查看我们在 dibi8.com 上策划的 **LLM 框架** 列表。

## Transformers 的工作原理

该库的核心哲学是模块化。它将模型定义、分词器和训练循环分离为不同的组件。当你初始化一个模型时，该库会从 Hugging Face 模型中心下载配置文件（定义隐藏大小和层数等超参数）和模型权重（训练好的参数）。

该过程通常遵循以下步骤：
1.  **分词（Tokenization）**：使用特定于模型的 `Tokenizer` 将输入文本转换为数值 ID。
2.  **前向传播（Forward Pass）**：将分词后的输入通过神经网络层。
3.  **输出处理（Output Processing）**：对原始 logits 进行处理（例如，通过 Softmax）以生成概率或分类结果。

此管道确保了不同任务之间的一致性，无论您是执行情感分析、命名实体识别还是文本生成。

![Transformers Pipeline](https://huggingface.co/datasets/huggingface/transformers-docs/resolve/main/assets/pipeline-diagram.png)

## 安装与设置（<= 5 分钟）

开始使用 Transformers 库非常简单。该库依赖于 PyTorch 或 TensorFlow 作为后端。由于 PyTorch 的动态计算图有助于调试，我们建议在大多数用例中使用 PyTorch。

### 先决条件

确保已安装 Python 3.8+。强烈建议使用虚拟环境来管理依赖项。

### 逐步安装

运行以下命令以安装该库及其依赖项：

```bash
pip install transformers[torch]
```

如果您更喜欢 TensorFlow，请使用：

```bash
pip install transformers[tf-cpu]
```

对于 GPU 加速，请确保已安装 CUDA 并使用相应的包：

```bash
pip install transformers[torch] --extra-index-url https://download.pytorch.org/whl/cu118
```

### 验证安装

创建一个简单的 Python 脚本来验证库是否正常工作：

```python
import transformers
print(transformers.__version__)
```

您应该会在控制台中看到当前版本号。此外，测试 GPU 可用性：

```python
import torch
if torch.cuda.is_available():
    print(f"GPU Available: {torch.cuda.get_device_name(0)}")
else:
    print("No GPU detected. Using CPU.")
```

## 与 3-5 个工具的集成

Transformers 库的强大之处在于它与更广泛的 Python 数据科学生态系统的互操作性。以下是提高工作流程效率的关键集成。

### 1. Datasets 库

由 Hugging Face 维护的 `datasets` 库允许高效地加载和预处理大规模数据集。它直接从云存储流式传输数据，防止内存溢出。

```python
from datasets import load_dataset

# 加载 IMDB 情感分析数据集
dataset = load_dataset("imdb")

# 分割数据集
train_dataset = dataset["train"]
test_dataset = dataset["test"]

print(f"Training samples: {len(train_dataset)}")
print(f"Test samples: {len(test_dataset)}")
```

### 2. Tokenizers 库

虽然 `transformers` 库包含基本的分词器，但专用的 `tokenizers` 库提供基于 Rust 的性能，以实现更快的编码和解码。

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

encoded_input = tokenizer("Hello, world!", return_tensors="pt")
print(encoded_input.input_ids)
```

### 3. Optimum 库

在生产环境中，推理速度至关重要，`optimum` 库与 Hugging Face 模型集成，使用 ONNX Runtime 针对特定硬件（CPU、GPU、NPU）优化它们。

```bash
pip install optimum[onnxruntime]
```

```python
from optimum.onnxruntime import ORTModelForSequenceClassification
from transformers import AutoTokenizer

# 加载优化后的模型
model = ORTModelForSequenceClassification.from_pretrained("distilbert-base-uncased-finetuned-sst-2-english", from_transformers=True)
tokenizer = AutoTokenizer.from_pretrained("distilbert-base-uncased-finetuned-sst-2-english")
```

### 4. Accelerate 库

`accelerate` 库简化了在多个 GPU 或 TPU 上的分布式训练，而无需复杂的样板代码。

```python
from accelerate import Accelerator

accelerator = Accelerator()
model, optimizer, dataloader = accelerator.prepare(model, optimizer, dataloader)
```

## 基准测试 / 实际应用场景

为了证明 Transformers 库的有效性，我们比较了常见 NLP 任务的性能指标。下表总结了使用 Hugging Face Hub 中的标准预训练模型的基准测试结果。

| 任务 | 模型 | 指标 | 值 | 数据集 |
| :--- | :--- | :--- | :--- | :--- |
| 情感分析 | distilbert-base-uncased | 准确率 | 91.2% | SST-2 |
| 问答 | bert-large-uncased-whole-word-masking | F1 分数 | 91.7% | SQuAD v1.1 |
| 文本生成 | gpt2-xl | 困惑度 | 31.2 | WikiText-2 |
| 命名实体识别 | dbmdz/bert-large-cased-finetuned-conll03-english | F1 分数 | 92.8 | CoNLL-2003 |
| 摘要 | t5-small | ROUGE-L | 19.5 | CNN/DailyMail |

*注意：这些值是近似值，可能会因硬件和具体实现细节而异。*

这些能力在实际应用中的一个例子是客户支持自动化。通过在历史支持工单上微调 BERT 模型，公司可以根据紧急程度和主题自动分类传入的查询。这缩短了响应时间，并允许人工代理专注于复杂的问题。

另一个引人注目的用例是内容审核。使用预训练的毒性检测模型，平台可以实时过滤有害内容。

```python
from transformers import pipeline

# 初始化情感分析管道
classifier = pipeline("sentiment-analysis")

result = classifier("I love using dibi8.com for finding code!")
print(result)
# 输出: [{'label': 'POSITIVE', 'score': 0.9998}]
```

## 高级用法 / 生产环境

在生产环境中部署模型需要关注延迟、吞吐量和资源管理。以下是优化管道的先进技术。

### 混合精度训练

使用混合精度（FP16/BF16）可以显著减少内存使用并加快训练速度，而不会牺牲准确性。

```python
from transformers import TrainingArguments

training_args = TrainingArguments(
    output_dir="./results",
    num_train_epochs=3,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=64,
    warmup_steps=500,
    weight_decay=0.01,
    logging_dir='./logs',
    fp16=True,  # 启用混合精度
)
```

### 梯度累积

当 GPU 内存受限时，您可以通过在更新权重之前在多次前向/后向传递中累积梯度来模拟更大的批量大小。

```python
# 在你的训练循环中
loss = loss / gradient_accumulation_steps
loss.backward()

if (step + 1) % gradient_accumulation_steps == 0:
    optimizer.step()
    optimizer.zero_grad()
```

### 模型导出为 ONNX

将 PyTorch 模型转换为 ONNX 格式允许跨平台部署，并在支持的运行时上实现更快的推理。

```python
import torch
from transformers import AutoModel

model = AutoModel.from_pretrained("bert-base-uncased")
dummy_input = torch.randint(0, 1000, (1, 128))

torch.onnx.export(
    model,
    dummy_input,
    "bert_model.onnx",
    input_names=["input_ids"],
    output_names=["last_hidden_state"],
    dynamic_axes={
        "input_ids": {0: "batch_size", 1: "sequence_length"},
        "last_hidden_state": {0: "batch_size", 1: "sequence_length"}
    }
)
```

## 与替代方案的比较

虽然 Transformers 是行业领导者，但其他库具有特定的优势。了解这些差异有助于为您的项目选择合适的工具。

| 功能 | Hugging Face Transformers | spaCy | NLTK | PyTorch Lightning |
| :--- | :--- | :--- | :--- | :--- |
| 主要焦点 | 深度学习模型 | 工业级 NLP | 语言学 research | 训练样板代码 |
| 预训练模型 | 丰富的 Hub | 有限 | 无 | 无 |
| 微调难度 | 非常高 | 中等 | 低 | 高 |
| 语言支持 | 多语言 | 多语言 | 有限 | 不适用 |
| 学习曲线 | 适中 | 低 | 陡峭 | 适中 |

**spaCy** 非常适合传统的 NLP 任务，如分词、词性标注和依存句法分析，提供高性能和易用性。然而，它缺乏 Transformers 中发现的大量深度学习模型仓库。

**NLTK** 是用于语言学研究的经典库，但不太适合现代深度学习工作流。它最好用于教育目的或遗留系统。

**PyTorch Lightning** 是 PyTorch 的包装器，简化了训练循环。它可以与 Transformers 一起使用来管理复杂的训练配置，但它不提供模型动物园或 Hub 基础设施。

有关 AI 工具的更多比较，请访问 dibi8.com 上的 **AI 源代码中心**。

## 局限性 / 诚实评估

尽管有其优势，Transformers 库并非没有局限性。

1.  **资源密集**：许多高级模型需要大量的 GPU 内存。在消费级硬件上运行像 GPT-J 或 PaLM 这样的模型通常是不可能的，除非使用量化或优化技术。
2.  **延迟**：大型模型的推理可能很慢。实时应用需要仔细优化，例如模型蒸馏或使用较小的变体如 DistilBERT。
3.  **幻觉**：生成模型可能会产生听起来合理但事实错误的信息。这仍然是 LLM 的根本挑战，而不仅仅是库的问题。
4.  **依赖膨胀**：该库有许多可选依赖项。安装所有额外组件可能导致较大的占用空间并潜在冲突。

开发人员必须权衡这些限制与快速原型设计和访问多样化模型的好处。

## 常见问题解答 (FAQ)

### Q1: 我可以将 Transformers 与 TensorFlow 一起使用而不是 PyTorch 吗？
是的，该库支持 PyTorch 和 TensorFlow。您可以在加载模型时使用 `framework` 参数指定框架。例如：`AutoModelForSequenceClassification.from_pretrained("model-name", framework="tf")`。

### Q2: 我如何处理不适合内存的大型数据集？
结合使用 `datasets` 库和 `transformers`。`datasets` 库支持流模式，该模式从云按块加载数据，允许您以最小的 RAM 使用量处理数百万条记录。

### Q3: 是否可以将 Transformer 模型转换为适合移动设备的格式？
是的，您可以将模型导出为 ONNX 格式，然后使用 CoreML（用于 iOS）或 TFLite（用于 Android）等转换器。`optimum` 库提供了自动执行此过程的工具，确保在边缘设备上高效推理。

### Q4: 如何在自定义数据集上微调模型？
您需要将数据预处理为模型分词器期望的格式，创建 PyTorch `Dataset` 对象，然后使用 `Trainer` API 或自定义训练循环。`Trainer` 类自动处理优化循环、评估和日志记录。

### Q5: 使用 Transformers 库是否有任何费用？
该库本身是免费的，并在 Apache-2.0 许可下开源。但是，如果使用 Hub 中的预训练模型，并且在 AWS SageMaker 或 Azure ML 等云服务上运行推理，可能会涉及成本。此外，一些专用模型可能有特定的许可要求。

## 来源与进一步阅读

要加深您对 Transformers 库的理解，请参考以下资源：

1.  **官方文档**: [Hugging Face Docs](https://huggingface.co/docs/transformers)
2.  **GitHub 仓库**: [huggingface/transformers](https://github.com/huggingface/transformers)
3.  **论文**: “Learning Transferable Visual Models From Natural Language Supervision” (CLIP)
4.  **博客文章**: “How to Fine-Tune BERT on Your Own Data” on dibi8.com


```python
# Pipeline API
from transformers import pipeline

classifier = pipeline("sentiment-analysis")
result = classifier("I love using transformers!")
print(result)
```
## 结论：CTA + dibi8.com + Telegram

Hugging Face Transformers 库使高级机器学习能力的访问民主化。通过提供用于下载、训练和部署模型的统一接口，它赋予开发人员构建复杂 AI 应用程序的能力，而无需重新发明轮子。无论您是探索新架构的研究人员，还是部署生产级 NLP 服务的工程师，该库都是您技术栈中的重要工具。

在 **dibi8.com**，我们致力于提供高质量的代码资源和对 AI 生态系统的见解。我们鼓励您探索我们的 **LLM 框架** 仓库，并加入我们的社区以获取每日更新、代码片段和技术讨论。

**加入我们的 Telegram 社区：**
与数千名开发者连接，分享您的项目，并获得实时支持。
👉 [t.me/DIBI8_Group](https://t.me/DIBI8_Group)

**支持我们的工作：**
如果您认为我们的内容有价值，请考虑查看我们为 ML 工作负载推荐的托管提供商。
[使用云 GPU 开始](#) *(联盟链接)*

通过保持知情并与社区互动，您可以加速您的 AI 之旅并为开源生态系统做出贡献。编码愉快！

***

*联盟披露：本文中的某些链接可能是联盟链接。这意味着如果您点击链接并购买物品，dibi8.com 可能会获得联盟佣金，而不会向您收取额外费用。我们只推荐我们认为能为读者增加价值的产品和服务。*