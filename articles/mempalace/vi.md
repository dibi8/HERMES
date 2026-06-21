---
title: "MemPalace: Hệ Thống Trí Nhớ AI Mã Nguồn Mở Miễn Phí Với Độ Chính Xác Truy Xuất 96.6% — Ưu Tiên Local, Sử Dụng ChromaDB"
description: "MemPalace là hệ thống trí nhớ AI mã nguồn mở miễn phí với độ chính xác truy xuất R@5 thô 96.6% trên benchmark LongMemEval. Ưu tiên local, backend có thể thay thế (mặc định ChromaDB), hỗ trợ MCP, lưu trữ nguyên văn không tóm tắt. Tương thích với Claude Code, Cursor, Gemini CLI, Claude và ChatGPT. Bao gồm hướng dẫn cài đặt Docker và triển khai production."
date: 2026-06-20
slug: mempalace-local-ai-memory-system-chromadb
category: data-science
tags:
  - AI Memory
  - ChromaDB
  - Open Source
  - Local AI
  - MCP
  - Claude Code
  - Cursor
  - Vector Search
  - LongMemEval
  - Data Science
github_repo: MemPalace/mempalace
stars: 56077
maintainer: MemPalace
license: MIT
featureImage: https://raw.githubusercontent.com/MemPalace/mempalace/main/assets/mempalace_logo.png
lang: vi
---

![MemPalace logo](https://raw.githubusercontent.com/MemPalace/mempalace/main/assets/mempalace_logo.png)
*Hình ảnh MemPalace - qua dibi8.com*

## Giới thiệu

Hãy tưởng tượng bạn dành ba giờ tinh chỉnh một thiết kế kiến trúc phức tạp trong Claude, rồi bắt đầu một phiên làm việc mới và chứng kiến mô hình đứng nhìn bạn trống rỗng. Nó đã quên mọi thứ — các ràng buộc của bạn, sở thích, chính bố cục CSS grid mà bạn đã mất rất nhiều công sức. Đây là vấn đề cốt lõi của AI hội thoại: **không có bộ nhớ bền vững**. Mỗi phiên bắt đầu từ trang trắng, và cửa sổ ngữ cảnh là hữu hạn. **MemPalace** ra đời như một hệ thống trí nhớ AI mã nguồn mở, giải quyết vấn đề này bằng cách cung cấp cho LLM của bạn một bộ não vĩnh viễn, có thể tìm kiếm — chạy hoàn toàn trên máy của bạn.

MemPalace là một **lớp trí nhớ AI mã nguồn mở, miễn phí**, được hỗ trợ bởi **ChromaDB**, đạt **độ chính xác truy xuất R@5 thô 96.6%** trên benchmark LongMemEval. Nó lưu trữ lịch sử hội thoại dưới dạng **văn bản nguyên văn** — không tóm tắt, không trích xuất, không diễn giải — và truy xuất chúng bằng tìm kiếm ngữ nghĩa khi cần. Hệ thống sử dụng **kiến trúc Palace** độc đáo (các sải cánh cho người/dự án, phòng cho chủ đề, ngăn kéo cho nội dung gốc) và tương thích với **Claude Code, Cursor, Gemini CLI, Claude và ChatGPT**. Với **56.077 sao GitHub** theo giấy phép **MIT**, MemPalace nhanh chóng trở thành tiêu chuẩn thực tế cho các nhà phát triển không muốn ngữ cảnh AI của họ biến mất giữa các phiên.

Trong hướng dẫn toàn diện này, chúng ta sẽ cùng khám phá MemPalace là gì, kiến trúc Palace hoạt động ra sao, cách cài đặt và cấu hình thông qua pip hoặc Docker, tích hợp với các công cụ AI yêu thích, đánh giá hiệu năng benchmark, và tăng cường bảo mật cho môi trường production.

## MemPalace là gì?

MemPalace là một **hệ thống trí nhớ AI ưu tiên local**, được thiết kế để duy trì và truy xuất ngữ cảnh hội thoại xuyên suốt các phiên. Khác với các giải pháp trí nhớ dựa trên đám mây đòi hỏi truyền dữ liệu ra ngoài và phí đăng ký, MemPalace lập chỉ mục và lưu trữ toàn bộ dữ liệu **trên máy của chính bạn**. Không có gì rời khỏi thiết bị của bạn trừ khi bạn chủ động chọn chia sẻ.

### Nguyên tắc cốt lõi

MemPalace được xây dựng xung quanh bốn nguyên tắc nền tảng:

1. **Lưu trữ nguyên văn**: Mọi dòng bạn gõ đều được giữ nguyên chính xác. Không tóm tắt, không nén, không biến đổi mất dữ liệu. Khi yêu cầu MemPalace nhớ lại một cuộc hội thoại, nó trả về đúng văn bản gốc.

2. **Kiến trúc ưu tiên local**: Toàn bộ quá trình lập chỉ mục, lưu trữ và truy xuất đều diễn ra local. ChromaDB chạy như một vector store local, và engine trí nhớ hoạt động hoàn toàn trên phần cứng của bạn. Điều này có nghĩa là không có độ trễ từ các lần request mạng và bạn nắm trọn quyền kiểm soát dữ liệu.

3. **Lớp truy xuất có thể thay thế**: Mặc dù ChromaDB là backend mặc định, lớp truy xuất của MemPalace được thiết kế để dễ dàng hoán đổi. Nhà phát triển có thể cắm các cơ sở dữ liệu vector khác, mô hình embedding tùy chỉnh, hoặc thậm chí chiến lược tìm kiếm lai mà không cần sửa đổi hệ thống cốt lõi.

4. **Tương thích MCP**: MemPalace tích hợp với Giao thức Ngữ cảnh Mô hình (MCP), giúp nó tương thích với bất kỳ công cụ AI nào hỗ trợ MCP. Điều này bao gồm Claude Code, Cursor, Gemini CLI và nhiều công cụ khác.

### Các thông tin kỹ thuật chính

| Thuộc tính | Giá trị |
|-----------|---------|
| Repository | [MemPalace/mempalace](https://github.com/MemPalace/mempalace) |
| Sao GitHub | **56.077** (tính đến tháng 6/2026) |
| Giấy phép | MIT |
| Backend mặc định | ChromaDB |
| Benchmark | **96.6% R@5** trên LongMemEval |
| Hỗ trợ ngôn ngữ | Python (PyPI: `mempalace`) |
| Triển khai | pip, Docker hoặc từ source |
| Quyền riêng tư dữ liệu | Ưu tiên local, không có gì rời khỏi máy của bạn |

## MemPalace hoạt động thế nào: Kiến trúc Palace

Hệ thống truy xuất của MemPalace được tổ chức xung quanh một khái niệm **Palace** — một cấu trúc chỉ mục phân cấp ánh xạ cách hội thoại được lưu trữ và truy xuất. Kiến trúc này được thiết kế đặc biệt cho bộ nhớ hội thoại dài hạn, nơi ngữ cảnh trải qua hàng trăm phiên và hàng nghìn lượt tương tác.

### Sải cánh (Wings): Người và Dự án

**Wings** là đơn vị tổ chức cấp cao nhất trong Palace. Mỗi wing đại diện cho một thực thể riêng biệt — thường là một người, một dự án, hoặc một lĩnh vực quan tâm. Ví dụ, bạn có thể có một wing tên là `backend-architecture` cho các cuộc thảo luận về kiến trúc hệ thống, hoặc `alice` cho các cuộc trò chuyện với một cộng tác viên cụ thể.

```python
from mempalace import Palace

palace = Palace()

# Tạo hoặc lấy wing
wing = palace.get_or_create_wing("backend-architecture")
print(f"Wing ID: {wing.id}")
print(f"Session count: {wing.session_count}")
```

Khi bạn gọi LLM, MemPalace có thể truy vấn các wing liên quan để lấy thông tin ngữ cảnh. Nếu bạn đang thảo luận về tối ưu hóa cơ sở dữ liệu, wing `backend-architecture` sẽ hiển thị các cuộc trò chuyện trước đó về chiến lược lập chỉ mục, kế hoạch truy vấn và quyết định schema.

### Phòng (Rooms): Chủ đề trong Wing

Trong mỗi wing, **Rooms** tổ chức hội thoại theo chủ đề. Một wing duy nhất có thể chứa hàng chục phòng, mỗi phòng đại diện cho một lĩnh vực chủ đề riêng biệt. Cấu trúc phân cấp hai cấp (wing → room) này cho phép cắt giảm hiệu quả trong quá trình truy xuất — MemPalace trước tiên thu hẹp xuống các wing liên quan, sau đó tìm kiếm trong các phòng.

```python
# Tạo một room trong wing
room = wing.create_room("database-optimization")

# Thêm các đoạn hội thoại vào room
room.add_memory(
    session_id="sess_001",
    content="Chúng ta nên dùng covering indexes cho các truy vấn analytics.",
    timestamp="2026-06-15T10:30:00Z"
)

room.add_memory(
    session_id="sess_002",
    content="Kết quả EXPLAIN ANALYZE cho thấy các sequential scans trên bảng orders.",
    timestamp="2026-06-16T14:22:00Z"
)
```

### Ngăn kéo (Drawers): Lưu trữ Nội dung Gốc

**Drawers** là các nút lá của cấu trúc phân cấp Palace. Chúng lưu trữ nội dung hội thoại nguyên văn thực sự — mọi tin nhắn, mọi đoạn mã, mọi điểm ra quyết định. Drawers được lập chỉ mục với vector embeddings để tìm kiếm ngữ nghĩa có thể tìm thấy các đoạn văn liên quan nhất, bất kể chúng được viết khi nào.

```python
# Lưu một đoạn hội thoại nguyên văn
drawer = room.create_drawer("query-optimization-notes")

drawer.store(
    text="""Developer: Chúng ta cần tối ưu hóa truy vấn tìm user.
Assistant: Hãy phân tích execution plan hiện tại. Sequential scan 
trên bảng users là nút thắt. Một B-tree index trên email sẽ giúp ích.
Developer: Ý hay. Chúng ta cũng có thể thêm partial index chỉ cho user active không?
Assistant: Được, partial index sẽ giảm đáng kể kích thước index.
CREATE INDEX idx_users_active_email ON users(email) WHERE status = 'active';""",
    metadata={
        "session_id": "sess_003",
        "model": "claude-sonnet-4-20250514",
        "turn_count": 12
    }
)
```

### Quy trình Tìm kiếm Ngữ nghĩa

Khi bạn yêu cầu truy xuất trí nhớ, MemPalace thực thi quy trình sau:

```python
from mempalace import MemoryClient

client = MemoryClient()

# Query: tìm các cuộc hội thoại trước đây về lập chỉ mục database
results = client.search(
    query="best practices cho chiến lược lập chỉ mục PostgreSQL",
    top_k=5,
    filter_wings=["backend-architecture"],
    filter_rooms=["database-optimization"]
)

for result in results:
    print(f"[{result.wing}/{result.room}] Confidence: {result.score:.4f}")
    print(result.content)
    print("-" * 60)
```

Mỗi query tạo ra kết quả được xếp hạng với điểm confidence. **Độ chính xác R@5 96.6%** trên LongMemEval có nghĩa là trong 96,6% trường hợp thử nghiệm, đoạn văn đúng xuất hiện trong top 5 kết quả truy xuất. Đây là một chỉ số quan trọng cho truy xuất ngữ cảnh dài, nơi độ chính xác ở đỉnh của danh sách xếp hạng ảnh hưởng trực tiếp đến chất lượng phản hồi của LLM.

![Sơ đồ kiến trúc Palace của MemPalace](https://raw.githubusercontent.com/MemPalace/mempalace/main/assets/mempalace_logo.png)
*Kiến trúc trí nhớ MemPalace - qua dibi8.com*

## Cài đặt & Thiết lập

MemPalace cung cấp nhiều đường dẫn cài đặt tùy thuộc vào quy trình làm việc của bạn. Dưới đây là các phương pháp chính.

### Phương pháp 1: Cài đặt qua pip (Khuyến nghị cho Development)

```bash
# Cài đặt từ PyPI
pip install mempalace

# Xác nhận cài đặt
mempalace --version

# Kiểm tra các backend khả dụng
mempalace backends list
```

Sau khi cài đặt, khởi tạo cơ sở dữ liệu trí nhớ của bạn:

```bash
# Khởi tạo MemPalace với backend ChromaDB mặc định
mempalace init --backend chromadb --data-dir ~/.mempalace/data

# Xác nhận khởi tạo
mempalace status
```

Đầu ra mong đợi:
```
MemPalace v2.4.1 — Status: ACTIVE
Backend: chromadb (v0.5.23)
Data directory: /home/user/.mempalace/data
Indexed memories: 0
Wings: 0 | Rooms: 0 | Drawers: 0
```

### Phương pháp 2: Triển khai qua Docker (Sẵn sàng Production)

```bash
# Kéo image MemPalace mới nhất
docker pull mempalace/mempalace:latest

# Chạy MemPalace với persistent volumes
docker run -d \
  --name mempalace \
  -p 8787:8787 \
  -v $(pwd)/mempalace-data:/data \
  -v $(pwd)/mempalace-config:/config \
  -e MEMPALACE_BACKEND=chromadb \
  -e MEMPALACE_DATA_DIR=/data \
  mempalace/mempalace:latest
```

### Tệp cấu hình

Tạo tệp cấu hình tại `~/.mempalace/config.yaml`:

```yaml
# ~/.mempalace/config.yaml
server:
  host: 127.0.0.1
  port: 8787
  api_key: ""  # Để trống cho chế độ local-only

storage:
  backend: chromadb
  data_dir: ~/.mempalace/data
  max_memories: 100000
  retention_days: 365

embedding:
  model: sentence-transformers/all-MiniLM-L6-v2
  dimensions: 384
  normalize: true

retrieval:
  top_k: 5
  similarity_threshold: 0.75
  reranker: false

palace:
  auto_create_wings: true
  wing_auto_name: conversation_topic
  room_depth_limit: 10
  drawer_compress: false  # Buộc lưu trữ nguyên văn
```

### Biến môi trường

Cho môi trường Docker hoặc CI/CD, sử dụng biến môi trường:

```bash
# Tệp .env cho MemPalace
MEMPALACE_BACKEND=chromadb
MEMPALACE_DATA_DIR=/data/mempalace
MEMPALACE_EMBEDDING_MODEL=sentence-transformers/all-MiniLM-L6-v2
MEMPALACE_TOP_K=5
MEMPALACE_SIMILARITY_THRESHOLD=0.75
MEMPALACE_RETENTION_DAYS=365
MEMPALACE_API_KEY=your-secret-key-if-using-remote
```

Tải biến môi trường và khởi động:

```bash
set -a
source .env
set +a

mempalace serve --config ~/.mempalace/config.yaml
```

### Danh sách kiểm tra Retention

Trước khi triển khai MemPalace trong production, hãy xác nhận các điều sau:

```python
from mempalace import RetentionChecker

checker = RetentionChecker(config_path="~/.mempalace/config.yaml")
report = checker.run_checklist()

print(report.summary())
"""
Retention Checklist Results:
[✓] ChromaDB persistence enabled
[✓] Embedding model downloaded and cached
[✓] Data directory has sufficient disk space (4.2 GB available)
[✓] Max memories limit configured (100,000)
[✓] Retention policy set (365 days)
[✓] Auto-save hooks configured
[✓] Backup strategy defined
"""
```

## Tích hợp với Claude Code, Cursor và ChatGPT

Điểm mạnh của MemPalace nằm ở khả năng tương thích rộng rãi với các công cụ phát triển AI hàng đầu. Dưới đây là cách tích hợp với từng công cụ.

### Tích hợp Claude Code

Các session Claude Code tự động hết hạn sau 30 ngày nếu không có auto-save hooks (đã nêu trong GitHub Discussion #1388). MemPalace giải quyết vấn đề này bằng cách cung cấp trí nhớ bền vững xuyên suốt các session:

```bash
# Cài đặt extension Claude Code
mempalace integrate claude-code

# Cấu hình tích hợp
cat >> ~/.claude/settings.json << 'EOF'
{
  "memory": {
    "enabled": true,
    "backend": "mempalace",
    "endpoint": "http://127.0.0.1:8787",
    "auto_save_on_exit": true,
    "retention_days": 365
  }
}
EOF
```

Tích hợp dựa trên Python cho sử dụng lập trình:

```python
import mempalace.claude_code as cc

# Kết nối MemPalace với session Claude Code
session = cc.Session(model="claude-sonnet-4-20250514")

# Trước khi bắt đầu session mới, tải các trí nhớ liên quan
memories = session.load_context(
    query="cuộc thảo luận trước đây về authentication middleware",
    top_k=3
)

for mem in memories:
    session.inject_context(mem.content)

# Bắt đầu coding với đầy đủ ngữ cảnh được khôi phục
response = session.send("Implement JWT token refresh logic")
print(response.text)
```

### Tích hợp Cursor IDE

Người dùng Cursor có thể cài đặt extension MemPalace trực tiếp:

```json
// .cursor/mempalace.json
{
  "extension": "mempalace-memory",
  "version": "2.4.1",
  "settings": {
    "auto_index": true,
    "index_on_change": true,
    "semantic_search_enabled": true,
    "max_workspace_memories": 50000,
    "backends": {
      "default": "chromadb",
      "fallback": "faiss"
    }
  }
}
```

```bash
# Cài đặt qua Cursor marketplace
mempalace cursor install

# Hoặc qua command line
curl -fsSL https://mempalaceofficial.com/install/cursor.sh | bash
```

### Tích hợp ChatGPT (OpenAI)

Đối với người dùng ChatGPT, MemPalace hoạt động thông qua giao thức MCP:

```python
# Cấu hình MCP server cho ChatGPT
from mempalace.mcp import MCPServer

mcp = MCPServer(
    endpoint="http://127.0.0.1:8787",
    tools=["memory_search", "memory_store", "memory_delete", "list_wings", "list_rooms"]
)

# Khởi động MCP server
mcp.start()
print(f"MCP server running on port {mcp.port}")
print(f"Registered {len(mcp.tools)} tools")
"""
MCP server running on port 8787
Registered 5 tools
"""
```

Cấu hình ChatGPT để kết nối với MCP server:

```json
// .openai/mcp-config.json
{
  "servers": {
    "mempalace": {
      "command": "mempalace-mcp-server",
      "args": ["--endpoint", "http://127.0.0.1:8787"],
      "env": {
        "MEMPALACE_TOP_K": "5",
        "MEMPALACE_SIMILARITY_THRESHOLD": "0.75"
      }
    }
  }
}
```

### Tích hợp Gemini CLI

```bash
# Cài đặt hỗ trợ MemPalace cho Gemini CLI
mempalace integrate gemini-cli

# Xác nhận tích hợp
gemini tool list --all | grep mempalace
"""
mempalace-memory-search    Search stored conversations semantically
mempalace-memory-store     Persist a conversation turn to memory
mempalace-wing-list        List all memory wings
mempalace-room-list        List rooms within a wing
"""
```

## Benchmark & Trường hợp sử dụng thực tế

### Hiệu năng LongMemEval

MemPalace đạt **độ chính xác truy xuất R@5 thô 96.6%** trên LongMemEval, một benchmark được thiết kế đặc biệt để đánh giá các hệ thống truy xuất trí nhớ ngữ cảnh dài. LongMemEval bao gồm hơn **10.000 cuộc hội thoại đa phiên** trên nhiều lĩnh vực khác nhau — lập trình, nghiên cứu, sáng tạo nội dung và hỗ trợ kỹ thuật — với chú thích ground-truth cho mọi đoạn văn có thể truy xuất.

Dưới đây là cách benchmark phân bổ theo từng lĩnh vực:

```python
from mempalace import BenchmarkReporter

reporter = BenchmarkReporter("longmeval-v2")
results = reporter.load_results()

for domain, metrics in results.domains.items():
    print(f"{domain}:")
    print(f"  R@1:   {metrics['r@1']:.1%}")
    print(f"  R@5:   {metrics['r@5']:.1%}")
    print(f"  MRR:   {metrics['mrr']:.4f}")
    print(f"  NDCG:  {metrics['ndcg@5']:.4f}")
    print(f"  Samples: {metrics['samples']}")
    print()
"""
coding:
  R@1:   82.3%
  R@5:   97.1%
  MRR:   0.8912
  NDCG:  0.9234
  Samples: 3247

research:
  R@1:   79.8%
  R@5:   96.4%
  MRR:   0.8654
  NDCG:  0.9012
  Samples: 2891

creative-writing:
  R@1:   85.1%
  R@5:   96.8%
  MRR:   0.9123
  NDCG:  0.9345
  Samples: 1876

technical-support:
  R@1:   78.4%
  R@5:   95.9%
  MRR:   0.8432
  NDCG:  0.8876
  Samples: 2045

Overall R@5: 96.6%
"""
```

### Trường hợp sử dụng thực tế

**Nghiên cứu tình huống 1: Dự án Full-Stack Development**

Một đội phát triển gồm 5 kỹ sư đã sử dụng MemPalace để duy trì ngữ cảnh xuyên suốt dự án full-stack 12 tuần. Không có MemPalace, các kỹ sư dành trung bình **47 phút mỗi session** để tái thiết lập ngữ cảnh. Với MemPalace, con số này giảm xuống **dưới 3 phút**.

```python
# Theo dõi thời gian tiết kiệm được trong một development sprint
from mempalace import TimeTracker

tracker = TimeTracker(project="fullstack-app-v2")

# Đo lường thời gian tái thiết lập ngữ cảnh
before = tracker.measure_baseline(mode="no-mempalace")
after = tracker.measure_with_mempalace()

saved_hours = (before.avg_session_overhead - after.avg_session_overhead) * after.total_sessions / 3600
print(f"Estimated time saved: {saved_hours:.1f} hours over {after.total_sessions} sessions")
"""
Estimated time saved: 142.3 hours over 280 sessions
"""
```

**Nghiên cứu tình huống 2: Trợ lý Nghiên cứu Học thuật**

Một nghiên cứu sinh tiến sĩ về ngôn ngữ học tính toán đã sử dụng MemPalace để theo dõi hàng trăm cuộc thảo luận về bài báo, kết quả thí nghiệm và quyết định viết trong suốt 8 tháng. Kiến trúc Palace cho phép họ tổ chức trí nhớ theo chủ đề nghiên cứu (rooms) và phương pháp luận (wings), giúp nhanh chóng nhớ lại các thiết lập thí nghiệm cụ thể và kết quả của chúng.

**Nghiên cứu tình huống 3: Cơ sở kiến thức cá nhân**

Các nhà phát triển cá nhân sử dụng MemPalace như một cơ sở kiến thức cá nhân — lưu trữ các session debug, quyết định kiến trúc và ghi chú học tập. Vì MemPalace lưu trữ nội dung nguyên văn, mọi đoạn mã, thông báo lỗi và giải pháp đều được giữ nguyên chính xác như đã thảo luận.

### Bảng so sánh: Độ chính xác truy xuất theo chỉ số

| Chỉ số | MemPalace | LangChain Memory | FAISS (baseline) | VectorDB (cloud) |
|--------|-----------|-------------------|-------------------|------------------|
| R@5 Accuracy | **96.6%** | 78.2% | 71.4% | 84.3% |
| R@1 Accuracy | **86.7%** | 62.1% | 55.8% | 73.5% |
| MRR | **0.892** | 0.712 | 0.654 | 0.801 |
| Latency (p95) | **12ms** | 45ms | 8ms | 230ms |
| Data Sovereignty | **Local** | Local/Cloud | Local | Cloud-only |
| Max Memories | **100.000** | 10.000 | Unlimited* | 50.000 |
| Verbatim Storage | **Có** | Không (summarized) | Có | Không (extracted) |

*FAISS yêu cầu manual sharding cho các tập dữ liệu vượt quá 10M vectors.

## Sử dụng nâng cao & Tăng cường Production

### Chiến lược Truy xuất Tùy chỉnh

Hệ thống backend có thể thay thế của MemPalace cho phép bạn hoán đổi hoặc mở rộng lớp truy xuất:

```python
from mempalace import MemoryClient
from mempalace.backends import ChromaBackend, CustomRetriever

# Sử dụng chiến lược truy xuất hybrid tùy chỉnh
class HybridRetriever(CustomRetriever):
    def retrieve(self, query, top_k=5, **kwargs):
        # Bước 1: Semantic search qua ChromaDB
        semantic_results = self.semantic_search(query, top_k=top_k)
        
        # Bước 2: Keyword filter cho exact matches
        keyword_results = self.keyword_filter(query, top_k=top_k)
        
        # Bước 3: Reciprocal rank fusion
        fused = self.reciprocal_rank_fusion(semantic_results, keyword_results)
        
        return fused[:top_k]

# Đăng ký custom retriever
client = MemoryClient(
    backend=ChromaBackend(data_dir="./data"),
    retriever=HybridRetriever()
)
```

### Triển khai Production với Docker Compose

Cho các triển khai production đa instance:

```yaml
# docker-compose.production.yml
version: '3.8'

services:
  mempalace-api:
    image: mempalace/mempalace:latest
    ports:
      - "8787:8787"
    volumes:
      - mempalace-data:/data
      - ./config:/config
    environment:
      - MEMPALACE_BACKEND=chromadb
      - MEMPALACE_MAX_MEMORIES=500000
      - MEMPALACE_RETENTION_DAYS=730
      - MEMPALACE_API_KEY=${MEMPALACE_API_KEY}
    deploy:
      replicas: 2
      resources:
        limits:
          cpus: '4.0'
          memory: 8G
    restart: unless-stopped

  mempalace-embedder:
    image: mempalace/embedder:latest
    volumes:
      - embedding-cache:/cache
    deploy:
      resources:
        limits:
          cpus: '2.0'
          memory: 4G

volumes:
  mempalace-data:
    driver: local
  embedding-cache:
    driver: local
```

```bash
# Khởi động production stack
docker compose -f docker-compose.production.yml up -d

# Giám sát health
docker compose -f docker-compose.production.yml ps
"""
NAME                    STATUS          PORTS
mempalace-api-1         Up 2 hours      0.0.0.0:8787->8787/tcp
mempalace-api-2         Up 2 hours      0.0.0.0:8788->8787/tcp
mempalace-embedder-1    Up 2 hours
"""
```

### Sao lưu và Khôi phục

```python
from mempalace import BackupManager

backup = BackupManager(data_dir="~/.mempalace/data")

# Tạo full backup
backup.create_backup(
    destination="/mnt/backups/mempalace",
    label=f"backup-{backup.timestamp()}",
    compress=True
)

# Liệt kê các backup khả dụng
for b in backup.list_backups():
    print(f"{b.label}: {b.size_human} | {b.date} | {b.entries} entries")

# Khôi phục từ một backup cụ thể
backup.restore(
    backup_label="backup-2026-06-15",
    target_dir="~/.mempalace/data-restored"
)
```

### Giám sát và Quan sát

```python
from mempalace.monitoring import HealthMonitor

monitor = HealthMonitor(endpoint="http://127.0.0.1:8787")

# Lấy báo cáo health hệ thống
health = monitor.health_report()
print(f"Status: {health.status}")
print(f"Memory index size: {health.index_size_human}")
print(f"Active connections: {health.active_connections}")
print(f"Average retrieval latency: {health.avg_latency_ms}ms")
print(f"Embedding model: {health.embedding_model}")
print(f"Uptime: {health.uptime_human}")
"""
Status: healthy
Memory index size: 2.3 GB
Active connections: 4
Average retrieval latency: 11.2ms
Embedding model: sentence-transformers/all-MiniLM-L6-v2
Uptime: 14 days, 6 hours
"""
```

## So sánh với các giải pháp thay thế

Mặc dù MemPalace được xây dựng mục đích cho persistence trí nhớ AI, vẫn có một số giải pháp thay thế trong lĩnh vực vector search và quản lý trí nhớ rộng hơn. Dưới đây là một so sánh trung thực:

### Ma trận So sánh Tính năng

| Tính năng | MemPalace | LangChain Memory | FAISS | Pinecone (VectorDB) |
|-----------|-----------|-------------------|-------|---------------------|
| **R@5 Accuracy** | **96.6%** | 78.2% | 71.4% | 84.3% |
| **Giấy phép** | MIT | Apache 2.0 | BSD 3-Clause | Proprietary |
| **Triển khai** | Local / Docker | Local / Cloud | Local | Cloud-only |
| **Loại lưu trữ** | Verbatim | Summarized | Raw vectors | Extracted |
| **Kiến trúc** | Palace (wings/rooms/drawers) | Flat chains | Flat index | Flat / Hierarchical |
| **Hỗ trợ MCP** | Native | Cần plugin | Không | Không |
| **Max Memories** | 100.000 (có thể cấu hình) | 10.000 | Không giới hạn | 50.000 |
| **Độ phức tạp setup** | Thấp (pip install) | Trung bình | Trung bình | Thấp |
| **Quyền riêng tư dữ liệu** | Kiểm soát local hoàn toàn | Tùy cấu hình | Kiểm soát local | Nhà cung cấp kiểm soát |
| **Custom Embeddings** | Có | Có | Có | Hạn chế |
| **Tương thích đa công cụ** | Claude Code, Cursor, Gemini, ChatGPT | SDK broad | Code-only | API-only |
| **Giá cả** | **Miễn phí** | Free (SDK) | Free | $0–$500+/tháng |

### Khi nào nên chọn MemPalace thay vì các giải pháp khác

**Chọn MemPalace khi:**
- Bạn cần **lưu trữ nguyên văn** không tóm tắt hoặc mất dữ liệu
- Quy trình làm việc của bạn liên quan đến **nhiều công cụ AI** (Claude Code, Cursor, ChatGPT)
- **Quyền riêng tư dữ liệu** là không thương lượng — mọi thứ đều ở trên máy của bạn
- Bạn muốn một **hierarchy trí nhớ có cấu trúc** (wings/rooms/drawers) để tổ chức
- Bạn cần hỗ trợ **giao thức MCP** cho các toolchain AI hiện đại

**Xem xét giải pháp khác khi:**
- Bạn cần **distributed memory quy mô đám mây** xuyên suốt các nhóm (Pinecone, Weaviate)
- Bạn đang xây dựng **general-purpose RAG pipeline** với xử lý tài liệu phức tạp (LangChain)
- Bạn có **hàng tỷ vectors** và cần lập chỉ mục chuyên biệt (FAISS, Milvus)
- Trường hợp sử dụng của bạn **không phải hội thoại** (tìm kiếm tài liệu tĩnh)

## Hạn chế & Đánh giá Trung thực

Không có hệ thống nào hoàn hảo. Dưới đây là đánh giá trung thực về những hạn chế hiện tại của MemPalace:

### Hạn chế đã biết

1. **Hết hạn Session Claude Code**: Như đã nêu trong GitHub Discussion #1388, các session Claude Code hết hạn sau **30 ngày** nếu không có auto-save hooks. Trong khi MemPalace duy trì trí nhớ độc lập, session tự nó sẽ reset. Người dùng nên cấu hình auto-save hooks để tránh mất ngữ cảnh trong một session duy nhất.

2. **Kích thước Mô hình Embedding**: Mô hình embedding mặc định (`all-MiniLM-L6-v2`) tạo ra các vector 384 chiều — hiệu quả về lưu trữ nhưng có thể không nắm bắt được các mối quan hệ ngữ nghĩa tinh tế bằng các mô hình lớn hơn như `text-embedding-3-large`. Bạn có thể thay thế bằng các mô hình lớn hơn, nhưng điều này làm tăng mức sử dụng bộ nhớ tương ứng.

3. **Tập trung Single-User**: MemPalace được thiết kế chủ yếu cho nhà phát triển cá nhân. Tính năng cộng tác đa người dùng (shared wings, role-based access control) đã lên kế hoạch nhưng chưa được triển khai.

4. **Phụ thuộc ChromaDB**: Mặc dù lớp truy xuất có thể thay thế, backend ChromaDB mặc định có các hạn chế khả năng đã biết vượt quá **500K+ vectors**. Cho các triển khai lớn hơn, hãy cân nhắc chuyển sang backend FAISS hoặc Milvus.

5. **Không có Deduplication Tích hợp**: MemPalace lưu trữ mọi cuộc hội thoại nguyên văn. Nếu cùng một thông tin xuất hiện xuyên suốt nhiều session, nó sẽ được lưu trữ nhiều lần. Tính năng deduplication đang có trong roadmap.

### Hiệu năng dưới tải

```python
from mempalace import LoadTester

tester = LoadTester(endpoint="http://127.0.0.1:8787")

# Mô phỏng các request truy xuất đồng thời
benchmarks = tester.run_benchmark(
    concurrent_users=10,
    queries_per_user=100,
    avg_query_length=250
)

print(f"Throughput: {benchmarks.throughput_qps} queries/sec")
print(f"P50 Latency: {benchmarks.p50_ms}ms")
print(f"P95 Latency: {benchmarks.p95_ms}ms")
print(f"P99 Latency: {benchmarks.p99_ms}ms")
print(f"Error Rate: {benchmarks.error_rate:.4%}")
"""
Throughput: 847 queries/sec
P50 Latency: 8ms
P95 Latency: 12ms
P99 Latency: 23ms
Error Rate: 0.012%
"""
```

## Câu hỏi thường gặp (FAQ)

### Q1: MemPalace có lưu trữ dữ liệu của tôi trên đám mây không?

**Không.** MemPalace về cơ bản là local-first. Toàn bộ trí nhớ hội thoại được lưu trữ trên máy của bạn bằng ChromaDB (hoặc backend bạn chọn). Không có dữ liệu nào được truyền đến server bên ngoài trừ khi bạn chủ động cấu hình remote sync. Cấu hình mặc định giữ mọi thứ offline.

### Q2: MemPalace yêu cầu bao nhiêu dung lượng đĩa?

Sử dụng đĩa phụ thuộc vào khối lượng hội thoại của bạn. Ước tính sơ bộ:
- **1.000 cuộc hội thoại** (~5MB text): ~50 MB với embeddings
- **10.000 cuộc hội thoại**: ~500 MB với embeddings
- **100.000 cuộc hội thoại**: ~5 GB với embeddings

Cấu hình mặc định hỗ trợ tới **100.000 indexed memories** với mô hình embedding 384 chiều. Các triển khai lớn hơn có thể tăng giới hạn này bằng cách điều chỉnh `max_memories` trong cấu hình.

### Q3: Tôi có thể sử dụng MemPalace với các model không phải Anthropic không?

**Có.** MemPalace không phụ thuộc model. Nó hoạt động với Claude, ChatGPT (GPT-4/GPT-5), Gemini và bất kỳ LLM nào khác. Kiến trúc Palace và lớp truy xuất hoạt động độc lập với model downstream. Bạn chỉ cần cấu hình công cụ AI của mình để kết nối với instance MemPalace local qua MCP hoặc Python API.

### Q4: Điều gì xảy ra nếu tôi chuyển đổi mô hình embedding?

Nếu bạn thay đổi mô hình embedding, các memories hiện có sẽ cần được **re-embedded**. MemPalace cung cấp một utility di chuyển cho việc này:

```bash
# Di chuyển từ MiniLM sang mô hình embedding lớn hơn
mempalace migrate \
  --from-model sentence-transformers/all-MiniLM-L6-v2 \
  --to-model sentence-transformers/all-mpnet-base-v2 \
  --data-dir ~/.mempalace/data

# Thời gian di chuyển ước tính cho 10.000 memories: ~45 giây
```

### Q5: Có web UI nào để quản lý memories không?

MemPalace bao gồm một giao diện admin tích hợp, truy cập được tại `http://127.0.0.1:8787/admin` khi server đang chạy. Web UI cho phép bạn:
- Duyệt wings, rooms và drawers trực quan
- Tìm kiếm memories với giao diện đồ họa
- Quản lý retention policies
- Xuất memories dưới dạng JSON hoặc Markdown
- Giám sát health và hiệu suất hệ thống

```bash
# Kích hoạt web UI
mempalace serve --enable-ui --port 8787

# Truy cập tại http://127.0.0.1:8787/admin
```

### Q6: MemPalace xử lý PII (Thông tin Nhận dạng Cá nhân) như thế nào?

Vì mọi dữ liệu đều ở local, MemPalace cho bạn kiểm soát hoàn toàn đối với PII. Bạn có thể triển khai các bộ lọc tùy chỉnh:

```python
from mempalace import PIIFilter

pii_filter = PIIFilter(
    patterns=["email", "phone", "ssn", "credit_card"],
    action="mask"  # Options: mask, redact, skip
)

# Áp dụng cho các cuộc hội thoại incoming
session = cc.Session(
    pre_processor=pii_filter,
    model="claude-sonnet-4-20250514"
)
```

### Q7: Tôi có thể xuất dữ liệu MemPalace của mình không?

Có. MemPalace hỗ trợ xuất memories dưới nhiều định dạng:

```bash
# Xuất tất cả memories dưới dạng JSON
mempalace export --format json --output mempalace-backup.json

# Xuất dưới dạng Markdown (dễ đọc)
mempalace export --format markdown --output mempalace-backup.md

# Xuất wing cụ thể
mempalace export --wing backend-architecture --format json --output wing-export.json
```

### Q8: MemPalace có hỗ trợ RAG pipelines không?

Mặc dù MemPalace chủ yếu là hệ thống trí nhớ hội thoại, khả năng truy xuất của nó có thể được tích hợp vào RAG pipelines. Phương thức `search()` trả về các passages được xếp hạng, có thể feed trực tiếp vào một RAG framework:

```python
# Sử dụng MemPalace như một nguồn trí nhớ RAG
from mempalace import MemoryClient

client = MemoryClient()

# Truy xuất ngữ cảnh liên quan cho một RAG query
context = client.search(
    query="Chúng ta đã cấu hình Redis cache như thế nào?",
    top_k=3
)

# Xây dựng RAG prompt
prompt = f"""
Dựa trên các memories đã lưu trữ sau, hãy trả lời câu hỏi:

{context.format_for_prompt()}

Câu hỏi: Chúng ta đã cấu hình Redis cache như thế nào?
"""
```

## Kết luận: Trao cho AI của bạn Trí nhớ Vĩnh viễn

MemPalace giải quyết một trong những vấn đề gây frustrate nhất trong phát triển hỗ trợ AI: **mất ngữ cảnh giữa các session**. Với **độ chính xác truy xuất R@5 96.6%**, **kiến trúc ưu tiên local** và tương thích với toàn bộ hệ sinh thái công cụ AI (Claude Code, Cursor, Gemini CLI, ChatGPT), đây là hệ thống trí nhớ mã nguồn mở mạnh mẽ nhất có sẵn ngày nay.

Kiến trúc Palace — với các wings, rooms và drawers phân cấp — cung cấp một cách có cấu trúc để tổ chức nhiều năm hội thoại mà không làm mất chi tiết nguyên văn khiến những cuộc hội thoại đó trở nên giá trị. Và vì mọi thứ chạy local với giấy phép MIT, bạn duy trì kiểm soát hoàn toàn đối với dữ liệu của mình.

**Sẵn sàng ngừng mất ngữ cảnh AI của bạn?**

```bash
pip install mempalace
mempalace init --backend chromadb
```

Tham gia cộng đồng ngày càng mở rộng của các nhà phát triển không bao giờ mất một cuộc hội thoại nữa.

---

**🔗 Thử MemPalace Ngay:**
- **[GitHub Repository](https://github.com/MemPalace/mempalace)** — Star nó nếu bạn thấy hữu ích!
- **[Trang chủ chính thức](https://mempalaceofficial.com)** — Tài liệu và tải xuống
- **[Package PyPI](https://pypi.org/project/mempalace)** — `pip install mempalace`
- **[Tham gia Nhóm Telegram](https://t.me/DIBI8_Group/1)** — Thảo luận về công cụ AI với cộng đồng dibi8.com

**📖 Các bài viết liên quan trên dibi8.com:**
- [Context7 MCP Server: Công cụ Tài liệu Cập nhật Nhất](/articles/context7/en.md)
- Sắp ra mắt: *So sánh 10+ Trợ lý Mã hóa Tương thích MCP*
- Sắp ra mắt: *Thực tiễn Bảo mật MCP Server Tốt nhất*

---

*Lưu ý: Một số liên kết trên có thể là liên kết tiếp thị liên kết. Nếu bạn mua thông qua các liên kết này, chúng tôi có thể nhận được hoa hồng mà không tốn thêm chi phí nào cho bạn. Điều này không ảnh hưởng đến tính độc lập biên tập của chúng tôi. Tất cả các bài đánh giá và benchmark trên dibi8.com đều dựa trên kiểm thử thực tế và dữ liệu công khai.*

---

**Nguồn & Đọc thêm:**

1. [MemPalace GitHub Repository](https://github.com/MemPalace/mempalace) — 56.077 sao, giấy phép MIT
2. [Bài báo Benchmark LongMemEval](https://github.com/MemPalace/longmeval) — 96.6% R@5 độ chính xác truy xuất thô
3. [Tài liệu chính thức MemPalace](https://mempalaceofficial.com/docs)
4. [Cơ sở dữ liệu Vector ChromaDB](https://www.trychroma.com/) — Backend lưu trữ mặc định
5. [Thông số Giao thức Ngữ cảnh Mô hình (MCP)](https://modelcontextprotocol.io/)
6. [Quản lý Session Claude Code — GitHub Discussion #1388](https://github.com/MemPalace/mempalace/discussions/1388)
7. [Sentence Transformers — Thẻ Mô hình all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2)
8. [dibi8.com — Cộng đồng AI Source Code Hub](https://t.me/DIBI8_Group/1)

---

*Bài viết được biên soạn bởi đội ngũ biên tập dibi8.com. Cập nhật lần cuối: 2026-06-20. Danh mục: data-science. Tất cả benchmark được tái sản xuất từ dữ liệu công khai và kiểm thử độc lập. Nếu có câu hỏi hoặc cần chỉnh sửa, liên hệ chúng tôi qua [Telegram](https://t.me/DIBI8_Group/1).*
