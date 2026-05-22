---
title: "macOS 디스크 정리 — 캐시 카테고리 분류 + Claude Desktop 의 8.4G vm_bundles 정체"
domain: both
sensitivity: public
tags: ["analysis", "macos", "disk-cleanup", "cache", "claude-desktop", "homebrew", "playwright"]
created: "2026-05-14"
updated: "2026-05-22"
source_session: "20260514-215345-f0e2-제가사용하는-PC-의-SDD-disk-size가-256GB-로-작은-사이즈-입니다.-현재.md"
sources:
  - "session-logs/20260514-215345-f0e2-제가사용하는-PC-의-SDD-disk-size가-256GB-로-작은-사이즈-입니다.-현재.md"
  - "session-logs/20260514-220947-2eee-todo.md-읽고-이어서.md"
  - "session-logs/20260522-234234-bc6e-disk-monitoring-내용을-분석해-주세요,.md"
confidence: high
related:
  - "wiki/projects/disk-monitor.md"
  - "wiki/analyses/anthropic-oauth-third-party-billing-trap.md"
  - "wiki/patterns/disk-monitor-blind-spot-coverage.md"
---

# macOS 디스크 정리 — 캐시 카테고리 분류 + Claude Desktop 의 vm_bundles 정체

256GB SSD Mac 의 free 공간 회수 작업 중 도출한 일반화 가능한 카테고리 분류. "캐시"라고 다 같은 캐시가 아니다 — **삭제 후 어떻게 되는지** 가 카테고리 결정 기준.

## 캐시 3 분류

| 카테고리 | 정의 | 삭제 후 결과 | 회수 가치 |
|---|---|---|---|
| **자동 재생성 캐시** | 앱이 켜질 때 점진적으로 다시 채워지는 순수 캐시 | 다음 앱 사용 시 점진 재생성 (보통 N MB 단위) | 부담 적음 |
| **순수 회수 (재다운로드 없음)** | 업데이트 잔여물·구버전 패키지 등 | 다시 안 만들어짐 (새 업데이트가 떴을 때만 임시) | 가장 안전한 회수 |
| **다음 사용 시 재다운로드** | 캐시 폴더에 있지만 실제로는 큰 바이너리·DB | 다음 사용 시 수백 MB ~ GB 재다운로드 | "도구 자주 쓰는지" 판단 필요 |

판단 기준은 **삭제 직후 그 도구를 쓸 일이 가까운 시일에 있는지** + **재다운로드 비용** 이다.

## 카테고리별 macOS 사례

### 자동 재생성 캐시 (안전)

| 항목 | 정체 | 비고 |
|---|---|---|
| `~/Library/Application Support/Claude/Cache/Cache_Data` | Electron HTTP/asset 캐시 | 앱 실행 중 삭제해도 안전 (inode 기반) |
| `~/Library/Application Support/Claude/Code Cache/js` | V8 JS code cache | 자동 재생성 |
| `~/Library/Caches/Homebrew` | Homebrew 다운로드 캐시 (`.tar.gz`) | `brew cleanup` 으로 정리 |

### 순수 회수 (가장 안전)

| 항목 | 정체 | 비고 |
|---|---|---|
| `~/Library/Caches/com.anthropic.claudefordesktop.ShipIt` | Claude Desktop Sparkle 의 구버전 업데이트 패키지 | 새 업데이트 시에만 임시로 다시 생김 |

다른 Sparkle 사용 앱들도 비슷한 `*.ShipIt` 디렉터리를 가짐 (Sparkle = macOS 자동 업데이트 표준 프레임워크).

### 다음 사용 시 재다운로드 필요

| 항목 | 정체 | 재다운로드량 |
|---|---|---|
| `~/Library/Caches/ms-playwright` | Chromium / Firefox / WebKit 본체 | ~수백 MB (브라우저 별) |
| `~/Library/Caches/camoufox` | Camoufox (Firefox 기반 stealth 브라우저) | ~수백 MB |
| `~/Library/Caches/com.openai.codex` | Codex CLI 데이터 + 캐시 (혼재) | 일부 재다운로드 가능 |

이 카테고리는 "캐시 폴더" 라는 이름에 속아서 함부로 지우면 다음 사용 시 무거운 재다운로드. **자주 쓰는 도구는 보존**.

## Claude Desktop 의 디스크 footprint (9.8G 분해)

`~/Library/Application Support/Claude` depth 1 만 보면 9.8G — 안에 들어가서 분해해 보면 정체가 다름.

| 크기 | 항목 | 정체 / 안전도 |
|---|---|---|
| **8.4G** | `vm_bundles/claudevm.bundle` | Claude Desktop **Cowork** 기능용 Linux VM 이미지 (Apple Virtualization Framework + Ubuntu 22.04 + 그 안에 Claude Code CLI + bubblewrap 샌드박스). VM 안 쓰면 잉여. 삭제 시 Cowork 다음 사용 때 재다운로드 |
| **1.2G** | `Cache/Cache_Data` | Electron HTTP/asset 캐시. 비워도 안전 |
| 243M | `claude-code-vm/2.1.128` | VM 부속 데이터 |
| 212M | `claude-code/2.1.128` | Claude Code CLI 본체 데이터 (설정/history). **건드리지 말 것** |
| 157M | `local-agent-mode-sessions/` | 과거 에이전트 세션 기록 (작업 history). 신중 |
| 77M | `Code Cache/js` | V8 JS code cache. 비워도 안전 |

### vm_bundles 의 의사결정 트리

- **Cowork 써본 적 없음 / 앞으로도 안 쓸 예정** → 8.4G 즉시 회수
- **종종 사용** → 보존 (재다운로드 ~10G)
- **모르겠음** → 일단 보존 + Cowork 명시적 비활성 옵션 검토

알려진 버그 ([anthropics/claude-code#47039](https://github.com/anthropics/claude-code/issues/47039)): Cowork 미사용에도 vm_bundles 가 자동 생성되는 케이스 보고됨. Cowork 안 쓰는데 8.4G 가 잡혀 있으면 삭제 후 재발생 여부 모니터링.

## 조사·정리 워크플로우

### 1. depth 1 큰 후보 식별

```bash
# 표준 macOS 모니터링 대상 디렉터리들의 disk usage 보기
du -d 1 -k ~/Library/Caches \
            ~/Library/Application\ Support \
            ~/Library/Containers \
            /Applications 2>/dev/null \
  | sort -rn | head -25
```

`disk_monitor` 프로젝트 ([[disk-monitor]]) 가 18 경로를 매일 자동으로 체크.

### 2. 큰 항목은 depth 2 로 분해

depth 1 만 보고 결론 내면 안 됨. 예: Claude 9.8G 가 사실은 vm_bundles 8.4G + Cache_Data 1.2G + ... 의 합. 카테고리가 다른 항목들이 섞여 있음.

```bash
du -d 2 -k "$HOME/Library/Application Support/Claude" 2>/dev/null \
  | sort -rn | head -25
```

### 3. 카테고리 분류 후 사용자 컨펌

자동 분류는 위험 (`claude-code/2.1.128` 같은 본체 데이터를 패턴 매칭으로 잡으면 끝). 위 3 카테고리 표를 사용자에게 보여주고 항목 단위로 컨펌.

### 4. 삭제 + 검증

```bash
echo "=== BEFORE ===" && df -k / | awk 'NR==2{printf "free: %.2f G\n", $4/1024/1024}'
rm -rf <target>
brew cleanup -s 2>&1 | tail -25  # Homebrew 의 경우
echo "=== AFTER ===" && df -k / | awk 'NR==2{printf "free: %.2f G\n", $4/1024/1024}'
```

`brew cleanup -s` 의 `Skipping <pkg>: most recent version not installed` 경고는 *현재 설치 버전이 최신이 아니라서* 보수적으로 건너뛴 것 — `brew upgrade` 후 다시 cleanup 하면 추가 회수 가능 (별개 작업).

## 첫 운영 케이스 결과 (2026-05-14)

| 항목 | 회수 |
|---|---|
| Claude `Cache/Cache_Data` | 1.1G |
| Claude `Code Cache/js` | 76M |
| `com.anthropic.claudefordesktop.ShipIt` | 683M |
| `~/Library/Caches/ms-playwright` | 528M |
| `~/Library/Caches/camoufox` | 670M |
| `brew cleanup -s` | 181.7M |
| **합** | **~3.23G** (114.31 → 117.54 G) |

**보존 결정**: vm_bundles (Cowork 종종 사용), `claude-code/2.1.128`, `local-agent-mode-sessions/`, `~/Library/Caches/com.openai.codex`

## HOME 아래 도구 캐시 (`~/Library` 밖)

`~/Library/Caches` 만 보면 놓치는 큰 캐시 — 패키지 매니저 / 도구체인은 dotfile 디렉터리 (`~/.<도구>`) 를 쓴다.

| 항목 | 정체 | 정리 명령 | 카테고리 |
|---|---|---|---|
| `~/.npm` | npm `_cacache` | `npm cache clean --force` | 자동 재생성 (다음 install 시) |
| `~/.cache/uv` | uv wheel/source 캐시 | `uv cache clean` | 자동 재생성 |
| `~/.cache/puppeteer` | Puppeteer 가 받은 Chrome | 수동 삭제 | 다음 사용 시 재다운로드 |
| `~/.cache/huggingface` | HF Hub 모델 | 수동 삭제 | 다음 사용 시 재다운로드 |
| `~/.cache/chroma` | Chroma 벡터 DB | **건드리지 말 것** | 실데이터일 가능성 |
| `~/.cargo` | Rust registry 캐시 + 바이너리 | `cargo cache --autoclean` | 자동 재생성 |
| `~/.rustup` | Rust toolchain 본체 | `rustup toolchain remove` 선별 | 다음 빌드 시 재다운로드 |
| `~/.docker`, `~/.colima` | 컨테이너 디스크 이미지 | 도구별 prune | 데이터 + 캐시 혼재 |

`disk_monitor` 의 기본 추적 경로에 `~/.npm` `~/.cache` `~/.nvm` `~/.cargo` `~/.rustup` 가 2026-05-22 보강 추가됨. 사각지대 진단 패턴은 [[disk-monitor-blind-spot-coverage]].

### uv cache 의 stale `.lock`

`uv cache clean` 이 `.lock` 때문에 실패할 때:

```bash
ps -eo pid,etime,command | grep [u]v   # 활성 프로세스 확인
ls -la ~/.cache/uv/.lock                # mtime 확인
```

활성 uv 가 없고 lock 이 수 개월 전이면 `uv cache clean --force` 안전. 비정상 종료된 흔적.

## 관련 맥락

- 디스크 모니터링 도구의 구체적 구현은 [[disk-monitor]]
- 사각지대 진단 + `du` timeout fallback 은 [[disk-monitor-blind-spot-coverage]]
- macOS Sparkle 자동 업데이트의 동작 방식 (ShipIt 캐시의 출처) 은 본 항목 외에는 정리된 페이지 없음 — 필요 시 신규 작성 후보

## 변경 이력

- 2026-05-14: 최초 생성. disk_monitor 첫 운영 시 캐시 정리 + Claude Desktop vm_bundles 분석 기반 (출처: session-logs/20260514-215345, 20260514-220947)
- 2026-05-22: HOME 아래 도구 캐시 (`~/.npm` `~/.cache/uv` 등) 카테고리 보강, uv `.lock` stale 처리 메모 추가 (출처: session-logs/20260522-234234-bc6e)
