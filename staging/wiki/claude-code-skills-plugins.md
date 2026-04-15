---
title: "Claude Code 스킬 및 공식 플러그인 완전 가이드"
domain: "staging"
tags: ["Claude Code", "Skills", "Plugin", "번들 스킬", "/loop", "/simplify", "/batch", "feature-dev", "pr-review"]
created: "2026-04-15"
updated: "2026-04-15"
sources:
  - "raw/Anthropic 공식 스킬 및 플러그인 완전 가이드-202604-김재우.pdf"
confidence: "high"
related:
  - "wiki/claude-code-overview.md"
  - "wiki/claude-code-basic-usage.md"
  - "wiki/claude-code-advanced.md"
---

# Claude Code 스킬 및 공식 플러그인 완전 가이드

Claude Code의 스킬 3종 분류와 Anthropic 공식 플러그인 마켓플레이스 전체 구조를 정리한다.

## 핵심 내용

### 스킬 3종류

| 종류 | 설명 | 작성 방법 |
|---|---|---|
| **Bundle Skill** | Claude Code에 내장된 공식 스킬 | 설치 불필요, 즉시 사용 |
| **User-Defined Skill** | 사용자가 직접 작성하는 스킬 | SKILL.md 파일에 작성 |
| **Plugin Skill** | 플러그인으로 설치하는 스킬 | `/plugin install <url>` |

### 번들 스킬 5가지 상세

#### 1. /loop — 주기 실행

- 기본 10분 간격 반복 실행
- 세션 종료 시 소멸 (영구 예약은 CronCreate)
- 최대 7일, 50 태스크 한도
- `/sandbox`와 조합 시 무인 자동화 파이프라인 구축 가능

#### 2. /simplify — 코드 간소화

- 3개 에이전트가 병렬로 코드 리뷰
  - Agent 1: 재사용성 관점
  - Agent 2: 코드 품질 관점
  - Agent 3: 효율성 관점
- 리뷰 완료 후 자동 수정 적용

#### 3. /debug — 디버그 모드

- ON/OFF 토글
- 활성화 시 상세 실행 로그 출력
- 문제 진단 시 활용

#### 4. /batch — 대규모 병렬 변경

5단계 프로세스:
1. 변경 범위 분석
2. 작업 분해 (파일 단위)
3. 병렬 서브에이전트 실행
4. 결과 통합
5. PR 자동 생성

- git 환경 필수
- 대규모 리팩토링, 마이그레이션에 최적

#### 5. /claude-api — API 레퍼런스

- 8개 언어 API 레퍼런스 자동 로드 (Python, JS, TS, Go, Ruby, Java, PHP, C#)
- API 연동 코드 작성 시 최신 문서 참조

## 세부 사항

### 공식 플러그인 마켓플레이스 5개

| 마켓 | 대상 | 주요 내용 |
|---|---|---|
| `anthropics/skills` | 일반 개발자 | 핵심 개발 스킬 모음 |
| `anthropics/claude-code` | Claude Code 특화 | Claude Code 전용 플러그인 |
| `claude-plugins-official` | 공식 파트너 | 검증된 파트너 플러그인 |
| `financial-services-plugins` | 금융 도메인 | 금융 특화 도구 |
| `knowledge-work-plugins` | 지식 업무 | 8개 직종별 플러그인 |

### 개발 라이프사이클 플러그인

#### feature-dev — 기능 개발 7단계

| 단계 | 이름 | 내용 |
|---|---|---|
| 1 | Discovery | 요구사항 탐색 |
| 2 | Exploration | 코드베이스 분석 |
| 3 | Questions | 불명확한 점 질문 |
| 4 | Architecture | 설계 수립 |
| 5 | Implementation | 병렬 에이전트로 구현 |
| 6 | Quality Review | 코드 리뷰 |
| 7 | Summary | 작업 요약 |

#### pr-review-toolkit — 6에이전트 병렬 PR 리뷰

| 에이전트 | 역할 |
|---|---|
| code-reviewer | 코드 품질 전반 |
| silent-failure-hunter | 에러 무시 패턴 탐지 |
| code-simplifier | 불필요한 복잡도 제거 |
| comment-analyzer | 코드 주석 품질 |
| pr-test-analyzer | 테스트 커버리지 |
| type-design-analyzer | 타입 설계 검토 |

#### commit-commands — 커밋 자동화 3개

- `/commit`: 변경사항 분석 + 커밋 메시지 자동 생성
- `/commit-push-pr`: 커밋 + 푸시 + PR 생성 한 번에
- `/clean_gone`: 리모트에서 삭제된 브랜치 일괄 정리

#### doc-coauthoring — 문서 공동 집필 3단계

1. Context Gathering: 배경 정보 수집
2. Refinement: 초안 작성 및 개선
3. Reader Testing: 독자 관점 검토

### 가드레일 플러그인

| 플러그인 | 기능 |
|---|---|
| `hookify` | 행동 규칙 자동화 (자연어 → Hook 변환) |
| `security-guidance` | 파일 편집 시 9가지 OWASP 취약점 자동 검출 |
| `claude-md-management` | CLAUDE.md 품질 6항목 스코어링 + `/revise-claude-md` |

### 자동화 플러그인

| 플러그인 | 기능 |
|---|---|
| `ralph-loop` | 무한 반복 실행 루프 |
| `plugin-dev` | 커스텀 플러그인 8페이즈 제작 가이드 |
| `mcp-server-dev` | MCP 서버 5페이즈 구축 가이드 |

### knowledge-work-plugins 8종

`productivity`, `sales`, `customer-support`, `product-management`, `marketing`, `legal`, `finance`, `data`

각 직종의 반복 업무를 자동화하는 스킬 모음.

### Document Skills (문서 처리)

- `pptx`: PowerPoint 파일 생성/수정
- `xlsx`: Excel 파일 처리
- `pdf`: PDF 읽기/분석
- `docx`: Word 문서 처리

## 관련 맥락

- 플러그인 설치: `/plugin install <github-url>`
- User-Defined Skill은 팀 내 반복 작업을 템플릿화하는 데 가장 실용적
- security-guidance 플러그인은 개발 중 OWASP 취약점 자동 탐지 — 보안 교육 효과도 있음

## 변경 이력

- 2026-04-15: 최초 생성 (출처: raw/Anthropic 공식 스킬 및 플러그인 완전 가이드-202604-김재우.pdf)
