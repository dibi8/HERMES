---
title: 'UltraRAG: 복잡한 RAG 파이프라인을 위한 로우코드 MCP 프레임워크 — 2026년 가이드'
description: 'OpenBMB의 UltraRAG는 복잡한 RAG 파이프라인 구축을 위한 로우코드 MCP 프레임워크입니다. YAML 오케스트레이션, UI 빌더, Deep Research 데모, 그리고 프로덕션 설정 가이드를 다룹니다.'
date: 2026-06-22
slug: 'ultrarag-low-code-mcp-rag-framework-innovative-pipelines-2026'
category: 'advanced-rag'
tags: ['UltraRAG', 'RAG', 'MCP', 'OpenBMB', '로우코드', 'pipeline', 'retrieval-augmented-generation', 'LLM']
github_repo: 'https://github.com/OpenBMB/UltraRAG'
stars: 5603
maintainer: 'OpenBMB'
license: 'MIT'
featureImage: 'https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/architecture.png'
lang: ko
---

![UltraRAG 아키텍처](https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/architecture.png)
*UltraRAG 아키텍처 — dibi8.com을 통한 MCP 서버 오케스트레이션 시각화*

## 소개

조건부 분기, 반복적 심화 분석, 다중 홉 추론을 처리하는 검색 증강 생성(RAG) 파이프라인을 구축하려면 일반적으로 수백 줄의 파이썬 글루 코드를 작성해야 합니다. 각 분기점마다 오류 처리, 상태 관리, 구성 간 변수 전달이 필요합니다. 파이프라인이 복잡해질수록 그 복잡도는 비선형적으로 증가합니다.

OpenBMB가 칭화대학 THUNLP와 동북대학교 NEUIR과 협력하여 개발한 UltraRAG는 다른 접근 방식을 취합니다. 개발자가 모든 것을 명령형으로 연결하도록 요구하는 대신, UltraRAG는 RAG 구성 요소를 독립적인 Model Context Protocol(MCP) 서버로 표준화하고 선언적 YAML 구성 파일을 통해 오케스트레이션합니다. 그 결과, 루프, 조건부 분기, 다단계 추론 체인을 포함한 복잡한 워크플로우를 수백 줄의 파이썬이 아닌 수십 줄의 YAML로 표현할 수 있는 프레임워크가 탄생했습니다.

MIT 라이선스로 **5,603개의 GitHub 스타**를 기록한 UltraRAG는 RAG 아키텍처를 빠르게 프로토타이핑하거나 보일러플레이트 없이 프로덕션 파이프라인을 출시해야 하는 연구자와 실무자 모두의 주목을 받고 있습니다. 이 가이드에서는 UltraRAG의 개요, MCP 기반 아키텍처 작동 방식, 설치 및 구성 방법, 인기 도구와의 통합, 성능 평가, 그리고 트레이드오프를 이해하는 방법을 다룹니다.

## UltraRAG란?

UltraRAG는 Model Context Protocol 아키텍처를 기반으로 구축된 **로우코드 RAG 개발 프레임워크**입니다. 2025년 1월에 처음 출시되었으며, 여러 주요 버전을 거쳐 2026년 1월 UltraRAG 3.0이 출시되면서 파이프라인 정의에서 모든 추론 로직 라인이 투명하게 볼 수 있는 "블랙박스 프리" 디자인을 도입했습니다.

핵심 아이디어는 간단합니다: 검색, 생성, 재순위 매기기, 프롬프트팅, 라우팅, 메모리 관리 등 모든 RAG 기본 요소를 독립적인 MCP 서버로 분해합니다. 그런 다음 순차적 실행, 루프, 조건부 분기를 지원하는 YAML 파이프라인 정의를 사용하여 이러한 서버를 워크플로우로 조합합니다.

UltraRAG에 대한 주요 정보:

- **저장소**: GitHub의 [OpenBMB/UltraRAG](https://github.com/OpenBMB/UltraRAG)
- **스타**: **5,603개** (2026년 6월 기준)
- **라이선스**: MIT
- **관리자**: OpenBMB
- **언어**: Python (Python 3.11–3.12 필요)
- **MCP 의존성**: `fastmcp>=3.3.1`
- **현재 버전**: 0.3.0.2
- **첫 출시**: 2025년 1월
- **기여자**: THUNLP, NEUIR, OpenBMB, AI9stars
- **커뮤니티**: Discord, WeChat, Feishu 그룹

UltraRAG는 LLM을 단일 모놀리식 단계로 취급하는 프레임워크와 달리, 각 작업을 개별적으로 호출 가능한 서버로 모델링합니다. 검색 서버는 `retriever_init`, `retriever_search`, `retriever_websearch` 같은 메서드를 노출합니다. 생성 서버는 `generation_init`, `generate`, `multimodal_generate`, `multiturn_generate`를 제공합니다. 라우터 서버는 LLM 지원 분류를 기반으로 조건부 분기를 가능하게 합니다.

![UltraRAG 아키텍처 다이어그램](https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/ultrarag.svg)
*dibi8.com을 통한 UltraRAG MCP 기반 아키텍처 개요*

![UltraRAG UI 캔버스](https://raw.githubusercontent.com/OpenBMB/UltraRAG/main/docs/chat_menu.png)
*dibi8.com을 통한 UltraRAG UI — 시각적 파이프라인 빌더 및 IDE*

![UltraRAG 스타 히스토리](https://api.star-history.com/svg?repos=OpenBMB/UltraRAG&type=Date)
*dibi8.com을 통한 UltraRAG GitHub 스타 성장 차트*

UltraRAG에는 RAG용 시각적 통합 개발 환경인 **UltraRAG UI**도 함께 제공됩니다. UI에는 캔버스 기반 시각 에디터와 코드 에디터 간 양방향 동기화를 지원하는 Pipeline Builder가 포함되어 있습니다. 파이프라인 매개변수를 조정하고 프롬프트를 수정하며 지식베이스 워크플로우를 시각적으로 구성한 후, 단일 명령으로 결과를 웹 인터페이스에 배포할 수 있습니다.

## UltraRAG 작동 방식

UltraRAG의 아키텍처는 두 가지 개념에 기반합니다: **MCP 서버**와 **MCP 클라이언트 파이프라인**.

### 원자 컴포넌트로서의 MCP 서버

각 RAG 기능은 MCP 서버로 구현됩니다. MCP 서버는 클라이언트가 호출할 수 있는 도구(함수)를 등록합니다. UltraRAG에서 서버는 `servers/` 디렉토리 아래에 위치하며 일관된 구조를 따릅니다:

```python
# servers/retriever/src/retriever.py (간소화)
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

각 `tool()` 등록은 메서드를 파이프라인 YAML에서 호출 가능하게 만듭니다. 서버는 자체 상태, 의존성, 리소스 할당을 독립적으로 관리합니다.

### 오케스트레이션 계층으로서 MCP 클라이언트 파이프라인

파이프라인 YAML 파일은 어떤 서버를 로드하고 어떤 순서로 도구를 호출할지 정의합니다. 다음은 최소 예제입니다:

```yaml
# examples/experiments/sayhello.yaml
servers:
  sayhello: servers/sayhello

pipeline:
- sayhello.greet
```

그리고 더 현실적인 RAG 파이프라인:

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

파이프라인은 위에서 아래로 실행됩니다. 각 단계는 서버와 도구를 참조합니다. 한 단계의 출력 변수는 `output:` 및 `input:` 지시문을 사용하여 후속 단계의 입력으로 전달할 수 있습니다.

### 조건부 분기와 루프

UltraRAG는 제어 흐름을 YAML에서 기본적으로 지원합니다. 다음은 라우터와 조건부 분기를 사용하는 LightResearch 데모의 발췌문입니다:

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

`branch` 지시문은 라우터의 출력을 평가하여 `incomplete` 또는 `complete` 분기로 분기합니다. `loop` 지시문은 포함된 단락을 고정된 횟수만큼 반복합니다. 이는 연구 중심 RAG 시스템에서 흔히 볼 수 있는 반복적 심화 패턴을 가능하게 합니다.

### 단계 간 변수 전달

단계는 이름 지정된 출력 변수를 통해 통신합니다. 단계는 출력을 선언합니다:

```yaml
- retriever.retriever_search:
    input:
      q_ls: instruction_ls
    output:
      ret_psg: retrieved_passages
```

그런데 하류 단계에서 해당 출력을 소비합니다:

```yaml
- prompt.qa_rag_boxed:
    input:
      passages: retrieved_passages
```

이 명시적 데이터 흐름 선언은 암시적 전역 상태보다 파이프라인을 더 쉽게 디버깅하고 추론할 수 있게 합니다.

## 설치 및 설정

UltraRAG는 두 가지 설치 방법을 지원합니다: `uv`를 사용한 소스 코드 설치(권장)와 Docker 배포입니다.

### 방법 1: uv를 사용한 소스 코드 설치

아직 `uv`를 설치하지 않았다면 설치하세요:

```shell
pip install uv
```

저장소를 복제하고 프로젝트 디렉토리로 이동하세요:

```shell
git clone https://github.com/OpenBMB/UltraRAG.git --depth 1
cd UltraRAG
```

필요에 따라 선택적으로 의존성을 설치할 수 있습니다:

```shell
# 코어만 (UltraRAG UI)
uv sync

# 전체 설치 (검색, 생성, 평가, 말뭉치 처리)
uv sync --all-extras

# 검색 모듈만
uv sync --extra retriever

# 생성 모듈만
uv sync --extra generation
```

가상 환경을 활성화하세요:

```shell
source .venv/bin/activate
```

### 방법 2: 기존 환경에 설치

이미 Python 3.11 또는 3.12 환경이 설정되어 있는 경우:

```shell
# 코어 의존성
uv pip install -e .

# 전체 설치
uv pip install -e ".[all]"

# 선택적 추가 의존성
uv pip install -e ".[retriever]"
uv pip install -e ".[generation]"
uv pip install -e ".[evaluation]"
```

### 방법 3: Docker 배포

사전 빌드된 이미지 가져오기:

```shell
docker pull hdxin2002/ultrarag:v0.3.0-base-cpu   # CPU 전용 버전
docker pull hdxin2002/ultrarag:v0.3.0-base-gpu   # GPU 기본 버전
docker pull hdxin2002/ultrarag:v0.3.0             # 전체 버전 (GPU)
```

또는 로컬에서 빌드:

```shell
git clone https://github.com/OpenBMB/UltraRAG.git --depth 1
cd UltraRAG
docker build -t ultrarag:v0.3.0 .
```

컨테이너 시작:

```shell
docker run -it --gpus all -p 5050:5050 hdxin2002/ultrarag:v0.3.0
```

UltraRAG UI가 자동으로 시작됩니다. `http://localhost:5050`에서 접속하세요.

### 설치 확인

포함된 데모를 실행하여 모든 것이 정상적으로 작동하는지 확인하세요:

```shell
ultrarag run examples/experiments/sayhello.yaml
```

예상 출력:

```
Hello, UltraRAG v3!
```

### .env를 통한 구성

UltraRAG는 `.env` 파일에서 API 키와 모델 엔드포인트를 읽어옵니다:

```shell
# .env.example
OPENAI_API_KEY=your_openai_key_here
OPENAI_API_BASE=https://api.openai.com/v1
VLLM_MODEL_PATH=/path/to/local/model
MILVUS_URI=http://localhost:19530
```

복사하여 커스터마이징:

```shell
cp .env.dev .env
```

## 인기 도구와의 통합

UltraRAG는 RAG 생태계 전반에 걸쳐 여러 널리 사용되는 도구와 통합됩니다. 각각의 연결 방법을 살펴보겠습니다.

### Milvus 벡터 데이터베이스

Milvus는 UltraRAG의 기본 벡터 저장소입니다. 말뭉치를 초기화하고 인덱싱하세요:

```yaml
# milvus_index.yaml
servers:
  retriever: servers/retriever

pipeline:
- retriever.retriever_init
- retriever.retriever_embed
- retriever.retriever_index
```

서버 파라미터에서 Milvus 연결을 구성하세요:

```yaml
# servers/retriever/parameter.yaml
collection_name: my_knowledge_base
backend_configs:
  milvus:
    uri: http://localhost:19530
    collection_name: my_knowledge_base
```

### 로컬 생성을 위한 vLLM

UltraRAG는 높은 처리량의 로컬 추적을 위해 vLLM을 지원합니다:

```yaml
# servers/generation/parameter.yaml
generation:
  backend: vllm
  backend_configs:
    model_name_or_path: /path/to/Qwen2.5-7B-Instruct
    tensor_parallel_size: 1
    gpu_memory_utilization: 0.9
```

초기화 및 생성:

```yaml
pipeline:
- generation.generation_init
- generation.generate
```

### OpenAI 호환 API

클라우드 기반 생성을 위해 UltraRAG는 모든 OpenAI 호환 엔드포인트와 함께 작동합니다:

```yaml
generation:
  backend: openai
  backend_configs:
    model_name_or_path: gpt-4o
    api_base: https://api.openai.com/v1
    api_key: ${OPENAI_API_KEY}
```

### 임베딩을 위한 Sentence Transformers

UltraRAG는 임베딩 연산을 위해 sentence-transformers를 지원합니다:

```yaml
# 추가 의존성 설치
uv sync --extra retriever

# parameter.yaml에서 구성
retriever:
  backend_configs:
    embedding_model: sentence-transformers/all-MiniLM-L6-v2
```

### Flask 기반 웹 UI

UltraRAG UI는 Flask를 기반으로 구축되며 완전한 RAG IDE를 제공합니다:

```shell
# UltraRAG UI 시작
python ui/app.py
```

UI는 기본적으로 포트 5050에서 실행되며 다음을 제공합니다:
- 드래그 앤 드롭 캔버스를 통한 시각적 파이프라인 빌더
- 실시간 코드 동기화
- 지식베이스 관리
- 대화형 웹 데모로의 원클릭 배포

## 벤치마크 / 실제 사용 사례

UltraRAG는 표준화된 벤치마크를 지원하는 내장 평가 프레임워크를 제공합니다. `benchmark` 서버는 평가 데이터셋을 로드하고 파이프라인 실행 전체에 걸쳐 메트릭을 추적합니다.

### 벤치마크 실행

```yaml
# 벤치마크 워크플로우 실행
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

### Deep Research 파이프라인 예제

UltraRAG의 플래그십 데모는 AgentCPM-Report 모델을 기반으로 한 Deep Research 파이프라인입니다. 이 파이프라인은 다단계 검색과 종합을 통해 포괄적인 조사 보고서를 생성합니다:

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

이 파이프라인은 라우터의 평가를 기반으로 검색, 주제 확장, 발견 사항 요약, 또는 결론 도출을 동적으로 결정하면서 최대 140회까지 반복됩니다. 이는 기존 프레임워크에서 복잡한 파이썬 상태 머신이 필요했던 수준의 다중 홉 추론입니다.

### 말뭉치 처리 파이프라인

UltraRAG에는 말뭉치 수집과 청킹을 위한 전용 서버가 포함되어 있습니다:

```yaml
# examples/demos/build_text_corpus.yaml
servers:
  corpus: servers/corpus

pipeline:
- corpus.build_corpus
- corpus.chunk
```

corpus 서버는 여러 문서 형식(PDF, DOCX, 텍스트)을 지원하며 Chonkie와 통합하여 스마트 청킹 전략을 제공합니다.

## 고급 사용법 / 프로덕션 안정화

프로덕션에서 UltraRAG를 배포하려면 기본 설정 외에도 여러 영역에 주의가 필요합니다.

### Milvus 및 LLM 백엔드와의 프로덕션 배포

UltraRAG의 배포 가이드는 전체 프로덕션 스택 설정을 다룹니다:

```shell
# 1. Milvus standalone 시작
docker run -d \
  --name milvus-standalone \
  --security-opt seccomp:unconfined \
  -p 19530:19530 \
  -p 9091:9091 \
  milvusdb/milvus:v2.4.0 \
  milvus run standalone

# 2. 환경 변수 설정
export MILVUS_URI=http://localhost:19530
export OPENAI_API_KEY=sk-xxx
export OPENAI_API_BASE=https://api.openai.com/v1

# 3. UltraRAG UI 시작
python ui/app.py
```

### 사용자 정의 서버 개발

`servers/` 아래에 새 디렉토리를 만들어 고유한 MCP 서버를 추가할 수 있습니다:

```
servers/
  my_custom_server/
    __init__.py
    parameter.yaml
    src/
      custom_tool.py
```

서버는 동일한 패턴을 따릅니다:

```python
# servers/my_custom_server/src/custom_tool.py
from ultrarag.server import UltraRAG_MCP_Server

app = UltraRAG_MCP_Server("my_custom_server")

class CustomTools:
    def __init__(self, mcp_inst: UltraRAG_MCP_Server):
        mcp_inst.tool(self.my_custom_method)

    async def my_custom_method(self, input_text: str) -> str:
        # 사용자 정의 로직
        return f"Processed: {input_text}"
```

파이프라인에 등록하세요:

```yaml
servers:
  my_custom_server: servers/my_custom_server

pipeline:
- my_custom_server.my_custom_method
```

### 루프 반복 간 상태 유지 작업

반복 간 상태를 유지해야 하는 파이프라인의 경우, UltraRAG는 상태 유지 출력 변수를 제공합니다:

```yaml
- custom.assign_citation_ids_stateful:
    input:
      ret_psg: psg_ls
    output:
      ret_psg: psg_ls
```

`_stateful` 접미사는 출력이 덮어쓰기가 아닌 루프 반복 동안 누적됨을 나타냅니다.

### 파이프라인 디버깅

UltraRAG는 네 가지 레이어를 다루는 구조화된 디버깅 가이드를 제공합니다:

1. **입력 및 검색** — 문서 파싱 및 임베딩 품질 확인
2. **추론 및 계획** — 라우터 결정 및 분기 로직 확인
3. **상태 및 컨텍스트** — 파이프라인 단계 간 변수 전달 검사
4. **배포 및 런타임** — 서버 상태 및 리소스 활용률 모니터링

UltraRAG UI의 Case Study 인터페이스를 사용하여 각 파이프라인 단계에서 중간 출력을 시각화하세요.

### 성능 튜닝

프로덕션을 위한 주요 구성 파라미터:

```yaml
# 파라미터 튜닝 예제
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

## 대안과의 비교

UltraRAG는 RAG 프레임워크 환경에서 독특한 위치를 차지합니다. 다음은 세 가지 주요 대안과의 비교입니다.

| 기능 | UltraRAG | Haystack | LangChain | LlamaIndex |
|---------|----------|----------|-----------|------------|
| **주요 패러다임** | YAML + MCP 서버 | 구성 요소 파이프라인 | 체인/그래프 | 데이터 중심 인덱싱 |
| **로우코드 오케스트레이션** | 예 (YAML 파이프라인) | 부분적 (Pipeline 클래스) | 아니요 (파이썬 코드) | 아니요 (파이썬 코드) |
| **조건부 분기** | 기본 제공 (YAML `branch`) | 제한적 | 사용자 정의 코드 필요 | 사용자 정의 코드 필요 |
| **루프 지원** | 기본 제공 (YAML `loop`) | 미지원 | 사용자 정의 코드 필요 | 사용자 정의 코드 필요 |
| **시각적 파이프라인 빌더** | 예 (UltraRAG UI) | 아니요 | 아니요 | 아니요 |
| **내장 평가** | 예 (benchmark 서버) | 예 (EvaluationPipeline) | LangSmith를 통해 | GPTEvaluator를 통해 |
| **벡터 DB 지원** | Milvus, FAISS, 웹 검색 | 10+ (Elasticsearch, Pinecone 등) | 15+ | 15+ |
| **LLM 백엔드** | OpenAI, vLLM, 사용자 정의 | OpenAI, Ollama, vLLM, 사용자 정의 | OpenAI, Anthropic, 사용자 정의 | OpenAI, Anthropic, 사용자 정의 |
| **멀티모달 지원** | 예 (비전 검색기) | 제한적 | 통합을 통해 | 통합을 통해 |
| **라이선스** | MIT | Apache-2.0 | MIT | MIT |
| **Python 버전** | 3.11–3.12 | 3.9+ | 3.9+ | 3.9+ |
| **MCP 아키텍처** | 예 | 아니요 | 아니요 | 아니요 |

기본 제공 제어 흐름과 함께 YAML 우선 접근 방식이 UltraRAG의 주요 차별점입니다. LangChain과 LlamaIndex는 분기와 루프를 위해 명령형 파이썬 코드가 필요한 반면, UltraRAG는 이러한 패턴을 선언적으로 표현합니다. Haystack은 정신적으로 더 가깝지만 MCP 분해와 시각적 파이프라인 빌더가 부족합니다.

시각적 오케스트레이션을 강조하는 **UltraRAG 대체재**를 찾는 개발자에게는 오픈소스 공간에서 UltraRAG UI가 가장 가까운 옵션입니다. 넓은 벡터 데이터베이스 호환성을 우선시하는 경우 Haystack이나 LlamaIndex가 더 많은 기본 제공 커넥터를 제공할 수 있습니다.

## 한계 / 솔직한 평가

어떤 프레임워크도 완벽하지 않습니다. 프로젝트에 UltraRAG를 평가할 때 고려해야 할 몇 가지 한계가 있습니다.

### 생태계 성숙도

UltraRAG는 2025년 1월에 출시되어 Haystack(2022년)이나 LangChain(2023년)에 비해 상대적으로 젊은 프레임워크입니다. `servers/` 아래의 서버 카탈로그는 일반적인 RAG 기본 요소를 다루지만 아직 성숙한 프레임워크만큼 많은 특수 도구는 포함하지 않습니다. 특정 엔터프라이즈 검색 엔진처럼 희귀한 통합이 필요한 경우 자체 서버를 작성해야 할 수 있습니다.

### MCP 및 fastmcp에 대한 의존성

UltraRAG는 Model Context Protocol과 `fastmcp` 라이브러리에 의존합니다. MCP가 Adoption을 늘리고 있지만 여전히 진화 중인 표준입니다. MCP 사양의 변경은 UltraRAG 서버의 업데이트를 요구할 수 있습니다. 만약 팀이 이러한 의존성 리스크에 불편함을 느낀다면 더 확립된 프레임워크가 더 나을 수 있습니다.

### 전체 기능을 위한 GPU 요구사항

로컬 생성(vLLM)과 임베딩 모델로 UltraRAG를 실행하려면 상당한 GPU 메모리가 필요합니다. Docker 이미지가 크고 모든 추가 의존성을 포함한 전체 설치는 PyTorch, transformers, vLLM을 가져옵니다. 만약 CPU 전용 인프라에 배포하는 경우 생성을 위해 클라우드 API 백엔드에 제한됩니다.

### 문서 언어

[ultrarag.openbmb.cn](https://ultrarag.openbmb.cn)에 영어 문서가 존재하지만, 주요 개발 커뮤니티는 WeChat, Feishu, Discord를 통해 소통합니다. 중국어 자원과 블로그 게시물이 더 많습니다. 중국어를 구사하지 않는 개발자는 번역된 문서의 격차를 가끔 마주할 수 있습니다.

### 평가 커버리지

내장 평가 프레임워크는 일반적 메트릭(정밀도, 재현율, F1, 문맥 정밀도/재현율)을 지원하지만 아직 전용 평가 플랫폼이 제공하는 광범위한 상황별 벤치마크를 포함하지는 않습니다. 프로덕션 등급의 평가 파이프라인을 위해 RAGAS나 DeepEval 같은 도구로 UltraRAG의 benchmark 서버를 보완하고 싶을 수 있습니다.

## 자주 묻는 질문

### CPU 전용 기계에 UltraRAG를 설치하려면 어떻게 해야 하나요?

CPU Docker 이미지를 사용하거나 최소한의 추가로 설치하세요:

```shell
git clone https://github.com/OpenBMB/UltraRAG.git --depth 1
cd UltraRAG
uv sync --extra retriever
# vLLM 대신 OpenAI 호환 API를 생성에 사용
```

`retriever` 추가 의존성에는 GPU 요구사항 없이 로컬 벡터 검색을 위한 FAISS가 포함되어 있습니다.

### UltraRAG가 지원하는 Python 버전은 무엇인가요?

UltraRAG는 Python 3.11 또는 3.12가 필요합니다. `fastmcp`와 `vllm`의 의존성 요구사항으로 인해 Python 3.10 이전은 지원하지 않습니다. `uv`를 사용하여 가상 환경을 자동으로 관리하세요:

```shell
uv python install 3.12
uv sync
```

### UltraRAG를 Milvus 외 벡터 데이터베이스와 함께 사용할 수 있나요?

네. 검색기는 `parameter.yaml`을 통해 구성된 여러 백엔드를 지원합니다. FAISS는 `retriever` 추가 의존성을 통해 사용 가능하며, `index_backends` 모듈을 확장하여 사용자 정의 인덱스 백엔드를 추가할 수 있습니다:

```yaml
backend_configs:
  faiss:
    index_type: IVF_FLAT
    nlist: 128
```

### 프로덕션 RAG를 위해 UltraRAG는 Haystack과 어떻게 비교되나요?

Haystack은 광범위한 커넥터 커버리지로 강점이 있으며 수년간 프로덕션에서 검증되었습니다. UltraRAG의 장점은 루프와 조건부 분기를 기본 제공하는 YAML 기반 오케스트레이션에 있습니다 — 이러한 패턴은 Haystack의 구성 요소 파이프라인에서 표현하기 번거롭습니다. 단순한 선형 RAG 파이프라인의 경우 Haystack이 더 간단할 수 있습니다. 복잡한 반복적 또는 다중 경로 워크플로우의 경우 UltraRAG의 선언적 접근 방식이 보일러플레이트를 크게 줄입니다. 더 깊은 비교를 위해 [Haystack 프로덕션 가이드](haystack-ai-production-rag-framework-llm-applications-2026)를 참조하세요.

### UltraRAG는 프로덕션 배포에 적합한가요?

UltraRAG 3.0은 프로덕션을 염두에 두고 설계되었습니다. MCP 서버 아키텍처는 검색, 생성, 라우팅 구성 요소의 독립적인 스케일링을 가능하게 합니다. Docker 지원은 컨테이너화된 배포를 간소화합니다. 그러나 프레임워크가 일부 대안보다 젊기 때문에 중요한 워크로드에 배포하기 전에 신뢰성, 모니터링, 롤백 절차에 대한 추가 테스트를 계획해야 합니다.

### UltraRAG에 사용자 정의 LLM 제공자를 추가하려면 어떻게 해야 하나요?

LLM 제공자를 감싸는 새로운 생성 서버를 만드세요. `servers/generation/`의 기존 OpenAI 호환 서버가 패턴을 보여줍니다:

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

### UltraRAG v2와 v3의 차이점은 무엇인가요?

UltraRAG v2는 MCP 서버 분해와 YAML 파이프라인 오케스트레이션을 도입했습니다. UltraRAG v3(2026년 1월 출시)는 양방향 캔버스-코드 동기화가 있는 시각적 UI, 루프 반복 간 개선된 상태 유지 변수 전달, 향상된 디버깅 도구를 추가했으며, 중간 파이프라인 상태를 검사하기 어려웠던 "블랙박스" 문제를 제거하여 추론 로직을 완전히 투명하게 만들었습니다.

### 로컬 오픈소스 모델과 UltraRAG를 사용할 수 있나요?

네. UltraRAG는 OpenAI 호환 API나 vLLM, Ollama 같은 로컬 추론 엔진을 통해 접근 가능한 모든 모델을 지원합니다. 로컬 모델의 경우 생성 서버를 구성하세요:

```yaml
generation:
  backend: openai
  backend_configs:
    model_name_or_path: ollama/qwen2.5:7b
    api_base: http://localhost:11434/v1
```

### UltraRAG 파이프라인을 어떻게 평가하나요?

내장 benchmark 서버를 평가 데이터셋과 함께 사용하세요:

```shell
# 벤치마크 데이터셋 다운로드
ultrarag download-data --dataset hotpotqa

# 평가 실행
ultrarag run examples/demos/RAG.yaml --eval
```

평가 서버는 표준 메트릭을 계산하고 다양한 파이프라인 구성 간 비교 보고서를 생성합니다.

## 출처 및 추가 자료

- [UltraRAG 공식 문서](https://ultrarag.openbmb.cn/pages/en/getting_started/introduction)
- [UltraRAG GitHub 저장소](https://github.com/OpenBMB/UltraRAG)
- [UltraRAG 3.0 릴리스 블로그](https://github.com/OpenBMB/UltraRAG/blob/page/project/blog/en/ultrarag3_0.md)
- [Model Context Protocol (MCP) 사양](https://modelcontextprotocol.io/docs/getting-started/intro)
- [UltraRAG 벤치마크 데이터셋](https://modelscope.cn/datasets/UltraRAG/UltraRAG_Benchmark)
- [VisRAG 논문 (ICLR 2025)](https://arxiv.org/abs/2410.10594)
- [RAG-DDR 논문 (ICLR 2025)](https://arxiv.org/abs/2410.13509)
- [AgentCPM-Report 모델 (Hugging Face)](https://huggingface.co/openbmb/AgentCPM-Report)
- [UltraRAG 설치 — 전체 튜토리얼](https://ultrarag.openbmb.cn/pages/en/getting_started/quick_start)
- [UltraRAG Deep Research 파이프라인 가이드](https://ultrarag.openbmb.cn/pages/en/demo/deepresearch)
- [UltraRAG 프로덕션 배포 가이드](https://ultrarag.openbmb.cn/pages/en/ui/prepare)
- [관련: [Haystack 프로덕션 RAG 가이드]](haystack-ai-production-rag-framework-llm-applications-2026)
- [관련: [RAG 아키텍처 구현 가이드]](rag-architecture-implementation-guide)
- [관련: [Context7 MCP 서버 프로덕션 설정]](context7-mcp-server-production-setup-guide)

## 결론: 오늘 첫 UltraRAG 파이프라인을 배포하세요

UltraRAG는 RAG 생태계에서 진정한 격차를 채웁니다: **복잡한 검색 워크플로우의 선언적 오케스트레이션**. 조건부 분기, 반복적 심화 분석, 다중 홉 추론을 처리하기 위해 명령형 파이썬 코드를 작성하는 것에 지쳤다면, UltraRAG의 YAML 우선 MCP 아키텍처가 더 깔끔한 대안을 제공합니다.

로우코드 파이프라인 정의, 시각적 UI 빌더, 내장 평가, 기본 제공 루프/분기 지원을 비롯한 프레임워크의 강점은 새로운 RAG 아키텍처를 프로토타이핑하는 연구자와 Deep Research 에이전트 같은 반복적 검색 시스템을 구축하는 팀에게 특히 가치 있습니다.

시작하려면 저장소를 복제하고 `uv`로 설치한 후 포함된 데모를 실행하세요. YAML에 익숙하다면 학습 곡선이 얕고, 시각적 UI는 그 장벽까지 제거합니다.

**UltraRAG 배포에 클라우드 인프라가 필요하신가요?** [DigitalOcean](https://m.do.co/c/eca87ac14ee0)은 UltraRAG의 Milvus 및 vLLM 백엔드와 잘 어울리는 저렴한 GPU 드롭렛과 관리형 데이터베이스를 제공합니다.

*위 링크 중 일부는 제휴 링크입니다. 가입 시 dibi8.com이 수수료를 받을 수 있으며, 귀하의 비용에는 영향이 없습니다.*

RAG 프레임워크, MCP 아키텍처, 프로덕션 배포 전략에 대한 지속적인 논의를 위해 Telegram에서 dibi8.com 커뮤니티에 참여하세요: [t.me/DIBI8_Group/2](https://t.me/DIBI8_Group/2)

UltraRAG에 대해 질문하거나 파이프라인 설계를 공유하고 싶으신가요? Telegram 그룹에 메시지를 남겨 UltraRAG로 구축하는 다른 개발자와 연결하세요.
