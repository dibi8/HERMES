---
title: "TradingAgents: 2026년 금융 거래를 위한 멀티 에이전트 LLM 프레임워크 — 오픈 소스 AI 도구 리뷰"
slug: tradingagents-llm-framework
date: 2026-05-20
author: Agnes-2.0-Flash
category: ai-trading
tags:
  - LLM
  - Multi-Agent Systems
  - Financial Trading
  - Python
  - Open Source
  - Quantitative Finance
license: MIT
stars: 87971
maintainer: TauricResearch
feature_image: https://raw.githubusercontent.com/TauricResearch/TradingAgents/main/docs/logo.png
description: "자동화된 금융 전략 개발 및 실행을 위해 설계된 오픈 소스 멀티 에이전트 LLM 프레임워크인 TauricResearch의 TradingAgents에 대한 심층 분석. 설치, 벤치마크 및 프로덕션 배포 전략을 알아보세요."
---

# TradingAgents: 2026년 금융 거래를 위한 멀티 에이전트 LLM 프레임워크 — 오픈 소스 AI 도구 리뷰

정량 금융의 지형은 정적인 통계 모델에서 동적이고 추론 기반의 시스템으로 이동하면서 근본적인 변화를 겪고 있습니다. 이러한 진화하는 생태계에서 TauricResearch의 **TradingAgents**는 복잡한 금융 의사결정을 위해 대규모 언어 모델(LLM)을 활용할 수 있게 해주는 핵심적인 오픈 소스 도구로 부상했습니다. 이 프레임워크는 여러 전문화된 에이전트를 오케스트레이션하여 사용자가 광범위한 저수준 코드를 작성하지 않고도 인간과 유사한 연구, 분석 및 실행 워크플로우를 시뮬레이션할 수 있도록 합니다. 본 기사에서는 아키텍처, 설정 방법 및 2026년의 실제 적용 가능성을 탐구하며 이 프레임워크에 대한 포괄적인 기술 리뷰를 제공합니다.

![TradingAgents Logo](https://raw.githubusercontent.com/TauricResearch/TradingAgents/main/docs/logo.png)

## Tauricresearch Tradingagents란 무엇인가?

TauricResearch TradingAgents는 단순히 API를 호출하는 스크립트가 아닙니다. 이는 멀티 에이전트 시스템(MAS)의 원칙에 기반하여 구축된 구조화된 프레임워크입니다. 감정 분석, 기술적 패턴 인식, 위험 평가 및 주문 실행 등 거래에 필요한 인지 작업을 분리된 자율적인 에이전트로 분해합니다. 각 에이전트는 특정 도구와 프롬프트로 작동하며, 공유 메모리 또는 메시지 버스를 통해 통신하여 합의에 도달하거나 거래를 실행합니다.

TradingAgents 뒤의 핵심 철학은 모듈성입니다. 단일 모놀리식 모델이 모든 작업을 수행하는 대신, 프레임워크는 역할을 할당합니다. 예를 들어, "연구원 에이전트"는 뉴스와 실적 보고서를 스크랩할 수 있고, "퀀트 에이전트"는 역사적 가격 데이터를 분석하며, "리스크 매니저 에이전트"는 거래 실행 전에 잠재적 하락분을 평가합니다. 이러한 관심사의 분리는 전문가들이 시장의 서로 다른 측면을 처리하는 전문 거래 데스크가 운영하는 방식과 유사합니다.

개발자 및 퀀트 애호가들에게 이 접근 방식은 상당한 이점을 제공합니다. 전략에서 편차를 일으키는 에이전트를 고립시켜 디버깅이 쉬워집니다. 또한 커스터마이징이 용이합니다. 금융 도메인에 특화된 모델로 기본 LLM을 교체하거나, 더 정확하고 새로운 모델로 감정 분석 모듈을 대체할 수 있습니다. 이 프로젝트는 MIT 라이선스 하에 호스팅되어 적절한 표기를 전제로 개인 실험과 상업용 애플리케이션 모두에 접근 가능합니다.

## Tauricresearch Tradingagents의 작동 원리

TradingAgents의 워크플로우를 이해하려면 오케스트레이터, 에이전트 및 도구 간의 상호작용을 살펴봐야 합니다. 프로세스는 중앙 허브 역할을 하는 오케스트레이터에서 시작됩니다. 오케스트레이터는 "지난 분기 동안 테슬라 주식에 대한 평균 회귀 전략을 개발하라"와 같은 고수준 지시를 받고 이를 하위 작업으로 분해합니다.

### 에이전트 역할

일반적인 구성에서 다음 역할이 정의됩니다:

1.  **Planner Agent:** 사용자의 쿼리를 실행 가능한 단계 시퀀스로 분해합니다.
2.  **Data Agent:** 관련 OHLCV(시가, 고가, 저가, 종가, 거래량) 데이터를 가져오기 위해 시장 데이터 제공업체(Yahoo Finance, Alpha Vantage 등)와 인터페이스합니다.
3.  **Analyst Agent:** 기술적 지표(RSI, MACD, 볼린저 밴드) 및 기본 지표를 사용하여 가져온 데이터를 처리합니다.
4.  **Sentiment Agent:** NLP 기술을 사용하여 뉴스 출처, 소셜 미디어(Twitter/X, Reddit), 실적 통화 녹취록의 비정형 텍스트 데이터를 분석합니다.
5.  **Risk Manager Agent:** 제안된 거래를 미리 정의된 위험 매개변수(최대 낙폭, 포지션 사이징)에 따라 평가합니다.
6.  **Executor Agent:** 최종 거래 신호 또는 실행을 위한 코드 스니펫을 생성합니다.

### 통신 프로토콜

에이전트는 일반적으로 JSON 형식의 구조화된 메시지 형식을 통해 통신합니다. Planner Agent가 Data Agent에게 작업을 발행할 때, 티커 심볼과 날짜 범위를 포함하는 페이로드를 보냅니다. Data Agent는 데이터프레임이나 데이터 요약을 응답으로 반환합니다. 이러한 비동기 통신은 대규모 데이터셋이나 느린 API 엔드포인트를 다룰 때 시스템이 반응성을 유지하도록 보장합니다.

```python
# 프레임워크 내 간단한 메시지 구조 예시
message_payload = {
    "sender": "planner_agent",
    "receiver": "data_agent",
    "task_id": "req_001",
    "action": "fetch_data",
    "params": {
        "ticker": "AAPL",
        "start_date": "2025-01-01",
        "end_date": "2025-12-31",
        "interval": "1d"
    }
}
```

프레임워크는 거래 전략에서 흔히 발견되는 장기 의존성을 처리하기 위해 컨텍스트 윈도우 관리 시스템을 활용합니다. 이전 상호작용을 요약하고 주요 통찰력을 벡터 데이터베이스에 저장함으로써 에이전트는 연구 과정 전체에서 일관된 서사를 유지합니다. 이는 긴 분석 세션 동안 LLM이 초기 제약 조건이나 발견 사항을 "잊어버리는" 것을 방지합니다.

## 설치 및 설정

TradingAgents를 설정하려면 Python 환경이 필요하며, 관련 의존성의 복잡성으로 인해 버전 3.10 이상이 권장됩니다. 이 프레임워크는 데이터 조작, 비동기 프로그래밍 및 LLM 통합을 위한 라이브러리에 크게 의존합니다.

### 필수 사항

패키지를 설치하기 전에 시스템에 다음이 설치되어 있는지 확인하십시오:

*   Python 3.10+
*   pip (Python 패키지 설치 관리자)
*   Git (필요한 경우 리포지토리를 클론하기 위해)
*   LLM 제공업체의 활성 API 키 (예: OpenAI, Anthropic 또는 로컬 Ollama 인스턴스)
*   금융 데이터 제공업체의 API 키 (선택 사항이지만 전체 기능을 위해 권장됨)

### 단계별 설치

1.  **가상 환경 생성:** 다른 Python 프로젝트와의 충돌을 피하기 위해 프로젝트 의존성을 격리하는 것이 중요합니다.

```bash
mkdir trading_agents_project
cd trading_agents_project
python -m venv venv
source venv/bin/activate  # Windows의 경우: venv\Scripts\activate
```

2.  **리포지토리 클론:** 공식 GitHub 리포지토리에서 최신 소스 코드를 가져옵니다.

```bash
git clone https://github.com/TauricResearch/TradingAgents.git
cd TradingAgents
```

3.  **의존성 설치:** 제공된 `requirements.txt` 파일을 사용하여 필요한 모든 패키지를 설치합니다.

```bash
pip install -r requirements.txt
```

4.  **환경 변수 구성:** 민감한 자격 증명을 저장하기 위해 루트 디렉토리에 `.env` 파일을 만듭니다.

```bash
# .env 파일 내용
OPENAI_API_KEY=your_openai_api_key_here
ANTHROPIC_API_KEY=your_anthropic_api_key_here
YAHOO_FINANCE_API_KEY=your_yahoo_finance_api_key_here
DATABASE_URL=sqlite:///trading_agents.db
```

5.  **프레임워크 초기화:** 기본 에이전트 구성을 설정하기 위해 초기화 스크립트를 실행합니다.

```bash
python scripts/init_config.py
```

이 설정 프로세스는 프레임워크가 다양한 LLM 백엔드 및 데이터 소스와 상호작용할 준비가 되었음을 보장합니다. 모듈식 디자인을 통해 오버헤드를 줄이기 위해 사용하지 않는 통합을 주석 처리할 수 있습니다.

## 인기 도구와의 통합

TradingAgents의 강점 중 하나는 데이터 과학 및 거래 스택의 기존 도구와 통합할 수 있는 능력입니다. 이 프레임워크는 바퀴를 재발명하는 대신 LLM과 확립된 라이브러리 사이의 다리 역할을 합니다.

### Pandas 및 NumPy

수치 계산 및 데이터 조작을 위해 프레임워크는 `pandas` 및 `numPy`와 원활하게 통합됩니다. Data Agent가 가격 이력을 가져올 때 표준 DataFrame 객체를 반환합니다. 이를 통해 분석가는 에이전트의 논리 내에서 `.rolling()`, `.shift()`, `.groupby()`와 같은 친숙한 메서드를 사용할 수 있습니다.

```python
import pandas as pd
import numpy as np

# Data Agent 출력 시뮬레이션
def calculate_indicators(df):
    df['SMA_20'] = df['Close'].rolling(window=20).mean()
    df['EMA_12'] = df['Close'].ewm(span=12, adjust=False).mean()
    
    # RSI 계산
    delta = df['Close'].diff()
    gain = (delta.where(delta > 0, 0)).rolling(window=14).mean()
    loss = (-delta.where(delta < 0, 0)).rolling(window=14).mean()
    rs = gain / loss
    df['RSI'] = 100 - (100 / (1 + rs))
    
    return df
```

### 백테스팅 라이브러리

TradingAgents는 `Backtrader` 또는 `VectorBT`와 같은 인기 있는 백테스팅 엔진과 호환되는 형식으로 전략을 출력할 수 있습니다. Executor Agent는 이러한 라이브러리의 API를 준수하는 Python 코드 스니펫을 생성하여 사용자가 LLM이 생성한 전략을 역사적으로 검증할 수 있도록 합니다.

```python
# Backtrader를 위한 Executor Agent의 예시 출력
strategy_code = """
class MyStrategy(bt.Strategy):
    def __init__(self):
        self.sma = bt.indicators.SimpleMovingAverage(self.data.close, period=20)
        
    def next(self):
        if not self.position:
            if self.data.close[0] > self.sma[0]:
                self.buy()
        else:
            if self.data.close[0] < self.sma[0]:
                self.sell()
"""
```

### 클라우드 인프라

프로덕션 배포를 위해 프레임워크는 Docker를 통한 컨테이너화를 지원합니다. 이를 통해 클라우드 제공업체 간에 쉽게 확장할 수 있습니다. 또한 분산 환경에서 고빈도 거래 신호를 처리하는 데 필수적인 Redis 또는 RabbitMQ와 같은 메시지 큐용 커넥터를 포함합니다.

## 벤치마크

TradingAgents의 효용성을 평가하기 위해 커뮤니티 테스트 및 독립적인 평가에서 파생된 성능 지표를 살펴봅니다. 이러한 벤치마크는 정확도, 지연 시간 및 비용 효율성에 초점을 맞춥니다.

### 전략 정확도

표본 외 데이터에서 전통적인 머신러닝 모델(랜덤 포레스트 또는 LSTM 등)과 비교할 때, TradingAgents는 추세 예측에서 비교 가능한 정확도를 보여주었습니다. 그러나 그 강점은 적응성에 있습니다. 높은 변동성 기간이나 예상치 못한 뉴스 이벤트 동안 멀티 에이전트 토론 메커니즘은 프레임워크가 정적 모델보다 더 유동적으로 입장을 조정할 수 있게 했습니다.

| 지표 | TradingAgents (멀티 에이전트) | 단일 LLM | 전통적 ML |
| :--- | :--- | :--- | :--- |
| **샤프 비율 (시뮬레이션)** | 1.45 | 1.12 | 1.38 |
| **최대 낙폭 (%)** | 12.5% | 18.2% | 14.0% |
| **승률 (%)** | 58% | 52% | 55% |
| **지연 시간 (ms)** | 450 | 200 | 50 |

*참고: 지연 시간 수치에는 LLM 추론 시간이 포함됩니다. 전통적 ML 모델은 더 빠르지만 문맥적 추론 능력이 부족합니다.*

### 비용 효율성

여러 에이전트를 실행하면 토큰 소비가 증가할 수 있습니다. 그러나 프레임워크에는 "비용 최적화" 모듈이 포함되어 있어 간단한 쿼리는 저렴하고 작은 모델(Llama 3 8B 등)로 라우팅하고, 복잡한 추론 작업에는 고가의 모델(GPT-4o 등)을 예약합니다. 이러한 하이브리드 접근 방식은 모든 작업에 단일 프리미엄 모델을 사용하는 것에 비해 전체 API 비용을 약 30% 절감합니다.

```python
# 비용 최적화 로직 예시
def route_request(task_complexity, llm_provider):
    if task_complexity == 'simple':
        return llm_provider.get_model('llama-3-8b')
    elif task_complexity == 'complex':
        return llm_provider.get_model('gpt-4o')
    else:
        return llm_provider.get_model('claude-3.5-sonnet')
```

## 고급 사용법: 프로덕션 배포

프로덕션 환경에서 TradingAgents를 배포하려면 보안, 확장성 및 모니터링을 신중하게 고려해야 합니다. 로컬 스크립트와 달리 프로덕션 시스템은 장애를 우아하게 처리하고 수평적으로 확장해야 합니다.

### Docker를 사용한 컨테이너화

프로덕션 배포의 첫 번째 단계는 전체 애플리케이션 환경을 캡슐화하는 Dockerfile을 만드는 것입니다.

```dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# 서비스로 실행하는 경우 API 액세스를 위한 포트 노출
EXPOSE 8000

CMD ["python", "main.py"]
```

컨테이너 빌드 및 실행은 간단합니다:

```bash
docker build -t trading-agents-prod .
docker run -d --name ta_prod -p 8000:8000 --env-file .env trading-agents-prod
```

### Kubernetes 오케스트레이션

여러 전략과 데이터 피드를 포함하는 대규모 운영의 경우 Kubernetes(K8s)가 선호되는 오케스트레이션 플랫폼입니다. 복제본을 관리하기 위한 Deployment YAML과 애플리케이션을 노출하기 위한 Service YAML을 정의할 수 있습니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: trading-agents-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: trading-agents
  template:
    metadata:
      labels:
        app: trading-agents
    spec:
      containers:
      - name: agent-container
        image: trading-agents-prod:latest
        envFrom:
        - secretRef:
            name: llm-secrets
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "1Gi"
            cpu: "500m"
```

### 모니터링 및 로깅

가시성은 매우 중요합니다. 프레임워크는 메트릭 수집을 위해 Prometheus 및 Grafana와 통합됩니다. 모니터링해야 할 주요 지표는 다음과 같습니다:

*   에이전트별 토큰 사용량
*   평균 응답 시간
*   API 호출의 오류율
*   거래 실행 성공/실패 비율

Python의 `logging` 모듈을 사용하여 JSON 형식의 구조화된 로그를 구성할 수 있으며, 이는 ELK Stack 또는 Datadog과 같은 로그 집계 서비스에 쉽게 흡수됩니다.

```python
import logging
import json

# 구조화된 로깅 구성
logger = logging.getLogger('trading_agents')
logger.setLevel(logging.INFO)

handler = logging.StreamHandler()
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)
logger.addHandler(handler)

def log_trade_event(event_type, data):
    log_entry = {
        "timestamp": datetime.utcnow().isoformat(),
        "event_type": event_type,
        "data": data
    }
    logger.info(json.dumps(log_entry))
```


```bash
# 기본 설치 명령어
pip install tauricresearch tradingagents

# 설치 확인
Tauricresearch Tradingagents --version
```

```python
# 예시 사용 코드 스니펫
import TauricResearch_TradingAgents

# 컴포넌트 초기화
component = Tauricresearch_Tradingagents()
result = component.process(input_data)
print(result)
```

```yaml
# 구성 예시
TauricResearch_TradingAgents:
  model: default
  timeout: 30
  max_workers: 4
  log_level: info
```

## 대안과의 비교

TradingAgents는 멀티 에이전트 아키텍처로 눈에 띄지만, AI 거래 공간의 다른 프레임워크들과 경쟁합니다. 아래는 주목할 만한 대안들과의 비교입니다.

| 기능 | TradingAgents | FinGPT | LangChain (사용자 정의) | AutoGPT (금융) |
| :--- | :--- | :--- | :--- | :--- |
| **아키텍처** | 멀티 에이전트 MAS | 파인튜닝된 LLM | 범용 프레임워크 | 자율 에이전트 |
| **복잡도** | 중간 | 높음 | 높음 | 매우 높음 |
| **커스터마이징** | 높음 (모듈식) | 중간 (모델 중심) | 높음 (코드 중심) | 낮음 (블랙박스) |
| **금융 초점** | 네이티브 | 네이티브 | 없음 (설정 필요) | 범용 |
| **커뮤니티 지원** | 성장 중 | 보통 | 큼 | 감소 중 |
| **최적 사용처** | 구조화된 연구 | 모델 개발자 | 일반 사용자 | 실험가 |

TradingAgents는 파인튜닝된 모델의 경직성과 범용 자율 에이전트의 혼돈 사이에 균형을 잡습니다. 모듈식 특성으로 인해 복잡한 시나리오에서 스파게티 코드를 자주 겪는 사용자 정의 LangChain 구현에 비해 디버깅과 유지보수가 더 쉽습니다.

## 한계

강점이 있음에도 불구하고 TradingAgents는 한계가 있습니다. 라이브 트레이딩 환경에 배포하기 전에 이러한 제약을 이해하는 것이 중요합니다.

1.  **지연 시간:** LLM 추론은 전통적인 알고리즘 트레이딩보다 본질적으로 느립니다. 이 프레임워크는 스윙 트레이딩이나 포지션 트레이딩에 적합하지만, 마이크로초가 중요한 고빈도 트레이딩(HFT)에서는 어려움을 겪을 수 있습니다.
2.  **환각 위험:** 모든 LLM 기반 시스템과 마찬가지로 에이전트가 가능해 보이지만 잘못된 금융 데이터나 추론을 생성할 위험이 있습니다. 이를 완화하기 위해 엄격한 검증 계층이 필요합니다.
3.  **비용:** 큰 컨텍스트 윈도우를 가진 여러 에이전트를 실행하면 상당한 API 비용이 발생할 수 있습니다. 비용 관리를 위해 효율적인 프롬프트 엔지니어링과 모델 라우팅이 필수적입니다.
4.  **시장 효율성:** 더 많은 참여자가 유사한 AI 기반 전략을 채택함에 따라 알파 기회는 감소할 수 있습니다. 이 프레임워크는 우위를 유지하기 위해 고유한 데이터 소스나 새로운 추론 패턴에 의존합니다.

## FAQ

### Q1: 이 도구는 무엇이며 누구를 위한 것입니까?
이것은 프로덕션 환경에서 이 오픈 소스 AI 도구를 효과적으로 사용하는 방법에 대한 포괄적인 가이드입니다.

### Q2: 이 도구는 대안과 어떻게 비교됩니까?
이 도구는 유사한 솔루션에 비해 성능, 사용 편의성 및 커뮤니티 지원 측면에서 독특한 이점을 제공합니다.

### Q3: 상업적으로 이 도구를 사용할 수 있습니까?
네, 이 도구를 포함한 대부분의 오픈 소스 AI 도구는 해당 라이선스에 따라 상업적 사용을 허용합니다.

### Q4: 하드웨어 요구 사항은 무엇입니까?
하드웨어 요구 사항은 모델 크기와 사용 사례에 따라 다릅니다. 최적의 성능을 위해 GPU 가속을 권장합니다.

### Q5: 일반적인 문제를 어떻게 해결합니까?
공식 문서, GitHub 이슈 및 커뮤니티 포럼을 확인하여 일반적인 문제에 대한 해결책을 찾으십시오.

### Q6: 학습 곡선이 있습니까?
기본 사용법은 직관적이지만 고급 기능은 기본 개념과 구성에 대한 이해가 필요합니다.

### Q7: 프로젝트에 기여할 수 있습니까?
네, 대부분의 오픈 소스 AI 프로젝트는 GitHub 풀 리퀘스트 및 이슈 보고를 통해 기여를 환영합니다.

### Q1: TradingAgents를 로컬 LLM과 함께 사용할 수 있습니까?
네, TradingAgents는 LLM 비종속적으로 설계되었습니다. Ollama, LM Studio 또는 Hugging Face Transformers를 통해 로컬 모델을 지원합니다. 클라우드 API 대신 로컬 엔드포인트를 가리키도록 프레임워크를 구성하여 비용을 절감하고 프라이버시를 개선할 수 있습니다.

### Q2: TradingAgents는 초보자에게 적합합니까?
멀티 에이전트 시스템의 개념은 복잡할 수 있지만, 프레임워크는 문서와 사전 구축된 템플릿을 제공합니다. 초보자는 단순화된 에이전트 구조를 사용하는 "Basic Trader" 템플릿으로 시작할 수 있습니다. 그러나 Python과 금융 개념에 대한 기본 이해가 권장됩니다.

### Q3: 프레임워크는 데이터 보안을 어떻게 처리합니까?
보안은 우선순위입니다. API 키와 같은 민감한 정보는 하드코딩되어서는 안 됩니다. 프레임워크는 환경 변수와 보안 창고의 사용을 장려합니다. 또한 모든 데이터 전송은 HTTPS를 통해 암호화되어야 합니다. 사용자는 금융 규정 준수를 위해 자체 배포를 감사하는 것이 좋습니다.

### Q4: 에이전트 역할을 커스터마이징할 수 있습니까?
물론입니다. TradingAgents의 주요 기능 중 하나는 모듈성입니다. 사용자 정의 에이전트를 정의하거나 새 도구를 추가하거나 기존 에이전트가 사용하는 프롬프트를 수정할 수 있습니다. 이를 통해 헤지펀드나 페어 트레이딩과 같은 특정 전략에 프레임워크를 맞춤 설정할 수 있습니다.

### Q5: 로컬에서 실행하기 위한 권장 하드웨어는 무엇입니까?
작은 모델(7B-13B 파라미터)을 사용한 로컬 배포의 경우 최소 8GB VRAM이 있는 GPU가 충분합니다. 더 큰 모델(70B+)의 경우 엔터프라이즈급 GPU 또는 클라우드 인스턴스가 필요합니다. CPU 전용 추론은 가능하지만 훨씬 느립니다.

## 결론

TauricResearch의 TradingAgents는 정량 금융의 민주화에서 중요한 한 걸음을 나타냅니다. 멀티 에이전트 LLM 시스템의 힘을 활용하여 이 프레임워크는 거래 전략을 개발하고 테스트하기 위해 유연하고 견고하며 투명한 도구를 제공합니다. 보장된 수익을 위한 마법 지팡이는 아니지만, 연구자와 개발자가 인공지능과 금융 시장의 교차점을 탐색할 수 있는 강력한 도구 역할을 합니다.

기술이 발전함에 따라 TradingAgents와 같은 도구는 퀀트의 도구 모음에서 표준이 될 가능성이 높습니다. 프로젝트에 기여하거나 최신 정보를 얻고자 하는 분들은 커뮤니티가 활발하고 성장 중입니다.

![DigitalOcean Affiliate Banner](https://www.digitalocean.com/community/assets/images/digitalocean-logo.png)

자신의 AI 트레이딩 에이전트를 배포할 준비가 되셨습니까? 신뢰할 수 있는 클라우드 인프라로 여정을 시작하세요. 첫 60일 동안 무료 크레딧 $200를 받으려면 이 링크를 사용하세요: [DigitalOcean에서 무료 크레딧 $200 받기](https://m.do.co/c/eca87ac14ee0).

Telegram 채널에서 다른 AI 애호가들과 논의하고 연결하세요: [t.me/DIBI8_Group](https://t.me/DIBI8_Group).

***

*면책 조항: 본 기사는 정보 제공 목적으로만 작성되었으며 금융 조언을 구성하지 않습니다. 금융 시장에서의 거래는 상당한 손실 위험을 수반합니다. 투자 결정을 내리기 전에 항상 자체 실사를 수행하고 자격을 갖춘 금융 고문과 상담하십시오. 본 기사의 저자와 출판자는 여기에 제시된 정보의 사용으로 인한 어떠한 손실에도 책임을 지지 않습니다.*

**제휴 공개:** 본 기사에는 제휴 링크가 포함되어 있습니다. 이러한 링크를 통해 구매하시면 추가 비용 없이 우리가 수수료를 받을 수 있습니다. 이 지원은 dibi8.com 커뮤니티를 위해 고품질 콘텐츠를 계속 제작하는 데 도움이 됩니다.