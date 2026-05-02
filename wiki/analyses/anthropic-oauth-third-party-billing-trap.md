---
title: "Anthropic OAuth 의 third-party 빌링 함정 — Claude Max 구독을 외부 CLI 가 재사용한다는 오해"
domain: personal
sensitivity: public
tags: ["analysis", "anthropic", "claude", "oauth", "billing", "third-party-cli", "hermes", "subscription"]
created: 2026-05-02
updated: 2026-05-02
source_session: "20260502-092045-628d-hermes-라는-opensource-agent-를-설치하려고하는데-조사좀-해-주세요.md"
confidence: medium
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/concepts/claude-code-overview.md"
---

# Anthropic OAuth 의 third-party 빌링 함정

## 개요

Claude Code 외 third-party CLI 도구 (Hermes Agent 등) 가 「Anthropic OAuth 로그인」 옵션을 제공하면, 직관적으로는 **자신의 Claude 구독 (Pro / Max) 이 재사용될 것** 으로 기대한다. 실제로는 **Anthropic 이 third-party OAuth 클라이언트 요청을 별도의 `extra_usage` billing pool 로 라우팅** 하기 때문에, 표면상 인증은 성공해도 거의 즉시 「out of extra usage」 에러가 발생한다.

이 페이지는 Hermes Agent 조사 중에 드러난 함정 (그리고 비슷한 third-party 도구 일반에 적용되는 패턴) 을 정리한다.

## 함정의 구조

| 단계 | 표면상 동작 | 실제 청구 경로 |
|---|---|---|
| OAuth 로그인 | "Claude 계정으로 로그인" 화면 → 성공 | OK |
| 모델 호출 | refresh token 으로 Anthropic API 호출 | **`extra_usage` pool 로 라우팅** |
| 청구 | (사용자는 Pro/Max 구독비로 처리되리라 기대) | extra_usage credit 잔액 차감 — 대부분 0 |
| 결과 | 즉시 "out of extra usage" 에러 | — |

## 구체적 제약 (2026-05 시점, Hermes Agent 사례)

| 제약 | 내용 |
|---|---|
| 구독 플랜 | **Claude Max 플랜 + extra usage credits 별도 구매 필요**. Pro 플랜은 사용 불가 |
| 모델 가용성 | **Sonnet 4.6 / Opus 4.7 은 429 반환** (Anthropic 측 최근 third-party 제한 강화). Haiku 4.5 만 안정적 |
| 진행 중인 이슈 | NousResearch/hermes-agent#17169 (429), #12905 (라우팅), #6475 ("out of extra usage"), #10575 (proxy 시스템 프롬프트 오분류) |
| 메인테이너 입장 | "문서가 오해 소지 있음" 인정 중 |

## 왜 이렇게 되는가 (추정)

- Anthropic 입장에서 Pro/Max 의 quota 는 **자사 1st-party 클라이언트 (claude.ai 웹, Claude Code, Claude Desktop) 에 한정해 보장하고 싶다**
- third-party OAuth 클라이언트가 동일 quota 에 접근하면 **악용 (관리되지 않는 봇 트래픽) 위험** 이 있어, 별도 pool 로 강제 분리한 것으로 보임
- 결과적으로 OAuth 는 **「인증 메커니즘으로는 동작하지만 빌링 의미로는 동작하지 않는」** 모호한 상태

## 권장 패턴

| 시나리오 | 권장 |
|---|---|
| third-party CLI 에서 Claude 모델을 메인으로 쓰고 싶다 | **`ANTHROPIC_API_KEY` 직발급** (콘솔에서 발급, 종량제). OAuth 가 아니라 API key. |
| Claude Pro / Max 구독을 살리고 싶다 | **Claude Code 등 1st-party 클라이언트로 한정**. 같은 구독으로 third-party CLI 를 굴리려는 시도는 포기 |
| Claude 모델은 부수적이고, 다른 vendor 도 함께 쓴다 | **OpenRouter** 한 키로 200+ 모델 (Claude 포함). 단일 결제 / 단일 키 / 단일 인터페이스 |
| ChatGPT / Codex 구독은 살리고 싶다 | **Codex CLI OAuth** 는 Hermes 사례 기준 비교적 무난. `~/.codex/auth.json` 자동 import 됨. ChatGPT 구독 라우팅에 함정 보고는 검색 결과 상 없음 |

## 같은 부류의 도구를 만났을 때 점검할 것

third-party CLI / agent 가 「Claude OAuth 로그인」 옵션을 제공하면 다음을 의심한다:

1. **빌링 pool 이 분리되는가?** 공식 문서에 명시 없으면 issue tracker 에서 "out of usage" / "billing" / "subscription" 으로 검색
2. **모델 제한이 있는가?** Sonnet / Opus 가 429 로 막히는지, Haiku 만 통과하는지
3. **메인테이너가 "API key 권장" 이라고 별도로 안내하는가?** 그렇다면 OAuth 는 사실상 함정

ChatGPT / Codex 구독에는 이런 함정이 (현재까지) 보고되지 않았다. **OAuth 라는 표면이 같다고 빌링 정책까지 같다고 가정하지 말 것.**

## 결론

- "OAuth 로그인 = 내 구독 재사용" 이 항상 성립하는 게 아니다. **Anthropic 은 third-party OAuth 를 의도적으로 별도 pool 로 라우팅**
- third-party 도구에서 Claude 를 쓰고 싶으면 **API key 직발급** 또는 **OpenRouter 경유** 가 안전한 기본값
- 1st-party 구독 (Claude Pro / Max) 의 가치는 1st-party 클라이언트 안에서 다 소진하는 게 효율적

## 관련 페이지

- [[hermes-agent]] — 본 함정이 처음 발견된 도구
- [[llm-provider-aggregator-vs-local-vs-hub]] — OpenRouter 가 권장 폴백이 되는 이유
- [[claude-code-overview]] — 1st-party 클라이언트 측 구독 / 결제 모델

## 변경 이력

- 2026-05-02: 최초 생성. Hermes Agent 조사 중에 드러난 Anthropic OAuth third-party 빌링 함정 정리 (출처: session-logs/20260502-092045-628d-*)
