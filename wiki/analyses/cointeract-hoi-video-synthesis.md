---
title: "CoInteract — 물리적 일관성 HOI 영상 합성"
domain: "personal"
sensitivity: "public"
tags: ["paper", "video-generation", "diffusion", "HOI", "MoE", "DiT"]
created: "2026-04-26"
updated: "2026-04-26"
sources:
  - "session-logs/20260426-080023-cb0a-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md"
arxiv: "2604.19636"
confidence: "high"
related: []
---

# CoInteract — 물리적 일관성 Human-Object Interaction 영상 합성

## 한줄 요약

Human-Aware MoE와 Spatially-Structured Co-Generation으로 손-물체 교합(interpenetration)을 제거하고 구조적 안정성을 확보한 end-to-end HOI 영상 합성 프레임워크.

## 연구 배경 및 동기

e-커머스·디지털 광고 등에서 사람이 제품을 조작하는 HOI(Human-Object Interaction) 영상 합성 수요가 급증하고 있다. 기존 Diffusion 모델은 두 가지 반복 실패를 보인다:

1. **구조 붕괴(Structural Collapse)**: 손가락 병합, 얼굴 블러 등 고빈도 세부 영역 왜곡
2. **물리 위반(Physical Violation)**: 손이 물체를 관통하는 교합 현상

기존 접근은 ① 다중 조건 생성(multi-condition, 전처리 부담), ② 다중 참조 생성(multi-reference, 상호작용 구조 부재) 두 계열로 나뉘며, 두 방식 모두 RGB 픽셀 중심이라 3D 공간 관계를 학습하지 못한다.

## 핵심 방법론

### 1. Spatially-Structured Co-Generation (쌍스트림 공동 생성)

DiT(Diffusion Transformer) 백본에서 **RGB 스트림**과 **HOI 구조 스트림**을 동시에 학습한다.

- **HOI 스트림**: 인체 메시를 실루엣으로 투영 + 물체 마스크 융합 → 텍스처 없이 상호작용 경계만 남긴 3채널 렌더링
- **공유 백본**: 두 스트림이 같은 DiT 파라미터를 사용하되, 각자 adaptive layer norm의 scale/shift만 별도 관리
- **2단계 비대칭 코어텐션**:
  - Stage 1 (5K 이터): 양방향 풀어텐션 → 두 스트림이 서로 참조하며 빠른 수렴
  - Stage 2 (2K 이터): 비대칭 마스크 적용 — RGB 쿼리는 RGB 토큰만 참조, HOI 쿼리는 양쪽 참조
  - 추론 시: HOI 브랜치 제거 → **추가 비용 0**. HOI 손실의 역전파가 공유 파라미터를 통해 RGB 생성기에 상호작용 구조를 내재화

### 2. Human-Aware MoE (전문가 혼합)

손·얼굴은 고주파 세부 영역이라 공통 FFN만으로는 부족. DiT 블록 내 FFN을 4개 전문가로 교체:

| 전문가 | 역할 |
|--------|------|
| Shared | 원래 DiT FFN 재사용 (단축 경로) |
| Head | 얼굴 토큰 전담 |
| Hand | 손 토큰 전담 |
| Base | 나머지 토큰 |

- 라우터: 2층 MLP, 얼굴·손 바운딩박스로 지도학습. stop-gradient로 DiT 표현 학습 간섭 방지
- 파라미터 오버헤드: **1.04× 수준으로 매우 경량**

### 3. 3D RoPE 좌표 할당

이질적 모달리티(모션 히스토리, 참조 이미지, RGB·HOI 스트림)를 단일 시공간 좌표계로 통합:
- HOI 스트림은 w 축에 음수 좌표 부여 → 픽셀 레벨 정합 유지
- 참조 이미지는 t >> T(먼 미래)로 배치 → 전역 아이덴티티 앵커 역할

## 주요 결과

**테스트셋**: 12K 클립 중 50개 held-out (다양한 제품 카테고리, 미학습 아이덴티티)

| 지표 | AnchorCrafter | Phantom | InteractAvatar | **CoInteract** |
|------|:---:|:---:|:---:|:---:|
| VLM-QA↑ (HOI 타당성) | 0.22 | 0.50 | 0.62 | **0.72** |
| HQ↑ (손 구조) | 0.596 | 0.650 | 0.696 | **0.724** |
| Smooth↑ (시간 일관성) | 0.9743 | 0.9916 | 0.9938 | **0.9951** |
| DINOid↑ (아이덴티티) | 0.538 | 0.654 | 0.658 | **0.671** |
| FaceSim↑ | 0.487 | 0.593 | 0.681 | **0.696** |

**사용자 연구** (24명, 10케이스씩): 상호작용 타당성 평균 순위 1.79위 (낮을수록 좋음, 경쟁자 최고 3.33).

**Ablation 핵심**:
- Co-Gen 제거 시: VLM-QA 0.72 → 0.48 (−33.3%, 가장 큰 하락)
- MoE 제거 시: HQ 0.724 → 0.658, FaceSim 0.696 → 0.662
- HOI 브랜치 추론 시 유지 시: VLM-QA +0.04 향상이지만 추론 비용 **4.13× 급등**

## 한계점 및 향후 연구

- 40시간, 12K 클립의 자체 curated 데이터셋에 의존 — 공개 데이터 없음
- 4D 물리 시뮬레이션 없이 구조 스트림으로 대체하므로, 복잡 물리(무게, 탄성) 모사 불가
- HOI 구조 스트림 생성을 위한 별도 파이프라인(SAM3, SAM3D-body, Qwen-Edit) 전처리 필요

## 실무 적용 가능성

- e-커머스 제품 광고 영상 자동 생성 (음성 + 참조 이미지만으로)
- 가상 앵커(홈쇼핑) 콘텐츠 제작 자동화
- HOI 구조 스트림 학습 패턴은 다른 도메인(수술 영상, 제조 공정)에도 전이 가능

## 핵심 아이디어 — 비대칭 Co-Attention의 이점

추론 시 HOI 브랜치를 제거하면서도 성능을 유지하는 핵심: HOI 손실의 역전파가 **공유 파라미터**를 통해 RGB 생성기에 구조 정보를 이식한다. "학습엔 참여, 추론엔 사라짐"이라는 구조는 효율-성능 상충을 크게 줄인다.

## 관련 논문

- WanS2V (Gao et al., 2025, arXiv 2508.18621) — 초기화 베이스라인
- AnchorCrafter (Xu et al., arXiv 2604.17748) — 다중 조건 생성 대표 기준선
- CyberHost (Lin et al., arXiv 2409.01876) — Region Codebook Attention 방식 손/얼굴 처리

## 변경 이력

- 2026-04-26: 최초 작성 (세션 로그 20260426-080023-cb0a에서 추출)
