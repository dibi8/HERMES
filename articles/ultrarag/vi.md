---
title: 'UltraRAG: Khung MCP Low-Code cho Pipeline RAG Phức Tạp — Hướng dẫn 2026'
description: 'UltraRAG của OpenBMB là một khung MCP low-code để xây dựng pipeline RAG phức tạp. Điều phối bằng YAML, trình soạn thảo UI, demo Deep Research và hướng dẫn triển khai production.'
date: 2026-06-22
slug: 'ultrarag-low-code-mcp-rag-framework-innovative-pipelines-2026'
category: 'advanced-rag'
tags: ['UltraRAG', 'RAG', 'MCP', 'OpenBMB', 'low-code', 'pipeline', 'retrieval-augmented-generation', 'LLM']
github_repo: 'https://github.com/OpenBMB/UltraRAG'
stars: 5603
maintainer: 'OpenBMB'
license: 'MIT'
featureImage: 'https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/architecture.png'
lang: vi
---

![Kiến trúc UltraRAG](https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/architecture.png)
*Kiến trúc UltraRAG — trực quan hóa điều phối MCP server qua dibi8.com*

## Giới thiệu

Xây dựng một pipeline retrieval-augmented generation (RAG) xử lý phân nhánh điều kiện, làm sâu lặp và suy luận đa bước thường đòi hỏi viết hàng trăm dòng mã Python ghép nối. Mỗi điểm phân nhánh cần xử lý lỗi, quản lý trạng thái và chuyển biến cẩn thận giữa các thành phần. Độ phức tạp tăng theo cấp số nhân khi pipeline ngày càng tinh vi.

UltraRAG, được phát triển bởi OpenBMB hợp tác với THUNLP tại Đại học Thanh Hoa và NEUIR tại Đại học Đông Bắc, tiếp cận theo hướng khác. Thay vì yêu cầu nhà phát triển tự nối mọi thứ theo cách mệnh lệnh, UltraRAG chuẩn hóa các thành phần RAG thành các MCP server độc lập và điều phối chúng thông qua các tệp cấu hình YAML khai báo. Kết quả là một khung làm việc nơi các quy trình phức tạp — bao gồm vòng lặp, phân nhánh điều kiện và chuỗi suy luận đa giai đoạn — có thể được biểu diễn trong vài chục dòng YAML thay vì hàng trăm dòng Python.

Với **5.603 sao GitHub** theo giấy phép MIT, UltraRAG đã thu hút sự chú ý từ cả nhà nghiên cứu và thực hành cần原型 nhanh các kiến trúc RAG hoặc triển khai pipeline production mà không cần boilerplate. Hướng dẫn này bao gồm UltraRAG là gì, kiến trúc dựa trên MCP hoạt động ra sao, cách cài đặt và cấu hình, tích hợp với các công cụ phổ biến, đánh giá hiệu suất và hiểu các đánh đổi của nó.

## UltraRAG là gì?

UltraRAG là một **khung phát triển RAG low-code** được xây dựng trên kiến trúc Model Context Protocol. Nó được phát hành lần đầu vào tháng 1 năm 2025 và đã trải qua nhiều phiên bản chính, với UltraRAG 3.0 ra mắt tháng 1 năm 2026 giới thiệu thiết kế "không hộp đen" nơi mỗi dòng logic suy luận đều được hiển thị rõ ràng trong định nghĩa pipeline.

Ý tưởng cốt lõi rất đơn giản: phân rã mọi nguyên thủy RAG — retrieval, generation, reranking, prompting, routing, quản lý bộ nhớ — thành một MCP server độc lập. Sau đó, kết hợp các server này thành workflow bằng các định nghĩa pipeline YAML hỗ trợ thực thi tuần tự, vòng lặp và phân nhánh điều kiện.

Các thông tin chính về UltraRAG:

- **Kho lưu trữ**: [OpenBMB/UltraRAG](https://github.com/OpenBMB/UltraRAG) trên GitHub
- **Stars**: **5.603** (tính đến tháng 6 năm 2026)
- **Giấy phép**: MIT
- **Người bảo trì**: OpenBMB
- **Ngôn ngữ**: Python (yêu cầu Python 3.11–3.12)
- **Phụ thuộc MCP**: `fastmcp>=3.3.1`
- **Phiên bản hiện tại**: 0.3.0.2
- **Phát hành đầu tiên**: Tháng 1 năm 2025
- **Người đóng góp**: THUNLP, NEUIR, OpenBMB, AI9stars
- **Cộng đồng**: Discord, WeChat, Feishu groups

Khác với các framework coi LLM như một bước đơn lẻ duy nhất, UltraRAG mô hình hóa mỗi thao tác thành một server có thể gọi là tool riêng biệt. Server retriever expose các phương thức như `retriever_init`, `retriever_search` và `retriever_websearch`. Server generation cung cấp `generation_init`, `generate`, `multimodal_generate` và `multiturn_generate`. Server router cho phép phân nhánh điều kiện dựa trên phân loại do LLM hỗ trợ.

![Sơ đồ kiến trúc UltraRAG](https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/ultrarag.svg)
*Tổng quan kiến trúc UltraRAG dựa trên MCP qua dibi8.com*

![Giao diện UltraRAG UI](https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/chat_menu.png)
*UltraRAG UI — trình soạn thảo pipeline trực quan và IDE qua dibi8.com*

![Lịch sử sao UltraRAG](https://api.star-history.com/svg?repos=OpenBMB/UltraRAG&type=Date)
*Biểu đồ tăng trưởng sao UltraRAG trên GitHub qua dibi8.com*

UltraRAG cũng đi kèm **UltraRAG UI**, một môi trường phát triển tích hợp trực quan cho RAG. UI bao gồm Pipeline Builder đồng bộ hai chiều giữa trình soạn thảo trực quan dựa trên canvas và trình soạn thảo mã. Bạn có thể điều chỉnh tham số pipeline, sửa đổi prompt và xây dựng workflow cơ sở tri thức trực quan, sau đó triển khai pipeline kết quả đến giao diện web chỉ với một lệnh.

## UltraRAG hoạt động như thế nào

Kiến trúc của UltraRAG dựa trên hai khái niệm: **MCP Servers** và **MCP Client Pipelines**.

### MCP Servers như thành phần nguyên tử

Mỗi khả năng RAG được triển khai dưới dạng một MCP server. Một MCP server đăng ký các tool (hàm) có thể được gọi bởi client. Trong UltraRAG, các server nằm trong thư mục `servers/` và tuân theo cấu trúc nhất quán:

```python
# servers/retriever/src/retriever.py (đã đơn giản hóa)
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

Mỗi đăng ký `tool()` khiến phương thức có thể được gọi từ pipeline YAML. Server quản lý trạng thái, phụ thuộc và phân bổ tài nguyên của riêng mình một cách độc lập.

### MCP Client Pipelines như lớp điều phối

Một tệp pipeline YAML định nghĩa server nào cần tải và theo thứ tự nào để gọi các tool của chúng. Đây là ví dụ tối thiểu:

```yaml
# examples/experiments/sayhello.yaml
servers:
  sayhello: servers/sayhello

pipeline:
- sayhello.greet
```

Và một pipeline RAG thực tế hơn:

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

Pipeline thực thi từ trên xuống dưới. Mỗi bước tham chiếu đến một server và một tool. Biến output từ bước này có thể được truyền làm input cho các bước tiếp theo bằng cách sử dụng các directive `output:` và `input:`.

### Phân nhánh điều kiện và vòng lặp

UltraRAG hỗ trợ kiểm soát luồng ngay trong YAML. Đây là một đoạn từ demo LightResearch sử dụng phân nhánh điều kiện với router:

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

Directive `branch` đánh giá output của router và điều hướng đến nhánh `incomplete` hoặc `complete`. Directive `loop` lặp lại các bước được bao bọc một số lần cố định. Điều này cho phép các mẫu làm sâu lặp thường gặp trong hệ thống RAG hướng nghiên cứu.

### Truyền biến giữa các bước

Các bước giao tiếp thông qua biến output có tên. Một bước khai báo output của nó:

```yaml
- retriever.retriever_search:
    input:
      q_ls: instruction_ls
    output:
      ret_psg: retrieved_passages
```

Sau đó, một bước downstream tiêu thụ các output đó:

```yaml
- prompt.qa_rag_boxed:
    input:
      passages: retrieved_passages
```

Khai thác dữ liệu tường minh này giúp pipeline dễ debug và suy luận hơn so với state toàn cục ẩn.

## Cài đặt & Thiết lập

UltraRAG hỗ trợ hai phương pháp cài đặt: cài đặt từ source code với `uv` (được khuyến nghị) và triển khai Docker.

### Phương pháp 1: Cài đặt từ source code với uv

Cài đặt `uv` nếu bạn chưa có:

```shell
pip install uv
```

Clone repository và đi vào thư mục dự án:

```shell
git clone https://github.com/OpenBMB/UltraRAG.git --depth 1
cd UltraRAG
```

Cài đặt phụ thuộc. Bạn có thể cài đặt chọn lọc theo nhu cầu:

```shell
# Chỉ core (UltraRAG UI)
uv sync

# Cài đặt đầy đủ (retrieval, generation, evaluation, xử lý corpus)
uv sync --all-extras

# Chỉ module retrieval
uv sync --extra retriever

# Chỉ module generation
uv sync --extra generation
```

Kích hoạt virtual environment:

```shell
source .venv/bin/activate
```

### Phương pháp 2: Cài đặt vào môi trường hiện có

Nếu bạn đã có sẵn môi trường Python 3.11 hoặc 3.12:

```shell
# Phụ thuộc core
uv pip install -e .

# Cài đặt đầy đủ
uv pip install -e ".[all]"

# Extras chọn lọc
uv pip install -e ".[retriever]"
uv pip install -e ".[generation]"
uv pip install -e ".[evaluation]"
```

### Phương pháp 3: Triển khai Docker

Kéo image có sẵn:

```shell
docker pull hdxin2002/ultrarag:v0.3.0-base-cpu   # Phiên bản chỉ CPU
docker pull hdxin2002/ultrarag:v0.3.0-base-gpu   # Phiên bản GPU base
docker pull hdxin2002/ultrarag:v0.3.0             # Phiên bản đầy đủ (GPU)
```

Hoặc build cục bộ:

```shell
git clone https://github.com/OpenBMB/UltraRAG.git --depth 1
cd UltraRAG
docker build -t ultrarag:v0.3.0 .
```

Khởi động container:

```shell
docker run -it --gpus all -p 5050:5050 hdxin2002/ultrarag:v0.3.0
```

UltraRAG UI sẽ tự động khởi động. Truy cập tại `http://localhost:5050`.

### Xác nhận cài đặt

Chạy demo đi kèm để xác nhận mọi thứ hoạt động:

```shell
ultrarag run examples/experiments/sayhello.yaml
```

Output mong đợi:

```
Hello, UltraRAG v3!
```

### Cấu hình với .env

UltraRAG đọc API key và model endpoint từ tệp `.env`:

```shell
# .env.example
OPENAI_API_KEY=your_openai_key_here
OPENAI_API_BASE=https://api.openai.com/v1
VLLM_MODEL_PATH=/path/to/local/model
MILVUS_URI=http://localhost:19530
```

Sao chép và tùy chỉnh:

```shell
cp .env.dev .env
```

## Tích hợp với các công cụ phổ biến

UltraRAG tích hợp với nhiều công cụ được sử dụng rộng rãi trong hệ sinh thái RAG. Dưới đây là cách nó kết nối với từng công cụ.

### Cơ sở dữ liệu vector Milvus

Milvus là vector store mặc định của UltraRAG. Khởi tạo và index corpus của bạn:

```yaml
# milvus_index.yaml
servers:
  retriever: servers/retriever

pipeline:
- retriever.retriever_init
- retriever.retriever_embed
- retriever.retriever_index
```

Cấu hình kết nối Milvus trong tham số server:

```yaml
# servers/retriever/parameter.yaml
collection_name: my_knowledge_base
backend_configs:
  milvus:
    uri: http://localhost:19530
    collection_name: my_knowledge_base
```

### vLLM cho Generation cục bộ

UltraRAG hỗ trợ vLLM cho inference cục bộ thông lượng cao:

```yaml
# servers/generation/parameter.yaml
generation:
  backend: vllm
  backend_configs:
    model_name_or_path: /path/to/Qwen2.5-7B-Instruct
    tensor_parallel_size: 1
    gpu_memory_utilization: 0.9
```

Khởi tạo và generate:

```yaml
pipeline:
- generation.generation_init
- generation.generate
```

### API tương thích OpenAI

Cho generation dựa trên đám mây, UltraRAG hoạt động với mọi endpoint tương thích OpenAI:

```yaml
generation:
  backend: openai
  backend_configs:
    model_name_or_path: gpt-4o
    api_base: https://api.openai.com/v1
    api_key: ${OPENAI_API_KEY}
```

### Sentence Transformers cho Embedding

UltraRAG hỗ trợ sentence-transformers cho tính toán embedding:

```yaml
# Cài đặt extra
uv sync --extra retriever

# Cấu hình trong parameter.yaml
retriever:
  backend_configs:
    embedding_model: sentence-transformers/all-MiniLM-L6-v2
```

### Web UI dựa trên Flask

UltraRAG UI được xây dựng trên Flask và cung cấp một RAG IDE đầy đủ:

```shell
# Khởi động UltraRAG UI
python ui/app.py
```

UI chạy trên cổng 5050 mặc định và cung cấp:
- Trình soạn thảo pipeline trực quan với canvas kéo-thả
- Đồng bộ mã thời gian thực
- Quản lý cơ sở tri thức
- Triển khai một cú nháy chuột đến demo web tương tác

## Benchmark / Trường hợp thực tế

UltraRAG đi kèm một khung đánh giá tích hợp hỗ trợ các benchmark tiêu chuẩn hóa. Server `benchmark` tải các tập dữ liệu đánh giá và theo dõi metrics qua các lần chạy pipeline.

### Chạy Benchmark

```yaml
# Chạy workflow benchmark
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

### Ví dụ Pipeline Deep Research

Demo nổi bật của UltraRAG là một pipeline Deep Research được vận hành bởi mô hình AgentCPM-Report. Pipeline này thực hiện retrieval và tổng hợp đa bước để tạo báo cáo khảo sát toàn diện:

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

Pipeline lặp lại tối đa 140 lần, quyết định động xem có nên tìm kiếm, mở rộng chủ đề, tóm tắt kết quả hoặc kết thúc dựa trên đánh giá của router. Đây là loại suy luận đa bước mà sẽ đòi hỏi các state machine Python phức tạp trong các framework truyền thống.

### Pipeline xử lý Corpus

UltraRAG bao gồm các server chuyên dụng cho việc ingest và chunking corpus:

```yaml
# examples/demos/build_text_corpus.yaml
servers:
  corpus: servers/corpus

pipeline:
- corpus.build_corpus
- corpus.chunk
```

Server corpus hỗ trợ nhiều định dạng tài liệu (PDF, DOCX, text) và tích hợp với Chonkie cho các chiến lược chunking thông minh.

## Sử dụng nâng cao / Hardening production

Triển khai UltraRAG trong production đòi hỏi chú ý đến nhiều khía cạnh ngoài thiết lập cơ bản.

### Triển khai Production với Milvus và LLM Backend

Hướng dẫn triển khai của UltraRAG bao gồm thiết lập toàn bộ stack production:

```shell
# 1. Khởi động Milvus standalone
docker run -d \
  --name milvus-standalone \
  --security-opt seccomp:unconfined \
  -p 19530:19530 \
  -p 9091:9091 \
  milvusdb/milvus:v2.4.0 \
  milvus run standalone

# 2. Đặt biến môi trường
export MILVUS_URI=http://localhost:19530
export OPENAI_API_KEY=sk-xxx
export OPENAI_API_BASE=https://api.openai.com/v1

# 3. Khởi động UltraRAG UI
python ui/app.py
```

### Phát triển Server tùy chỉnh

Bạn có thể thêm MCP server riêng bằng cách tạo thư mục mới dưới `servers/`:

```
servers/
  my_custom_server/
    __init__.py
    parameter.yaml
    src/
      custom_tool.py
```

Server tuân theo cùng mẫu:

```python
# servers/my_custom_server/src/custom_tool.py
from ultrarag.server import UltraRAG_MCP_Server

app = UltraRAG_MCP_Server("my_custom_server")

class CustomTools:
    def __init__(self, mcp_inst: UltraRAG_MCP_Server):
        mcp_inst.tool(self.my_custom_method)

    async def my_custom_method(self, input_text: str) -> str:
        # Logic tùy chỉnh của bạn ở đây
        return f"Processed: {input_text}"
```

Đăng ký trong pipeline:

```yaml
servers:
  my_custom_server: servers/my_custom_server

pipeline:
- my_custom_server.my_custom_method
```

### Thao tác có trạng thái qua các vòng lặp

Đối với pipeline cần duy trì trạng thái qua các lần lặp, UltraRAG cung cấp biến output có trạng thái:

```yaml
- custom.assign_citation_ids_stateful:
    input:
      ret_psg: psg_ls
    output:
      ret_psg: psg_ls
```

Hậu tố `_stateful` cho biết output được tích lũy qua các lần lặp vòng lặp thay vì ghi đè.

### Debug Pipeline

UltraRAG cung cấp hướng dẫn debug có cấu trúc bao gồm bốn lớp:

1. **Input & Retrieval** — Xác nhận chất lượng phân tích tài liệu và embedding
2. **Reasoning & Planning** — Kiểm tra quyết định router và logic phân nhánh
3. **State & Context** — Kiểm tra truyền biến giữa các bước pipeline
4. **Deployment & Runtime** — Giám sát sức khỏe server và mức sử dụng tài nguyên

Sử dụng giao diện Case Study trong UltraRAG UI để trực quan hóa output trung gian tại mỗi bước pipeline.

### Tinh chỉnh hiệu năng

Các tham số cấu hình chính cho production:

```yaml
# Ví dụ tinh chỉnh tham số
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

## So sánh với các lựa chọn thay thế

UltraRAG chiếm một vị trí riêng biệt trong cảnh quan framework RAG. Dưới đây là cách nó so sánh với ba lựa chọn thay thế chính.

| Tính năng | UltraRAG | Haystack | LangChain | LlamaIndex |
|-----------|----------|----------|-----------|------------|
| **Paradigm chính** | YAML + MCP server | Component pipeline | Chain/graph | Indexing hướng dữ liệu |
| **Điều phối low-code** | Có (pipeline YAML) | Một phần (Pipeline class) | Không (Python code) | Không (Python code) |
| **Phân nhánh điều kiện** | Native (YAML `branch`) | Hạn chế | Cần code tùy chỉnh | Cần code tùy chỉnh |
| **Hỗ trợ vòng lặp** | Native (YAML `loop`) | Không hỗ trợ | Cần code tùy chỉnh | Cần code tùy chỉnh |
| **Trình soạn pipeline trực quan** | Có (UltraRAG UI) | Không | Không | Không |
| **Evaluation tích hợp** | Có (benchmark server) | Có (EvaluationPipeline) | Qua LangSmith | Qua GPTEvaluator |
| **Hỗ trợ Vector DB** | Milvus, FAISS, web search | 10+ (Elasticsearch, Pinecone, v.v.) | 15+ | 15+ |
| **LLM backend** | OpenAI, vLLM, tùy chỉnh | OpenAI, Ollama, vLLM, tùy chỉnh | OpenAI, Anthropic, tùy chỉnh | OpenAI, Anthropic, tùy chỉnh |
| **Hỗ trợ đa phương tiện** | Có (vision retriever) | Hạn chế | Qua tích hợp | Qua tích hợp |
| **Giấy phép** | MIT | Apache-2.0 | MIT | MIT |
| **Phiên bản Python** | 3.11–3.12 | 3.9+ | 3.9+ | 3.9+ |
| **Kiến trúc MCP** | Có | Không | Không | Không |

Phương pháp YAML-first với kiểm soát luồng native là điểm khác biệt chính của UltraRAG. Trong khi LangChain và LlamaIndex yêu cầu mã Python mệnh lệnh cho phân nhánh và vòng lặp, UltraRAG biểu diễn các mẫu này một cách khai báo. Haystack gần gũi hơn về mặt tinh thần nhưng thiếu phân rã MCP và trình soạn pipeline trực quan.

Đối với nhà phát triển tìm kiếm **lựa chọn thay thế UltraRAG** nhấn mạnh điều phối trực quan, UltraRAG UI là tùy chọn gần nhất trong không gian mã nguồn mở. Đối với những người ưu tiên khả năng tương thích cơ sở dữ liệu vector rộng, Haystack hoặc LlamaIndex có thể cung cấp nhiều connector hơn sẵn có.

## Hạn chế / Đánh giá trung thực

Không có framework nào hoàn hảo. Dưới đây là một số hạn chế cần cân nhắc khi đánh giá UltraRAG cho dự án của bạn.

### Độ trưởng thành hệ sinh thái

UltraRAG ra mắt tháng 1 năm 2025, khiến nó tương đối trẻ so với Haystack (2022) hoặc LangChain (2023). Thư mục server dưới `servers/` bao gồm các nguyên thủy RAG phổ biến nhưng chưa có nhiều tool chuyên biệt như các framework trưởng thành. Nếu bạn cần tích hợp hiếm (ví dụ: cụ thể các engine tìm kiếm doanh nghiệp), bạn có thể cần tự viết server.

### Phụ thuộc vào MCP và fastmcp

UltraRAG phụ thuộc vào Model Context Protocol và thư viện `fastmcp`. Trong khi MCP đang được áp dụng rộng rãi, nó vẫn là một tiêu chuẩn đang phát triển. Các thay đổi trong đặc tả MCP có thể yêu cầu cập nhật các server UltraRAG. Nếu nhóm của bạn không thoải mái với rủi ro phụ thuộc này, một framework đã được thiết lập vững chắc hơn có thể là lựa chọn phù hợp.

### Yêu cầu GPU cho tính năng đầy đủ

Chạy UltraRAG với generation cục bộ (vLLM) và các mô hình embedding đòi hỏi bộ nhớ GPU đáng kể. Các image Docker lớn và cài đặt đầy đủ với tất cả extras kéo theo PyTorch, transformers và vLLM. Nếu bạn đang triển khai trên hạ tầng chỉ CPU, bạn bị giới hạn ở các backend cloud API cho generation.

### Ngôn ngữ tài liệu

Mặc dù tài liệu tiếng Anh tồn tại tại [ultrarag.openbmb.cn](https://ultrarag.openbmb.cn), cộng đồng phát triển chính giao tiếp qua WeChat, Feishu và Discord. Tài nguyên và bài viết blog tiếng Trung nhiều hơn. Nhà phát triển không nói tiếng Trung đôi khi có thể gặp khoảng trống trong tài liệu dịch.

### Phạm vi Evaluation

Khung đánh giá tích hợp hỗ trợ các metrics phổ biến (precision, recall, F1, contextual precision/recall) nhưng chưa bao gồm phạm vi benchmark cụ thể tình huống mà các nền tảng evaluation chuyên dụng cung cấp. Đối với pipeline evaluation cấp production, bạn có thể muốn bổ sung benchmark server của UltraRAG với các công cụ như RAGAS hoặc DeepEval.

## Câu hỏi thường gặp (FAQ)

### Làm thế nào để cài đặt UltraRAG trên máy chỉ có CPU?

Sử dụng image Docker CPU hoặc cài đặt với ít extras nhất:

```shell
git clone https://github.com/OpenBMB/UltraRAG.git --depth 1
cd UltraRAG
uv sync --extra retriever
# Sử dụng API tương thích OpenAI cho generation thay vì vLLM
```

Extra `retriever` bao gồm FAISS cho tìm kiếm vector cục bộ mà không yêu cầu GPU.

### UltraRAG hỗ trợ những phiên bản Python nào?

UltraRAG yêu cầu Python 3.11 hoặc 3.12. Nó không hỗ trợ Python 3.10 trở xuống do yêu cầu phụ thuộc từ `fastmcp` và `vllm`. Sử dụng `uv` để quản lý virtual environment tự động:

```shell
uv python install 3.12
uv sync
```

### UltraRAG có hoạt động với cơ sở dữ liệu vector không phải Milvus không?

Có. Server retriever hỗ trợ nhiều backend được cấu hình thông qua `parameter.yaml`. FAISS có sẵn qua extra `retriever`, và bạn có thể thêm các backend index tùy chỉnh bằng cách mở rộng module `index_backends`:

```yaml
backend_configs:
  faiss:
    index_type: IVF_FLAT
    nlist: 128
```

### UltraRAG so sánh với Haystack cho RAG production như thế nào?

Haystack xuất sắc về phạm vi connector rộng và đã được thử nghiệm trong production nhiều năm. Ưu điểm của UltraRAG nằm ở điều phối dựa trên YAML với hỗ trợ native cho vòng lặp và phân nhánh điều kiện — các mẫu khó biểu đạt trong component pipeline của Haystack. Đối với pipeline RAG tuyến tính đơn giản, Haystack có thể đơn giản hơn. Đối với workflow lặp hoặc đa đường phức tạp, phương pháp khai báo của UltraRAG giảm đáng kể boilerplate. Xem [hướng dẫn Haystack production](haystack-ai-production-rag-framework-llm-applications-2026) để so sánh chi tiết hơn.

### UltraRAG có phù hợp cho triển khai production không?

UltraRAG 3.0 được thiết kế với production trong tâm trí. Kiến trúc MCP server cho phép mở rộng độc lập các thành phần retrieval, generation và routing. Hỗ trợ Docker đơn giản hóa triển khai containerized. Tuy nhiên, vì framework này trẻ hơn một số lựa chọn thay thế, bạn nên lên kế hoạch kiểm tra bổ sung về độ tin cậy, giám sát và quy trình rollback trước khi triển khai cho các workload quan trọng.

### Làm thế nào để thêm nhà cung cấp LLM tùy chỉnh vào UltraRAG?

Tạo một server generation mới bao bọc nhà cung cấp LLM của bạn. Server tương thích OpenAI hiện có trong `servers/generation/` minh họa mẫu:

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

### Sự khác biệt giữa UltraRAG v2 và v3 là gì?

UltraRAG v2 giới thiệu phân rã server MCP và điều phối pipeline YAML. UltraRAG v3 (phát hành tháng 1 năm 2026) thêm UI trực quan với đồng bộ hóa hai chiều canvas-mã, cải thiện truyền biến có trạng thái qua các lần lặp vòng lặp, công cụ debug nâng cao và khiến logic suy luận hoàn toàn minh bạch — loại bỏ vấn đề "hộp đen" nơi trạng thái pipeline trung gian khó kiểm tra.

### Tôi có thể sử dụng UltraRAG với các mô hình open-source cục bộ không?

Có. UltraRAG hỗ trợ bất kỳ mô hình nào truy cập được qua API tương thích OpenAI hoặc engine inference cục bộ như vLLM và Ollama. Đối với mô hình cục bộ, cấu hình server generation:

```yaml
generation:
  backend: openai
  backend_configs:
    model_name_or_path: ollama/qwen2.5:7b
    api_base: http://localhost:11434/v1
```

### Làm thế nào để đánh giá pipeline UltraRAG của tôi?

Sử dụng server benchmark tích hợp với tập dữ liệu đánh giá:

```shell
# Tải tập dữ liệu benchmark
ultrarag download-data --dataset hotpotqa

# Chạy evaluation
ultrarag run examples/demos/RAG.yaml --eval
```

Server evaluation tính toán các metrics tiêu chuẩn và tạo báo cáo so sánh qua các cấu hình pipeline khác nhau.

## Nguồn & Đọc thêm

- [Tài liệu chính thức UltraRAG](https://ultrarag.openbmb.cn/pages/en/getting_started/introduction)
- [Kho lưu trữ UltraRAG trên GitHub](https://github.com/OpenBMB/UltraRAG)
- [Blog phát hành UltraRAG 3.0](https://github.com/OpenBMB/UltraRAG/blob/page/project/blog/en/ultrarag3_0.md)
- [Đặc tả Model Context Protocol (MCP)](https://modelcontextprotocol.io/docs/getting-started/intro)
- [Tập dữ liệu Benchmark UltraRAG](https://modelscope.cn/datasets/UltraRAG/UltraRAG_Benchmark)
- [Bài báo VisRAG (ICLR 2025)](https://arxiv.org/abs/2410.10594)
- [Bài báo RAG-DDR (ICLR 2025)](https://arxiv.org/abs/2410.13509)
- [Mô hình AgentCPM-Report (Hugging Face)](https://huggingface.co/openbmb/AgentCPM-Report)
- [Cách cài đặt UltraRAG — Hướng dẫn đầy đủ](https://ultrarag.openbmb.cn/pages/en/getting_started/quick_start)
- [Hướng dẫn Pipeline Deep Research UltraRAG](https://ultrarag.openbmb.cn/pages/en/demo/deepresearch)
- [Hướng dẫn Triển khai Production UltraRAG](https://ultrarag.openbmb.cn/pages/en/ui/prepare)
- [Liên quan: [Hướng dẫn RAG Production Haystack]](haystack-ai-production-rag-framework-llm-applications-2026)
- [Liên quan: [Hướng dẫn Triển khai Kiến trúc RAG]](rag-architecture-implementation-guide)
- [Liên quan: [Thiết lập Production Server MCP Context7]](context7-mcp-server-production-setup-guide)

## Kết luận: Triển khai Pipeline UltraRAG đầu tiên của bạn ngay hôm nay

UltraRAG lấp đầy một khoảng trống thực sự trong hệ sinh thái RAG: **điều phối khai báo cho workflow retrieval phức tạp**. Nếu bạn đã chán viết mã Python mệnh lệnh để xử lý phân nhánh điều kiện, làm sâu lặp và suy luận đa bước trong pipeline RAG của mình, kiến trúc MCP YAML-first của UltraRAG cung cấp một lựa chọn sạch hơn.

Điểm mạnh của framework — định nghĩa pipeline low-code, trình soạn thảo UI trực quan, evaluation tích hợp và hỗ trợ vòng lặp/phân nhánh native — khiến nó đặc biệt có giá trị cho nhà nghiên cứu原型 các kiến trúc RAG mới và đội xây dựng hệ thống retrieval lặp như agent Deep Research.

Để bắt đầu, clone repository, cài đặt với `uv` và chạy demo đi kèm. Đường cong học tập nông nếu bạn thoải mái với YAML, và UI trực quan còn loại bỏ rào cản đó.

**Cần hạ tầng đám mây cho deployment UltraRAG của bạn?** [DigitalOcean](https://m.do.co/c/eca87ac14ee0) cung cấp GPU droplet giá cả phải chăng và cơ sở dữ liệu quản lý hoạt động tốt với backend Milvus và vLLM của UltraRAG.

*Một số liên kết bên trên là liên kết tiếp thị. Nếu bạn đăng ký qua các liên kết này, dibi8.com có thể nhận hoa hồng mà bạn không tốn thêm chi phí.*

Tham gia cộng đồng dibi8.com trên Telegram để thảo luận liên tục về framework RAG, kiến trúc MCP và chiến lược triển khai production: [t.me/DIBI8_Group/2](https://t.me/DIBI8_Group/2)

Bạn có câu hỏi về UltraRAG hoặc muốn chia sẻ thiết kế pipeline của mình? Gửi tin nhắn trong nhóm Telegram của chúng tôi và kết nối với các nhà phát triển khác đang xây dựng với UltraRAG.
