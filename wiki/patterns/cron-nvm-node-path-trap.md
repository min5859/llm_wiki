---
title: "cron 에서 NVM 의 node 를 못 찾는 함정 (env: node: No such file or directory)"
domain: both
sensitivity: public
tags: ["cron", "macos", "linux", "nvm", "node", "path", "automation"]
created: 2026-05-08
updated: 2026-05-08
sources:
  - "session-logs/20260507-225855-4c7c-현재-프로그램을-분석해-주세요.md"
confidence: high
related:
  - "wiki/patterns/launchd-secret-management.md"
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
  - "wiki/projects/dev-blog.md"
---

# cron 에서 NVM 의 node 를 못 찾는 함정

cron 이 `~/.zshrc` 를 읽지 않기 때문에, NVM 으로 설치한 node 는 cron 환경에서 PATH 에 없다. 결과는 `env: node: No such file or directory` 로 즉시 실패. [[launchd-secret-management]] 와 같은 *비대화 셸 환경* 함정 패밀리.

## 증상

```
env: node: No such file or directory
```

`crontab -l` 에 라인은 정상 등록, 터미널에서 같은 명령은 잘 동작.

## 원인

cron 은 *최소한의 환경* 에서 도는 셸 (`PATH=/usr/bin:/bin` 수준) 을 기동한다. NVM 으로 설치한 node 는 `~/.nvm/versions/node/v24.14.0/bin/` 같은 비표준 경로에 있고, NVM 의 PATH 주입은 *interactive shell 의 `~/.zshrc`/`~/.bashrc` 에서만* 일어난다. cron 은 어떤 셸 프로파일도 거치지 않으므로 `node` 를 못 찾는다.

`npm` 자체는 절대경로로 호출했더라도, `npm` 이 내부적으로 `#!/usr/bin/env node` 셰뱅 등을 통해 `node` 를 PATH 에서 다시 찾으면 같은 에러로 빠진다. **npm 만 절대경로화** 하는 부분 수정은 효과 없음.

## 안티 패턴

```cron
# BAD — npm 절대경로만 잡고 PATH 누락
0 7 * * * cd /path/to/repo && /Users/wooki/.nvm/versions/node/v24.14.0/bin/npm run daily \
  >> logs/cron.log 2>&1
```

## 표준 패턴: crontab 상단에 `PATH=` 명시

```cron
PATH=/Users/wooki/.local/bin:/Users/wooki/.nvm/versions/node/v24.14.0/bin:/usr/local/bin:/usr/bin:/bin
CLAUDE_BIN=/Users/wooki/.local/bin/claude
DAILY_REWRITE_ADAPTER=template

0 7 * * * cd /path/to/repo && npm run daily >> logs/cron.log 2>&1
```

핵심:

- `PATH` 에 NVM node 디렉터리를 *맨 앞 또는 명시적으로* 포함
- 외부 CLI (`claude`, `gh`, `python` 등) 도 PATH 의존이면 동일하게 디렉터리 추가
- 환경변수는 crontab 파일 상단에 두면 모든 라인에 적용. 한 라인에만 필요하면 `KEY=val cmd` 인라인.

## 검증 방법

cron 에 진단 라인 한 번 추가:

```cron
* * * * * env > /tmp/cron-env.txt; which node >> /tmp/cron-env.txt 2>&1; which npm >> /tmp/cron-env.txt 2>&1
```

→ 실제 cron 환경의 PATH/which 확인 후 진단 라인 제거.

## 관련 패턴 비교

| 환경 | 셸 프로파일 (`~/.zshrc`) | 해결 |
|---|---|---|
| **cron (macOS/Linux)** | 안 읽음 | crontab 상단에 `PATH=` |
| **launchd plist** | 안 읽음 | `EnvironmentVariables` 또는 wrapper `run.sh` 에서 PATH export. 시크릿은 [[launchd-secret-management]] 참조 |
| **GitHub Actions** | 안 읽음 | `actions/setup-node` 가 PATH 주입 |
| **systemd unit** | 안 읽음 | `Environment=` 또는 `EnvironmentFile=` |
| **터미널 interactive** | 읽음 | 평소엔 잘 됨 → 이 격차가 모든 함정의 원인 |

## NVM 사용자 대안 — node 를 시스템 위치로 symlink

`/usr/local/bin/node` → `~/.nvm/versions/node/<v>/bin/node` symlink 를 두면 PATH 추가 없이도 cron 에서 동작. 단 NVM 으로 node 버전 갱신할 때마다 symlink 재설정 필요. crontab 상단 `PATH=` 가 더 직관적이고 verbose 하지 않음.

## 함정

- **macOS 의 zsh 가 비-interactive 에서도 `~/.zprofile` 은 읽음** — 단, cron 은 `/bin/sh` 를 기본 셸로 호출하므로 zprofile 도 안 읽힘. cron 의 `SHELL=/bin/zsh` 지정 + `~/.zshenv` 사용은 가능하나 권장하지 않음 (의존성 늘어남).
- **brew 로 설치한 node** 는 `/opt/homebrew/bin/node` 에 있어 cron 의 default PATH 에는 여전히 없음 (brew 가 별도 디렉터리). 동일하게 PATH 추가 필요.
- **동일 패턴이 ruby (`rbenv`/`rvm`), python (`pyenv`), go 멀티버전, java (`jenv`) 모두에 재현** — 버전 매니저는 본질적으로 interactive shell hook 에 의존.

## 변경 이력

- 2026-05-08: 최초 작성. dev-blog cron 분석 중 발견 (출처: session-logs/20260507-225855-4c7c-*)
