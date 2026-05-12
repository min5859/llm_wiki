---
title: "SSH 개발 환경의 CLI 필수 도구 — tmux/ripgrep/fzf 우선"
domain: both
sensitivity: public
tags: ["ssh", "cli", "tmux", "ripgrep", "fzf", "lazygit", "developer-tools"]
created: 2026-05-13
updated: 2026-05-13
sources:
  - "session-logs/20260512-214455-2046-*.md"
confidence: high
related:
  - "wiki/analyses/terminal-markdown-viewer-tools.md"
---

# SSH 개발 환경의 CLI 필수 도구

Windows PC 에서 리눅스 머신에 SSH 로 접속해 개발하거나 mac 로컬·SSH 양쪽을 오가는 환경에서, AI 가 만든 `.md` 더미 + 코드 파일을 빠르게 다루기 위한 표준 CLI 도구 세트. **3개부터 깔고 익히면 즉시 체감되고 나머지는 천천히 추가**.

## 강력 추천 — 먼저 깔 것 (Top 3)

| 도구 | 용도 | 한 줄 |
|---|---|---|
| **tmux** | 세션 유지 | SSH 끊겨도 작업 그대로. 이 환경엔 필수 |
| **ripgrep (rg)** | 코드/문서 검색 | `grep` 보다 10배 빠름. AI 가 뿌린 .md 더미에서 키워드 찾기 |
| **fzf** | 파일/명령 fuzzy 검색 | `Ctrl+R` 로 명령 히스토리, `Ctrl+T` 로 파일 찾기. 한번 쓰면 못 돌아감 |

## 다음 — 여유 있을 때

| 도구 | 용도 | 한 줄 |
|---|---|---|
| **bat** | `cat` 컬러판 | 코드/마크다운 미리보기 |
| **fd** | `find` 대체 | 문법 단순, 속도 빠름 |
| **lazygit** | git TUI | 마우스/키로 git 다루기 |
| **delta** | git diff 컬러 | diff 가독성 폭상승 |

## 설치 — mac

```bash
brew install tmux ripgrep fzf bat fd lazygit git-delta
$(brew --prefix)/opt/fzf/install   # fzf 키바인딩 활성화
```

## 설치 — 리눅스 (Ubuntu/Debian)

```bash
sudo apt install tmux ripgrep fzf bat fd-find git-delta
# Ubuntu 에선 bat → batcat, fd → fdfind 로 깔리니 alias 권장
echo "alias bat=batcat" >> ~/.bashrc
echo "alias fd=fdfind" >> ~/.bashrc

# lazygit 은 PPA 별도
sudo add-apt-repository ppa:lazygit-team/release
sudo apt update && sudo apt install lazygit
```

mac/리눅스 양쪽 명령이 거의 동일하므로 mac 에서 익힌 워크플로를 SSH 환경에 그대로 옮길 수 있다.

## 표준 워크플로 — AI .md 파일 다루기

```bash
# 1. tmux 세션 시작
tmux new -s work

# 2. 프로젝트 폴더에서
cd ~/project/foo
rg "TODO" --type md          # 모든 md 의 TODO 찾기
rg "mermaid" -l              # mermaid 들어간 파일 목록만
fzf                          # 파일 fuzzy 검색 → Enter
glow -p $(fzf)               # fzf 로 고른 md 를 glow 로 렌더

# 3. tmux 분리 (작업 유지된 채로 SSH 끊어도 됨)
Ctrl+B  d
```

다음 SSH 접속 → `tmux attach -t work` → 작업 그대로 복귀.

## 왜 이 3개가 우선인가

| | 가치 |
|---|---|
| **tmux** | SSH 단절 / 노트북 잠금 / 회사 PC 종료 등 흔한 사고에서 작업을 잃지 않는다. 다른 도구의 효용은 모두 *그 위에서* 누적된다 |
| **rg** | AI 가 만든 `.md` 가 디렉터리에 수십 개 쌓일 때 `grep -r` 으로는 체감 속도가 다르다. 글로브 / 파일타입 필터까지 한 번에 |
| **fzf** | bash/zsh 의 `Ctrl+R` 히스토리 검색을 *대화형 인터랙티브 fuzzy* 로 바꿔준다. tmux pane 안에서 즉시 활용 가능 |

마크다운 뷰어 ([[terminal-markdown-viewer-tools]] 참고: `glow`, `neovim + render-markdown.nvim`) 와 함께 묶이면 **편집·검색·세션·미리보기·git 5가지가 같은 ttys 안**에서 완결된다.

## 변경 이력

- 2026-05-13: 최초 작성 (출처: session-logs/20260512-214455-2046 — SSH 환경에서 마크다운 작업 효율을 묻는 흐름에서 도출)
