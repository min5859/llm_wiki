---
title: "속성은 디스크에 있는데 AttributeError — in-process 강결합 데몬의 stale 프로세스"
domain: "ai-agent"
sensitivity: "public"
tags: ["bug", "hermes", "hermes-webui", "stale-process", "in-process-coupling", "launchd", "debugging", "attributeerror"]
created: "2026-07-06"
updated: "2026-07-06"
sources:
  - "session-logs/20260705-203359-3336-'ToolEntry'-object-has-no-attribute-'dynamic_schem.md"
confidence: "high"
related:
  - "wiki/analyses/self-hosted-agent-webui-integration.md"
  - "wiki/projects/hermes-dashboard.md"
  - "wiki/projects/hermes.md"
  - "wiki/concepts/hermes-agent.md"
---

# 속성은 디스크에 있는데 AttributeError — stale 프로세스

hermes-webui(:8787)에서 대화를 시도하면 `'ToolEntry' object has no attribute 'dynamic_schema_overrides'` 런타임 에러가 발생. **코드 버그가 아니라, 업스트림 갱신 후 재시작하지 않은 stale 서버 프로세스**가 원인이었다. hermes-webui 가 hermes-agent 내부를 in-process import 하는 강결합 구조([[self-hosted-agent-webui-integration]] 방식 A)라서, "버전 동기화 필수" 리스크가 실제로 터진 사례.

## 증상

- 8787 채팅 시 `AttributeError: 'ToolEntry' object has no attribute 'dynamic_schema_overrides'`
- `api/streaming.py:272` 이 `entry.dynamic_schema_overrides` 를 읽는데 그 속성이 없다고 함
- **그런데 디스크의 코드에는 그 속성이 분명히 존재** → 여기서 "코드 버그"라는 첫 직관이 깨진다

## 핵심 함정 — 디스크 코드는 정상인데 런타임만 실패

grep 으로 확인하면 시스템 전체에 `ToolEntry` 정의는 **딱 하나**(`~/.hermes/hermes-agent/tools/registry.py`)뿐이고, 그 클래스는 `__slots__`·`__init__` 모두 `dynamic_schema_overrides` 를 갖는다. `api/streaming.py:272` 도 그걸 정상 참조한다. 즉 **지금 새로 뜨는 프로세스라면 에러가 안 난다.** 그런데도 실행 중 프로세스만 AttributeError를 낸다 → **메모리에 로드된 옛 코드 스냅샷**이 범인.

## 결정적 진단 — 프로세스 기동 시각 vs 파일 mtime 대조

```bash
# 실행 중 프로세스의 기동 시각
ps -o pid,lstart,etime,command -p <PID>
#  55747  2026-07-04 14:42:33  01-05:55:35  .../venv/bin/python server.py

# 관련 파일들의 갱신 시각
stat -f "%Sm %N" api/streaming.py                    # Jul 4 14:45:36
stat -f "%Sm %N" ~/.hermes/hermes-agent/tools/registry.py   # Jul 4 18:05:08

# 속성이 언제 추가됐는지 (git)
git log --oneline -S "dynamic_schema_overrides" -- tools/registry.py
```

타임라인: 프로세스는 **14:42 기동** → 그 뒤 `streaming.py`(14:45)·`registry.py`(18:05)가 갱신됨. 즉 프로세스가 두 파일 업데이트보다 **먼저 떠서** 메모리에 서로 어긋난 옛 코드를 물고 있다. `dynamic_schema_overrides` 는 2026-05-09에 추가돼 현재 HEAD에 있으므로 디스크는 일관.

**결정 검증**: agent venv 파이썬으로 지금 새로 import 해서 속성 존재를 확인 → disk 코드가 정상임을 확정.

```bash
venv/bin/python -c "from tools.registry import ToolEntry; \
print('dynamic_schema_overrides' in ToolEntry.__slots__)"   # True
```

## 원인

hermes-webui 는 hermes-agent 를 **in-process import** 하는 강결합 구조다. 이 방식은 UI 가 에이전트 내부 메모리 상태에 직접 붙어 깊은 기능을 쓰지만, **에이전트 내부가 바뀌면 프로세스를 재시작해야 반영된다.** 업스트림(agent) 갱신 후 서버를 재시작하지 않으면, WebUI 코드(신)와 메모리에 로드된 agent 코드(구)의 버전이 어긋나 이런 AttributeError가 난다. README 도 "agent와 webui는 같이 올리고 재시작"을 명시.

## 해결 — 코드 수정 불필요, 서버 재시작

```bash
kill <PID>   # 이 프로세스는 pidfile 없이 직접 기동돼 ctl.sh stop 으로 못 잡음
```

재시작 후 `/health` 로 `status: ok` + 낮은 uptime 확인 → 일관된 디스크 코드가 새로 로드됨.

### launchd 데몬 함정 — kill 하면 자동 respawn

이 프로세스는 **launchd 가 관리(PPID 1)**하는 데몬이라, `kill` 하는 순간 바로 새 프로세스가 8787을 다시 잡는다. 따라서:

- `kill <PID>` 만으로 재시작 완료 (respawn 이 알아서 새 코드 로드)
- `ctl.sh start` 를 이어서 실행하면 **포트 충돌 위험** — 불필요
- 재시작 여부는 `ps -o pid,ppid,lstart -p <새PID>` 로 PPID=1 + 방금 기동 확인

## 재사용 교훈

- **"속성이 코드에는 있는데 AttributeError"** = 코드 버그가 아니라 **stale 장수 프로세스**를 1순위로 의심하라. 추측 전에 mtime·기동시각·git 을 대조.
- 진단 3종: `ps ... lstart/etime`(기동 시각) ↔ `stat -f %Sm`(파일 mtime) ↔ `git log -S`(속성 도입 시점). 프로세스 기동이 파일 갱신보다 이르면 stale 확정.
- **in-process import 강결합 데몬**은 업스트림 코드 변경 시 반드시 재시작. `git pull`/자동 업데이트가 파일만 바꾸고 실행 중 프로세스는 안 건드린다는 점이 함정. ([[self-hosted-agent-webui-integration]] 방식 A의 "버전 동기화 필수" 리스크의 실제 발현)
- **재시작 절차는 기동 방식에 종속**: pidfile 있는 `ctl.sh` 기동이면 `ctl.sh restart`, 직접 기동이면 `kill`. launchd/watchdog 관리 데몬이면 `kill` = 자동 respawn 이므로 별도 start 금지(포트 충돌).

## 변경 이력

- 2026-07-06: 신규 작성 — hermes-webui ToolEntry AttributeError stale-process 진단 사례 (출처 3336).
