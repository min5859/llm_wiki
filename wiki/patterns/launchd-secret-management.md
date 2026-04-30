---
title: "macOS launchd 환경 시크릿 분리 패턴 — ~/.zshrc 는 안 읽힌다"
domain: both
sensitivity: public
tags: ["launchd", "macos", "secret-management", "zshrc", "github-pat", "fine-grained-pat", "env-file"]
created: 2026-04-30
updated: 2026-04-30
sources:
  - "session-logs/20260430-134759-328e-*.md"
confidence: high
related:
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
  - "wiki/projects/oss-radar.md"
---

# macOS launchd 환경 시크릿 분리 패턴

launchd 가 plist 의 `ProgramArguments` 를 직접 fork/exec 하기 때문에 **어떤 셸 프로파일도 거치지 않는다**. 이 사실을 모른 채 토큰을 `~/.zshrc` 에 두거나 plist 평문에 넣다가 사고가 반복된다. 표준 패턴은 **gitignore 된 `config/.env` (chmod 600) + `run.sh` 에서 source**.

## 왜 `~/.zshrc` 는 안 읽히는가

| 컴포넌트 | 동작 |
|---|---|
| 터미널이 zsh 를 띄울 때 | interactive shell → `~/.zshrc` 읽음 |
| **launchd 가 plist 실행** | 셸을 거치지 않고 `ProgramArguments` 의 `/bin/bash run.sh` 를 직접 fork/exec → 어떤 프로파일도 안 읽음 |
| `run.sh` 자체 | `#!/usr/bin/env bash`. bash 는 애초에 `~/.zshrc` 를 읽지 않는다 (zsh 전용 파일) |

거기에 더해 많은 `~/.zshrc` 가 맨 위에 `[[ -o interactive ]] || return` 가드를 두기 때문에 어떻게든 비대화 셸에서 source 해도 export 까지 도달 못 한다.

→ **launchd 환경에서 `~/.zshrc` 의 환경변수는 절대 보이지 않는다**. zshrc 에 토큰을 두면 터미널에선 동작하지만 자동화에선 안 된다.

## 안티 패턴 1: plist 평문에 토큰

```xml
<!-- ~/Library/LaunchAgents/com.example.app.plist (BAD) -->
<key>EnvironmentVariables</key>
<dict>
    <key>GITHUB_TOKEN</key>
    <string>ghp_xxxxxxxxxxxxx</string>
</dict>
```

문제:
- 백업·동기화 (Time Machine, iCloud Drive, Dropbox 등) 시 토큰 노출 경로 확장.
- launchctl print 결과로 평문 노출.
- 이 plist 를 git 으로 관리하려는 순간 `git log` 에 영구 박힘.
- 토큰 갱신 시 매번 XML 편집 + bootout/bootstrap 필요.

## 안티 패턴 2: `~/.zshrc` 에 export

```bash
# ~/.zshrc (BAD for launchd)
export GITHUB_TOKEN=ghp_xxxxxxxxxxxxx
```

문제:
- launchd 가 안 읽음 (위 설명).
- 터미널에서 띄우는 모든 자식 프로세스 (brew, npm, 기타 스크립트 등) 가 환경변수로 토큰을 보게 됨 → plist 보다 오히려 노출 표면이 넓다.
- 어떤 자동화에서는 보이고 어떤 자동화에서는 안 보이는 비결정적 동작.

## 표준 패턴: 전용 env 파일 + run.sh source

### 1. 디렉터리 생성 + env 파일 작성

프로젝트 트리 안 (gitignore 처리) 또는 `~/.config/<app>/` 에 둔다.

```bash
# 프로젝트 트리 방식
mkdir -p config
$EDITOR config/.env  # 내용: GITHUB_TOKEN=ghp_xxxxxxxxxxxxx
chmod 600 config/.env

# 또는 ~/.config/ 방식
mkdir -p ~/.config/<app>
chmod 700 ~/.config/<app>
$EDITOR ~/.config/<app>/env
chmod 600 ~/.config/<app>/env
```

### 2. `.gitignore` 점검

`.gitignore` 에 `.env` 한 줄이 있으면 모든 하위 디렉터리의 `.env` 파일을 매칭한다. `config/.env` 도 자동 커버.

```bash
# 추적 안 됨을 확인
git check-ignore -v config/.env
git ls-files --error-unmatch config/.env  # 에러 떨어지면 안전 (tracked 아님)
```

### 3. `run.sh` 첫머리에서 source

```bash
#!/usr/bin/env bash
SCRIPT_DIR="$(cd "$(dirname "$0")" && pwd)"

# Load secrets (GITHUB_TOKEN, etc.) from gitignored env file
if [ -f "$SCRIPT_DIR/config/.env" ]; then
    set -a
    source "$SCRIPT_DIR/config/.env"
    set +a
fi

# (이후 로직)
```

`set -a` / `set +a` 는 source 한 변수들을 자동 export 시키는 트릭 — env 파일에 일일이 `export` 를 쓰지 않아도 자식 프로세스에 전달된다.

### 4. plist 에서 토큰 줄 제거

```xml
<!-- ~/Library/LaunchAgents/com.example.app.plist (GOOD) -->
<key>EnvironmentVariables</key>
<dict>
    <key>PATH</key>
    <string>/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin</string>
</dict>
```

이로써 plist 는 비밀 없는 일반 설정 파일이 되어 git 관리도 가능해진다.

### 5. 재로드 + 즉시 검증

```bash
launchctl bootout gui/$(id -u)/com.example.app
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.example.app.plist
launchctl kickstart -k gui/$(id -u)/com.example.app
tail -f /path/to/logs/cron.log
```

## 토큰 권한 — Fine-grained PAT 권장

GitHub 토큰을 발급할 때:

| 옵션 | 권장도 | 비고 |
|---|---|---|
| **Fine-grained, Public Repositories (read-only)** | ⭐ 1순위 | write 권한 0. 본인 private repo 접근권 0. 5,000 req/h 인증 rate limit 그대로 받음. |
| Fine-grained, Selected repositories | 2순위 | 특정 private repo 접근 시 |
| Classic, scope 없음 | 3순위 | scope 0개로 발급하면 공개 read + rate limit 만 얻음 |
| Classic, `public_repo` 체크 | ❌ | 이름이 헷갈리지만 issue/PR/code 쓰기 포함 — 자동화엔 과한 권한 |

GitHub 자체도 fine-grained 를 권장하고 classic 은 "legacy" 로 표시. 새로 만드는 거면 fine-grained.

만료는 90일 권장 (1년 가능, 무기한 비권장).

## 노출이 일어났을 때

`launchd` 환경에서 토큰이 발견되면 다음 위치를 모두 점검:

```bash
echo "=== 1) 배포 plist ==="
grep -n "ghp_\|github_pat_" ~/Library/LaunchAgents/<app>.plist || echo "없음"

echo "=== 2) git-tracked plist ==="
grep -n "ghp_\|github_pat_" project/<app>/config/<app>.plist || echo "없음"

echo "=== 3) 프로젝트 트리 전체 ==="
grep -rn "ghp_\|github_pat_" project/<app>/ 2>/dev/null || echo "없음"

echo "=== 4) git history ==="
git -C project/<app> log --all -S "ghp_" --oneline | head -5

echo "=== 5) shell history ==="
grep -l "ghp_\|github_pat_" ~/.zsh_history ~/.bash_history 2>/dev/null
```

git history 에 들어 있으면 `git filter-repo` 등으로 재작성 후 force push 가 필요하지만, 보통 토큰 자체를 폐기하고 새로 발급하는 편이 빠르고 안전하다.

## 관련 맥락

- launchd 의 미실행 작업 catchup 동작 (Mac 이 꺼져 있던 동안의 스케줄을 깨어난 직후 폭주시키는 것) 은 [[macos-launchagent-catchup-behavior]] 에 정리.
- `oss-radar` 가 이 패턴으로 이전한 실제 사례는 [[oss-radar]] "GITHUB_TOKEN 보안 패턴" 절 참고.

## 변경 이력

- 2026-04-30: 최초 생성. oss-radar 의 401 토큰 만료 디버깅 + plist 평문 → config/.env 분리 사례 기반 (출처: session-logs/20260430-134759-328e-*)
