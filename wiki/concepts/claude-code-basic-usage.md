---
title: "Claude Code 기본 사용법 — Plan 모드·세션 관리·슬래시 커맨드"
domain: "personal"
sensitivity: "public"
tags: ["Claude Code", "Plan 모드", "세션 관리", "슬래시 커맨드", "/compact", "/clear", "/context"]
created: "2026-04-15"
updated: "2026-04-16"
sources:
  - "raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf"
  - "raw/Claude-code-슬래시커맨드-중요도분류-실전가이드-김재우-2026.pdf"
  - "raw/claude-code-token-소비절반줄이기.pdf"
  - "raw/음성 260413_AA-1-3_original.txt"
  - "raw/음성 260413_AA-1-4_original.txt"
  - "raw/음성 260414_AA-2-5_original.txt"
  - "raw/Tips/Claude Code loop 완전 정복-매일 아침 자동으로 기술·금융 정보를 수집하는 방법.pdf"
confidence: "high"
related:
  - "wiki/claude-code-overview.md"
  - "wiki/claude-code-token-optimization.md"
  - "wiki/claude-code-skills-plugins.md"
  - "wiki/claude-code-loop-automation.md"
---

# Claude Code 기본 사용법 — Plan 모드·세션 관리·슬래시 커맨드

Claude Code의 일상적 사용에서 핵심이 되는 Plan 모드, 세션 관리, 슬래시 커맨드 활용법을 정리한다.

## 핵심 내용

### Plan 모드

`/plan` 커맨드로 진입. 코드를 실제로 변경하지 않고 작업 계획만 수립한다.

- 복잡한 작업 전에 계획을 먼저 확인하고 승인
- 승인 후 컨텍스트가 자동으로 초기화되어 클린 상태에서 구현 시작
- 대형 리팩토링, 신규 기능 추가 시 반드시 활용 권장
- 기존 코드 분석에도 유용: 특정 디렉터리나 기능을 지정하고 "어떤 방식으로 구현돼 있는지 먼저 분석해줘"라고 지시

실습에서는 Plan 모드를 "시작점"으로 사용했다. 먼저 계획을 검토하고, 충분히 납득한 뒤 "실행해줘"라고 넘기면 구현 단계로 전환한다. 처음부터 구현을 맡기는 것보다 방향 수정 비용이 작다.

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

`/loop`는 매일 아침 기술·금융 정보 수집처럼 반복 리포트가 필요한 작업에 적합하다. 단, 세션 기반 반복이므로 영구 스케줄러가 아니며, 출력 리포트 위치·수집 소스·요약 형식을 `CLAUDE.md`나 별도 prompt 파일로 고정해야 재현성이 높아진다.

### 오류 전달과 디버깅

실습에서 권장한 기본 흐름:

1. 터미널 오류는 그대로 붙여 넣고 "이 오류를 분석해서 수정해줘"라고 요청
2. 화면 문제는 캡처 이미지를 붙여 넣고 증상을 설명
3. 테스트 작성 기준은 `CLAUDE.md`에 명시
4. 한 작업이 끝나기 전 다른 리팩터링이나 문서 작업을 섞지 않기

작업 중간에 "이참에 리팩터링도 해줘"처럼 주제를 섞으면 컨텍스트가 오염된다. 버그 수정, 문서 업데이트, 테스트 추가는 가능한 한 별도 세션으로 나누는 편이 안정적이다.

## 관련 맥락

- 컨텍스트 누적 → 성능 저하(Context Rot) 문제: 주기적 compact/clear가 해법
- `/rewind`와 `/fork`는 실험적 변경 사항 되돌리기·분기에 유용
- `/export`로 중요 대화 내용을 팀과 공유 가능

## 변경 이력

- 2026-04-16: 녹취 기반 Plan 모드 운용, /loop 활용, 오류 전달 및 세션 분리 원칙 보강
- 2026-04-15: 최초 생성 (출처: raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf, raw/Claude-code-슬래시커맨드-중요도분류-실전가이드-김재우-2026.pdf, raw/claude-code-token-소비절반줄이기.pdf)
