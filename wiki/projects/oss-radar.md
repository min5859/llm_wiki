---
title: "oss-radar — 주간 GitHub OSS 발굴 파이프라인"
domain: "personal"
sensitivity: "public"
tags: ["project", "github", "automation", "pipeline", "claude-cli", "launchd"]
created: "2026-04-28"
updated: "2026-05-19"
sources:
  - "session-logs/20260428-152446-9b5b-project-toy-oss-radar--프로젝트를-시작하려고합니다.-현재상태를-분석해주세.md"
  - "session-logs/20260428-153031-2553-project-toy-oss-radar--프로젝트의-phase-1부터-진행해-주세요.md"
  - "session-logs/20260428-231551-12d1-현재-프로젝트를-실행시키려면-어떻게-해야-하나요.md"
  - "session-logs/20260430-134759-328e-지금-이-프로그램을-매일-오전-9시에-돌아가도록-설정해-두었는데-동작-하지-않는-것-같습니.md"
  - "session-logs/20260517-090048-1b9a-Reply-with-only--OK.md"
  - "session-logs/20260517-090054-75ca-*.md"
  - "session-logs/20260517-090122-6f35-*.md"
  - "session-logs/20260517-090150-c808-*.md"
  - "session-logs/20260517-090223-159b-*.md"
  - "session-logs/20260517-090258-7b96-*.md"
  - "session-logs/20260518-090051-c52b-Reply-with-only--OK.md"
  - "session-logs/20260518-090057-153d-*.md"
  - "session-logs/20260518-090130-8d3b-*.md"
  - "session-logs/20260518-090158-f59e-*.md"
  - "session-logs/20260518-090228-3568-*.md"
  - "session-logs/20260518-090304-cd1c-*.md"
  - "session-logs/20260519-090053-4874-Reply-with-only--OK.md"
  - "session-logs/20260519-090100-8b6f-*.md"
  - "session-logs/20260519-090136-dc98-*.md"
  - "session-logs/20260519-090206-1b85-*.md"
  - "session-logs/20260519-090250-026d-*.md"
  - "session-logs/20260519-090316-89f0-*.md"
confidence: "high"
related:
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
  - "wiki/patterns/launchd-secret-management.md"
  - "wiki/analyses/github-search-api-topic-or-limitation.md"
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

### GITHUB_TOKEN 보안 패턴 (2026-04-30 갱신)

초기에는 `~/Library/LaunchAgents/com.wooki.oss-radar.plist` 의 `EnvironmentVariables` 에 토큰을 평문으로 넣었으나, 다음 두 이유로 **`config/.env` (gitignore + chmod 600) 분리 + `run.sh` 에서 source** 패턴으로 이전했다.

1. plist 평문은 백업·동기화 시 노출 경로가 넓다.
2. **launchd는 `~/.zshrc` 를 읽지 않는다.** plist 가 `/bin/bash run.sh` 를 직접 fork/exec 하므로 어떤 셸 프로파일도 거치지 않는다. → zshrc 에 둔 토큰이 cron/launchd 환경에선 보이지 않는다. 이 일반 패턴은 [[launchd-secret-management]] 에 정리.

현재 구성:

```bash
# run.sh 첫머리 (venv activate 직전)
SCRIPT_DIR="$(cd "$(dirname "$0")" && pwd)"
if [ -f "$SCRIPT_DIR/config/.env" ]; then
    set -a
    source "$SCRIPT_DIR/config/.env"
    set +a
fi
```

- `config/.env`: `GITHUB_TOKEN=...` 한 줄. `chmod 600`. `.gitignore` 의 `.env` 패턴이 자동 매칭.
- `~/Library/LaunchAgents/com.wooki.oss-radar.plist`: 토큰 줄 삭제, 일반 설정만 유지.
- 토큰 권한: Fine-grained PAT의 **Public Repositories (read-only)** 가 적합 (publish 는 SSH 키 사용, write 권한 불필요). Classic 을 쓸 경우 scope 를 0개로 두면 된다 (`public_repo` 는 issue/PR 쓰기 포함이라 헷갈리지만 비추천).

토큰 갱신은 `config/.env` 한 곳만 수정 → launchd `bootout → bootstrap → kickstart -k` 로 즉시 반영.

### GitHub Search API의 topic OR 미지원 (2026-04-30 발견)

`fetch_github_search` 의 카테고리 필터 (`topic:ai topic:developer-tools topic:productivity`) 가 의도와 다르게 동작했다. GitHub Repository Search API의 기본 연산자는 AND이고, **`topic:` 한정자에는 `OR` 연산자가 받아들여지지 않는다** (응답이 invalid query → `total_count` 없음).

수정: 카테고리별로 별도 쿼리를 던지고 `full_name` 으로 dedupe 후 병합. 효과: Search 후보 0 → 422 (ai 187, dev-tools 161, productivity 150). 일반 패턴은 [[github-search-api-topic-or-limitation]].

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
- 2026-04-30: GITHUB_TOKEN 만료(401) → fine-grained PAT 재발급 + plist에서 `config/.env` 로 시크릿 분리. GitHub Search API의 `topic:` OR 미지원 발견 → 카테고리별 개별 쿼리로 우회 (Search 후보 0 → 422). 출처: session-logs/20260430-134759-328e-*
- 2026-05-17: 09:00 cron 잡 전체 silent fail — `Reply-with-only--OK` 헬스체크 + 5건 OSS 레포 분석 (continuedev/cli, pinokiocomputer/program-pinokio, microsoft/awesome-copilot, OliveTin/OliveTin, photoprism/photoprism) 모두 `assistant_turns: 0`. claude CLI 가 시작은 했지만 모델 호출이 전부 무응답으로 끝남. 같은 시간대 dev-blog (07:00) / research-wiki (08:00) cron 도 광범위 silent fail 동시 발생 → 시스템 단 원인 의심 (특정 토픽·레포 결함이 아님). 산출물 없음, 코드 변경 없음, 운영 관찰만 기록. (출처: session-logs/20260517-090048-1b9a-* 외 5건)
- 2026-05-18: 09:00 cron 잡 재가동 — alive 핑 + 5건 OSS 레포 분석 (microsoft/ai-agents-for-beginners 등) prompt 가 모두 정상적으로 발사·기록됨. 5/17 의 광범위 silent fail 패턴은 사라짐 (시스템 단 원인이 1일 만에 해소). prompt 본문은 기존 「한줄 요약 / 주요 기능 / 사용 시나리오 / 기술 스택 / 주목 이유 / 실용성 평가 + 800~1200자」 템플릿 그대로 — 신규 룰 없음. **운영 관찰만, 코드 변경 없음** (출처: session-logs/20260518-090051-c52b-* 외 5건)
- 2026-05-19: 09:00 cron 정상 사이클 (2일 연속). alive 핑 (`assistant_turns: 1`) + 5건 OSS 레포 분석 prompt (`assistant_turns` 0~1 분포). 분석 prompt 본문은 5/18 과 동일 템플릿. 신규 룰 없음. **운영 관찰만, 코드 변경 없음** (출처: session-logs/20260519-090053-4874-* 외 5건)
