### 부서별 LLM 사용 패턴 분석 기반 AI 업무 자동화 추천 서비스

<div align="center">
  <h1>프라이버시 보호형 LLM 사용 패턴 분석 대시보드</h1>
  <p>🔍 부서별 LLM 사용 로그 → AI 업무 자동화 기획 추천 서비스 🔍</p>
</div>

<br/>

<div align="center">
  <img src="./assets/Main.png" alt="Main" style="border-radius: 10px;"/>
</div>

<br/>

<div align="center">
  <a href="">홈페이지</a>
    |  
  <a href="">Swagger</a>
    |  
  <a href="./docs/feature_specification.csv">기능명세서</a>
</div>

---

## ✍️ 프로젝트 개요

- **프로젝트명:** 프라이버시 보호형 LLM 사용 패턴 분석 기반 AI 업무 자동화 추천 대시보드
- **프로젝트 형태:** Azure 기반 AI 서비스
- **목표:** 기존 LLM 모니터링의 한계(“얼마나 썼는지”)를 넘어, 사용 로그를 기반으로 **“어떤 부서의 어떤 업무를 AI 서비스로 전환해야 하는지”** 를 추천하는 기능으로 확장
- **주요 타겟 사용자:** 사내 Azure OpenAI / Copilot을 도입했지만, 부서별 활용 현황과 다음 자동화 과제를 정하지 못한 중견·대기업 DX팀 (DX팀, IT기획팀, 정보보안팀, 경영기획팀, HRD/교육팀 등)

---

## ✍️ 프로젝트 소개

### 프로젝트 배경

기존 LLM 모니터링 서비스는 다음과 같은 한계가 있었습니다:

1. **사용량/비용 모니터링 중심** — “누가 얼마나 썼는지”만 확인 가능

2. **자동화 기회 식별 부재** — 어떤 업무를 AI로 전환해야 하는지 인사이트 부족

3. **보안 리스크 분석 부재** — 프롬프트에 포함된 민감정보에 대한 통합적 관리 부재

본 서비스는 위 문제를 해결하기 위해 **프롬프트 원문을 저장하지 않고**, 마스킹·분류·임베딩·군집화를 통해 **부서별 업무 패턴**과 **보안 위험**을 함께 분석하여, 다음 AI 자동화 과제를 추천하는 서비스입니다.

```
[기존 LLM 모니터링 서비스] : 누가 얼마나 썼는지 확인
[우리 서비스]              : 원문을 저장하지 않고, 부서별 업무 패턴과 보안 위험을 분석해
                            다음 AI 자동화 과제를 추천
```

---

## 🚀 프로젝트 목표

1. **부서별 업무 DNA 분석** — 프롬프트/업무 유형 분류 기반 사용 성향 파악

2. **AI 자동화 후보 추천** — Opportunity Score 기반 우선순위 추천 및 기대효과 제시

3. **책임 있는 AI 실현** — 원문 미보관, 마스킹, 부서 단위 집계로 프라이버시 보호

---

## 📌 주요 기능

### **1. 프롬프트 마스킹 (Sensitive Prompt Filter)**

프롬프트에서 민감정보를 즉시 탐지·마스킹하고 원문은 폐기합니다.

```
마스킹 탐지 항목:
- 개인정보: 이름, 이메일, 전화번호, 주민번호 패턴
- 고객정보: 고객명, 주문번호, 계정번호
- 기밀정보: 계약서, NDA, 내부회의록, 인사평가
- 소스코드: API Key, Secret, Token, DB Connection String
- 재무정보: 매출, 급여, 정산, 비용, 견적
- 법무정보: 소송, 법률검토, 계약조건
```

```python
prompt_text
→ PII / 기밀 정보 탐지
→ 즉시 마스킹
→ 업무 유형 분류
→ embedding 생성
→ 원문 폐기 (FUNC-PROC-009 폐기 검증 / FUNC-PROC-010 실패 Fallback)
→ cluster_id, intent_label, risk_score, department 통계만 저장
```

### **2. 업무 유형 분류 (AI 업무 자동화 측면 해석)**

- “보고서 작성형” → 문서 자동화 후보
- “코드 생성형” → 개발 생산성 Copilot 후보
- “고객 응대형” → 상담/FAQ Agent 후보
- “문서 요약형” → RAG 검색/요약 시스템 후보
- “데이터 분석형” → BI Agent 후보
- “단순 검색/질문형” → 사내 지식검색 고도화 후보

### **3. 부서별 사용 패턴 분석 (Privacy-preserving Clustering)**

```python
department
→ 업무 유형 비중
→ 토큰 사용량
→ 비용
→ 사용자 수
→ 반복 프롬프트 비율
→ 위험 프롬프트 비율
→ 자동화 후보 점수
```

### **4. Opportunity Score (자동화 후보 점수)**

```python
Opportunity Score =
  사용 빈도         30%
+ 반복성           25%
+ 비용 영향        20%
+ 다수 사용자 여부 15%
+ 구현 난이도 역점수 10%
```

### **5. Risk Score (보안 위험도 점수)**

```python
Risk Score =
  개인정보 위험도     30%
+ 기밀정보 위험도     25%
+ 고객정보 위험도     20%
+ 재무/법무 민감도    15%
+ 원문 노출 가능성    10%
```

| Risk Score | 등급 | 의미 |
| :---: | :---: | :--- |
| 0~30 | Low | 일반 업무 프롬프트 |
| 31~60 | Medium | 일부 민감정보 가능성 |
| 61~80 | High | 개인정보/기밀정보 포함 가능성 높음 |
| 81~100 | Critical | 원문 저장 금지, 관리자 검토 필요 |

### **6. AI 업무 자동화 추천 카드 (Explainable Recommendation)**

```python
<마케팅팀 - 캠페인 보고서 자동 생성기>

Opportunity Score: 82
Risk Score: 38

추천 이유:
- 보고서 작성형 프롬프트 비중 42%
- 반복 프롬프트 비율 63%
- 여러 사용자가 유사 업무 수행
- 평균 위험도는 Medium 이하

보안 조치:
- 고객명/이메일 자동 마스킹
- 원문 프롬프트 미저장
- 부서 단위 통계만 제공
```

```python
<법무팀 - 계약서 검토 Agent>

Opportunity Score: 78
Risk Score: 86

판단:
자동화 후보로는 가치가 높지만,
계약서/기밀정보 포함 가능성이 높아 즉시 도입보다 보안 검토가 필요합니다.

권장 조치:
- Private RAG 환경 필요
- 접근 권한 제한
- 원문 저장 금지
- 감사 로그 적용
```

### **7. 부서별 대시보드 + 보안 위험도 (Department-level Only)**

```python
[부서별 Risk Overview]
- 부서별 평균 Risk Score
- High/Critical 프롬프트 비율
- 탐지된 민감정보 유형 비중
- 위험도가 높은 업무유형
- 보안 교육 필요 영역
```

> 개인별 프롬프트 순위, 개인별 위험도 순위, 개인별 비용 순위는 제공하지 않습니다. 모든 분석은 부서 단위로만 집계합니다.

---

## 🔒 책임 있는 AI

### 데이터 저장 정책

| 구분 | 저장 여부 | 설명 |
| :--- | :---: | :--- |
| 프롬프트 원문 | ❌ | 원칙적으로 저장 X, 데모/운영 모두 미보관 |
| 마스킹된 프롬프트 | △ | 대표 예시용으로 선택적 저장, 접근 제한 |
| Embedding Vector | ⚪ (민감) | 원문 유추 가능성을 고려해 민감 데이터처럼 관리 |
| 부서명 | ⚪ | 분석 단위로 사용 |
| 사용자 ID | △ | Hash 처리, 개인별 분석 금지 |
| Token / Cost / Model | ⚪ | 비용 분석용 |
| Cluster ID (업무유형 라벨) | ⚪ | 업무 유형 군집 |
| Risk Score | ⚪ | 보안 위험 관리 |
| Opportunity Score | ⚪ | 자동화 우선순위 추천 |

### 보안성 핵심 6원칙

1. **원문 프롬프트 장기 저장 금지** — 마스킹·분류 후 즉시 폐기 (`FUNC-PROC-009` 폐기 검증 + `FUNC-PROC-010` 실패 Fallback)
2. **개인 단위 분석 금지** — 부서 단위로만 집계 (감시 도구가 아닌 업무 개선 도구)
3. **마스킹 실패 가능성 전제 설계** — 원문 미보관, 접근통제, 보관기간 제한 병행
4. **Embedding도 민감 파생 데이터로 취급** — 접근 권한·보관 기간 제한 (`FUNC-PROC-011`)
5. **Risk Score와 Opportunity Score 분리** — 가치가 높아도 위험도 높으면 ‘보안 검토 필요’로 분류 (`SCR-RECO-004`)
6. **추천 근거 설명 가능성** — 업무유형 비중·반복성·비용 영향·사용자 수·위험도를 근거로 설명

> 본 서비스는 프롬프트 원문을 장기 저장하지 않는다. 입력된 프롬프트는 Azure 환경 내에서 즉시 개인정보·기밀정보 마스킹을 거친 뒤, 업무 유형 분류와 임베딩 생성에만 사용된다. 저장되는 데이터는 부서 단위 통계, 군집 ID, 위험도 점수, 자동화 기회 점수이며, 개인별 감시가 아니라 부서 단위 업무 개선을 목적으로 한다.

### 🏢 기업 도입 시 보안 우려 및 대응

| # | 기업의 보안 우려 | 본 서비스의 대응 |
|:---:|:---|:---|
| 1 | 마스킹 전 원문이 외부 인프라에 잠시라도 머무름 | **Edge Masking** — 사내 LLM Gateway 단에서 선마스킹 옵션 제공 (옵션 C) |
| 2 | 데이터 주권 / Region 이슈 | 고객별 Azure Region 선택 (Korea Central 등) |
| 3 | 멀티테넌시 격리 수준 | 테넌트별 DB Schema 분리, 별도 Blob Container |
| 4 | 과도한 Azure 권한 요구 | 최소 권한 원칙 — `Storage Blob Data Reader`, `Event Hub Data Receiver`만 요구 |
| 5 | 네트워크 경로 노출 | Private Endpoint / Private Link 지원, TLS 1.2+, 저장 시 CMK |
| 6 | 감사 추적 및 책임 소재 | 고객사에 접근 감사 로그 제공, 유출 통지 SLA 명시 |
| 7 | 계약 종료 시 데이터 잔존 | 임베딩·백업본 포함 완전 삭제 보장 및 증빙 발급 |

### 🛠️ 배포 모드 (Deployment Options)

| 배포 모드 | 설명 | 보안 수준 | 적합한 고객 |
|:---|:---|:---:|:---|
| **A. SaaS Multi-tenant** | 본 서비스 인프라에서 모든 고객 처리 | 중 | 중견 기업, PoC 단계 |
| **B. Customer-hosted (BYO Cloud)** | ARM/Bicep 템플릿으로 고객 Azure 구독 내 배포, 데이터 외부 미반출 | 높음 | 대기업, 금융·공공 |
| **C. Edge Masking Gateway** | 사내 LLM Gateway에서 마스킹 후 전송, 본 서비스는 마스킹된 데이터만 수신 | 최고 | 법무·의료·기밀 업무 다수 |

---

## 🧑‍💻 팀원 소개

| **이름** | **역할** |
|:-----------:|:---------------:|
|  | Data/AI |
|  | AI/ML |
|  | Backend |
|  | Frontend |

---

## ⚙️ 기술 스택

<table>
  <thead>
    <tr>
      <th>분류</th>
      <th>기술 스택</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>프론트엔드</td>
      <td>
        <img src="https://img.shields.io/badge/React-61DAFB?style=flat&logo=react&logoColor=white"/>
        <img src="https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white"/>
        <img src="https://img.shields.io/badge/Azure%20Static%20Web%20Apps-0078D4?style=flat&logo=microsoftazure&logoColor=white"/>
      </td>
    </tr>
    <tr>
      <td>백엔드</td>
      <td>
        <img src="https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white"/>
        <img src="https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white"/>
        <img src="https://img.shields.io/badge/Azure%20App%20Service-0078D4?style=flat&logo=microsoftazure&logoColor=white"/>
        <img src="https://img.shields.io/badge/Azure%20Functions-0062AD?style=flat&logo=microsoftazure&logoColor=white"/>
      </td>
    </tr>
    <tr>
      <td>AI / ML</td>
      <td>
        <img src="https://img.shields.io/badge/Azure%20OpenAI-412991?style=flat&logo=openai&logoColor=white"/>
        <img src="https://img.shields.io/badge/Embedding%20Model-412991?style=flat&logo=openai&logoColor=white"/>
        <img src="https://img.shields.io/badge/scikit--learn-F7931E?style=flat&logo=scikitlearn&logoColor=white"/>
      </td>
    </tr>
    <tr>
      <td>데이터베이스 / 스토리지</td>
      <td>
        <img src="https://img.shields.io/badge/Azure%20SQL%20Database-CC2927?style=flat&logo=microsoftsqlserver&logoColor=white"/>
        <img src="https://img.shields.io/badge/Azure%20Blob%20Storage-0078D4?style=flat&logo=microsoftazure&logoColor=white"/>
      </td>
    </tr>
    <tr>
      <td>실시간 수집 (P2)</td>
      <td>
        <img src="https://img.shields.io/badge/Azure%20Event%20Hub-0078D4?style=flat&logo=microsoftazure&logoColor=white"/>
        <img src="https://img.shields.io/badge/LLM%20Gateway-1E1E1E?style=flat&logoColor=white"/>
      </td>
    </tr>
    <tr>
      <td>인프라 / 운영</td>
      <td>
        <img src="https://img.shields.io/badge/Application%20Insights-68217A?style=flat&logo=microsoftazure&logoColor=white"/>
        <img src="https://img.shields.io/badge/Azure%20Key%20Vault-0078D4?style=flat&logo=microsoftazure&logoColor=white"/>
        <img src="https://img.shields.io/badge/Microsoft%20Entra%20ID-0078D4?style=flat&logo=microsoftazure&logoColor=white"/>
        <img src="https://img.shields.io/badge/GitHub%20Actions-2088FF?style=flat&logo=githubactions&logoColor=white"/>
      </td>
    </tr>
  </tbody>
</table>

---

## 🏗️ 시스템 아키텍처

### 입력 경로 (P0 메인 / P2 실시간)

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│   [P0 메인 입력]                  [P2 실시간 입력]              │
│                                                                 │
│   운영자가 CSV 추출/업로드        사내 LLM Gateway가 push       │
│         ↓                                ↓                      │
│   [React Dashboard]               [사내 LLM Gateway]            │
│   Azure Static Web Apps                  ↓ (Event Hub push)     │
│         ↓                         [Azure Event Hub]             │
│         ↓                                ↓ (EventHubTrigger)    │
│         ↓                         [Azure Functions]             │
│         ↓                                ↓                      │
│         └──→ [Azure Blob Storage] ←──────┘                      │
│                       ↓                                         │
│         ┌─────────────────────────┐                             │
│         │  FastAPI Backend         │                             │
│         │  (Azure App Service)     │                             │
│         │                          │                             │
│         │  마스킹 → 분류 → Score   │                             │
│         │  → 자동화 매칭           │                             │
│         └─────────────────────────┘                             │
│                       ↓                                         │
│              [Azure SQL Database]                               │
│              부서별 통계 / Risk / Opportunity                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### AI 처리 흐름 (입력 경로 무관 단일 파이프라인)

```
입력 (CSV or Event Hub)
  ↓
Prompt Text 추출
  ↓
PII/기밀정보 탐지 (정규식 + LLM)
  ↓
마스킹 → 원문 즉시 폐기 (폐기 검증 / 실패 Fallback)
  ↓
업무 유형 분류 (Azure OpenAI) — 6개 거시 카테고리
  ↓
[P1] Embedding 생성 → 카테고리 내부 Sub-Clustering (계층적)
       └ 같은 분류 안에서 세부 반복 업무 패턴 발견
         예) "보고서 작성형" → 캠페인 보고서 / 주간 운영 보고서 / 임원 보고서
  ↓
Risk Score 계산 (5개 항목 가중치 합산)
  ↓
Opportunity Score 계산 (5개 항목 가중치 합산)
  ↓
자동화 후보 매칭
  ↓
부서별 대시보드 표시 / Risk 시각화 / 추천 카드
```

---

## ☁️ 사용 Azure 리소스

### 필수 리소스 (P0)

| 용도 | Azure 리소스 | 사용 이유 |
| :--- | :--- | :--- |
| 프론트 배포 | Azure Static Web Apps | React 대시보드 배포 |
| 백엔드 API | Azure App Service | FastAPI 서버 배포 |
| LLM 분류/추천 | Azure OpenAI Service | 업무유형 분류, 마스킹 탐지 |
| DB | Azure SQL Database | 부서별 통계, 점수, 추천 결과 저장 |
| 파일 저장 | Azure Blob Storage | CSV 업로드 파일 임시 저장 |
| Secret 관리 | App Service Environment Variables | API Key, DB URL 관리 (P0 단계) |

### 확장 리소스 (P1)

| 용도 | Azure 리소스 | 사용 이유 |
| :--- | :--- | :--- |
| 임베딩 | Azure OpenAI Embedding Model | 프롬프트 군집화용 벡터 생성 |
| 모니터링 | Application Insights | API 오류/호출 로그 / 시스템 모니터링 대시보드 |

### 운영 리소스 (P2)

| 용도 | Azure 리소스 | 사용 이유 |
| :--- | :--- | :--- |
| 실시간 수집 | Azure Event Hub | 사내 LLM Gateway에서 로그 push 수신 |
| 실시간 처리 | Azure Functions | EventHubTrigger 기반 자동 분석 파이프라인 진입 |
| 인증 | Microsoft Entra ID | SSO 로그인 / RBAC |
| Key 관리 | Azure Key Vault | OpenAI Key, DB Password 통합 관리 |
| 배포 자동화 | GitHub Actions | main 브랜치 push 시 자동 배포 |

---

## 🗂️ 주요 구현사항 (우선순위)

> 전체 38개 요구사항의 상세 명세는 [기능명세서 CSV](./docs/feature_specification.csv) 참고

### P0 — 메인 데모 필수 (17개)

| 화면 넘버링 | 요구사항 | 담당 파트 | Azure |
|:---|:---|:---|:---|
| SCR-INPUT-001 | CSV 로그 업로드 | Frontend, Backend | Static Web Apps, App Service, Blob |
| SCR-INPUT-004 | 데이터 입력 이력 조회 | Frontend, Backend | Static Web Apps, Azure SQL |
| FUNC-PROC-001 | PII/기밀정보 탐지 | AI/ML | Azure OpenAI |
| FUNC-PROC-002 | 프롬프트 마스킹 | AI/ML | Azure OpenAI |
| FUNC-PROC-003 | 업무 유형 분류 | AI/ML | Azure OpenAI |
| FUNC-PROC-006 | Risk Score 계산 | Backend | App Service, Azure SQL |
| FUNC-PROC-007 | Opportunity Score 계산 | Backend | App Service, Azure SQL |
| FUNC-PROC-008 | 자동화 후보 매칭 | Backend, AI/ML | Azure OpenAI, Azure SQL |
| FUNC-PROC-009 | 마스킹 후 원문 즉시 폐기 검증 | Backend, AI/ML | App Service |
| FUNC-PROC-010 | 마스킹 실패 Fallback | AI/ML | Azure OpenAI |
| SCR-DASH-001 | 부서별 LLM 사용 현황 | Frontend, Backend | Static Web Apps, Azure SQL |
| SCR-DASH-002 | 부서별 업무 유형 비중 | Frontend | Static Web Apps |
| SCR-RECO-001 | AI 자동화 후보 카드 리스트 | Frontend | Static Web Apps |
| SCR-RECO-002 | 추천 상세 보기 | Frontend, Backend | Static Web Apps, App Service |
| SCR-RECO-004 | Risk 기반 도입 판단 | Frontend, Backend | Static Web Apps, App Service |
| SCR-RISK-001 | 부서별 Risk Overview | Frontend | Static Web Apps |
| SCR-RISK-003 | 위험도 등급 시각화 | Frontend | Static Web Apps |

### P1 — 확장 데모 (9개)

| 화면 넘버링 | 요구사항 | 담당 파트 | Azure |
|:---|:---|:---|:---|
| FUNC-PROC-004 | Embedding 생성 (Sub-Clustering 용) | AI/ML | Azure OpenAI Embedding |
| FUNC-PROC-005 | 카테고리 내부 Sub-Clustering | AI/ML | App Service |
| FUNC-PROC-011 | Embedding 접근 통제 | Backend, Infra | Azure SQL, Key Vault |
| SCR-DASH-003 | 부서별 반복 프롬프트 비율 | Frontend, Backend | Static Web Apps, App Service |
| SCR-DASH-004 | 부서별 비용 트렌드 | Frontend | Static Web Apps |
| SCR-RECO-003 | 추천 근거 설명 (XAI) | AI/ML, Backend | Azure OpenAI |
| SCR-RISK-002 | 민감정보 유형별 통계 | Frontend, Backend | Static Web Apps, App Service |
| SCR-ADMIN-001 | 마스킹 규칙 관리 | Frontend, Backend | Static Web Apps, App Service, Azure SQL |
| SCR-AUDIT-002 | 시스템 모니터링 대시보드 | Backend, Infra | Application Insights |

### P2 — 운영 확장 (12개)

| 화면 넘버링 | 요구사항 | 담당 파트 | Azure |
|:---|:---|:---|:---|
| SCR-AUTH-001 | 관리자 로그인 (SSO) | Backend | Microsoft Entra ID, App Service |
| SCR-AUTH-002 | 역할 기반 권한 관리 | Backend | Microsoft Entra ID, Azure SQL |
| SCR-INPUT-002 | **사내 LLM Gateway 실시간 수집** | Backend, Infra | **Event Hub, Azure Functions, Blob** |
| SCR-INPUT-003 | Gateway 사전 마스킹 (Edge Masking) | Backend, Infra | Event Hub, App Service |
| SCR-INPUT-005 | 데모 트리거 (샘플 프롬프트 실행) | Frontend, Backend | App Service, Azure OpenAI, Event Hub |
| SCR-RISK-004 | 보안 교육 필요 영역 추천 | Backend, AI/ML | Azure OpenAI |
| SCR-ADMIN-002 | 데이터 보관 정책 | Backend | App Service, Azure SQL |
| SCR-ADMIN-003 | 배포 모드 설정 | Backend, Infra | App Service, Key Vault |
| SCR-ADMIN-004 | 부서/사용자 관리 | Backend | Azure SQL, Microsoft Entra ID |
| SCR-AUDIT-001 | 접근 감사 로그 조회 | Backend | Application Insights, Azure SQL |
| SCR-AUDIT-003 | 데이터 완전 삭제 | Backend | Azure SQL, Blob Storage |
| SCR-AUDIT-004 | Key/Secret 관리 (Key Vault) | Infra | Azure Key Vault |

---

## 🎯 단계별 완료 시 구현 범위

### ✅ P0 완료 시 — 완벽한 메인 서비스 (CSV 기반 SaaS 단독 동작)

```
[데이터 입력]
✅ CSV 업로드 + 입력 이력 가시성

[데이터 처리 - 책임 있는 AI 완비]
✅ PII/기밀정보 탐지 → 마스킹 → 원문 폐기 검증 → 마스킹 실패 Fallback
✅ 업무 유형 분류 (LLM 기반 6개 카테고리)
✅ Risk Score / Opportunity Score 계산
✅ 자동화 후보 매칭

[부서별 대시보드]
✅ 부서별 사용 현황 + 업무 유형 비중

[자동화 추천 - 핵심 가치]
✅ 자동화 후보 카드 + 추천 상세
✅ Risk 기반 도입 판단 (Opportunity vs Risk 결합 — 핵심 차별화)

[보안/위험도 - 책임 있는 AI 시각화]
✅ 부서별 Risk Overview + 위험도 등급 시각화
```

> **"CSV 업로드 한 번으로 부서별 자동화 후보 + 보안 위험을 함께 추천"** 하는 완결된 서비스. P1/P2 없이도 완전 동작 및 평가 가능.

**실행 환경**: SaaS (옵션 A), 단일 관리자 모드, 환경변수 기반 Secret 관리

### 🔵 P1 완료 시 — 분석 정밀도/설명력 강화 (확장 데모)

P0 + 다음 9개 추가:

```
[정밀도 향상]
🔵 Embedding 생성 + 카테고리 내부 Sub-Clustering → 6개 거시 카테고리를
   세부 업무 패턴까지 분해 ("보고서 작성형" → "캠페인 보고서" 등)
   → 추천 카드의 구체성 ↑ ("마케팅팀 - 캠페인 보고서 자동 생성기" 수준)
🔵 Embedding 접근 통제 (민감 파생 데이터 관리)

[설명력 향상]
🔵 추천 근거 자연어 설명 (XAI) — "왜 이 부서에 이 자동화를?"

[대시보드 확장]
🔵 부서별 반복 프롬프트 비율
🔵 부서별 비용 트렌드 (월별/주별)
🔵 민감정보 유형별 통계

[운영 편의]
🔵 마스킹 규칙 관리 (관리자가 패턴 직접 추가/수정)
🔵 시스템 모니터링 대시보드 (Application Insights)
```

> **분석 결과의 신뢰도와 운영 편의성**이 크게 올라감. 평가 발표 시 "왜 이렇게 추천했는가" 설명력 ↑

### 🟡 P2 완료 시 — 운영 환경 완성 (대기업 도입 가능 수준)

P0 + P1 + 다음 12개 추가:

```
[실시간 수집 파이프라인]
🟡 사내 LLM Gateway → Event Hub 실시간 수집
🟡 Gateway 사전 마스킹 (Edge Masking, 옵션 C)
🟡 데모 트리거 (end-to-end 시연 버튼)

[보안 거버넌스]
🟡 Microsoft Entra ID SSO 로그인 + RBAC
🟡 Azure Key Vault 통합 관리
🟡 접근 감사 로그 조회 + 데이터 완전 삭제 + 증빙 발급
🟡 데이터 보관 정책 자동 스케줄링

[배포 유연성]
🟡 배포 모드 설정 (옵션 A/B/C 선택)
🟡 부서/사용자 매핑 관리

[가치 확장]
🟡 보안 교육 필요 영역 추천
```

> **금융·공공·대기업이 도입 가능한 운영 시스템**. 옵션 B(Customer-hosted) / 옵션 C(Edge Masking)까지 지원.

### 📋 도입 단계별 시나리오 (영업 관점)

| 단계 | 적합한 고객 | 환경 |
|:---|:---|:---|
| **P0** | PoC / 중소·중견 기업 / 데모 평가 | SaaS, CSV 입력, 단일 관리자 |
| **P0+P1** | 중견 기업 정식 도입 / 컨설팅 활용 | SaaS, CSV 입력, 분석 정밀도 ↑ |
| **P0+P1+P2** | 대기업 / 금융 / 공공 운영 도입 | Customer-hosted, 실시간 수집, SSO/RBAC |

---

## 📂 문서 자료

- [기능명세서 (CSV)](./docs/feature_specification.csv)
- [포팅 매뉴얼]()
- [API 명세서]()
- [데이터 마스킹 정책서]()

---
