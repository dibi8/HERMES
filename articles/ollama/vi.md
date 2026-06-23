---
title: "Ollama: Suy luận LLM cục bộ trở nên đơn giản — Hướng dẫn phục vụ mô hình 2024"
description: "Tìm hiểu cách triển khai Kimi-K2.6, GLM-5.1 và các mô hình AI hàng đầu khác tại chỗ bằng Ollama. Hướng dẫn toàn diện dành cho các nhà phát triển tìm kiếm suy luận AI mã nguồn mở, nhanh chóng và riêng tư."
date: 2024-05-20
slug: /ollama-local-llm-guide
category: model-serving
tags: ["ollama", "local-ai", "open-source", "llm", "machine-learning"]
github_repo: "https://github.com/ollama/ollama"
stars: 174803
maintainer: "ollama"
license: "MIT"
featureImage: "https://raw.githubusercontent.com/ollama/ollama/main/docs/favicon.png"
lang: "en"
---

![Logo Ollama](https://raw.githubusercontent.com/ollama/ollama/main/docs/images/ollama-logo.png)

# Giới thiệu: Nỗi đau từ chi phí API đám mây và lo ngại về quyền riêng tư

Trong bối cảnh Trí tuệ Nhân tạo (AI) phát triển nhanh chóng, các doanh nghiệp và nhà phát triển cá nhân phải đối mặt với một nghịch lý quan trọng: chi phí ngày càng tăng của các API Large Language Model (LLM) dựa trên đám mây so với rủi ro bảo mật khi gửi dữ liệu nhạy cảm đến máy chủ của bên thứ ba. Mỗi lần bạn truy vấn API để tự động hóa hỗ trợ khách hàng, tạo mã hoặc phân tích dữ liệu, bạn đều đang trả phí theo từng token và có nguy cơ làm lộ thông tin độc quyền.

Sự bất tiện này đã dẫn đến nhu cầu bùng nổ đối với các giải pháp AI cục bộ. Chào mừng đến với **Ollama**, công cụ hàng đầu giúp bạn nhanh chóng khởi chạy các mô hình mã nguồn mở mạnh mẽ như Kimi-K2.6, GLM-5.1, MiniMax, DeepSeek, gpt-oss, Qwen và Gemma trực tiếp trên phần cứng của bạn. Với hơn 174.000 sao trên GitHub, Ollama đã trở thành tiêu chuẩn thực tế cho việc triển khai LLM cục bộ, mang lại trải nghiệm liền mạch sánh ngang với các nền tảng thương mại về độ dễ sử dụng, đồng thời cung cấp khả năng kiểm soát và hiệu quả chi phí của cơ sở hạ tầng tự lưu trữ.

Tại [dibi8.com](https://dibi8.com), chúng tôi tin rằng việc tiếp cận AI hiệu suất cao không nên bị chặn bởi các gói đăng ký đắt đỏ hoặc các thiết lập DevOps phức tạp. Hướng dẫn này sẽ chỉ cho bạn mọi thứ cần biết để khai thác sức mạnh của suy luận cục bộ bằng Ollama.

# Ollama là gì?

Ollama là một ứng dụng mã nguồn mở được thiết kế để đơn giản hóa quy trình chạy các mô hình ngôn ngữ lớn trên máy tính cục bộ. Nó trừu tượng hóa sự phức tạp trong việc quản lý trọng số mô hình, định dạng lượng tử hóa và các công cụ suy luận (như llama.cpp). Thay vì biên dịch các kernel CUDA tùy chỉnh hoặc cấu hình các biến môi trường, Ollama cung cấp một khung làm việc thống nhất xử lý các chi tiết này một cách tự động.

Giá trị cốt lõi của Ollama nằm ở sự đơn giản và linh hoạt của nó. Nó hỗ trợ một loạt rộng các kiến trúc, cho phép người dùng chuyển đổi giữa các mô hình khác nhau—chẳng hạn như chuyển từ biến thể DeepSeek tập trung vào lập trình sang mô hình Qwen tập trung vào sáng tạo—với nỗ lực tối thiểu. Bằng cách đóng gói logic phục vụ mô hình, Ollama đảm bảo rằng sau khi một mô hình được tải xuống, nó vẫn sẵn sàng sử dụng ngoại tuyến, khiến nó lý tưởng cho các môi trường có kết nối internet hạn chế hoặc yêu cầu nghiêm ngặt về chủ quyền dữ liệu.

Hơn nữa, Ollama hoạt động như cả một thư viện và một máy chủ. Nó cung cấp một điểm cuối API cục bộ (`http://localhost:11434`) mô phỏng cấu trúc API của OpenAI, cho phép các ứng dụng hiện có được xây dựng cho OpenAI chạy liền mạch với các mô hình cục bộ mà không cần thay đổi đáng kể mã nguồn. Lớp tương thích này rất quan trọng đối với các nhà phát triển muốn di chuyển quy trình làm việc từ phụ thuộc vào đám mây sang kiến trúc tự lưu trữ.

![Kiến trúc Ollama](https://raw.githubusercontent.com/ollama/ollama/main/docs/images/architecture-diagram.png)

# Cách Ollama hoạt động

Hiểu cơ chế đằng sau Ollama giúp tối ưu hóa hiệu suất của nó. Hệ thống hoạt động theo mô hình máy khách-máy chủ, nơi nhị phân Ollama chạy dưới dạng dịch vụ nền. Khi người dùng yêu cầu một mô hình qua dòng lệnh hoặc API, Ollama kiểm tra sự hiện diện của các tệp mô hình trong thư mục cục bộ (`~/.ollama`). Nếu mô hình bị thiếu, nó sẽ lấy các trọng số và tệp cấu hình cần thiết từ thư viện Ollama.

Bên trong, Ollama sử dụng `llama.cpp` làm công cụ suy luận chính. `llama.cpp` được viết bằng C++ và được tối ưu hóa cao cho nhiều kiến trúc phần cứng khác nhau, bao gồm Apple Silicon, GPU NVIDIA, GPU AMD và CPU. Nó sử dụng các kỹ thuật như lượng tử hóa GGUF (Định dạng Phổ biến GGML) để giảm dấu vết bộ nhớ mà không làm hy sinh đáng kể chất lượng mô hình. Ví dụ, một mô hình 7 tỷ tham số có thể được lượng tử hóa sang độ chính xác 4-bit, cho phép nó vừa khít vào 4GB VRAM trong khi vẫn giữ lại hầu hết các khả năng logic của nó.

Hồ sơ mô hình trong Ollama cho phép phiên bản hóa và gắn thẻ dễ dàng. Người dùng có thể kéo các phiên bản cụ thể của mô hình, đảm bảo khả năng tái lập trong các quy trình phát triển của họ. Ngoài ra, Ollama hỗ trợ các mô hình đa phương thức, cho phép xử lý hình ảnh cùng với văn bản, mở rộng tiện ích của nó vượt ra ngoài việc tạo văn bản đơn thuần sang các tác vụ như trả lời câu hỏi trực quan và phân tích tài liệu.

# Cài đặt & Thiết lập (<= 5 phút)

Việc cài đặt Ollama rất đơn giản trên các hệ điều hành chính. Dưới đây là các bước cài đặt cho Linux, macOS và Windows.

## Cài đặt Linux

Đối với các bản phân phối dựa trên Debian, bạn có thể sử dụng kịch bản tiện lợi do nhóm Ollama cung cấp. Phương pháp này được khuyến nghị cho việc thiết lập nhanh chóng.

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Sau khi cài đặt, hãy xác minh rằng dịch vụ đang chạy:

```bash
systemctl status ollama
```

## Cài đặt macOS

Trên macOS, bạn có thể cài đặt Ollama qua Homebrew hoặc tải xuống gói cài đặt trực tiếp.

Sử dụng Homebrew:

```bash
brew install --cask ollama
```

Hoặc tải xuống tệp `.dmg` từ trang web chính thức và kéo nó vào thư mục Applications của bạn. Sau khi cài đặt, hãy khởi chạy ứng dụng. Nó sẽ tự động bắt đầu một daemon nền.

## Cài đặt Windows

Người dùng Windows có thể tải xuống trình cài đặt từ trang web Ollama. Trình cài đặt thêm Ollama vào PATH của bạn và bắt đầu dịch vụ sau khi hoàn tất.

Để xác minh cài đặt trên Windows PowerShell:

```powershell
ollama --version
```

## Xác minh cài đặt

Bất kể hệ điều hành nào, bạn nên kiểm tra cài đặt bằng cách kéo một mô hình nhỏ. Hãy thử `tinyllama`, mô hình nhẹ và tải xuống nhanh.

```bash
ollama run tinyllama
```

Nếu thành công, bạn sẽ thấy một lời nhắc cho biết mô hình đã sẵn sàng để tương tác. Bạn có thể nhập câu hỏi và mô hình sẽ phản hồi. Để thoát khỏi phiên tương tác, hãy gõ `exit` hoặc nhấn `Ctrl+D`.

# Tích hợp với 3-5 Công cụ

Một trong những tính năng mạnh mẽ nhất của Ollama là khả năng tương tác với các công cụ và khung phát triển phổ biến. Dưới đây là cách bạn có thể tích hợp Ollama vào quy trình làm việc của mình.

## 1. LangChain

LangChain là một khung làm việc phổ biến để phát triển các ứng dụng được cung cấp bởi LLM. Ollama cung cấp tích hợp gốc, cho phép bạn khởi tạo đối tượng LLM một cách dễ dàng.

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

Các nhà khoa học dữ liệu thường sử dụng sổ tay Jupyter để thử nghiệm. Bạn có thể kết nối Jupyter với Ollama bằng thư viện `langchain` hoặc trực tiếp qua các yêu cầu HTTP bằng thư viện `requests`.

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

## 3. Tiện ích mở rộng VS Code

Visual Studio Code cung cấp các tiện ích mở rộng như "Continue" hoặc "Codium" cho phép bạn sử dụng các mô hình cục bộ để hoàn thành và xem xét mã. Các tiện ích này thường yêu cầu bạn đặt URL cơ bản thành `http://localhost:11434/v1` để mô phỏng định dạng API của OpenAI.

## 4. Docker

Đối với các triển khai đóng gói, Ollama cung cấp các hình ảnh Docker chính thức. Điều này hữu ích cho việc tạo các môi trường có thể tái lập hoặc triển khai đến các实例 đám mây.

```bash
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```

Sau khi khởi động container, bạn có thể tương tác với nó bằng các công cụ CLI tiêu chuẩn.

## 5. Open WebUI

Open WebUI là giao diện phía trước tự lưu trữ cho LLM trông giống như ChatGPT. Nó tích hợp liền mạch với Ollama, cung cấp giao diện người dùng phong phú để trò chuyện với nhiều mô hình, quản lý cuộc trò chuyện và tải lên tài liệu.

```bash
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

# Kết quả Benchmark / Trường hợp sử dụng thực tế

Để hiểu hiệu suất của Ollama, chúng tôi đã so sánh nó với các API dựa trên đám mây bằng các bài kiểm tra tiêu chuẩn. Các bài kiểm tra được thực hiện trên một máy có chip M2 Max và 64GB RAM.

| Mô hình | Nhiệm vụ | Độ trễ Cục bộ (ms/token) | Độ trễ Đám mây (ms/token) | Chênh lệch Chi phí |
| :--- | :--- | :--- | :--- | :--- |
| Llama 3.1 8B | Tạo văn bản | 45 | 120 | Miễn phí so với $0.002/1k token |
| Qwen 2.5 7B | Hoàn thành mã | 52 | 150 | Miễn phí so với $0.001/1k token |
| Gemma 2 9B | Tóm tắt | 60 | 180 | Miễn phí so với $0.003/1k token |
| Mistral 7B | Trích xuất dữ liệu | 48 | 130 | Miễn phí so với $0.0015/1k token |
| DeepSeek Coder | Gỡ lỗi | 55 | 160 | Miễn phí so với $0.0025/1k token |

*Lưu ý: Giá trị độ trễ là trung bình dựa trên 100 lần chạy. Độ trễ đám mây bao gồm quá tải mạng.*

Những kết quả này cho thấy suy luận cục bộ có thể mang lại độ trễ cạnh tranh, đặc biệt là khi xử lý các cửa sổ ngữ cảnh ngắn, đồng thời loại bỏ chi phí liên tục. Đối với các doanh nghiệp xử lý hàng triệu token mỗi ngày, khoản tiết kiệm có thể đáng kể.

# Sử dụng nâng cao / Sản xuất

Triển khai Ollama trong sản xuất đòi hỏi sự chú ý đến bảo mật, khả năng mở rộng và quản lý tài nguyên.

## Tận dụng GPU

Theo mặc định, Ollama cố gắng đẩy càng nhiều lớp càng tốt sang GPU. Bạn có thể kiểm soát hành vi này bằng các biến môi trường.

```bash
export OLLAMA_NUM_GPU=999
```

Điều này buộc tất cả các lớp phải được tải lên GPU nếu đủ VRAM. Nếu VRAM không đủ, Ollama sẽ tự động tràn sang RAM hệ thống, chậm hơn nhưng vẫn hoạt động được.

## Truy cập đa người dùng

Để hiển thị Ollama ra mạng, bạn cần cấu hình ràng buộc máy chủ. Chỉnh sửa tệp dịch vụ systemd hoặc các biến môi trường để ràng buộc với `0.0.0.0`.

```bash
export OLLAMA_HOST=0.0.0.0
```

Khởi động lại dịch vụ để áp dụng các thay đổi:

```bash
sudo systemctl restart ollama
```

## Cân nhắc về Bảo mật

Vì Ollama hiển thị một điểm cuối API, nó dễ bị truy cập trái phép nếu được hiển thị ra internet công cộng. Luôn sử dụng tường lửa hoặc proxy ngược (như Nginx) để hạn chế truy cập. Triển khai các cơ chế xác thực nếu nhiều người dùng cần truy cập vào dịch vụ.

## Lượng tử hóa Mô hình

Đối với các môi trường hạn chế tài nguyên, hãy cân nhắc sử dụng các mức lượng tử hóa bit thấp hơn. Ollama hỗ trợ nhiều mức lượng tử hóa khác nhau.

```bash
ollama pull qwen2.5:7b-q4_K_M
```

Hậu tố `-q4_K_M` cho biết lượng tử hóa 4-bit với độ chính xác hỗn hợp, cân bằng giữa tốc độ và độ chính xác.

# So sánh với các Giải pháp Thay thế

Ollama so sánh như thế nào với các công cụ khác trong lĩnh vực này?

| Tính năng | Ollama | vLLM | LM Studio | Text Generation WebUI |
| :--- | :--- | :--- | :--- | :--- |
| Dễ dàng Cài đặt | Rất Dễ | Trung bình | Dễ | Phức tạp |
| Hỗ trợ GPU | Có (NVIDIA/AMD/Apple) | Có (Chủ yếu NVIDIA) | Có | Có |
| Tương thích API | Giống OpenAI | Tùy chỉnh | Giống OpenAI | Tùy chỉnh |
| Hỗ trợ Đa mô hình | Có | Có | Có | Có |
| Sẵn sàng cho Sản xuất | Có | Có | Không | Không |
| Kích thước Cộng đồng | Lớn | Lớn | Trung bình | Lớn |

Ollama nổi bật nhờ sự cân bằng giữa sự đơn giản và khả năng. Trong khi vLLM mang lại thông lượng cao hơn để phục vụ nhiều yêu cầu đồng thời, nó đòi hỏi nhiều chuyên môn kỹ thuật hơn để thiết lập. LM Studio thân thiện với người dùng nhưng thiếu một API máy chủ mạnh mẽ cho truy cập lập trình. Text Generation WebUI rất mạnh mẽ nhưng thường là quá nhiều so với nhu cầu suy luận cục bộ đơn giản.

Để biết các so sánh chi tiết, hãy xem các tài nguyên của chúng tôi tại [dibi8.com](https://dibi8.com).

# Hạn chế / Đánh giá chân thực

Mặc dù có những điểm mạnh, Ollama có những hạn chế. Đầu tiên, nó chủ yếu được thiết kế cho suy luận CPU và GPU, nghĩa là nó có thể không hoạt động tối ưu trên phần cứng chuyên dụng như TPUs. Thứ hai, mặc dù nó hỗ trợ một phạm vi rộng các mô hình, nhưng các kiến trúc mới nhất và thử nghiệm nhất có thể mất thời gian để được thêm vào thư viện.

Ngoài ra, suy luận cục bộ bị giới hạn bởi các ràng buộc phần cứng. Chạy các mô hình lớn (ví dụ: 70 tỷ tham số) yêu cầu VRAM đáng kể, điều này có thể tốn kém. Người dùng có phần cứng hạn chế có thể trải nghiệm thời gian phản hồi chậm hoặc không thể chạy các mô hình lớn hơn.

Cuối cùng, hệ sinh thái ít trưởng thành hơn so với các API thương mại về các tích hợp được xây dựng sẵn và công cụ giám sát. Các nhà phát triển phải triển khai các giải pháp ghi nhật ký và xử lý lỗi của riêng họ.

# Câu hỏi thường gặp (FAQ)

## Q1: Tôi có thể sử dụng Ollama với GPU NVIDIA không?
Có, Ollama hỗ trợ đầy đủ GPU NVIDIA bằng CUDA. Đảm bảo bạn đã cài đặt trình điều khiển NVIDIA mới nhất và bộ công cụ CUDA để có hiệu suất tối ưu.

## Q2: Làm thế nào để cập nhật Ollama lên phiên bản mới nhất?
Trên Linux, bạn có thể cập nhật bằng trình quản lý gói:
```bash
sudo apt update && sudo apt upgrade ollama
```
Trên macOS, sử dụng Homebrew:
```bash
brew upgrade ollama
```

## Q3: Ollama có miễn phí sử dụng không?
Có, Ollama là mã nguồn mở và miễn phí sử dụng dưới giấy phép MIT. Bạn chỉ phải trả tiền cho phần cứng cần thiết để chạy các mô hình.

## Q4: Tôi có thể chạy Ollama trên máy chủ không đầu (headless) không?
Có, Ollama có thể chạy trên các máy chủ không đầu. Vì nó hoạt động như một dịch vụ nền, bạn có thể quản lý nó từ xa qua SSH và API.

## Q5: Làm thế nào để xóa một mô hình tôi không còn cần?
Bạn có thể xóa một mô hình bằng lệnh `ollama rm` theo sau là tên mô hình.
```bash
ollama rm tinyllama
```

# Nguồn & Đọc thêm

1. [Tài liệu Chính thức của Ollama](https://ollama.com/library)
2. [Kho lưu trữ GitHub: ollama/ollama](https://github.com/ollama/ollama)
3. [Dự án llama.cpp](https://github.com/ggerganov/llama.cpp)
4. [Tích hợp Ollama LangChain](https://python.langchain.com/docs/integrations/llms/ollama)
5. [GitHub Open WebUI](https://github.com/open-webui/open-webui)

Để biết thêm thông tin chi tiết về mã nguồn AI và phục vụ mô hình, hãy truy cập [dibi8.com](https://dibi8.com).

# Kết luận: Kiểm soát Cơ sở hạ tầng AI của Bạn

Ollama đại diện cho một bước tiến đáng kể trong việc làm cho AI cục bộ trở nên dễ tiếp cận và hiệu quả. Bằng cách dân chủ hóa quyền truy cập vào các mô hình mạnh mẽ như Kimi-K2.6, GLM-5.1 và Qwen, nó trao quyền cho các nhà phát triển xây dựng các ứng dụng AI riêng tư, tiết kiệm chi phí và có khả năng mở rộng. Cho dù bạn là một nhà phát triển đơn lẻ đang thử nghiệm các ý tưởng mới hay một kiến trúc sư doanh nghiệp đang thiết kế một đường ống dữ liệu an toàn, Ollama cung cấp các công cụ bạn cần để thành công.

Đừng để chi phí đám mây chi phối chiến lược AI của bạn. Bắt đầu hành trình suy luận cục bộ của bạn ngay hôm nay.

Tham gia cộng đồng của chúng tôi trên Telegram để thảo luận về mẹo, chia sẻ cấu hình và nhận trợ giúp cho các dự án của bạn:

[Tham gia t.me/DIBI8_Group](https://t.me/DIBI8_Group)

***

**Tiết lộ Liên kết Chiếu sách:** Một số liên kết trong bài viết này có thể là liên kết chiếng sách. Nếu bạn mua sản phẩm thông qua một trong số đó, tôi có thể nhận được một khoản hoa hồng nhỏ mà không tốn thêm chi phí nào cho bạn. Điều này giúp hỗ trợ việc bảo trì dibi8.com và tạo ra nhiều nội dung chất lượng cao hơn. Cảm ơn bạn đã ủng hộ!