---
title: "macOS python3.12 ~/Documents 접근 TCC 팝업 — 갑작스레 시작된 원인 진단"
domain: both
sensitivity: public
tags: ["analysis", "macos", "tcc", "python", "launchd", "diagnosis", "fs_usage", "lsof"]
created: "2026-05-16"
updated: "2026-05-16"
source_session: "20260515-231744-34b6-지금-python3.12-가-문서-폴더의-파일에-접근하려고-합니다.-라는-팝업창이-계속-뜨.md"
sources:
  - "session-logs/20260515-231744-34b6-지금-python3.12-가-문서-폴더의-파일에-접근하려고-합니다.-라는-팝업창이-계속-뜨.md"
confidence: high
related:
  - "wiki/patterns/macos-tcc-full-disk-access.md"
  - "wiki/projects/upbit-trading.md"
  - "wiki/patterns/launchd-plist-symlink-from-project.md"
---

# macOS python3.12 ~/Documents 접근 TCC 팝업 진단

장기 가동 중인 launchd 관리 Python 봇이 어느 날부터 갑자기 "python3.12 가 문서 폴더의 파일에 접근하려고 합니다" 토스트 팝업을 반복적으로 띄우는 케이스의 진단 절차. iTerm/Terminal 류의 [[macos-tcc-full-disk-access]] (= 사용자 입력 셸 → 다른 앱 sandbox) 와 달리, 이건 **데몬화된 Python 프로세스 → ~/Documents** 라는 별개의 시나리오다.

## 증상

- 어떤 사용자 조작 없이도 화면 상단에 "python3.12 가 문서 폴더의 파일에 접근하려고 합니다 (허용 안 함) (허용)" 토스트가 주기적으로 등장
- 같은 Python 봇이 **수 주 ~ 수 개월** 정상 가동해 왔는데도 갑자기 발생
- 사용자가 "허용/안 함" 어느 쪽을 눌러도 다시 떴다 사라졌다 반복

## 5단계 진단 절차

### 1단계 — 어느 PID 인가 (Python.app 번들 탐지)

macOS TCC 가 표시하는 "python3.12" 는 보통 **`Python.app` 번들 인터프리터** (`/opt/homebrew/.../Python.framework/.../Resources/Python.app/Contents/MacOS/Python`) 로 실행된 프로세스다. 일반 `python3.12` 심볼릭 링크나 venv 인터프리터는 보통 TCC 에 다른 이름으로 인식된다.

```bash
pgrep -lf python | grep -v "notion-mcp"
# 각 PID 의 실행 경로 확인
lsof -p <PID> 2>/dev/null | awk '$4=="cwd" || $4=="txt"' | head -5
```

`txt` 행이 `.../Python.framework/Versions/3.12/Resources/Python.app/Contents/MacOS/Python` 으로 끝나는 프로세스가 용의자. 같은 3.12.x 라도 venv 의 일반 `python3.12` 바이너리는 토스트 대상이 아닐 수 있다.

### 2단계 — 부모와 가동 시점 확인 (launchd 데몬?)

```bash
ps -o pid,ppid,user,lstart,command -p <PID>
```

`PPID=1` 이면 launchd 직속 데몬. 가동 시점이 며칠 ~ 몇 주 전이라면 그 사이의 코드 변경이나 OS 정책 변경 둘 중 하나가 트리거.

### 3단계 — 코드 자체에 ~/Documents 참조가 있는가

```bash
grep -rIn --include="*.py" -E "Documents|expanduser|Path\.home\(\)|os\.environ.*HOME" <project> 2>/dev/null | grep -v "/\.venv/"
```

대부분의 케이스에서 **사용자 코드에는 Documents 참조가 없다**. 그 경우 의존성 라이브러리 (matplotlib 캐시·OpenAI/HF 캐시·telegram-bot 다운로드 디렉터리 등) 가 HOME 디렉터리를 traverse 하다 ~/Documents 게이트에 걸렸을 가능성이 높다.

### 4단계 — 실시간 syscall 추적 (fs_usage)

권한 필요 (sudo). 팝업이 다시 뜨는 순간 어느 파일 호출인지 한 줄로 잡는다:

```bash
sudo fs_usage -w -f filesys -p <PID> 2>/dev/null | grep -i Documents
```

**함정**: TCC 가 *거부* 한 호출은 fs_usage 에 안 잡히기도 한다. 아무것도 안 잡혀도 의존성 호출은 일어났을 수 있다 — `tccd` 로그를 봐야 한다 (5단계).

### 5단계 — TCC 시스템 정책 변경 시점 확인

```bash
# 시스템 TCC DB 의 mtime
stat -f "%Sm  %N" /Library/Application\ Support/com.apple.TCC/TCC.db
# 사용자 TCC DB 의 mtime
stat -f "%Sm  %N" ~/Library/Application\ Support/com.apple.TCC/TCC.db
# 최근 OS / Safari / XProtect 설치 이력
system_profiler SPInstallHistoryDataType 2>/dev/null | grep -B1 -A4 -iE "macOS|Safari|Tahoe|XProtect" | head -80
# tccd 의 최근 로그
log show --predicate 'process == "tccd"' --info --last 3h 2>/dev/null | grep -iE "documents|python|prompt"
```

`/Library/Application Support/com.apple.TCC/TCC.db` 의 mtime 이 **OS 본체 업데이트 일자와 무관한 정시 (예: 10:00:01)** 에 변경됐다면, Apple 의 백그라운드 정책 푸시 (XProtect / 보안 설정 자동 동기화) 가 들어왔다는 강한 신호. 묵시적으로 통과되던 접근이 "신규 동의 필요" 카테고리로 재분류된 가능성이 크다.

## "왜 갑자기" 의 가장 흔한 원인 2가지

### ① macOS 시스템 TCC 정책의 백그라운드 자동 갱신

OS 본체와 Python 바이너리는 안 건드려졌는데도 시스템 TCC.db 만 갱신될 수 있다. Apple 의 보안 정책 푸시 (XProtect / Safari / GateKeeper 등) 시점과 일치하는 경우가 많다. 같은 코드·같은 바이너리·같은 launchd 잡인데 "갑자기" 묻기 시작하는 패턴의 1순위 후보.

### ② 며칠 만에 처음으로 호출된 코드 경로

최근 커밋이 부분 익절·일일 리포트·trailing tighten 같은 **간헐적으로만 트리거되는 코드 경로**를 추가했고, 그 경로가 며칠 동안은 호출 안 되다가 특정 조건에 처음 도달했을 가능성. 매매 봇·모니터링 봇처럼 조건부 분기가 많은 시스템에서 흔하다.

①과 ②가 결합되면 ("전엔 안 닿던 코드 경로 + 새 정책") 더 그럴듯해진다.

## 대응 옵션

| 옵션 | 효과 | 부작용 |
|------|------|--------|
| 시스템 설정 → 개인정보 보호 → 파일 및 폴더 → `python3.12` 의 "문서 폴더" 토글을 **거부** 로 두기 | 더 안 묻음 | 봇이 그 경로를 못 읽음. 의존성이 ~/Documents 를 쓰면 그 기능만 깨짐 |
| `kill <PID>` 후 venv 의 일반 인터프리터로 재기동 | 동일 봇이 일반 `python3.12` 로 뜨면 Python.app 번들 인식이 회피될 수 있음 | venv 도 동일 framework 이면 효과 없음. plist 변경 필요 |
| fs_usage / tccd 로그로 정확한 호출 라이브러리 추적 → 그 라이브러리 캐시 경로를 ~/Library 로 우회 | 근본 원인 제거 | 라이브러리 설정 변경 필요 |
| 무시 (허용/안함 둘 다 안 누르고 토스트 자동 사라짐 대기) | 0 작업 | 토스트가 며칠 ~ 영구 반복 |

대다수 case 의 실용적 답은 **"파일 및 폴더" 시스템 설정에서 거부 토글로 두기** — 봇이 ~/Documents 를 진짜 써야 하는 정상 코드가 아닌 한 (대부분 그렇다), 거부해도 봇 동작에 영향이 없다.

## 함정

1. **venv 도 Python.app 번들을 상속**: `~/.venv/bin/python` 이 사실은 framework 번들의 심볼릭 링크 / wrapper 다. venv 라고 안전한 게 아니다. `readlink -f` 로 확인.
2. **자식 프로세스가 별개 PID 로 추가 등장**: `fork()` 하는 코드 (multiprocessing 등) 면 매번 새 PID 가 뜨고, 그때마다 토스트가 다시 뜰 수 있다. 부모만 허용/거부해도 자식이 따로 묻는 듯한 증상.
3. **cron 으로 30분마다 새 인터프리터**: 사용자 crontab 에 `*/30 ... python ...` 같은 잡이 있으면 매 발화마다 새 단명 프로세스가 뜨고 토스트가 묶음으로 발생한다. `crontab -l` 로 확인.
4. **TCC 거부 후에도 봇 동작은 계속**: TCC 가 거부한 syscall 은 `EACCES` 로 반환되는데 라이브러리들이 보통 `try / except` 로 흡수해 봇 자체는 멀쩡히 돈다. "팝업만 시끄럽다" 가 거의 정답.
5. **fs_usage 가 아무것도 안 잡힘**: TCC 가 *거부* 한 호출은 syscall 레벨에서 안 보일 수 있다. 그 경우 `tccd` 로그가 더 신뢰할 수 있는 소스.

## Apple 백그라운드 정책 푸시의 흔적 (참고)

OS 본체 업데이트 없이도 보안 정책만 갱신되는 채널은 macOS 에 여러 개 있다:

- **XProtect** (악성코드 시그니처) — `/Library/Apple/System/Library/CoreServices/XProtect.bundle`
- **XProtectPlistConfigData** — 정책 plist
- **XProtectCloudKitUpdate** — CloudKit 경로의 정책 푸시
- **MRT** (Malware Removal Tool) — 구버전, 현재는 XProtect 통합

`system_profiler SPInstallHistoryDataType` 로 최근 1주 ~ 1개월의 푸시 이력을 보면 며칠 간격으로 들어온다. TCC 정책 자체는 이들과 별개 채널일 수 있지만, **OS 업데이트 없이 시스템 TCC.db 가 갱신된 경우** 그 시점에 Apple 의 정책 푸시가 같이 들어왔을 확률이 높다.

## 관련 맥락

- iTerm/Terminal 류의 TCC (셸이 다른 앱 sandbox 에 접근) 는 [[macos-tcc-full-disk-access]] 의 시나리오 — 본 페이지와 처리 방식이 다르다 (시스템 설정의 "전체 디스크 접근" vs "파일 및 폴더").
- launchd 로 데몬화된 Python 봇의 가시성·재기동 패턴은 [[launchd-plist-symlink-from-project]] 와 [[upbit-trading]] 참조.

## 변경 이력

- 2026-05-16: 최초 생성 (session-logs/20260515-231744-34b6) — 5/10 튜닝 커밋 직후 launchd 가동 중인 upbit_trading 봇이 5/15 자로 갑자기 ~/Documents TCC 팝업을 띄우기 시작한 케이스. 시스템 `/Library/Application Support/com.apple.TCC/TCC.db` 가 같은 날 10:00:01 정시에 자동 변경된 흔적 확인. fs_usage / tccd 로그 추적 후에도 정확한 라이브러리 미특정 (TCC 거부된 호출이 syscall 레벨에 안 잡힘) — 진단 절차와 일반 패턴만 영속화
