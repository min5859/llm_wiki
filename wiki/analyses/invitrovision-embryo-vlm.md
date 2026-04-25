---
title: "InVitroVision — IVF 배아 이미지 캡셔닝을 위한 VLM LoRA 적응"
domain: "personal"
sensitivity: "public"
tags: ["vlm", "lora", "domain-adaptation", "medical-ai", "paligemma", "image-captioning", "ivf"]
created: "2026-04-25"
updated: "2026-04-25"
sources:
  - "session-log: 20260425-080113-edb6 (arXiv 2604.21061)"
confidence: "high"
related:
  - "wiki/analyses/onevl-latent-reasoning.md"
---

# InVitroVision — IVF 배아 이미지 캡셔닝을 위한 VLM LoRA 적응

오스트리아 연구팀이 PaliGemma-2(Google)를 LoRA로 IVF(체외수정) 배아 이미징 도메인에 적응시킨 연구. 1,000개 이미지만으로 fine-tuning하여 ChatGPT 5.2를 능가. 소량 데이터로 범용 VLM을 의료 특화 모델로 전환하는 LoRA 적응의 실효성을 보인다. (arXiv: 2604.21061, 2026)

## 핵심 내용

### 연구 배경

- IVF 워크플로우에서 배아 형태 평가는 주관성 높아 기관 간 일관성 부족
- 기존 AI 접근: 이미지 기반 단일 모달, 분류 모델 중심 → 임상 해석 어려움
- VLM을 이용한 자연어 설명 생성으로 해석 가능성과 표준화 동시 달성 목표

### 방법론

**모델**: PaliGemma-2 + LoRA 어댑터
- 백본(vision encoder + 언어 모델) 가중치 동결
- LoRA를 언어·비전-언어 커넥터 레이어에 적용
- 입력: `<image>` 토큰 + "describe en" 프롬프트
- 옵티마이저: AdamW, 그래디언트 누적

**데이터**:
- 공개 TL(Time-Lapse) 배아 데이터셋 (Gomez et al. 2022)
- 1,100 프레임 → 1,000 학습 / 100 테스트
- 임상 배아학자 2인이 자연어로 어노테이션

### 임상 맞춤 평가 메트릭

기존 BLEU/ROUGE 등은 임상 정확성을 반영 못함 → 새 복합 점수 설계:

| 기준 | 최대점 | 가중치(α/β/γ) |
|---|---|---|
| ER (배아 인식) | 1.0 (이진) | — |
| ECC (세포 주기 인식) | 3.0 | α=4 |
| MD (형태 세부 사항) | 4.0 | β=2 |
| PD (위치 세부 사항) | 3.0 | γ=1 |

ECC에 최고 가중치 (배아 단계 오인이 임상적으로 가장 치명적)

### 성능 결과

| 모델 | ECC | MD | PD | 총점 |
|---|---|---|---|---|
| BASE (no fine-tune) | 0.93 | 0.97 | 0.99 | 0.29 |
| IVV-400 | 2.04 | 1.89 | 0.98 | 0.57 |
| IVV-700 | 2.14 | 2.17 | 0.86 | 0.60 |
| **IVV-1000** | **2.30** | **2.52** | **1.03** | **0.67** |
| ChatGPT 5.2 | 1.87 | 1.62 | 0.98 | 0.52 |

> ChatGPT 5.2는 cleavage stage(ECC2, ECC3)에서는 경쟁력 있으나, compaction 단계(t9+~)부터 급격히 성능 하락. 도메인 fine-tuning의 가치가 가장 두드러지는 구간.

### 핵심 인사이트

1. **1,000개 이미지** 로 GPT-5.2 능가 — 소량 데이터 LoRA 적응의 실효성
2. **범용 모델의 한계**: 비과학적 표현("shared boundary" vs "zona pellucida"), 이미지 근거 없는 추론 경향
3. **도메인 특화 평가 메트릭** 필요성 — 의료 AI에서 어휘 유사도 기반 지표의 부적절함
4. **RAG 통합 가능성**: VLM이 표준화된 배아 설명 생성 → RAG 쿼리로 임상 가이드라인 검색

### 한계

- 단일 TL 데이터셋 → 타 기관 장비 환경 도메인 시프트 미검증
- 공간적 위치 추론(PD)은 여전히 취약
- 임상 결과(착상률, 생존율)와의 연결 미확인

## 관련 맥락

- 소량 데이터 LoRA 적응은 [[onevl-latent-reasoning]]의 도메인 특화 VLA와 비교 가능
- 의료 도메인 AI에서 임상 맞춤 평가 메트릭 설계의 필요성을 잘 보여주는 사례

## 변경 이력

- 2026-04-25: 최초 생성 (출처: session-log 20260425-080113-edb6, arXiv 2604.21061)
