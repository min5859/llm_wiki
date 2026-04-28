---
title: Near-Future Policy Optimization (NPO) — RLVR 혼합 정책의 Quality-Variance 트레이드오프
domain: personal
sensitivity: public
sources: session-logs/20260428-080124-aa02
updated: 2026-04-28
arxiv: "2604.20733"
---

# Near-Future Policy Optimization (NPO) — RLVR 혼합 정책의 Quality-Variance 트레이드오프

## 한줄 요약

RLVR(검증 가능한 보상 기반 강화학습)에서 **현재 훈련 런의 미래 체크포인트를 가이드로 사용**함으로써, 신호 품질(Q)은 높고 분산 비용(V)은 낮은 최적 보조 궤적 소스를 실현한 혼합 정책 방법론.

Institute of Information Engineering, CAS / JD.COM (arXiv 2604.20733, 2026).

## 연구 배경

### 문제: Pure On-Policy RLVR의 두 가지 한계

1. **초기 훈련**: 올바른 궤적이 희박 → 그래디언트 신호 붕괴
2. **후기 훈련**: 롤아웃 분포가 좁아지면서 성능 고원(plateau)에 도달

**자연스러운 해결책**: 보조 궤적을 혼합 → 그러나 기존 방법들은 모두 부최적 코너에 위치

| 방법 | 신호 품질 Q | 분산 비용 V | 문제 |
|------|------------|------------|------|
| External Teacher (LUFFY) | 높음 | **매우 높음** | 분포 격차가 커서 학습 불안정 |
| Historical Replay (ExGRPO) | 낮음~중간 | 중간 | 과거 체크포인트 품질에 제한됨 |
| Far-Future Replay (RLEP) | 높음 | **높음** | External Teacher와 동일한 문제 |
| Pure On-Policy (GRPO) | 현재 정책 수준 | 없음 | 탐색 한계 돌파 불가 |

## 핵심 아이디어

### 유효 학습 신호 S = Q(Δ) / V(Δ)

보조 궤적 소스의 기여를 두 양으로 정식화:

```
Q(Δ): 신호 품질 — 현재 정책이 실패하는 프롬프트에서 소스가 
      검증-정답 궤적을 생성할 수 있는 비율. Δ(스텝 거리)에 따라 오목 증가.

V(Δ): 분산 비용 — 다른 정책의 궤적을 중요도 가중치로 포함할 때 
      발생하는 그래디언트 분산. Δ에 따라 지수적 증가.

S(Δ) = Q(Δ) / V(Δ)  →  내부 최적 Δ* 존재 (U자형 곡선)
```

Q는 포화되고 V는 지수적으로 증가하므로, 비율 S(Δ)는 **명확한 내부 최대값 Δ***을 가진다.

### NPO의 해법: 같은 훈련 런의 근미래 체크포인트

같은 초기화·아키텍처·최적화 이력을 공유하므로:
- **파라미터 거리 작음** → V(Δ) 낮음
- **최적화가 더 진행** → Q(Δ) 높음

**구현 방식**: 각 프롬프트 x에 대해, 현재 정책이 어려워하는 경우 (group accuracy ≤ τ_gate) 롤아웃 그룹의 n번째 슬롯을 미래 체크포인트 π^(t+Δ)의 검증-정답 궤적으로 교체:

```
G_NPO(x) = {o₁, ..., oₙ₋₁, õₙ}

õₙ = o'ₓ  (미래 체크포인트의 검증-정답 궤적)  if p̂(x) ≤ τ_gate
     oₙ   (순수 온-폴리시)                    otherwise
```

IS(Importance Sampling) 보정: 근미래 특성 덕분에 πθ/π^(t+Δ) ≈ 1 → IS 보정을 생략해도 성능 동일 (ablation 검증됨)

### 두 가지 수동 개입 방식

1. **Early-Stage Bootstrapping**: 짧은 스카우트 런으로 체크포인트 획득, 재시작 후 초기 창에 가이드 적용 → 희박 보상 구간 조기 탈출
2. **Late-Stage Plateau Breakthrough**: 고원 이후까지 계속 훈련, 더 강한 체크포인트로 고원 구간 재학습 → 온-폴리시 천장 돌파

### AutoNPO: 적응형 자동화

수동 개입 시점·롤백 거리를 자동화하는 온라인 컨트롤러:

1. **Trigger**: 보상 EMA 정체 + 엔트로피 감소 동시 패턴 감지 → 탐색 붕괴 시그니처
2. **Rollback Distance**: 확인 롤아웃으로 각 Δ의 Q̂(Δ)/V̂(Δ) 추정 → S̃ 최대화하는 Δ* 선택
3. **Execution**: Δ* 체크포인트 기준 mistake pool B에 대해 가이드 궤적 생성 → NPO 메커니즘으로 구간 재학습

## 주요 결과

실험: Qwen3-VL-8B-Instruct, GRPO 백본, 8가지 멀티모달 추론 벤치마크 (MathVista, MathVision, WeMath, MMMU-Pro 등)

| 방법 | 평균 Avg. | GRPO 대비 |
|------|----------|----------|
| Base LLM | — | 기준 |
| GRPO (Pure On-Policy) | 57.88 | 기준 |
| LUFFY (External Teacher) | < GRPO | 분산 비용이 신호 품질 압도 |
| NPO (수동 개입) | **62.84** | +4.96 |
| AutoNPO | **63.15** | +5.27 |

**훈련 동역학 주요 관찰:**
- GRPO: 꾸준한 엔트로피 붕괴 (좁은 추론 템플릿으로 수렴)
- AutoNPO: 개입 창 이후 엔트로피가 재확장 → 탐색 다양성 유지 → 더 높은 고원

## 한계 및 향후 연구

- **체크포인트 저장 오버헤드**: 여러 Δ를 탐색하려면 중간 체크포인트 보관 필요
- **Δ* 의존성**: 최적 Δ는 훈련 스텝, 데이터셋, 모델 크기에 따라 달라짐
- **On-Policy Distillation으로 확장 가능**: 저자들이 후속 연구로 제안
- **Self-Taught RLVR 프레임워크**: informed self(특권 정보), temporal self(NPO), parallel self(예정)

## 범용 시사점

1. **"강할수록 좋다"는 오해 수정**: 외부 teacher가 더 강해도, 분산 비용이 신호 품질을 압도하면 오히려 성능 하락
2. **훈련 런 자체가 데이터 소스**: 별도의 외부 모델·데이터 없이 같은 훈련 런의 미래 체크포인트를 활용
3. **엔트로피 붕괴가 고원의 전조**: 탐색 다양성 유지가 late-stage 성능 천장을 결정

## 관련 논문

- DeepSeek-R1 (Guo et al., 2025) — RLVR의 핵심 기반
- LUFFY (Yan et al., 2025) — External teacher 혼합 정책 방법
- ExGRPO (Zhan et al., 2025) — Historical replay 기반 혼합 정책

## 변경 이력

- 2026-04-28: 최초 생성 (arXiv 2604.20733 분석)
