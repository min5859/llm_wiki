---
title: "PDF 텍스트 추출 vs OCR — 침묵 실패 차단 패턴"
domain: both
sensitivity: public
tags: ["pdf", "ocr", "pdf-parse", "tesseract", "text-extraction", "silent-fail", "data-quality"]
created: 2026-05-01
updated: 2026-05-01
sources:
  - "session-logs/20260430-174408-1a2e-*.md"
confidence: high
related:
  - "wiki/analyses/ai-valuation-trustworthiness.md"
  - "wiki/projects/finance-analysis-nextjs.md"
---

# PDF 텍스트 추출 vs OCR — 침묵 실패 차단 패턴

`pdf-parse` / `pdftotext` / `pdfjs-dist` 같은 라이브러리는 **PDF 의 텍스트 레이어만 추출**한다. 스캔된 종이 문서를 PDF 로 만든 경우 (이미지만 들어 있고 텍스트 레이어가 없는 PDF) 에는 빈 문자열이 반환되며, 라이브러리는 에러를 던지지 않는다. 다운스트림 파이프라인이 빈 입력을 받고도 진행하면 LLM 이 백지 입력으로 결과를 날조하는 사고로 이어진다.

## PDF 의 두 종류

| 유형 | 텍스트 레이어 | 텍스트 추출 | OCR 필요 |
|---|---|---|---|
| **디지털 네이티브 PDF** (Word/HWP/LaTeX → Export) | ✅ 있음 | ✅ 정상 | ❌ |
| **스캔 PDF** (종이를 스캐너 / 스마트폰 카메라로 캡처) | ❌ 없음 (이미지만) | **빈 문자열** | ✅ 필수 |
| **하이브리드 PDF** (디지털인데 일부 페이지만 스캔, 표/도장 영역만 이미지) | 부분적 | 부분적 (표/도장 누락) | 부분 영역만 |

한국 기업의 감사보고서·사업보고서는 **하이브리드** 가 매우 흔하다 — 본문은 텍스트인데 첨부된 재무제표가 스캔본인 경우. 이 패턴이 가장 위험하다 (일부 추출되니 빈 문자열 검증을 통과해 버린다).

## 안티 패턴: 빈 입력 침묵 통과

```ts
// BAD: 빈 텍스트도 그대로 진행
const buffer = await readFile(pdfPath);
const { text } = await pdfParse(buffer);
const extractedJson = await ai.extract(text);  // text 가 "" 인데도 호출됨
return extractedJson;  // AI 가 "재무 데이터를 모르겠어서 평균값으로 채워봅니다" 같은 환각
```

이 안티 패턴이 위험한 이유:

1. **에러가 안 나서 사용자 모름** — 업로드 → "추출 완료" 토스트 → 결과는 가짜.
2. **AI 가 빈 입력을 거부하지 않음** — 프롬프트에 "데이터에서 찾아서 채우세요" 같은 지시가 있으면 학습 분포의 평균값으로 채움.
3. **하류 검증 의미 없음** — 단위 검증, 회계 항등식 검증을 통과해 버린다 (가짜이지만 일관된 가짜).

## 수정 패턴 1: 빈 텍스트 즉시 차단

```ts
const { text } = await pdfParse(buffer);
const trimmed = text.replace(/\s/g, '');

if (trimmed.length < 100) {
  throw new Error(
    'PDF_NO_TEXT_LAYER: 이 PDF 는 스캔 문서이거나 텍스트 레이어가 없습니다. ' +
    'OCR 처리가 필요합니다.'
  );
}

if (trimmed.length < 1000) {
  // 일부만 추출됨 (하이브리드 PDF 가능성) — 사용자 경고 + 계속 진행
  console.warn('PDF_PARTIAL_TEXT: 추출된 텍스트가 너무 짧습니다. 일부 페이지가 스캔본일 수 있습니다.');
}
```

임계값은 도메인에 따라 다름. 재무제표 PDF 라면 최소 1,000자 이상 (회계 단어가 충분히 들어 있을 만한 양) 을 권장.

## 수정 패턴 2: OCR 폴백 자동 결정

빈 텍스트 감지 시 **자동으로 OCR 로 재시도** 하는 흐름을 둘 수 있다. 단 OCR 은 느리고 비싸므로 명시적 사용자 컨펌이 권장:

```ts
async function extractPdfText(buffer: Buffer): Promise<{ text: string; method: 'native' | 'ocr' }> {
  // 1) 네이티브 텍스트 레이어 시도
  const { text } = await pdfParse(buffer);
  const trimmed = text.replace(/\s/g, '');

  if (trimmed.length >= 1000) {
    return { text, method: 'native' };
  }

  // 2) OCR 폴백 (사용자가 동의했을 때만)
  if (await userConfirmsOcr()) {
    const ocrText = await runOcr(buffer);
    return { text: ocrText, method: 'ocr' };
  }

  throw new Error('PDF_NO_TEXT_LAYER: OCR 사용을 거부함. 다른 PDF 를 업로드해 주세요.');
}
```

UI 에서 `method: 'ocr'` 일 때 "이 추출은 OCR 로 수행됨 — 정확도가 낮을 수 있음" 배너를 노출.

## OCR 엔진 선택지

| 엔진 | 종류 | 한국어 정확도 | 비용 | 비고 |
|---|---|---|---|---|
| **Tesseract.js** | 클라이언트 / 서버 OSS | 보통 | 무료 | 한국어 traineddata 별도 다운로드. 표/숫자 인식은 약함. |
| **Google Cloud Vision (Document AI)** | API | 높음 | $1.50/1000p | 표·레이아웃 인식 우수. 페이지당 과금. |
| **AWS Textract** | API | 보통 | $0.0015/p (table $0.015/p) | 표 추출 강함. 한국어 비공식 지원. |
| **Upstage Document AI** | 한국 기업 API | **매우 높음** (한국어 / 한국 양식 특화) | 페이지당 과금 | 한국 사업/감사 보고서·계약서 / 영수증 특화. |
| **Azure Document Intelligence** | API | 높음 | $1.50/1000p | 사전훈련 모델 (invoice, receipt 등) 풍부. |

**한국 재무 PDF** 의 경우 Upstage > Google Vision > Azure 순서로 정확도가 우월한 것으로 알려져 있다 (커뮤니티 평가 기준, 직접 측정 추천).

## 수정 패턴 3: 텍스트 트렁케이션 안전 처리

LLM 컨텍스트 한계로 추출된 텍스트를 자르는 케이스가 흔하다. 단순 `.slice(0, 20000)` 은 위험:

```ts
// BAD: 뒷부분 재무제표가 잘릴 수 있음 (긴 감사보고서의 경우)
const truncated = extractedText.slice(0, 20_000);
```

`pdf-parse` 는 페이지별 정보도 제공하므로, **재무제표 페이지만 선별** 해서 보내는 편이 손실이 적다:

```ts
// 페이지별로 키워드 가중치를 계산
function detectFinancialPages(pages: { text: string; pageNum: number }[]): number[] {
  const keywords = ['재무상태표', '손익계산서', '현금흐름표', '자본변동표', '연결재무제표', '주석'];
  return pages
    .map(p => ({
      pageNum: p.pageNum,
      score: keywords.reduce((s, k) => s + (p.text.includes(k) ? 1 : 0), 0),
    }))
    .filter(p => p.score > 0)
    .sort((a, b) => b.score - a.score)
    .slice(0, 10)  // 상위 10 페이지만
    .map(p => p.pageNum);
}
```

이 방식은 `finance-analysis-nextjs` 의 `financial-page-detector.ts` 가 시도하던 패턴.

## 연결재무제표 vs 별도재무제표 구분

한국 상장사는 **연결재무제표 (consolidated)** 와 **별도재무제표 (separate)** 두 종류를 모두 발행한다. 둘이 섞이면 valuation 이 완전히 틀어진다.

| 항목 | 연결 | 별도 |
|---|---|---|
| 자회사 포함 | ✅ | ❌ |
| 매출액 | 보통 더 큼 | 본사만 |
| 자산 / 부채 | 그룹 합산 | 본사만 |
| M&A 분석 시 우선순위 | **연결** (그룹 전체 가치) | 본사 단독 분석 시 |

PDF 헤더 / 목차에서 "연결" 또는 "별도" 키워드를 명시 추출 후 metadata 로 보존:

```ts
function detectStatementType(text: string): 'consolidated' | 'separate' | 'unknown' {
  if (/연결\s*재무제표|consolidated/i.test(text)) return 'consolidated';
  if (/별도\s*재무제표|separate/i.test(text)) return 'separate';
  return 'unknown';
}
```

`'unknown'` 인 경우 사용자에게 명시적으로 묻기 — AI 가 추측하게 두면 안 된다.

## 종합 — 추출 파이프라인 7체크리스트

```
PDF 업로드
   ↓
1) 텍스트 레이어 추출
   ↓
2) 빈 텍스트 감지 → 차단 OR OCR 폴백
   ↓
3) 페이지별 분류 (재무제표 페이지 선별)
   ↓
4) 연결 / 별도 구분 metadata 추출
   ↓
5) 트렁케이션 (선별된 페이지만)
   ↓
6) AI 추출 호출
   ↓
7) 결과에 method (native / ocr), statement_type (연결 / 별도), 추출 페이지 정보 포함
```

각 단계에서 **실패는 다음 단계로 침묵 통과시키지 말 것** — 빈 입력 / 짧은 입력 / 분류 실패 시 사용자에게 명시 노출.

## 일반화 — 입력 단계 침묵 실패의 패턴

본 패턴은 PDF 한정이 아니다. 외부 입력을 LLM 에 넘기는 시스템 일반에 적용:

| 입력 종류 | 침묵 실패 형태 |
|---|---|
| 웹 스크래핑 | JS 렌더링 페이지를 정적 HTML 로 받아 빈 본문 |
| 음성 트랜스크립트 | 무음 / 잡음 구간을 빈 자막으로 반환 |
| OCR 결과 | 신뢰도 낮은 글자를 그냥 비우고 진행 |
| 이미지 인식 | 객체 미검출 = `[]` 반환 (실패와 동일 표현) |
| API 응답 | partial response (필드 일부 누락 + 200 OK) — [[yahoo-finance-concurrent-silent-fail]] 참고 |

공통 원칙: **"빈 결과 = 정상 결과 0건" 인지, "추출 / 인식 / 통신 실패" 인지를 반드시 구분**. 하나의 같은 표현 (`""`, `[]`, `null`) 으로 묶지 말 것.

## 변경 이력

- 2026-05-01: 최초 생성. finance-analysis-nextjs 의 PDF 인식율 저하 디버깅 (스캔 PDF 침묵 실패 + 20,000자 하드 트렁케이션 + 연결/별도 미구분) 을 일반 패턴으로 추출 (출처: session-logs/20260430-174408-1a2e-*).
