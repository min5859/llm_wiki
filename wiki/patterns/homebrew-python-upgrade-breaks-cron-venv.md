---
title: "Homebrew python 메이저 업그레이드가 cron/launchd venv 파이프라인을 조용히 깨뜨리는 패턴"
domain: "ai-agent"
sensitivity: "internal"
tags: ["homebrew", "python", "venv", "cron", "launchd", "path", "silent-failure"]
created: "2026-07-23"
updated: "2026-07-23"
sources:
  - "session-logs/20260722-235919-b228-지금-프로젝트가-7월5일-이후로-동작을-안하고-있는-것-같은데-확인좀-해줘.md"
  - "oss-radar repo (b7ca8dd, b267aaa)"
  - "research-wiki repo (2bf5be8, 1e0910b)"
confidence: "high"
related:
  - "wiki/patterns/launchd-plist-symlink-from-project.md"
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
  - "wiki/projects/oss-radar.md"
---

# Homebrew python 메이저 업그레이드가 cron/launchd venv 파이프라인을 조용히 깨뜨리는 패턴

2026-07-05 Homebrew python 3.14 업그레이드로 `oss-radar`·`research-wiki` 두 cron/launchd 파이프라인이 7/6부터 **17일간 매일** `ModuleNotFoundError: No module named 'requests'` 로 silent 중단됐다 (세션 20260722-235919-b228 에서 발견·수정). 두 프로젝트의 사고 경위는 서로 다르지만 근본 원인은 같다 — **`python3` 라는 이름에 암묵 의존**.

## 함정 1: venv activate 뒤에 PATH 를 다시 prepend

oss-radar 의 `run.sh` 는 `.venv` 를 activate 해 PATH 맨 앞에 `.venv/bin` 을 놓았지만, 그 아래 줄에서 `claude` CLI 를 찾기 위해 PATH 앞에 `/opt/homebrew/bin` 을 **다시** prepend했다. 그 결과 이후의 `python3` 호출은 venv 가 아니라 `/opt/homebrew/bin/python3` (Homebrew 3.14, `requests` 없음) 를 가리키게 됐다.

```bash
source .venv/bin/activate          # PATH: .venv/bin : ...
export PATH="/opt/homebrew/bin:$PATH"   # PATH: /opt/homebrew/bin : .venv/bin : ...  ← venv 무력화
python3 src/discover.py            # /opt/homebrew/bin/python3 실행됨
```

activate 이후 PATH 를 다시 만지는 코드가 있으면 activate 의 효과가 지워진다는 것을 놓치기 쉽다.

## 함정 2: venv 없이 전역 site-packages 에 암묵 의존

research-wiki 는 애초에 `.venv` 가 없어 `python3` 전부가 Homebrew python 을 가리켰다. 7/5 이전에는 구 Homebrew python 의 전역 site-packages 에 `requests` 등이 설치돼 있어 우연히 동작했지만, **Homebrew python 메이저 버전 업그레이드는 site-packages 경로 자체를 바꾼다** (예: `3.13/lib/python3.13/site-packages` → `3.14/lib/python3.14/site-packages`). 업그레이드 순간 참조하던 site-packages 가 깡통으로 리셋되어 의존성이 전부 사라진 것처럼 실패한다.

## 원칙: cron/launchd 는 `python3` 를 부르지 말고 venv 인터프리터를 직접 호출

```bash
# BAD — PATH 상태에 결과가 좌우됨
python3 src/discover.py

# GOOD — 인터프리터 고정, PATH 무관
"$SCRIPT_DIR/.venv/bin/python" src/discover.py
```

launchd/cron 은 로그인 셸을 거치지 않고 fork/exec 하므로 PATH 가 항상 예상대로라는 보장이 없다. `.venv/bin/python` 절대경로를 직접 호출하면 activate 유무·PATH prepend 순서·Homebrew 업그레이드 어느 것도 영향을 못 미친다.

검증은 스크립트 단독 실행이 아니라 **`launchctl kickstart` 로 실제 launchd 환경에서 exit 0** 을 확인해야 한다 — 터미널 셸에서는 PATH 가 달라 재현되지 않는 함정이 흔하다.

## 승격 근거

oss-radar 에서는 이미 2026-04-28 `analyze.sh` 가 같은 계열 버그를 겪은 바 있다 — 시스템 전역 `python3` 를 써서 venv 전용 `yaml`(PyYAML) 을 못 찾은 `ModuleNotFoundError`. 당시 수정은 `.venv/bin/python3` 우선 탐색 + fallback 이었으나, `run.sh` 의 다른 4개 호출부(discover/fetch/analyze/publish)에는 같은 원칙이 전파되지 않아 이번 PATH prepend 사고로 재발했다. **동일 계열 버그가 같은 프로젝트에서 2번째로 등장**한 것이 이 문서의 승격 근거 — 개별 스크립트 단위가 아니라 "cron/launchd 스크립트는 venv 인터프리터를 예외 없이 직접 호출한다"는 프로젝트 전체 원칙으로 승격해야 재발이 멎는다.

## 관련 맥락

- [[launchd-plist-symlink-from-project]] — plist 자체의 PATH·환경변수 함정 (`~/.zshrc` 미반영 등)
- [[macos-launchagent-catchup-behavior]] — launchd 가 미실행 트리거를 처리하는 방식
- [[oss-radar]] — 이번 사고의 프로젝트 기록, 4/28 최초 등장 사례 포함

## 변경 이력

- 2026-07-23: 최초 생성. oss-radar·research-wiki 7/5~7/22 17일 중단 사건 기반, 4/28 analyze.sh 버그에 이은 2회차 등장으로 패턴 승격 (출처: session-logs/20260722-235919-b228-*)
