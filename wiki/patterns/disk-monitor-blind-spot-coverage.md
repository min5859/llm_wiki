---
title: "디스크 모니터링 사각지대 — 추적 경로 vs 실제 free delta 의 괴리"
domain: both
sensitivity: public
tags: ["pattern", "disk-monitoring", "macos", "du", "homedir-caches", "diagnosis"]
created: "2026-05-22"
updated: "2026-05-22"
sources:
  - "session-logs/20260522-234234-bc6e-disk-monitoring-내용을-분석해-주세요,.md"
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

## 안티패턴

- **사각지대 추정치를 단정형으로 보고하기** — "1주일간 `.npm` 이 7G 증가" 같은 단정은 잘못. 과거 측정이 없으므로 **현재 절대값** 만 알 수 있다. 사용자에게 보고할 때 명시.
- **자동 정리** — 사각지대를 발견했다고 스크립트가 자동으로 `cache clean` 호출하면 `~/.cache/chroma` (실데이터일 수 있음) 같은 항목을 망친다. 분류 + 컨펌 패턴 ([[macos-disk-cleanup-cache-classification]]) 유지.

## 사례 (2026-05-22, [[disk-monitor]])

- ΔFree = -18.4G, 추적 경로 내 Σ = -1.4G → 사각지대 ≈ 17G
- 보강 대상: `~/.npm` (7.2G), `~/.cache` (6.2G), `~/.nvm`, `~/.cargo`, `~/.rustup`
- 회수: `npm cache clean --force` 5.3G + `uv cache clean --force` 4.1G = 9.4G
- 코드 변경: `du` timeout fallback (`depth=0` 재시도), config 18 → 23 경로

## 변경 이력

- 2026-05-22: 최초 생성. disk_monitor 두 번째 운영 회고 기반 (출처: session-logs/20260522-234234-bc6e)
