---
title: "LLM 기반 자동 콘텐츠 발행의 4 가지 품질 가드"
domain: both
sensitivity: public
tags: ["analysis", "llm", "newsletter", "content-quality", "hallucination", "prompt-engineering"]
created: 2026-05-12
updated: 2026-05-12
source_session: "20260511-230001-14d5-오늘-dev-blog-주제들이-5월11일-자로-업데이트-되지않았습니다.md"
sources:
  - "session-logs/20260511-230001-14d5-오늘-dev-blog-주제들이-5월11일-자로-업데이트-되지않았습니다.md"
confidence: medium
related:
  - "wiki/projects/dev-blog.md"
  - "wiki/analyses/llm-news-prediction-pitfalls.md"
---

# LLM 기반 자동 콘텐츠 발행의 4 가지 품질 가드

LLM 으로 매일 자동 발행되는 콘텐츠 (뉴스레터, 트렌딩 큐레이션, 일일 브리핑) 가 "겉으론 그럴듯한데 정독하면 빈약" 상태에 빠지는 4가지 전형적 결함과, 각각의 파이프라인 레벨 가드.

5/11 dev-blog 의 12종 토픽 게시본을 사용자 관점에서 정독한 결과 발견된 패턴이며, **자동 콘텐츠 발행 시스템 일반에 적용** 된다.

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

## 4 가지 가드의 적용 위치

| 가드 | 적용 위치 | 비용 |
|------|---------|------|
| 1. 토픽별 release opt-in | pipeline.json + draft.mjs + prompt | 1일 |
| 2. action 형식 강제 | prompt (부정 예시 포함) | 30분 |
| 3. README excerpt 그라운딩 | collect/draft + prompt + publish 안전망 | 0.5일 |
| 4. signalLevel 메타 | draft.mjs + prompt + draftMetadata | 30분 |

## 일반 원칙

1. **프롬프트 룰만으로는 부족** — LLM 은 추상적 안전 표현으로 회귀. publish 단계의 강제 주입·검증 안전망을 함께 둬야 함
2. **부정 예시 명시** — 금지 사항을 구체적으로 적어야 LLM 이 회피. "구체적으로 쓰세요" 보다 "이런 표현 금지: …" 가 효과적
3. **draft 단계의 메타 노출** — signalLevel / count / candidate 출처 등 LLM 입력의 핵심 통계를 metadata 로 항상 직렬화. LLM 이 그 메타를 보고 톤을 조정할 수 있음
4. **수집 단계의 그라운딩 자료 확보** — description 만 주면 hallucination, README/본문 발췌를 주면 그라운딩. fetch 시간을 들이는 게 hallucination 가드보다 비용 대비 효과 큼
5. **결함 패턴은 토픽별로 다름** — "lens 토픽은 중복", "OSS 토픽은 hallucination", "perf 토픽은 저신호" 등 토픽 특성에 맞춘 가드가 다름. 단일 가드 셋으로 모두 처리하려 하지 말 것

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
