---
title: "CLAUDE.md 완전 가이드 — 작성법·패턴·템플릿"
domain: "personal"
sensitivity: "public"
tags: ["CLAUDE.md", "Claude Code", "프롬프트 설계", "Memory", "하네스 엔지니어링", "AGENTS.md"]
created: "2026-04-15"
updated: "2026-04-16"
sources:
  - "raw/CLAUDE.md 완전 가이드-김재우-202604.pdf"
  - "raw/CLAUDE.md-완전가이드-12-실물공개-프로젝트규칙패턴.pdf"
  - "raw/CLAUDE.md-완전가이드-13-스타터템플릿.pdf"
  - "raw/CLAUDE.md-완전가이드-14_다음단계.pdf"
  - "raw/음성 260414_AA-2-3_original.txt"
  - "raw/음성 260414_AA-2-5_original.txt"
  - "raw/음성 260415_AA-3-4_original.txt"
confidence: "high"
related:
  - "wiki/claude-code-setup.md"
  - "wiki/claude-code-advanced.md"
  - "wiki/ai-agent-basics.md"
---

# CLAUDE.md 완전 가이드 — 작성법·패턴·템플릿

CLAUDE.md는 Claude Code에 자연어로 프로젝트 규칙을 전달하는 지시서 파일이다. 올바르게 작성하면 매번 같은 지시를 반복하지 않아도 되고, 팀 전체가 일관된 방식으로 Claude를 활용할 수 있다.

## 핵심 내용

### CLAUDE.md의 4계층 강제력

| 순위 | 방법 | 강제력 | 특징 |
|---|---|---|---|
| 1위 | 플로우 내장 (스킬/플러그인) | 최강 | LLM 해석 없이 실행 |
| 2위 | Hook | 강 | 이벤트 발생 시 무조건 실행 |
| 3위 | 함수/스크립트 | 중 | 코드로 구현된 규칙 |
| 4위 | CLAUDE.md 규칙 | 약 | 자연어, LLM이 해석 |

> 핵심: CLAUDE.md는 4가지 강제력 중 가장 약하다. 중요한 제약은 Hook이나 스크립트로 구현해야 한다.

### Memory 4타입

Claude Code는 4가지 타입의 메모리를 활용한다:

| 타입 | 저장 위치 | 지속성 |
|---|---|---|
| In-Context | 현재 대화 창 | 세션 내 |
| External Files | CLAUDE.md, 파일 | 영구 |
| In-Weights | 모델 학습 데이터 | 영구 (변경 불가) |
| In-Cache | 프롬프트 캐시 | 일시적 |

실용적 관점: CLAUDE.md = External Files 메모리. 자주 참조해야 할 정보를 여기에 기록.

## 세부 사항

### 7가지 작성 테크닉

1. **명확한 우선순위 지정**: "이것보다 저것을 먼저"
2. **금지사항 명시**: "절대 하지 말아야 할 것" 섹션 별도 작성
3. **예시 포함**: 모호한 규칙보다 구체적 예시가 효과적
4. **계층적 구조**: 전역 규칙 → 프로젝트 규칙 → 서브디렉토리 규칙
5. **Tier 분류**: 작업 유형별 다른 규칙 적용
6. **도구 접속 정보**: DB 연결, API 엔드포인트 등 고정 정보
7. **병렬 작업 규칙**: 병렬 에이전트 사용 시 충돌 방지 규칙

### 5가지 프로젝트 규칙 패턴

#### 패턴 1: Tier 분류
```markdown
## 작업 Tier
- Tier 1 (단순): 설명 없이 즉시 실행
- Tier 2 (중간): 계획 수립 후 사용자 확인
- Tier 3 (복잡): 반드시 Plan 모드 사용
```

#### 패턴 2: 도구 접속 정보
```markdown
## 환경 정보
- DB: postgresql://localhost:5432/mydb
- API: https://api.internal.company.com
- 문서: docs/ 폴더 참조
```

#### 패턴 3: 문서 우선순위
```markdown
## 참조 우선순위
1. docs/architecture.md (최우선)
2. README.md
3. 코드 내 주석
```

#### 패턴 4: 병렬 작업 규칙
```markdown
## 병렬 에이전트 규칙
- 같은 파일을 동시에 수정하지 않는다
- 공유 변수는 lock 파일로 관리한다
- 완료 후 summary.md에 결과 기록
```

#### 패턴 5: 정지 시 보고 포맷
```markdown
## 블로커 발생 시
형식: "BLOCKED: [이유] / 필요한 것: [무엇]"
예시: "BLOCKED: DB 연결 실패 / 필요한 것: VPN 연결 확인"
```

#### 패턴 6: 실행 환경 고정
```markdown
## 실행 환경
- Python은 uv만 사용
- Node 패키지 매니저는 pnpm만 사용
- 임의로 npm install 하지 말 것
- 실행 명령은 README의 "Development" 섹션을 우선 참조
```

실습에서는 실행 방법을 명시하지 않으면 Claude가 임의로 패키지 매니저나 실행 방식을 선택할 수 있다고 설명했다. 프로젝트가 uv, pnpm, bun처럼 특정 도구를 전제로 한다면 CLAUDE.md에 고정한다.

#### 패턴 7: Prompt 파일 위임
```markdown
## 반복 작업
- 복잡한 작업 지시는 `.claude/prompts/`에 md 파일로 저장한다.
- 세션에서는 "해당 prompt 파일을 읽고 실행"하도록 요청한다.
```

긴 지시를 매번 대화창에 붙여 넣는 대신 파일로 관리하면 팀 공유와 재실행이 쉽고, 세션 주제를 좁게 유지할 수 있다.

### 스타터 템플릿 3가지

#### 미니멀 (10행, 개인 프로젝트)

```markdown
# [프로젝트명]

## 기술 스택
- [언어/프레임워크]

## 규칙
- 테스트는 항상 작성
- 커밋 전 lint 실행
- 복잡한 작업은 Plan 모드 사용
```

#### 스탠다드 (개인, 본격 사용)

```markdown
# [프로젝트명] — Claude Code 지침

## 프로젝트 개요
[한 줄 설명]

## 기술 스택
[상세 기술 목록]

## 디렉토리 구조
[핵심 디렉토리]

## 개발 규칙
[팀 규칙]

## 절대 하지 말아야 할 것
- [금지사항 목록]

## 블로커 발생 시
[보고 포맷]
```

#### 팀 버전 (git 커밋, 팀 공유)

스탠다드에 추가:
```markdown
## 코드 리뷰 기준
[리뷰 체크리스트]

## 브랜치 전략
[브랜치 규칙]

## 접속 정보
[개발 환경 정보]
```

### AGENTS.md — 서브에이전트 전용 지시서

```markdown
# AGENTS.md

## 모든 서브에이전트 공통
- 작업 완료 후 결과 요약 출력
- 외부 API 호출 전 rate limit 확인
- 오류 발생 시 즉시 중단하고 보고

## 코드 리뷰 에이전트 전용
- SOLID 원칙 기준으로 검토
- ...
```

CLAUDE.md가 오케스트레이터를 위한 지시서라면, AGENTS.md는 서브에이전트를 위한 지시서.

### CLAUDE.md의 한계와 대안

| 한계 | 대안 |
|---|---|
| LLM이 해석에 따라 무시 가능 | Hook으로 강제 |
| 길수록 토큰 낭비 | 필수 항목만 유지 |
| 버전 관리 필요 | git 커밋으로 관리 |
| 팀 규칙 강제 불가 | 린터/CI 로 검증 |

### CLAUDE.md와 Rules의 역할 분리

`CLAUDE.md`는 프로젝트 전체 규칙과 온보딩 문서에 적합하다. 반면 `.claude/rules/*.md`는 특정 파일 패턴, 언어, 디렉터리별 규칙에 적합하다.

예:

- Python 파일: uv, 타입 힌트, pytest 기준
- TypeScript 파일: pnpm, strict type, ESLint 기준
- 문서 디렉터리: 문체, 용어, 변경 이력 기준

언어·디렉터리별 규칙을 모두 CLAUDE.md에 몰아넣으면 매 요청마다 불필요한 컨텍스트가 들어간다. 전체 규칙은 CLAUDE.md, 국소 규칙은 rules로 분리한다.

## 관련 맥락

- 좋은 CLAUDE.md는 "신입 개발자 온보딩 문서"처럼 작성하면 된다
- `claude-md-management` 플러그인으로 CLAUDE.md 품질 자동 스코어링 가능
- CI/CD에서 CLAUDE.md를 로드하면 파이프라인에서도 일관된 규칙 적용

## 변경 이력

- 2026-04-16: 녹취 기반 실행 환경 고정, prompt 파일 위임, rules 역할 분리 보강
- 2026-04-15: 최초 생성 (출처: raw/CLAUDE.md 완전 가이드-김재우-202604.pdf, raw/CLAUDE.md-완전가이드-12-실물공개-프로젝트규칙패턴.pdf, raw/CLAUDE.md-완전가이드-13-스타터템플릿.pdf, raw/CLAUDE.md-완전가이드-14_다음단계.pdf)
