---
title: "OpenGame — 에이전틱 코딩으로 웹 게임 자동 생성"
domain: "research"
sensitivity: "public"
tags: ["agent", "code-generation", "game-development", "LLM", "RL", "benchmark", "template-pattern", "Phaser3"]
created: "2026-04-27"
updated: "2026-04-27"
sources:
  - "session-logs/20260427-080119-fdca-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md"
confidence: "high"
related:
  - "wiki/analyses/agentspex-agent-specification-language.md"
---

# OpenGame — 에이전틱 코딩으로 웹 게임 자동 생성

CUHK MMLab이 제안한 end-to-end 웹 게임 생성을 위한 첫 번째 오픈소스 에이전틱 프레임워크. arXiv 2604.18394.
자연어 명세 → 완전히 플레이 가능한 2D 웹 게임(HTML5/Phaser 3)을 자율적으로 생성.

## 핵심 내용

### 해결한 문제

범용 LLM/코드 에이전트가 게임 개발에서 겪는 3가지 반복적 실패 모드:

| 실패 모드 | 내용 |
|---------|------|
| **Logical Incoherence (논리적 비일관성)** | 게임 루프 전체에서 전역 상태를 추적하지 못해 중단·무한루프·핵심 메카닉 미구현 |
| **Engine-Specific Knowledge Gaps (엔진 지식 부재)** | 프레임워크 네이티브 물리/씬/이벤트 시스템을 무시하고 처음부터 재구현 |
| **Cross-File Inconsistencies (파일 간 불일치)** | 에셋 키 불일치, 씬 와이어링 오류, 설정 필드 누락, 초기화 순서 오류 |

### 시스템 구성

| 컴포넌트 | 역할 |
|---------|------|
| **Game Skill** | 재사용 가능한 진화형 역량: Template Skill + Debug Skill |
| **GameCoder-27B** | 게임 엔진 특화 LLM (Qwen3.5-27B 기반, 3단계 훈련) |
| **OpenGame-Bench** | 동적 플레이어빌리티 평가 파이프라인 (150개 프롬프트) |

### Game Skill 상세

**Template Skill**:
- 게임 장르/물리 특성별로 5개 전문화 템플릿 패밀리(플랫포머, 탑다운, 그리드 로직, 타워 디펜스, UI 헤비) 라이브러리(L) 구축
- 단일 범용 메타 템플릿(M₀)에서 시작해 실행 경험을 통해 라이브러리 확장
- **Physics-First 분류** — 장르명이 아닌 중력, 시점, 이동 유형으로 아키타입 결정

**Debug Skill**:
- 검증된 수정 사례(verified fixes)의 living protocol(P) 유지
- Pre-execution 검증(컴파일 전 에셋 키 불일치 등 고빈도 오류 클래스 사전 차단) + Post-execution 수정(런타임 오류 대응)

### GameCoder-27B 훈련 파이프라인

1. **Continual Pre-Training (CPT)** — Phaser 3 API, 멀티파일 프로젝트 구조에 대한 도메인 사전 학습
2. **Supervised Fine-Tuning (SFT)** — 고품질 합성 QA 데이터로 게임 설계 명세 정렬 (Intent Alignment +1.9)
3. **Execution-Grounded RL** — 유닛 테스트 실행 피드백 기반 강화학습 (Visual Usability·IA 추가 향상)

### OpenGame-Bench 평가 지표

정적 코드 분석이 아닌 **헤드리스 브라우저 실행 + VLM 판정** 기반 동적 평가:

| 지표 | 내용 |
|------|------|
| **Build Health (BH)** | 컴파일 및 런타임 안정성 |
| **Visual Usability (VU)** | 인터랙티브한 콘텐츠 렌더링 일관성 |
| **Intent Alignment (IA)** | 자연어 프롬프트 요구사항 충족도 |

### 주요 결과 (OpenGame-Bench, 150개 게임 프롬프트)

**직접 LLM 비교**:

| 시스템 | Build Health | Visual Usability | Intent Alignment |
|-------|-------------|-----------------|-----------------|
| Claude Sonnet 4.6 (단독) | 58.5 | 50.8 | 50.3 |
| GPT-5.1 (단독) | 57.4 | 52.9 | 49.4 |
| Gemini 3.1 Pro (단독) | 53.6 | 60.2 | 42.1 |

**에이전틱 프레임워크 비교**:

| 시스템 | Build Health | Visual Usability | Intent Alignment |
|-------|-------------|-----------------|-----------------|
| Cursor + Claude Sonnet 4.6 | 66.8 | 61.4 | 58.9 |
| qwen-code + Claude Sonnet 4.6 | 63.2 | 54.3 | 57.8 |
| **OpenGame + GameCoder-27B** | **63.9** | **57.0** | **54.1** |
| **OpenGame Full Workflow** | **72.4** | **67.2** | **65.1** |

- GameCoder-27B: 모든 오픈소스/클로즈드소스 단독 LLM 기준 BH·IA 1위
- 여전히 weighted 기계적 요구사항의 약 34.9%는 부분/전체 미충족 → 게임 생성의 본질적 어려움 반영

### Ablation 결과 (핵심 인사이트)

**에이전트 워크플로우 컴포넌트별 기여도** (Claude Sonnet 4.6 백엔드 고정):

| 제거 컴포넌트 | BH 손실 | IA 손실 |
|------------|--------|--------|
| Hook-Driven Implementation 제거 | **-10.1** | **-11.6** |
| Three-Layer Reading 제거 | -4.6 | -8.6 |
| Physics-First 분류 제거 | -2.2 | -3.5 |

→ **Hook-Driven Implementation(템플릿 메서드 패턴)이 가장 중요한 컴포넌트** — 에이전트가 베이스 클래스 훅을 오버라이드하는 방식이 아니라 처음부터 구현하면 생명주기 관리 오류가 치명적으로 증가

**Game Skill 진화 효과**:

| 설정 | Build Health | Intent Alignment |
|------|-------------|-----------------|
| 정적 단일 템플릿 + 정적 규칙 체크리스트 | 60.5 | 51.2 |
| 5개 진화 라이브러리 + Full Living Protocol | **72.4** | **65.1** |

## 세부 사항

### 에이전트 6단계 자율 워크플로우

1. **Classification & Scaffolding** — 게임 타입 분류 → 해당 템플릿 패밀리 복사
2. **Game Design** — 기술적 GDD(Game Design Document) 생성, 파일별 todo 확장
3. **Asset Synthesis** — GDD 에셋 레지스트리 기반 이미지/타일맵 생성
4. **Config & Registration** — gameConfig.json 병합, 씬 등록
5. **Code Implementation** — 3계층 읽기 전략 (API 요약→타겟 소스→구현 가이드)
6. **Verification** — 정적 자가 검토 체크리스트 → `npm run build/test/dev`

### 3계층 읽기 전략 (Three-Layer Reading)

대형 컨텍스트 윈도우에서도 "lost-in-the-middle" 오류 방지를 위한 점진적 현저성(salience) 제어:
1. API 요약 레벨 (전체 조망)
2. 타겟 소스 레벨 (관련 파일만)
3. 구현 가이드 레벨 (상세 지침)

### 평가의 어려움

기존 소프트웨어 벤치마크(SWE-bench 등)의 정적 입출력 유닛 테스트는 게임의 시간적·인터랙티브 특성에 부적합 → OpenGame-Bench의 동적 평가 방식 도입 이유

## 관련 맥락

- [[agentspex-agent-specification-language]] — 에이전트 워크플로우 명세 언어, 구조화된 에이전트 설계의 중요성
- Phaser 3: HTML5 게임 엔진 (JavaScript), OpenGame의 타겟 프레임워크
- SWE-bench: 소프트웨어 엔지니어링 에이전트 표준 벤치마크 (저장소 수준 이슈 해결)
- GameDevBench: 게임 개발 특화 벤치마크 (OpenGame 이전 선행 연구)

## 변경 이력

- 2026-04-27: 최초 작성 (세션 로그 20260427-080119-fdca, arXiv 2604.18394)
