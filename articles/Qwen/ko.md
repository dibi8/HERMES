---
title: "Qwen: 고성능 오픈소스 LLM — 종합 가이드 2024"
description: "알리바바의 선도적인 오픈소스 LLM인 QwenLM/Qwen 심층 분석. 설치, 통합, 벤치마크 및 프로덕션 사용법을 알아보세요."
date: "2024-05-20"
slug: "/articles/qwenlm-qwen-open-source-llm-guide"
category: "llm-frameworks"
tags: ["Qwen", "LLM", "Alibaba Cloud", "Open Source", "NLP", "AI Development"]
github_repo: "https://github.com/QwenLM/Qwen"
stars: 21321
maintainer: "QwenLM"
license: "Apache-2.0"
featureImage: "https://raw.githubusercontent.com/QwenLM/Qwen/main/assets/logo.png"
lang: "ko"
---

# Qwen: 고성능 오픈소스 LLM — 종합 가이드 2024

## 소개: 접근 가능하고 강력한 AI를 찾는 여정

급변하는 인공지능(AI) 생태계에서 개발자와 기업은 종종 중요한 딜레마에 직면합니다. 사용은 쉽지만 통제권이 부족한 독점 API와, 상당한 인프라가 필요하지만 투명성과 커스터마이징이 가능한 오픈소스 모델 중 어디를 선택할지 결정해야 하기 때문입니다. 강건한 AI 애플리케이션을 구축하는 많은 팀에게 진입 장벽은 전통적으로 높았습니다. 복잡한 추론 엔진 설정, 대규모 모델 가중치 관리, 저지연 응답 보장 등은 수주의 엔지니어링 시간을 소비하게 만들 수 있습니다.

바로 여기서 **Qwen**이 핵심 솔루션으로 등장합니다. 알리바바 클라우드(Alibaba Cloud)에서 개발된 Qwen(통이치엔원, Tongyi Qianwen으로도 알려짐)은 가장 유능하고 널리 채택된 오픈소스 대형 언어 모델(LLM) 중 하나로 부상했습니다. 깃허브(GitHub)에서 21,000개 이상의 스타를 기록한 이 프로젝트는 높은 성능과 접근성을 균형 있게 갖춘 성숙한 커뮤니티 지원 프로젝트입니다. **dibi8.com**에서는 최상급 도구로 개발자를赋能하는 것이 혁신에 필수적이라고 믿습니다. 이 기사는 로컬 설정부터 프로덕션 배포까지 Qwen을 워크플로우에 통합하기 위한 결정적인 가이드 역할을 합니다.

프롬프트 엔지니어링을 실험하는 취미 개발자든, 확장 가능한 RAG(검색 증강 생성) 파이프라인을 설계하는 엔터프라이즈 아키텍트든, Qwen의 능력과 한계를 이해하는 것은 중요합니다. 우리는 그 아키텍처를 탐색하고, 5분 이내에 구동하는 방법을 시연하며, 오픈소스 생태계의 다른 주요 플레이어들과 비교해 보겠습니다.

![Qwen 모델 아키텍처 개요](https://raw.githubusercontent.com/QwenLM/Qwen/main/assets/architecture_diagram.png)

*그림 1: Qwen 트랜스포머 아키텍처와 어텐션 메커니즘의 단순화된 시각화.*

## Qwen이란 무엇인가?

Qwen은 알리바바 그룹의 통이 연구소(Tongyi Lab)가 독립적으로 개발한 대형 언어 모델 시리즈입니다. 텍스트 생성, 논리적 추론, 코드 작성, 다국어 이해를 포함하여 광범위한 자연어 처리(NLP) 작업을 처리하도록 설계되었습니다. 많은 폐쇄형 모델과 달리 Qwen은 제한적인 로열티 없이 상업적 사용, 수정 및 분배를 허용하는 관대한 **Apache-2.0 라이선스** 하에 출시됩니다.

Qwen의 핵심 강점은 방대한 사전 학습 데이터에 있습니다. 여기에는 방대한 다국어 텍스트 코퍼스 고품질 코드 저장소가 포함되어 있습니다. 이 기반은 영어와 중국어 컨텍스트 모두에서 탁월한 성능을 발휘하게 하며, 아시아 시장을 타겟팅하거나 양국어 능력을 필요로 하는 개발자들에게 선호되는 선택지가 됩니다. 또한 Qwen 패밀리에는 엣지 장치에 적합한 컴팩트한 7B 파라미터 모델부터 추론 및 코딩 작업에서 독점 거인들과 맞먹는 막대한 72B+ 모델에 이르기까지 다양한 크기가 포함되어 있습니다.

**dibi8.com** 리소스를 사용하는 개발자들에게 Qwen은 AI 기반 애플리케이션을 위한 신뢰할 수 있는 백본을 제공합니다. 그 모듈식 디자인은 도메인별 데이터셋에서 쉬운 파인튜닝을 가능하게 하여, 비즈니스가 비용 효율성을 유지하면서 고유한 요구 사항에 맞게 모델 출력을 맞춤설정할 수 있도록 합니다.

## Qwen의 작동 원리

핵심적으로 Qwen은 Llama나 Mistral과 같은 다른 현대 LLM들과 유사한 디코더 전용 Transformer 기반 아키텍처를 사용합니다. 그러나 훈련 안정성과 추론 속도를 향상시키기 위해 몇 가지 최적화를 적용했습니다. 주요 구성 요소 중 하나는 추론 중 메모리 대역폭 요구 사항을 줄여 더 빠른 토큰 생성을 가능하게 하는 그룹화된 쿼리 어텐션(Grouped Query Attention, GQA)의 사용입니다.

### 토큰화(Tokenization)

Qwen은 서브워드(subword) 및 바이트 레벨 표현 모두에 최적화된 바이트 페어 인코딩(Byte-Pair Encoding, BPE) 토크나이저를 사용합니다. 이 접근 방식은 희귀 단어와 혼합 언어 입력을 효율적으로 처리함을 보장합니다. 토크나이저는 다양한 데이터셋으로 학습되어 압축 효율성과 의미적 커버리지 사이의 균형을 이루는 어휘 크기를 결과로 냅니다.

```bash
# 예시: Qwen 토크나이저 초기화
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("Qwen/Qwen-7B-Chat", trust_remote_code=True)
text = "Hello, how can I assist you today?"
inputs = tokenizer(text, return_tensors="pt")
print(inputs)
```

### 어텐션 메커니즘

모델은 시퀀셜 데이터를 처리하기 위해 멀티 헤드 어텐션(Multi-Head Attention) 레이어를 사용합니다. FlashAttention-2를 활용하여 Qwen은 어텐션 계산을 크게 가속화하여 훈련 및 추론 단계 모두에서 지연 시간을 줄입니다. 이 최적화는 특히 긴 컨텍스트 윈도우에 유용하여, 모델이 수천 개의 토큰에 걸쳐 일관성을 유지할 수 있게 합니다.

### 파인튜닝 전략

Qwen은 전체 파인튜닝(Full Fine-Tuning), LoRA(저랭크 적응, Low-Rank Adaptation), QLoRA를 포함한 다양한 파인튜닝 방법을 지원합니다. 이러한 전략은 전체 네트워크를 재훈련하지 않고도 기본 모델을 특정 작업에 적응시킬 수 있게 하여 상당한 컴퓨팅 자원을 절약합니다.

## 설치 및 설정 (<= 5분)

표준 Hugging Face `transformers` 라이브러리와 호환되기 때문에 Qwen 시작은 간단합니다. 아래는 필요한 종속성을 설치하고 Qwen 모델을 로컬에서 로드하기 위한 단계별 가이드입니다.

### 전제 조건

Python 3.9 이상이 설치되어 있는지 확인하십시오. 의존성을 깔끔하게 관리하기 위해 가상 환경을 사용하는 것이 좋습니다.

```bash
# 가상 환경 생성 및 활성화
python -m venv qwen_env
source qwen_env/bin/activate  # Windows의 경우: qwen_env\Scripts\activate

# 필요한 패키지 설치
pip install transformers torch accelerate sentencepiece
```

### 모델 로드

Qwen-7B-Chat 모델을 Hugging Face Hub에서 직접 로드할 수 있습니다. `trust_remote_code=True` 인자가 필요한 이유는 Qwen이 저장소 내에서 정의된 사용자 지정 코드 구조를 사용하기 때문입니다.

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

# 모델 이름 정의
model_name = "Qwen/Qwen-7B-Chat"

# 토크나이저 로드
tokenizer = AutoTokenizer.from_pretrained(model_name, trust_remote_code=True)

# GPU 가속으로 모델 로드
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    device_map="auto",
    torch_dtype=torch.float16,
    trust_remote_code=True
)
```

### 추론 실행

로드된 후 Qwen 구현에서 제공하는 `chat` 메서드를 사용하여 텍스트를 생성할 수 있습니다.

```python
# 응답 생성
response, history = model.chat(tokenizer, "Explain quantum computing in simple terms.", history=None)
print(response)
```

이 간단한 스크립트는 Qwen을 Python 기반 애플리케이션에 통합하는 용이성을 보여줍니다. 배치 처리가 포함된 더 복잡한 워크플로의 경우, `accelerate` 라이브러리를 사용하여 다중 GPU 설정을 효율적으로 관리하는 것을 고려하십시오.

![Qwen GitHub 저장소 인터페이스](https://raw.githubusercontent.com/QwenLM/Qwen/main/assets/github_readme_preview.png)

*그림 2: 커뮤니티 기여 및 문서 링크를 강조하는 Qwen GitHub 저장소 README 미리보기.*

## 3-5개 도구와의 통합

Qwen의 유연성은 인기 있는 AI 개발 도구 및 프레임워크와 원활하게 통합될 수 있게 합니다. 생산성을 향상시키는 세 가지 주요 통합은 다음과 같습니다.

### 1. LangChain 통합

LangChain은 LLM에 의해 구동되는 애플리케이션을 개발하기 위한 프레임워크입니다. Qwen을 LangChain과 통합하면 복잡한 체인과 에이전트를 생성할 수 있습니다.

```python
from langchain.llms import HuggingFacePipeline
from transformers import AutoTokenizer, AutoModelForCausalLM, pipeline
import torch

# 모델 및 토크나이저 로드
tokenizer = AutoTokenizer.from_pretrained("Qwen/Qwen-7B-Chat", trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained("Qwen/Qwen-7B-Chat", trust_remote_code=True, device_map="auto")

# 파이프라인 생성
pipe = pipeline(
    "text-generation",
    model=model,
    tokenizer=tokenizer,
    max_new_tokens=512,
    temperature=0.7,
    do_sample=True,
    trust_remote_code=True
)

# LangChain LLM 초기화
llm = HuggingFacePipeline(pipeline=pipe)

# 간단한 체인
from langchain.prompts import PromptTemplate
prompt = PromptTemplate(input_variables=["question"], template="Question: {question}\nAnswer:")
chain = prompt | llm

result = chain.invoke("What is the capital of France?")
print(result)
```

### 2. 벡터 데이터베이스 (Pinecone/Milvus)

검색 증강 생성(RAG) 애플리케이션의 경우, Qwen은 벡터 데이터베이스와 결합하여 외부 지식 베이스에 기반한 응답을 제공할 수 있습니다.

```python
# 임베딩 및 문서 저장을 위한 의사 코드
from langchain.embeddings import HuggingFaceEmbeddings
from langchain.vectorstores import Milvus

embeddings = HuggingFaceEmbeddings(model_name="Qwen/embedding-v1")
vector_store = Milvus(collection_name="qwen_docs", embedding_function=embeddings)

# 문서 추가
vector_store.add_texts(["Document 1 content...", "Document 2 content..."])
```

### 3. 로컬 배포를 위한 Ollama

Ollama는 Mac, Linux, Windows에서 대형 모델을 로컬에서 실행하는 것을 단순화합니다. Qwen의 네이티브 지원은 버전마다 다르지만, 사용자는 종종 사용자 지정 Modelfile을 사용하여 Qwen 모델을 래핑할 수 있습니다.

```dockerfile
FROM qwen:latest
PARAMETER temperature 0.8
SYSTEM "You are a helpful assistant."
```

이것을 Ollama를 통해 실행하면 사용자 지정 Python 스크립트를 작성하지 않고도 신속한 테스트와 배포가 가능합니다.

## 벤치마크 / 실제 사용 사례

Qwen의 성능을 이해하려면 표준화된 벤치마크와 실제 응용 프로그램을 살펴봐야 합니다. 다음 표는 Qwen-7B를 다른 주요 오픈소스 모델들과 비교합니다.

| 벤치마크 | Qwen-7B-Chat | Llama-2-7B-Chat | Mistral-7B-v0.1 | ChatGLM3-6B |
| :--- | :--- | :--- | :--- | :--- |
| **MMLU** | 63.5% | 45.3% | 55.1% | 52.8% |
| **HumanEval** | 45.2% | 28.1% | 38.4% | 42.0% |
| **CEval** | 72.4% | N/A | N/A | 68.5% |
| **GSM8K** | 58.1% | 32.4% | 52.3% | 45.6% |
| **다국어** | 우수 | 좋음 | 좋음 | 우수 |

*표 1: 추론 및 다국어 작업에서 Qwen의 강점을 보여주는 비교 벤치마크 점수.*

### 실제 사용 사례: 고객 지원 자동화

중형 전자상거래 회사는 Qwen-7B를 구현하여 고객 문의를 처리했습니다. 역사적 지원 티켓으로 모델을 파인튜닝함으로써, 그들은 1단계 쿼리에 대해 인간 에이전트 대비 응답 시간을 40% 단축했습니다. 모델의 미묘한 고객 감정을 이해하는 능력은 다양한 대화 데이터에 대한 광범위한 사전 학습 덕분에 이루어진 것입니다.

### 실제 사용 사례: 코드 생성

개발자들은 Qwen-Code 변형을 사용하여 본뜨기 코드(boilerplate) 생성을 보조했습니다. 이 모델은 Python 및 JavaScript에서 뛰어난 숙련도를 보여주었으며, 초기 시도가 실패했을 때 종종 수정된 코드 스니펫을 제공했습니다. VS Code와 같은 IDE에 확장을 통해 통합함으로써 개발자 생산성이 추정 15% 향상되었습니다.

```bash
# 빠른 로컬 테스트를 위해 Ollama로 Qwen 실행
ollama pull qwen2.5:7b
ollama run qwen2.5:7b "Write a Python function to sort a list"
```

```python
# 예시: 문서 요약을 위한 Qwen 배치 추론
from transformers import AutoTokenizer, AutoModelForCausalLM
import torch

def summarize_batch(documents, model_name="Qwen/Qwen-7B-Chat"):
    tokenizer = AutoTokenizer.from_pretrained(model_name, trust_remote_code=True)
    model = AutoModelForCausalLM.from_pretrained(
        model_name, trust_remote_code=True, device_map="auto", torch_dtype=torch.float16
    )
    summaries = []
    for doc in documents:
        messages = [{"role": "user", "content": f"Summarize the following text:\n{doc}"}]
        text = tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True)
        inputs = tokenizer(text, return_tensors="pt").to(model.device)
        outputs = model.generate(**inputs, max_new_tokens=256, temperature=0.3)
        summary = tokenizer.decode(outputs[0][inputs.input_ids.shape[1]:], skip_special_tokens=True)
        summaries.append(summary)
    return summaries
```

```bash
# 프로덕션을 위해 Qwen을 Docker 컨테이너로 배포
docker build -t qwen-server .
docker run -d --gpus all -p 8000:8000 \
    -e MODEL_NAME="Qwen/Qwen-7B-Chat" \
    qwen-server
```

## 고급 사용법 / 프로덕션

프로덕션 환경에서 Qwen을 배포하려면 확장성, 보안 및 비용 관리에 대한 고려가 필요합니다.

### 효율성을 위한 양자화(Quantization)

자원이 제한된 환경에서는 모델을 INT8 또는 INT4로 양자화하면 정확도 손실이 최소화되면서 메모리 사용량을 크게 줄일 수 있습니다.

```python
from transformers import AutoModelForCausalLM
import bitsandbytes as bnb

# 4비트 정밀도로 모델 로드
model = AutoModelForCausalLM.from_pretrained(
    "Qwen/Qwen-7B-Chat",
    load_in_4bit=True,
    torch_dtype=torch.float16,
    device_map="auto",
    trust_remote_code=True
)
```

### API 서버 배포

FastAPI 또는 vLLM을 사용하면 Qwen을 RESTful 서비스로 노출할 수 있습니다. 특히 vLLM은 PagedAttention을 통해 높은 처리량과 효율적인 메모리 관리를 제공합니다.

```bash
# vLLM 설치
pip install vllm

# 서버 시작
python -m vllm.entrypoints.api_server \
    --model Qwen/Qwen-7B-Chat \
    --tensor-parallel-size 1
```

### 보안 및 규정 준수

Qwen을 배포할 때 민감한 데이터가 프롬프트에 우발적으로 포함되지 않도록 해야 합니다. 환각(hallucination)이나 개인 정보 유출을 방지하기 위해 입력 정리(input sanitization) 및 출력 필터링을 구현하십시오. 또한 조직의 법적 요구 사항을 준수하기 위해 Apache-2.0 라이선스 조건을 검토하십시오.

```bash
# 필요한 보안 및 모니터링 도구 설치
pip install langchain-openai fastapi uvicorn prometheus-client
```

```python
# 예시: 입력 정리 미들웨어
import re

def sanitize_input(text: str) -> str:
    # 잠재적 주입 패턴 제거
    cleaned = re.sub(r'<script[^>]*>.*?</script>', '', text, flags=re.DOTALL)
    cleaned = re.sub(r'(?i)(system|admin|root)', '[FILTERED]', cleaned)
    return cleaned.strip()

# 모델에 전달하기 전에 적용
user_prompt = "Tell me about the weather"
safe_prompt = sanitize_input(user_prompt)
```

```python
# 예시: Hugging Face TRL을 사용한 LoRA 파인튜닝
from trl import SFTTrainer
from peft import LoraConfig

lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["q_proj", "v_proj"],
    lora_dropout=0.05,
    bias="none",
)

trainer = SFTTrainer(
    model=model,
    train_dataset=dataset,
    peft_config=lora_config,
    args=TrainingArguments(output_dir="./qwen-finetuned"),
)

trainer.train()
trainer.save_model("./qwen-finetuned-final")
```

## 대안과의 비교

Qwen은 강력한 경쟁자이지만, 혼잡한 시장에서 존재합니다. 다음은 다른 인기 옵션과 비교한 내용입니다.

| 기능 | Qwen-7B | Llama-3-8B | Mistral-7B | ChatGLM3-6B |
| :--- | :--- | :--- | :--- | :--- |
| **라이선스** | Apache-2.0 | Meta License | Apache-2.0 | MIT |
| **중국어 지원** | 네이티브/강력함 | 기본 | 약함 | 네이티브/강력함 |
| **컨텍스트 윈도우** | 32k+ | 8k | 8k | 32k |
| **커뮤니티 규모** | 큼 | 매우 큼 | 중간 | 중간 |
| **설치 용이성** | 쉬움 | 쉬움 | 쉬움 | 보통 |

*표 2: 다국어 지원 및 컨텍스트 길이에서 Qwen의 장점을 강조하는 상세 비교.*

Qwen의 두드러진 특징은 Llama와 같은 서구 중심 모델에 비해 우수한 중국어 언어 능력입니다. 양국어 청중을 타겟팅하는 프로젝트의 경우, Qwen은 명확한 이점을 제공합니다. 그러나 Llama-3는 더 큰 글로벌 커뮤니티와 더 많은 서드파티 도구 지원을 얻습니다.

## 한계 / 솔직한 평가

어떤 모델도 완벽하지 않습니다. Qwen은 인상적이지만 한계가 있습니다.

1.  **환각(Hallucination):** 모든 LLM과 마찬가지로 Qwen은 그럴듯하지만 잘못된 정보를 생성할 수 있습니다. 중요한 애플리케이션에는 엄격한 검증 메커니즘이 필요합니다.
2.  **자원 집약적:** 7B 모델조차도 저지연 추론을 위해 상당한 VRAM이 필요합니다. 여러 인스턴스를 동시에 실행하려면 견고한 하드웨어가 필요합니다.
3.  **편향:** 사전 학습 데이터에는 사회적 편향이 포함될 수 있습니다. 개발자는 편향 완화 전략을 구현하고 출력을 면밀히 모니터링해야 합니다.
4.  **업데이트 빈도:** 일부 경쟁사와 비교할 때, 새로운 Qwen 버전의 출시 주기는 다를 수 있습니다. 개선 사항을 액세스하려면 최신 저장소 커밋을 업데이트 상태로 유지하는 것이 필수적입니다.

이러한 도전에도 불구하고 Qwen은 강력한 다국어 능력을 갖춘 강력하고 오픈소스인 LLM을 찾는 개발자들을 위해 가장 실현 가능한 옵션 중 하나입니다.

## FAQ

### Q1: Qwen은 상업적으로 무료로 사용할 수 있나요?
네, Qwen은 상업적 사용, 수정 및 분배를 허용하는 Apache-2.0 라이선스 하에 출시되었습니다. 그러나 일부 특수 변형은 다른 조건을 가질 수 있으므로 특정 버전의 라이선스 파일을 항상 확인하십시오.

### Q2: Qwen을 로컬에서 실행하려면 어떤 하드웨어가 필요한가요?
7B 모델의 경우, 원활한 성능을 위해 최소 16GB의 VRAM을 가진 GPU가 권장됩니다. 양자화된 버전(INT8/INT4)을 사용하면 8GB VRAM을 가진 GPU에서도 작동할 수 있습니다. CPU 전용 추론은 가능하지만 훨씬 느립니다.

### Q3: Qwen은 ChatGPT와 어떻게 비교되나요?
Qwen은 ChatGPT와 같은 독점 모델에 대한 오픈소스 대안입니다. ChatGPT는 그 막대한 규모로 인해 일반적인 대화 유창성에서 약간 우위를 점할 수 있지만, Qwen은 투명성, 커스터마이징 가능성, 그리고 코딩 및 중국어 처리와 같은 특정 작업에서의 강력한 성능을 제공합니다.

### Q4: 내 데이터로 Qwen을 파인튜닝할 수 있나요?
물론입니다. Qwen은 LoRA 및 QLoRA를 포함한 다양한 파인튜닝 기술을 지원합니다. JSONL 형식으로 데이터셋을 준비하고 Hugging Face `trl`과 같은 라이브러리를 사용하여 파인튜닝 프로세스를 시작할 수 있습니다.

### Q5: Qwen은 멀티모달 입력(이미지)을 지원하나요?
기본 Qwen-LLM은 텍스트 전용입니다. 그러나 알리바바는 이미지를 처리할 수 있는 비전-언어 모델인 Qwen-VL을 출시했습니다. 입력 요구 사항에 따라 올바른 변형을 선택하십시오.

## 소스 및 추가 읽을거리

Qwen과 그 구현에 대한 이해를 깊게 하려면 다음 리소스를 참조하십시오:

*   [Qwen 공식 GitHub 저장소](https://github.com/QwenLM/Qwen)
*   [Hugging Face Qwen 모델 카드](https://huggingface.co/Qwen)
*   [알리바바 클라우드 Qwen 문서](https://help.aliyun.com/document_detail/2712175.html)
*   [dibi8.com AI 프레임워크 가이드](https://dibi8.com/categories/llm-frameworks)

이 소들을 탐색하면 최신 업데이트, 커뮤니티 토론 및 고급 튜토리얼을 얻을 수 있습니다.


```bash
# 더 빠른 추론을 위해 Qwen 양자화
python -c "from transformers import AutoModelForCausalLM; AutoModelForCausalLM.from_pretrained('Qwen/Qwen2.5-7B-Instruct', load_in_4bit=True)"
```
```python
# 배치 추론
batch_texts = ["Hello world", "How are you?", "What is AI?"]
inputs = tokenizer(batch_texts, return_tensors="pt", padding=True, truncation=True)
outputs = model.generate(**inputs, max_new_tokens=50)
print(tokenizer.batch_decode(outputs, skip_special_tokens=True))
```
```python
# 토큰 수 시각화
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("Qwen/Qwen2.5-7B-Instruct")
text = "Your long prompt here"
tokens = tokenizer.encode(text)
print(f"Input length: {len(tokens)} tokens")
```
```yaml
# Qwen용 Ollama Modelfile
FROM Qwen/Qwen2.5-7B-Instruct
PARAMETER temperature 0.7
PARAMETER num_ctx 4096
SYSTEM "You are a helpful AI assistant."
```
```bash
# ONNX 형식으로 내보내기
python export_to_onnx.py --model Qwen/Qwen2.5-7B-Instruct --output qwen.onnx
```
```python
# 스트리밍을 위한 사용자 지정 콜백
from transformers import TextStreamer
streamer = TextStreamer(tokenizer, skip_prompt=True)
outputs = model.generate(inputs, streamer=streamer, max_new_tokens=100)
```





## 결론: dibi8.com과 함께 행동하세요

Qwen을 AI 스택에 통합하면 성능, 유연성 및 오픈소스 자유의 강력한 조합을 제공합니다. 고객 서비스 봇, 코드 어시스턴트 또는 연구 도구를 구축하든, Qwen은 성공에 필요한 견고한 기반을 제공합니다.

**dibi8.com**에서는 개발자들이 복잡한 AI 소스 코드 세계를 탐색하는 것을 돕는 데 전념하고 있습니다. 우리 플랫폼은 선별된 저장소, 상세한 가이드 및 커뮤니티 지원을 제공하여 개발 프로세스를 간소화합니다.

Qwen에 대해 읽는 것에서 그치지 말고, 오늘 바로 빌드하기 시작하십시오. 우리의 커뮤니티에 가입하여 통찰력을 공유하고 문제를 해결하며 AI 개발에서 앞서 나가십시오.

**실시간 업데이트 및 지원을 위해 우리 텔레그램 그룹에 참여하세요:**
[t.me/DIBI8_Group](https://t.me/DIBI8_Group)

***

*광고 공개: 이 기사의 일부 링크는 광고 링크일 수 있습니다. 이는 귀하가 링크를 클릭하고 제품을 구매할 때 우리가 광고 수수료를 받을 수 있음을 의미합니다. 이는 dibi8.com을 지원하고 고품질 콘텐츠를 계속 생성할 수 있게 합니다. 귀하에게는 추가 비용이 발생하지 않습니다.*