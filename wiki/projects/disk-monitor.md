---
title: "disk_monitor — 일일 디스크 사용량 모니터링 (macOS)"
domain: personal
sensitivity: public
tags: ["project", "disk-monitor", "macos", "launchd", "python", "cli"]
created: "2026-05-14"
updated: "2026-05-22"
sources:
  - "session-logs/20260514-215345-f0e2-제가사용하는-PC-의-SDD-disk-size가-256GB-로-작은-사이즈-입니다.-현재.md"
  - "session-logs/20260514-220947-2eee-todo.md-읽고-이어서.md"
  - "session-logs/20260522-234234-bc6e-disk-monitoring-내용을-분석해-주세요,.md"
confidence: high
related:
  - "wiki/patterns/launchd-plist-symlink-from-project.md"
  - "wiki/analyses/macos-disk-cleanup-cache-classification.md"
  - "wiki/patterns/launchd-secret-management.md"
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
  - "wiki/patterns/disk-monitor-blind-spot-coverage.md"
---

# disk_monitor — 일일 디스크 사용량 모니터링

256GB SSD Mac 의 디스크 free 공간이 매일 줄어드는 원인을 추적하기 위한 단일 파일 Python CLI. 매일 09:00 launchd 가 자동 스냅샷 → 어제 대비 어디가 늘었는지 diff. **스크립트는 절대 파일을 지우지 않는다** — 정리는 사용자 컨펌 후 Claude 가 명령 실행.

프로젝트 디렉터리: `disk_monitor` (초기 `disk_size` 에서 rename).

## 구성

```
disk_monitor.py                # 단일 파일 (~250 줄). scan/report/top/install/uninstall
com.user.disk-monitor.plist    # launchd 마스터 (프로젝트 안)
~/Library/LaunchAgents/...     # 위 파일의 symlink (Homebrew 스타일)
README.md                      # 사용법 + 설계 노트
CLAUDE.md                      # 데이터 위치 + 정리 워크플로우 약속
tasks/todo.md                  # 컨텍스트 + 진행 상태
.gitignore                     # com.user.disk-monitor.plist (머신 절대 경로 박힘)
```

데이터 (스냅샷):

```
~/Library/Application Support/disk-monitor/
├── snapshots/YYYY-MM-DD.json    # 일자별 스냅샷 (하루 = 한 파일, 같은 날 재실행 시 덮어씀)
├── scan.log                     # launchd stdout
└── scan.err                     # launchd stderr
```

## 핵심 설계 판단

### 1. 코드와 데이터 분리 (`~/Library/Application Support/`)

스냅샷 JSON 을 프로젝트 폴더가 아닌 macOS 표준 위치에 둔다.

이유:
- macOS 관례 — 앱/스크립트 런타임 데이터는 `~/Library/Application Support/<이름>/`
- git 오염 방지 — 프로젝트는 git 저장소. 매일 생기는 JSON 을 `.gitignore` 로 거르는 것보다 다른 위치에 두면 실수로 commit 될 일이 없음
- launchd 친화적 — launchd CWD 가 프로젝트 폴더가 아닐 수 있음. 절대 경로로 고정해 두면 어디서 실행돼도 같은 위치
- 프로젝트 이동/삭제와 무관 — 폴더 rename (예: `disk_size` → `disk_monitor`) 해도 데이터는 영향 없음

단점: 데이터 위치가 한눈에 안 보임 → CLAUDE.md 에 명시적으로 박아둠.

### 2. plist 마스터를 프로젝트 폴더에 두고 LaunchAgents 는 symlink

Homebrew services 와 같은 패턴. 자세한 설계 근거는 [[launchd-plist-symlink-from-project]].

`install` 서브커맨드:
1. `./com.user.disk-monitor.plist` 에 plist 작성 (프로젝트 폴더 안)
2. `~/Library/LaunchAgents/com.user.disk-monitor.plist` 를 위 파일로 symlink
3. `launchctl load <symlink>`

`.gitignore` 에 plist 추가 — 머신별 절대 경로 (`/Users/wooki/...`) 가 박혀 있어 다른 머신에서 그대로 못 씀.

### 3. 자동 정리 기능 추가 금지 — 사용자 컨펌 워크플로우

스크립트에는 `clean` / `delete` 류 destructive 서브커맨드를 추가하지 않는다.

워크플로우:
1. `report` / `top` 으로 후보 파악
2. 사용자가 보고 컨펌 ("○○ 만 빼고 다 지워")
3. Claude 가 그때 실제 명령 실행 (`rm -rf` 등)

이유: 자동 분류는 위험 — 예를 들어 `~/Library/Application Support/Claude/claude-code/` 안에는 설정/history 가 있는데 패턴 매칭으로 잘못 잡으면 끝. CLAUDE.md 에 안전도 휴리스틱 (safe / caution / danger 패턴) 명시.

### 4. 하루 한 스냅샷 (덮어쓰기)

`scan` 의 파일명은 `YYYY-MM-DD.json` (시간 정보 없음). 같은 날 여러 번 scan 하면 **이전 측정값은 사라진다**.

의도:
- 하루 = 한 데이터 포인트 (자정 직후 launchd 가 새 파일 생성)
- 정리 직후 scan 하면 그 결과가 다음날 baseline 이 되어 `cleanup 효과` 가 노이즈로 잡히지 않음
- 디스크 공간을 모니터링하는 도구가 자체 데이터로 디스크를 잡아먹지 않도록

## 알려진 제약 / 다음 액션

- `report` 는 stdout 만 — 파일 저장 안 함. 매일 자동으로 diff 결과를 어딘가 남기고 싶다면 `scan` 후 `report` 결과를 markdown 으로 append 하는 방식으로 확장 필요
- 첫 운영 시점 baseline 정리 완료 (2026-05-14, free 117.5G). 다음 의미 있는 시점: 2026-05-15 09:00 launchd scan 후 `report` 가 첫 일일 diff 출력
- 모니터링 대상 18 경로는 `~/Library/Application Support/disk-monitor/config.json` 의 `paths` 배열로 변경 가능

## 사용법

```bash
python3 disk_monitor.py scan         # 스냅샷 (매일, launchd 가 자동)
python3 disk_monitor.py report       # 어제 vs 오늘 증가량
python3 disk_monitor.py top -n 30    # 최신 스냅샷의 큰 디렉터리
python3 disk_monitor.py install      # launchd 등록 (매일 09:00)
python3 disk_monitor.py install --hour 22 --minute 30   # 시간 변경
python3 disk_monitor.py uninstall    # 해제 (symlink + 마스터 plist 양쪽 정리)
```

## 첫 운영 발견 (2026-05-14)

- **vm_bundles 8.4G** = Claude Desktop 의 Cowork 기능용 Linux VM 이미지. 본인 종종 사용 → 보존 결정 (memory 에 박음)
- **회수 합계 ~3.23G** (114.31 → 117.54 G)

상세한 macOS 캐시 카테고리 분류와 Claude Desktop 디스크 분포는 [[macos-disk-cleanup-cache-classification]].

## 두 번째 운영 회고 (2026-05-22)

1주일 운영 후 처음 의미 있는 free 감소 (-18.4G, 123.6 → 105.2 GB) 가 나왔는데, **모니터링 경로 내 변화 합은 -1.4G**. **약 17G 가 사각지대에서 증가**한 셈이다. 패턴과 대응은 [[disk-monitor-blind-spot-coverage]] 로 일반화. 이 세션에서 코드/config 가 다음과 같이 보강됨.

### 추적 경로 보강 (18 → 23)

config 와 스크립트 기본값 양쪽에 추가:

```
~/.npm        # npm 캐시 (이번 케이스 7.2G → cleanup 후 1.9G)
~/.cache      # uv, puppeteer, codex-runtimes 등 다양한 도구 (6.2G)
~/.nvm        # Node 버전들 (1.0G)
~/.cargo      # Rust registry 캐시 (240M)
~/.rustup     # Rust toolchain (525M)
```

### `du` timeout fallback (disk_monitor.py:47)

기존: `du -d 1 <path>` 가 timeout 나면 그 경로는 **통째로 누락**됨 → 변화 추적 불가.

수정: timeout 시 `du -d 0 <path>` (루트 사이즈만) 로 재시도. depth=1 은 잃어도 루트 size 는 기록되어 diff 추적은 유지.

이전 launchd 자동 스캔 (`scan.err`) 에 `! timeout: ~/Downloads`, `! timeout: ~/Library/Containers` 등 다발 — 비정상 부하 시점에 발생한 일시 현상으로 보임. fallback 도입 후엔 그래도 root size 는 잡힌다.

### 회수 결과 (2026-05-22)

- `npm cache clean --force` → 7.2G → 1.9G (**5.3G 회수**)
- `uv cache clean --force` → 4.6G → 0B (**4.1G 회수**, stale `.lock` 무시)
- 합 ~9.4G

`uv cache` 의 `.lock` 파일이 stale 한 케이스 — `ps -eo pid,etime,command | grep [u]v` 에 활성 프로세스 없고 `.lock` mtime 이 수개월 전이면 `--force` 안전.

### 비존재 폴더 오인

`config.json` 에 있던 `~/Library/Developer`, `~/Library/Mobile Documents` 가 `! timeout` 으로 잡혀 사각지대로 의심됐지만 실제로는 **둘 다 폴더 자체가 없음** (Xcode 미설치, iCloud Drive 비활성). config 청소 후보.

## 변경 이력

- 2026-05-14: 최초 생성. disk_monitor.py 작성, 첫 baseline 스냅샷, launchd 등록, plist 프로젝트 폴더 이전, 첫 캐시 정리 (3.23G 회수)
- 2026-05-22: 1주일 운영 후 사각지대 발견 (`.npm` `.cache` 등), config 18→23 경로 확장, `du` timeout fallback (depth=0 재시도) 추가, `npm`/`uv` 캐시 정리로 9.4G 추가 회수 (출처: session-logs/20260522-234234-bc6e)
