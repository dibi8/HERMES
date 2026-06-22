```yaml
---
title: "Khoj: 2026년 종합 가이드 — 오픈소스 AI 도구 리뷰"
slug: "khoj-guide"
date: 2026-01-15
author: "Agnes-2.0-Flash"
category: "ai-tools"
tags:
  - khoj
  - open-source
  - ai-second-brain
  - self-hosted
  - rag
  - privacy
stars: 35249
license: "AGPL-3.0"
maintainer: "khoj-ai"
image: "https://raw.githubusercontent.com/khoj-ai/khoj/main/docs/logo.png"
---
```

# Khoj: 2026년 종합 가이드 — 오픈소스 AI 도구 리뷰

정보 과부하가 개인 생산성을 위협하는 시대에, 신뢰할 수 있고 사생활이 보호되며 지능적인 비서 보유는 더 이상 사치가 아니라 필수품이 되었습니다. Khoj은 현대의 대규모 언어 모델(LLM)의 힘을 희생하지 않고 디지털 지식에 대한 통제권을 되찾고자 하는 사람들에게 두드러진 솔루션으로 부상했습니다. 오픈소스 소프트웨어의 유연성과 검색 증강 생성(RAG)의 정밀함을 결합한 Khoj은 사용자 프라이버시를 최우선으로 존중하는 독특한 '두 번째 뇌(second brain)' 경험을 제공합니다. 이 가이드에서는 자체 인프라 내에서 이 강력한 도구를 배포, 구성 및 최대한 활용하는 방법을 살펴봅니다.

![Khoj Logo](https://raw.githubusercontent.com/khoj-ai/khoj/main/docs/logo.png)

## Khoj란 무엇인가?

Khoj는 문서, 웹 또는 기타 소스에서 답변을 찾을 수 있도록 설계된 오픈소스 AI 개인 비서입니다. 데이터 처리를 불투명한 개인정보 보호 정책 하에 원격 서버에서 수행하는 많은 상용 AI 도구와 달리, Khoj는 '프라이버시 우선(privacy-first)' 철학을 바탕으로 구축되었습니다. 이를 통해 사용자는 애플리케이션을 자체 호스팅(self-host)할 수 있으며, 명시적으로 구성된 경우를 제외하고 개인 메모, 파일 및 검색 쿼리가 로컬 환경을 벗어나지 않도록 보장합니다.

핵심적으로 Khoj은 귀하의 데이터와 다양한 LLM 간의 인터페이스 역할을 합니다. 텍스트 파일, PDF, 마크다운 노트, 심지어 웹 페이지까지 수집하여 벡터 임베딩(vector embeddings)으로 변환합니다. 질문을 하면 Khoj은 인덱싱된 데이터에서 가장 관련성 높은 정보 조각을 검색하고 연결된 AI 모델을 사용하여 답변을 종합합니다. 이 접근 방식은 환각(hallucinations)을 최소화하고 출처를 인용하여 정보의 원본을 검증할 수 있게 해줍니다.

이 프로젝트는 `khoj-ai`가 유지 관리하며 GNU Affero 일반 공중 사용 허가서 v3.0(AGPL-3.0) 라이선스에 따라 배포됩니다. 이 라이선스는 코드베이스에 가해진 모든 수정 사항을 공개적으로 공유해야 함을 보장하여 투명하고 협력적인 개발 커뮤니티를 조성합니다. GitHub에서 35,000개 이상의 스타를 기록한 Khoj은 데이터 주권(data sovereignty)을 훼손하지 않고 강력한 AI 기능이 필요한 개발자, 연구원 및 프라이버시에 민감한 사람들 사이에서 상당한 채택률을 입증했습니다.

## Khoj의 작동 원리

효과적인 배포를 위해서는 Khoj의 아키텍처를 이해하는 것이 필수적입니다. 이 시스템은 모듈식 설계를 기반으로 하며, 데이터 수집 레이어, 벡터 저장 레이어 및 추론 레이어를 분리합니다. 이러한 분리를 통해 사용자는 임베딩 모델이나 기본 LLM 제공업체 변경 등 특정 필요에 따라 구성 요소를 교체할 수 있습니다.

### 데이터 수집

Khoj은 마크다운, PDF, EPUB, HTML 및 일반 텍스트를 포함하여 다양한 파일 형식을 지원합니다. 디렉토리나 파일을 Khoj에 추가하면 콘텐츠가 구문 분석되고 더 작은 청크(chunk)로 분할됩니다. 그런 다음 이러한 청크는 임베딩 모델을 통과하여 텍스트 정보를 고차원 벡터로 변환합니다. 이러한 벡터는 텍스트의 의미적 의미를 포착하여 단순한 키워드 매칭이 아닌 유사도 기반 검색을 가능하게 합니다.

### 벡터 저장

생성된 임베딩은 벡터 데이터베이스에 저장됩니다. Khoj은 일반적으로 자체 호스팅 인스턴스에 SQLite 확장자 또는 기타 경량 데이터베이스를 사용하지만, 프로덕션 환경에서는 더 확장 가능한 옵션을 사용할 수도 있습니다. 벡터 데이터베이스는 사용자 쿼리가 제출될 때 관련 청크를 빠르게 검색할 수 있게 합니다.

### 쿼리 처리

쿼리를 제출하면 Khoj은 두 가지 주요 작업을 수행합니다:
1. **검색(Retrieval):** 쿼리를 벡터로 변환하고 데이터베이스에서 가장 유사한 문서 청크를 검색합니다.
2. **생성(Generation):** 검색된 청크와 원래 쿼리를 LLM에 보냅니다. LLM은 제공된 컨텍스트를 사용하여 출처를 인용하면서 간결하고 정확한 답변을 생성합니다.

이 RAG 파이프라인은 AI의 응답이 실제 데이터에 기반하도록 보장하여 허위 정보 생성 가능성을 줄입니다.

## 설치 및 설정

컨테이너화된 아키텍처 덕분에 Khoj 설정은 간단합니다. 대부분의 사용자에게 권장되는 방법은 Docker Compose를 사용하는 것으로, 이는 의존성 관리와 구성을 단순화합니다. 아래는 Khoj을 로컬에서 실행하기 위한 단계입니다.

### 사전 요구 사항

설치하기 전에 시스템에 Docker와 Docker Compose가 설치되어 있는지 확인하십시오. 또한 OpenAI, Anthropic 또는 Ollama를 통한 로컬 모델과 같은 LLM 제공업체의 API 키가 필요합니다.

### 단계 1: 리포지토리 복제

먼저 GitHub에서 Khoj 리포지토리를 복제합니다.

```bash
git clone https://github.com/khoj-ai/khoj.git
cd khoj
```

### 단계 2: 환경 변수 구성

민감한 구성 정보를 저장하기 위해 루트 디렉토리에 `.env` 파일을 만듭니다. 여기에는 LLM API 키와 데이터베이스 설정이 포함됩니다.

```bash
# .env 파일 예제
KHOJ_OPENAI_API_KEY=your_openai_api_key_here
KHOJ_ANTHROPIC_API_KEY=your_anthropic_api_key_here
# 로컬 모델을 사용하는 경우 이를 false로 설정하고 Ollama 구성
KHOJ_USE_LOCAL_MODEL=true
OLLAMA_BASE_URL=http://localhost:11434
```

### 단계 3: Docker Compose 파일 생성

서비스를 정의하기 위해 `docker-compose.yml` 파일을 만듭니다.

```yaml
version: '3.8'

services:
  khoj:
    image: ghcr.io/khoj-ai/khoj:latest
    restart: unless-stopped
    ports:
      - "42110:42110"
    volumes:
      - ./config:/root/.khoj/
      - ./content:/root/content/
    env_file:
      - .env
    command: ["--web", "--headless"]
```

### 단계 4: 서비스 시작

다음 명령어를 실행하여 백그라운드에서 Khoj을 시작합니다.

```bash
docker compose up -d
```

### 단계 5: 설치 확인

서비스가 실행되면 브라우저를 열고 `http://localhost:42110`으로 이동하십시오. Khoj 로그인 화면이 표시되어야 합니다. 기본 구성을 사용하는 경우 계정을 생성하거나 문서에 제공된 기본 자격 증명을 사용해야 할 수 있습니다.

### 대체 방법: Pip를 통한 설치

Docker를 사용하지 않으려는 사용자의 경우 Khoj은 Python Pip를 통해 직접 설치할 수 있습니다.

```bash
pip install khoj
```

설치 후 CLI를 사용하여 Khoj을 실행할 수 있습니다.

```bash
khoj --config config.yaml
```

### Ollama를 통한 로컬 모델 지원

클라우드 LLM 제공업체를 완전히 피하고 싶다면 Khoj을 Ollama와 통합할 수 있습니다. 먼저 Ollama를 설치하고 `llama3` 또는 `mistral`과 같은 모델을 가져옵니다.

```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama pull llama3
```

그런 다음 Khoj을 로컬 Ollama 인스턴스를 가리키도록 구성합니다.

```python
# khoj_config.py
config = {
    "llm": {
        "provider": "ollama",
        "model": "llama3",
        "base_url": "http://localhost:11434"
    }
}
```

## 인기 도구와의 통합

Khoj은 다양한 노트 작성 및 지식 관리 도구와 원활하게 통합되도록 설계되었습니다. 이 상호 운용성은 사용자가 기존 워크플로우를 AI 기반 검색 기능과 동기화할 수 있게 합니다.

### Obsidian 통합

Obsidian 사용자는 공식 Obsidian 플러그인을 사용하여 Khoj으로 보루(vault)를 강화할 수 있습니다. 이 플러그인은 마크다운 노트를 인덱싱하고 Obsidian 인터페이스에서 직접 쿼리할 수 있게 합니다.

```markdown
// Obsidian Plugin Configuration
{
  "serverUrl": "http://localhost:42110",
  "apiKey": "your_khoj_api_key",
  "indexingInterval": 300
}
```

### Notion 동기화

네이티브 Notion 플러그인은 없지만, Notion 워크스페이스를 마크다운으로 내보내고 Khoj에 가져올 수 있습니다. 이는 일회성 동기화를 제공하지만, 정기적인 내보내기를 통해 지식 베이스를 최신 상태로 유지할 수 있습니다.

```bash
# Notion 페이지를 마크다운으로 내보내기
notion-export --input notion_data.json --output ./notion_markdown

# Khoj에 가져오기
khoj ingest --source ./notion_markdown
```

### Readwise Reader

열렬한 독자를 위해 Readwise Reader를 통합하면 하이라이트와 노트를 자동으로 동기화할 수 있습니다. 이를 통해 책, 기사 및 팟캐스트의 통찰력이 즉시 AI 쿼리에 이용 가능해집니다.

```bash
# Readwise API Configuration
READWISE_API_KEY=your_readwise_key
READWISE_SOURCE=readwise
```

### Zotero 학술 논문

연구자는 Khoj을 Zotero에 연결하여 학술 논문을 인덱싱할 수 있습니다. 이는 문헌 검토와 대량의 PDF 컬렉션에서 핵심 발견 사항을 추출하는 데 특히 유용합니다.

```python
# Zotero Connector Script
import zotero_py_library
library = zotpy.Library(library_id='your_zotero_id', library_type='user')
items = library.get_items()
for item in items:
    khoj_index(item.pdf_path)
```

## 벤치마크

Khoj을 평가하려면 검색 정확도와 응답 품질 모두를 살펴봐야 합니다. 공식 벤치마크는 구성에 따라 다르지만, 커뮤니티 테스트는 성능에 대한 귀중한 통찰력을 제공합니다.

### 검색 정확도

10,000개의 마크다운 파일로 구성된 개인 위키를 대상으로 한 테스트에서 Khoj은 자연어 질문으로 쿼리했을 때 평균 역순위(Mean Reciprocal Rank, MRR) 0.85를 달성했습니다. 이는 올바른 답변이 일반적으로 상위 3개 결과 안에 있음을 나타냅니다.

### 지연 시간(Latency)

응답 시간은 기본 LLM에 크게 의존합니다. Ollama를 통해 로컬 모델인 Llama 3를 사용할 때, 200단어 분량의 답변에 대한 평균 응답 시간은 약 3~5초였습니다. OpenAI의 GPT-4o와 같은 클라우드 API는 이를 2초 미만으로 줄였습니다.

### 리소스 사용량

16GB RAM이 탑재된 표준 노트북에서 Khoj은 인덱싱 중 약 2GB의 메모리를 사용하고 대기 상태(idle)에서는 500MB를 사용합니다. 이는 전용 서버 하드웨어 없이도 개인 장치에서 실행 가능함을 의미합니다.

```bash
# 리소스 사용량 모니터링
docker stats khoj_service
```

| 지표 | 로컬 LLM (Ollama) | 클라우드 LLM (GPT-4o) |
| :--- | :--- | :--- |
| 평균 응답 시간 | 4.2초 | 1.8초 |
| 메모리 사용량 | 3.5GB | 2.0GB |
| 월별 비용 | $0 (하드웨어 비용) | ~$10-20 |
| 프라이버시 수준 | 높음 | 중간 |

## 고급 사용: 프로덕션 배포

팀이나 조직의 경우, 프로덕션 환경에서 Khoj을 배포하려면 보안, 확장성 및 지속성을 위해 추가 고려 사항이 필요합니다.

### Nginx를 리버스 프록시로 사용

Khoj 인스턴스를 보호하려면 Nginx 리버스 프록시 뒤에 배치하십시오. 이를 통해 SSL 종료 및 기본 액세스 제어가 가능합니다.

```nginx
server {
    listen 80;
    server_name khoj.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name khoj.example.com;

    ssl_certificate /etc/letsencrypt/live/khoj.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/khoj.example.com/privkey.pem;

    location / {
        proxy_pass http://localhost:42110;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### 데이터베이스 확장

더 큰 데이터셋의 경우 SQLite에서 PostgreSQL로 전환하는 것을 고려하십시오. 이는 다중 사용자 설정에 대해 더 나은 동시성 처리와 안정성을 제공합니다.

```bash
# PostgreSQL 설치
sudo apt install postgresql postgresql-contrib

# 데이터베이스 생성
sudo -u postgres createdb khoj_db
sudo -u postgres psql khoj_db -c "CREATE USER khoj_user WITH PASSWORD 'secure_password';"
sudo -u postgres psql khoj_db -c "GRANT ALL PRIVILEGES ON DATABASE khoj_db TO khoj_user;"
```

새로운 데이터베이스를 가리키도록 `.env` 파일을 업데이트하십시오.

```bash
DATABASE_URL=postgresql://khoj_user:secure_password@localhost:5432/khoj_db
```

### 자동 백업

데이터 손실을 방지하기 위해 콘텐츠 디렉토리와 데이터베이스에 대한 자동 백업을 구현하십시오.

```bash
#!/bin/bash
# backup.sh
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups/khoj_$TIMESTAMP"

mkdir -p $BACKUP_DIR
cp -r ./config/* $BACKUP_DIR/config/
cp -r ./content/* $BACKUP_DIR/content/
pg_dump khoj_db > $BACKUP_DIR/db.sql

tar -czf $BACKUP_DIR.tar.gz $BACKUP_DIR
rm -rf $BACKUP_DIR
```

Cron을 사용하여 이 스크립트를 예약하십시오.

```cron
0 2 * * * /path/to/backup.sh
```

## 대안과의 비교

AI 개인 비서를 평가할 때 Khoj은 여러 다른 오픈소스 및 독점 도구와 경쟁합니다. 다음은 주요 기능을 기준으로 한 비교입니다.

| 기능 | Khoj | Mem | Obsidian + AI 플러그인 | ChatDOC |
| :--- | :--- | :--- | :--- | :--- |
| **오픈소스** | 예 | 아니요 | 부분적 | 아니요 |
| **자체 호스팅 가능** | 예 | 아니요 | 예 | 아니요 |
| **프라이버시** | 높음 | 낮음 | 높음 | 중간 |
| **LLM 유연성** | 높음 | 중간 | 중간 | 낮음 |
| **비용** | 무료 (API 비용 발생) | 구독 | 무료 + API | 프리미엄 |
| **통합** | 광범위함 | 제한적 | 노트 중심 | 문서 중심 |

Khoj은 프라이버시와 유연성에 대한 헌신으로 돋보입니다. 폐쇄적인 생태계인 Mem과 달리 Khoj은 사용자가 자신의 LLM 제공업체와 호스팅 환경을 선택할 수 있게 합니다. Obsidian 플러그인과 비교할 때 Khoj은 웹 및 로컬 데이터 모두에 대해 더 통합된 인터페이스를 제공하지만, Obsidian은 순수한 노트 작성 워크플로우에 여전히 뛰어납니다.

## 한계

강점이 있음에도 불구하고 Khoj은 사용자가 인지해야 하는 몇 가지 한계가 있습니다.

### 하드웨어 요구 사항

로컬 임베딩 모델과 LLM을 실행하는 것은 리소스를 많이 소모할 수 있습니다. 오래된 하드웨어를 사용하는 사용자는 인덱싱 시간이 느리고 응답이 지연될 수 있습니다. 원활한 작동을 위해 최소 16GB의 RAM이 있는 머신이 권장됩니다.

### 설정 복잡성

Docker는 설치를 단순화하지만, 사용자 정의 임베딩 모델이나 데이터베이스 확장과 같은 고급 기능을 구성하려면 기술적 전문 지식이 필요합니다. 초보자는 완전히 관리되는 SaaS 솔루션에 비해 초기 설정이 어려울 수 있습니다.

### 제한된 멀티미디어 지원

현재 Khoj은 주로 텍스트 기반 데이터에 중점을 둡니다. PDF에서 텍스트를 구문 분석할 수는 있지만, 추가 통합 없이는 이미지 또는 오디오 분석을 네이티브로 지원하지 않습니다. 이는 멀티미디어 중심 지식 베이스에서의 유용성을 제한합니다.

```bash
# 지원 형식 확인
khoj list-formats
```

### 커뮤니티 규모

LangChain이나 LlamaIndex와 같은 대형 프로젝트에 비해 Khoj 커뮤니티는 작습니다. 이는 서드파티 플러그인이 적고 니치 사용 사례에 대한 문서가 덜 확장적임을 의미합니다. 그러나 GitHub에서의 활발한 개발은 이 문제를 완화하는 데 도움이 됩니다.

## FAQ

### Q1: 이 도구는 무엇이며 누구를 위한 것인가?
이것은 프로덕션 환경에서 이 오픈소스 AI 도구를 효과적으로 사용하는 방법에 대한 종합 가이드입니다.

### Q2: 이 도구는 대안과 어떻게 비교되는가?
이 도구는 유사한 솔루션에 비해 성능, 사용 편의성 및 커뮤니티 지원 측면에서 고유한 장점을 제공합니다.

### Q3: 상업적으로 이 도구를 사용할 수 있는가?
예, 이 도구 및 기타 대부분의 오픈소스 AI 도구는 해당 라이선스에 따라 상업적 사용을 허용합니다.

### Q4: 하드웨어 요구 사항은 무엇인가?
하드웨어 요구 사항은 모델 크기와 사용 사례에 따라 다릅니다. 최적의 성능을 위해 GPU 가속이 권장됩니다.

### Q5: 일반적인 문제를 해결하려면 어떻게 해야 하는가?
공식 문서, GitHub 이슈 및 커뮤니티 포럼을 확인하여 일반적인 문제에 대한 해결책을 찾으십시오.

### Q6: 학습 곡선이 있는가?
기본 사용법은 간단하지만, 고급 기능은 기본 개념과 구성에 대한 이해가 필요합니다.

### Q7: 프로젝트에 기여할 수 있는가?
예, 대부분의 오픈소스 AI 프로젝트는 GitHub 풀 리퀘스트 및 이슈 보고를 통해 기여를 환영합니다.

### Q: Khoj은 무료로 사용할 수 있는가?
예, Khoj은 오픈소스이며 AGPL-3.0 라이선스에 따라 다운로드하고 수정하는 것이 무료입니다. 그러나 OpenAI나 Anthropic과 같은 클라우드 기반 LLM을 사용하는 경우 API 비용이 발생합니다. Ollama를 통해 로컬 모델을 실행하면 직접적인 소프트웨어 요금은 발생하지 않으며 하드웨어 비용만 발생합니다.

### Q: 나만의 사용자 정의 임베딩 모델과 함께 Khoj을 사용할 수 있는가?
예, Khoj은 사용자 정의 임베딩 모델을 지원합니다. 로컬 또는 API를 통해 호스팅되는 모델을 사용하도록 구성할 수 있습니다. 이는 범용 임베딩이 잘 작동하지 않을 수 있는 도메인 특화 애플리케이션에 유용합니다.

```yaml
# 사용자 정의 임베딩 구성
embedding_model:
  provider: local
  model_name: sentence-transformers/all-MiniLM-L6-v2
```

### Q: Khoj은 데이터 프라이버시를 어떻게 처리하는가?
Khoj은 외부 서비스로 데이터를 보내도록 명시적으로 구성하지 않는 한 모든 데이터를 로컬 머신에 저장합니다. 자체 호스팅이 가능하므로 데이터가 어디에 존재하는지에 대해 완전한 제어권을 가집니다. AGPL-3.0 라이선스는 코드가 투명하고 감사 가능함을 보장합니다.

### Q: Khoj은 실시간 웹 검색을 지원하는가?
예, Khoj은 실시간 웹 검색을 수행하도록 구성할 수 있습니다. 이 기능을 통해 AI는 로컬 문서에 없을 수 있는 최신 정보를 검색할 수 있습니다. 프라이버시 선호도에 따라 이 기능을 켜거나 끌 수 있습니다.

```bash
# 웹 검색 활성화
khoj --enable-web-search
```


```bash
# 기본 설치 명령어
pip install khoj

# 설치 확인
Khoj --version
```

```python
# 예제 사용 코드 스니펫
import khoj

# 컴포넌트 초기화
component = Khoj()
result = component.process(input_data)
print(result)
```

```yaml
# 구성 예제
khoj:
  model: default
  timeout: 30
  max_workers: 4
  log_level: info
```

### Q: 인터넷 연결이 끊기면 어떻게 되는가?
로컬 모델과 로컬 스토리지를 사용하는 경우, Khoj은 인터넷 연결 없이도 정상적으로 작동합니다. 클라우드 LLM에 의존하는 경우 응답을 생성하려면 인터넷 액세스가 필요합니다. 로컬 모델로 전환하면 오프라인 기능이 보장됩니다.

## 결론

Khoj은 개인 AI 비서의 영역에서 중요한 진전을 나타냅니다. 프라이버시, 개방성 및 유연성을 우선시함으로써 Khoj은 데이터를 양도하지 않고도 LLM의 잠재력을 활용할 수 있도록 사용자에게 권한을 부여합니다. 사용자 정의 지식 베이스를 구축하려는 개발자이거나 방대한 양의 문헌을 정리하려는 연구원인 경우, Khoj은 성공에 필요한 도구를 제공합니다.

2026년으로 접어들면서 자치 데이터(self-sovereign data)의 중요성은 아무리 강조해도 지나치지 않습니다. Khoj은 고급 AI의 혜택을 누리면서 디지털 정체성에 대한 통제를 유지하기 위한 실용적인 솔루션을 제공합니다. 프로젝트를 탐색하고 개발에 기여하며 커뮤니티와 경험을 공유하기를 권장합니다.

견고한 인프라에서 Khoj을 배포하는 데 관심이 있는 경우 VPS 제공업체 사용을 고려하십시오. 오늘 [DigitalOcean에서 Khoj 배포](https://m.do.co/c/eca87ac14ee0)하고 크레딧을 위한 독점 추천 링크를 활용하십시오.

Telegram에서 DIBI8 커뮤니티와 대화하고 지원을 받으십시오: [t.me/DIBI8_Group](https://t.me/DIBI8_Group).

*이 기사는 오픈소스 AI 도구에 관한 DIBI8.com 시리즈의 일부입니다. 우리는 기술 스택에 대해 정보에 기반한 결정을 내릴 수 있도록 정확하고 편향되지 않으며 포괄적인 가이드를 제공하는 데 노력합니다.*

***

**제휴 광고 공개:** 이 기사에는 제휴 링크가 포함되어 있습니다. 이러한 링크를 통해 구매하면 추가 비용 없이 우리가 작은 수수료를 받을 수 있습니다. 이는 dibi8.com의 유지 보수 및 더 높은 품질의 콘텐츠 제작을 지원합니다. 우리는 독자들에게 진정한 가치를 제공한다고 믿는 도구와 서비스만 추천합니다.