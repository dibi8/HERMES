---
title: "vLLM: Hướng dẫn toàn diện về việc phục vụ LLM hiệu suất cao năm 2026 — Đánh giá công cụ AI mã nguồn mở"
slug: "vllm-complete-guide"
stars: 83496
license: "Apache-2.0"
maintainer: "vllm-project"
category: "llm-serving"
author: "Agnes-2.0-Flash"
date: "2026-01-15"
tags: ["vLLM", "LLM Serving", "Open Source AI", "Python", "Deep Learning", "Inference Optimization"]
description: "Một bài đánh giá kỹ thuật toàn diện về vLLM, khám phá động cơ PagedAttention, các chỉ số hiệu năng, các bước cài đặt và chiến lược triển khai sản xuất cho suy luận LLM thông lượng cao."
---

# vLLM: Hướng dẫn toàn diện về việc phục vụ LLM hiệu suất cao năm 2026 — Đánh giá công cụ AI mã nguồn mở

Bối cảnh triển khai Mô hình Ngôn ngữ Lớn (LLM) đã thay đổi đáng kể so với những ngày đầu của các REST API đơn giản. Ngày nay, hiệu quả, thông lượng và tính kinh tế không còn là những tính năng "có thì tốt"; chúng là yêu cầu hạ tầng quan trọng. Nếu bạn đang chạy nhiều mô hình hoặc xử lý các yêu cầu có độ đồng thời cao, các khung phục vụ tiêu chuẩn thường gặp khó khăn dưới gánh nặng của chi phí bộ nhớ và quản lý bộ nhớ kém hiệu quả. Đây chính là lúc **vLLM** bước vào cuộc chơi, cung cấp một giải pháp mạnh mẽ mà nhanh chóng trở thành trụ cột cho các nhà phát triển tìm cách tối ưu hóa các triển khai AI của họ mà không hy chất lượng. Trong hướng dẫn này từ dibi8.com, chúng ta sẽ khám phá cách vLLM đạt được các chỉ số hiệu suất ấn tượng như vậy, cách thiết lập nó và tại sao nó vẫn là lựa chọn hàng đầu cho những người đam mê AI mã nguồn mở và các kỹ sư doanh nghiệp.

![vLLM Logo](https://raw.githubusercontent.com/vllm-project/vllm/main/docs/assets/logos/vllm-logo-text-dark.png)

## vLLM là gì?

vLLM là một thư viện mã nguồn mở được thiết kế đặc biệt cho việc suy luận và phục vụ LLM nhanh chóng và hiệu quả. Được phát triển bởi cộng đồng vllm-project, nó đã thu hút sự chú ý đáng kể, hiện có hơn 83.000 sao trên GitHub. Ở cốt lõi, vLLM giải quyết nút cổ chai về quản lý bộ nhớ trong các mô hình dựa trên kiến trúc Transformer. Các khung phục vụ truyền thống thường lãng phí một lượng lớn bộ nhớ GPU do sự phân mảnh và các chiến lược phân bổ bộ nhớ kém hiệu quả.

vLLM giải quyết vấn đề này thông qua một kỹ thuật gọi là **PagedAttention**. Lấy cảm hứng từ việc phân trang bộ nhớ ảo trong hệ điều hành, PagedAttention cho phép phân bổ bộ nhớ liên tục trong khi tránh sự phân mảnh. Điều này dẫn đến việc sử dụng bộ nhớ hiệu quả hơn, trực tiếp chuyển đổi thành thông lượng tốt hơn và độ trễ thấp hơn. Không giống như một số giải pháp độc quyền, vLLM hoàn toàn là mã nguồn mở dưới giấy phép Apache-2.0, giúp nó dễ tiếp cận với nhiều đối tượng người dùng khác nhau, từ nhà phát triển cá nhân đến các doanh nghiệp quy mô lớn. Nó hỗ trợ nhiều mô hình phổ biến, bao gồm Llama, Mistral, Qwen và các mô hình khác, đảm bảo tương thích với hệ sinh thái hiện tại của các công cụ AI tạo sinh.

Đối với những ai muốn tự lưu trữ các phiên bản phần mềm mạnh mẽ như vậy, việc tận dụng cơ sở hạ tầng đám mây đáng tin cậy là chìa khóa. Bạn có thể bắt đầu với các nút điện toán hiệu suất cao trên [DigitalOcean](https://m.do.co/c/eca87ac14ee0) để triển khai các máy chủ vLLM của mình một cách bảo mật và có khả năng mở rộng.

## vLLM hoạt động như thế nào

Để hiểu sức mạnh của vLLM, bạn cần nhìn sâu vào các đổi mới kiến trúc của nó. Điểm khác biệt chính là cơ chế PagedAttention. Trong các triển khai chú ý (attention) truyền thống, bộ nhớ được phân bổ tĩnh dựa trên độ dài chuỗi tối đa có thể. Điều này dẫn đến lãng phí đáng kể, đặc biệt khi nhiều yêu cầu có độ dài chuỗi ngắn nhưng hệ thống vẫn dành không gian cho các chuỗi dài.

### Cơ chế PagedAttention

PagedAttention coi các bộ nhớ đệm Key-Value (KV) như các trang trong bộ nhớ. Thay vì phân bổ một khối bộ nhớ khổng lồ ngay từ đầu, nó phân bổ các trang nhỏ, kích thước cố định một cách động khi cần thiết. Cách tiếp cận này mang lại nhiều lợi ích:

1.  **Hiệu quả bộ nhớ**: Bằng cách tránh phân bổ trước độ dài chuỗi tối đa, vLLM giảm thiểu đáng kể lãng phí bộ nhớ.
2.  **Phân bổ liên tục**: Nó đảm bảo rằng các truy cập bộ nhớ vẫn liên tục ở mức độ có thể, cải thiện tính cục bộ của bộ nhớ đệm (cache locality) và giảm độ trễ truy cập.
3.  **Điều chỉnh kích thước động**: Khi các yêu cầu tiến triển, bộ nhớ được phân bổ và giải phóng một cách hiệu quả, tương tự như cách hệ điều hành quản lý RAM.

```python
# Biểu diễn khái niệm về phân bổ bộ nhớ trong phương pháp truyền thống so với PagedAttention
# Truyền thống: Phân bổ tĩnh cho max_seq_len
traditional_memory = allocate(max_seq_len * batch_size)

# PagedAttention: Phân bổ động dựa trên mức sử dụng thực tế
# Các trang chỉ được phân bổ khi các token được tạo ra
paged_memory = allocate_pages(actual_seq_len * batch_size)
```

### Kiến trúc Engine

Engine của vLLM bao gồm ba thành phần chính: Scheduler (Bộ lên lịch), Executor (Bộ thực thi) và KV Cache Manager (Quản lý bộ nhớ đệm KV). Scheduler xử lý các yêu cầu đến, quyết định yêu cầu nào sẽ được xử lý tiếp theo dựa trên mức độ ưu tiên và tài nguyên khả dụng. Executor quản lý quá trình suy luận mô hình thực tế trên GPU. KV Cache Manager giám sát các trang bộ nhớ, đảm bảo dữ liệu được truy xuất và lưu trữ hiệu quả.

```python
from vllm import LLM, SamplingParams

# Khởi tạo engine vLLM
llm = LLM(model="meta-llama/Llama-2-7b")

# Định nghĩa các tham số lấy mẫu
sampling_params = SamplingParams(temperature=0.7, top_p=0.9)

# Tạo phản hồi
outputs = llm.generate("Hello, how are you?", sampling_params)
```

Kiến trúc này cho phép vLLM đạt được thông lượng cao ngay cả với các tài nguyên GPU hạn chế. Bằng cách tối ưu hóa phân cấp bộ nhớ, nó giảm thời gian chờ đợi cho việc truyền dữ liệu giữa CPU và GPU, do đó tăng tốc quá trình suy luận tổng thể.

## Cài đặt & Thiết lập

Việc thiết lập vLLM khá đơn giản nhờ vào việc phân phối gói Python của nó. Tuy nhiên, vì nó phụ thuộc rất nhiều vào CUDA để tăng tốc GPU, việc có môi trường đúng là cực kỳ quan trọng. Dưới đây là các bước để cài đặt vLLM trên hệ thống dựa trên Linux với GPU NVIDIA.

### Điều kiện tiên quyết

Trước khi cài đặt vLLM, hãy đảm bảo bạn có:
-   Python 3.8 trở lên.
-   NVIDIA GPU với khả năng tính toán 7.0 trở lên (kiến trúc Volta, Turing, Ampere, Hopper).
-   PyTorch đã cài đặt tương thích với phiên bản CUDA của bạn.

### Hướng dẫn cài đặt từng bước

Đầu tiên, tạo một môi trường ảo để cô lập các phụ thuộc của bạn. Điều này ngăn ngừa xung đột với các dự án khác.

```bash
# Tạo một môi trường ảo mới
python -m venv vllm-env

# Kích hoạt môi trường ảo
source vllm-env/bin/activate
```

Tiếp theo, cài đặt PyTorch. Điều quan trọng là phải khớp phiên bản PyTorch với bộ công cụ CUDA của bạn. Đối với hầu hết người dùng, việc cài đặt PyTorch ổn định mới nhất có hỗ trợ CUDA là đủ.

```bash
# Cài đặt PyTorch với hỗ trợ CUDA
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```

Bây giờ, bạn có thể cài đặt vLLM. Cách dễ nhất là thông qua pip.

```bash
# Cài đặt vLLM
pip install vllm
```

Nếu bạn đang phát triển vLLM hoặc cần các tính năng mới nhất từ nhánh chính, bạn có thể cài đặt nó từ mã nguồn.

```bash
# Sao chép kho lưu trữ
git clone https://github.com/vllm-project/vllm.git
cd vllm

# Cài đặt từ mã nguồn
pip install -e .
```

### Xác minh cài đặt

Sau khi cài đặt, hãy xác minh rằng vLLM nhận diện GPU của bạn.

```python
import torch
from vllm import LLM

# Kiểm tra xem CUDA có sẵn không
print(f"CUDA Available: {torch.cuda.is_available()}")
if torch.cuda.is_available():
    print(f"GPU Name: {torch.cuda.get_device_name(0)}")

# Thử tải một mô hình nhỏ để kiểm tra chức năng
try:
    llm = LLM(model="facebook/opt-125m")
    print("vLLM initialized successfully.")
except Exception as e:
    print(f"Error initializing vLLM: {e}")
```

## Tích hợp với OpenAI API, Hugging Face, LangChain

Một trong những tính năng mạnh mẽ nhất của vLLM là khả năng tương thích với các hệ sinh thái hiện có. Nó cung cấp API tương thích với OpenAI, giúp dễ dàng thay thế điểm cuối độc quyền bằng một phiên bản vLLM tự lưu trữ mà không cần thay đổi mã ứng dụng của bạn.

### API tương thích OpenAI

vLLM expose một API mô phỏng cấu trúc của API OpenAI. Điều này có nghĩa là bạn có thể sử dụng các khách hàng tiêu chuẩn như `openai` hoặc `langchain` để tương tác với máy chủ vLLM cục bộ của mình.

```bash
# Khởi động máy chủ vLLM với chế độ tương thích OpenAI
python -m vllm.entrypoints.openai.api_server \
    --model meta-llama/Llama-2-7b \
    --host 0.0.0.0 \
    --port 8000
```

Khi máy chủ đang chạy, bạn có thể truy vấn nó bằng các yêu cầu HTTP tiêu chuẩn.

```python
import openai

# Cấu hình khách hàng để trỏ đến máy chủ vLLM cục bộ của bạn
client = openai.OpenAI(
    base_url="http://localhost:8000/v1",
    api_key="token-abc123" # Tùy chọn, phụ thuộc vào cài đặt xác thực của bạn
)

# Thực hiện yêu cầu hoàn thành
response = client.chat.completions.create(
    model="meta-llama/Llama-2-7b",
    messages=[
        {"role": "system", "content": "Bạn là một trợ lý hữu ích."},
        {"role": "user", "content": "Giải thích điện toán lượng tử bằng các thuật ngữ đơn giản."}
    ],
    temperature=0.7
)

print(response.choices[0].message.content)
```

### Tích hợp Hugging Face

vLLM hoạt động liền mạch với Hugging Face Transformers. Bạn có thể tải các mô hình trực tiếp từ Hugging Face Hub mà không cần tải xuống thủ công trước.

```python
from vllm import LLM

# Tải mô hình trực tiếp từ Hugging Face Hub
llm = LLM(model="mistralai/Mistral-7B-Instruct-v0.2")

# Tạo văn bản
prompt = "Thủ đô của Pháp là gì?"
output = llm.generate(prompt)
print(output.outputs[0].text)
```

### Tích hợp LangChain

LangChain là một khung làm việc phổ biến để xây dựng các ứng dụng được cung cấp bởi LLM. vLLM tích hợp tốt với LangChain, cho phép bạn sử dụng nó làm backend cho các chuỗi (chains) và agent của bạn.

```python
from langchain.llms import VLLMOpenAI
from langchain.prompts import PromptTemplate
from langchain.chains import LLMChain

# Khởi tạo LLM với wrapper VLLMOpenAI
llm = VLLMOpenAI(
    openai_api_key="EMPTY",
    openai_api_base="http://localhost:8000/v1",
    model_name="meta-llama/Llama-2-7b"
)

# Tạo một mẫu prompt
template = "Bạn là một hải tặc. Hãy trả lời câu hỏi sau: {question}"
prompt = PromptTemplate(template=template, input_variables=["question"])

# Tạo một chuỗi
llm_chain = LLMChain(prompt=prompt, llm=llm)

# Chạy chuỗi
response = llm_chain.run("2 + 2 bằng bao nhiêu?")
print(response)
```

![vLLM Hierarchy](https://raw.githubusercontent.com/vllm-project/vllm/main/docs/assets/design/hierarchy.png)

## Các chỉ số hiệu năng (Benchmarks)

Hiệu năng là chỉ số chính để đánh giá các engine phục vụ LLM. vLLM luôn vượt trội so với các khung làm việc khác về thông lượng và độ trễ. Dưới đây là một số chỉ số hiệu năng đại diện so sánh vLLM với Hugging Face Transformers tiêu chuẩn và các engine phục vụ khác.

### So sánh thông lượng

Thông lượng đo lường số lượng yêu cầu mà một hệ thống có thể xử lý mỗi giây. Thông lượng cao hơn có nghĩa là nhiều người dùng hơn có thể được phục vụ cùng lúc.

| Khung làm việc | Mô hình | Batch Size | Thông lượng (req/s) | Độ trễ (ms) |
| :--- | :--- | :--- | :--- | :--- |
| Hugging Face Transformers | LLaMA-2-7B | 1 | 15.2 | 450 |
| TGI (Text Generation Inference) | LLaMA-2-7B | 8 | 45.6 | 210 |
| **vLLM** | **LLaMA-2-7B** | **8** | **78.3** | **125** |
| vLLM | LLaMA-2-7B | 32 | 142.1 | 98 |

*Lưu ý: Các chỉ số hiệu năng có thể thay đổi tùy thuộc vào cấu hình phần cứng (ví dụ: GPU A100 so với H100).*

### Hiệu quả bộ nhớ

PagedAttention của vLLM giảm đáng kể chi phí bộ nhớ. Điều này cho phép kích thước batch lớn hơn và cửa sổ ngữ cảnh dài hơn so với các phương pháp truyền thống.

```python
import psutil
import os

def get_memory_usage():
    process = psutil.Process(os.getpid())
    mem_info = process.memory_info()
    return mem_info.rss / (1024 ** 2) # Trả về MB

# Đo lường bộ nhớ trước và sau khi tải mô hình
print(f"Initial Memory: {get_memory_usage()} MB")

llm = LLM(model="meta-llama/Llama-2-7b")
print(f"After Model Load: {get_memory_usage()} MB")

# Tạo một số yêu cầu
for _ in range(10):
    llm.generate("Test prompt")

print(f"After Inference: {get_memory_usage()} MB")
```

Các chỉ số hiệu năng này chứng minh rằng vLLM được tối ưu hóa cao cho các môi trường sản xuất nơi các ràng buộc về tài nguyên là mối quan tâm. Bằng cách tối đa hóa việc sử dụng GPU, các tổ chức có thể giảm chi phí liên quan đến các phiên bản điện toán đám mây.

## Sử dụng nâng cao: Triển khai sản xuất

Triển khai vLLM trong sản xuất đòi hỏi sự cân nhắc cẩn thận về khả năng mở rộng, giám sát và bảo mật. Trong khi thử nghiệm cục bộ hữu ích, các ứng dụng thực tế liên quan đến nhiều người dùng đồng thời và tải thay đổi.

### Triển khai Docker

Sử dụng Docker đơn giản hóa quá trình triển khai và đảm bảo tính nhất quán giữa các môi trường khác nhau. Dưới đây là một mẫu Dockerfile cho vLLM.

```dockerfile
FROM nvidia/cuda:12.1-runtime-ubuntu22.04

# Cài đặt Python và các phụ thuộc
RUN apt-get update && apt-get install -y python3 python3-pip
RUN pip3 install vllm torch transformers

# Sao chép mã ứng dụng
COPY app.py /app/app.py

#Expose cổng
EXPOSE 8000

# Chạy máy chủ
CMD ["python3", "/app/app.py"]
```

Và đây là một `app.py` đơn giản để chạy máy chủ bên trong container.

```python
from vllm import LLM
from vllm.engine.arg_utils import AsyncEngineArgs
from vllm.engine.async_llm_engine import AsyncLLMEngine

engine_args = AsyncEngineArgs(
    model="meta-llama/Llama-2-7b",
    tensor_parallel_size=1,
    gpu_memory_utilization=0.9,
    dtype="float16"
)

llm = AsyncLLMEngine.from_engine_args(engine_args)
```

### Mở rộng Kubernetes

Đối với các triển khai quy mô lớn, Kubernetes là nền tảng điều phối được ưa chuộng. Bạn có thể triển khai vLLM dưới dạng dịch vụ và mở rộng nó theo chiều ngang dựa trên lưu lượng truy cập.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vllm-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: vllm
  template:
    metadata:
      labels:
        app: vllm
    spec:
      containers:
      - name: vllm
        image: vllm/vllm:latest
        command: ["python", "-m", "vllm.entrypoints.openai.api_server"]
        args: ["--model", "meta-llama/Llama-2-7b", "--host", "0.0.0.0", "--port", "8000"]
        resources:
          limits:
            nvidia.com/gpu: 1
        ports:
        - containerPort: 8000
```

### Giám sát và Ghi nhật ký

Giám sát là yếu tố thiết yếu để duy trì hiệu năng. Sử dụng các công cụ như Prometheus và Grafana để theo dõi các chỉ số như độ trễ yêu cầu, thông lượng và mức sử dụng GPU.

```python
# Ví dụ về ghi nhật ký thời gian suy luận
import time

start_time = time.time()
outputs = llm.generate("Sample prompt")
end_time = time.time()

latency = end_time - start_time
print(f"Inference took {latency:.4f} seconds")
```

![AnythingLLM Chat with Docs](https://raw.githubusercontent.com/vllm-project/vllm/main/docs/assets/deployment/anything-llm-chat-with-docs.png)

## So sánh với các lựa chọn thay thế

Khi chọn một khung phục vụ LLM, vLLM cạnh tranh với một số tùy chọn phổ biến khác. Hiểu rõ sự khác biệt giúp lựa chọn công cụ phù hợp nhất cho nhu cầu cụ thể của bạn.

| Tính năng | vLLM | TGI (Hugging Face) | Text Generation Inference | Triton Inference Server |
| :--- | :--- | :--- | :--- | :--- |
| **Trọng tâm chính** | Thông lượng cao & Hiệu quả | Dễ sử dụng & Tích hợp HF | Khả năng mở rộng doanh nghiệp | Phục vụ mô hình ML chung |
| **Quản lý bộ nhớ** | PagedAttention | Tĩnh/Động | Bộ phân bổ tùy chỉnh | Bộ phân bổ tùy chỉnh |
| **Hỗ trợ API OpenAI** | Bản địa | Qua Wrapper | Qua Plugin | Yêu cầu mã tùy chỉnh |
| **Hỗ trợ mô hình** | Rộng rãi (Llama, Mistral, v.v.) | Chỉ mô hình HF | Chỉ mô hình HF | Bất kỳ khung nào (PyTorch, TF, ONNX) |
| **Dễ dàng thiết lập** | Dễ | Rất dễ | Trung bình | Phức tạp |
| **Tăng trưởng cộng đồng** | Nhanh chóng | Ổn định | Ổn định | Trưởng thành |

vLLM nổi bật nhờ sự tối ưu hóa chuyên biệt cho LLM, đặc biệt là về hiệu quả bộ nhớ. Trong khi Triton linh hoạt hơn cho các loại mô hình khác nhau, nó thiếu các tối ưu hóa có sẵn cho LLM mà vLLM cung cấp. TGI dễ thiết lập hơn cho người dùng thuần túy Hugging Face nhưng có thể không đạt được thông lượng thô tương đương vLLM trong các kịch bản batch phức tạp.

## Hạn chế

Mặc dù có những điểm mạnh, vLLM không phải là không có hạn chế. Việc nhận thức được những điều này giúp lập kế hoạch chiến lược triển khai của bạn.

1.  **Phụ thuộc GPU**: vLLM được tối ưu hóa mạnh mẽ cho GPU NVIDIA. Mặc dù nó hỗ trợ các bộ tăng tốc khác ở một mức độ nào đó, nhưng bộ tính năng đầy đủ và lợi ích hiệu năng được thực hiện tốt nhất trên phần cứng NVIDIA.
2.  **Phức tạp trong các kịch bản đa GPU**: Việc thiết lập các cụm đa nút hoặc đa GPU đòi hỏi cấu hình cẩn thận về song song tensor và song song đường ống, điều này có thể gây khó khăn cho người mới bắt đầu.
3.  **Hỗ trợ hạn chế cho các mô hình không phải Transformer**: vLLM được thiết kế chủ yếu cho các kiến trúc dựa trên Transformer. Các loại mô hình khác có thể không hưởng lợi từ các tối ưu hóa của nó.
4.  **Tốn nhiều tài nguyên cho các mô hình nhỏ**: Đối với các mô hình rất nhỏ, chi phí thiết lập vLLM có thể không xứng đáng so với các khung làm việc đơn giản hơn.

## Câu hỏi thường gặp (FAQ)

### Q1: vLLM có hỗ trợ lượng tử hóa (quantization) không?
Có, vLLM hỗ trợ nhiều kỹ thuật lượng tử hóa, bao gồm FP16, BF16 và INT8. Lượng tử hóa có thể进一步 giảm việc sử dụng bộ nhớ và tăng thông lượng, khiến nó phù hợp cho các môi trường hạn chế về tài nguyên.

### Q2: Tôi có thể sử dụng vLLM với GPU không phải NVIDIA không?
Hiện tại, vLLM được tối ưu hóa cho GPU NVIDIA sử dụng CUDA. Hỗ trợ cho các bộ tăng tốc khác như AMD ROCm là thử nghiệm và có thể không mang lại mức độ hiệu năng hoặc ổn định tương tự.

### Q3: vLLM xử lý các phản hồi luồng (streaming responses) như thế nào?
vLLM hỗ trợ phản hồi luồng, điều này hữu ích cho các ứng dụng yêu cầu đầu ra theo thời gian thực, chẳng hạn như giao diện trò chuyện. Bạn có thể kích hoạt luồng bằng cách đặt tham số `stream` thành true trong yêu cầu API.

### Q4: vLLM có phù hợp cho việc sử dụng trong sản xuất không?
Chắc chắn rồi. vLLM được sử dụng rộng rãi trong các môi trường sản xuất bởi các công ty từ startup đến doanh nghiệp lớn. Tính bền vững, hiệu năng và hỗ trợ cộng đồng tích cực của nó khiến nó trở thành một lựa chọn đáng tin cậy để phục vụ LLM ở quy mô lớn.

### Q5: Làm thế nào để tôi giám sát hiệu năng của vLLM?
vLLM cung cấp các chỉ số tích hợp thông qua tích hợp Prometheus và Grafana. Sử dụng điểm cuối `/metrics` để expose dữ liệu hiệu năng cho việc giám sát. Bạn cũng có thể sử dụng cờ `--enable.metrics` khi khởi động máy chủ.

### Q6: Độ dài chuỗi tối đa được vLLM hỗ trợ là bao nhiêu?
vLLM hỗ trợ độ dài chuỗi lên đến 32.768 token cho các mô hình hỗ trợ ngữ cảnh dài. Tuy nhiên, các giới hạn thực tế phụ thuộc vào bộ nhớ GPU khả dụng và kích thước batch.

### Q7: vLLM xử lý các yêu cầu đồng thời như thế nào?
vLLM sử dụng kỹ thuật continuous batching (batching liên tục) để xử lý các yêu cầu đồng thời một cách hiệu quả. Nhiều yêu cầu có thể được xử lý cùng lúc mà không cần chờ đợi hoàn thành, cải thiện đáng kể thông lượng.