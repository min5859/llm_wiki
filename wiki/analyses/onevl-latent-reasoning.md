---
title: "OneVL — 자율주행을 위한 1스텝 잠재 추론·계획 모델"
domain: "research"
sensitivity: "public"
tags: ["autonomous-driving", "VLM", "VLA", "chain-of-thought", "latent-reasoning", "xiaomi", "real-time"]
created: "2026-04-23"
updated: "2026-04-23"
sources:
  - "session-logs/20260423-080131-e2e6-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md"
confidence: "high"
related:
  - "wiki/analyses/meanflow-text-to-image.md"
---

# OneVL — 자율주행을 위한 1스텝 잠재 추론·계획 모델

Xiaomi Embodied Intelligence Team 개발. arXiv 2604.18486.
자율주행 VLA(Vision-Language-Action model)에서 Chain-of-Thought(CoT)의 정확도를 유지하면서 답변-only 예측 수준의 지연시간을 달성하는 것이 목표.

## 핵심 내용

### 해결한 문제

자율주행에서의 CoT 딜레마:

| 방식 | 장점 | 단점 |
|------|------|------|
| 답변 only (answer-only) | 빠름 | 추론 부재, 해석 불가 |
| 명시적 CoT (explicit CoT) | 정확, 해석 가능 | 지연이 체인 길이에 비례 → 실시간 불가 |
| **OneVL (잠재 CoT)** | **두 장점 결합** | — |

> 핵심 통찰: 명시적 CoT의 대부분은 맥락 반복이나 공식적 패턴 — 실제로 필요한 인과 구조만 컴팩트한 잠재 토큰으로 압축 가능

### 아키텍처

```
메인 VLM
  └─ Latent Token 생성 (압축된 추론)
       ├─ Language Auxiliary Decoder → 자연어 설명 (훈련 시 지도 신호)
       └─ Visual Auxiliary Decoder  → 시각적 설명 (훈련 시 지도 신호)
```

#### Prefill Inference

추론 시에는 Auxiliary Decoder를 생략하고 잠재 토큰만으로 예측 수행 → 답변-only와 동일한 지연시간 달성.

### 3단계 훈련 파이프라인

| 단계 | 내용 |
|------|------|
| 사전 준비 | Visual Auxiliary Decoder 자기지도 사전학습 |
| Stage 0 | 메인 모델 워밍업 |
| Stage 1 | Auxiliary Decoder 워밍업 |
| Stage 2 | 엔드투엔드 공동 파인튜닝 |

### 주요 결과

- 명시적 CoT를 뛰어넘는 최초의 모델 (정확도 기준)
- 달성 지연시간: 답변-only 예측과 동등
- 평가 벤치마크: NAVSIM, ROADWork, Impromptu, Alpamayo-R1

## 세부 사항

### Latent Token 설계

- 잠재 토큰은 추론 체인의 "핵심 인과 구조"만 인코딩
- 압축 관점(compression view of intelligence): 중간 단계 명시 강제 → 체계적·일반화된 표현 학습
- 명시적 CoT의 중복성(문맥 반복, 공식적 패턴)을 제거

### 자율주행 적용

- VLM → VLA 확장: 언어·시각 이해 + 궤적 웨이포인트/제어 신호 출력
- Scene semantics, 에이전트 행동 예측, 고수준 주행 의도를 잠재 공간에 통합

## 관련 맥락

- [[meanflow-text-to-image]] — 1스텝 생성 가속화라는 공통 테마
- 유사 접근: 암묵적 CoT(Implicit CoT), 내면화 추론(Internalized Reasoning) 연구
- VLA 관련 선행 연구: DriveLM, DriveVLM, UniAD 등

## 변경 이력

- 2026-04-23: 최초 작성 (세션 로그 20260423-080131-e2e6, arXiv 2604.18486)
