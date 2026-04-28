---
title: "oss-radar — 주간 GitHub OSS 발굴 파이프라인"
domain: "personal"
sensitivity: "public"
tags: ["project", "github", "automation", "pipeline", "claude-cli", "launchd"]
created: "2026-04-28"
updated: "2026-04-29"
sources:
  - "session-logs/20260428-152446-9b5b-project-toy-oss-radar--프로젝트를-시작하려고합니다.-현재상태를-분석해주세.md"
  - "session-logs/20260428-153031-2553-project-toy-oss-radar--프로젝트의-phase-1부터-진행해-주세요.md"
  - "session-logs/20260428-231551-12d1-현재-프로젝트를-실행시키려면-어떻게-해야-하나요.md"
confidence: "high"
related:
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
---

# oss-radar — 주간 GitHub OSS 발굴 파이프라인

GitHub에서 매주 주목할 만한 오픈소스 프로젝트를 자동 발굴·분석하여 GitHub Wiki에 한국어 리포트로 발행하는 파이프라인. `research-wiki` 프로젝트(AI 논문 발굴)를 기반으로 OSS 레포 대상으로 재설계.

- **위치**: `~/project/toy/oss-radar/`
- **참조 프로젝트**: `~/project/toy/research-wiki/`
- **Phase 1~6 전체 구현 완료 (2026-04-28)**

## 파이프라인 아키텍처

```
discover.py → fetch.py → analyze.sh → publish.py
(발굴)        (수집)      (분석)        (발행)
```

### Phase별 구현

| Phase | 파일 | 역할 |
|-------|------|------|
| 1 | `config.yaml`, `requirements.txt`, `.gitignore`, `run.sh`, `prompts/analyze.md` | 기반 셋업 |
| 2 | `src/discover.py` | GitHub Search API + Trending 스크래핑으로 후보 레포 수집 |
| 3 | `src/fetch.py` | 레포 메타데이터 + README 수집, `data/repos.json` 업데이트 |
| 4 | `src/analyze.sh` | Claude CLI 호출로 한국어 분석 리포트 생성 |
| 5 | `src/publish.py` | 주간 Wiki 페이지 빌드 후 GitHub Wiki에 git push |
| 6 | `config/com.wooki.oss-radar.plist`, `README.md` | macOS launchd 자동화 + 문서화 |

## 핵심 설계 판단

### 스코어링 공식

```
score = star_velocity×0.5 + star_total_norm×0.3 + fork_norm×0.2
```

- `star_velocity`: 최근 7일 기준 스타 증가 속도 (가중치 0.5 — 가장 중요)
- `star_total_norm`: 전체 스타 수 정규화 (가중치 0.3)
- `fork_norm`: 포크 수 정규화 (가중치 0.2)

### 중복 방지

`data/history.json`으로 이미 수집한 레포를 추적하여 주간 실행 시 중복 발굴 방지.

### README 수집 한도

40,000자 초과 시 truncate. GitHub API README는 base64 인코딩으로 수신 후 디코딩.

### bkit footer 제거

Claude CLI 출력에 자동 삽입되는 bkit footer를 `sed`로 제거.

### 한국어 검증 + 재시도

`analyze.sh`에서 Claude 출력 결과가 한국어인지 검증 후 최대 2회 재시도. `prompts/analyze.md`에 "반드시 한국어로" 지시가 있어도 간헐적으로 영어 출력 발생.

### GitHub API 인증

| 인증 여부 | Rate Limit |
|----------|-----------|
| 미인증 | 60 req/h |
| `GITHUB_TOKEN` 환경변수 설정 | 5,000 req/h |

`oss-radar`는 GitHub Search API와 Trending을 사용하므로 `GITHUB_TOKEN`이 없으면 rate limit에 빠르게 도달. `research-wiki`는 HuggingFace/Semantic Scholar를 사용하므로 GITHUB_TOKEN 불필요.

### 중첩 Claude 세션 방지 패턴

```bash
env -u CLAUDECODE claude -p "$(cat prompt.txt)" < /dev/null
```

`CLAUDECODE` 환경변수를 unset하여 Claude CLI가 이미 Claude Code 내부에서 실행 중임을 인지하지 못하도록 방지. cron/launchd 환경에서 중첩 세션 충돌 없이 claude CLI를 호출할 때 사용.

## 자동화 설정

초기에 매주 월요일 09:00으로 설정됐으나, 2026-04-28 매일 실행으로 변경.

```xml
<!-- config/com.wooki.oss-radar.plist -->
<!-- 매일 09:00 KST (변경 후) -->
<key>StartCalendarInterval</key>
<dict>
    <key>Hour</key><integer>9</integer>
    <key>Minute</key><integer>0</integer>
</dict>
```

Wiki 페이지 명명 규칙: `YYYY-MM-DD-Weekly-OSS-Radar.md`

### GITHUB_TOKEN 보안 패턴

`GITHUB_TOKEN`을 plist에 하드코딩하면 git 히스토리에 영구 노출됨.

- **git에 올라가는 `config/com.wooki.oss-radar.plist`**: 토큰 없이 관리
- **실제 실행되는 `~/Library/LaunchAgents/com.wooki.oss-radar.plist`**: 설치 시 토큰 직접 추가

Mac 재설정·이전 시 LaunchAgents 파일에 토큰을 다시 수동으로 추가해야 한다.

## 초기 실행 절차

```bash
cd ~/project/toy/oss-radar
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
export GITHUB_TOKEN="..."
bash run.sh
```

launchd 등록은 `README.md` 참고.

## 중복 방지 로직과 한계

`data/history.json`으로 이미 선정된 레포를 추적하여 재선정 방지.

```python
candidates = [r for r in merged.values() if r["full_name"] not in history]
```

**한계 3가지:**

1. **최신성 보장 안 됨**: GitHub Search는 `stars >= 100` 조건만 봄. `public-apis`, `free-programming-books` 같은 수년 된 레포도 Trending에 오르면 선정 가능.
2. **GitHub Trending 의존도 높음**: GitHub Search 쿼리가 결과를 너무 좁혀 0개가 나올 때 Trending 전체를 대체 소스로 사용. 2026-04-28 첫 실행 시 Search 0개, Trending 13개에서 5개 선정.
3. **lookback_days 미적용**: `pushed_at >= 7일 전` 조건이 GitHub Search에만 적용됨. Trending 스크래핑에서는 `pushed_at`을 수집하지 않아 필터링 불가.

## 버그 수정 이력

### 2026-04-28: analyze.sh venv python3 우선 사용

**증상**: `analyze.sh` 실행 시 `ModuleNotFoundError: No module named 'yaml'`.

**원인**: 스크립트가 시스템 전역 `python3`를 사용했는데, `yaml`(PyYAML) 패키지는 `.venv`에만 설치됨.

**수정 내용**:
```bash
# .venv/bin/python3를 먼저 탐색, 없으면 시스템 python3 fallback
PYTHON3="$ROOT/.venv/bin/python3"
if [ ! -f "$PYTHON3" ]; then
    PYTHON3="python3"
fi
```

모든 `python3` 참조를 `$PYTHON3`로 교체. `export PATH="$HOME/.local/bin:$PATH"` 선언 위치도 파일 상단으로 이동 (cron 환경에서 claude CLI PATH 확보 목적).

## 변경 이력

- 2026-04-28: 최초 생성 — Phase 1~6 전체 구현 완료 기록
- 2026-04-28: 자동화 스케줄 매주 월요일 → 매일 09:00 변경, GITHUB_TOKEN 보안 패턴, analyze.sh venv 버그 수정, 중복 방지 한계 3가지 추가
