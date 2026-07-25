---
title: "evidence.kind enum 거부가 topic 전체 drop 으로 증폭 — opensource-curation research 이틀 연속 조용한 결손"
domain: "ai-agent"
sensitivity: "internal"
tags: ["bug", "dev-blog", "dossier", "schema", "validator", "enum", "research-write-agent-separation", "silent-failure"]
created: "2026-07-26"
updated: "2026-07-26"
sources:
  - "session-logs/20260725-224215-c0eb-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "dev-blog commit 823213a / f956b88"
confidence: "high"
related:
  - "wiki/patterns/prompt-schema-pipeline-coupling.md"
  - "wiki/bugs/highlights-action-validator-schema-drift.md"
  - "wiki/analyses/llm-content-quality-guards.md"
  - "wiki/projects/dev-blog.md"
---

# evidence.kind enum 거부가 topic 전체 drop 으로 증폭 — opensource-curation research 이틀 연속 조용한 결손

dev-blog `opensource-curation` research 단계가 07-24·07-25 아침 cron 에서 반복 실패해 07-25 게시가 결손된 사건. 원인은 LLM 이 `evidence.kind` 필드에 스키마 enum 밖의 값을 스스로 창발한 것이고, 이 값 하나의 거부가 validator 예외로 topic 전체를 drop 시켰다.

## 증상

07-24·07-25 아침 cron 에서 `opensource-curation` research 가 반복 실패, 모니터링·알림 없이 07-25 게시 자체가 나오지 않음. 이틀 연속 발생한 뒤에야 발견된 조용한 결손이다.

## 원인

research LLM 이 GitHub repo 를 근거로 들며 evidence 를 만들 때 `evidence.kind: "repo"` 를 생성했다. 그러나 `EVIDENCE_KIND_VALUES` 는 `commit / thread / changelog / cve / article` 만 허용하는 enum 이었고, `validateEvidence()` 가 이를 거부하며 throw — 예외가 evidence 1건이 아니라 **topic 전체를 drop** 시켰다.

- **LLM 의 창발 라벨** — prompt 에 적힌 kind 값 목록과 schema 의 enum 이 **별도 파일에 이중 정의**돼 있었다. LLM 은 "GitHub repo 를 인용한다"는 맥락에서 자연스럽게 `"repo"` 라는, enum 에는 없는 그러나 의미상 합리적인 값을 만들어냈다.
- **blast radius 설계 결함** — evidence 하나의 kind 값이 잘못됐다고 해서 dossier·topic 전체가 버려질 이유는 없다. 검증 실패의 파급 범위가 실패 단위(evidence 1건)보다 훨씬 크게 설계돼 있었다.

## 수정

dev-blog commit `823213a` (2026-07-25 22:22:52 +0900) "fix(dossier): allow evidence.kind \"repo\" to stop opensource-curation research failures". evidence.kind 는 다운스트림에서 `dossier-to-post.mjs` 의 note 필드에 쓰이는 display-only 라벨(분기 없음)이므로 enum 확장이 안전하다고 판단.

변경 3파일 +9/-3:
- `scripts/lib/dossier-schema.mjs` — enum 에 `"repo"` 추가
- `prompts/opensource-curation-research-ko.md` — prompt 의 kind 값 목록을 enum 과 동기화 (이 prompt 만 갱신)
- `scripts/dossier-schema.test.mjs` — `"repo"` 유효 케이스 + 미지 kind 는 여전히 거부되는 회귀 추가

테스트 118/118 통과.

## 일반 교훈

1. **LLM 이 채우는 분류 라벨 필드는 enum 밖 값을 창발한다** — 자유 텍스트 근거를 구조화된 enum 으로 강제 매핑시키면, LLM 은 맥락상 합리적이지만 정의되지 않은 값을 만들어낼 수 있다. 그 필드가 다운스트림에서 display-only(분기 없음)라면 reject 보다 enum 확장·normalize·soft-fail 을 먼저 검토한다.
2. **검증 실패의 blast radius 설계** — evidence 1건 거부가 topic 전체 drop 으로 증폭된 것은 과잉 처벌이다. 검증 단위와 실패 시 폐기 단위를 일치시켜야 한다 (예: 해당 evidence 만 드롭하고 dossier 는 나머지로 계속 진행).
3. **prompt 의 값 목록과 schema enum 의 이중 정의는 동기화 대상** — 같은 제약이 두 파일에 따로 적혀 있으면 한쪽만 바뀌는 표류가 필연적이다. [[prompt-schema-pipeline-coupling]] 이 정리한 결합점 인벤토리와 같은 뿌리.
4. **조용한 결손은 재발해야 발견된다** — 이번에도 이틀 연속 발생한 뒤에야 사람이 알아챘다. 결손 감지 장치 부재는 [[newsletter-research-anti-bot-blocking]] 이 기록한 anti-bot 차단 결손과 동일한 구조적 공백(파이프라인이 "정상 완료"처럼 보이는 로그를 남긴 채 조용히 결손된다)이다.

## 변경 이력

- 2026-07-26: 최초 작성 (출처: session-logs/20260725-224215-c0eb-*, dev-blog commit 823213a/f956b88)
