---
title: "Hermes WebUI 업데이트 후 offline — 런타임·모듈 경로 복구"
domain: ai-agent
sensitivity: internal
tags: [hermes, webui, python, launchd, debugging, operations]
created: 2026-10-03
updated: 2026-10-03
sources:
  - "local-only: /Users/wooki/.hermes/webui.launchd.log"
  - "local-only: /Users/wooki/Library/LaunchAgents/com.hermestalk.webui.plist"
  - "local-only: /Users/wooki/.hermes/scripts/webui_runtime_launcher.py"
  - "local-only: /Users/wooki/.hermes/scripts/test_webui_runtime_launcher.py"
  - "local-only: /Users/wooki/.hermes/scripts/test_webui_runtime_integration.py"
  - "local-only: /Users/wooki/.hermes/hermes-agent/hermes_cli/venv_sync.py"
confidence: high
related:
  - wiki/projects/hermes.md
  - wiki/analyses/multi-profile-cli-agent-isolation.md
---

# WebUI 업데이트 후 offline: 복구·재발 방지 Runbook

2026-10-03 WebUI 업데이트 후 브라우저에 “You are offline / Hermes requires a server connection”이 표시됐다. 인터넷 장애가 아니라 로컬 WebUI 프로세스의 반복 종료였다. Mac 기본 Python 버전을 변경할 필요는 없었다.

## 증상과 조사 근거

- `127.0.0.1:8787` HTTP 연결 실패, listener 없음.
- launchd의 `com.hermestalk.webui`는 실행 재시도 상태, exit code 1.
- stderr: `ModuleNotFoundError: No module named 'api'`.
- WebUI 저장소의 `api/` 폴더는 존재했고, 기존 Python에서 저장소 cwd로 import하거나 server.py를 직접 실행하면 동작했다. 따라서 `pip install api`를 할 문제가 아니었다.
- 서비스는 업데이트 전 `~/.hermes/hermes-agent/venv/bin/python`(Python 3.11)을 사용했다. 최신 Hermes는 별도 관리 Python 3.14 런타임을 사용했다.
- 오류 trace는 격리된 `-c`/`runpy.run_path` 실행을 보여줬다. 기존 interpreter에서 Hermes 런타임 자동 전환을 거치며 WebUI 저장소 경로가 빠지는 것으로 진단했다. 격리 실행에서 script 경로를 지정하는 것만으로 api 패키지 검색 경로가 자동 보존되는 것은 아니었다.
- 단순 `kickstart`로는 같은 오류가 반복됐고, 현재 Hermes runtime bootstrap에 WebUI 경로를 명시한 실행으로 복구됐다.

## 1차 복구 결과

대표님이 Mac 터미널에서 서비스 reload를 수행한 뒤 확인:

- `/`: HTTP 200
- `/api/profiles`: HTTP 200, 프로필 9개
- launchd: running, PID 89728, 당시 재기동 이후 종료 없음

1차 복구는 Python 3.14 실행 파일을 plist에 고정했다. 현재는 동작하지만 추후 Hermes 런타임 경로가 바뀌면 재수정이 필요할 수 있어, 다음의 자동 탐색 런처를 추가했다.

## 재발 방지 설정

영구 파일(자동 삭제되는 scratch에 두지 않음):

- `~/.hermes/scripts/webui_runtime_launcher.py`: 실제 실행 런처
- `~/.hermes/scripts/install_webui_runtime_launcher.py`: 백업·설치·설정 검증
- `~/.hermes/scripts/test_webui_runtime_launcher.py`: 경로/argv 회귀 테스트
- `~/.hermes/scripts/test_webui_runtime_integration.py`: 별도 포트의 실제 HTTP 기동 테스트

새 plist ProgramArguments:

```text
/usr/bin/python3
/Users/wooki/.hermes/scripts/webui_runtime_launcher.py
```

동작:

1. macOS의 `/usr/bin/python3`는 가벼운 런처만 실행한다.
2. `~/.local/bin/hermes --print-runtime-command`로 그 시점의 Hermes runtime argv를 조회한다.
3. 조회된 runtime bootstrap을 유지하고 WebUI 저장소를 sys.path에 명시한다.
4. server.py의 절대경로를 sys.argv에 설정하고 현재 Hermes Python으로 exec한다.
5. 인증 환경·포트·로그·KeepAlive 설정은 유지한다. 실제 WebUI는 시스템 Python이 아니라 Hermes 관리 runtime에서 실행된다.

Hermes/WebUI 저장소 내부 코드는 수정하지 않아 git update로 로컬 패치가 사라지는 문제를 피했다. 다만 Hermes CLI의 runtime 출력 형식이나 저장소 위치가 바뀌면 런처도 점검해야 한다. 모든 종류의 업데이트 장애를 막는다는 보장은 아니다.

### 적용 상태 — 2026-10-03 18:33 KST

- 자동 탐색 런처 설치 및 테스트 완료.
- plist를 새 런처로 변경하고 read-back 검증 완료. ProgramArguments 외 모든 기존 필드 동일 확인.
- 실행 중인 production 서버는 기존 1차 복구 프로세스이며 HTTP 200 유지.
- **새 plist를 launchd에 reload하는 마지막 단계는 대표님의 외부 Mac 터미널 실행 대기. 아직 새 방식으로 production 서비스가 기동했다고 주장하지 않는다.**

백업:

- 1차 복구 전: `~/Library/LaunchAgents/com.hermestalk.webui.plist.bak.runtime-20261003-175958`
- 자동 탐색 전: `~/Library/LaunchAgents/com.hermestalk.webui.plist.bak.dynamic-20261003-183359`

백업은 mode 600이다. plist에는 인증 환경값이 포함되므로 내용 공개·Git 커밋 금지.

## 적용 / 재발 시 복구 명령

**Mac 터미널에서 실행.** Telegram Gateway 안에서는 launchctl bootstrap/submit이 안전장치로 차단된다. 다른 동사로 우회하지 않는다.

```bash
/usr/bin/python3 /Users/wooki/.hermes/scripts/webui_runtime_launcher.py --check
```

`WEBUI_RUNTIME_IMPORT_OK` 확인 후, 이미 수정된 서비스 정의를 다시 불러온다:

```bash
launchctl bootout gui/$(id -u)/com.hermestalk.webui &&
launchctl bootstrap gui/$(id -u) /Users/wooki/Library/LaunchAgents/com.hermestalk.webui.plist
```

서비스가 아예 등록되지 않았다면 bootout은 실패한다. 그 경우 상태를 확인하고 bootstrap만 실행한다(반복 무작정 재실행 금지).

설치 정의가 예전 실행 경로로 덮어써졌다면 먼저 installer를 재실행한다:

```bash
/usr/bin/python3 /Users/wooki/.hermes/scripts/install_webui_runtime_launcher.py
```

재기동 뒤 검증:

```bash
curl -fsS -o /dev/null -w '%{http_code}\n' http://127.0.0.1:8787/
curl -fsS http://127.0.0.1:8787/api/profiles
lsof -nP -iTCP:8787 -sTCP:LISTEN
```

HTTP 200과 프로필 목록을 확인하고 브라우저를 새로고침한다. 실제 채팅 1회도 확인하면 좋다. launchctl 전체 출력은 API 키가 포함될 수 있으므로 공유하지 않는다.

## 검증 범위

- 회귀 테스트: 누락된 launcher를 실패로 확인한 뒤 구현. 서로 다른 interpreter argv 두 개로 모듈 import와 server argv 보존 테스트 통과.
- 실제 runtime import: `WEBUI_RUNTIME_IMPORT_OK`.
- 별도 포트·임시 WebUI state에서 실제 서버를 두 번 기동: 매회 `/`·`/api/profiles` 200, 프로필 9개.
- 테스트 서버는 종료했고 production 포트는 건드리지 않았다.
- 실제 Hermes 업그레이드를 다시 실행하거나 WebUI 업데이트 버튼을 재클릭하는 파괴적/네트워크 변경 테스트는 하지 않았다.
- 새 launchd 정의의 production 활성화와 실제 채팅은 마지막 reload 뒤 확인해야 한다.

## 관련 맥락

운영 정본은 [[hermes]], 프로필별 실행 환경 원리는 [[multi-profile-cli-agent-isolation]] 참고.

## 변경 이력

- 2026-10-03: 장애 원인·1차 복구 결과, 자동 runtime 탐색 런처·테스트·백업·재발 복구 명령 기록. 새 production 서비스 활성화 대기는 별도 표시.
