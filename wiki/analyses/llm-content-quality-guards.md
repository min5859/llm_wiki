---
title: "LLM 기반 자동 콘텐츠 발행의 5 가지 품질 가드"
domain: "ai-agent"
sensitivity: public
tags: ["analysis", "llm", "newsletter", "content-quality", "hallucination", "prompt-engineering", "cjk-leak", "language-enforcement"]
created: 2026-05-12
updated: 2026-06-28
source_session: "20260511-230001-14d5-오늘-dev-blog-주제들이-5월11일-자로-업데이트-되지않았습니다.md"
sources:
  - "session-logs/20260511-230001-14d5-오늘-dev-blog-주제들이-5월11일-자로-업데이트-되지않았습니다.md"
  - "session-logs/20260514-080604-8120-자동-파이프라인-상태-2026-05-14-1개-토픽-실패---9개-성공.-linux-gpu.md"
  - "session-logs/20260628-030337-d36d-#-Linux-Daily-Newsletter-—-Write-from-Dossier-당신은.md"
  - "session-logs/20260628-032046-b0b8-#-Opensource-Trending-Newsletter-—-Write-from-Doss.md"
confidence: medium
related:
  - "wiki/projects/dev-blog.md"
  - "wiki/analyses/llm-news-prediction-pitfalls.md"
  - "wiki/analyses/research-write-agent-separation.md"
---

# LLM 기반 자동 콘텐츠 발행의 5 가지 품질 가드

LLM 으로 매일 자동 발행되는 콘텐츠 (뉴스레터, 트렌딩 큐레이션, 일일 브리핑) 가 "겉으론 그럴듯한데 정독하면 빈약" 상태에 빠지는 5가지 전형적 결함과, 각각의 파이프라인 레벨 가드.

5/11 dev-blog 의 12종 토픽 게시본 정독에서 4가지가 발견됐고, 5/14 운영 사고에서 5번째 (CJK 비한국어 혼입) 가 추가됐다. **자동 콘텐츠 발행 시스템 일반에 적용** 된다.

## 결함 1 — 토픽 간 중복

같은 사실 (예: linux 7.0.6 stable 릴리스) 이 인접한 여러 토픽 (linux-daily / linux-distro-stable / linux-kernel-security) 의 highlight 에 거의 동일 문구로 동시 등장. 다수 토픽을 함께 구독하는 독자에게 같은 정보를 N번 보여주는 셈이라 신호 밀도가 깎인다.

### 가드: 토픽별 opt-in 필터

- 공통 사실 (release, 메이저 사건) 은 *상위 토픽 1곳* 에만 highlight 로 배치
- 하위 렌즈 토픽은 그 사실을 *자기 렌즈 관점에서만* 인용 (예: kernel-security 는 "7.0.6 안의 OOB/UAF 픽스" 만, distro-stable 은 "rxrpc 백포트" 만)
- pipeline 설정 (e.g. `highlightReleaseMonikers: ["stable", "longterm"]`) 으로 토픽별 opt-in 화 → 명시적 토픽만 release 를 highlight 로 채택
- 프롬프트에도 명시: "draft 에 release 가 highlight 로 들어 있지 않으면 출력에 추가하지 말 것"

## 결함 2 — action 문구의 일반성 (상위 포스트일수록 약함)

상위 (daily-briefing) 포스트의 행동 지침이 "changelog 로 확인하세요 / 태그 페이지를 보세요" 처럼 클릭 외 부가 정보 없음. 반면 하위 렌즈 포스트는 "ASHMEM ioctl 을 사용하는 코드가 있다면 X 케이스에서 …" 처럼 조건절 + 검증 단서 포함. **상위일수록 추상화돼서 약해지는 역설**.

### 가드: action 구조 강제

프롬프트 레벨에서 action 문구의 형식 강제:

```
action 은 다음 2가지를 모두 포함할 것:
(1) 조건절: "X 한 경우라면 / Y 를 사용 중이라면"
(2) 구체 검증 단서: "Z 파일의 N 라인 / W command 출력에서 K 패턴 확인"

부정 예시 (금지):
- "changelog 를 확인하세요"
- "릴리스 노트를 보세요"
- "태그 페이지에서 추적하세요"
```

부정 예시를 프롬프트에 명시하는 것이 핵심. LLM 은 추상도가 높은 안전한 표현으로 회귀하는 경향이 있어, 구체적 금지 없이는 "일반적 조언" 으로 수렴한다.

## 결함 3 — hallucination (특히 OSS / trending 토픽)

`description` 필드 한 줄만 보고 LLM 이 "이 레포는 X 한다, Y 설정 키로 Z 가능" 같은 단정형 본문을 생성. 실제 README 를 읽지 않았으므로:

- 존재하지 않는 설정 키 (`pairing` / `allowFrom` 등) 발명
- description 의 추측을 단정형 사실로 변환
- HN 스레드 / 사용자 평가 인용 시 출처 없이 본문에만 등장

### 가드 3-1: README excerpt 그라운딩

수집 단계에서 상위 후보의 README 첫 ~700자를 `candidateBodies.readmeExcerpt` 로 가져와 프롬프트에 동봉. 프롬프트에는:

```
candidateBodies 에 없는 설정 키·플래그·파일경로·함수명·API 이름을 발명하지 말 것.
README excerpt 에 없는 사실은 본문에 단정형으로 쓰지 말 것 ("것으로 보입니다" 같은 추측 표현 허용).
```

운영 노하우:
- HEAD → main → master 순으로 README fetch fallback
- 타임아웃 8s, 동시성 3 (rate limit 회피)
- 한 후보가 fetch 실패해도 다른 후보로 진행 (전체 중단 금지)

### 가드 3-2: publish 단계의 disclaimer 안전망

LLM 이 프롬프트 룰을 지키지 않을 가능성에 대비, publish 단계에서 confidence.note 가 비었거나 약하면 강제 주입:

```ts
if (!post.confidence?.note?.includes("트렌딩 신호일 뿐")) {
  post.confidence = {
    ...post.confidence,
    note: "트렌딩 신호일 뿐 실제 품질은 직접 확인 필요. " + (post.confidence?.note ?? "")
  };
}
```

defense-in-depth: 프롬프트 (입력) + publish 가드 (출력) 양쪽.

## 결함 4 — 저신호일에 단일 항목 부풀리기

신호량이 적은 날 (예: 1건만 수집된 렌즈 토픽) 에 LLM 이 1건을 "오늘의 핵심 이슈" 로 그럴듯하게 늘려 적음. 정직성 부족 → 독자 신뢰 손실.

### 가드: signalLevel 메타 + summary prefix

draft 단계에서 신호량을 계산해 메타에 노출:

```js
const lensSignalCount = candidates.filter(c => c.lensSpecific).length;
const signalLevel =
  lensSignalCount >= 5 ? 'high' :
  lensSignalCount >= 3 ? 'medium' : 'low';

if (signalLevel === 'low') {
  draft.summary = `오늘은 이 렌즈에서 신호가 적은 날입니다 (lens-specific ${lensSignalCount}건). ${draft.summary}`;
}
```

프롬프트에도 명시: "signalLevel=low 면 단일 항목을 부풀리지 말 것, placeholder highlight 를 발명하지 말 것".

draftMetadata 에 `signalLevel` + `lensSignalCount` 를 항상 노출하면 사용자도 신호량을 추적 가능 (낮은 날이 며칠 지속되면 수집 단계 점검).

## 결함 5 — 목표 언어 안에 다른 CJK 문자 혼입 (5/14 추가)

한국어 강제 프롬프트로 LLM 을 호출해도 가끔 한자 / 일본어 가나 / 중국어 간체가 한 두 글자 섞여 나온다. 한국어 독자에게는 즉시 "기계 번역 같다" 는 인상을 주고 신뢰를 떨어뜨리지만, **정독 회고로 발견하기엔 너무 후행** (이미 게시된 다음). 5/14 dev-blog `linux-gpu-ai` 토픽 rewrite stdout 에 한자 "明文" 두 글자가 혼입돼 `auditPostQuality` 가 차단한 사건이 발단.

발생 메커니즘 (추정):

- 영어 README / LKML 본문 안의 영문 약어를 한국어로 풀어 쓸 때 LLM 이 일본어 / 한자권 단어로 일시 sample
- "명문" / "설명문" 같은 한자어 → 부분적으로 한자로 빠짐 ("명文" / "설明文")
- few-shot prompt 안에 영문/한자 표기가 섞여 있으면 출력 분포가 한자 쪽으로 끌림
- 출력 길이가 길어질수록 발생 확률 증가 (1000자 이상 / 다수 highlight)

### 가드 5-1: post-generation 검출 (필수)

rewrite 단계 직후 출력 텍스트에 한국어 (Hangul) / 한자 (CJK Unified Ideographs) / 가나 (Hiragana/Katakana) / 간체 (CJK Extension A) 문자 비율을 측정. 비-한글 CJK 가 0보다 크면 즉시 차단:

```js
// quality-guard.mjs 의 auditPostQuality 일부
const HANGUL = /[가-힯ᄀ-ᇿ㄰-㆏]/g;
const NON_HANGUL_CJK = /[一-鿿㐀-䶿぀-ゟ゠-ヿ]/g;

const nonHangul = (text.match(NON_HANGUL_CJK) || []).length;
if (nonHangul > 0) {
  throw new QualityError(`Non-Hangul CJK detected: ${nonHangul} chars (${matched.slice(0,10).join('')}...)`);
}
```

- **허용 예외 목록** — 의도된 한자 / 외래어 (예: 제품명, 인용 원문) 가 있다면 small allowlist 로 명시. 기본은 0 허용
- **차단 후의 복구 흐름** — quality-guard 가 차단해 publish 가 실패하면 ① stdout 의 해당 몇 글자만 한글 교정 → ② rewritten JSON 재빌드 → ③ publish 만 재실행. LLM 재호출은 비용·시간·재현성 모두 손해
- **`markPublishOk` 같은 status 갱신 헬퍼와 짝** — 수동 복구 흐름이 status JSON 의 cron 모드와 동일하게 `ok=true` 로 마크되도록 보조 도구를 미리 두면 운영 부담 ↓

### 가드 5-2: 프롬프트 레벨의 강조 (보조)

프롬프트 끝 (룰 위반 검사 직전) 에 짧고 단정적인 출력 언어 룰을 둔다:

```
출력 언어: 한국어 (Hangul) 만 사용.
- 한자 / 일본어 가나 / 중국어 문자 절대 금지
- 영문 약어·고유명사·기술 용어는 원문 표기 유지 (예: Linux, GPU, eBPF)
- 한국어 안에 들어 있는 한자어는 모두 한글로 (예: 説明 → 설명)
```

LLM 은 프롬프트 룰만으로는 100% 보장하지 못함 — 가드 5-1 의 post-generation 검출이 필수, 5-2 는 발생 빈도 자체를 낮추는 보조.

### 가드 5-3: 단계별 status 가시성과 짝

`steps: ['collect:True', 'draft:True', 'rewrite:False']` 같은 단계별 status 메타가 있으면, "어느 단계에서 깨졌는가" 를 즉시 분류 가능. 가드 5-1 의 차단은 status JSON 의 rewrite step 을 false 로 마크해야 진단·복구 시간이 줄어든다 (로그 grep 없이 status JSON 만 보면 됨).

### 일반화 — 목표 언어 강제가 필요한 모든 상황

이 가드는 한국어에만 한정되지 않는다. 다국어 콘텐츠 시스템에서 "Lang A 만 출력해야 하는데 Lang B 가 섞이면 안 됨" 인 모든 경우에 동일. 영어 강제일 때 한자 혼입, 일본어 강제일 때 한글 혼입 등 — 유니코드 블록 단위 검출이 단순하고 안정적이다.

## 5 가지 가드의 적용 위치

| 가드 | 적용 위치 | 비용 |
|------|---------|------|
| 1. 토픽별 release opt-in | pipeline.json + draft.mjs + prompt | 1일 |
| 2. action 형식 강제 | prompt (부정 예시 포함) | 30분 |
| 3. README excerpt 그라운딩 | collect/draft + prompt + publish 안전망 | 0.5일 |
| 4. signalLevel 메타 | draft.mjs + prompt + draftMetadata | 30분 |
| 5. 비-한글 CJK 차단 | quality-guard.mjs (post-rewrite) + prompt 보조 | 1시간 |

## 일반 원칙

1. **프롬프트 룰만으로는 부족** — LLM 은 추상적 안전 표현으로 회귀. publish 단계의 강제 주입·검증 안전망을 함께 둬야 함
2. **부정 예시 명시** — 금지 사항을 구체적으로 적어야 LLM 이 회피. "구체적으로 쓰세요" 보다 "이런 표현 금지: …" 가 효과적
3. **draft 단계의 메타 노출** — signalLevel / count / candidate 출처 등 LLM 입력의 핵심 통계를 metadata 로 항상 직렬화. LLM 이 그 메타를 보고 톤을 조정할 수 있음
4. **수집 단계의 그라운딩 자료 확보** — description 만 주면 hallucination, README/본문 발췌를 주면 그라운딩. fetch 시간을 들이는 게 hallucination 가드보다 비용 대비 효과 큼
5. **결함 패턴은 토픽별로 다름** — "lens 토픽은 중복", "OSS 토픽은 hallucination", "perf 토픽은 저신호" 등 토픽 특성에 맞춘 가드가 다름. 단일 가드 셋으로 모두 처리하려 하지 말 것
   - 실증 (2026-06-28): "미출시 미래 버전 번호를 발명하지 말 것" 가드가 Linux Daily·AI Coding Agents write 프롬프트엔 있으나 Opensource Trending write 프롬프트엔 **없다**. 버전 번호가 핵심 신호인 토픽(커널 릴리스·CC/Copilot 버전)에만 anti-prediction 가드를 켜고, 트렌딩 repo 큐레이션처럼 버전 발명 위험이 낮은 토픽에선 뺀 토픽별 tailoring 의 구체 사례. (단, 토픽이 늘면 가드가 토픽별로 표류·누락될 위험도 함께 커진다 — 가드 자체를 공통 모듈로 두고 토픽별 on/off 플래그로 관리하는 편이 안전.)

## 안티 패턴

- **사용자 검토 없이 매일 발행** — LLM 출력의 결함 패턴은 정독하지 않으면 발견 안 됨. 주 1회 정도 표본 정독 + 결함 패턴화 → 가드 추가의 사이클이 필수
- **하나의 거대한 prompt 로 모든 토픽 처리** — 토픽 특성을 잃음. 상위 / 렌즈 / OSS 등 토픽 종류별 별도 prompt + 별도 draft 스크립트가 가드 다양성을 만든다
- **fix 후 회귀 시 prompt 만 수정** — prompt 는 stateless. 같은 결함이 다시 나면 draft 데이터 구조 (signalLevel, readmeExcerpt 등) 와 publish 안전망까지 함께 검토

## 관련 페이지

- [[dev-blog]] — 4가지 가드가 처음 적용된 프로젝트
- [[llm-news-prediction-pitfalls]] — LLM 시장 예측의 검증 결여 함정 (동일 사상의 도메인 다른 적용)
- [[news-driven-market-signal-framework]] — 시그널의 7-축 구조 (검증 가능 시그널 분리)

## 변경 이력

- 2026-05-12: 최초 작성 (session-logs/20260511-230001-14d5-*.md). dev-blog 5/11 콘텐츠 품질 회고에서 도출된 4 가드 패턴을 일반화
- 2026-06-28: 일반 원칙 #5(결함 패턴은 토픽별로 다름)에 **anti-prediction 가드의 토픽별 tailoring 실증** 1건 보강 — "미래 버전 번호 발명 금지" 가드가 Linux Daily·AI Coding Agents write 프롬프트엔 있고 Opensource Trending 엔 없음(버전 신호가 핵심인 토픽만 on). 관련: dossier 의 `verified`/`seenBefore` 스키마 성숙과 grounding≠정확성 함정은 [[research-write-agent-separation]] 에 수록 (출처: session-logs/20260628-030337-d36d-*, -032046-b0b8-* 외 dev-blog 03:00 사이클 22건)
- 2026-05-14: 5번째 가드 (비-한글 CJK 혼입 차단) 추가. 5/14 dev-blog `linux-gpu-ai` 토픽 rewrite stdout 에 한자 "明文" 두 글자가 혼입돼 `auditPostQuality` 가 차단한 운영 사례에서 일반화. 유니코드 블록 단위 검출 + 차단 후 stdout 교정·publish 재실행 복구 흐름 + `markPublishOk` 같은 status 갱신 헬퍼와의 짝 패턴. "정독 회고로는 너무 후행" → 사전 가드의 비용 효과성 강조 (출처: session-logs/20260514-080604-8120-*.md)
