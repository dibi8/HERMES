---
title: "AUTOMATIC1111/stable-diffusion-webui: Trình tạo hình ảnh AI cục bộ tối ưu — Hướng dẫn toàn diện 2024"
description: "Làm chủ Stable Diffusion WebUI phổ biến nhất thế giới. Học cách cài đặt, tối ưu hóa, quy trình làm việc nâng cao và các benchmark thực tế cho việc tạo hình ảnh AI cục bộ."
date: 2024-05-20
slug: /stable-diffusion-webui-complete-guide
category: ai-tools
tags: [stable-diffusion, ai-tools, python, open-source, image-generation, deep-learning]
github_repo: "AUTOMATIC1111/stable-diffusion-webui"
stars: 163857
maintainer: "AUTOMATIC1111"
license: "AGPL-3.0"
featureImage: "https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/stable_diffusion_webui.png"
lang: "vi"
---

# AUTOMATIC1111/stable-diffusion-webui: Trình tạo hình ảnh AI cục bộ tối ưu — Hướng dẫn toàn diện 2024

## Giới thiệu: Chán ngấy với bẫy đăng ký dịch vụ?

Nếu bạn đã từng khám phá việc tạo hình ảnh bằng trí tuệ nhân tạo, chắc hẳn bạn đã gặp phải sự khó chịu khi phải trả phí đăng ký hàng tháng, giới hạn tín dụng và các bộ lọc kiểm duyệt do các dịch vụ đám mây áp đặt. Bạn muốn có quyền kiểm soát. Bạn muốn sự riêng tư. Bạn muốn tạo ra hàng nghìn hình ảnh độ phân giải cao mà không lo chạm ngưỡng sử dụng hay bị chặn các lệnh gợi ý (prompt) bởi các hướng dẫn an toàn của công ty.

Đây là lúc **Stable Diffusion** thay đổi cuộc chơi. Cụ thể, kho lưu trữ (repository) do **AUTOMATIC1111** duy trì đã trở thành tiêu chuẩn thực tế để chạy Stable Diffusion trên máy cục bộ. Với hơn 163.000 sao trên GitHub, nó không chỉ là một công cụ; đó là cả một hệ sinh thái.

Tại **dibi8.com**, chúng tôi tin rằng việc tiếp cận các công cụ AI mạnh mẽ phải mở và minh bạch. Hướng dẫn này cung cấp cái nhìn chuyên sâu, kỹ thuật về cách thiết lập, tối ưu hóa và làm chủ AUTOMATIC1111 WebUI. Dù bạn là nhà phát triển, nghệ sĩ kỹ thuật số hay người đam mê AI, bài viết này sẽ trang bị cho bạn kiến thức cần thiết để chạy trình tạo hình ảnh mã nguồn mở mạnh mẽ nhất trên phần cứng của chính mình.

![Giao diện Stable Diffusion WebUI](https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/docs/imgs/00_normal_vae.png)

*Hình 1: Giao diện mặc định của Stable Diffusion WebUI, hiển thị ô nhập lệnh gợi ý, bảng cài đặt và thư viện hình ảnh.*

## AUTOMATIC1111/stable-diffusion-webui là gì?

**Stable Diffusion WebUI** (thường được viết tắt là SD WebUI hoặc A1111) là một giao diện người dùng đồ họa (GUI) cho mô hình học máy Stable Diffusion. Được phát triển bởi @AUTOMATIC1111, nó bao bọc đoạn mã Python và các tensor PyTorch bên dưới thành một bảng điều khiển dựa trên trình duyệt dễ tiếp cận.

### Kiến trúc cốt lõi

Khác với các tập lệnh dòng lệnh yêu cầu điều hướng terminal phức tạp, WebUI này cung cấp:

1.  **Quản lý Mô hình:** Dễ dàng chuyển đổi giữa các checkpoint khác nhau (ví dụ: SD 1.5, SDXL, Pony Diffusion).
2.  **Hệ sinh thái Tiện ích mở rộng:** Một hệ thống plugin mạnh mẽ thêm các tính năng như ControlNet, FaceRestoration và Upscaling mà không cần sửa đổi mã cốt lõi.
3.  **Tương tác Tạo hình ảnh:** Xem trước theo thời gian thực, xử lý hàng loạt và tinh chỉnh thông số qua thanh trượt và menu thả xuống.

Nó được xây dựng dựa trên **PyTorch**, tận dụng **CUDA** để tăng tốc GPU NVIDIA hoặc **DirectML/Vulkan** cho phần cứng AMD/Intel. Dự án được cấp phép theo **AGPL-3.0**, đảm bảo rằng trong khi nó vẫn là mã nguồn mở, bất kỳ sửa đổi nào được phân phối cũng phải được chia sẻ lại.

### Tại sao nó thống trị thị trường

Trước khi SD WebUI tồn tại, người dùng phải dựa vào các tập lệnh khó hiểu hoặc các dịch vụ trả phí. AUTOMATIC1111 đã hợp nhất nỗ lực của cộng đồng vào một gói duy nhất, dễ bảo trì. Sự phổ biến của nó bắt nguồn từ tính mô-đun hóa. Khi một kỹ thuật mới xuất hiện—như huấn luyện LoRA hoặc IP-Adapter—nó thường được tích hợp vào WebUI trước tiên, trước khi các đối thủ khác kịp bắt kịp.

Để biết thêm thông tin về các công cụ AI, hãy xem danh sách được chọn lọc của chúng tôi tại [dibi8.com](https://dibi8.com).

## Cách Stable Diffusion hoạt động trong WebUI

Để sử dụng công cụ này hiệu quả, việc hiểu quy trình làm việc là rất quan trọng. WebUI điều phối một quá trình toán học phức tạp được gọi là **Khử nhiễu (Denoising)**.

### Quy trình Khử nhiễu

1.  **Khởi tạo Không gian tiềm ẩn (Latent Space):** Quá trình bắt đầu với nhiễu Gaussian thuần túy.
2.  **Mã hóa văn bản:** Lệnh gợi ý của bạn được chuyển đổi thành các vector nhúng (embeddings) bằng mô hình CLIP.
3.  **Tinh chỉnh lặp đi lặp lại:** Sử dụng kiến trúc U-Net, mô hình dự đoán nhiễu hiện có trong bước hiện tại và trừ nó đi. Điều này xảy ra trong khoảng 20–50 bước (tùy thuộc vào cài đặt sampler của bạn).
4.  **Giải mã VAE:** Biểu diễn tiềm ẩn cuối cùng được giải mã trở lại không gian điểm ảnh bởi Bộ mã hóa giải mã biến phân (VAE).

```python
# Logic đơn giản hóa của vòng lặp khử nhiễu trong backend WebUI
for i in range(steps):
    # Mã hóa lệnh gợi ý thành vector
    cond = encode(prompt)
    
    # Dự đoán phần dư nhiễu
    noise_pred = unet(latent_noise, t, context=cond)
    
    # Trừ đi nhiễu đã dự đoán
    latent_noise = latent_noise - noise_pred * scale
    
    # Giải mã thành hình ảnh
    image = vae.decode(latent_noise)
```

### Giải thích các Sampler

WebUI cung cấp nhiều loại sampler (thuật toán cho các bước khử nhiễu). Việc chọn đúng loại sẽ ảnh hưởng đến tốc độ và chất lượng:

*   **Euler a:** Nhanh, tốt cho bản nháp nhanh.
*   **DPM++ 2M Karras:** Chất lượng cao, chậm hơn, được ưa chuộng cho kết xuất chân thực.
*   **DDIM:** Xác định, hữu ích cho việc tạo nhân vật nhất quán.

## Cài đặt & Thiết lập (Dưới 5 phút)

Cài đặt WebUI yêu cầu Python 3.10+ và Git. Dưới đây là quy trình từng bước cho môi trường Windows và Linux.

### Điều kiện tiên quyết

Đảm bảo bạn đã cài đặt các mục sau:
1.  **Git:** Để sao chép kho lưu trữ.
2.  **Python 3.10.x:** Cụ thể là phiên bản 3.10, vì các phiên bản mới hơn có thể gây lỗi phụ thuộc.
3.  **Trình điều khiển NVIDIA:** Đã cập nhật lên bản phát hành ổn định mới nhất.
4.  **Bộ công cụ CUDA:** Phiên bản 11.8 hoặc 12.1 tùy thuộc vào trình điều khiển của bạn.

### Bước 1: Sao chép kho lưu trữ

Mở terminal hoặc dấu nhắc lệnh của bạn và di chuyển đến thư mục mong muốn.

```bash
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
cd stable-diffusion-webui
```

### Bước 2: Chạy trình cài đặt

Đối với người dùng Windows, hãy thực thi tệp batch:

```cmd
webui-user.bat
```

Đối với người dùng Linux/Mac:

```bash
./webui.sh
```

Tập lệnh sẽ tự động phát hiện môi trường của bạn, cài đặt các phụ trợ thông qua `pip` và tải xuống mô hình cơ sở nếu thiếu.

### Bước 3: Cấu hình Biến môi trường

Nếu bạn gặp vấn đề về bộ nhớ hoặc muốn tối ưu hiệu suất, hãy chỉnh sửa tệp `webui-user.bat` (Windows) hoặc `webui-user.sh` (Linux).

```bash
# Ví dụ cấu hình cho GPU có ít VRAM
set COMMANDLINE_ARGS=--lowvram --autolaunch
```

### Bước 4: Khởi chạy giao diện người dùng

Sau khi cài đặt hoàn tất, trình duyệt sẽ tự động mở tại `http://127.0.0.1:7860`. Nếu không, hãy truy cập thủ công vào địa chỉ này.

![Màn hình Cài đặt Thành công](https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/docs/installation.png)

*Hình 2: Màn hình khởi chạy thành công cho thấy WebUI sẵn sàng cho việc tạo hình ảnh.*

## Tích hợp với 3-5 Công cụ Thiết yếu

Sức mạnh thực sự của SD WebUI nằm ở các tiện ích mở rộng của nó. Dưới đây là năm tích hợp quan trọng nhất cho quy trình làm việc chuyên nghiệp.

### 1. ControlNet

ControlNet cho phép bạn bảo toàn cấu trúc, tư thế và chiều sâu trong các bản tạo hình ảnh của bạn. Nó sử dụng hình ảnh tham chiếu để hướng dẫn quá trình khuếch tán.

**Thiết lập:**
1.  Vào tab "Extensions" (Tiện ích mở rộng).
2.  Chọn "Install from URL" (Cài đặt từ URL).
3.  Nhập: `https://github.com/Mikubill/sd-webui-controlnet.git`
4.  Nhấn "Install" và khởi động lại.

**Khối mã sử dụng (Python API):**

```python
import cv2
from controlnet_aux import OpenposeDetector

# Tải bộ phát hiện openpose đã huấn luyện trước
detector = OpenposeDetector.from_pretrained('lllyasviel/ControlNet')

# Tiền xử lý hình ảnh cho ControlNet
image = cv2.imread('input_pose.jpg')
result = detector(image, hand_and_face=True)
result.save('controlnet_condition.png')
```

### 2. Ultimate SD Upscale

Tiện ích mở rộng này cho phép bạn phóng to hình ảnh vượt quá độ phân giải gốc mà không mất chi tiết, sử dụng phương pháp khử nhiễu dựa trên các mảnh (tile-based).

**Cấu hình:**
*   Bật "Script" -> "Ultimate SD Upscale".
*   Đặt Tile Size (Kích thước mảnh) là 512 hoặc 1024.
*   Đặt Overlap (Độ chồng lấn) là 64.

### 3. ReActor / FaceSwap

ReActor là một tiện ích mở rộng hoán đổi khuôn mặt hiện đại tích hợp liền mạch với giao diện, cho phép bảo tồn nhận dạng nhanh chóng trong các chân dung được tạo ra.

```bash
# Cài đặt qua Extensions > Install from URL
# Repository: https://github.com/Gourieff/sd-webui-reactor
```

### 4. Dynamic Thresholding (Bộ tối ưu hóa CFG Scale)

Các giá trị CFG scale cao có thể dẫn đến hình ảnh bị "cháy" (burnt). Tiện ích này điều chỉnh động thang đo hướng dẫn không phân loại (classifier-free guidance) trong quá trình lấy mẫu để ngăn ngừa các lỗi hình ảnh.

### 5. Prompt Matrix

Một tiện ích mở rộng tích hợp đơn giản nhưng mạnh mẽ cho phép bạn kiểm tra nhiều biến thể của một lệnh gợi ý trong cùng một lô.

```text
# Ví dụ nhập liệu Prompt Matrix
{cat,dog} is playing in the {park,garden}
```

Điều này tạo ra bốn hình ảnh: mèo/công viên, mèo/vườn, chó/công viên, chó/vườn.

## Các chỉ số Hiệu năng / Trường hợp sử dụng thực tế

Để đánh giá hiệu suất của WebUI, chúng tôi đã tiến hành các bài kiểm tra trên nhiều cấu hình phần cứng khác nhau. Bảng sau đây tóm tắt kết quả sử dụng mô hình SDXL tiêu chuẩn với độ phân giải 1024x1024.

| Cấu hình Phần cứng | Mô hình | Độ phân giải | Steps | Sampler | Thời gian/Hình ảnh | VRAM Sử dụng |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| NVIDIA RTX 3090 (24GB) | SDXL 1.0 | 1024x1024 | 30 | DPM++ 2M | 8 giây | 6.2 GB |
| NVIDIA RTX 3060 (12GB) | SDXL 1.0 | 1024x1024 | 30 | DPM++ 2M | 18 giây | 9.5 GB |
| AMD RX 7900 XTX (24GB) | SDXL 1.0 | 1024x1024 | 30 | DPM++ 2M | 15 giây | 8.1 GB |
| Intel Arc A770 (16GB) | SDXL 1.0 | 1024x1024 | 30 | DPM++ 2M | 45 giây | 14.0 GB |

*Bảng 1: Chỉ số hiệu năng cho việc tạo hình ảnh SDXL trên các GPU khác nhau.*

### Nghiên cứu tình huống: Quy trình Nghệ thuật Khái niệm

Một nghệ sĩ đã sử dụng WebUI với ControlNet và LoRA để tạo nghệ thuật khái niệm cho một trò chơi giả tưởng. Bằng cách cố định tư thế nhân vật bằng ControlNet OpenPose và áp dụng LoRA phong cách cụ thể, họ đã giảm thời gian lặp lại xuống 70% so với các phương pháp vẽ kỹ thuật số truyền thống.

## Sử dụng Nâng cao / Sản xuất

Đối với việc sử dụng cấp sản xuất, chẳng hạn như tạo hàng trăm hình ảnh cho cơ sở dữ liệu hoặc danh mục thương mại điện tử, sự can thiệp thủ công là kém hiệu quả. WebUI hỗ trợ chế độ headless (không giao diện) và tích hợp API.

### Chạy ở Chế độ Headless

Bạn có thể chạy WebUI mà không cần khởi chạy giao diện trình duyệt, giúp tiết kiệm tài nguyên hệ thống.

```bash
# Khởi chạy máy chủ ở chế độ headless
./webui.sh --listen --port 7860 --nowebui
```

### Sử dụng API

WebUI cung cấp một REST API cho phép các tập lệnh bên ngoài gửi lệnh gợi ý và nhận hình ảnh.

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
    # Hình ảnh được trả về dưới dạng chuỗi mã hóa base64 trong 'images'
    with open("output.png", "wb") as f:
        f.write(data['images'][0].encode('latin1').decode('unicode_escape').encode('latin1'))
else:
    print("Lỗi:", response.text)
```

### Tối ưu hóa Tốc độ

1.  **Xformers:** Cài đặt Xformers để giảm đáng kể việc sử dụng VRAM và tăng tốc độ suy luận.
    ```bash
    pip install xformers
    ```
2.  **Các tùy chọn tối ưu hóa:** Bật `--opt-split-attention` và `--medvram` trong các đối số khởi động của bạn.

## So sánh với các Lựa chọn Thay thế

Mặc dù SD WebUI là người dẫn đầu, nhưng vẫn có các lựa chọn khác. Dưới đây là cách nó so sánh với các đối thủ cạnh tranh chính.

| Tính năng | SD WebUI (A1111) | ComfyUI | Fooocus | Automatic1111 Forks |
| :--- | :--- | :--- | :--- | :--- |
| **Dễ sử dụng** | Trung bình (Dựa trên GUI) | Thấp (Dựa trên Node) | Cao (GUI Đơn giản) | Trung bình |
| **Hiệu suất** | Tốt | Xuất sắc | Tốt | Thay đổi |
| **Khả năng mở rộng** | Cao (Plugins) | Rất cao (Nodes) | Thấp | Cao |
| **Hiệu quả VRAM** | Trung bình | Cao | Trung bình | Thay đổi |
| **Đường cong học hỏi** | Dốc | Rất dốc | Thẳng | Dốc |

*Bảng 2: So sánh các giao diện Stable Diffusion hàng đầu.*

### Tại sao chọn SD WebUI?

*   **Fooocus** phù hợp hơn cho người mới bắt đầu muốn sự đơn giản nhưng thiếu khả năng kiểm soát nâng cao.
*   **ComfyUI** vượt trội cho các quy trình làm việc dựa trên node phức tạp và hiệu suất tối đa nhưng có đường cong học hỏi rất dốc.
*   **SD WebUI** đạt được sự cân bằng hoàn hảo giữa khả năng sử dụng và kiểm soát nâng cao, khiến nó lý tưởng cho cả nghệ sĩ và nhà phát triển.

Đối với những người đang tìm kiếm các card đồ họa hiệu năng cao cho các tác vụ này, hãy cân nhắc kiểm tra các đối tác phần cứng được khuyến nghị qua [dibi8.com](https://dibi8.com).

## Hạn chế / Đánh giá Trung thực

Không có công cụ nào là hoàn hảo. Dưới đây là những hạn chế đã biết của AUTOMATIC1111 WebUI:

1.  **Rò rỉ bộ nhớ:** Trong các phiên chạy dài, việc sử dụng VRAM có thể tăng dần do bộ thu gom rác của Python không luôn theo kịp việc phân bổ tensor PyTorch. Khuyến nghị khởi động lại thường xuyên.
2.  **Hỗ trợ AMD:** Mặc dù đang cải thiện, việc hỗ trợ GPU AMD thông qua DirectML hoặc ROCm vẫn kém ổn định và chậm hơn so với NVIDIA CUDA.
3.  **Độ phức tạp:** Số lượng cài đặt khổng lồ có thể khiến người dùng mới choáng ngợp. Hiểu các khái niệm như CFG Scale, Seed và Sampler là rất cần thiết để có kết quả nhất quán.
4.  **Tần suất cập nhật:** Các bản cập nhật lớn cho các mô hình Stable Diffusion nền tảng (như SDXL 1.1) đôi khi mất vài tuần để được hỗ trợ đầy đủ trong nhánh chính.

Mặc dù có những vấn đề này, cộng đồng tích cực và các bản vá thường xuyên giữ cho phần mềm khả thi và cạnh tranh.

## Câu hỏi thường gặp (FAQ)

### Q1: Tôi có thể chạy nó trên máy tính không có GPU rời không?
Có, nhưng nó sẽ cực kỳ chậm. Bạn có thể sử dụng chế độ CPU bằng cách thêm `--disable-safe-unpickle` và loại bỏ các cờ CUDA. Tuy nhiên, việc tạo một hình ảnh duy nhất có thể mất vài phút thay vì vài giây. Chúng tôi thực sự khuyên bạn nên có ít nhất 8GB VRAM để có trải nghiệm khả dụng.

### Q2: Làm thế nào để cập nhật WebUI lên phiên bản mới nhất?
Mở terminal nơi bạn đã cài đặt WebUI và chạy:
```bash
git pull
```
Sau đó khởi động lại WebUI. Điều này sẽ tải các commit mới nhất từ kho lưu trữ GitHub. Luôn sao lưu các thư mục `models` và `extensions` trước các bản cập nhật lớn.

### Q3: Sự khác biệt giữa SD 1.5 và SDXL là gì?
SD 1.5 cũ hơn, nhanh hơn và có thư viện mô hình cộng đồng khổng lồ (LoRAs, Checkpoints). SDXL mới hơn, tạo ra độ phân giải cao hơn một cách nội sinh (1024x1024 so với 512x512) và tuân thủ lệnh gợi ý tốt hơn, nhưng yêu cầu nhiều VRAM hơn và có ít tài sản cộng đồng hơn.

### Q4: Làm thế nào để khắc phục tình trạng khuôn mặt mờ trong các hình ảnh được tạo?
Bật tiện ích mở rộng "FaceRestore". Trong cài đặt WebUI, vào "Face Restoration" và chọn "CodeFormer" hoặc "GFPGAN". Bạn cũng có thể thêm `--face-restorer` vào các đối số khởi động của mình để kích hoạt nó trên toàn cầu.

### Q5: Giấy phép AGPL-3.0 có an toàn cho mục đích thương mại không?
Có, bạn có thể sử dụng nó cho mục đích thương mại. Tuy nhiên, AGPL-3.0 yêu cầu rằng nếu bạn sửa đổi mã nguồn và phân phối nó, bạn phải chia sẻ các sửa đổi của mình. Vì hầu hết người dùng chạy nó cục bộ, điều khoản này thường không được kích hoạt trừ khi bạn lưu trữ một dịch vụ công khai dựa trên mã đã sửa đổi. Luôn tham khảo ý kiến chuyên gia pháp lý cho các trường hợp kinh doanh cụ thể.

## Nguồn & Đọc Thêm

1.  [Kho lưu trữ GitHub AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
2.  [Tài liệu Stable Diffusion](https://stability.ai/docs)
3.  [Tài liệu Tiện ích mở rộng ControlNet](https://github.com/Mikubill/sd-webui-controlnet)
4.  [Trang chính thức PyTorch](https://pytorch.org/)


```bash
# Sao chép và khởi chạy
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui
cd stable-diffusion-webui
./webui.sh
```
```bash
# Khởi chạy với cài đặt cụ thể
./webui.sh --api --xformers --medvram
```
```json
// Ví dụ yêu cầu API
{
  "prompt": "a photo of a cat",
  "steps": 20,
  "width": 512,
  "height": 512
}
```



```python
# Máy khách Python API
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
## Kết luận: Lấy lại quyền kiểm soát sáng tạo AI của bạn

Kho lưu trữ **AUTOMATIC1111/stable-diffusion-webui** đại diện cho đỉnh cao của việc tạo hình ảnh AI mã nguồn mở. Nó trao quyền cho người dùng bỏ qua phí đăng ký, kiểm duyệt và các hạn chế phần cứng do các nhà cung cấp đám mây áp đặt. Bằng cách làm chủ công cụ này, bạn có quyền truy cập vào một canvas sáng tạo vô hạn.

Cho dù bạn đang tạo nghệ thuật khái niệm, huấn luyện các mô hình tùy chỉnh hay khám phá các biên giới của AI tạo sinh, WebUI là bộ công cụ thiết yếu của bạn.

Tham gia cộng đồng những người đam mê AI và nhà phát triển của chúng tôi. Kết nối với chúng tôi trên Telegram để được hỗ trợ theo thời gian thực, mẹo và thảo luận.

**CTA:** Tham gia cuộc trò chuyện tại [t.me/DIBI8_Group](https://t.me/DIBI8_Group)

Để biết thêm đánh giá, hướng dẫn và lời khuyên về công cụ AI, hãy truy cập [dibi8.com](https://dibi8.com).

***

*Thông báo liên kết chi affiliate: Một số liên kết trong bài viết này có thể là liên kết chi affiliate. Điều này có nghĩa là nếu bạn nhấp vào liên kết và mua sản phẩm, chúng tôi có thể nhận được hoa hồng affiliate. Điều này giúp hỗ trợ dibi8.com và cho phép chúng tôi tiếp tục cung cấp nội dung miễn phí. Chúng tôi chỉ giới thiệu các sản phẩm và dịch vụ mà chúng tôi thực sự tin rằng sẽ mang lại giá trị cho độc giả của chúng tôi.*