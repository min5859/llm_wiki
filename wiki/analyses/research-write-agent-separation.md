---
title: "Research / Write 에이전트 분리 — LLM 콘텐츠 파이프라인의 도구 기반 조사 격상"
domain: both
sensitivity: public
tags: ["llm", "pipeline", "agent", "research", "hallucination", "grounding", "dossier", "newsletter", "claude-cli"]
created: 2026-06-06
updated: 2026-06-18
sources:
  - "session-logs/20260606-151702-d243-지금-프로그램을-개선하고-싶은데-개선할만한-포인트를-찾아줘-관점은-블로그-내용의-질적인-향.md"
  - "session-logs/20260606-184227-1875-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260611-031305-4894-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md"
  - "session-logs/20260618-035229-521c-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
confidence: high
related:
  - "wiki/analyses/llm-newsletter-rewrite-metadata-grounding.md"
  - "wiki/analyses/llm-content-quality-guards.md"
  - "wiki/analyses/llm-json-parse-retry-with-dump.md"
  - "wiki/patterns/prompt-schema-pipeline-coupling.md"
  - "wiki/projects/dev-blog.md"
---

# Research / Write 에이전트 분리 — LLM 콘텐츠 파이프라인의 도구 기반 조사 격상

LLM 으로 글을 자동 생성하는 파이프라인에서 "조사(research)" 단계와 "작문(write)" 단계를 분리하는 패턴. 핵심은 **단계를 둘로 쪼개는 것 자체가 아니라, 조사 단계에 능동적 도구 접근(WebFetch/WebSearch/git log)을 주어 입력 재료의 깊이 천장을 깨는 것**이다. 두 에이전트는 *dossier*(근거가 붙은 사실 묶음) 라는 계약으로 연결되며, 이 계약이 hallucination 가드를 구조적으로 강화한다. dev-blog 의 Linux Daily 뉴스레터 파이프라인 PoC 에서 일반화.

## 핵심 통찰: 분리의 가치는 "도구"에 있다

`collect → draft → rewrite(AI) → publish` 처럼 AI 변환이 한 단계뿐인 파이프라인의 품질 천장은 **AI 가 손에 쥔 재료의 깊이**다. dev-blog 의 경우 AI 가 본 재료는 "메일 앞 700자 excerpt + commit message + 릴리스 메타"가 전부였다. rewrite 단계는 도구가 없는 단일 변환(`claude -p --output-format text`)이라 떠먹여진 것만 요약·재배치할 뿐, "이 패치가 무엇을 되돌리는가, 관련 CVE 가 있는가"를 능동적으로 더 파지 못한다.

> 따라서 research/write 분리에서 진짜 레버는 **"결정론적(regex 고정) fetch" 를 "추론 기반 조사(도구 사용)" 로 격상**하는 것이다. 단계만 쪼개고 research 에 도구를 주지 않으면 700자 천장이 그대로라 분리 효과가 반감된다.

이 구분이 중요한 이유: 분리 작업의 대부분은 **배관(plumbing)** — 단계를 나누고 계약(dossier)을 정의하고 출처 강제 안전장치를 까는 것 — 이고, 이 배관만으로는 입력 깊이가 그대로라 내용 품질이 거의 변하지 않는다. 품질 리프트는 오직 **도구 조사를 켰을 때** 발생한다.

## Dossier 계약 — 출처 강제로 hallucination 을 구조적으로 차단

두 에이전트의 인터페이스는 `research-latest.json`(dossier)이다. 설계의 핵심 불변식:

- **본문에 들어갈 모든 claim 은 evidence URL 로 추적된다.** validator 가 http(s) 가 아닌 url, evidence 없는 entry 를 거부한다.
- write 에이전트는 **dossier 만** 입력으로 받는다. grounding 근거를 dossier 로 넘기면 품질 가드(`findUngroundedUrls`)가 "dossier 밖 URL 금지"를 자동 강제한다 → write 가 근거 없는 사실을 쓰면 게시 전에 차단된다.
- `impactType` 등 enum 은 기존 스키마(`highlight-schema`)의 값을 재사용해 단일화한다.

즉 "조사/작문 분리"가 단순한 구조 정리가 아니라, **출처 추적 가능성을 계약으로 못 박아 hallucination 가드를 강화**하는 장치가 된다. (관련: [[llm-content-quality-guards]] 의 hallucination 가드)

## 결정론적 fallback — 도구 없는 어댑터도 끝까지 돈다

research 단계를 도구 조사로만 구현하면 도구 권한이 없는 환경/어댑터에서 파이프라인이 멈춘다. 해결:

- `AI_ADAPTER=claude` → read-only 도구(WebFetch/WebSearch/Bash(git log))로 능동 조사
- `AI_ADAPTER=template|codex` → draft 결과만으로 **schema-valid dossier** 를 결정론적으로 생성 → 파이프라인이 end-to-end 로 돈다
- 도구 조사는 어댑터별 **opt-in** (PoC 에서는 claude 만). codex/cursor 도 웹 fetch 도구가 있으므로 어댑터에 같은 분기만 추가하면 원리상 동작한다.

이렇게 하면 "구조 전환"과 "품질 리프트"를 분리해서 검증할 수 있다 — 먼저 fallback 으로 배관이 끝까지 도는지 확인하고, 그 다음 도구 경로로 실제 품질을 측정한다.

## 측정된 before/after (template 기계 dossier vs claude 도구 조사)

| | template(기계) | claude(도구 조사) |
|---|---|---|
| 항목 수 | 8 (전부 통과) | 4 (국부 패치 2건 스스로 제외 판단) |
| evidence | 8건 (record URL 뿐) | 13건 |
| evidence 종류 | commit/changelog/thread | article(LWN) 5, changelog 3, thread 3, cve 2 |

claude 는 collect 에 **없던 LWN 기사 5건·CVE 2건을 능동적으로 찾아 읽고**, "501개 파일·6371줄 수정", "Greg KH 가 전 사용자 업그레이드 권고" 같은 구체 근거를 모았다. 최종 글 출처에 기존 파이프라인엔 0건이던 2차 소스(LWN)가 들어가고 longterm 항목에 CVE 번호가 붙었다 — 700자 천장을 실제로 돌파한 증거. 비용: 항목당 fetch + 2패스라 토큰·시간 증가(후보 4개 제한으로 관리), 외부 fetch 는 비결정적(약 7분 소요 관측).

## 구현 함정

- **validator 가 긴 인용문을 거부** — claude 가 7분 조사 끝에 만든 dossier 가 evidence.quote 232자(>200자 제한)로 마지막 저장 직전에 막혔다. `normalizeDossier` 로 초과분을 잘라 해결하고, **`RESEARCH_RAW_PATH` 로 직전 어댑터 stdout 을 재호출 없이 재파싱**하는 복구 경로를 추가(7분 조사 결과 보존). LLM 출력은 스키마 제약을 종종 어기므로, 검증기는 *거부* 만 하지 말고 *정규화 후 통과* 경로를 함께 둬야 비싼 조사 결과를 버리지 않는다.
- **봇 차단(Anubis)** — research 에이전트가 `git.kernel.org` 에 직접 fetch 하다 Anubis 봇 챌린지에 막혔다. read-only 도구를 WebFetch 단독이 아니라 **WebFetch + WebSearch + Bash(git log)** 조합으로 줘야 봇 차단 시 대체 소스(LWN 등)로 우회할 수 있다.
  - **User-Agent 우회 (2026-06-18 실측)**: Anubis 는 *브라우저류 UA* 에만 챌린지를 건다. WebFetch 는 완전 차단되고 `curl` 의 브라우저 UA(`Mozilla/5.0`)·기본 UA(`curl/8.4`)도 `403 Forbidden`. 그러나 **`curl -A "git/2.39.0"` (비-브라우저 UA) 는 `lore.kernel.org/<thread>/raw` mbox 를 정상 취득**한다(`git.kernel.org` commit 도 같은 UA 로 `<title>` 확인 가능). `lei/0.9`·`Wget` 등 일부 UA 는 호스트별로 갈린다. **일반 교훈: "도구 차단 ≠ 소스 접근 불가"** — WebFetch 가 막히면 `Bash(curl) + 비-브라우저 UA` 가 read-only 조사의 4번째 폴백 축이 된다.
- **WebFetch 가 에러 페이지를 환각으로 변환** — GitHub repo `url` 을 WebFetch 로 열어 검증할 때, 404/삭제된 페이지도 HTTP 상태와 무관하게 본문이 채워진 마크다운으로 반환되면 LLM 이 존재하지 않는 repo 를 실재하는 것처럼 기술할 수 있다 (2026-06-11 Opensource Trending dossier 에서 에이전트 스스로 "WebFetch 가 GitHub 404 페이지에서 환각했을 가능성" 을 인지하고 신규 repo 교차검증을 시도). **일반 교훈**: "도구가 돌려준 텍스트 ≠ 검증된 사실". 최근 생성된 repo·진위 불확실한 신규 후보는 WebFetch 단독으로 확정하지 말고 WebSearch 교차검증 후에만 `confidence: high` 로 올린다. 봇 차단과 함께 *도구 신뢰성* 이라는 별도 레이어(grounding 계약과 다른 축)를 형성한다.
- **"배관 완료"를 "품질 완료"로 오인 금지** — fallback·codex 경로로 파이프라인이 도는 것을 확인해도 입력이 여전히 draft 의 700자라 내용 품질은 거의 그대로다. 실제 리프트는 도구 조사 경로(`research:linux:claude`)에서만 나온다. PoC 가 도구 경로 실측을 빠뜨리면 작업 효과를 측정하지 못한 셈.

## 관련 맥락

- [[llm-newsletter-rewrite-metadata-grounding]] — write 단계가 dossier/메타를 본문에 그라운딩하는 룰
- [[llm-content-quality-guards]] — `findUngroundedUrls` 등 게시 전 품질 가드(defense-in-depth)
- [[llm-json-parse-retry-with-dump]] — 어댑터 JSON 출력 파싱 실패의 재시도+덤프 패턴 (dossier 파싱에도 동일 적용)
- [[prompt-schema-pipeline-coupling]] — 프롬프트 스키마 변경이 validator/publisher 와 비동기로 표류하는 결합 위험
- [[dev-blog]] — 이 패턴이 구현된 프로젝트 (Step 1~4 commits da60cec~9f2ccad)

## 변경 이력

- 2026-06-06: 최초 생성 — dev-blog Linux Daily 파이프라인의 research/write 분리 PoC 에서 일반화 (출처: session-logs/20260606-151702-d243-*, 20260606-184227-1875-*)
- 2026-06-11: 구현 함정에 *WebFetch 404 환각* 항목 1건 추가 — dev-blog 03:00 cron 사이클(6/10 에 이어 이틀째)의 Opensource Trending dossier 에서 에이전트가 GitHub 404 페이지의 환각 가능성을 자기 인지하고 교차검증한 사례에서 일반화 (도구가 반환한 텍스트 ≠ 검증된 사실). 한편 Newsletter Write 단계는 6/10·6/11 연속 전량 `assistant_turns: 0` 으로 silent fail 고착 — 운영 관찰은 [[dev-blog]] 에 기록, 파이프라인 구조·grounding 룰은 기존 기술과 동일해 본 페이지 본문 변경은 함정 1건뿐 (출처: session-logs/20260611-031305-4894-* 외 03:00 사이클 23건)
- 2026-06-18: 봇 차단(Anubis) 함정에 **User-Agent 우회 실측** 보강 — `curl -A "git/2.39.0"` 비-브라우저 UA 로 `lore.kernel.org/<thread>/raw` mbox 취득(WebFetch·브라우저 UA·기본 UA 는 전부 403). "도구 차단 ≠ 소스 접근 불가, Bash(curl)+비브라우저 UA 가 4번째 폴백 축". Newsletter Write 단계 `assistant_turns: 0` silent fail 은 6/18 에도 지속·확대(write 단계만 0, dossier 단계는 정상) — 운영 관찰은 [[dev-blog]]. 본문 변경은 UA 우회 1건 (출처: session-logs/20260618-035229-521c-* 외 03:00~04:10 dev-blog 사이클 20건)
