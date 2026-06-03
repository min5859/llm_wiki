---
title: "Zed — Rust 기반 고속 코드 에디터 (AI · 원격 개발 내장)"
domain: both
sensitivity: public
tags: [analysis, editor, rust, ai, coding-assistant, ssh, remote-development, vscode-alternative]
created: 2026-06-03
updated: 2026-06-03
sources:
  - "conversation: 2026-06-03 Zed 설치/사용/원격/AI 문의"
confidence: medium
related:
  - "wiki/analyses/continue-open-source-ai-code-assistant.md"
  - "wiki/summaries/mac-keyboard-shortcuts-for-windows-users.md"
  - "wiki/analyses/terminal-markdown-viewer-tools.md"
---

# Zed — Rust 기반 고속 코드 에디터

Zed는 Rust로 작성된 고성능 코드 에디터로, GPU 가속 렌더링으로 빠른 반응성을 강점으로 한다. macOS 전용으로 출발해 Linux, Windows 순으로 플랫폼을 확장했다. AI provider 연결과 SSH 원격 개발이 기본 내장되어 있어 VS Code 대안으로 주목된다.

## 설치

| 플랫폼 | 명령 |
|---|---|
| macOS | `brew install --cask zed` 또는 [zed.dev](https://zed.dev) 다운로드 |
| Windows | `winget install Zed.Zed` 또는 [zed.dev/download](https://zed.dev/download) |
| Linux | `curl -f https://zed.dev/install.sh \| sh` |

- 설치 후 터미널에서 `zed .` 로 현재 폴더를 열 수 있다 (CLI 자동 등록).
- Windows는 가장 늦게 추가된 플랫폼이라 최신 기능 반영·안정성에서 macOS/Linux보다 뒤처질 수 있다.

## 핵심 사용법

- **명령 팔레트**: `Cmd/Ctrl + Shift + P` — 거의 모든 기능 진입점
- **파일 열기**: `Cmd/Ctrl + P` (파일명), 전체 검색 `Cmd/Ctrl + Shift + F`
- **멀티커서**: `Cmd/Ctrl + D`(다음 동일 단어), `Cmd/Ctrl + Click`
- **내장 터미널**: `Ctrl + ~`
- **설정**: `Cmd/Ctrl + ,` → `settings.json` 직접 편집 (VS Code 유사 방식)
- **협업(Collab)**: 실시간 공동 편집 기본 내장

VS Code 사용자는 키맵·설정 개념이 비슷해 적응이 빠르다. 다만 확장 생태계는 VS Code보다 작고, 대신 기본 속도가 빠르다.

## 원격 개발 (SSH)

VS Code Remote-SSH와 유사하게 SSH로 원격 서버에 접속해 로컬처럼 편집한다.

- 동작: Zed 클라이언트 → SSH 접속 → 원격(Linux)에 Zed 원격 서버 컴포넌트 자동 설치
- 내장 터미널이 **원격 서버의 shell**로 연결됨
- 진입: 명령 팔레트 → `projects: open remote`, 또는 시작 화면 "Open a remote project"
- `~/.ssh/config`의 호스트 설정을 그대로 인식

> 원격 개발은 클라이언트 플랫폼과 무관하게 설계되어, Windows 클라이언트에서 Linux 서버로 접속 가능. 단 Windows는 OpenSSH 클라이언트 설치 여부·`ssh` CLI 정상 동작을 먼저 확인할 것. (이 항목은 공식 문서 미검증, confidence medium)

## AI provider 연결

AI 기능이 핵심 축으로, 다양한 provider를 직접 연결할 수 있다.

| 분류 | provider |
|---|---|
| 클라우드 API | Anthropic (Claude), OpenAI (GPT), Google (Gemini) |
| 로컬 모델 | Ollama, LM Studio |
| 커스텀 | OpenAI 호환 엔드포인트 (사내 게이트웨이 등) |
| 자동완성 | GitHub Copilot |
| 호스팅형 | Zed AI (구독, 별도 키 불필요) |

- 연결: AI 패널(`Cmd/Ctrl + ?`) 또는 설정 → provider 선택 → API 키 입력. `settings.json`의 `language_models` 항목에 직접 기재도 가능.
- 활용: **Assistant 패널**(채팅), **Inline Assist**(`Cmd/Ctrl + Enter`, 선택 영역 자연어 편집), **Agent 모드**(파일 직접 읽기·수정), **Edit Prediction**(다음 편집 예측 자동완성)
- API 키 방식은 본인 계정으로 사용량이 청구됨.

## 관련 맥락

- AI 코드 어시스턴트 대안 비교는 `wiki/analyses/continue-open-source-ai-code-assistant.md` 참고.
- Windows↔macOS 단축키 매핑은 `wiki/summaries/mac-keyboard-shortcuts-for-windows-users.md` 참고.

## 변경 이력

- 2026-06-03: 최초 생성 (출처: 2026-06-03 대화 — Zed 설치/사용/원격 SSH/AI provider 문의). 설치·사용법·SSH 원격 개발·AI provider 연결 정리. Windows 지원 및 SSH 세부는 공식 문서 미검증으로 confidence medium.
