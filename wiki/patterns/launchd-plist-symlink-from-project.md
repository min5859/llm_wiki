---
title: "launchd plist 프로젝트 폴더 마스터 + ~/Library/LaunchAgents symlink 패턴 (Homebrew 스타일)"
domain: both
sensitivity: public
tags: ["launchd", "macos", "plist", "symlink", "homebrew", "project-layout"]
created: "2026-05-14"
updated: "2026-06-07"
sources:
  - "session-logs/20260514-220947-2eee-todo.md-읽고-이어서.md"
confidence: high
related:
  - "wiki/patterns/launchd-secret-management.md"
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
  - "wiki/projects/disk-monitor.md"
---

# launchd plist 마스터를 프로젝트 폴더에 두고 LaunchAgents 는 symlink

자기 머신에서 매일 돌리는 launchd 잡의 plist 가 `~/Library/LaunchAgents/` 안에만 있으면 **존재 자체를 잊어버린다**. plist 마스터를 프로젝트 폴더에 두고 `~/Library/LaunchAgents/` 는 symlink 만 두는 패턴이 깔끔하다 — Homebrew services 도 같은 방식.

## 효과

| 항목 | LaunchAgents 직접 | 프로젝트 + symlink |
|---|---|---|
| 프로젝트 폴더 열었을 때 plist 가 보임 | ❌ | ✅ |
| 의존하는 스크립트 (`run.sh`, `*.py`) 와 같이 들어 있음 | ❌ | ✅ |
| `git log` 으로 plist 변경 이력 추적 | ❌ | ⚠️ (`.gitignore` 면 불가) |
| launchd 가 정상 로드 | ✅ | ✅ (symlink 따라감) |
| 프로젝트 rename 영향 | — | symlink 끊어짐 → 재 install 필요 |

## 구조

```
project/
├── com.user.<name>.plist     # 마스터 (프로젝트 안)
├── run.sh / *.py             # 실제 실행될 스크립트
└── .gitignore                # com.user.<name>.plist 제외 (절대 경로 박힘)

~/Library/LaunchAgents/
└── com.user.<name>.plist     # → ../../../project/com.user.<name>.plist (symlink)
```

## install 서브커맨드 구현 (Python 예시)

`disk_monitor.py` 에서 발췌·일반화:

```python
LABEL = "com.user.disk-monitor"

def _plist_paths():
    """Return (project-local master plist, ~/Library/LaunchAgents symlink)."""
    project = Path(__file__).resolve().parent / f"{LABEL}.plist"
    link = Path.home() / "Library" / "LaunchAgents" / f"{LABEL}.plist"
    return project, link


def cmd_install(args):
    script = Path(__file__).resolve()
    project_plist, link_path = _plist_paths()

    # 1) plist 본문 작성
    plist = f"""<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key><string>{LABEL}</string>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/bin/python3</string>
        <string>{script}</string>
        <string>scan</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key><integer>{args.hour}</integer>
        <key>Minute</key><integer>{args.minute}</integer>
    </dict>
    <key>StandardOutPath</key><string>{DATA_DIR / 'scan.log'}</string>
    <key>StandardErrorPath</key><string>{DATA_DIR / 'scan.err'}</string>
    <key>RunAtLoad</key><false/>
</dict>
</plist>
"""
    # 2) 마스터를 프로젝트 폴더에 작성
    project_plist.write_text(plist)

    # 3) symlink 갱신 (기존 파일/symlink 제거 후 재링크)
    link_path.parent.mkdir(parents=True, exist_ok=True)
    if link_path.exists() or link_path.is_symlink():
        subprocess.run(["launchctl", "unload", str(link_path)], capture_output=True)
        link_path.unlink()
    link_path.symlink_to(project_plist)

    # 4) launchd 에 로드
    subprocess.run(["launchctl", "load", str(link_path)], check=True)

    print(f"plist (master) → {project_plist}")
    print(f"LaunchAgent    → {link_path}  (symlink)")
```

`uninstall` 시에는 `launchctl unload` → symlink 와 마스터 plist 양쪽 다 정리.

## 함정

### 1. 절대 경로가 박힌다 → `.gitignore` 필수

plist 의 `ProgramArguments` 와 `StandardOutPath` 등은 **절대 경로**를 요구한다. 머신 별로 `/Users/wooki/...` 가 다르므로 그대로 commit 하면 다른 머신에서 못 씀.

```gitignore
# 머신별 absolute path 가 박힌 launchd plist (install 명령이 생성)
com.user.<name>.plist
```

generate 로직만 코드에 들어 있고, plist 자체는 머신 로컬 산출물.

### 2. 프로젝트 폴더 rename 시 symlink 끊어짐

```bash
mv ~/project/git/wk/disk_size ~/project/git/wk/disk_monitor
# → ~/Library/LaunchAgents/com.user.disk-monitor.plist 가 dangling symlink
```

대응: rename 후 `python3 disk_monitor.py install` 한 번 더 실행 (마스터 plist 도 새 경로로 재생성, symlink 도 새로 link).

또는 `Path(__file__).resolve()` 를 install 시점에 박는 패턴이라 install 만 다시 돌리면 자동 갱신 — 코드에서는 rename 영향 없음.

### 3. `~/.zshrc` 의 환경 변수는 안 들어옴

이 패턴 자체와 별개의 launchd 일반 함정. plist 가 셸을 거치지 않고 fork/exec 하므로 토큰/PATH 설정은 별도 처리 필요. 자세히는 [[launchd-secret-management]].

### 4. launchctl list 의 출력 의미

```bash
$ launchctl list | grep com.user.disk-monitor
-       0       com.user.disk-monitor
```

- `-` (PID 자리) → 현재 실행 중 아님 (StartCalendarInterval 의 시간을 기다리는 상태, 정상)
- `0` → 마지막 종료 코드 0 (아직 한 번도 안 돈 상태에서도 0)

실제 첫 실행 후 종료 코드가 0 이 아니면 `scan.err` 확인.

## 영구 비활성화 — `unload` 만으로는 부족하다

잡을 **당분간/영구 중지**하려면 `launchctl unload` 한 번으로는 안 된다. `RunAtLoad`/`KeepAlive` 가 켜진 plist 는 **재부팅 시 macOS 가 `~/Library/LaunchAgents/` 의 plist 를 다시 자동 로드**하므로, unload 는 현재 세션에서만 멈춘 것이다.

이 symlink 패턴에서는 **재부팅 자동 시작을 막는 것이 곧 symlink 제거**다 (원본 plist 는 프로젝트 폴더에 보존되므로 원복이 한 줄):

```bash
# 1) 현재 실행 중지
launchctl unload ~/Library/LaunchAgents/com.user.foo.plist

# 2) 재부팅 자동 시작 차단 — LaunchAgents 의 symlink 만 제거 (원본은 보존)
rm ~/Library/LaunchAgents/com.user.foo.plist

# 원복: symlink 재생성 + load
ln -s "$(pwd)/config/com.user.foo.plist" ~/Library/LaunchAgents/
launchctl load ~/Library/LaunchAgents/com.user.foo.plist
```

체크리스트:
- **중지 전 원본 plist 존재를 확인**한다 (`ls config/com.user.foo.plist`). symlink 만 지우고 원본이 살아 있어야 무손실 원복.
- 여러 잡이 떠 있으면 `launchctl list | grep -iE "foo|bar"` 로 **대상만 콕 집어** 확인하고, 다른 프로젝트의 잡(예: 같은 머신의 다른 trading 봇)은 건드리지 않는다.
- 중지 후 `ps aux | grep <entrypoint>` 로 잔여 프로세스가 완전히 종료됐는지 확인한다.

> Homebrew 의 `brew services stop` 도 동일 구조 — 프로세스 중지 + LaunchAgents symlink 제거.

## 활용 사례

- `disk_monitor` 프로젝트의 plist install 로직 ([[disk-monitor]])
- Homebrew services (`brew services start <name>`) 가 `/opt/homebrew/Cellar/<name>/.../homebrew.<name>.plist` 마스터 + `~/Library/LaunchAgents/homebrew.<name>.plist` symlink 로 구성

## 관련 맥락

- 시크릿 (토큰) 분리는 [[launchd-secret-management]] 참조
- launchd 의 catchup 동작 (Mac 절전 중 누적된 트리거 처리) 은 [[macos-launchagent-catchup-behavior]]

## 변경 이력

- 2026-05-14: 최초 생성. disk_monitor 의 plist 위치 이전 사례 기반 (출처: session-logs/20260514-220947-2eee)
- 2026-06-07: 「영구 비활성화 — unload 만으로는 부족」 섹션 추가. `RunAtLoad`/`KeepAlive` plist 는 재부팅 시 재로드되므로 LaunchAgents symlink 제거가 곧 자동 시작 차단 (원본 plist 보존 → 무손실 원복). upbit_trading 봇 당분간 중지 사례 (출처: session-logs/20260606-210943-6534-*)
