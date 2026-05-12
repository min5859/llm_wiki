---
title: "finance-analysis-nextjs — 한국 기업 재무분석 대시보드"
domain: personal
sensitivity: internal
tags: ["nextjs", "prisma", "ai-extract", "dart-api", "valuation", "ma", "pdf-extraction"]
created: 2026-04-30
updated: 2026-05-13
sources:
  - "session-logs/20260430-174408-1a2e-*.md"
  - "session-logs/20260501-233118-b6e0-*.md"
  - "session-logs/20260505-101659-115c-*.md"
  - "session-logs/20260512-231800-c191-*.md"
confidence: medium
related:
  - "wiki/projects/japa-asset-dashboard.md"
  - "wiki/analyses/ai-valuation-trustworthiness.md"
  - "wiki/analyses/pdf-text-extraction-vs-ocr.md"
  - "wiki/patterns/vercel-timeout-browser-direct-api.md"
  - "wiki/analyses/financial-health-composite-scores.md"
  - "wiki/patterns/ai-token-usage-cost-guard.md"
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

## 코드 깊이 분석으로 발견한 구조적 결함 (2026-04-30)

위 사용자 진단을 코드로 추적해 보니, 사소한 버그가 아니라 **여러 레이어에서 동시에** 신뢰도를 무너뜨리는 구조 문제로 확인됐다. 4가지 모두 데이터 품질·계산 정확성·실패 가시성과 직결된다.

### 1) 밸류에이션 — AI 환각 + 수식 결함의 이중 문제

| 문제 | 위치 | 결과 |
|---|---|---|
| AI 밸류에이션이 **100% AI 환각 기반** | `/api/valuation` | LLM 에 "EBITDA/DCF 로 평가" 만 던져 할인율·성장률·멀티플을 모두 지어내게 함. 수식 기반 계산이 아님. |
| **EV 계산 오류** | `multiples.ts` | PER/PBR (이미 주주가치) 에 총부채를 더해 EV 를 구함 → 논리 오류. |
| **Net Debt 정의 오류** | 수동 DCF | `netDebt = 총부채` (현금 미차감). 정확하게는 `차입금 - 현금성자산`. |
| **EBITDA 근사** | 수동 DCF | `영업이익 × 1.3` 으로 가정 (감가상각비 미추출). 업종별 D&A 비중이 다른데 일률 적용. |
| **FCF 폴백** | 수동 DCF | FCF 데이터 없으면 `영업이익 × 0.7` 자동 대체 → 사용자에게 "추정값" 표시 안 됨. |
| **WACC ≤ 성장률 가드 부재** | 수동 DCF | gordon growth 공식 분모가 0 또는 음수 가능 → 비현실적 valuation. |

### 2) 단위 불일치 — 강제 장치 부재

- `finance_format.json` 에 "억원" 이라고 **주석만** 있고 변환·검증 코드가 없음.
- DART (원), 감사보고서 (백만원/천원) 가 제각각인데 **단위 변환을 전적으로 AI 에 위임** → 1,000배 ~ 1억배 오차 발생 가능.
- 추출 후 **회계 항등식 검증** (`총자산 = 총부채 + 자본총계`) 도 없음.

### 3) 빈 인풋 → 가짜 데이터 — 5곳에서 동시 발생

| 위치 | 안티 패턴 |
|---|---|
| `prompt.txt` 규칙 3 | "빈 필드는 최대한 데이터에서 찾아서 채우세요" → AI 에게 **날조를 지시**. |
| `DataLoader` | 모든 `null` → `0` 변환. **"데이터 없음"과 "실제 0"이 구분 불가**. |
| `DCFForm` | FCF 가 0 이면 `baseFcf = 100` (억원) 으로 하드코딩. |
| `multiples.ts` | FCF 없으면 `영업이익 × 0.7`, EBITDA 없으면 `영업이익 × 1.3`. |
| `finance_format.json` | 템플릿에 **풍전비철의 실제 데이터가 예시값으로 박혀 있음** → AI 가 추출 실패 시 이 값을 그대로 써 다른 회사의 결과로 노출. |

`null`/`0` 구분이 안 되는 이상 어떤 다운스트림 검증도 의미가 없다 — 가장 먼저 풀어야 할 결함.

### 4) PDF 인식율 — 텍스트 PDF 만 지원, 스캔 PDF 침묵 실패

- `pdf-parse` 는 **텍스트 레이어만 추출** → 스캔 PDF (이미지 PDF) 는 빈 텍스트 반환.
- 빈 텍스트가 반환되어도 **에러 없이 진행** → AI 가 빈 입력으로 전체 재무데이터를 날조.
- **20,000자 하드 트렁케이션** — 긴 감사보고서 뒷부분 재무제표가 잘릴 수 있음.
- 연결/별도 재무제표 구분 없음.

일반 패턴은 [[pdf-text-extraction-vs-ocr]] 참고.

## 개선 백로그 (제안 우선순위)

### 1단계 — 신뢰도 기반 (시급)

- `null` ↔ `0` 구분 복원: `DataLoader` 의 `null → 0` 변환 제거, 차트/UI 에서 `null` 핸들링.
- `prompt.txt` 규칙 3 변경: "없는 데이터는 `null` 로 표시, **절대 추정하지 말 것**".
- **데이터 완성도 스코어** 노출: 핵심 필드 중 몇 % 가 실제 데이터인지 표시.
- **밸류에이션 시작 전 프리플라이트 체크**: 핵심 데이터 누락 시 경고.
- `finance_format.json` 의 풍전비철 실제값을 **placeholder 문자열** 로 교체.

### 2단계 — 밸류에이션 로직 수정

- `/api/valuation` 을 **"AI 가 가정값 제안 → 사용자 확인/수정 → 수식 기반 계산"** 으로 전환.
- `Net Debt = 차입금 - 현금성자산` 으로 수정.
- 감가상각비 (D&A) 추출 → 진짜 EBITDA 계산.
- `multiples.ts` 의 EV 계산을 PER/PBR 와 EV-EBITDA 로 분리.
- `WACC ≤ 성장률` 가드 추가.
- 밸류에이션 결과에 **신뢰도 등급 + 데이터 완성도** 함께 표시.

### 3단계 — 단위 자동화 + DART 다년도

- 추출 JSON 에 **`unit` 메타데이터** 필드 (원본 단위 + 변환 단위).
- 추출 후 **회계 항등식 자동 검증**.
- DART 데이터는 원 단위가 확정 → **코드 레벨에서 억원 변환** (AI 위임 제거).
- DART 다년도 자동 수집 (현재 1년 → 3~5년).

### 4단계 — PDF OCR + M&A 기능

- OCR 엔진 추가: Tesseract.js (클라이언트) 또는 Google Vision / Upstage Document AI (외부).
- 빈 텍스트 감지 시 "스캔 PDF 입니다. OCR 필요" 경고 반환.
- `financial-page-detector.ts` 로 감지된 재무제표 페이지만 AI 에 전송 (트렁케이션 회피).
- 연결/별도 재무제표 구분 로직.

### M&A 활용 신규 기능 후보

| 우선순위 | 기능 | 비고 |
|---|---|---|
| 높음 | 다중 기업 비교 분석 | 타겟 후보 여러 개 나란히 비교 |
| 높음 | 시나리오 분석 강화 | 시너지 / 인수 프리미엄 / 통합 비용 시뮬레이션 |
| 높음 | 데이터 신뢰도 리포트 / audit trail | 각 수치의 출처 (DART 실제값 / AI 추출값 / 추정값) 추적 |
| 높음 | DART 다년도 자동 수집 | 추세 분석 강화 |
| 중간 | Due Diligence 체크리스트 | 소송 / 감사의견 / 특수관계자 거래 등 자동 점검 |
| 중간 | 산업 멀티플 DB | 업종별 PER/PBR/EV-EBITDA 평균 내장 → 상대가치 신뢰도 향상 |
| 중간 | 리포트 자동 생성 | M&A 투자 심의 보고서 형식 PDF |
| 중간 | 사용자 가정값 관리 | 할인율 / 성장률 시나리오 저장·비교 |
| 낮음 | 실시간 주가 연동 | 시가총액 대비 평가가치 괴리율 |
| 낮음 | 비상장사 비유동성 할인 | 소수지분 할인 적용 |
| 낮음 | 거래 구조 시뮬레이터 | 주식매수 / 자산양수 / 합병 세후 효과 비교 |

## 운영 측 약점 — 데이터 누적·삭제 경로 부재 (2026-05-01 추가)

기능 제안 검토 중 코드를 다시 훑다가 발견한 운영성 결함. 모두 **사용자 데이터 정리 경로가 전혀 없는** 데서 비롯된다.

### 1) `analyses` 테이블이 누적만 됨, 갱신 안 됨

`src/app/api/companies/route.ts:78` 에서 `prisma.analysis.create` 만 호출. `upsert` / `update` 가 없어서 **같은 회사를 N 번 분석하면 row 가 N개 쌓인다**. 읽기 쪽 `[name]/route.ts` 는 `findFirst` + `orderBy: { createdAt: 'desc' }` 로 가장 최근 것만 보므로 동작은 정상이지만, `analyses` 테이블은 시간이 지나면 같은 회사 row 로 부푼다.

### 2) UI / API 어디에도 DELETE 경로가 없음

- API 라우트 전수 점검: `companies/route.ts` 는 `findMany` + `upsert`, `companies/[name]/route.ts` 는 `GET` 만, `valuation/route.ts` 는 `POST` 만. **`DELETE` 핸들러가 하나도 없음**.
- 클라이언트 쪽도 `fetch(..., { method: 'DELETE' })` 호출 없음.
- 결과: 한번 저장된 데이터는 Prisma Studio 또는 직접 SQL 로만 삭제 가능 → **수동 정리 부담**.

좋은 소식: `prisma/schema.prisma` 에서 `Company → FinancialStatement / Analysis → Valuation` 관계에 모두 `onDelete: Cascade` 가 이미 걸려 있어서 **`Company` 한 줄만 지우면 연관 데이터가 자동 cascade**. 즉 구현은 매우 가볍다 — `[name]/route.ts` 에 `DELETE` 핸들러 + UI 의 휴지통 + 회사명 직접 입력 같은 가드 정도면 충분.

### 3) `financial_statements` 테이블은 스키마만 있고 실제 사용 안 됨

`grep` 으로 코드 전수 점검해도 `prisma.financialStatement.*` 호출이 한 군데도 없음. BS/IS/CF 를 정규화해 분리 저장하려던 흔적이지만 현재는 모든 재무 데이터가 `analyses.financialData` JSON 안에 통째로 묶여 들어간다 — 그래서 쿼리·검증이 어렵다는 약점이 그대로 남음 (위 §1단계 백로그의 정규화 필요성과 같은 뿌리).

### 4) `provider` 필드가 사실상 하드코딩

`companies` POST 가 항상 `'anthropic'` 으로 저장 → DB 레벨에는 멀티 프로바이더 (Claude/GPT/Gemini/DeepSeek) 가 반영되지 않음. UI 에선 멀티 프로바이더 선택지가 있어도 DB 검색·필터링은 의미 없는 상태.

## 백로그 추가

위 결함을 반영해 운영 측 백로그를 추가:

| 우선 | 항목 | 비고 |
|---|---|---|
| 높음 | `companies/[name]` 에 `DELETE` 핸들러 + UI 휴지통 (확인 다이얼로그 + 회사명 직접 입력) | onDelete Cascade 가 있어서 1줄 구현 |
| 중간 | `analysis` upsert 또는 일별 cleanup — 같은 (companyId, reportYear, provider) 키로 row 누적 방지 | 누적 row 가 의미 있는 시계열은 아니므로 최신만 유지가 자연스러움 |
| 중간 | `provider` 하드코딩 제거 — 실제 호출에 사용한 프로바이더를 DB 에 기록 | DB 레벨 필터링·통계 가능 |
| 낮음 | `financial_statements` 정규화 활성화 또는 스키마 제거 | 미사용 표 제거하거나 §1단계 백로그와 연동 |

## Vercel 60초 timeout 우회 — 임시 client-direct Anthropic 패턴 (2026-05-04 hotfix, 회수 대상)

큰 PDF 의 vision 분석이 Vercel Hobby 의 60초 lambda timeout 에 걸려 서버에서 완료되지 못하는 사고를 일시 우회한 commit (`215b9ff TEMPORARY: bypass Vercel timeout via client-direct Anthropic call for PDF`). 인증된 사용자에게 `ANTHROPIC_API_KEY` 를 직접 내려주고 브라우저에서 Anthropic SDK (`dangerouslyAllowBrowser: true`) 로 호출해 timeout 제한을 사라지게 만드는 escape hatch.

| 추가/변경 | 역할 |
|---|---|
| `src/app/api/anthropic-config/route.ts` (신규) | `auth()` 통과 시 `{ apiKey, model, system }` 응답. 미인증 401. 머리말에 ⚠️ TEMPORARY 마킹 + 회수 조건 명시. |
| `src/lib/anthropic-browser.ts` (신규) | `fetchAnthropicConfig` / `fileToBase64` / `extractFinanceFromPdfDirect` 헬퍼. `dangerouslyAllowBrowser: true` + tool_use 강제로 structured JSON 출력. |
| `src/app/page.tsx` | `processPdfFile` 에 분기 — `provider === 'anthropic'` 또는 OCR 강제 시 client-direct, 그 외 server multipart 경로 유지. 진행 메시지 "1~5분 소요" 로 갱신. |
| `src/proxy.ts` | 미인증 + `/api/*` 경로 → HTML redirect 대신 JSON 401 응답. 클라이언트 fetch 가 res.ok 분기로 깔끔히 처리. |
| `docs/tasks/todo.md` | G 섹션 신설 — 보안 부채 추적, 회수 조건, 회수 시 작업 체크리스트. |

**보안 부채**: DevTools 로 키 추출 가능. 회수 조건 (Pro 업그레이드 / Cloud Run / 외부 워커 도입 중 하나 완료) 충족 시 즉시 회수해야 하고, 회수 절차에 **`ANTHROPIC_API_KEY` 회전**이 반드시 포함되어야 한다 — 한 번이라도 클라이언트에 내려간 키는 유출 전제로 다뤄야 한다. 일반 패턴과 회수 체크리스트는 [[vercel-timeout-browser-direct-api]] 참고.

## 관련 맥락

- 자산 보유 측의 [[japa-asset-dashboard]] 와는 별도 프로젝트. japa는 본인 자산 추적, finance-analysis-nextjs는 외부 기업 분석.
- M&A 가치평가 도구로의 발전을 염두에 두고 있어, 단순 대시보드가 아니라 "측정 신뢰도" 와 "검증 가능성" 이 핵심 요구사항이 된다.
- Vercel timeout 의 또 다른 갈래 (cron / DB acquire 누적) 는 [[vercel-cron-best-practices]], [[prisma-connection-pool-vercel-supabase]].

## 변경 이력

- 2026-04-30: 최초 생성. 프로젝트 구조·기능·약점 정리 (출처: session-logs/20260430-174408-1a2e-*)
- 2026-04-30: 코드 깊이 분석 추가 — 4가지 구조적 결함 (AI 환각 + 수식 오류 / 단위 강제 부재 / 빈 인풋 → 가짜 데이터 5곳 / 스캔 PDF 침묵 실패) 식별, 4단계 우선순위 백로그 + M&A 기능 11개 후보 정리. 일반 패턴은 [[ai-valuation-trustworthiness]], [[pdf-text-extraction-vs-ocr]] 로 분리.
- 2026-05-02: 운영성 결함 4가지 추가 — analyses 누적 / DELETE 경로 부재 / financial_statements 미사용 / provider 하드코딩. 관련 백로그 4개 추가. PDF 내보내기 (`pdf-generator.ts` 의 `downloadPdf`/`downloadFullReportPdf` + `html2canvas-pro`/`jspdf`) 가 이미 구현되어 있음을 확인 (출처: session-logs/20260501-233118-b6e0-*)
- 2026-05-05: Vercel 60초 timeout 우회를 위한 임시 client-direct Anthropic 패턴 도입 사항 기록 (commit `215b9ff`). `/api/anthropic-config` + `lib/anthropic-browser.ts` + `proxy.ts` JSON 401 갱신. 일반 패턴은 [[vercel-timeout-browser-direct-api]] 로 분리. 회수 조건과 키 회전 의무 명시 (출처: session-logs/20260505-101659-115c-*)
