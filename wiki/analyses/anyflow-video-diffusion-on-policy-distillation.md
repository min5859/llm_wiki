---
title: AnyFlow — 임의 스텝 비디오 확산을 위한 On-Policy Flow Map Distillation
domain: personal
sensitivity: public
tags: [analysis, paper, video-diffusion, flow-matching, distillation, nvidia]
created: 2026-05-17
updated: 2026-05-17
arxiv: "2605.13724"
source_session: 20260517-080124-0726-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md
sources:
  - "session-logs/20260517-080124-0726-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md"
confidence: medium
related:
  - "wiki/analyses/meanflow-text-to-image.md"
  - "wiki/analyses/diffusion-snr-t-bias.md"
---

# AnyFlow — 임의 스텝 비디오 확산을 위한 On-Policy Flow Map Distillation

## 한줄 요약

NVIDIA + NUS + MIT 의 비디오 확산 distillation 프레임워크 (arXiv 2605.13724). flow map 백워드 시뮬레이션 + on-policy DMD 손실로 단일 모델이 4 NFE 부터 32 NFE 까지 임의 스텝 추론을 지원하면서 인과·양방향 모두에서 SOTA.

## 연구 배경

기존 비디오 확산 가속 (Consistency Models 계열) 의 한계:

- **CM 의 fixed-point mapping** (z_t → z_0) 은 multi-step 시 중간 상태 re-noising 으로 trajectory drift 발생
- 4 NFE 같은 극저 스텝에서는 잘 동작하지만, 스텝 수가 늘어나면 오히려 품질 저하
- sCM, rCM, Self-Forcing 등은 모두 consistency 기반이라 어느 한 스텝 budget 에 맞춰 학습됨

## 핵심 방법론

### Flow Map Formulation

PF-ODE 의 정확한 flow map Φ_{r←t}(z_t) = z_r 을 근사:

```
f_θ(z_t, t, r) ≈ z_r,  for 1 ≥ t > r ≥ 0
```

- **r = 0 → Consistency Model** (특수 케이스)
- **t = r → Flow Matching** (특수 케이스)

즉, flow map 은 두 패러다임을 일반화. 임의 시간쌍 사이 transition 을 학습하므로 가변 스텝 추론을 자연 지원.

### MeanFlow 목적함수 + 유한차분

`u_θ(z_t, r, t) = (z_t - z_r) / (t - r)` 평균 속도로 파라미터화. Jacobian-vector product (JVP) 가 FSDP 와 호환 안 되는 문제를 유한차분 근사로 우회:

```
du/dt ≈ [u(z_{t+Δt}, r, t+Δt) - u(z_{t-Δt}, r, t-Δt)] / (2Δt)
```

→ 학습 스텝당 forward pass 2회만으로 FSDP 호환.

### Flow Map Backward Simulation (핵심 기여)

기존 consistency backward simulation 은 re-noising 으로 drift 발생. AnyFlow 는 flow map 의 **composition 성질** (`Φ_{q←r} ∘ Φ_{r←t} = Φ_{q←t}`) 을 활용해 Euler 샘플링 trajectory 를 shortcut segment 로 분해:

- z_t → z_r → z_T 의 backward 를 shortcut 으로 효율 시뮬레이션
- 임의 step 크기 transition 의 효율적 평가가 가능 → on-policy distillation 의 self-rollout 비용 절감

### DMD 분포 매칭 손실

self-rollout z^_0 = f_θ(z) → t ∈ [0, T] 에서 re-noise (z_t = (1-t)z^_0 + tε) → real/fake score 차이로 reverse-KL gradient 계산.

## 주요 결과

- **양방향 T2V (14B)**: AnyFlow 4 NFE 에서 VBench 84.04, rCM-14B (4 NFE 83.73) 능가
- **인과 I2V**: AnyFlow 4 NFE 에서 VBench-I2V 87.87, Wan2.1-I2V-14B (50×2 NFE) 87.71 와 동등
- **임의 스텝**: 4 NFE → 32 NFE 까지 단일 모델로 모두 SOTA
- **Ablation**: Flow Map Training + Flow Map Backward Simulation 조합이 Consistency Backward Simulation 보다 4·32 NFE 양쪽에서 모두 우수

## 한계 및 향후 과제

- 유한차분 근사 (2 forward) 가 JVP 와 정확히 동등하지는 않음, 학습 후반 수렴에 영향 가능
- VBench 위주 평가, 인지 품질 (motion realism, temporal consistency) 의 인간 평가는 보완 필요
- 단일 GPU 환경에서의 효율 대비 FSDP 멀티 GPU 의 이점 강조 — 소규모 모델 적용성 미검증

## 설계 인사이트

- **Flow Map 의 일반화 위력** — consistency / flow matching 을 특수 케이스로 포함하므로 두 패러다임의 장점을 단일 학습으로 흡수
- **Composition 성질의 algorithmic exploitation** — 수학적 성질을 backward simulation 의 효율 트릭으로 직접 활용
- **임의 스텝 = 운영 유연성** — 사용자가 추론 시점에 「품질-속도」를 자유 선택. 4 NFE 인터랙티브 ↔ 32 NFE 고품질 한 모델
- **JVP 회피 패턴** — finite difference 가 FSDP 친화 정확도-속도 절충의 표준이 되어가는 흐름 (Transition Model [21], JVP-free 알고리즘 [22, 23] 과 같은 맥락)

## 관련 논문

- **MeanFlow** (Geng et al., 2025) — Flow Map 모델 학습의 대표 알고리즘. 본 wiki [[meanflow-text-to-image]] 참고
- **rCM** (2025) — sCM 기반 score distillation regularizer, 이 논문의 핵심 baseline
- **Self-Forcing** (2025) — causal video 의 consistency ODE init + on-policy distillation. AnyFlow 가 causal 설정에서 능가

## 변경 이력

- 2026-05-17: 초기 생성 (session-log 20260517-080124 기반, cron 분석 미생성 상태에서 raw paper 본문 직접 요약)
