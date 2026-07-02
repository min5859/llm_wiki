---
title: "macOS TCC: 터미널이 다른 앱 sandbox 에 접근할 때 토스트 팝업 처리"
domain: "ai-agent"
sensitivity: public
tags: ["macos", "tcc", "iterm", "terminal", "sandbox", "permission", "claude-code"]
created: "2026-05-06"
updated: "2026-05-06"
sources:
  - "session-logs/20260505-235703-d859-https---www.gpters.org-dev-post-kakaotalk-macro-er.md"
confidence: medium
related:
  - "wiki/projects/kakao-db.md"
  - "wiki/analyses/kakaotalk-mac-data-locations.md"
---

# macOS TCC: 터미널이 다른 앱 sandbox 에 접근할 때 토스트 팝업 처리

macOS Sonoma (14)+ 부터 도입된 TCC (Transparency, Consent, Control) 정책은 iTerm/Terminal/Claude Code 같은 앱이 다른 앱 (예: KakaoTalk, Mail) 의 sandbox 데이터에 접근하려 할 때마다 시스템 토스트 팝업을 띄운다 ("iTerm 이 다른 앱의 데이터에 접근하려고 합니다"). 이는 **Claude Code 의 권한 팝업과는 별개의 macOS 시스템 권한**이며, 처리 방식이 다르다.

## 두 종류의 권한 팝업 구분

| | Claude Code 권한 팝업 | macOS TCC 토스트 |
|---|----------------------|------------------|
| 출처 | Claude Code 하네스 | macOS 시스템 |
| 형태 | TUI 인라인 프롬프트 | 화면 상단 토스트 |
| 단위 | Bash 명령의 첫 토큰 (`find`, `xargs`, `mdfind` 등) | 접근 대상 sandbox 경로 |
| 처리 | "Always allow", `/permissions`, `/fewer-permission-prompts` skill | 시스템 설정 → 전체 디스크 접근 |
| 영구 적용 | 프로젝트 `.claude/settings.local.json` | 시스템 권한 (앱 재시작 후 유효) |

증상 예시 (사용자 보고): "iTerm이 다른 앱의 데이터에 접근하려고 합니다. (허용안함) (허용)" 토스트가 새 명령마다 반복.

## 한 번에 끄는 방법 (권장)

**시스템 설정 → 개인정보 보호와 보안 → 전체 디스크 접근 (Full Disk Access)** 에서 사용 중인 터미널 앱을 추가:

1. 시스템 설정 열기
2. `개인정보 보호와 보안` 클릭
3. `전체 디스크 접근` 항목 클릭
4. `+` 버튼 → `/Applications/iTerm.app` (또는 Terminal.app) 추가
5. 토글 **ON**
6. **iTerm 완전 종료 후 재시작** (중요 — 살아있는 프로세스에는 권한이 즉시 적용 안 됨)

> 보안 영향: 해당 터미널이 사용자 라이브러리 전체 (`~/Library/Containers/*` 포함) 를 읽을 수 있게 된다. 작업이 끝나면 같은 화면에서 토글 OFF 해서 회수할 수 있다.

## 권한 부여 없이 우회하는 방법

- (a) **팝업이 뜰 때마다 "허용" 클릭**: 같은 경로는 한 번 허용하면 보통 다시 안 묻는다. 다만 매번 새 자식 프로세스를 띄우는 명령은 자주 다시 묻는다.
- (b) **사용자가 데이터를 작업 위치로 직접 cp**: 사용자가 직접 `cp ~/Library/Containers/com.kakao.KakaoTalkMac/Data/.../<files> ~/Documents/kakao-snapshot/` 한 뒤, 자동화 도구는 그 사본만 읽음. sandbox 침범 자체를 없앰 → 토스트도 안 뜸. 단발 분석에 적합.

## Claude Code 권한 팝업 (별개) 줄이기

같은 세션에서 Claude Code 자체의 Bash 권한 팝업이 자주 뜬다면 별개의 처리:

| | 방법 | 효과 | 주의점 |
|---|---|---|---|
| A | 팝업에서 "Always allow ..." 선택 | 해당 명령 영구 허용 | 패턴 단위로 누적, 정밀 제어 |
| B | `/fewer-permission-prompts` skill 실행 | 트랜스크립트 기반으로 안전한 read-only Bash/MCP 만 골라 프로젝트 `.claude/settings.local.json` 에 일괄 추가 | 가장 빠르고 안전 |
| C | `/permissions` 로 직접 추가 (예: `Bash(find:*)`, `Bash(stat:*)`) | 수동 정밀 제어 | 패턴 일일이 입력 |

## 함정

1. **재시작을 안 하면 권한이 반영 안 됨**: 토글만 ON 하고 iTerm 을 그대로 쓰면 토스트가 계속 뜬다. 반드시 완전 종료 (⌘+Q) 후 재시작.
2. **Claude Code 가 별도 프로세스로 보일 수 있음**: 일부 환경에서는 Claude Code 의 자식 프로세스에 별도로 권한 부여가 필요할 수 있다 (해당 시 Claude Code 도 전체 디스크 접근에 추가).
3. **권한 거부 후에도 일부 명령은 동작**: 권한 거부 시 해당 sandbox 경로만 skip 하고 명령 자체는 exit code 0 으로 끝나는 경우가 많다 (예: `find` 의 부분 결과). "성공한 듯" 보여도 핵심 데이터가 빠질 수 있으니 결과 검증 필요.
4. **TCC 와 Bash 권한이 동시에 뜨면 헷갈림**: 한 번에 두 종류 팝업이 뜰 수 있다 — 토스트는 macOS, TUI 는 Claude Code. 위치와 형태로 구별.

## 적용 시나리오 (예시)

- KakaoTalk 메시지 DB 분석 ([[kakaotalk-mac-data-locations]], [[kakao-db]])
- Mail.app DB / Calendar.app sqlite 분석
- 다른 sandboxed 앱의 로컬 캐시·DB 인스펙션
- 일반적으로 `~/Library/Containers/<bundle-id>/Data/` 에 접근하는 모든 자동화

## 변경 이력

- 2026-05-06: 최초 생성 (session-logs/20260505-235703-d859) — KakaoTalk 분석 중 등장한 TCC 토스트 처리 패턴 일반화
