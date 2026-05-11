---
title: "shell 의 set -eu 와 multi-topic 파이프라인의 격리 패턴"
domain: both
sensitivity: public
tags: ["pattern", "shell", "bash", "set-eu", "error-handling", "pipeline", "isolation"]
created: 2026-05-12
updated: 2026-05-12
source_session: "20260511-230001-14d5-오늘-dev-blog-주제들이-5월11일-자로-업데이트-되지않았습니다.md"
sources:
  - "session-logs/20260511-230001-14d5-오늘-dev-blog-주제들이-5월11일-자로-업데이트-되지않았습니다.md"
confidence: high
related:
  - "wiki/bugs/ndjson-stdout-parser-greedy-regex.md"
  - "wiki/projects/dev-blog.md"
---

# shell 의 `set -eu` 와 multi-topic 파이프라인의 격리 패턴

`set -eu` 는 shell script 의 안전성 표준이지만, 여러 독립 작업을 순차 실행하는 멀티 토픽 파이프라인에서는 **한 토픽 실패가 후속 모든 토픽을 중단** 시키는 부작용이 있다. 의도와 정반대로 "한 번의 깨짐 = 그날 모든 작업 누락" 을 만든다. 토픽 별 격리는 `if !` 한 줄 wrap 으로 해결할 수 있다.

## 사례: dev-blog 5/11 사고

12개 토픽을 매일 cron 으로 발행하는 `daily-deploy.sh` 가 다음 구조였다:

```bash
#!/bin/bash
set -eu

npm run daily:linux:publish
npm run daily:android:publish
npm run daily:opensource:publish
npm run daily:opensource-curation:publish
npm run daily:lens:linux-kernel-security
# ... (총 12 토픽)

git add -A && git commit -m "..." && git push
```

`linux` 토픽의 cursor 어댑터가 NDJSON 파싱으로 깨지자 (→ [[ndjson-stdout-parser-greedy-regex]]), `set -e` 가 즉시 종료시켰고 **나머지 11개 토픽 + git push 까지 한 번에 누락**. 사용자 입장에서는 "사이트 전체가 어제 자에서 멈춤".

## 안티 패턴: 무방비 `set -eu` 의 직렬 호출

```bash
set -eu
cmd_topic_A   # 실패하면 cmd_topic_B 부터는 영원히 안 돈다
cmd_topic_B
cmd_topic_C
```

`set -e` 의 본래 의도는 **하나의 의존 사슬에서 중간 단계가 깨졌을 때 그 뒤를 무의미하게 진행하지 않는 것** (e.g. `mkdir build && cd build && cmake ..`). 독립적인 N개 작업을 도는 루프에서는 정반대 효과.

## 격리 패턴: `if !` 한 줄 wrap

```bash
set -eu

run_topic() {
  local topic="$1"
  if ! npm run "daily:$topic:publish"; then
    echo "[WARN] $topic failed, continuing"
    failed_topics+=("$topic")
    return 0   # 0 으로 swallow → set -e 가 안 잡음
  fi
}

failed_topics=()
for topic in linux android opensource opensource-curation \
             lens:linux-kernel-security lens:linux-toolchain; do
  run_topic "$topic"
done

# 모든 토픽이 시도되고, 실패 목록은 별도로 보고
if [ ${#failed_topics[@]} -gt 0 ]; then
  echo "Failed: ${failed_topics[*]}"
fi
```

핵심:

- `if !` 가 명령의 종료 코드를 자기 분기 안으로 흡수 → `set -e` 가 안 잡음
- 토픽별 실패는 로깅 + 누적 목록에만 기록, 다음 토픽 진행
- 마지막에 누적 목록을 출력하여 사용자가 무엇이 깨졌는지 한 눈에 확인

## 적용 기준

| 상황 | `set -e` 적용 |
|------|---------------|
| 의존 사슬 (A→B→C, B 실패 시 C 무의미) | 그대로 적용 |
| 독립 작업 N개 순차 실행 | 작업 단위로 `if !` wrap |
| 마지막에 git push / artifact 업로드 | 1개라도 성공했으면 push 할지의 정책 결정 필요 |

git push 정책 분기 예시:

```bash
if [ ${#failed_topics[@]} -lt ${#all_topics[@]} ]; then
  # 일부라도 성공했으면 push
  git add -A
  git commit -m "daily: $(date +%F) (failed: ${failed_topics[*]})"
  git push
else
  echo "All topics failed; skipping commit/push"
  exit 1
fi
```

## 함정: 함수 안의 `return 0` 누락

```bash
run_topic() {
  if ! npm run "$1"; then
    echo "failed"
    # ← return 누락
  fi
}
```

이 경우 `npm run` 실패가 `if !` 로 흡수되지만, **함수의 마지막 명령이 `echo`** (성공) 이라 함수 종료 코드는 0. 의도와 일치한다. 다만 `echo` 뒤에 다른 명령이 와서 그 명령이 실패하면 다시 `set -e` 가 발동. 안전하게 명시적 `return 0` 을 권장.

## subshell vs. block

- `if ! cmd; then ...; fi` — 같은 shell 안에서 실행, 환경 공유
- `( cmd ) || true` — subshell. 환경 변수 변경이 격리됨. 토픽이 cwd / 환경 변수를 변경한다면 subshell 이 더 안전

```bash
( npm run "daily:$topic:publish" ) || failed_topics+=("$topic")
```

## 일반 원칙: 작업 단위 결정의 중요성

`set -e` 의 효과를 활용하려면 "한 작업의 단위" 를 정확히 설정해야 한다.

- 의존 사슬이라면 → 사슬 전체가 한 작업, `set -e` 가 적합
- 독립 N건이라면 → 각 N 이 작업, 작업 경계에서 종료 코드 흡수

이 결정은 script 가 자라면서 자주 흐려진다. 처음엔 1개 토픽 → 점점 토픽이 추가 → 어느 시점부터 "독립 N건" 인데 `set -e` 가 1번째 토픽 실패에 모두 죽이는 구조로 변질. 정기적으로 "이 script 는 의존 사슬인가, 독립 N건인가?" 질문해야 한다.

## 관련 페이지

- [[ndjson-stdout-parser-greedy-regex]] — 이번 사고의 직접 원인이 된 파서 깨짐
- [[dev-blog]] — multi-topic 파이프라인의 대표 사례

## 변경 이력

- 2026-05-12: 최초 작성 (session-logs/20260511-230001-14d5-*.md). dev-blog 5/11 사고의 토픽 격리 패턴을 일반화
