---
title: "Claude Code 기본 사용법 — Plan 모드·세션 관리·슬래시 커맨드"
domain: "staging"
tags: ["Claude Code", "Plan 모드", "세션 관리", "슬래시 커맨드", "/compact", "/clear", "/context"]
created: "2026-04-15"
updated: "2026-04-15"
sources:
  - "raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf"
  - "raw/Claude-code-슬래시커맨드-중요도분류-실전가이드-김재우-2026.pdf"
  - "raw/claude-code-token-소비절반줄이기.pdf"
confidence: "high"
related:
  - "wiki/claude-code-overview.md"
  - "wiki/claude-code-token-optimization.md"
  - "wiki/claude-code-skills-plugins.md"
---

# Claude Code 기본 사용법 — Plan 모드·세션 관리·슬래시 커맨드

Claude Code의 일상적 사용에서 핵심이 되는 Plan 모드, 세션 관리, 슬래시 커맨드 활용법을 정리한다.

## 핵심 내용

### Plan 모드

`/plan` 커맨드로 진입. 코드를 실제로 변경하지 않고 작업 계획만 수립한다.

- 복잡한 작업 전에 계획을 먼저 확인하고 승인
- 승인 후 컨텍스트가 자동으로 초기화되어 클린 상태에서 구현 시작
- 대형 리팩토링, 신규 기능 추가 시 반드시 활용 권장

### 컨텍스트 관리 기준 (/context)

| 상태 | 컨텍스트 사용률 | 권장 조치 |
|---|---|---|
| 정상 | ~50% | 계속 작업 |
| 모니터링 | 50-79% | 주시 |
| compact 권장 | 80%+ | `/compact` 실행 |
| clear 필수 | 95%+ | `/clear` 실행 |

### /compact vs /clear

| 커맨드 | 동작 | 토큰 회복 | 맥락 보존 |
|---|---|---|---|
| `/compact` | 대화 압축 요약 | 최대 77% | 중요 맥락 유지 |
| `/clear` | 대화 초기화 | 100% | 없음 |

> 실무 원칙: 작업 중간엔 `/compact`, 작업 완료 후 새 작업엔 `/clear`

## 세부 사항

### 슬래시 커맨드 중요도 분류

#### 상급 (필수 숙지)

| 커맨드 | 기능 |
|---|---|
| `/clear` | 대화 전체 초기화, 새 작업 시작 |
| `/compact` | 대화 압축, 최대 77% 토큰 회복 |
| `/context` | 현재 컨텍스트 사용률 확인 |
| `/plan` | 계획 모드 진입 (코드 변경 없음) |
| `/copy` | 마지막 응답 클립보드 복사 |
| `/export` | 대화 내용 파일로 내보내기 |
| `/security-review` | 보안 취약점 검토 |

#### 중급 (상황별 활용)

| 커맨드 | 기능 |
|---|---|
| `/resume` | 이전 세션 재개 |
| `/rewind` | 특정 시점으로 롤백 |
| `/fork` | 현재 세션 분기 (실험용) |
| `/btw` | 사이드 메모 (작업 흐름 방해 없이) |
| `/add-dir` | 추가 디렉토리를 컨텍스트에 포함 |
| `/tasks` | 현재 태스크 목록 확인 |
| `/desktop` | 데스크톱 앱 연동 |
| `/remote-control` | 스마트폰에서 로컬 세션 원격 조작 |

#### 하급 (설정성, 필요할 때만)

`/debug`, `/privacy-settings`, `/sandbox`, `/model`, `/mcp`, `/hooks`, `/config` 등 20+개

### /remote-control

30초 셋업으로 스마트폰에서 로컬 Claude Code 세션을 원격 조작 가능.
- QR 코드로 연결
- 이동 중에도 빌드·배포 모니터링 및 지시 가능

### /sandbox + --dangerously-skip-permissions

```bash
claude --dangerously-skip-permissions
```

- OS 격리 환경(샌드박스)에서 실행
- 승인 다이얼로그 84% 절감
- 자동화 파이프라인 구축 시 활용

### /loop 자동화 파이프라인

```
/sandbox → /loop → --dangerously-skip-permissions
```

- `/loop`: 주기적 반복 실행, 기본 10분 간격
- 세션 종료 시 소멸 (영구 예약은 CronCreate 별도)
- 최대 7일, 50 태스크 제한

## 관련 맥락

- 컨텍스트 누적 → 성능 저하(Context Rot) 문제: 주기적 compact/clear가 해법
- `/rewind`와 `/fork`는 실험적 변경 사항 되돌리기·분기에 유용
- `/export`로 중요 대화 내용을 팀과 공유 가능

## 변경 이력

- 2026-04-15: 최초 생성 (출처: raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf, raw/Claude-code-슬래시커맨드-중요도분류-실전가이드-김재우-2026.pdf, raw/claude-code-token-소비절반줄이기.pdf)
