---
title: "finance-analysis-nextjs — 한국 기업 재무분석 대시보드"
domain: personal
sensitivity: internal
tags: ["nextjs", "prisma", "ai-extract", "dart-api", "valuation", "ma", "pdf-extraction"]
created: 2026-04-30
updated: 2026-04-30
sources:
  - "session-logs/20260430-174408-1a2e-*.md"
confidence: medium
related:
  - "wiki/projects/japa-asset-dashboard.md"
---

# finance-analysis-nextjs — 한국 기업 재무분석 대시보드

PDF/JSON 업로드 또는 DART API로 한국 상장기업 재무데이터를 수집하고, AI로 표준 JSON 으로 구조화한 뒤 12개 슬라이드형 대시보드로 시각화하는 Next.js 앱. 향후 M&A 활용을 위한 기업가치 평가 도구로 발전 계획.

## 핵심 내용

### 기술 스택

| 계층 | 선택 |
|---|---|
| 프레임워크 | Next.js 16, React 19, TypeScript 5 |
| UI | Tailwind CSS 4 |
| 차트 | Chart.js + react-chartjs-2 |
| 상태관리 | Zustand 5 (sessionStorage persist) |
| AI | Anthropic SDK + OpenAI SDK (멀티 프로바이더: Claude, GPT-4o, Gemini, DeepSeek) |
| DB | PostgreSQL + Prisma |
| PDF 입력 | PDF 텍스트 추출 → AI 구조화 |
| PDF 출력 | jsPDF + html2canvas |
| 검증 | Zod |
| 외부 데이터 | DART API (JSZip 으로 응답 처리) |

### 데이터 흐름

1. **데이터 입력** — 3가지 방법
   - JSON 업로드 (이미 표준 형식)
   - PDF 업로드 → 텍스트 추출 → `POST /api/extract` 로 AI 구조화
   - DART API 조회 (검색 / 재무제표 / 기업정보 / 감사보고서)
2. **AI 구조화** — `POST /api/extract` 가 비정형 텍스트 → 표준 재무 JSON 변환
3. **DB 저장** — `Company` + `Analysis` 레코드
4. **대시보드** — 12페이지 (요약, 손익, 재무상태, 현금흐름, 안정성, 수익성, 성장률, 운전자본, 산업비교, AI밸류에이션, 수동밸류에이션, 결론)
5. **밸류에이션** — AI 자동 (EBITDA / DCF) + 수동 (DCF / 상대가치 / 자산가치 / 혼합)
6. **PDF 내보내기** — 전체 리포트를 A4 PDF로 생성

### API 라우트

| 경로 | 기능 |
|---|---|
| `/api/companies` | 회사 목록 조회/저장 |
| `/api/companies/[name]` | 특정 회사 분석 데이터 조회 |
| `/api/config` | API 키 설정 상태 확인 |
| `/api/dart` | DART 검색/재무제표/기업정보/감사보고서 |
| `/api/upload` | PDF/JSON 파일 업로드 및 텍스트 추출 |
| `/api/extract` | AI로 텍스트 → 재무 JSON 변환 |
| `/api/valuation` | AI 기업가치 평가 |

### DB 스키마

- **Company** — 기업 기본정보 (이름, DART코드, 종목코드, 업종)
- **Analysis** — 재무분석 JSON 데이터 (`Company`에 연결)
- **Valuation** — AI 밸류에이션 결과 (`Analysis`에 연결)
- **FinancialStatement** — 정의되어 있으나 **미사용** (이상치)

### 주요 디렉터리

```
src/
├── app/
│   ├── (dashboard)/    # 12개 분석 페이지
│   ├── api/            # API 라우트
│   ├── companies/      # 회사 목록·상세
│   └── valuation/[name]/  # 종목별 밸류에이션
├── features/
│   ├── dart/
│   ├── financial-analysis/
│   └── valuation/
├── lib/
│   ├── ai-client.ts            # 멀티 AI 프로바이더 추상화
│   ├── financial-page-detector.ts  # PDF 페이지 분류
│   ├── pdf-generator.ts        # 리포트 PDF 출력
│   └── parse-ai-response.ts
└── prisma/             # 스키마 + 마이그레이션 + seed
```

## 알려진 약점 (사용자 진단)

향후 개선 백로그로 식별된 항목 (2026-04-30):

1. **밸류에이션 신뢰도가 낮음** — 단위 표시가 일정하지 않고, 인풋 데이터가 부족해도 AI가 "가라"로 생성해서 평가하는 경향
2. **데이터는 모두 `Analysis.financialData` JSON 필드에 통째로** 저장됨 → 정규화된 `FinancialStatement` 테이블 미활용 → 쿼리·검증 어려움
3. **PDF 인식율이 낮음** — 텍스트 추출이 거친 PDF (스캔 / 표 많음) 에서 실패 빈도 높음
4. **sessionStorage 기반 상태** → 탭 간 데이터 공유 불가

## 관련 맥락

- 자산 보유 측의 [[japa-asset-dashboard]] 와는 별도 프로젝트. japa는 본인 자산 추적, finance-analysis-nextjs는 외부 기업 분석.
- M&A 가치평가 도구로의 발전을 염두에 두고 있어, 단순 대시보드가 아니라 "측정 신뢰도" 와 "검증 가능성" 이 핵심 요구사항이 된다.

## 변경 이력

- 2026-04-30: 최초 생성. 프로젝트 구조·기능·약점 정리 (출처: session-logs/20260430-174408-1a2e-*)
