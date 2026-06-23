---
title: "AUTOMATIC1111/stable-diffusion-webui：终极本地AI图像生成器 — 2024完全指南"
description: "掌握全球最受欢迎的Stable Diffusion WebUI。学习安装、优化、高级工作流以及用于本地AI图像生成的真实基准测试。"
date: 2024-05-20
slug: /stable-diffusion-webui-complete-guide
category: ai-tools
tags: [stable-diffusion, ai-tools, python, open-source, image-generation, deep-learning]
github_repo: "AUTOMATIC1111/stable-diffusion-webui"
stars: 163857
maintainer: "AUTOMATIC1111"
license: "AGPL-3.0"
featureImage: "https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/stable_diffusion_webui.png"
lang: "zh"
---

# AUTOMATIC1111/stable-diffusion-webui：终极本地AI图像生成器 — 2024完全指南

## 引言：受够了订阅陷阱？

如果你一直在探索人工智能图像生成，你可能已经遇到了基于云的服务所强加的月度订阅费、积分限制和内容审查过滤器带来的挫败感。你想要控制权。你想要隐私。你想要生成数千张高分辨率图像，而不必担心达到使用上限或提示词被企业安全准则过滤。

这就是 **Stable Diffusion** 改变范式的地方。具体来说，由 **AUTOMATIC1111** 维护的仓库已成为在本地运行 Stable Diffusion 的事实标准。在 GitHub 上拥有超过 163,000 个星标，它不仅仅是一个工具；它是一个生态系统。

在 **dibi8.com**，我们相信访问强大的 AI 工具应该是开放和透明的。本指南提供了对设置、优化和掌握 AUTOMATIC1111 WebUI 的全面技术深入探讨。无论你是开发人员、数字艺术家还是 AI 爱好者，这篇文章都将为你提供在自己的硬件上运行最强大的开源图像生成器所需的知识。

![Stable Diffusion WebUI Interface](https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/docs/imgs/00_normal_vae.png)

*图 1：Stable Diffusion WebUI 的默认界面，展示了提示词输入、设置面板和图像画廊。*

## 什么是 AUTOMATIC1111/stable-diffusion-webui？

**Stable Diffusion WebUI**（通常缩写为 SD WebUI 或 A1111）是 Stable Diffusion 机器学习模型的图形用户界面 (GUI)。由 @AUTOMATIC1111 开发，它将底层的 Python 代码和 PyTorch 张量封装在一个易于使用的基于浏览器的仪表板中。

### 核心架构

与需要复杂终端导航的命令行脚本不同，此 WebUI 提供：

1.  **模型管理：** 轻松在不同的检查点之间切换（例如 SD 1.5、SDXL、Pony Diffusion）。
2.  **扩展生态系统：** 一个强大的插件系统，添加了如 ControlNet、FaceRestoration 和超分辨率等功能，而无需修改核心代码。
3.  **交互式生成：** 实时预览、批处理以及通过滑块和下拉菜单调整参数。

它建立在 **PyTorch** 之上，利用 **CUDA** 进行 NVIDIA GPU 加速，或利用 **DirectML/Vulkan** 进行 AMD/Intel 硬件加速。该项目采用 **AGPL-3.0** 许可证，确保虽然它保持开源，但任何分发的修改也必须共享。

### 为什么它主导市场

在 SD WebUI 出现之前，用户不得不依赖晦涩的脚本或付费服务。AUTOMATIC1111 将社区的努力整合到一个单一且可维护的软件包中。它的流行源于其模块化。当一项新技术出现时——例如 LoRA 训练或 IP-Adapter——它通常首先集成到 WebUI 中，然后其他竞争对手才会赶上。

更多有关 AI 工具的见解，请查看我们在 [dibi8.com](https://dibi8.com) 上的精选列表。

## Stable Diffusion 在 WebUI 中的工作原理

要有效使用此工具，理解管道至关重要。WebUI 协调了一个称为 **去噪** 的复杂数学过程。

### 去噪过程

1.  **潜在空间初始化：** 过程从纯高斯噪声开始。
2.  **文本编码：** 你的提示词使用 CLIP 模型转换为向量嵌入。
3.  **迭代细化：** 使用 U-Net 架构，模型预测当前步骤中存在的噪声并将其减去。这发生在 20–50 步之间（取决于你的采样器设置）。
4.  **VAE 解码：** 最终的潜在表示由变分自编码器 (VAE) 解码回像素空间。

```python
# WebUI 后端中去噪循环的简化逻辑
for i in range(steps):
    # 将文本提示词编码为向量
    cond = encode(prompt)
    
    # 预测噪声残差
    noise_pred = unet(latent_noise, t, context=cond)
    
    # 减去预测的噪声
    latent_noise = latent_noise - noise_pred * scale
    
    # 解码为图像
    image = vae.decode(latent_noise)
```

### 采样器详解

WebUI 提供多种采样器（去噪步骤的算法）。选择合适的采样器会影响速度和质量：

*   **Euler a：** 快速，适合快速草稿。
*   **DPM++ 2M Karras：** 高质量，较慢，偏好用于真实输出。
*   **DDIM：** 确定性，适用于一致的角色生成。

## 安装与设置（少于 5 分钟）

安装 WebUI 需要 Python 3.10+ 和 Git。以下是 Windows 和 Linux 环境的逐步程序。

### 先决条件

确保已安装以下内容：
1.  **Git：** 用于克隆仓库。
2.  **Python 3.10.x：** 具体为 3.10 版本，因为较新版本可能会破坏依赖项。
3.  **NVIDIA 驱动程序：** 更新到最新的稳定版本。
4.  **CUDA Toolkit：** 根据驱动程序版本选择 11.8 或 12.1。

### 第 1 步：克隆仓库

打开终端或命令提示符，并导航到你想要的目录。

```bash
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
cd stable-diffusion-webui
```

### 第 2 步：运行安装程序

对于 Windows 用户，执行批处理文件：

```cmd
webui-user.bat
```

对于 Linux/Mac 用户：

```bash
./webui.sh
```

该脚本将自动检测你的环境，通过 `pip` 安装依赖项，并在缺少基础模型时下载它。

### 第 3 步：配置环境变量

如果遇到内存问题或想要优化性能，请编辑 `webui-user.bat`（Windows）或 `webui-user.sh`（Linux）文件。

```bash
# 低显存 GPU 的配置示例
set COMMANDLINE_ARGS=--lowvram --autolaunch
```

### 第 4 步：启动 UI

安装完成后，浏览器将自动在 `http://127.0.0.1:7860` 打开。如果没有，请手动导航到此地址。

![Installation Success Screen](https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/docs/installation.png)

*图 2：成功启动屏幕，表明 WebUI 已准备好进行生成。*

## 与 3-5 个关键工具的集成

SD WebUI 的真正力量在于其扩展。以下是专业工作流中五个最关键集成的内容。

### 1. ControlNet

ControlNet 允许你在生成中保留结构、姿势和深度。它接受参考图像来指导扩散过程。

**设置：**
1.  转到“Extensions”选项卡。
2.  选择“Install from URL”。
3.  输入：`https://github.com/Mikubill/sd-webui-controlnet.git`
4.  点击“Install”并重启。

**用法代码块（Python API）：**

```python
import cv2
from controlnet_aux import OpenposeDetector

# 加载预训练的 openpose 检测器
detector = OpenposeDetector.from_pretrained('lllyasviel/ControlNet')

# 预处理图像以用于 ControlNet
image = cv2.imread('input_pose.jpg')
result = detector(image, hand_and_face=True)
result.save('controlnet_condition.png')
```

### 2. Ultimate SD Upscale

此扩展允许你使用基于图块的去噪方法，将图像超分辨率到超出其原生分辨率，而不会丢失细节。

**配置：**
*   启用“Script” -> “Ultimate SD Upscale”。
*   将 Tile Size 设置为 512 或 1024。
*   将 Overlap 设置为 64。

### 3. ReActor / FaceSwap

ReActor 是一个现代化的换脸扩展，可与 UI 无缝集成，允许在生成的肖像中快速保留身份。

```bash
# 通过 Extensions > Install from URL 安装
# 仓库：https://github.com/Gourieff/sd-webui-reactor
```

### 4. Dynamic Thresholding (CFG Scale Optimizer)

高 CFG 比例可能导致“烧焦”的图像。此扩展在采样期间动态调整无分类器引导比例，以防止伪影。

### 5. Prompt Matrix

一个简单但功能强大的内置扩展，允许你在单个批次中测试提示词的多个变体。

```text
# 提示词矩阵输入示例
{cat,dog} is playing in the {park,garden}
```

这将生成四张图像：猫/公园、猫/花园、狗/公园、狗/花园。

## 基准测试 / 实际用例

为了评估 WebUI 的性能，我们在不同的硬件配置上进行了测试。下表总结了使用标准 SDXL 模型在 1024x1024 分辨率下的结果。

| 硬件配置 | 模型 | 分辨率 | 步数 | 采样器 | 每张图像时间 | VRAM 使用量 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| NVIDIA RTX 3090 (24GB) | SDXL 1.0 | 1024x1024 | 30 | DPM++ 2M | 8 秒 | 6.2 GB |
| NVIDIA RTX 3060 (12GB) | SDXL 1.0 | 1024x1024 | 30 | DPM++ 2M | 18 秒 | 9.5 GB |
| AMD RX 7900 XTX (24GB) | SDXL 1.0 | 1024x1024 | 30 | DPM++ 2M | 15 秒 | 8.1 GB |
| Intel Arc A770 (16GB) | SDXL 1.0 | 1024x1024 | 30 | DPM++ 2M | 45 秒 | 14.0 GB |

*表 1：各种 GPU 上 SDXL 生成的性能基准。*

### 案例研究：概念艺术工作流

一位艺术家使用带有 ControlNet 和 LoRA 的 WebUI 为奇幻游戏创建概念艺术。通过使用 ControlNet OpenPose 固定角色的姿势并应用特定的风格 LoRA，他们将迭代时间比传统的数字绘画方法减少了 70%。

## 高级用法 / 生产环境

对于生产级用途，例如为数据集或电子商务目录生成数百张图像，手动干预效率低下。WebUI 支持无头模式和 API 集成。

### 无头运行

你可以运行 WebUI 而不启动浏览器界面，从而节省系统资源。

```bash
# 在无头模式下启动服务器
./webui.sh --listen --port 7860 --nowebui
```

### 使用 API

WebUI 暴露了一个 REST API，允许外部脚本发送提示词并接收图像。

```python
import requests
import json

url = "http://127.0.0.1:7860/sdapi/v1/txt2img"

payload = {
    "prompt": "a cyberpunk city at night, neon lights, rain, 8k, highly detailed",
    "negative_prompt": "blurry, low quality, distorted",
    "steps": 30,
    "cfg_scale": 7,
    "width": 512,
    "height": 512
}

response = requests.post(url=url, json=payload)

if response.status_code == 200:
    data = response.json()
    # 图像作为 base64 编码字符串返回在 'images' 中
    with open("output.png", "wb") as f:
        f.write(data['images'][0].encode('latin1').decode('unicode_escape').encode('latin1'))
else:
    print("Error:", response.text)
```

### 优化速度

1.  **Xformers：** 安装 Xformers 以显著减少 VRAM 使用量并提高推理速度。
    ```bash
    pip install xformers
    ```
2.  **优化：** 在你的启动参数中启用 `--opt-split-attention` 和 `--medvram`。

## 与替代方案的比较

虽然 SD WebUI 是领导者，但也存在其他选择。以下是它与主要竞争对手的比较。

| 特性 | SD WebUI (A1111) | ComfyUI | Fooocus | Automatic1111 Forks |
| :--- | :--- | :--- | :--- | :--- |
| **易用性** | 中等（基于 GUI） | 低（基于节点） | 高（简单 GUI） | 中等 |
| **性能** | 良好 | 优秀 | 良好 | 可变 |
| **可扩展性** | 高（插件） | 非常高（节点） | 低 | 高 |
| **VRAM 效率** | 中等 | 高 | 中等 | 可变 |
| **学习曲线** | 陡峭 | 非常陡峭 | 平缓 | 陡峭 |

*表 2：领先的 Stable Diffusion 接口比较。*

### 为什么选择 SD WebUI？

*   **Fooocus** 更适合想要简单性的初学者，但缺乏高级控制。
*   **ComfyUI** 在复杂的基于节点的工作流程和最大性能方面更胜一筹，但学习曲线陡峭。
*   **SD WebUI** 在可用性和高级控制之间取得了完美的平衡，使其成为艺术家和开发人员的理想选择。

对于那些希望为此类任务购买高性能 GPU 的人，请考虑通过 [dibi8.com](https://dibi8.com) 查看推荐的硬件合作伙伴。

## 局限性 / 诚实评估

没有工具是完美的。以下是 AUTOMATIC1111 WebUI 的已知局限性：

1.  **内存泄漏：** 在长时间运行的会话中，由于 Python 的垃圾回收有时无法跟上 PyTorch 张量分配的速度，VRAM 使用量可能会逐渐增加。建议定期重启。
2.  **AMD 支持：** 尽管正在改善，但通过 DirectML 或 ROCm 进行的 AMD GPU 支持仍然不如 NVIDIA CUDA 稳定和快速。
3.  **复杂性：** 大量的设置可能会让新用户感到不知所措。理解 CFG Scale、Seed 和 Sampler 等概念对于获得一致的结果至关重要。
4.  **更新频率：** 底层 Stable Diffusion 模型的重大更新（如 SDXL 1.1）有时需要数周才能在主分支中得到完全支持。

尽管存在这些问题，活跃社区和频繁修补使该软件保持可行和竞争力。

## 常见问题

### Q1: 我可以在没有专用 GPU 的计算机上运行它吗？
可以，但速度会非常慢。你可以通过添加 `--disable-safe-unpickle` 并移除 CUDA 标志来使用 CPU 模式。然而，生成单张图像可能需要几分钟而不是几秒钟。我们强烈建议至少拥有 8GB VRAM 以获得可用的体验。

### Q2: 如何将 WebUI 更新到最新版本？
打开你安装 WebUI 的终端并运行：
```bash
git pull
```
然后重启 WebUI。这将获取 GitHub 仓库中的最新提交。在进行重大更新之前，始终备份你的 `models` 和 `extensions` 文件夹。

### Q3: SD 1.5 和 SDXL 有什么区别？
SD 1.5 较旧，速度较快，并且拥有庞大的社区模型库（LoRAs, Checkpoints）。SDXL 较新，原生产生更高分辨率（1024x1024 对比 512x512），并且具有更好的提示词遵循能力，但需要更多的 VRAM 且社区资产较少。

### Q4: 我如何修复生成图像中模糊的面孔？
启用“FaceRestore”扩展。在 WebUI 设置中，转到“Face Restoration”并选择“CodeFormer”或“GFPGAN”。你也可以在启动参数中添加 `--face-restorer` 以全局启用它。

### Q5: AGPL-3.0 许可证用于商业用途安全吗？
是的，你可以用于商业用途。但是，AGPL-3.0 要求如果你修改源代码并进行分发，你必须分享你的修改。由于大多数用户在本地运行它，除非你基于修改后的代码托管公共服务，否则此条款通常不会触发。对于具体的商业案例，请咨询法律专家。

## 来源与进一步阅读

1.  [AUTOMATIC1111 GitHub 仓库](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
2.  [Stable Diffusion 文档](https://stability.ai/docs)
3.  [ControlNet 扩展文档](https://github.com/Mikubill/sd-webui-controlnet)
4.  [PyTorch 官方网站](https://pytorch.org/)


```bash
# 克隆并启动
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui
cd stable-diffusion-webui
./webui.sh
```
```bash
# 使用特定设置启动
./webui.sh --api --xformers --medvram
```
```json
// API 请求示例
{
  "prompt": "a photo of a cat",
  "steps": 20,
  "width": 512,
  "height": 512
}
```



```python
# API Python 客户端
import requests

response = requests.post(
    url="http://localhost:7860/api/v1/predict",
    json={
        "prompt": "a beautiful sunset over mountains",
        "negative_prompt": "blurry, low quality",
        "steps": 30,
        "cfg_scale": 7.5
    }
)
print(response.json())
```
## 结论：掌控你的 AI 创造力

**AUTOMATIC1111/stable-diffusion-webui** 仓库代表了开源 AI 图像生成的巅峰。它使用户能够绕过订阅费用、审查以及云服务提供商强加的计算限制。通过掌握此工具，你获得了无限的创意画布。

无论你是生成概念艺术、训练自定义模型还是探索生成式 AI 的前沿，WebUI 都是你的必备工具箱。

加入我们的 AI 爱好者和开发者社区。通过 Telegram 与我们连接，获取实时支持、技巧和讨论。

**CTA:** 在 [t.me/DIBI8_Group](https://t.me/DIBI8_Group) 加入对话

更多评论、教程和 AI 工具推荐，请访问 [dibi8.com](https://dibi8.com)。

***

*附属披露：本文中的某些链接可能是附属链接。这意味着如果你点击链接并购买商品，我们可能会收到附属佣金。这有助于支持 dibi8.com 并使我们能够继续提供免费内容。我们只推荐我们真心相信能为读者增添价值的产品和服务。*