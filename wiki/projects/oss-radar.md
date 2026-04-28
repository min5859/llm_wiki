---
title: "oss-radar — 주간 GitHub OSS 발굴 파이프라인"
domain: "personal"
sensitivity: "public"
tags: ["project", "github", "automation", "pipeline", "claude-cli", "launchd"]
created: "2026-04-28"
updated: "2026-04-28"
sources:
  - "session-logs/20260428-152446-9b5b-project-toy-oss-radar--프로젝트를-시작하려고합니다.-현재상태를-분석해주세.md"
  - "session-logs/20260428-153031-2553-project-toy-oss-radar--프로젝트의-phase-1부터-진행해-주세요.md"
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

```xml
<!-- config/com.wooki.oss-radar.plist -->
<!-- 매주 월요일 09:00 KST -->
<key>StartCalendarInterval</key>
<dict>
    <key>Weekday</key><integer>1</integer>
    <key>Hour</key><integer>9</integer>
    <key>Minute</key><integer>0</integer>
</dict>
```

Wiki 페이지 명명 규칙: `YYYY-MM-DD-Weekly-OSS-Radar.md`

## 초기 실행 절차

```bash
cd ~/project/toy/oss-radar
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
export GITHUB_TOKEN="..."
bash run.sh
```

launchd 등록은 `README.md` 참고.

## 변경 이력

- 2026-04-28: 최초 생성 — Phase 1~6 전체 구현 완료 기록
