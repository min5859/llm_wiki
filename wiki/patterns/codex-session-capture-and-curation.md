---
title: "Claude 훅을 유지하면서 Codex 세션 수집·위키 정리를 연결하기"
domain: "ai-agent"
sensitivity: "internal"
tags: ["codex", "hooks", "gieok", "ingest", "launchd", "idempotency"]
created: "2026-10-10"
updated: "2026-10-11"
sources:
  - "session-logs/codex-01a125b5-9d9a-7392-96fa-f91459bb4130.md"
  - "session-logs/codex-01a125c8-3e17-7de2-9855-9b2eaacd74fc.md"
  - "local-repo: gieok/hooks/codex-session-logger.mjs"
  - "local-repo: gieok/scripts/lib/llm.sh"
  - "https://learn.chatgpt.com/docs/hooks"
  - "https://learn.chatgpt.com/docs/non-interactive-mode"
confidence: "high"
related:
  - "wiki/projects/gieok.md"
  - "wiki/concepts/gieok.md"
  - "wiki/analyses/openai-codex-cli-overview.md"
---

# Claude 훅을 유지하면서 Codex 세션 수집·위키 정리를 연결하기

세션을 쓰는 에이전트와 위키를 정리하는 에이전트는 별개다. `claude -p`만 Codex로 교체해도 Codex 대화가 수집되는 것은 아니다. Claude 수집을 보존하고 Codex 수집기를 별도로 연결한다.

## 수집

Codex 전역 `~/.codex/hooks.json`에 SessionStart, UserPromptSubmit, Stop, PostToolUse, SessionEnd, Interrupt를 등록했다. 설치된 정의를 `/hooks`에서 신뢰 등록하고 실제 질문·응답 저장을 확인했다. 기존 Claude 설정의 SHA256은 설치 전·후 일치했다.

로거는 외부 네트워크·모델 호출 없이 대화와 제한된 도구 기록을 마스킹해 `session-logs/codex-<id>.md`로 저장한다. 개발자 지침과 reasoning 레코드는 수집하지 않는다. 프롬프트와 마지막 응답 훅 필드는 미플러시 원본의 보완 자료로 사용한다.

동일 세션이 재개되며 여러 rollout 파일로 저장될 수 있다. 세션 ID만 보고 마지막 파일로 덮어쓰면 과거 기록이 사라지므로 원본 경로들을 합치고 겹친 이벤트를 제거한다. 세션별 잠금과 atomic rename으로 동시 수집을 보호한다. 내용이 동일하면 처리 플래그를 보존하고 새 내용이 생기면 `ingested: false`로 되돌린다.

## 정리와 예약 실행

- LaunchAgent의 `GIEOK_LLM=codex`로 ingest·lint를 선택한다. 공통 스크립트의 기본값은 Claude로 유지해 다른 기존 설치를 바꾸지 않았다.
- 예약 PATH에 실제 nvm Node·Codex 디렉터리를 넣었다. 터미널의 `which` 결과만으로 예약 실행 환경을 검증하지 않는다.
- 정리용 Codex는 `--ephemeral`과 `GIEOK_NO_LOG=1`로 재귀 수집을 막고, 저장된 ChatGPT 인증을 사용한다.
- ingest는 대상 배치를 제한하고 대화 읽기용 로컬 뷰를 제공한다. 원본 로그는 유지한다. 처리 중 소스가 바뀌면 완료 플래그를 되돌려 새 내용을 다시 검토한다.
- lint는 read-only로 읽고 최종 Markdown만 반환한다. 셸이 형식을 검증한 뒤 리포트를 교체하므로 실패하면 이전 리포트가 남는다.

## 처리 완료와 원문 변경의 구분

2026-10-11 재검토에서는 완료 표시만 바뀐 로그를 새 대화로 오인하는 감사 체크섬 문제를 바로잡았다. 비교 시 frontmatter의 `ingested` 값만 정규화하고, 대화 본문에 들어 있는 같은 문자열 예시는 그대로 보존해야 한다. 파일 전체에 무조건 문자열 치환을 적용하면 본문까지 달라져 잘못된 변경 판정이 생길 수 있다.

검토 시점과 완료 표시 직전의 내용을 대조해 실제 새 대화가 추가된 경우에는 다시 검토한다. 완료 플래그만 달라진 경우와 새 대화가 추가된 경우를 각각 검증한다. 이는 앞서 구현한 배치 변경 감지와 별개로, 수동 감사에서도 동일하게 지켜야 할 기준이다.

## 운영 점검에서 확인한 사실

마지막 Claude 자동 ingest 성공은 8월 3일이었고 이후 인증 만료로 실패했다. 10월 10일 Claude 로그 104건은 Codex CLI로 검토·스킵 처리했다. 다음 Codex 기록 처리에서 CLI 사용량 제한이 발생하여 남은 기록은 현재 Codex 대화에서 검토했다. 성공한 인증·한 번의 ingest와 사용량 한도는 구분해서 진단한다. 한도 회복 뒤 10월 11일 실제 Codex lint를 read-only로 실행해 리포트 생성과 exit 0을 확인했다.

## 변경 이력

- 2026-10-11: 완료 플래그와 본문 변경을 구분하는 감사 체크섬 기준 보강. 전환 설계·재개 파일 병합은 기수록으로 중복 생성을 생략 (출처: session-logs/codex-01a125b5-9d9a-7392-96fa-f91459bb4130.md).

- 2026-10-10: 구독 전환으로 수집이 끊긴 실제 사례와 설치·회귀 검증 결과를 기록. 원본 포맷은 안정된 공개 인터페이스가 아니므로 훅 이벤트와 가져오기 회귀 테스트를 함께 유지한다.
- 2026-10-11: 사용량 한도 회복 후 실제 read-only lint 리포트 생성·정상 종료 검증.
