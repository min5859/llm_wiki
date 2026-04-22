---
title: "MeanFlow의 텍스트 조건부 이미지 생성 확장 (EMF)"
domain: "research"
tags: ["diffusion", "MeanFlow", "text-to-image", "few-step-generation", "text-encoder", "discriminability", "disentanglement"]
created: "2026-04-22"
updated: "2026-04-22"
sources:
  - "session-logs/20260422-080037-7719-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md"
confidence: "high"
related:
  - "wiki/analyses/diffusion-snr-t-bias.md"
arxiv: "2604.18168"
---

# MeanFlow의 텍스트 조건부 이미지 생성 확장 (EMF)

논문 "Extending One-Step Image Generation from Class Labels to Text via Discriminative Text Representation" (arXiv: 2604.18168, Nankai University / Alibaba AMAP)의 핵심 발견 정리.

MeanFlow 프레임워크를 클래스 레이블 조건에서 자유로운 텍스트 입력으로 확장하는 방법을 제시하며, 핵심은 **텍스트 인코더의 discriminability와 disentanglement** 특성이다.

## 핵심 내용

### MeanFlow란

Flow Matching이 각 timestep의 순간 속도(instantaneous velocity)를 모델링하는 데 비해, MeanFlow는 시간 구간 [t, r] 에 걸친 **평균 속도(average velocity)**를 직접 학습한다. 이를 통해 단 1~4 step 만으로 고품질 이미지 생성이 가능하다.

- 기존 MeanFlow 연구는 ImageNet의 클래스 레이블 조건부 생성에만 집중
- 텍스트 조건으로 확장하려 할 때 단순히 LLM 기반 텍스트 인코더를 연결하는 것만으로는 성능이 불만족스럽다는 사실 발견

### 클래스 레이블 vs 텍스트의 근본적 차이

| 특성 | 클래스 레이블 | 텍스트 |
|---|---|---|
| 임베딩 분포 | 드문드문한 클러스터 (inter-class margin 큼) | 연속적이고 조밀한 분포 |
| Velocity field 경로 | 매끄러운 궤적 (순간 속도 ≈ 평균 속도) | 굴곡진 궤적 (순간 속도 ≠ 평균 속도) |
| 조건 복잡도 | 단일 개념 | 다중 속성·객체·공간 관계 |
| Few-step 적합성 | 높음 | 낮음 (보정 step 필요) |

텍스트 임베딩은 "blue teapot" vs "red teapot" 처럼 의미적으로 유사한 프롬프트가 이웃한 공간을 점유하여, velocity field가 세밀한 의미적 구분을 위해 굴곡진 경로를 택하게 된다. Step 수가 제한된 MeanFlow에서는 이 굴곡이 semantic drift로 이어진다.

### 핵심 발견: Discriminability & Disentanglement

Few-step 생성에 적합한 텍스트 표현이 갖춰야 할 두 가지 조건:

**1. Discriminability (판별력)**
- 텍스트 임베딩이 대응하는 이미지 임베딩과 잘 정렬되어야 함
- 측정: COCO 2017 train split에서 text→image retrieval → DINOv3로 재채점
- BLIP3o-NEXT: 0.734, CLIP: 0.730, Gemma: 0.713, T5: 0.634

순수 언어 코퍼스만으로 학습된 T5/SANA-1.5 인코더는 비전-언어 정렬이 없어 discriminability가 낮다.

**2. Disentanglement (의미 분리)**
- 전체 프롬프트의 임베딩과 부분 프롬프트의 임베딩 간 거리가 작아야 함
- 즉, 텍스트의 언어 구조를 임베딩에 보존해야 함
- 측정: DPG-Bench 프롬프트에서 일부 텍스트 제거 후 cosine 거리 비교
- BLIP3o-NEXT: 0.999, Gemma: 0.987, CLIP: 0.967, T5: 0.893

자기회귀(next-token prediction) 방식으로 학습된 LLM 계열 인코더가 disentanglement에서 우수.

### EMF 설계

- 기반 모델: BLIP3o-NEXT (LLM 기반 텍스트 인코더, 비전-언어 정렬 사전학습)
- MeanFlow를 위해 temporal embedding을 두 개로 분리:
  - `φ_interval(t-r)`: 구간 길이
  - `φ_end(t)`: 구간 끝 시간
- 조건부 임베딩: `φ_cond(t, r) = φ_interval(t-r) + φ_end(t)`
- 학습 데이터: ~170,000 샘플 (BLIP3o-60k, shareGPT-4o, Echo-4o)

## 주요 결과 (GenEval 벤치마크)

| 모델 | Steps | Overall |
|---|---|---|
| BLIP3o-NEXT | 30 | 0.91 |
| **EMF (ours)** | **4** | **0.90** |
| EMF | 2 | 0.85 |
| EMF | 1 | 0.74 |
| SANA-Sprint | 4 | 0.75~0.77 |
| FLUX.1-schnell | 4 | 0.69 |

4-step EMF가 BLIP3o-NEXT 30-step 수준 달성. 추론 시간: 30-step 1.24s → 4-step 0.22s (H200).

DPG-Bench에서도 1-step EMF(Overall 77.36)가 BLIP3o-NEXT 4-step(78.15)에 근접.

### 왜 SANA-1.5에서는 실패했는가

SANA-1.5에 MeanFlow를 적용하면:
- 동일 SFT 데이터로 텍스트 인코더를 추가 fine-tuning해도 GenEval 4-step 0.47~0.50 수준에 머묾
- SANA-1.5의 Gemma 기반 인코더는 T5보다 disentanglement가 좋지만 **discriminability가 부족**
- MeanFlow는 average velocity 학습에 실패하지만, 20-step으로는 원래 성능 유지 → 텍스트 조건이 문제, trajectories 파괴는 아님

## 실무 적용 시사점

- T2I few-step 생성에서 텍스트 인코더 선택은 단순 "더 큰 LLM"이 아닌 **비전-언어 정렬 여부**가 결정적
- MeanFlow 계열 방법론 적용 시 사전에 텍스트 인코더의 discriminability/disentanglement를 측정하는 것이 합리적
- Consistency 모델 계열의 distillation 방식과 달리, MeanFlow는 step 수 증가 시 성능이 계속 향상 (saturation 없음)

## 관련 논문

- **MeanFlow** (Geng et al., 2025, arXiv: 2505.13447): 원본 MeanFlow 프레임워크
- **BLIP3o-NEXT** (Chen et al., 2025, arXiv: 2510.15857): 기반 모델로 사용된 통합 멀티모달 모델
- **SANA-1.5** (Xie et al., 2025, arXiv: 2501.xxxx): 비교 대상, LLM 인코더 but 비전 정렬 미흡

## 변경 이력

- 2026-04-22: 최초 생성 (출처: session-log 7719, arXiv 2604.18168 논문 본문)
