---
title: "재무 건전성 합성 스코어 — Altman Z / Piotroski F / Beneish M + 룰 기반 risk flag"
domain: both
sensitivity: public
tags: ["finance", "valuation", "z-score", "f-score", "m-score", "distress", "risk-flag"]
created: 2026-05-13
updated: 2026-05-13
sources:
  - "session-logs/20260512-231800-c191-*.md"
confidence: medium
related:
  - "wiki/projects/finance-analysis-nextjs.md"
  - "wiki/analyses/ai-valuation-trustworthiness.md"
---

# 재무 건전성 합성 스코어 — Altman Z / Piotroski F / Beneish M + 룰 기반 risk flag

재무제표 (BS / IS / CF) 데이터만으로 *LLM 호출 0* 으로 계산할 수 있는 부실/건전성 합성 스코어 3종 + 룰 기반 risk flag 분류 체계. 분석 대시보드의 "AI 인사이트" 옆에 **신뢰감 큰 향상**을 1일 작업으로 추가할 수 있는 영역.

## 3대 합성 스코어 비교

| 스코어 | 측정 | 산식 핵심 | 필요 데이터 | 라벨 (대략) |
|---|---|---|---|---|
| **Altman Z-Score** | 부실 위험 | 5개 비율 가중합 (운전자본/총자산, 이익잉여금/총자산, EBIT/총자산, 시가총액/총부채, 매출/총자산) | 운전자본, 이익잉여금, EBIT, 시가총액, 매출 (절대값) | < 1.81 부실 / 1.81~2.99 회색 / > 2.99 안전 |
| **Piotroski F-Score** | 건전성 (개선 추세) | 9개 (또는 8개) 이진 체크의 합 — ROA, OCF, ROA 개선, OCF>NI(accruals), 부채 추세, 유동비율 추세, 영업이익률 추세, 자산회전율 추세 | 2년 이상 데이터 (전년 비교 필수) | 0~3 약함 / 4~6 보통 / 7~9 강함 |
| **Beneish M-Score** | 분식 의심 | 8개 비율 가중합 (DSRI/GMI/AQI/SGI/DEPI/SGAI/LVGI/TATA) | 매출채권, 매출원가, 자산, 매출, 감가상각, 판관비, 부채 추세 | < -2.22 정상 / > -1.78 의심 |

### 실제 적용에서의 주의

- **Z-Score 는 운전자본·이익잉여금 절대값이 필요**. PDF 추출 기반 시스템에선 이 두 값이 누락되는 경우가 흔해서, 정작 계산이 안 됨. 대안으로 *단순화된 부실 신호* (부채비율, 이자보상배율, 영업이익 부호, OCF<NI 3년 연속) 룰 기반으로 safe/watch/danger 3단계 라벨링이 현실적
- **F-Score 의 "insufficient" fallback** — 8개 체크 중 prior-year 데이터가 없어 평가 불가한 항목이 너무 많으면 (예: 3개 미만) `insufficient` 로 마킹. "낮은 점수" 와 "데이터 부족" 을 구별해야 사용자가 오해 안 함
- **M-Score 는 dsri/gmi/aqi 등 비율의 의미가 한국 회계와 미국 GAAP 사이 차이가 있어** 그대로 적용 시 false positive 가 잦다. 학술 검증이 매우 신뢰 가능한 영역이 아니라 "**참고용 신호**" 로 한정

## 룰 기반 risk flag — 합성 스코어 외 보조 알람

합성 스코어가 한 줄 라벨이라면, **각 카테고리의 구체 신호**를 사용자가 직접 보게 하는 보조 카드. 모두 룰 기반 (AI 호출 0).

### 카테고리별 기본 룰셋

| 카테고리 | 신호 |
|---|---|
| **profitability** | 순손실 / 영업손실 / 2년 매출 하락 / ROE 급락 |
| **liquidity** | 유동비율 < 100% (watch) / < 80% (danger) |
| **leverage** | 자본잠식 / 부채 급증 (+50%p YoY) / 부채비율 > 200% (watch) / > 400% (danger) / 이자보상배율 < 1 (좀비 기업) |
| **efficiency** | CCC (현금전환주기) 급증 (+30일 YoY) / DSO (매출채권회전일) 급증 (+30일 YoY) |
| **cash** | 음의 FCF / 영업CF 음수 + 재무CF 양수 (차입금으로 운영) |

### 구현 가이드

각 룰은 **self-contained 함수** 로 작성 — `RiskFlag | null` 반환. 임계값 튜닝이 한 줄 수정.

```ts
function checkInterestCoverage(data: FinancialData): RiskFlag | null {
  const coverage = data.operatingIncome / data.interestExpense;
  if (!isFinite(coverage) || coverage >= 1) return null;
  return {
    category: 'leverage',
    severity: 'danger',
    message: '이자보상배율 1 미만 — 좀비 기업 위험',
    detail: `coverage=${coverage.toFixed(2)}`,
  };
}
```

`severity` 가 sort order 를 만들어 critical 항목이 항상 상단에 렌더되도록 한다. 룰 추가/조정의 변경 비용을 한 줄로 유지.

## 합성 스코어 + risk flag 의 분담

| 도구 | 형태 | 사용자 액션 |
|---|---|---|
| 합성 스코어 (Z/F/M) | 한 줄 라벨 + 점수 | 첫 인상. "이 회사 위험한가?" 한눈에 |
| risk flag 카드 | 구체 신호 N건 | "왜 그렇게 판단했나?" 의 근거 |

두 도구가 같은 데이터를 다르게 표현 — 카테고리 카드 위에 합성 스코어 라벨이 떠야 사용자가 신뢰 단계와 근거를 한 번에 본다.

## LLM 호출 0 의 가치

- **속도** — 페이지 로드와 동시에 계산. AI 응답 대기 없음
- **비용** — 0
- **신뢰** — 같은 입력이면 결정적 (deterministic) 출력. AI 환각의 여지가 없음
- **튜닝 가능** — 임계값을 코드로 직접 보고 수정. AI 의 black box 가 아님

따라서 "재무 분석" 시스템에서 LLM 으로 만들고 있는 인사이트 중 **단순 비율 계산이나 룰 기반 신호 detection 으로 표현 가능한 것**은 모두 코드로 회수하는 것이 ROI 가 가장 좋다. AI 의 가치는 비정형 텍스트 해석 / 다항 비교 / 자연어 설명에 한정해 두는 편이 정확.

## 변경 이력

- 2026-05-13: 최초 작성 (출처: session-logs/20260512-231800-c191 — finance-analysis-nextjs 비판적 검토 Phase 1 "F-Score + 단순화된 distress signals" 와 Phase 4 "rule-based risk flag detector" 구현 일반화. 한국 시장에서 Z-Score 의 절대값 데이터 누락 회피 + Beneish 의 false positive 한계 + F-Score 의 insufficient fallback 패턴 정리)
