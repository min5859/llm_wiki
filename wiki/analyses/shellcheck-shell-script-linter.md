---
title: ShellCheck — Bash/POSIX 셸 스크립트 정적 분석기
domain: personal
sensitivity: public
tags: [analysis, oss, shell, bash, linter, static-analysis, haskell]
created: 2026-05-17
updated: 2026-05-17
source_session: 20260517-090223-159b-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md
sources:
  - "session-logs/20260517-090223-159b-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md"
confidence: high
related:
  - "wiki/analyses/ssh-cli-toolkit-essentials.md"
---

# ShellCheck — Bash/POSIX 셸 스크립트 정적 분석기

## 개요

- **레포**: https://github.com/koalaman/shellcheck
- **공식 사이트**: https://www.shellcheck.net (브라우저에서 즉시 검사 가능)
- **저작자**: Vidar 'koala_man' Holen
- **라이선스**: GNU GPLv3 (기여물 동일 라이선스)
- **언어**: Haskell

bash / sh / dash / ksh 스크립트의 흔한 버그·이식성 문제·스타일 결함을 정적 분석으로 잡아 주는 사실상 표준 셸 린터.

## 검출 카테고리 (대표)

### 1. Quoting

- `$@` unquoted → word splitting
- 작은따옴표 닫는 `'` 의 escape 시도
- 작은따옴표 내 `$PATH` 등 변수 미확장 의도 vs 오타 구분

### 2. Conditionals

- `[[ n != 0 ]]` 같은 상수 테스트
- `[[ -e *.mpg ]]` glob 존재 검사 함정
- `[[ $foo==0 ]]` 공백 누락으로 always-true
- `[[ -n "$foo " ]]` 리터럴 포함 always-true
- `[ $1 -eq "shellcheck" ]` 숫자-문자열 혼동

### 3. 자주 오용되는 명령

- `find . -exec foo {} && bar {} \;` 의 조기 종료
- `sudo echo 'Var=42' > /etc/profile` (sudo 가 echo 만 받고 redirect 는 부모 셸)
- `time --format=%s sleep 10` (built-in vs `/usr/bin/time` 의 플래그 차이)
- `while read h; do ssh "$h" uptime` (ssh 가 stdin 을 먹어 loop 종료)

### 4. 초보자 실수

- `var = 42` (공백)
- `$foo=42` (할당부에 `$`)
- `else if` (셸은 `elif`)
- `if ( -f file )` (test 가 아니라 서브셸)

### 5. 견고성

- `rm -rf "$STEAMROOT/"*` — 빈 변수 시 catastrophic rm
- `find . -exec sh -c 'a && b {}' \;` — find -exec shell injection
- `export MYVAR=$(cmd)` — exit code 마스킹

### 6. 이식성 (`#!/bin/sh` 시)

- `echo {1..10}` (bash/ksh 만)
- `cmd &> file` (bash 만)
- `local var=value` (sh 미지원)
- `[ $UID = 0 ]` (dash/sh 미정의)

### 7. 기타

- `PATH="$PATH:~/bin"` (literal tilde)
- `rm "file"` 의 유니코드 따옴표
- `echo hello \` 뒤 trailing 공백 (line continuation 깨짐)
- `var=42 echo $var` (inlined env 의 확장 시점)

## 사용 시나리오

- CI 의 셸 스크립트 quality gate (`shellcheck **/*.sh`)
- 새 스크립트 작성 시 IDE 통합 실시간 경고 (vscode-shellcheck, vim ale 등)
- 레거시 sh 스크립트의 이식성 audit
- 셸 학습자의 self-tutor (각 경고에 wiki 페이지 + SC 코드)

## 통합·확장

- **IDE**: VS Code (timonwong.shellcheck), Vim ALE/CoC, Emacs flycheck
- **CI**: GitHub Actions (ludeeus/action-shellcheck), GitLab CI
- **packaging**: 거의 모든 패키지 매니저 (`brew install shellcheck`, `apt install shellcheck`, `cargo` 미지원)
- **무시 메커니즘**: `# shellcheck disable=SCNNNN` 주석, 환경변수, 파일·전역
- **이슈별 long form**: wiki 의 [Checks](https://github.com/koalaman/shellcheck/wiki/Checks) 페이지 (예: SC2221)

## 평가

- **성숙도**: 2012년부터 유지보수, 사실상 표준
- **GPLv3**: 기여 시 라이선스 유지 의무
- **보완 도구**: 포맷팅은 별도 — [shfmt](https://github.com/mvdan/sh) (Go) 와 함께 쓰는 게 일반적

## 한계

- Haskell 로 작성 → 동적 컴파일·임베딩 어려움 (CLI 호출이 표준)
- DSL 분석 한계 (eval / source 동적 평가는 추적 못 함)
- 셸 변형 (zsh, fish) 미지원

## 관련

- 「shellcheck is awesome 하지만 결국 왜 아직 bash 를 쓰고 있는가」 라는 유명한 testimonial — 셸 스크립트의 본질적 위험성을 단적으로 표현
- 보조 도구: [[ssh-cli-toolkit-essentials]] (셸 자동화 환경의 CLI 도구 묶음)

## 변경 이력

- 2026-05-17: oss-radar 자동 분석 로그 기반 신규 작성 (cron 분석 미생성 상태에서 README 직접 요약)
