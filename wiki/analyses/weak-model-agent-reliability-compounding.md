---
title: "약한 모델의 에이전트 전략 — 신뢰도 복리 붕괴와 결정적 워크플로우"
domain: "ai-agent"
sensitivity: "public"
tags: ["analysis", "ai-agent", "open-weight", "reliability", "workflow", "verification-gate", "model-routing"]
created: "2026-07-05"
updated: "2026-07-05"
sources:
  - "session-logs/20260704-132738-e509-지금-세션에서-작업했던-hermes-webui-설치가-pc-를-껏다켜니-접속이-안되네-다시.md"
confidence: high
related:
  - "wiki/analyses/multi-agent-orchestration-taxonomy.md"
  - "wiki/analyses/ai-coding-agent-cost-and-context-patterns.md"
  - "wiki/concepts/hermes-agent.md"
---

# 약한 모델의 에이전트 전략 — 신뢰도 복리 붕괴와 결정적 워크플로우

SOTA 모델(Opus/GPT-5.5급)과 약한 오픈웨이트 모델(Gemma/Qwen/Kimi/GLM급)은 **최적 에이전트 구조가 정반대**다. 회사 보안·비용 제약으로 셀프호스팅 오픈모델을 쓸 수밖에 없는 환경의 설계 지침 (2026-07-04, Hermes 멀티에이전트 운영 실험 중 리서치).

## 핵심 — 신뢰도는 곱해진다

스텝당 90% 정확한 모델도 5스텝 자율 루프면 `0.9⁵ ≈ 59%` 로 붕괴한다. SOTA 는 스텝당 정확도가 높아 긴 자율 루프를 버티지만, 약한 모델은 **자율 스텝 수 자체를 줄이는 구조**가 필요하다.

## 약한 모델의 6가지 설계 규칙

1. **자율 에이전트가 아니라 결정적 워크플로우** — 다음 스텝을 LLM 이 아니라 코드/파이프라인이 결정한다. LLM 은 각 스텝의 변환기로만.
2. **한 호출 = 한 책임**으로 좁게 분해 — "조사하고 판단하고 작성"을 한 호출에 몰지 않는다.
3. **스텝 사이 검증 게이트** — 스키마 검증·테스트·리뷰어 호출·self-consistency 다수결. 오류가 다음 스텝으로 복리 전파되기 전에 차단.
4. **짧은 체인 + 재시도/휴먼 체크포인트** — 체인이 길어질수록 복리 붕괴. 중간 산출물을 사람이 확인할 지점을 설계.
5. **구조화 출력 + tool-calling 안정성으로 모델 선택** — 벤치마크 점수보다 "실패가 복구 가능한 형태인가"가 중요 (Kimi K2.6 이 recoverable failure 로 호평).
6. **모델 라우팅** — 쉬운 스텝은 더 작은 모델로 (비용·지연 절감, [[ai-coding-agent-cost-and-context-patterns]] 의 강/약 모델 분리와 같은 축).

## SOTA 와의 대비

| | SOTA 모델 | 약한 오픈모델 |
|---|----------|--------------|
| 정답 구조 | 강한 단일 에이전트 + 위임 | 좁은 역할 파이프라인 + 검증 게이트 |
| 제어 흐름 | LLM 이 자율 결정 | 코드/파이프라인이 결정 |
| 체인 길이 | 길어도 버팀 | 짧게, 게이트로 격리 |

역할 분할 파이프라인(architect→coder→reviewer 류)은 SOTA 에선 조율 비용이 이득을 잠식하지만, **약한 모델에선 오히려 정답**이 된다 — 오케스트레이션 방식 자체의 분류는 [[multi-agent-orchestration-taxonomy]].

## 부가 구분 — 지식베이스 오염과 모델 오염

에이전트가 공유 위키를 지저분하게 만들어도 **모델 가중치가 나빠지는 게 아니라 참조 시 답 품질만 흐려진다**. 오염은 되돌릴 수 있는 데이터 문제(git revert·큐레이션)이지 비가역 학습 문제가 아니다 — 다중 에이전트 공유 위키의 실제 위험은 [[multi-agent-shared-wiki-concurrency]] 참조.

## 변경 이력

- 2026-07-05: 최초 생성 — Hermes 멀티에이전트 운영 실험(회사 보안 환경용 오픈모델 전제) 중 리서치에서 정리 (출처: session-logs/20260704-132738-e509-*)
