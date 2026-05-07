---
title: "ESamp — Latent Distilling 기반 LLM 탐색 샘플링"
domain: "personal"
sensitivity: "public"
tags: ["analysis", "LLM", "decoding", "test-time-scaling", "exploration", "sampling", "RND", "latent-distillation"]
created: "2026-05-07"
updated: "2026-05-07"
source_session: "20260507-080029-d1f3-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md"
sources:
  - "session-logs/20260507-080029-d1f3-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md"
confidence: "medium"
related: []
---

# ESamp — Latent Distilling 기반 LLM 탐색 샘플링

Yuanhao Zeng 외 (BIGAI, ShanghaiTech). arXiv 2604.24927 (Preprint, 2026-04-29).

## 한줄 요약

LLM 내부 hidden representation 의 예측 오차를 신규성(novelty) 신호로 활용해, 표층 어휘가 아닌 의미 공간에서 다양성을 강제하는 디코딩 기법(Exploratory Sampling, ESamp).

## 개요

기존 LLM test-time scaling(Pass@k, 자기검증, 다수결 등)은 후보 집합의 다양성에 의해 성능 상한이 결정된다. 그러나 temperature, top-p, min-p 같은 표준 stochastic sampling 은 token 수준의 어휘 변이만 만들고, 핵심 추론 구조는 거의 동일한 후보를 반복 생성한다. ESamp 는 이 한계를 의미 공간(representation space) 에서의 신규성 측정으로 돌파한다.

## 주요 기여

- **Latent Distiller (LD)**: 얕은 layer 의 hidden state 로부터 깊은 layer 의 hidden state 를 예측하는 경량 2-layer MLP (hidden 384). 테스트 타임에 online 으로 학습된다.
- **RND(Random Network Distillation) 영감의 신규성 신호**: 이미 본 표현 매핑은 예측 오차가 작고, 새로운 매핑은 오차가 크다는 점을 활용.
- **KL-regularized policy 의 closed-form 재가중**: `π_new(z|s) ∝ π_ref(z|s) · exp(β·r(s,z))`, `r = log π_ref − log q_dist`. logit 공간에서는 `logit_new = (1+β)·logit_ref − β·logit_dist`.
- **공유 LD 를 통한 협력적 탐색**: K 개의 병렬 시퀀스가 같은 LD 를 갱신해, 한 워커가 탐색한 의미 영역을 다른 워커가 자동으로 회피하는 implicit 스케줄러로 동작.
- **비동기 파이프라인**: LD forward 는 LLM 의 첫 layer 직후 시작해 중간 layer 와 overlap, LD 학습 backward 는 sampling/de-tokenization 슬랙에 배치. 오버헤드 < 5% (최적화판은 1.2%).

## 방법

1. step `t` 에서 첫 transformer layer 출력 `H_t^1` 계산.
2. LD 가 `Ĥ_t^L = f_φ(H_t^1)` 예측 (비동기 stream).
3. LLM 본 forward 가 끝나면 `logit_ref = W_head·H_t^L`, `logit_dist = W_head·Ĥ_t^L` 두 로짓 산출.
4. `logit_new = (1+β)·logit_ref − β·logit_dist` 로 융합 후 sampling.
5. `MSE(H_t^L, Ĥ_t^L)` 로 LD 를 매 step online 학습.

기하학적 해석: `Δlogit_z = β·||w_z||·||e_t||·cos(w_z, e_t)` — `||e_t||` 는 context novelty, cosine 항이 의미 방향을 선택.

## 결과

| 벤치마크 | 모델 | 핵심 수치 |
|---|---|---|
| AIME25 Pass@64 | Qwen2.5-7B | β=0.25 → 46.7% (vanilla 대비 큰 폭 우위) |
| AIME24 Pass@64 | Qwen3-8B | ESamp 80.0% vs Vanilla 70.0% |
| GPT-OSS-20B | reasoning | Pass@8 ESamp ≈ 베이스라인 Pass@64 |
| Creative Writing | Qwen2.5-7B | Vendi 1.67(↑), Sim 0.57(↓), PPL 3.55(↓) — 다양성·품질 동시 개선 |
| 처리량(RTX4090, B=32, K=16) | Qwen3-8B | 4364 vs vanilla 4557 tok/s (오버헤드 4.25%) |
| 메모리 | 8B 모델 | LD < 200MB VRAM |

추가 검증:
- 같은 크기의 가우시안 노이즈로 latent error 를 대체하면 vanilla 수준으로 성능 회귀 → 오차 벡터가 구조적 정보 보유.
- vocabulary-space distiller 변형은 불안정 → latent space 에서 신규성 추정이 핵심.
- FIRE / Self-Consistency 와 조합 가능, Pass@64 추가 향상.

## 한계

- Pass@1 은 일부 세팅에서 소폭 하락 (커버리지 우선 설계의 trade-off).
- GPU 가 완전 포화된 환경에서는 비동기 overlap 의 이득이 줄어든다.
- Hyperparameter β 가 너무 크면 high-confidence token 을 과도하게 억제해 grammatically incorrect 출력 위험.
- per-prompt vs shared LD 선택은 task 구조에 의존적 (수학은 per-prompt 유리, 코드는 shared 유리).

## 실무 적용 가능성

- vLLM 기반 서빙에서 Pass@k reranking, reward-model selection, self-verification 류 test-time scaling 의 효율을 큰 sampling budget 없이 끌어올림.
- tLLM 프레임워크의 producer–consumer 추상화로 vLLM fork 없이 hidden state 캡처 + sampler 후처리만으로 통합 가능 → 기존 추론 스택의 CUDA Graph, FlashInfer 최적화 보존.
- 코드/수학/창의적 글쓰기 등 후보 다양성 자체가 품질을 결정하는 워크로드에 특히 적합.

## 관련 페이지

- [[everything-claude-code]] — agent harness 측면의 인접 주제

## 변경 이력

- 2026-05-07: 신규 작성 (자동 ingest from session-logs).
