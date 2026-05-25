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
→ 원문 폐기
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

### **7. 부서별 대시보드 (Department-level Only)**

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

## 🔒 책임 있는 AI (보안성 강조 포인트)

| 구분 | 저장 여부 | 설명 |
| :--- | :---: | :--- |
| 프롬프트 원문 | ❌ | 원칙적으로 저장 X, 데모/운영 모두 미보관 권장 |
| 마스킹된 프롬프트 | △ | 대표 예시용으로 선택적 저장, 접근 제한 |
| Embedding Vector | ⚪ (민감) | 원문 유추 가능성을 고려해 민감 데이터처럼 관리 |
| 부서명 | ⚪ | 분석 단위로 사용 |
| 사용자 ID | △ | Hash 처리, 개인별 분석 금지 |
| Token / Cost / Model | ⚪ | 비용 분석용 |
| Cluster ID (업무유형 라벨) | ⚪ | 업무 유형 군집 |
| Risk Score | ⚪ | 보안 위험 관리 |
| Opportunity Score | ⚪ | 자동화 우선순위 추천 |

**보안성 측면 강조 포인트 6가지**

1. **원문 프롬프트 장기 저장 금지** — 마스킹·분류 후 즉시 폐기
2. **개인 단위 분석 금지** — 부서 단위로만 집계 (감시 도구가 아닌 업무 개선 도구)
3. **마스킹 실패 가능성 전제 설계** — 원문 미보관, 접근통제, 보관기간 제한 병행
4. **Embedding도 민감 파생 데이터로 취급** — 접근 권한·보관 기간 제한
5. **Risk Score와 Opportunity Score 분리** — 가치가 높아도 위험도 높으면 ‘보안 검토 필요’로 분류
6. **추천 근거 설명 가능성** — 업무유형 비중·반복성·비용 영향·사용자 수·위험도를 근거로 설명

> 본 서비스는 프롬프트 원문을 장기 저장하지 않는다. 입력된 프롬프트는 Azure 환경 내에서 즉시 개인정보·기밀정보 마스킹을 거친 뒤, 업무 유형 분류와 임베딩 생성에만 사용된다. 저장되는 데이터는 부서 단위 통계, 군집 ID, 위험도 점수, 자동화 기회 점수이며, 개인별 감시가 아니라 부서 단위 업무 개선을 목적으로 한다.

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
      <td>데이터베이스</td>
      <td>
        <img src="https://img.shields.io/badge/Azure%20SQL%20Database-CC2927?style=flat&logo=microsoftsqlserver&logoColor=white"/>
        <img src="https://img.shields.io/badge/Azure%20Blob%20Storage-0078D4?style=flat&logo=microsoftazure&logoColor=white"/>
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

```
[사용자/관리자]
      |
      v
[React Dashboard - Azure Static Web Apps]
      |
      v
[FastAPI Backend - Azure App Service]
      |
      |----------------------
      |                      |
      v                      v
[Blob Storage]         [Azure SQL DB]
CSV 원본 임시저장        분석 결과 저장
(마스킹 후 원문 삭제)    부서별 통계 저장
                            |
                            v
                  [Dashboard API 조회]
```

**AI 처리 흐름**

```
CSV 업로드
  ↓
Prompt Text 추출
  ↓
PII/기밀정보 탐지
  ↓
마스킹
  ↓
Azure OpenAI 업무유형 분류
  ↓
Embedding 생성
  ↓
Clustering
  ↓
Risk Score 계산
  ↓
Opportunity Score 계산
  ↓
자동화 후보 추천
  ↓
부서별 대시보드 표시
```

---

## ☁️ 사용 Azure 리소스

### 필수 리소스

| 용도 | Azure 리소스 | 사용 이유 | 우선순위 |
| :--- | :--- | :--- | :---: |
| 프론트 배포 | Azure Static Web Apps | React 대시보드 배포 | P0 |
| 백엔드 API | Azure App Service | FastAPI 서버 배포 | P0 |
| LLM 분류/추천 | Azure OpenAI Service | 업무유형 분류, 추천 근거 생성 | P0 |
| 임베딩 | Azure OpenAI Embedding Model | 프롬프트 군집화용 벡터 생성 | P0 |
| DB | Azure SQL Database | 부서별 통계, 점수, 추천 결과 저장 | P0 |
| 파일 저장 | Azure Blob Storage | CSV 업로드 파일 임시 저장 | P0 |
| 시크릿 관리 | App Service Environment Variables | API Key, DB URL 관리 | P0 |
| 모니터링 | Application Insights | API 오류/호출 로그 확인 | P1 |

### 선택 리소스

| 용도 | Azure 리소스 | 사용 이유 | 우선순위 |
| :--- | :--- | :--- | :---: |
| 인증 | Microsoft Entra ID | 관리자 로그인/권한 관리 | P1~P2 |
| Key 관리 | Azure Key Vault | OpenAI Key, DB Password 보안 관리 | P1 |
| 배포 자동화 | GitHub Actions | main 브랜치 push 시 자동 배포 | P1 |
| 컨테이너 배포 | Azure Container Apps | App Service 대체 가능 | P2 |
| 검색/RAG | Azure AI Search | 문서 검색형 추천까지 확장 시 사용 | P2 |
| 서버리스 작업 | Azure Functions | CSV 분석 비동기 처리 | P2 |

---

## 🗂️ 주요 구현사항 (우선순위)

| 우선순위 | 파트 | 구현사항 | Azure 리소스 |
| :---: | :--- | :--- | :--- |
| P0 | Data/AI | CSV 로그 업로드, PII/기밀정보 마스킹 | Blob Storage, Azure OpenAI |
| P0 | AI/ML | 프롬프트 업무유형 분류 | Azure OpenAI |
| P0 | Back | 부서별 통계 API, 점수 계산 API | App Service / Container Apps |
| P0 | Front | 부서별 대시보드, 추천 자동화 후보 화면 | Static Web Apps |
| P0 | DB | 분석 결과 저장 | Azure SQL / PostgreSQL |
| P1 | AI/ML | Embedding + Clustering | Azure OpenAI Embedding, sklearn |
| P1 | Front | Risk Score / Opportunity Score 시각화 | Static Web Apps |
| P1 | Back | 추천 근거 설명 API | Azure OpenAI |
| P2 | Infra | 인증, 접근권한, 로그 | Microsoft Entra ID, App Insights |
| P2 | Demo | 샘플 데이터 기반 데모 시나리오 | Blob, DB |

---

## 📂 문서 자료

- [포팅 매뉴얼]()
- [API 명세서]()
- [데이터 마스킹 정책서]()

---
