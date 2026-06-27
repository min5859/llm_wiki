---
title: "Research / Write 에이전트 분리 — LLM 콘텐츠 파이프라인의 도구 기반 조사 격상"
domain: both
sensitivity: public
tags: ["llm", "pipeline", "agent", "research", "hallucination", "grounding", "dossier", "newsletter", "claude-cli"]
created: 2026-06-06
updated: 2026-06-28
sources:
  - "session-logs/20260606-151702-d243-지금-프로그램을-개선하고-싶은데-개선할만한-포인트를-찾아줘-관점은-블로그-내용의-질적인-향.md"
  - "session-logs/20260606-184227-1875-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260611-031305-4894-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md"
  - "session-logs/20260618-035229-521c-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260620-032907-1d4e-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260620-034402-92ed-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260620-034957-c486-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260628-030010-3806-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260628-030337-d36d-#-Linux-Daily-Newsletter-—-Write-from-Dossier-당신은.md"
  - "session-logs/20260628-032046-b0b8-#-Opensource-Trending-Newsletter-—-Write-from-Doss.md"
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

### dossier 스키마의 성숙 — 검증 자기보고 + 교차일 dedup (2026-06-28 실측)

폴백 사다리(봇 차단 시 confidence 강등·openQuestions 격리)가 산문 수준 약속이던 것이, 스키마 수준에서 **검증 상태를 정량적으로 자기보고**하도록 진화했다:

- **per-evidence `verified` (bool)** — 각 evidence 가 독립 도구로 교차검증됐는지를 evidence 단위로 표시. 후보 payload 의 인용을 1차 근거로만 쓴(독립 확인 못 한) evidence 는 `verified: false`.
- **dossier 레벨 `verifiedDowngradeCount`** — 그날 verified=false 로 강등된 evidence 수. dossier 1건의 "신뢰도 부채"를 한 숫자로 노출 → 운영자가 검증률을 추적 가능.
- **교차일(cross-day) dedup `seenBefore` / `seenBeforeCount`** — 어제 dossier 에도 있던 항목을 entry 단위로 표시. write 단계는 대응 grounding 룰("`seenBefore: true` 면 처음부터 재설명 말고 *무엇이 달라졌는지*만 한 줄, 변화 없으면 생략·강등 / `seenBefore` 없는 신규를 상단 우선")로 매일 같은 사실을 반복 노출하는 신호 밀도 저하를 억제. ([[llm-content-quality-guards]] 결함 1 토픽 간 중복의 *시간축* 판.)

핵심: dossier 계약이 "출처 URL 추적"(공간축)에 더해 "검증 상태 + 일자 간 신선도"(신뢰·시간축)까지 일급 필드로 격상했다. write 단계가 이 메타를 보고 톤(추정/생략/우선순위)을 조정한다.

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
  - **폴백 사다리 + 우아한 강등 (2026-06-20 실측)**: `lore.kernel.org` 와 `git.kernel.org` 가 **동시에** Anubis 로 막히는 날도 있다. 이때 관측된 단계적 폴백 사다리: ① raw 엔드포인트(`/raw` mbox)로 챌린지 우회 시도 → ② 실패 시 **`mail-archive.com` 미러** 등 대체 아카이브 → ③ 그래도 안 되면 **후보 페이로드에 이미 실려온 `commitMessage`/스레드 인용** 을 1차 근거로 + **WebSearch 교차검증**(CVE·LWN·회귀 보고) → ④ 끝내 독립 확인 불가한 사실은 **`confidence` 를 강등(high→medium/low)하고 해당 미확인 항목을 `openQuestions` 로 라우팅**. **핵심 불변식: 검증 못 한 것은 추측·날조 금지, 반드시 openQuestions 로 격리**(예: "Anubis 차단으로 스레드 전문/병합 여부 확인 불가"). 즉 봇 차단은 파이프라인을 *멈추는* 게 아니라 *신뢰도를 낮추되 끝까지 도는* 형태로 흡수된다.
  - **topic-id 자가판별 가드레일 (2026-06-20 실측)**: 렌즈(lens)별 dossier 에이전트는 프롬프트가 주장하는 topic 을 맹신하지 않고, 후보의 `sourceId`(예: `lore-bpf-new`)를 `grep -rl "<sourceId>" content/topics/*/sources.json` 으로 역추적해 **실제 topic 매핑(`linux-perf-rt` 등)을 스스로 재확인**한 뒤 조사를 시작한다. 프롬프트 주입/렌즈 라벨 오류로 *엉뚱한 토픽으로 실행되는* 사고를 데이터 기준으로 차단하는 self-grounding. (cf. [[prompt-schema-pipeline-coupling]] 의 표류 위험을 런타임에서 잡는 안전장치)
- **WebFetch 가 에러 페이지를 환각으로 변환** — GitHub repo `url` 을 WebFetch 로 열어 검증할 때, 404/삭제된 페이지도 HTTP 상태와 무관하게 본문이 채워진 마크다운으로 반환되면 LLM 이 존재하지 않는 repo 를 실재하는 것처럼 기술할 수 있다 (2026-06-11 Opensource Trending dossier 에서 에이전트 스스로 "WebFetch 가 GitHub 404 페이지에서 환각했을 가능성" 을 인지하고 신규 repo 교차검증을 시도). **일반 교훈**: "도구가 돌려준 텍스트 ≠ 검증된 사실". 최근 생성된 repo·진위 불확실한 신규 후보는 WebFetch 단독으로 확정하지 말고 WebSearch 교차검증 후에만 `confidence: high` 로 올린다. 봇 차단과 함께 *도구 신뢰성* 이라는 별도 레이어(grounding 계약과 다른 축)를 형성한다.
- **grounding 은 *추적 가능성*을 보장할 뿐 *정확성*을 보장하지 않는다 (2026-06-28 실측)** — 봇 차단으로 lore 원문을 못 연 날, research 에이전트가 후보 payload 의 메일 본문 인용을 그러모아 dossier 를 채우면서 **claim ↔ evidence.quote 가 불일치하는 entry** 가 만들어졌다(예: "Linux 7.1 mainline 릴리스" claim 아래에 RollBall deferred-probe revert·RCU 포인터 비교 quote 가 붙고, evidence 일부는 `verified: false`, url 은 자격증명형 `user:pass@` 가 마스킹돼 `https://***:***@gmail.com/` 로 들어옴). write 단계의 강제 룰("`quote` 있으면 반드시 `>` blockquote 로 노출")은 이 *불일치 quote 를 그대로 충실히 렌더*하므로, **상류 조사 오류가 grounding 가드를 통과해 가시적 근거인 척 전파**된다. 즉 URL-grounding(`findUngroundedUrls`)은 "dossier 밖 URL"만 막을 뿐 "quote 가 claim 을 실제로 뒷받침하는가"는 검사하지 않는다 — *claim↔quote 정합성 검사*는 URL-grounding 과 별개 축의 가드로 추가돼야 하고, write 프롬프트도 `verified: false` evidence 의 quote 는 억제하거나 "원문 미확인" 캡션을 붙여야 한다. (`verified` 플래그·`verifiedDowngradeCount` 가 *탐지*는 하지만 write 단계가 아직 *행동*으로 연결하지 않는 갭. cf. [[llm-content-quality-guards]] 의 hallucination 가드는 "도구가 반환한 텍스트 ≠ 검증된 사실"의 자매 명제.)
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
- 2026-06-28: **dossier 스키마 성숙**(per-evidence `verified` bool·`verifiedDowngradeCount`·교차일 dedup `seenBefore`/`seenBeforeCount` + write 단계 대응 grounding 룰)과 **grounding≠정확성 함정**(봇 차단 폴백 시 claim↔evidence.quote 불일치 entry 가 만들어지고 자격증명형 url 이 `https://***:***@gmail.com/` 로 마스킹된 채 들어옴 — 강제 blockquote 룰이 불일치 quote 를 충실히 전파, claim↔quote 정합성 검사는 URL-grounding 과 별개 가드로 필요) 2건 보강. 한편 Newsletter Write 단계 silent fail(`assistant_turns: 0`)은 6/28 에도 지속(약 2주 반째) — 운영 관찰은 [[dev-blog]]. (출처: session-logs/20260628-030010-3806-*, -030337-d36d-*, -032046-b0b8-* 외 03:00~04:08 dev-blog 사이클 22건)
- 2026-06-20: 봇 차단 함정에 **폴백 사다리 + 우아한 강등** 과 **topic-id grep 자가판별 가드레일** 2건 보강 — lore·git.kernel.org 동시 차단 시 raw 엔드포인트→mail-archive.com 미러→commitMessage+WebSearch 교차검증→끝내 미확인은 confidence 강등 + openQuestions 격리(추측·날조 금지)의 단계적 흡수가 03:00~04:00 Kernel Lens 사이클에서 반복 관측됨. 또한 dossier 에이전트가 `grep -rl <sourceId> content/topics/*/sources.json` 으로 실제 토픽 매핑을 스스로 재확인하고 조사를 시작하는 self-grounding 확인. (출처: session-logs/20260620-032907-1d4e-*, -034402-92ed-*, -034957-c486-* 외 03:00~04:00 Kernel Lens dossier 사이클)
