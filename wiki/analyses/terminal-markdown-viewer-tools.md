---
title: "터미널·CLI 마크다운 뷰어 도구 비교"
domain: both
sensitivity: public
tags: ["markdown", "cli", "terminal", "neovim", "glow", "mermaid", "ssh"]
created: 2026-05-13
updated: 2026-05-13
sources:
  - "session-logs/20260512-214455-2046-*.md"
confidence: high
related:
  - "wiki/patterns/ssh-cli-toolkit-essentials.md"
---

# 터미널·CLI 마크다운 뷰어 도구 비교

AI 가 만드는 `.md` 파일이 늘어나는 환경 (Claude Code / Cursor 등) 에서 SSH 터미널 안에서 마크다운을 보기 좋게 렌더해 주는 도구들의 비교. SSH 환경에 데스크탑 GUI 앱 (Tauri / Electron) 을 띄우는 접근은 작업 흐름이 어긋나므로, **터미널 안에서 도는 도구를 선택하는 것이 정답**.

## 도구 비교표

| 도구 | 언어 | 특징 | mermaid | 비고 |
|---|---|---|---|---|
| **glow** | Go | `glow file.md` ANSI 컬러 렌더, `glow -p` 페이저 모드. CLI 마크다운 뷰어 표준 | ❌ 코드블록 그대로 | 가장 인기. 디렉터리 모드 (`glow`) 로 목록 선택 가능 |
| **mdcat** | Rust | `cat` 의 마크다운판. Kitty/WezTerm 에서 인라인 이미지 | ❌ | 이미지 프로토콜 지원 터미널에서만 차별점 |
| **frogmouth** | Python/Textual | 마우스/링크 클릭 가능한 TUI 브라우저 | ❌ | 인터랙티브 탐색이 필요하면 |
| **bat** | Rust | 신택스 하이라이트만 (구조 렌더 X) | ❌ | `cat` 대체. 마크다운 *구조* 렌더는 아님 |
| **neovim + render-markdown.nvim** | Lua | 편집 + concealed 렌더, 헤더/리스트/체크박스 시각화 | ❌ (별도 플러그인 필요) | 편집까지 통합. nvim-treesitter 의존 |
| **neovim + markdown-preview.nvim** | Vim plug | 브라우저 탭 띄워 mermaid 까지 진짜 렌더 | ✅ (브라우저) | SSH 안에선 브라우저 띄우기 까다로워 비추 |

## Mermaid 의 본질적 한계 — 터미널에서는 SVG 렌더 불가

위 모든 터미널 도구가 mermaid 다이어그램은 **텍스트(코드블록) 로만 보여준다**. 이유:

- 표준 ANSI 터미널에는 SVG 를 그리는 표준 방법이 없다
- 인라인 이미지 프로토콜 (sixel / iTerm2 / Kitty / WezTerm) 은 터미널 의존이 크고, Windows Terminal 의 SSH 세션에서는 부분 지원
- mermaid 라이브 프리뷰가 결정적으로 필요한 경우 사실상 **GUI/webview** (Tauri / Electron / 브라우저 분리 뷰어) 가 강제됨

### 단계적 우회

| 단계 | 방법 | 효용 |
|---|---|---|
| 1 | 코드블록 그대로 노출 | 가장 단순. AI 가 만든 mermaid 는 짧으므로 텍스트도 읽힘 |
| 2 | `mermaid-ascii` 같은 외부 툴을 subprocess 로 ASCII art 변환 | flowchart 정도만 잘 됨 |
| 3 | `<leader>m` 키로 mermaid 블록을 임시 HTML 빌드 → `xdg-open` | SSH X-forwarding / scp 우회 필요 |
| 4 | 이미지 프로토콜로 PNG 렌더 (WezTerm/Kitty) | 환경 의존이 큼 |

대부분의 경우 **1~2 단계가 노력 대비 효용이 가장 크다.** AI 출력의 절대 다수는 헤더/리스트/코드/표라, mermaid 시각화가 빠져도 작업 효율 손해가 크지 않다.

## 현실적 추천 (mac/linux 양쪽 동일)

| 용도 | 추천 |
|---|---|
| **편집 중 보기** | `neovim` + `render-markdown.nvim` (2단계 설정) |
| **빠르게 훑어보기** | `glow -p 파일.md` |
| **mermaid 까지 렌더** | `markdown-preview.nvim` 또는 별도 브라우저 |

핵심 흐름: **편집은 nvim, 미리보기 토글은 nvim 안에서 `:!glow -p %`** — `%` 는 현재 파일.

### neovim 마크다운 렌더 3단계

1. **플러그인 없이** — `vim.opt.conceallevel = 2` + `vim.opt.wrap = true` 두 줄만으로 헤더/리스트가 시각적으로 깔끔해짐
2. **`render-markdown.nvim`** — `lazy.nvim` 매니저로 설치. 편집하면서 헤더/리스트/체크박스/표가 진짜 렌더된 것처럼 보임. `:RenderMarkdown toggle` 로 on/off
3. **`markdown-preview.nvim`** — 브라우저 탭으로 mermaid 까지 렌더. mac 로컬에선 좋지만 SSH 리눅스 머신에선 비추

mac/linux 사이 명령이 완전히 동일하므로, mac 에서 익혀 둔 워크플로를 SSH 환경에 그대로 옮길 수 있다.

## Tauri/Electron 데스크탑 앱과의 대비

직접 만든 Tauri+React 기반 "vim 키바인딩 마크다운 에디터" 같은 결과물은 **이름과 다크 테마 때문에 "터미널 앱"으로 착각하기 쉽지만 실은 webview 기반 GUI 앱**이다. SSH 환경에는 다음 이유로 부적합:

- Tauri 는 macOS 네이티브 윈도우 + 그 안의 webview → ttys 위에서 도는 TUI 가 아님
- `mermaid` 가 SVG 를 그려야 하므로 DOM 의존
- SSH 안에서 띄울 수 없고 Windows 클라이언트 쪽에 별도 GUI 윈도우로 떠버려 작업 흐름이 어긋남
- 빌드 산출물 무거움 (Rust target 3GB+, node_modules 300MB+) — 프로젝트당 GB 단위

진짜 터미널용 단일 바이너리가 필요하면 **Rust + ratatui + pulldown-cmark + syntect + crossterm** 정도의 스택이 합목적적이지만, 이 영역은 위 도구들이 이미 성숙해서 **새로 만들기보다 기존 도구 검토가 정공법**.

## 변경 이력

- 2026-05-13: 최초 작성 (출처: session-logs/20260512-214455-2046 — "vim 인터페이스 터미널형 마크다운 에디터" 만들던 도중 SSH 환경에 GUI 가 안 맞다는 사실 발견 → 기존 도구 비교)
