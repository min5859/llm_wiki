---
title: "OpenRouter vs Ollama vs Hugging Face — LLM 백엔드 3가지 분류"
domain: "ai-agent"
sensitivity: public
tags: ["analysis", "llm", "openrouter", "ollama", "huggingface", "provider", "aggregator"]
created: 2026-05-02
updated: 2026-05-02
source_session: "20260502-092045-628d-hermes-라는-opensource-agent-를-설치하려고하는데-조사좀-해-주세요.md"
confidence: high
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/analyses/anthropic-oauth-third-party-billing-trap.md"
---

# OpenRouter vs Ollama vs Hugging Face

## 개요

「LLM 을 어디서 가져오나?」 는 third-party agent / CLI 를 셋업할 때 매번 마주치는 결정점이다. 이름이 다 비슷하게 들리는 OpenRouter / Ollama / Hugging Face 는 **층이 다른 도구** 다. 같은 차원의 선택지가 아니라는 점부터 정리해야 의사결정이 빨라진다.

## 3가지 분류

| 항목 | OpenRouter | Ollama | Hugging Face |
|---|---|---|---|
| 분류 | **클라우드 API 중계 (aggregator / gateway)** | **로컬 inference 런타임** | **모델 허브 + Inference API** |
| 실행 위치 | 원격 (각 vendor 서버) | 내 컴퓨터 | HF 클라우드 또는 다운받아 로컬 |
| 모델 | Anthropic / OpenAI / Google / Meta / Mistral 등 200+ | 로컬에서 돌릴 수 있는 오픈모델 (Llama, Qwen 등) | 수십만 개 모델 (가중치 다운로드 중심) |
| 비용 | **사용량 기반 결제** (vendor 가격 + 약간의 마크업) | 무료 (전기료 / GPU 만) | 무료~유료 혼재 |
| 키 / 인증 | OpenRouter 키 1개 | 없음 | HF 토큰 |
| 데이터 프라이버시 | 각 vendor 정책 따름 (외부 전송) | **데이터가 안 나감** | HF 정책 (로컬 다운로드는 안 나감) |
| 인터넷 필요 | 필수 | 모델 다운로드 후 불필요 | 다운로드 시 필요 |
| 모델 추가 비용 | vendor 가격 + 소량 마크업 | 0원 | vendor 따라 다름 |

## 한 줄 요약

- **OpenRouter** = "어댑터 / 허브". Anthropic / OpenAI / xAI 등을 따로따로 가입·결제·관리하지 않고, **OpenRouter 한 곳만 충전** 해두면 안의 모든 모델을 동일 인터페이스로 호출. Claude Sonnet 도, GPT-5 도, Llama 도 한 키로 다 됨
- **Ollama** = 정반대. **내 맥북에서 직접** 모델 inference (외부 API 호출 없음, 인터넷 불필요, 데이터 안 나감, 대신 모델 크기와 VRAM 한계에 종속)
- **Hugging Face** = "모델 위키피디아". 가중치를 받아오는 곳이 본질이고, 부가로 Inference API 도 제공. Ollama 와도, OpenRouter 와도 다른 층

## 시나리오별 권장

| 사용 패턴 | 권장 |
|---|---|
| Claude / GPT 등 클로즈드 모델 여럿 실험해보고 싶다 | **OpenRouter** — 키 1개로 vendor 락인 회피 |
| 데이터 프라이버시가 최우선 (회사 코드 / 개인 정보) | **Ollama** — 외부 전송 없음. 단 모델 품질은 클로즈드보다 떨어짐 |
| 특정 오픈모델을 받아와서 직접 fine-tune / 실험 | **Hugging Face** 에서 가중치 → 로컬로 실행 |
| 인터넷이 안 되는 환경 (오프라인 노트북) | **Ollama** 만 가능 |
| 비용을 신경 쓰고 싶지 않고 가장 편하게 시작 | **OpenRouter** $5~10 충전 |
| 1st-party 구독 (Claude Pro / Max) 을 가진 코딩 도구 사용자 | Claude Code 로 1st-party 직접 사용. third-party CLI 에서 OAuth 로 재사용하려는 시도는 **함정** → [[anthropic-oauth-third-party-billing-trap]] |

## 자주 헷갈리는 점

### "OpenRouter 가 Ollama 같은 건가?"

**아니다.** OpenRouter 는 클라우드 중계 서비스, Ollama 는 로컬 런타임. 데이터가 외부로 나가는지부터 다르다.

### "Hugging Face Inference API 면 OpenRouter 같은 건가?"

부분적으로 비슷하지만 모델 라인업이 다르다. HF Inference 는 HF 에 올라간 오픈 weights 모델 중심, OpenRouter 는 클로즈드 vendor (Anthropic / OpenAI 등) 까지 망라.

### "Ollama 가 있는데 왜 OpenRouter 를 쓰나?"

오픈모델로는 Claude Opus / GPT-5 등의 품질을 못 맞추기 때문. **품질 / 데이터 프라이버시 / 비용** 의 3축에서 트레이드오프.

## third-party agent 의 provider 옵션 매핑

(Hermes Agent 같은 self-hosted agent 의 config 에서 다음과 같이 매핑됨)

```yaml
providers:
  openrouter: { type: api, key: $OPENROUTER_KEY }    # OpenRouter
  ollama:     { type: custom, base_url: "http://localhost:11434/v1" }  # Ollama 의 OpenAI 호환 엔드포인트
  huggingface:{ type: api, key: $HF_TOKEN }          # HF Inference
  anthropic:  { type: api, key: $ANTHROPIC_API_KEY } # 직 API key (OAuth 함정 회피)
```

## 결론

- 셋은 **다른 층의 도구** 다. 「OpenRouter vs Ollama」 같은 이지선다가 아니라, 「클라우드 중계 / 로컬 런타임 / 모델 허브」 의 3가지 차원
- 처음 셋업이라면 **OpenRouter** 가 가장 매끄러움 (vendor 락인 회피, 단일 결제)
- 데이터 프라이버시가 중요하면 **Ollama** 와 병용 — provider fallback 으로 같이 등록 가능
- HF 는 가중치를 받아오는 인프라 + 부수적인 Inference. 보통 Ollama / 로컬 vLLM 과 짝지어 쓴다

## 관련 페이지

- [[hermes-agent]] — provider 선택의 사례 (설치 시 interactive config 에서 묻는다)
- [[anthropic-oauth-third-party-billing-trap]] — third-party CLI 에서 Claude 를 쓸 때 OpenRouter 가 권장 폴백이 되는 이유

## 변경 이력

- 2026-05-02: 최초 생성. Hermes Agent 조사 중 사용자가 "OpenRouter 가 Ollama 같은 건가요?" 라고 물어 정리한 비교 (출처: session-logs/20260502-092045-628d-*)
