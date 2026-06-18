---
title: "디스크 모니터링 사각지대 — 추적 경로 vs 실제 free delta 의 괴리"
domain: both
sensitivity: public
tags: ["pattern", "disk-monitoring", "macos", "du", "homedir-caches", "diagnosis"]
created: "2026-05-22"
updated: "2026-06-18"
sources:
  - "session-logs/20260522-234234-bc6e-disk-monitoring-내용을-분석해-주세요,.md"
  - "session-logs/20260603-150720-5764-디스크-모니터링-상태를-체크해-주세요.-최근-용량이-많이-줄어든것-같습니다.md"
  - "session-logs/20260618-214720-d1fe-모니터링-값을-분석해줘-계속-disk-소모가-큰것-같네.md"
confidence: high
related:
  - "wiki/projects/disk-monitor.md"
  - "wiki/analyses/macos-disk-cleanup-cache-classification.md"
---

# 디스크 모니터링 사각지대 — 추적 경로 vs 실제 free delta 의 괴리

매일 N개 경로를 `du` 로 측정해 변화를 추적하는 디스크 모니터링 도구의 일반 패턴. **모니터링 경로 내 변화 합과 실제 free space 변화 사이의 괴리** 가 그 시점의 사각지대를 가리킨다.

## 진단 공식

```
disk_blind_spot ≈ ΔFree(전체) - Σ Δsize(추적경로)
```

- 양쪽 부호가 반대 (free 는 줄었는데 추적 경로는 변화 없음/감소) 면 **사각지대에서 증가**
- 단순 부등호가 아니라 **수 GB 단위로 어긋날 때** 만 조사 가치 있음 (작은 차이는 `du` 의 sparse/hardlink 누락 등 측정 노이즈)

## 흔한 사각지대 (HOME 아래)

| 경로 | 정체 | 회수 안전도 |
|---|---|---|
| `~/.npm` | npm 캐시 (`_cacache`) | safe (`npm cache clean --force`) |
| `~/.cache/uv` | uv 의 wheel/source 캐시 | safe (`uv cache clean`) |
| `~/.cache/puppeteer` | Puppeteer 가 다운받은 Chrome 본체 | 재다운로드 후보 — 자주 쓰면 보존 |
| `~/.cache/huggingface` | HF Hub 모델 캐시 | 재다운로드 (수 GB) — 사용 빈도 본 후 |
| `~/.cache/chroma` | Chroma DB (벡터 데이터) | **caution** — 데이터 자체일 수 있음 |
| `~/.nvm` | Node 다중 버전들 | 안 쓰는 버전만 선별 |
| `~/.cargo` | Rust registry 캐시 + 설치된 바이너리 | registry 만 정리 가능 |
| `~/.rustup` | Rust toolchain 본체 | 안 쓰는 toolchain 만 선별 |
| `~/.docker`, `~/.colima`, `~/.lima` | 컨테이너 런타임 데이터 | 디스크 이미지가 GB 단위 |
| `~/.gradle`, `~/.android` | Android/Gradle 도구체인 | 비활성 프로젝트라면 통째 |
| `~/.ollama` | Ollama 로컬 모델 | GB ~ 수십 GB 가능 |
| `/opt/homebrew`, `/usr/local` | Homebrew 인스톨 트리 | 자주 안 정리되는 누적 |

이 목록의 핵심은 "macOS 표준 `~/Library/...` 경로만 보면 놓친다" — 패키지 매니저 / 도구체인은 보통 dotfile 디렉터리 (`~/.<도구>`) 에 캐시를 둔다.

## 시스템 사각지대 (HOME 밖)

- **Time Machine local snapshots** — `tmutil listlocalsnapshots /` 로 확인. APFS 상에서 보이지 않는 free 잠금
- **APFS purgeable 공간** — `df` 의 free 와 Finder 의 "사용 가능" 가 불일치하는 주요 원인. `system_profiler SPStorageDataType` 으로 확인
- **`/private/var/folders/...`** — XPC 캐시, 일부 앱은 여기에 누적
- **macOS 업데이트 준비물** — OS 업데이트 다운로드(~2G 페이로드) + `com.apple.os.update-*` / `MSUPrepareUpdate` **준비 스냅샷**이 합쳐 흔히 10~16G 점유. `/Library/Updates` 는 보통 비어 있고 실제 페이로드·스냅샷은 `/System/Volumes/Update` 등 추적 밖. `tmutil listlocalsnapshots /` 에 `com.apple.os.update-*` 가 보이고 `softwareupdate --list` 에 대기 업데이트가 있으면 이것. **설치(재시작)하면 자연 회수**되며 사용자 파일 정리로 풀 양이 아니다 (강제 삭제는 재다운로드되므로 금지)

## `du` 의 trap: timeout 시 통째 누락

`du -d 1 <path>` 가 timeout (예: 큰 `~/Library/Containers`) 나면 해당 경로 행이 **결과에 아예 안 들어감** → 다음 측정과 diff 할 때 변화 추적이 끊긴다.

### Fallback 패턴

```python
def du(path, timeout_s):
    try:
        return subprocess.run(["du", "-d", "1", path], timeout=timeout_s, ...)
    except TimeoutExpired:
        # depth=1 은 포기하지만 root size 라도 회수
        return subprocess.run(["du", "-d", "0", path], timeout=timeout_s, ...)
```

`depth=1` 분해는 잃어도 root size 는 보존 → 일별 diff 가 계속 의미를 가진다.

### 더 나쁜 함정: 완전 실패 시 조용한 누락 (2026-06-03)

`depth=0` 재시도마저 timeout 으로 완전 실패하면 `du_bytes` 가 `{}` 를 반환 → 그 경로가 스냅샷에서 **조용히 사라진다**(`None`). 게다가 `report` 가 "추적 경로 합계" 와 "free 변화" 를 **대조하지 않으면**, "16G 줄었는데 추적 증가는 1.3G" 같은 거대한 갭이 **눈에 안 보인다**. 측정 실패가 곧 "변화 없음" 으로 위장된다.

**보완 3가지 (단일 파일·의존성 0 유지)**:

1. **측정 실패 가시화** — `du_bytes` 가 성공 여부(`ok`)를 함께 반환. `scan` 이 실패 경로를 스냅샷의 `errors` 필드에 기록 + 경고 출력. (실패 경로는 `0` 으로 기록하지 말고 diff 에서 **제외** — `ok=False`)
2. **진단 공식을 코드에 내장** — `report` 가 `free Δ` vs `추적 top-level 합계 Δ` 를 대조해 한 줄로:
   ```
   Free: 117.0G → 101.9G (-15.1G)
   Tracked Δ (top-level):  +1.2G                          ← 사용자 영역 실제 증가
   Unaccounted Δ (system/purgeable/untracked):  +13.9G    ← 사각지대 (업데이트/purgeable 등)
   ```
   사각지대 크기가 매 report 에 자동으로 드러나, 사람이 매번 빼볼 필요가 없다.
3. **스냅샷에 `roots` 기록** — 어떤 경로를 측정했는지 스냅샷에 명시. 구버전 스냅샷(roots/errors 없음)과의 diff 는 fallback 동작.

> 원리: **측정 실패를 0/누락으로 침묵 처리하면 안 된다.** 실패는 명시적으로 기록하고, 부분합은 항상 전체(free)와 대조해 설명 안 되는 갭을 드러내라. (LLM publish 의 silent skip 가시화 [[prompt-schema-pipeline-coupling]] 와 같은 결.)

## 진단 워크플로우

오늘 모니터링 결과와 실제 free delta 가 어긋나면:

1. **사각지대 후보 1차 스캔** — HOME 아래 흔한 도구 캐시 한 번 훑기

   ```bash
   for d in ~/.docker ~/.colima ~/.lima ~/.npm ~/.cache ~/.cargo ~/.rustup \
            ~/.ollama ~/.pyenv ~/.nvm ~/.gradle ~/.android; do
     [ -d "$d" ] && du -sh "$d" 2>/dev/null
   done
   du -sh /opt/homebrew /usr/local 2>/dev/null
   ```

2. **`config` 보강** — 발견된 큰 경로를 모니터링 대상에 추가. 그래야 다음 사이클부터 변화량 추적 가능 (지금 절대 크기는 추정만 가능, 정확한 1주일 증가량은 모른다)

3. **TM local snapshots / APFS purgeable 확인** — HOME 에 없는 GB 단위 잠금 의심 시

4. **존재 여부 검증** — `! timeout` 으로 잡힌 경로 중 실제로는 폴더 없음 (Xcode 미설치 → `~/Library/Developer`, iCloud Drive 비활성 → `~/Library/Mobile Documents`) 케이스. config 청소 후보.

## 함정: config 변경 구간을 가로지르는 diff (신규 경로의 `0.0 → X`)

추적 경로를 **중간에 config 로 추가**하면, 그 경로는 추가 이후 첫 스냅샷부터 측정되므로 그 이전 스냅샷과 diff 하면 `0.0 → X` 로 나온다. 이는 **"신규 증가"가 아니라 측정 시작점이 다른 diff 아티팩트**다.

```
전체 기간(5/14→6/18) 증가 TOP:
  +29.7G  0.0→29.7  ~/project      ← 5/30 에 추적 추가됨. 진짜 증가 아님
  +6.1G   0.0→ 6.1  ~/.hermes      ← 동일
  +3.2G   0.0→ 3.2  ~/.cache       ← 동일
```

- **올바른 baseline 선택**: 추세를 보려면 **모든 경로가 갖춰진 이후 시점**을 baseline 으로 잡는다. 위 사례에서 5/30 에 8개 경로를 추가했으므로 6/05 baseline 의 13일 diff 가 정확 (주범은 `~/project +10.5G`, codex 7.9G).
- **스냅샷 `roots` 로 자동 판별 가능**: `roots`(어떤 경로를 측정했는지)가 두 스냅샷에서 다르면 그 사이 diff 는 신규 경로에 대해 무효 → `report` 가 "이 경로는 baseline 에 없었음"을 표시하면 사람이 매번 의심할 필요가 없다.
- 같은 결의 silent 함정: 0 으로 보고하면(측정 실패 vs 신규 추가 vs 진짜 0 을 구분 못 하면) 사용자가 가짜 추세를 사실로 읽는다.

## 안티패턴

- **config 변경 구간을 가로질러 diff 의 절대 증가량을 보고하기** — 신규 추가 경로의 `0.0→X` 를 "증가"로 단정 (위 함정).
- **사각지대 추정치를 단정형으로 보고하기** — "1주일간 `.npm` 이 7G 증가" 같은 단정은 잘못. 과거 측정이 없으므로 **현재 절대값** 만 알 수 있다. 사용자에게 보고할 때 명시.
- **자동 정리** — 사각지대를 발견했다고 스크립트가 자동으로 `cache clean` 호출하면 `~/.cache/chroma` (실데이터일 수 있음) 같은 항목을 망친다. 분류 + 컨펌 패턴 ([[macos-disk-cleanup-cache-classification]]) 유지.

## 사례 (2026-05-22, [[disk-monitor]])

- ΔFree = -18.4G, 추적 경로 내 Σ = -1.4G → 사각지대 ≈ 17G
- 보강 대상: `~/.npm` (7.2G), `~/.cache` (6.2G), `~/.nvm`, `~/.cargo`, `~/.rustup`
- 회수: `npm cache clean --force` 5.3G + `uv cache clean --force` 4.1G = 9.4G
- 코드 변경: `du` timeout fallback (`depth=0` 재시도), config 18 → 23 경로

## 사례 (2026-06-03, [[disk-monitor]])

- ΔFree = -16.2G (125.6 → 109.5G) 하루 만에 급감. 추적 top-level 증가는 합쳐 ~1.3G (최대 항목 `Caches/...claudefordesktop.ShipIt` +670M = Claude Desktop 업데이트 잔재, safe). home 내 최근 2일 1GB+ 신규 파일 없음.
- 정체: **macOS Tahoe 26.5.1 업데이트가 다운로드되어 설치 대기(restart) 중.** `tmutil listlocalsnapshots /` 에 `com.apple.os.update-*` / `MSUPrepareUpdate` 3개, `softwareupdate --list` 에 대기 업데이트. 페이로드 ~2G + 준비 스냅샷이 free 점유. 5/22 -9.7G→5/23 +22.3G 출렁임도 같은 업데이트 사이클로 추정.
- 회복: 업데이트 설치(재시작)하면 자연 회수. 사용자 파일 정리 대상 아님.
- 코드 보완: `du_bytes` 성공여부 반환 + `errors`/`roots` 스냅샷 필드, `report` 에 Tracked vs Unaccounted 갭 표시, `/Library/Updates` 추적 추가. (이번 사건이 갭 표시 한 줄로 설명됨)

## 사례 (2026-06-18, [[disk-monitor]])

- 36일 추세에서 전체기간 diff TOP 이 전부 `0.0→X`(`~/project` +29.7G 등) → 5/30 에 추가한 경로들의 측정 시작 아티팩트. 진짜 추세는 6/05 baseline 의 13일 diff (`~/project +10.5G`, codex 7.9G 주범).
- 순수 안전 회수분(Caches/var folders/ShipIt)은 합쳐 ~3~4G 뿐. `Containers`/`Group Containers` du 타임아웃 사각지대 지속 → 숨은 소모 가능성 보고에 명시.

## 변경 이력

- 2026-05-22: 최초 생성. disk_monitor 두 번째 운영 회고 기반 (출처: session-logs/20260522-234234-bc6e)
- 2026-06-03: macOS 업데이트 준비물 사각지대 추가. `du` 완전 실패 시 조용한 누락 함정 + 보완 3가지(측정 실패 가시화 `errors`, 진단 공식 코드 내장 `Unaccounted Δ`, `roots` 기록). 2026-06-03 사례(-16G = macOS Tahoe 26.5.1 업데이트 준비) (출처: session-logs/20260603-150720-5764)
- 2026-06-18: "config 변경 구간을 가로지르는 diff" 함정 추가 — 신규 추가 경로의 `0.0→X` 가짜 증가, baseline 은 모든 경로 갖춰진 이후로, `roots` 차이로 자동 판별. 2026-06-18 사례(36일 추세) (출처: session-logs/20260618-214720-d1fe)
