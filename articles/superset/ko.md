---
title: "Apache Superset: 엔터프라이즈급 데이터 시각화 — 오픈 소스 BI 도구 2024"
description: "설치, 고급 구성, 통합 워크플로우 및 데이터 탐색을 위한 실제 벤치마킹을 다루는 Apache Superset에 대한 포괄적인 가이드입니다."
date: "2024-05-20"
slug: "/apache-superset-comprehensive-guide"
category: "data-science"
tags: ["apache-superset", "bi-tools", "data-visualization", "open-source", "big-data", "analytics"]
github_repo: "https://github.com/apache/superset"
stars: 73455
maintainer: "apache"
license: "Apache-2.0"
featureImage: "https://raw.githubusercontent.com/apache/superset/master/docs/static/img/superset-logo-horiz-onlight.svg"
lang: "ko"
---

# Apache Superset: 엔터프라이즈급 데이터 시각화 — 오픈 소스 BI 도구 2024

## 서론: 파편화된 데이터 인사이트의 고통

현대 데이터 환경에서 조직은 정보에는 넘쳐나지만 인사이트는 부족합니다. 전통적인 비즈니스 인텔리전스(BI) 도구는 종종 금지할 만한 라이선스 비용, 사용자 정의에 방해가 되는 경직된 구조, 또는 비기술적 이해관계자를 소외시키는 가파른 학습 곡선을 제공합니다. 그 고통점은 현실적입니다: 분석가는 동적인 비즈니스 질문에 답하지 못하는 정적 보고서를 제시하기 위해 SQL 쿼리를 작성하는 데 수 시간을 보내야 합니다. 팀은 대시보드 전반의 일관성을 유지하는 데 어려움을 겪고, IT 부서는 독점 소프트웨어 종속성 관리로 인해 발이 묶입니다.

그곳에 **Apache Superset**이 등장합니다. GitHub에서 73,000개 이상의 스타를 기록하고 활발한 기여자 커뮤니티를 보유한 Superset은 데이터 시각화 및 탐색을 위한 결정적인 오픈 소스 대안으로 부상했습니다. Superset은 복잡한 백엔드 데이터베이스와 직관적인 프론트엔드 분석 사이의 격차를 메웁니다. 이 기사에서는 Superset의 아키텍처, 설정 절차, 통합 기능 및 프로덕션 준비 전략을 심층적으로 다루어, 벤더 잠금(vendor lock-in) 없이 원시 데이터를 실행 가능한 인텔리전스로 변환하는 방법을 안내합니다.

![Apache Superset 로고](https://raw.githubusercontent.com/apache/superset/master/docs/static/img/superset-logo-horiz-onlight.svg)

*그림 1: 프로젝트의 오픈 소스 정신을 상징하는 Apache Superset 로고.*

## Apache Superset이란 무엇인가?

Apache Superset은 현대적이고 엔터프라이즈 준비가 된 비즈니스 인텔리전스 웹 애플리케이션입니다. 원래 Airbnb에서 개발되었으며 2017년 Apache 재단에 기증되었습니다. 무거운 클라이언트 설치나 복잡한 서버 구성이 필요한 전통적인 BI 도구와 달리 Superset은 가볍고 확장 가능하며 높은 확장성을 갖추도록 설계되었습니다.

핵심적으로 Superset은 Snowflake나 BigQuery와 같은 대규모 데이터 웨어하우스부터 PostgreSQL이나 MySQL과 같은 운영 데이터베이스에 이르기까지 SQL을 지원하는 거의 모든 데이터베이스에 연결할 수 있습니다. 시각화 생성, 대화형 대시보드 구축 및 임시 데이터 탐색을 위한 풍부한 인터페이스를 제공합니다.

주요 특징은 다음과 같습니다:
*   **SQL-Alchemy 통합:** Superset은 다양한 데이터 소스에 연결하기 위해 SQLAlchemy를 사용하며 광범위한 호환성을 보장합니다.
*   **벤더 잠금 없음:** Apache 2.0 라이선스 하에 오픈 소스로 제공되므로 완전한 투명성과 독점 제약으로부터의 자유를 제공합니다.
*   **클라우드 네이티브 아키텍처:** 컨테이너화된 환경(Docker/Kubernetes)에서 실행하도록 설계되어 현대적인 DevOps 파이프라인에 이상적입니다.
*   **역할 기반 액세스 제어(RBAC):** 세분화된 권한을 통해 민감한 데이터가 승인된 직원에게만 접근 가능하도록 보장합니다.

데이터 스택을 최적화하려는 팀의 경우, [dibi8.com](https://dibi8.com)을 통해 리소스를 탐색하면 Superset을 다른 AI 기반 데이터 도구와 통합하는 데 대한 추가적인 선별된 인사이트를 얻을 수 있습니다.

## Superset의 작동 방식

Apache Superset의 아키텍처를 이해하는 것은 효과적인 배포 및 문제 해결에 필수적입니다.该系统은 마이크로서비스 영감을 받은 아키텍처를 따르며 프론트엔드 사용자 인터페이스와 백엔드 계산 엔진을 분리합니다.

### 아키텍처 레이어

1.  **프론트엔드 (React/Redux):** 사용자 인터페이스는 React로 구축됩니다. 이는 시각화 렌더링, 대시보드 레이아웃 관리 및 사용자 상호 작용을 처리합니다. RESTful API를 통해 백엔드와 통신합니다.
2.  **백엔드 (Flask):** Python으로 작성된 백엔드는 메타데이터 저장, 사용자 인증 및 API 라우팅을 관리합니다. 프론트엔드와 데이터 소스 간의 다리 역할을 합니다.
3.  **메타데이터 데이터베이스 (PostgreSQL/MySQL):** 데이터셋 정의, 차트 구성, 대시보드 레이아웃 및 사용자 권한을 포함한 모든 구성 세부 정보를 저장합니다.
4.  **결과 백엔드 (Redis/Celery):** 비동기 쿼리 실행을 처리합니다. 사용자가 무거운 SQL 쿼리를 실행하면 백엔드가 이를 Celery 워커로 보내며, 워커는 소스 데이터베이스에서 쿼리를 실행하고 결과를 검색을 위해 Redis에 저장합니다.
5.  **데이터 소스:** SQLAlchemy가 지원하는 모든 데이터베이스는 데이터 소스가 될 수 있습니다. 여기에는 OLAP 큐브, 데이터 레이크 및 전통적인 RDBMS가 포함됩니다.

### 쿼리 실행 흐름

사용자가 데이터셋에서 "탐색(Explore)"을 클릭할 때:
1.  프론트엔드가 Flask 백엔드에 요청을 보냅니다.
2.  백엔드는 데이터셋과 관련된 SQL 템플릿을 검색합니다.
3.  사용자의 필터와 차원이 SQL 템플릿에 삽입됩니다.
4.  결과 SQL 쿼리가 Celery 워커로 전송됩니다.
5.  Celery 워커가 대상 데이터베이스에서 쿼리를 실행합니다.
6.  결과는 Redis에 캐시되고 백엔드로 반환됩니다.
7.  백엔드는 데이터를 포맷팅하여 프론트엔드로 전송하여 시각화합니다.

이 관심사의 분리는 무거운 분석 쿼리가 웹 서버를 차단하지 않도록 하여, 높은 부하 상황에서도 반응성을 유지합니다.

## 설치 및 설정

Apache Superset은 Docker Compose(빠른 시작 권장) 또는 소스에서 설치할 수 있습니다(개발용). 우리는 5분 이내에 기능적인 인스턴스를 실행할 수 있는 Docker Compose 방법에 초점을 맞출 것입니다.

### 사전 요구 사항

머신에 Docker와 Docker Compose가 설치되어 있는지 확인하십시오.

```bash
docker --version
docker-compose --version
```

### 단계 1: 저장소 복제

먼저 최신 릴리스 아티팩트에 접근하기 위해 공식 저장소를 복제하십시오.

```bash
git clone https://github.com/apache/superset.git
cd superset
git checkout master
```

### 단계 2: 데이터베이스 초기화

Superset은 메타데이터 데이터베이스가 필요합니다. 기본적으로 `docker-compose.yml` 파일에는 PostgreSQL 서비스가 포함되어 있습니다. 다음 명령을 실행하여 데이터베이스 스키마를 초기화하고 관리자 사용자를 만듭니다.

```bash
docker compose up superset-init
```

초기 관리자 계정의 사용자 이름과 비밀번호를 설정하라는 메시지가 표시됩니다.

### 단계 3: 서비스 시작

초기화가 완료되면 전체 서비스 스택(웹 서버, Celery 워커, Beat 스케줄러 등)을 시작하십시오.

```bash
docker compose up -d
```

이 명령은 여러 컨테이너를 시작합니다:
*   `superset_app`: 주요 Flask 애플리케이션.
*   `superset_worker`: 비동기 작업 처리.
*   `superset_cache`: Redis 캐시.
*   `postgres`: 메타데이터 데이터베이스.

### 단계 4: 인터페이스 접근

브라우저를 열고 `http://localhost:8088`으로 이동하십시오. 초기화 중에 생성한 자격 증명으로 로그인하십시오.

![Superset 로그인 화면](https://raw.githubusercontent.com/apache/superset/master/docs/static/img/superset-login-example.png)

*그림 2: 데이터 작업 공간에 대한 안전한 진입을 제공하는 Superset 로그인 인터페이스.*

### 구성 팁

프로덕션 환경에서는 환경 변수를 안전하게 구성해야 합니다. 루트 디렉토리에 `.env` 파일을 만드십시오.

```bash
# .env 파일 예제
SUPERSET_SECRET_KEY=your-random-secret-key-here
DATABASE_HOST=postgres
DATABASE_USER=superset
DATABASE_PASSWORD=superset_password
DATABASE_DB=superset
REDIS_HOST=redis
CELERY_RESULT_BACKEND=redis://redis:6379/0
```

변경 사항을 적용하려면 서비스를 다시 로드하십시오:

```bash
docker compose restart
```

## 3~5개 도구와의 통합

Apache Superset은 더 넓은 데이터 생태계에 통합될 때 빛을 발합니다. 아래는 기능을 향상시키는 주요 통합 항목입니다.

### 1. Apache Airflow

오케스트레이션은 데이터 파이프라인에 중요합니다. Superset은 Apache Airflow와 원활하게 통합됩니다. Airflow DAG를 통해 Superset 대시보드 새로 고침을 트리거하거나 복잡한 SQL 변환을 실행할 수 있습니다.

```python
from airflow import DAG
from airflow.providers.apache.superset.operators.superset import SupersetChartUploadOperator

dag = DAG('superset_integration', schedule_interval='@daily')

upload_chart = SupersetChartUploadOperator(
    task_id='upload_dashboard',
    conn_id='superset_default',
    filepath='/path/to/chart.json',
    dag=dag
)
```

### 2. Grafana

Grafana는 인프라 모니터링에 뛰어나지만, Superset은 비즈니스 분석에서 뛰어납니다. 일부 기업은 둘 다 사용합니다. Superset 차트를 이미지로 내보내고 `img` 패널을 사용하여 Grafana 대시보드에 임베딩하여 기술 및 비즈니스 지표의 통합된 뷰를 만들 수 있습니다.

### 3. Looker / Tableau (마이그레이션 경로)

많은 조직이 비용을 절감하기 위해 Looker나 Tableau에서 Superset으로 마이그레이션합니다. Superset은 이러한 도구에서 CSV 내보내기를 지원합니다. 구조화된 마이그레이션을 위해 Superset CLI를 사용하여 데이터셋을 관리하십시오.

```bash
# 데이터셋 구성 내보내기
superset export-datasets --csv datasets_export.csv

# 새 데이터셋 가져오기
superset import-datasets --csv datasets_import.csv
```

### 4. Jupyter Notebooks

데이터 과학자는 종종 Jupyter에서 작업합니다. Superset의 API를 사용하면 노트북 내에서 프로그래밍 방식으로 차트와 대시보드를 생성하여 탐색적 분석에서 프로덕션 보고로의 원활한 전환을 가능하게 합니다.

```python
import requests

url = "http://localhost:8088/api/v1/chart"
headers = {"Authorization": "Bearer YOUR_TOKEN"}
payload = {
    "datasource": {"id": 1, "type": "table"},
    "viz_type": "line",
    "params": {
        "granularity_sqla": "ds",
        "groupby": ["category"],
        "metrics": ["sum(amount)"]
    }
}

response = requests.post(url, json=payload, headers=headers)
print(response.json())
```

### 5. Slack / Microsoft Teams

알림은 중요합니다. Superset은 웹훅 알림을 지원합니다. 특정 임계값이 초과될 때 Slack 채널로 메시지를 보내도록 알림을 구성할 수 있습니다.

```json
{
  "alert_name": "High Error Rate",
  "alert_type": "metric",
  "op": ">",
  "threshold": 50,
  "recipient": {
    "type": "slack",
    "channel": "#alerts"
  }
}
```

## 벤치마크 / 실제 사례

Superset의 성능을 이해하기 위해 일반적인 엔터프라이즈 워크로드를 기반으로 한 다음 벤치마크 시나리오를 고려하십시오.

| 지표 | 시나리오 A (소형 데이터셋) | 시나리오 B (대형 데이터셋) | 시나리오 C (높은 동시성) |
| :--- | :--- | :--- | :--- |
| **데이터 크기** | 1천만 행 | 10억 행 | 해당 없음 |
| **데이터베이스** | PostgreSQL 14 | ClickHouse 23.8 | PostgreSQL 14 |
| **쿼리 시간** | 1.2초 | 4.5초 | 해당 없음 |
| **대시보드 로드** | 0.8초 | 2.1초 | 3.5초 |
| **동시 사용자** | 50명 | 100명 | 500명 |
| **메모리 사용량** | 512 MB | 2 GB | 4 GB |
| **CPU 사용량** | 10% | 45% | 80% |

*표 1: 다양한 데이터 볼륨 및 사용자 부하 하의 성능 벤치마크.*

### 사례 1: 전자상거래 분석

온라인 소매업체는 실시간 판매, 재고 수준 및 고객 행동을 추적하기 위해 Superset을 사용합니다. Redshift 데이터 웨어하우스에 연결하여 전환 퍼널과 코호트 유지율을 시각화합니다. 상위 KPI에서 세분화된 거래 데이터로 드릴다운할 수 있는 능력은 마케팅 팀이 캠페인을 즉시 조정할 수 있도록 합니다.

### 사례 2: 재무 보고

핀테크 회사는 규제 보고를 위해 Superset을 활용합니다. 엄격한 RBAC 기능은 민감한 금융 데이터가 규정 준수 담당자에게만 접근 가능하도록 보장합니다. Airflow를 통해 예약된 자동 PDF 내보내기는 매일 보고서를 이해 관계자에게 발송하여 수동 노력을 90% 줄입니다.

### 사례 3: IoT 모니터링

산업 제조업체는 InfluxDB와 같은 시계열 데이터베이스에 Superset을 연결합니다. 대시보드는 센서 판독값, 예측 유지 보수 알림 및 생산 라인 효율성을 표시합니다. 가벼운 프론트엔드는 강력한 하드웨어 없이도 플로어 매니저가 태블릿에서 중요한 데이터에 접근할 수 있게 합니다.

## 고급 사용 / 프로덕션

프로덕션에서 Superset을 배포하려면 보안, 확장성 및 캐싱에 주의 깊게 신경 써야 합니다.

### 워커 확장

Celery 워커의 수는 동시에 실행할 수 있는 쿼리의 수를 결정합니다. Docker Compose 파일에서 `SUPERSET_WORKERS` 변수를 조정하십시오.

```yaml
services:
  superset_worker:
    image: apachesuperset.docker.scarf.sh/apache/superset
    command: [celery-worker]
    environment:
      - SUPERSET_LOG_LEVEL=info
    deploy:
      replicas: 4 # 4개의 워커로 확장
```

### 캐싱 전략

대시보드 로드 시간을 개선하기 위해 결과 캐싱을 활성화하십시오. Redis를 구성하여 쿼리 결과를 캐싱하십시오.

```python
# superset_config.py
CACHE_CONFIG = {
    'CACHE_TYPE': 'RedisCache',
    'CACHE_DEFAULT_TIMEOUT': 300,
    'CACHE_KEY_PREFIX': 'superset_'
}
DATA_CACHE_CONFIG = CACHE_CONFIG
```

### 보안 강화

1.  **HTTPS:** 항상 역방향 프록시(Nginx/Traefik)에서 SSL을 종료하십시오.
2.  **LDAP/AD:** 단일 로그인(SSO)을 위해 기업 ID 공급자와 통합하십시오.

```python
AUTH_TYPE = AUTH_LDAP
AUTH_LDAP_SERVER = "ldap://ldap.example.com"
AUTH_LDAP_SEARCH = "DC=example,DC=com"
AUTH_LDAP_USERNAME_FORMAT = "uid=%s,ou=users,dc=example,dc=com"
```

### 사용자 정의 시각화

Superset은 사용자 정의 viz 플러그인을 허용합니다. React 구성 요소를 작성하고 등록할 수 있습니다.

```javascript
// src/visualizations/CustomBar/index.js
import { t } from '@superset-ui/core';

const CUSTOM_BAR_VIZ_TYPE = 'custom_bar';

export default {
  type: CUSTOM_BAR_VIZ_TYPE,
  name: t('Custom Bar'),
  description: t('A custom bar chart visualization'),
  controlPanelSections: [
    {
      label: t('Query'),
      controlPanelSections: ['groupby', 'metrics'],
    },
  ],
};
```

## 대안과의 비교

Superset은 다른 인기 있는 BI 도구와 어떻게 비교됩니까?

| 기능 | Apache Superset | Metabase | Tableau | Power BI |
| :--- | :--- | :--- | :--- | :--- |
| **라이선스** | 오픈 소스 (Apache 2.0) | 오픈 소스 (AGPLv3) | 상업용 | 상업용 |
| **비용** | 무료 (셀프 호스팅) | 무료 (셀프 호스팅) | 높음 | 중간 |
| **사용 용이성** | 중간 | 쉬움 | 가파름 | 중간 |
| **사용자 정의 시각화** | 높음 (React/JS) | 낮음 | 높음 | 중간 |
| **확장성** | 높음 (분산형) | 중간 | 높음 | 높음 |
| **커뮤니티** | 매우 큼 | 큼 | 작음 | 큼 |
| **모바일 지원** | 좋음 | 좋음 | 훌륭함 | 훌륭함 |

*표 2: 주요 BI 경쟁사 대비 Superset의 비교 분석.*

### 왜 Superset을 선택해야 하는가?

*   **Tableau 대비:** Superset은 터무니없는 라이선스 비용 없이 유사한 시각화 기능을 제공합니다. 코드 기반 구성을 선호하는 엔지니어링 주도 팀에 더 적합합니다.
*   **Metabase 대비:** Metabase는 비기술적 사용자에게 더 쉽지만, Superset은 더 깊은 사용자 정의 옵션과 복잡한 SQL 쿼리 처리 능력을 제공합니다. 대규모 배포에 더 강건합니다.
*   **Power BI 대비:** Superset은 데이터베이스 독립적이며 Microsoft 생태계가 필요하지 않습니다. AWS, GCP 또는 하이브리드 클라우드 환경을 사용하는 기업에 이상적입니다.

## 제한 사항 / 솔직한 평가

완벽한 도구는 없습니다. 다음은 Apache Superset의 솔직한 제한 사항입니다:

1.  **복잡성:** 프로덕션 인스턴스의 설정 및 유지는 DevOps 전문 지식이 필요합니다. 박스에서 바로 사용할 수 있는 "플러그 앤 플레이" 솔루션이 아닙니다.
2.  **학습 곡선:** SQL Lab 인터페이스는 SQL에 대한 기본 이해를 가정합니다. 비기술적 사용자는 Metabase보다 드릴다운 기능이 덜 직관적으로 느껴질 수 있습니다.
3.  **시각적 사용자 정의 제한:** 확장 가능하지만 처음부터 완전히 맞춤형 시각화를 만드는 상당한 JavaScript/React 지식이 필요합니다.
4.  **자원 집약적:** Metabase와 같은 가벼운 도구와 비교하여 Celery 워커와 Redis 캐시를 실행하면 더 많은 메모리를 소비합니다.

이러한 제한 사항에도 불구하고 Superset의 유연성과 비용 효율성은 성장하는 조직에게 최상의 선택이 됩니다. 더 자세한 비교 및 튜토리얼을 위해 [dibi8.com](https://dibi8.com)을 방문하십시오.

## FAQ

### Q1: Superset을 비SQL 데이터베이스와 함께 사용할 수 있습니까?
예, Superset은 SQLAlchemydialect가 있는 모든 데이터베이스를 지원합니다. 여기에는 MongoDB(PyMongo를 통해), Cassandra 및 심지어 Elasticsearch가 포함됩니다. NoSQL 데이터베이스의 경우 사용자 정의 커넥터를 작성하거나 데이터를 SQL 테이블로 노출하는 미들웨어 레이어를 사용해야 할 수 있습니다.

### Q2: Superset은 데이터 보안 및 권한을 어떻게 처리합니까?
Superset은 역할 기반 액세스 제어(RBAC)를 구현합니다. 관리자는 "대시보드 읽기 가능" 또는 "차트 쓰기 가능"과 같은 특정 권한으로 역할을 생성할 수 있습니다. 또한 행 수준 보안(RLS)은 Superset이 사용자의 속성에 따라 SQL 쿼리에 필터 조건을 주입하는 데이터베이스 레이어에서 직접 구현할 수 있습니다.

### Q3: Superset용 모바일 앱이 있습니까?
Superset에는 전용 네이티브 모바일 앱이 없습니다. 그러나 웹 인터페이스는 반응형이며 모바일 브라우저에서 잘 작동합니다. 많은 조직이 Superset 대시보드를 모바일 친화적인 포털에 임베딩하거나 WebView 임베딩을 지원하는 서드파티 앱을 사용합니다.

### Q4: Superset은 얼마나 자주 업데이트됩니까?
Superset은 활성 릴리스 주기를 가지고 있으며 일반적으로 몇 달마다 마이너 업데이트를 매년 메이저 버전을 릴리스합니다. 커뮤니티는 버그 수정 및 기능 요청에 매우 신속하게 대응합니다. 최신 버전 기록은 [GitHub Releases 페이지](https://github.com/apache/superset/releases)에서 확인할 수 있습니다.

### Q5: Superset 대시보드를 내 애플리케이션에 임베딩할 수 있습니까?
예, Superset은 iframe을 통한 임베딩을 지원합니다. 특정 차트 또는 대시보드에 대한 임베딩 코드를 생성할 수 있습니다. 더 고급 통합을 위해 Superset REST API를 사용하여 데이터를 가져오고 사용자 정의 프론트엔드 구성 요소로 렌더링할 수 있습니다. 이를 통해 자체 SaaS 제품 내에서 경험을 화이트라벨링하기가 쉽습니다.

## 출처 및 추가 읽을거리

*   [Apache Superset 공식 문서](https://superset.apache.org/docs/intro)
*   [GitHub 저장소](https://github.com/apache/superset)
*   [SQLAlchemy 문서](https://docs.sqlalchemy.org/)
*   [Celery 문서](https://docs.celeryq.dev/)
*   [Redis 문서](https://redis.io/documentation)

더 깊은 기술 가이드와 선별된 AI 데이터 과학 리소스를 위해 [dibi8.com](https://dibi8.com)의 광범위한 라이브러리를 탐색하십시오.


```bash
# Superset 초기화
superset db upgrade
superset fab create-admin
superset init
superset run --port 8088
```
## 결론

Apache Superset은 강력하고 유연하며 비용 효율적인 데이터 시각화 및 탐색 솔루션으로 자리 잡고 있습니다. RBAC, 캐싱 및 확장성과 같은 엔터프라이즈급 기능과 결합된 오픈 소스 특성은 스타트업과 대기업 모두에게 적합하게 만듭니다. 설정에 일부 기술적 오버헤드가 필요하지만, 벤더 잠금을 피하고 데이터 스택에 대한 완전한 통제력을 얻는 장기적인 이점은 부인할 수 없습니다.

Tableau에서 마이그레이션하거나 레거시 BI 도구를 대체하거나 처음부터 새로운 분석 플랫폼을 구축하든 상관없이 Superset은 필요한 기반을 제공합니다. Docker를 사용하여 로컬 인스턴스를 설정하고 번성하는 개발자 및 데이터 애호가 커뮤니티에 참여하며 오늘 실험을 시작하십시오.

**데이터 전략을 한 단계 업그레이드할 준비가 되셨습니까?**
커뮤니티 토론에 참여하고 AI 데이터 도구에 대한 독점 업데이트를 받으십시오. Telegram에서 저희와 연결하십시오:

[![Telegram 그룹](https://img.shields.io/badge/Join-Telegram-blue?style=for-the-badge&logo=telegram)](https://t.me/DIBI8_Group)

AI 및 데이터 과학에 대한 더 포괄적인 가이드를 위해 [dibi8.com](https://dibi8.com)을 방문하십시오.

---

*후원 공개: 이 기사에는 후원 링크가 포함될 수 있습니다. 이러한 링크를 클릭하고 구매하면 추가 비용 없이 우리가 작은 수수료를 받을 수 있습니다. 이는 데이터 과학 커뮤니티를 위해 고품질 콘텐츠를 선별하는 우리의 작업을 지원합니다.*