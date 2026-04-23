---
title: "Tstars-Tryon 1.0 — 상업용 가상 피팅 시스템"
domain: "research"
sensitivity: "public"
tags: ["virtual-try-on", "diffusion", "image-generation", "e-commerce", "MMDiT", "RL", "taobao"]
created: "2026-04-23"
updated: "2026-04-23"
sources:
  - "session-logs/20260423-080041-976a-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md"
confidence: "high"
related:
  - "wiki/analyses/meanflow-text-to-image.md"
---

# Tstars-Tryon 1.0 — 상업용 가상 피팅 시스템

Alibaba Taobao(Pailitao Team)가 개발한 상업 규모 가상 피팅(Virtual Try-On) 시스템. arXiv 2604.19748.
Taobao App에 실제 배포되어 수백만 사용자, 수천만 건 요청을 처리하고 있다.

## 핵심 내용

### 해결한 문제

기존 가상 피팅 시스템의 한계:
- **로버스트성 부족** — 극단적 포즈, 강한 역광, 모션 블러 등 실세계 조건 처리 실패
- **단일 아이템 한계** — 여러 의류를 동시에 조합하는 멀티 아이템 피팅 불가
- **지연 문제** — 상업 배포에 필요한 실시간 수준 달성 불가
- **데이터 부족** — 멀티 아이템 피팅 학습 데이터 희소

### 시스템 구성

| 컴포넌트 | 내용 |
|---------|------|
| **모델 아키텍처** | MMDiT 기반 통합 아키텍처 (기존 inpainting 로직 탈피) |
| **파라미터** | 5B |
| **지원 카테고리** | 상의·하의·치마·드레스·코트·신발·가방·모자 (8종) |
| **멀티 레퍼런스** | 최대 6장 동시 입력 |
| **훈련 전략** | Pre-train → SFT → RL (DiffusionNFT) |

### 추론 속도 (H200 기준)

| 시나리오 | Tstars-Tryon 1.0 | 오픈소스 대비 |
|---------|-----------------|-------------|
| 단일 의류 | **3.92초** | QwenEdit-2511, Flux.2 dev ≈ 200초 |
| 멀티 의류 (평균 5장) | **6.74초** | — |

CFG 증류 + Step 증류 조합으로 달성. CFG-free 추론.

### 훈련 파이프라인

1. **Pre-training** — 태스크·컨텐츠 균형 데이터셋, 점진적 난이도 스케일링, 고해상도 연속 학습
2. **High-quality SFT** — 수직 도메인 데이터 균형, 포괄적 메트릭 모니터링
3. **RL (DiffusionNFT)** — 그룹 레벨 trajectory 샘플링, 다차원 보상 파이프라인. 포지티브 trajectory를 네거티브 대비 우선화

### 데이터 엔진

- 자동화 파이프라인: 이미지 요소 분해 + 검색 기반 recall 시스템
- 커스텀 captioner로 전문가 수준 설명 생성
- VLM 포스트필터링 + 지각 메트릭 스크리닝

### 벤치마크 (Tstars-VTON Benchmark)

자체 벤치마크 개발:
- 1780 paired 샘플
- 5 의류 카테고리 + 3 액세서리 카테고리
- 465 세분화 서브카테고리
- 1~6 레이어 피팅 아이템

## 세부 사항

### 비표준 주제 지원

- 디지털 휴먼, 애니메이션 캐릭터
- 복수 인물 동시 처리
- 임산부 의류, 반려동물, 인형 등 특수 케이스

### 인프라 최적화

- Variable resolution 네이티브 지원
- 임의 레퍼런스 이미지 수 지원
- Data Parallelism + Tensor Parallelism + Data Packing (Diffusion Transformer용 개조)
- 전통적 bucketing 방식 대비 계산 낭비 제거

### Prompt Enhancement

맞춤형 rewriter 모델 도입: 복잡한 가상 피팅 편집 과정을 정확하게 기술하는 프롬프트 자동 생성.

## 관련 맥락

- [[meanflow-text-to-image]] — 동일 시기(2026-04) 확산 모델 가속화 관련 논문
- MMDiT 아키텍처: Stable Diffusion 3 계열에서 사용하는 Multi-Modal Diffusion Transformer
- DiffusionNFT: 확산 모델 RL 학습 방법 (Zheng et al., 2025)

## 변경 이력

- 2026-04-23: 최초 작성 (세션 로그 20260423-080041-976a, arXiv 2604.19748)
