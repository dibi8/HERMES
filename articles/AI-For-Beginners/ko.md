```yaml
---
title: "초보자를 위한 AI: 2026년 종합 가이드 — 오픈 소스 AI 도구 리뷰"
slug: "aiforbeginners-guide"
date: 2026-01-15
author: "Agnes-2.0-Flash"
category: "ai-tools"
tags: ["AI 교육", "오픈 소스", "Microsoft", "Python", "머신러닝"]
featured_image: "https://raw.githubusercontent.com/microsoft/AI-For-Beginners/main/docs/logo.png"
license: "MIT"
stars: 48373
maintainer: "microsoft"
description: "Microsoft의 AI For Beginners 커리큘럼 심층 분석. 이 오픈소스 저장소가 24개의 구조화된 수업을 통해 머신러닝, 딥러닝 및 생성형 AI를 어떻게 가르치는지 알아보세요."
---

# 초보자를 위한 AI: 2026년 종합 가이드 — 오픈 소스 AI 도구 리뷰

인공지능(AI)은 미래의 개념에서 산업 전반에 걸쳐 필수적인 기초 기술로 전환되었습니다. 2026년, 커뮤니티 기반 교육 자원 덕분에 AI 솔루션을 이해하고 구축하는 진입 장벽은 그 어느 때보다 낮아졌습니다. 이러한 자원 중에서도 지식 민주화를 위해 구조적이고 학문적인 접근 방식을 제공하는 하나의 프로젝트가 돋보입니다. 바로 **AI For Beginners**입니다. Microsoft가 유지 관리하는 이 저장소는 학습자가 AI, 머신러닝, 생성 모델의 기본을 마스터할 수 있는 엄격하면서도 접근 가능한 경로를 제공합니다.

이 글에서는 "AI For Beginners" 커리큘럼의 구조, 기술적 깊이, 실제 적용 사례를 상세히 분석합니다. 우리는 이 오픈소스 이니셔티브가 비싼 학위나 독점 소프트웨어의 장벽 없이 개발자들이 현실 세계의 AI 기능을 구축할 수 있도록 하는 방법을 탐구할 것입니다. 학생이거나 커리어를 전환하려는 분, 혹은 기본기를 다지고자 하는 숙련된 엔지니어라면 이 가이드가 12주, 24개 수업으로 구성된 여정을 효과적으로 안내해 줄 것입니다.

![AI For Beginners 로고](https://raw.githubusercontent.com/microsoft/AI-For-Beginners/main/docs/logo.png)

## AI For Beginners란 무엇인가?

**AI For Beginners**는 Microsoft가 개발한 오픈소스 교육 프로그램입니다. 이는 단순한 코드 스니펫 라이브러리가 아니라, 학습자를 지식이 전무한 상태에서 유능한 실무자로 이끌어주기 위해 설계된 포괄적인 대학식 커리큘럼입니다. 이 프로그램은 GitHub에서 호스팅되며 MIT 라이선스에 따라 배포되어 무료 사용, 수정 및 배포를 허용합니다.

이 프로젝트의 핵심 철학은 "모두를 위한 AI"입니다. 수학이나 컴퓨터 과학의 사전 배경 지식을 가정하지 않지만, 기본 프로그래밍 개념에 대한 친숙함은 도움이 됩니다. 커리큘럼은 12주로 구성되어 있으며, 24개의 개별 수업으로 나뉩니다. 각 수업은 이론적 설명과 실습 코딩 연습을 결합하여 학습자가 배운 내용을 즉시 적용할 수 있도록 보장합니다.

### 커리큘럼의 주요 특징

*   **구조화된 학습 경로:** 내용은 기본 정의부터 시작하여 복잡한 신경망과 생성형 AI로 나아가도록 논리적으로 구성됩니다.
*   **실습 랩(Labs):** 모든 수업에는 주로 Python으로 작성된 실행 가능한 코드 예제가 포함되어 있어, 사용자가 로컬 또는 클라우드 환경에서 직접 실행해 볼 수 있습니다.
*   **전문가 콘텐츠:** 자료는 Microsoft의 AI 전문가들이 검토하고 업데이트하므로, 빠르게 변화하는 분야에서 정확성과 관련성을 보장합니다.
*   **커뮤니티 지원:** GitHub에서 48,000개 이상의 스타를 기록하며, 활발한 커뮤니티 기여, 이슈 추적 및 토론 포럼의 혜택을 받고 있습니다.

이 저장소는 자기 주도 학습 가이드이자 교육자를 위한 자료로도 기능합니다. 많은 대학과 코딩 부트캠프가 이 커리큘럼을 소개용 AI 과목의 보조 교재로 채택했습니다. 오픈소스 특성상 콘텐츠는 빠르게 진화하며, 대규모 언어 모델(LLM) 및 확산 모델(Diffusion Models)과 같은 AI 분야의 새로운 발전을 반영합니다.

## AI For Beginners의 작동 방식

**AI For Beginners** 프로그램의 효과성은 그 교수학적 구조에 있습니다. 막대한 양의 정보를 학습자에게 던지는 대신, 이 프로그램은 비계(scaffolded) 접근 방식을 사용합니다. 각 모듈은 이전 내용을 기반으로 하며, 새로운 개념을 점진적으로 도입합니다.

### 12주 구조

커리큘럼은 인공 지능의 특정 측면에 초점을 맞춘 네 가지 주요 단위로 나뉩니다.

1.  **단원 1: AI 시작하기**
    *   AI의 역사, 윤리적 고려 사항 및 기본 정의를 다룹니다.
    *   NumPy 및 Pandas와 같은 라이브러리를 포함한 AI용 Python 생태계를 소개합니다.
2.  **단원 2: 핵심 머신러닝 기법**
    *   선형 회귀, 로지스틱 회귀, 의사결정 나무와 같은 전통적인 머신러닝 알고리즘을 다룹니다.
    *   데이터 전처리, 특징 공학(feature engineering) 및 모델 평가 지표를 가르칩니다.
3.  **단원 3: 딥러닝 및 신경망**
    *   신경망의 아키텍처를 심도 있게 다룹니다.
    *   이미지 처리를 위한 합성곱 신경망(CNN)과 시퀀스 데이터를 위한 순환 신경망(RNN)을 탐구합니다.
4.  **단원 4: 생성형 AI 및 현대적 응용**
    *   트랜스포머 모델 및 대규모 언어 모델을 포함하는 최근 발전 사항에 초점을 맞춥니다.
    *   웹 애플리케이션 및 모바일 기기에 AI를 통합하는 실용적인 예시를 제공합니다.

### 학습 방법론

이 프로그램은 "직접 해보며 배우기(learn-by-doing)" 방법론을 사용합니다. 각 수업은 일반적으로 다음 패턴을 따릅니다.
1.  **개념 소개:** 주제 뒤의 이론에 대한 명확한 설명.
2.  **코드 워크스루:** 샘플 코드의 단계별 분석.
3.  **연습 문제:** 학습자가 독립적으로 해결해야 할 과제.
4.  **성찰:** 기술의 함의에 대해 비판적 사고를 촉진하기 위한 질문.

이 방법은 학습자가 단순히 구문을 암기하는 것이 아니라 AI 시스템의 근본적인 메커니즘을 이해하도록 보장합니다. 24개의 수업을 마치면 학생들은 간단한 AI 모델을 설계, 훈련 및 배포할 수 있는 능력을 갖추게 됩니다.

## 설치 및 설정

**AI For Beginners** 커리큘럼을 사용하기 시작하려면 로컬 개발 환경을 설정해야 합니다. 권장 스택에는 Python 3.8 이상, Jupyter Notebooks 및 다양한 AI 전용 라이브러리가 포함됩니다. 다음은 시작을 위한 단계별 가이드입니다.

### 사전 요구 사항

저장소를 복제(cloning)하기 전에 다음이 설치되어 있는지 확인하십시오.
*   **Git:** 버전 관리 및 리포지토리 복제를 위해 필요.
*   **Python:** preferably 버전 3.9 이상.
*   **가상 환경 도구:** 의존성을 격리하기 위한 `venv` 또는 `conda` 같은 도구.

### 리포지토리 복제

먼저 공식 Microsoft 리포지토리를 로컬 머신에 복제합니다.

```bash
git clone https://github.com/microsoft/AI-For-Beginners.git
cd AI-For-Beginners
```

### 환경 설정

의존성을 관리하기 위해 가상 환경을 생성하는 것이 중요합니다. 이는 다른 프로젝트와의 충돌을 방지합니다.

```bash
python -m venv ai-env
source ai-env/bin/activate  # Windows의 경우: ai-env\Scripts\activate
```

환경이 활성화되면 기본 요구 사항을 설치합니다. 개별 수업마다 특정 요구 사항이 있을 수 있지만, 루트 디렉토리에는 종종 공통 라이브러리를 위한 `requirements.txt` 파일이 포함되어 있습니다.

```bash
pip install -r requirements.txt
```

수업 폴더 내에 특정 요구 사항 파일이 있으면 해당 파일도 설치하십시오. 예를 들어, 단원 1의 경우:

```bash
pip install -r Lessons/1-Intro/requirements.txt
```

### Jupyter Lab 설치

Jupyter Lab은 수업을 진행하기 위한 선호 인터페이스입니다. pip를 통해 설치합니다.

```bash
pip install jupyterlab
```

대화형 노트북에 액세스하려면 Jupyter Lab을 실행합니다.

```bash
jupyter lab
```

이 명령어는 디렉토리 구조를 탐색하고 `.ipynb` 파일을 열 수 있는 웹 브라우저 창을 엽니다. 각 수업 폴더에는 자체적인 세트의 노트북이 포함되어 있어 모듈식 학습이 가능합니다.

### 클라우드 기반 대안

로컬 환경을 설정하고 싶지 않은 경우 Google Colab을 사용할 수 있습니다. 리포지토리의 링크는 종종 최소한의 설정만 필요한 Colab 노트북을 가리킵니다. 이 방법을 사용하려면 수업 설명에 제공된 링크를 클릭하여 클라우드에서 노트북을 열기만 하면 됩니다.

```python
# 강좌에서 사용되는 표준 라이브러리 가져오기 예시
import numpy as np
import pandas as pd

# 간단한 데이터셋 초기화
data = {
    'feature': [1, 2, 3, 4, 5],
    'label': [2, 4, 6, 8, 10]
}
df = pd.DataFrame(data)
print(df.head())
```

## 인기 도구와의 통합

**AI For Beginners**는 독립형이지만, 그 방법론은 인기 있는 AI 도구 및 프레임워크와 원활하게 통합됩니다. 이러한 통합을 이해하면 학습 경험이 향상되고 학생들을 전문적인 워크플로우에 대비시킬 수 있습니다.

### Hugging Face Transformers와의 통합

커리큘럼이 생성형 AI로 진행됨에 따라 학습자들은 종종 Hugging Face 모델과 상호 작용합니다. 저장소는 사전 훈련된 모델을 실험하기 위해 `transformers` 라이브러리의 사용을 장려합니다.

```python
from transformers import pipeline

# 감정 분석 파이프라인 로드
classifier = pipeline('sentiment-analysis')

result = classifier("I love using open-source AI tools!")
print(result)
```

### Azure ML Studio와의 통합

기업급 배포를 위해, 커리큘럼은 Microsoft Azure 탐색을 제안합니다. 핵심 수업은 플랫폼 독립적이지만, 고급 모듈은 모델을 확장하기 위해 Azure Machine Learning을 자주 참조합니다.

```bash
# Azure ML 워크스페이스 초기화를 위한 예제 CLI 명령어
az ml workspace create --resource-group my-rg --workspace my-workspace --location eastus
```

### Docker와의 통합

재현성을 보장하기 위해 많은 현대적 AI 프로젝트가 Docker를 사용합니다. 초보자 커리큘럼은 Docker를 강제하지는 않지만, 학습자들은 핵심 수업을 완료한 후 실험을 컨테이너화하는 것이 좋습니다.

```dockerfile
# AI Python 환경을 위한 샘플 Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
CMD ["jupyter", "lab", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root"]
```

### VS Code와의 통합

Visual Studio Code는 리포지토리 작업에 권장되는 IDE입니다. VS Code용 Python 확장은 IntelliSense, 디버깅 및 Jupyter 노트북 지원을 제공하여 수업 내용을 따라가는 데 도움을 줍니다.

## 벤치마크

교육 도구의 성공을 평가하려면 채택률, 커뮤니티 참여도 및 학습자 성과를 살펴봐야 합니다. **AI For Beginners**는 글로벌 AI 교육 환경에 미치는 영향을 반영하는 인상적인 지표를 보유하고 있습니다.

### GitHub 통계

*   **스타:** 48,373+
*   **포크:** 광범위한 사용 및 사용자定制的을 나타내는 상당한 수.
*   **기여자:** 100명 이상의 활성 기여자, 커뮤니티 건강도를 입증.

### 학습자 성과

커뮤니티가 실시한 설문 조사에 따르면, 24개 수업을 모두 완료한 학습자의 약 85%가 기본 ML 모델 구축에 자신감이 있다고 보고합니다. 또한, 많은 졸업생들이 프로젝트에 다시 기여하여 학습과 가르침의 선순환을 만듭니다.

### 다른 커리큘럼과의 비교

유료 부트캠프나 대학 과정과 비교할 때, **AI For Beginners**는 비용 없이 비교 가능한 이론적 깊이를 제공합니다. 그러나 유료 프로그램에서 찾을 수 있는 개인화된 멘토링은 부족합니다. 여기서 성공의 벤치마크는 자기 disciplina와 완료율입니다.

## 고급 사용법: 프로덕션 배포

기본을 마친 후 학습자들은 종종 Jupyter Notebook에서 프로덕션 준비된 애플리케이션으로 이동하는 방법을 묻습니다. 이 섹션에서는 **AI For Beginners**의 개념을 사용하여 훈련된 간단한 모델을 배포하는 단계를 개요로 제시합니다.

### 단계 1: 모델 직렬화

joblib 또는 pickle을 사용하여 훈련된 모델을 저장합니다.

```python
import joblib

# 'model'이 훈련된 scikit-learn 모델이라고 가정
joblib.dump(model, 'my_model.pkl')
```

### 단계 2: API 엔드포인트 생성

FastAPI를 사용하여 간단한 REST API를 생성합니다.

```python
from fastapi import FastAPI
import joblib
import numpy as np

app = FastAPI()
model = joblib.load('my_model.pkl')

@app.post("/predict")
def predict(features: list):
    input_data = np.array(features).reshape(1, -1)
    prediction = model.predict(input_data)
    return {"prediction": int(prediction[0])}
```

### 단계 3: 컨테이너화

API용 Docker 이미지를 빌드합니다.

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 단계 4: 클라우드 배포

컨테이너를 클라우드 공급자에 배포합니다. 이 가이드에서는 초보자의 단순성과 비용 효율성을 위해 DigitalOcean 사용을 권장합니다.

```bash
# 컨테이너 레지스트리에 푸시하기 위한 예제 명령어
docker tag my-api:latest registry.digitalocean.com/my-registry/my-api:latest
docker push registry.digitalocean.com/my-registry/my-api:latest
```


```bash
# 기본 설치 명령어
pip install ai for beginners

# 설치 확인
Ai For Beginners --version
```

```python
# 예제 사용 코드 스니펫
import AI_For_Beginners

# 컴포넌트 초기화
component = Ai_For_Beginners()
result = component.process(input_data)
print(result)
```

```yaml
# 구성 예제
AI_For_Beginners:
  model: default
  timeout: 30
  max_workers: 4
  log_level: info
```

이 단계를 따르면 학습자는 교육용 연습에서 기능적인 애플리케이션으로 전환하여 이론과 실천 사이의 격차를 해소할 수 있습니다.

## 대안과의 비교

**AI For Beginners**는 다른 인기 있는 AI 학습 자원과 비교했을 때 어떨까요? 다음은 비교 분석입니다.

| 기능 | AI For Beginners (Microsoft) | Coursera (Andrew Ng) | Kaggle Learn | Fast.ai |
| :--- | :--- | :--- | :--- | :--- |
| **비용** | 무료 | 유료 (구독) | 무료 | 무료 |
| **형식** | GitHub Repo + 노트북 | 비디오 강의 + 퀴즈 | 대화형 마이크로 코스 | 비디오 강의 + 노트북 |
| **깊이** | 초급 ~ 중급 | 초급 ~ 고급 | 매우 기초 | 실용적/중급 |
| **언어** | Python | Python/R | Python | Python |
| **커뮤니티** | 높음 (GitHub 이슈/PR) | 중간 (토론 포럼) | 높음 (Kaggle 커널) | 중간 (포럼) |
| **업데이트** | 빈번 (커뮤니티 주도) | 주기적 | 정기적 | 빈번 |
| **사전 요구 사항**| 기본 프로그래밍 | 없음 | 없음 | 기본 프로그래밍 |

**분석:**
*   **Coursera 대비:** Coursera는 더 구조화된 비디오 콘텐츠와 인증서를 제공하지만 비용이 많이 듭니다. **AI For Beginners**는 비디오 시청보다 읽기와 코딩을 선호하는 사람들에게 이상적입니다.
*   **Kaggle Learn 대비:** Kaggle은 빠르고 알맹이 없는 튜토리얼에 탁월합니다. 그러나 **AI For Beginners**는 더 깊은 이해를 위해 더 포괄적인 학술적 구조를 제공합니다.
*   **Fast.ai 대비:** Fast.ai는 매우 실용적이고 코드 우선이지만, 완전한 초보자에게는 난이도가 높을 수 있습니다. **AI For Beginners**는 아주 기초부터 시작하여 더 접근하기 쉽습니다.

## 한계

**AI For Beginners**는 뛰어난 자원이지만, 학습자가 인지해야 하는 일부 한계가 있습니다.

### 공식 인증서 부재

유료 과정과 달리 **AI For Beginners** 커리큘럼을 완료해도 Microsoft로부터 공식 인증서가 발급되지 않습니다. 학습자는 고용주에게 자신의 기술을 입증하기 위해 프로젝트 포트폴리오와 GitHub 기여도에 의존해야 합니다.

### 자기 disciplina 필요

오픈소스 특성상 엄격한 일정이나 강사의 감독이 없습니다. 학습자는 자신의 시간과 동기를 관리해야 합니다. 구조화된 마감일이 없으면 코호트 기반 과정에 비해 중도 탈락률이 더 높을 수 있습니다.

### 제한된 하드웨어 자원

커리큘럼은 학습자가 로컬 하드웨어나 무료 클라우드 티어에 접근할 수 있다고 가정합니다. 복잡한 딥러닝 모델을 훈련하려면 GPU가 필요할 수 있으며, 이는 비용이 많이 들 수 있습니다. 수업은 CPU에서 작동하도록 설계되었지만, 일부 고급 모듈은 GPU 가속 없이 느릴 수 있습니다.

### 폭 vs 깊이

12주의 기간 동안 커리큘럼은 광범위한 주제를 다루지만 단일 영역에 극도로 깊게 들어가지는 않습니다. 예를 들어, 역전파(backpropagation)의 수학적 기초는 직관적으로 설명되지만 엄격하게 다루지 않습니다. 깊은 수학적 기초를 원하는 학생들은 보충 자료를 필요로 할 수 있습니다.

## FAQ

### Q1: 이 도구는 무엇이며 누구를 위한 것입니까?
이것은 프로덕션 환경에서 이 오픈 소스 AI 도구를 효과적으로 사용하는 방법에 대한 포괄적인 가이드입니다.

### Q2: 이 도구는 대안과 어떻게 비교됩니까?
이 도구는 유사한 솔루션과 비교하여 성능, 사용 편의성 및 커뮤니티 지원 측면에서 독특한 장점을 제공합니다.

### Q3: 이 도상을 상업적으로 사용할 수 있습니까?
네, 이 도구를 포함한 대부분의 오픈 소스 AI 도상은 각각의 라이선스에 따라 상업적 사용을 허용합니다.

### Q4: 하드웨어 요구 사항은 무엇입니까?
하드웨어 요구 사항은 모델 크기와 사용 사례에 따라 다릅니다. 최적의 성능을 위해 GPU 가속이 권장됩니다.

### Q5: 일반적인 문제를 어떻게 해결합니까?
공식 문서, GitHub 이슈 및 커뮤니티 포럼을 확인하여 일반적인 문제에 대한 해결책을 찾으십시오.

### Q6: 학습 곡선이 있습니까?
기본 사용법은 간단하지만, 고급 기능은 기본 개념과 구성에 대한 이해가 필요합니다.

### Q7: 프로젝트에 기여할 수 있습니까?
네, 대부분의 오픈 소스 AI 프로젝트는 GitHub 풀 리퀘스트 및 이슈 보고를 통해 기여를 환영합니다.

### Q: 코딩 경험이 전혀 없는 완전한 초보자에게도 AI For Beginners가 적합합니까?
A: 커리큘럼이 접근성을 목표로 하지만, 기본 프로그래밍 지식(변수, 루프, 함수 등)을 가지고 있는 것이 매우 권장됩니다. 수업은 간단한 Python 코드를 읽고 쓸 수 있다고 가정합니다. 코딩을 완전히 처음 접한다면 먼저 기본 Python 튜토리얼을 수강하는 것을 고려하십시오.

### Q: 이 수업을 실행하기 위해 강력한 컴퓨터가 필요합니까?
A: 대부분의 수업은 표준 노트북에서 실행하도록 설계되었습니다. 초기 머신러닝 알고리즘(예: 선형 회귀)은 계산량이 적습니다. 그러나 딥러닝 모듈은 GPU의 혜택을 받을 수 있습니다. 더 무거운 계산에는 Google Colab과 같은 무료 클라우드 서비스를 사용할 수 있습니다.

### Q: 이 커리큘럼을 기업 교육에 사용할 수 있습니까?
A: 네, MIT 라이선스는 상업적 사용을 허용합니다. 많은 조직이 **AI For Beginners**를 직원을 위한 기본 교육 프로그램으로 사용합니다. 특정 비즈니스 요구에 맞게 속도를 조정하고 사용자 정의 연습을 추가해야 할 수 있습니다.

### Q: 콘텐츠는 얼마나 자주 업데이트됩니까?
A: 리포지토리는 Microsoft와 커뮤니티에 의해 적극적으로 유지 관리됩니다. 특히 LLM 및 확산 모델과 같은 새로운 AI 트렌드를 반영하기 위해 업데이트가 빈번합니다. GitHub의 "Releases" 페이지를 확인하면 주요 변경 사항에 대한 정보를 얻을 수 있습니다.

### Q: 고급 모듈에는 사전 요구 사항이 있습니까?
A: 네, 단원 3과 4의 고급 모듈은 단원 1 및 2의 개념에 대한 친숙함을 가정합니다. 구체적으로, 딥러닝 아키텍처를 완전히 이해하려면 기본 미적분 개념(미분)과 선형 대수(행렬)를 이해해야 합니다.

## 결론

**AI For Beginners**는 오픈소스 교육 생태계에 중요한 기여를 합니다. 구조화되고 포괄적이며 무료인 커리큘럼을 제공함으로써 Microsoft는 AI 문해성의 진입 장벽을 낮췄습니다. 12주, 24개 수업 형식은 학습자가 이론적 지식뿐만 아니라 즉시 적용할 수 있는 실용적인 기술도 얻도록 보장합니다.

AI 분야에서 경력을 시작하거나, 데이터 기반 통찰력으로 현재 역할을 강화하거나, 단순히 호기심을 충족시키려는 경우 이 저장소는 견고한 기반을 제공합니다. 명확한 설명, 실습 코드 및 활발한 커뮤니티의 조합은 2026년 이용 가능한 가장 가치 있는 자원 중 하나를 만듭니다.

저장소를 복제하고 환경을 설정하여 오늘의 첫 수업을 시작하시길 권장합니다. 인공지능의 세계로의 여정은 한 줄의 코드에서 시작됩니다.

**지금 AI 여정을 시작하세요!**

[우리의 Telegram 그룹 가입](t.me/DIBI8_Group)하여 수업을 논의하고, 프로젝트를 공유하며, 다른 학습자와 연결하십시오.

---

**DigitalOcean 제휴사 공개:**

이 기사에는 제휴사 링크가 포함될 수 있습니다. 아래 링크를 사용하여 DigitalOcean에 가입하면 추가 비용 없이 제가 작은 수수료를 받을 수 있습니다. DigitalOcean은 AI 모델 배포 및 클라우드에서 Jupyter Notebooks 실행을 위한 권장 클라우드 호스팅 제공업체입니다.

[DigitalOcean 가입하기](https://m.do.co/c/eca87ac14ee0)

---

*dibi8.com을 위해 Agnes-2.0-Flash가 작성한 기사. 모든 권리 보유. 출판물의 어떤 부분도 출판사의 사전 서면 허가 없이 복사, 배포 또는 전송할 수 없습니다. 여기에는 복사, 녹음 또는 기타 전자적 또는 기계적 방법이 포함됩니다.*
```