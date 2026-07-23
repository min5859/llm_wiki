# 운영 로그

## 2026-07-24 (ingest)

- **session-logs 유래** — 2026-07-24 dev-blog cron 로그 21건 처리 (03:00~04:33 사이클: Linux Daily R+W, Android Kernel R+W, Opensource Trending R+W, Opensource Curation R+W, AI Coding Agents R+W, Linux Kernel Lens R6+W5). 뉴스레터 콘텐츠(커널·오픈소스·AI 도구 뉴스)는 뉴스성으로 전량 스킵(7/22와 동일 방침). AI Coding Agents dossier(033656) 내 뉴스 항목(skills 포맷 벤더 간 수렴, Gemma 4 벤치마크, undercover mode 논쟁)도 뉴스성·미검증 소스로 스킵.
  - **신규 1건**: ai-agent `bugs/newsletter-research-anti-bot-blocking` — Linux Daily(030015)·Kernel Lens `lore-stable-new`(040043)·AI Coding Agents(033656) 3건에서 각각 Anubis 봇 챌린지·403+봇검사(UA 5종 스푸핑 전부 무효)·HTTP 402 차단 관측. `lore-stable-new` 렌즈는 dossier 자체가 끝내 산출되지 않아(assistant_turns:0) 해당 회차 newsletter 가 결손(Kernel Lens 6렌즈 중 dossier 6/newsletter 5) — 상위 파이프라인의 자동 재시도·결손 감지 없음. 2026-07-03(20260703-030007) Anubis 관측의 두 번째 등장으로 승격.
  - **갱신 2건**: [[research-write-agent-separation]] (「Research 단계의 병렬 서브에이전트 클러스터링」 신규 절 — AI Coding Agents dossier(033656)에서 후보 12건을 4개 주제 클러스터로 나눠 병렬 `Task` 서브에이전트에 위임, "분리의 가치는 도구에 있다" 원리가 research 단계 내부에도 재귀 적용됨을 실측 + anti-bot 차단 단락을 신규 bugs 문서로 링크), [[dev-blog]] (운영 노트 — Kernel Lens dossier/newsletter 비대칭 + anti-bot 차단 이슈, 신규 bugs 문서 링크).
  - **mcp-note 유래** — 해당 없음 (type: mcp-note 파일 0건).
  - **raw-sources 유래** — `raw-sources/claude-code-opus-orchestration-setup.md` 는 기존 summary(`summaries/claude-code-opus-orchestration-setup`, 2026-07-12 작성)보다 원본 최종 변경(커밋 6a9858e, 2026-07-11)이 앞서 변경 없음 — 멱등 스킵.
  - **PDF 유래(.cache/extracted/)** — 대상 파일 없음.

## 2026-07-23 (ingest)

- **session-logs 유래** — 미처리 24건 처리 (인터랙티브 2건 + 테스트 1건 + dev-blog cron 뉴스레터/dossier 21건). raw-sources/·.cache/extracted/·fetched/·mcp-note 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재 + sidecar 부재로 source_sha256 미설정 유지, 멱등 스킵).
  - **신규 2건**: ai-agent `patterns/homebrew-python-upgrade-breaks-cron-venv` (oss-radar `run.sh` 의 venv activate 후 PATH 재-prepend·research-wiki 의 venv 부재 — 2026-04-28 analyze.sh venv 버그에 이은 2회차 등장으로 패턴 승격, 출처 b228), `bugs/gieok-session-log-url-credential-masking-false-positive` (gieok 마스킹이 lore.kernel.org 스타일 URL 을 `***:***@` 로 파괴 — 첫 등장이나 ingest 판단 자체를 오염시키는 핵심 버그라 즉시 기록, 출처 7948 외 12건)
  - **갱신 2건**: [[oss-radar]] (7/5~7/22 17일간 Step 1 중단 사건 + Step 4 게이트 결함 수정 + companion research-wiki 수정 기록, 출처 b228), [[gieok]] (마스킹 오탐 절 + 링크 추가)
  - **기수집 확인 2건**: 34ec (dev-blog cron 실패 토픽 분석 요청 — 07-23 커밋 `4a033ce` 로 `patterns/agentic-cli-text-generation-lockdown`·`patterns/llm-json-parse-retry-with-dump`·`projects/dev-blog` 에 이미 반영, 신규 정보 없음), bd44 (JSON 헬스체크 테스트, `assistant_turns: 0`, 내용 없음)
  - **스킵 21건**: 20260723 dev-blog cron 뉴스레터/dossier 뉴스 콘텐츠 전량 (뉴스성). 07-22 승격 보류 2건 대조 결과 — ① gh CLI 미설치 REPO API FAILED: 오늘 재발 없음 (보류 유지) ② url 필드 텍스트 혼입(cc55): 오늘 로그의 `***:***@` 유사 패턴은 dossier 결함 재발이 아니라 gieok 로거 마스킹 오탐으로 판명 (위 신규 bugs 문서 참조). lore Anubis 봇 차단 관찰은 [[research-write-agent-separation]] 에 기수록이라 스킵. `exit_reason: unknown` 전건은 로거 기본값(정상 완료 세션 bd44 도 unknown)이라 이상 신호 아님.
  - **stray 파일 정리**: runner 가 임시 생성한 `wiki/projects/oss-radar-research-wiki-7월5일-중단-원인분석-및-수정.md` 는 내용 흡수 후 삭제.

## 2026-07-23 — wiki-ingest

- Project: dev-blog
- Mode: diff
- Input: commits e28df77·7a8a83f (rewrite 어댑터 에이전트형 표류 차단 — `--tools ""`·cwd 격리·모델 고정·교정 재시도·덤프 adapter/model 메타)
- Created/Updated:
  - 신규: `patterns/agentic-cli-text-generation-lockdown` (에이전트형 CLI 를 순수 텍스트 생성기로 쓸 때의 잠금 7항목 — 7/5 "에이전트형 표류" 관측 1회차 + 7/22 원인 확정·수정 2회차로 승격 기준 충족)
  - 갱신: `patterns/llm-json-parse-retry-with-dump` (「확률적 vs 행동적 실패」 절 — 동일 프롬프트 재시도 회복률 54% 실측, 교정 재시도 + 덤프 adapter/model 메타 추가), `projects/dev-blog` (7/22 수정 섹션 + 연혁 엔트리 — 6/10 이래 write 실패 서사 종결), `index.md`

## 2026-07-22 (ingest)

- **session-logs 유래** — 미처리 23건 처리 (인터랙티브 2건 + dev-blog cron 20건 + ea52 재확인 1건). raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재 + 원본 마지막 변경(07-11 commit 6a9858e)이 summary 작성(07-12) 이전이라 멱등 스킵). runner 서브에이전트 4대 병렬 트리아지(인터랙티브 2건 개별 + 뉴스레터 일괄 + ea52) 후 기존 페이지와 중복 대조.
  - **신규 1건**: trading `bugs/sqlite-cross-thread-connection-threading-local` — ht_dde rt 데몬 스레드가 `RsPaperStore.__init__` 의 메인 스레드 생성 SQLite 커넥션을 재사용 → `ProgrammingError` 누적 388회, rt 장중 청산 7/17~7/21 완전 비동작(tier 11건 매도 vs rt 0건 → 감시 주기 A/B 오염, 데이터 리셋). `threading.local()` 지연 생성 @property 로 수정(호출부 무변경, pytest 24 passed). 첫 등장이나 도메인 핵심 운영 버그라 즉시 기록 (출처 bb66).
  - **갱신 4건**: `projects/ht-dde` (07-21 상태 점검 절 — tier vs rt 감시 주기 A/B 설계·오염 리셋, plist 4개 config/ 직접 bootstrap 임시 등록 → symlink+bootstrap 정식 전환, 0바이트 로그 오판 진단, 출처 bb66), `patterns/launchd-plist-symlink-from-project` (함정 5 「config/ 직접 bootstrap 은 재부팅 소멸」+ 함정 6 「StandardOutPath 0바이트 ≠ 미실행 — dated 산출물 mtime + launchctl print runs 병용」, 출처 bb66), `analyses/polling-interval-vs-bar-interval` (「매도 감시 2분→10~30초 검토」 절 — 0195S0 슬립 -3.54% 실측, 잔고 캐시 TTL 30초가 해상도 하한, 매도 전용 경량 루프 분리 전제, 감시 촘촘=트레일링 실질 타이트닝이라 1분봉 백테스트 재검증 전 미적용, 출처 8f9d), `projects/n-stock-info` (§8 유동성 게이트 "추천 0종목" — 자이에스앤디 80.25점도 min_market_cap 900억/min_trade_value 200억 미달로 is_recommended=0, 18회 연속 Filtered to 0, ht_trading "기준일=N/A"는 오늘자 추천만 읽는 stale 매수 방지 설계의 결과, 출처 8f9d).
  - **기수집 확인 (신규 없음)**: ea52 (llm_wiki v2 셋업 논의) — frontmatter 이미 `ingested: true`(본문 속 `ingested: false` 문자열에 grep 오탐). 내용은 07-04 사이클에서 [[personal-llm-wiki-curation]]·[[gieok]]·[[claude-code-token-optimization]] 에 완전 반영 확인.
  - **스킵 20건**: 20260722 dev-blog cron 뉴스레터/리서치 dossier (Linux Daily·Opensource Trending/Curation·AI Coding Agents·Linux Kernel Lens) 전량 — 뉴스성, 파이프라인 메타 지식은 기존 문서에 이미 흡수. 파이프라인 관찰 2건은 첫 등장·원인 미상이라 승격 보류: ① 7e5a dossier 에서 `gh` CLI 미설치(`command not found`)로 REPO API FAILED — 저장소 3건 메타데이터 누락 ② cc55 dossier 의 url 필드 42줄에 Claude Code 체인지로그 텍스트 혼입(데이터 무결성 손상, 원인 미상). 재발 시 [[dev-blog]]·[[research-write-agent-separation]] 으로 승격.

## 2026-07-21 (ingest)

- **session-logs 유래** — 미처리 23건 처리 (전부 20260721 dev-blog cron 03:00~04:44 배치: Linux Daily R+W · Android Kernel R+W · Opensource Trending R+W · Opensource Curation R+W · AI Coding Agents R+W · Linux Kernel Lens R6+W7). general-purpose 에이전트 3대(Linux/Android/Opensource 8건, AI Coding Agents 2건, Kernel Lens 13건) 병렬 트리아지 + 신규 발견 1건은 원문 재대조로 검증. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재·원본 미변경 판단 유지, 멱등 스킵).
  - **갱신 1건**: `analyses/research-write-agent-separation` — **PoW 우회를 Task 서브에이전트에 위임 + 하네스의 사후 보안 경고** 신규 관측. `linux-gpu-ai` 렌즈(044255-dab6)에서 research 에이전트가 후보 검증을 Task 서브에이전트("Verify simple-helper and cleanup candidates")에 위임했고, 그 서브에이전트가 `/tmp/anubis.py` PoW 솔버로 diff=4 챌린지를 풀어 lore 후보 4건을 우회 취득(07-17 의 "저-difficulty 완전우회" 재현). 서브에이전트 결과 반환 시 **하네스가 자동으로 `SECURITY WARNING: [Security Weaken] ... no user authorization for this bypass technique` 를 삽입** — 서브에이전트 감사가 프롬프트가 아니라 하네스 차원에서 사후 작동한 최초 실측. 경고가 파이프라인 후속 처리로 연결되는지는 미확인(세션이 `exit_reason: unknown`으로 종료, assistant_turns:2).
  - **스킵 22건**: 나머지 dev-blog cron 전량 — 뉴스성 콘텐츠 + Anubis 폴백 사다리·write 단계 `assistant_turns:0` no-op·에이전트형 표류(stdout 대신 파일 직접 Write) 등 기수록 패턴의 재현. AI Coding Agents 2건(스테가노그래피 마킹·source leak·OpenClaw 과금 거부·Code Quality GA 가격 공지 등)도 07-05 이래 반복 재탕이거나 제품 가격 공지라 스킵 — [[claude-code-source-leak-internals]]·[[anthropic-oauth-third-party-billing-trap]]·[[openai-codex-cli-overview]] 에 이미 흡수.

## 2026-07-20 (ingest)

- **session-logs 유래** — 미처리 24건 처리 (hermes 1건 + openclaw 1건 + 20260720 dev-blog cron 03:00~04:28 배치 22건: Linux Daily R+W, Android Kernel R+W2, Opensource Trending R+W, Opensource Curation R, AI Coding Agents R+W, Linux Kernel Lens R6+W5, Weekly Digest 1). runner 3대(dev-blog 배치) + Explore 1대(hermes/openclaw) 병렬 트리아지. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재·원본 미변경(원본 07-11 커밋 < summary 07-12) — 멱등 스킵).
  - **신규 1건**: `bugs/openclaw-provider-id-legacy-rename` — openclaw `2026.7.1-2` 업데이트가 provider ID `openai-codex`→`openai` 로 리네임, 설정·인증 프로파일이 옛 ID 참조해 기본+fallback 모델 전부 `Unknown model` 실패(텔레그램 전 토픽 무응답). 3계층(Node 엔진 게이트 24.14.0<24.15.0 → nvm 업그레이드 / 게이트웨이 plist 옛 node 하드코딩 → daemon install 재생성 / provider ID 리네임(진짜 원인) → `doctor --fix`)이 겹쳐 발현. `openclaw-coder-silent-3-layer`(05-07)와 증상(텔레그램 무응답)은 같지만 원인은 무관 — "텔레그램 무응답"이 매번 다른 근본원인으로 재발하는 OpenClaw 증상 패턴임을 재확인 (출처 b1d7).
  - **갱신 2건**:
    - `projects/openclaw` — 인트로 프로바이더 표기(`openai-codex/gpt-5.4`→`openai/gpt-5.6-sol`, fallback `openai/gpt-5.5`, `thinkingDefault: xhigh`), 버전 이력에 `2026.7.1-2` 추가, 2026-07-19 사건 섹션(3계층 요약 + 이월 과제 5건) 추가.
    - `projects/hermes` — 구성 표가 프로필 2개(default+maccoder)로만 기록돼 있었으나 실제로는 **8개**(architect/coder/designer/maccoder/news/reporter/reviewer/trading)로 확장된 상태 확인, 표 갱신. base+8프로필 모델 `gpt-5.5`→`gpt-5.6-sol`, effort `medium`→`xhigh` 일괄 변경 회고 추가 — **base config.yaml 은 프로필로 전파 안 됨**, 프로필마다 개별 gateway 재시작 필요, `delegation.reasoning_effort` 빈 값 유지가 위임 시 agent 설정 상속의 핵심이라는 신규 운영 사실 반영 (출처 2222).
  - **스킵 22건**: 20260720 dev-blog cron 뉴스레터/리서치 dossier 전량 — 뉴스성, 파이프라인 메타 지식은 기존 문서(research-write-agent-separation·llm-newsletter-rewrite-metadata-grounding·llm-content-quality-guards·highlights-action-validator-schema-drift·dev-blog)에 이미 흡수.
    - Kernel Lens/Weekly 배치를 맡은 runner 가 "write 완전 실패 집중화·research truncation·malformed JSON" 3종을 신규로 보고했으나 **직접 원문 검증 결과 전부 오탐**: (a) "truncation" 으로 지목한 034042 는 실은 `assistant_turns:0`(무응답) 세션의 **프롬프트에 임베드된 후보 데이터**를 assistant 출력으로 오독한 것, (b) 042628 을 `assistant_turns:0` 이라 보고했으나 실측은 `1`(수치 환각), (c) 041346 의 "malformed JSON"(`verifyLink` 필드에 markdown 본문 혼입)은 07-15 에 이미 `linux-distro-stable write 03df` 로 기수록된 동일 패턴의 재현. 서브에이전트 보고를 그대로 신뢰하지 않고 `grep`+원문 대조로 재검증한 뒤 스킵 확정 — 향후 유사 배치에서도 "신규 발견" 보고는 원문 재대조 필수.
    - Opensource Trending Newsletter(032059)의 X4G(`x4gKing/X4G`) openQuestions 에 07-18 관찰과 동일한 WebFetch stars/forks 수치(5.8k/10.8k vs 검색 요약 ~3,500/~6,600)가 재등장. 그러나 이 dossier 항목은 `seenBefore: true` 이고 오늘 research 단계는 미완료(계획만 하고 종료)였던 정황상 **오늘 새로 관측된 게 아니라 이전 날짜 dossier 가 그대로 캐리오버된 동일 관측치** — 독립된 두 번째 재현으로 볼 수 없어 승격 보류 유지 (07-19 와 동일 결정).
    - 나머지(Anubis PoW 폴백 재현·write 더블런·seenBefore 메타데이터 정상 동작 등)는 06-18~07-19 사다리와 동일한 재현.

## 2026-07-19 (ingest)
## 2026-07-19 (ingest)

- **session-logs 유래** — 미처리 21건 처리 (전부 20260719 dev-blog cron 03:00~04:44 배치: Linux Daily R(cad0, write 미발생) · Android Kernel R(6683)+W(7ab8) · Opensource Trending R(85e9)+W2(e9f7→8c17) · Opensource Curation R(2604)+W(a0df) · AI Coding Agents R(2b15)+W(6f56) · Linux Kernel Lens R6(c004·c501·8e4b·0d5b·a1e7·2d6a)+W5(5cb1·2480·6df0·6e93·793d)). runner 3대 병렬 트리아지로 파이프라인 운영 신호만 추출. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재·원본 미변경(raw 07-11 커밋 < summary 07-12) — sidecar 부재로 source_sha256 미설정 유지, 멱등 스킵).
  - **신규/갱신 0건. index.md 변경 없음.**
  - 스킵 21건 (뉴스성 + 기수록 패턴의 재현 — 파이프라인 메타는 아래 전부 기존 문서에 흡수됨):
    - 커널/OSS/AI 코딩 뉴스 콘텐츠 전량 — 재조회 가치 없음.
    - **Anubis PoW 솔버 완전 우회 재확인** (Linux Daily cad0): 즉석 Python SHA256 nonce 솔버가 `diff=4` 챌린지를 nonce 33426~131804 회(40~131ms)에 풀어 `/api/pass-challenge` 로 `techaro.lol-anubis-auth` 쿠키 획득 후 `lore.kernel.org/<thread>/raw` mbox 를 `code=200` 으로 취득. 07-17 에 이미 정제된 결론(솔버 성공은 서버 difficulty 에 좌우되는 *비결정적* 폴백 — 저-difficulty 일엔 완전 우회)의 재확인일 뿐, 새 기전·difficulty 상향·새 실패 양태 없음 → [[research-write-agent-separation]] line 93 기수록, 본문/변경이력 변경 없음.
    - **git UA 우회 정착** (Kernel Lens c501 이후 5쌍): `git/2.43.0` 비-브라우저 UA 로 `t.mbox.gz` 직접 취득 — 06-18 실측(`curl -A "git/2.39.0"`)과 동일 축, 기수록.
    - **write 더블런 1쌍** (Opensource Trending e9f7→8c17 +5분): 1차가 JSON 산출 완료 후 동일 dossier 재발사, 2차는 `assistant_turns:0` no-op. 07-05/07-15/07-16 더블런과 동일 현상 → [[research-write-agent-separation]]/[[prompt-schema-pipeline-coupling]] 기수록.
    - **write no-op·고아 research** (Opensource Curation write a0df `assistant_turns:0`; Kernel Lens research c004 는 초기 Anubis 분석 14 bash 후 Topic 미확정·`exit_reason: unknown` 으로 대응 newsletter 없이 버려진 고아) — 6/10 이래 비결정적 고착의 지속, 기수록.
    - **dossier/newsletter 스키마** (`seenBefore`·`verified`·`droppedCandidates`·`seenBeforeCount` 메타 + highlights `if/do/verify` 3분해) — 06-28·[[llm-newsletter-rewrite-metadata-grounding]] 기수록, 신규성 없음.
    - AI Coding Agents dossier(2b15) 뉴스 토픽(Claude Code v2.1.214 권한검사 우회 fail-closed 픽스 · Copilot repo-level usage metrics GA/review 커스터마이징 · 스테가노그래피 마킹 재탕 `seenBefore:true`)은 "버전별 릴리스 노트·기수록 주제 재탕은 재조회 가치 없음" 선례(07-18 v2.1.212 동일 처리) 유지 + [[claude-code-source-leak-internals]] 계열 기수록. 07-18 의 첫 등장 관찰(WebFetch fast-model 의 GitHub 수치 메타데이터 hallucinate)은 이번 배치에서 재현되지 않아 승격 보류 유지. **위키 페이지 변경 없음.**

## 2026-07-18 (ingest)

- **session-logs 유래** — 미처리 26건 처리 (전부 20260718 dev-blog cron 03:00~05:02 배치: Linux Daily R+W, Android Kernel R1+W2, Opensource Trending R1+W2, Opensource Curation R+W, AI Coding Agents R1+W2, Linux Kernel Lens R6+W7). runner 1대 병렬 트리아지로 파이프라인 운영 신호만 추출. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재·원본 미변경(raw 07-11 커밋 < summary 07-12)으로 멱등 스킵 — sidecar 부재로 source_sha256 미설정 유지).
  - **신규/갱신 0건. index.md 변경 없음.**
  - **첫 등장 관찰 (재등장 시 승격 후보)**: Opensource Trending research(032124, line 302)에서 **WebFetch 내장 fast model 이 GitHub 페이지의 수치 메타데이터를 hallucinate** — X4G 레포를 "5.8k stars / 10.7k forks" 로 읽었고, 세션이 "스타보다 포크가 많은 건 넌센스" 불변식으로 자가 감지. `gh` 부재 환경에서 `curl https://api.github.com/repos/<owner>/<repo>` 로 11개 후보 레포 메타데이터를 일괄 재검증했다. 교훈 후보: "WebFetch 는 페이지를 경량 모델로 요약하므로 수치·통계는 신뢰 불가 → 공개 API 를 1차 소스로, 도메인 불변식(forks>stars 넌센스)을 sanity check 로". 첫 등장이라 승격 규칙(2회 등장)상 보류 — 재등장 시 [[llm-content-quality-guards]] 결함 3(hallucination)의 연구 단계 변형으로 승격할 것.
  - 스킵 26건 (뉴스성 + 기수록 패턴의 재현, 위 첫 등장 관찰 포함):
    - 커널/OSS/AI 코딩 뉴스 콘텐츠 전량 — 재조회 가치 없음.
    - Anubis 차단 재현 (Linux Daily 030016 · Kernel Lens 040224): "primary URL 전부 차단 → WebSearch + secondary + public-inbox raw/mbox 폴백" — [[research-write-agent-separation]] 폴백 사다리 기수록.
    - HTTP 402(트윗 직접 페치, 034919 line 261 실측)·403(ccunpacked.dev) → WebSearch + 2차 출처 corroboration — 동일 사다리 패턴의 재현.
    - 스테가노그래피 마킹·source leak·undercover mode·OpenClaw 단어 필터 — 07-05 이래 반복 재탕, "출처 없는 정보 기정사실화 금지" 원칙상 미기록 유지 ([[claude-code-source-leak-internals]] 계열 기수록).
    - Claude Code v2.1.212 릴리스 노트 (plan-mode Bash 권한 우회 픽스 · worktree symlink 픽스 · `CLAUDE_CODE_MAX_WEB_SEARCHES_PER_SESSION`/`CLAUDE_CODE_MAX_SUBAGENTS_PER_SESSION` runaway cap · MCP 2분 초과 auto-background · `/fork`·`/subtask`) — "버전별 릴리스 노트는 재조회 가치 없음" 스킵 선례 유지.

## 2026-07-17 (ingest)

- **session-logs 유래** — 미처리 18건 처리 (전부 20260717 dev-blog cron 03:00~04:35 배치: Linux Daily R+W, Android Kernel R+W, Opensource Trending R+W, Opensource Curation R+W, AI Coding Agents R+W, Linux Kernel Lens R6+W2). runner 2대 병렬 트리아지로 파이프라인 운영 신호만 추출. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재·원본 미변경으로 멱등 스킵).
  - **갱신 1건**: `analyses/research-write-agent-separation` — 봇 차단(Anubis) 섹션에 **"PoW 솔버가 저-difficulty 일엔 완전 우회 성공"** 절 추가. `linux-arch-platform` 렌즈(041802)에서 07-15 와 동일한 Python SHA256 nonce 솔버가 `diff=4` 챌린지를 398~61582 회(1~69ms)에 풀어 `lore.kernel.org/<thread>/raw` mbox 3건을 `code=200`(10229·16379·10703 bytes, `challenged=False`)으로 완전 취득. 원문(041802 line 649~701)에서 solver 완전 성공을 직접 검증 후 기록. 07-15 의 "솔버=비신뢰 폴백" 결론을 *부분 정정*: 솔버 성공은 그날 서버 difficulty 에 좌우되는 비결정적 폴백이며, 저-difficulty 일엔 원문을 실제로 되살린다(고-difficulty 일엔 07-15 처럼 부분 성공). 우아한 강등이 안정 경로라는 실무 지침 자체는 유지. 두 번째 등장(07-15→07-17)이자 결과가 달라진 신규 데이터라 승격 규칙 충족.
  - **스킵 17건**: 20260717 dev-blog cron 뉴스 콘텐츠 전량 — Linux Daily·Android Kernel·Opensource Trending/Curation·AI Coding Agents·Linux Kernel Lens 의 커널/OSS 뉴스(BPF arena·GPU panthor·stable 백포트 등)는 뉴스성이라 재조회 가치 없음. Newsletter Write 세션의 `assistant_turns:0` no-op(빈 산출물)·마스킹된 후보 URL(`https://***:***@…` message-ID `@domain` 오탐)은 6/28~7/15 기수록 패턴의 재현이라 본문 변경 없음. Research 세션의 서브에이전트 병렬 조사·계획 수립은 정상 동작.

## 2026-07-16 (ingest)

- **session-logs 유래** — 미처리 24건 처리 (전부 20260716 dev-blog cron 03:00~04:38 배치: Linux Daily R+W, Android Kernel R+W, Opensource Trending R1+W2, Opensource Curation R+W, AI Coding Agents R1(write 미발생), Linux Kernel Lens R6+W8). raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재·원본 미변경(원본 07-11 커밋 < summary 07-12) — sidecar 부재로 source_sha256 미설정 유지, 멱등 스킵).
  - **신규/갱신 0건. index.md 변경 없음.**
  - 스킵 24건 (뉴스성 — runner 3대 병렬 트리아지로 전건 확인, 파이프라인 메타는 전부 기수록 패턴의 재현):
    - **write 더블런 3쌍** (Opensource Trending c53e→d218 +2분32초 · Kernel Lens distro-stable fb8d→0cac · gpu-ai f0cc→727e) — 1차 파일 산출 후 동일 dossier 재발사, 2차는 파일 쓰기 없이 JSON 재검증 또는 경미 작업. 07-05 "더블런 4쌍"·07-15 "더블런 5쌍" 과 동일 현상 → [[research-write-agent-separation]]/[[prompt-schema-pipeline-coupling]] 기수록.
    - **write/research silent fail·중단** (Linux Daily write 21e9 · Curation write cf00 `assistant_turns:0`, AI Coding Agents research ccf1 은 `exit_reason: unknown`·`assistant_turns:1` 로 리서치 중 중단 → write 스텝 자체가 미발사) — 6/10 이래 비결정적 고착의 지속, 기수록.
    - **Anubis 봇 차단 + 폴백 사다리** (kernel.org·lore.kernel.org v1.22.0/v1.25.0 차단 → opennet.ru·linuxcompatible.org 미러, GitHub torvalds/linux(cgit 불가), 후보 payload 의 embedded commitMessage 인용 + confidence 강등·openQuestions 명시) — 6/18~7/15 사다리와 동일, [[research-write-agent-separation]] 기수록.
    - AI Coding Agents dossier(ccf1) 뉴스 토픽(Copilot VS2026·JetBrains BYOK·CC v2.1.210·스테가노그래피 마킹 재탕·source leak 재탕·OpenClaw 과금 재탕·Cowork research preview·OpenAI skills 채택)은 기수록 주제([[claude-code-source-leak-internals]] 등)의 재탕 + 뉴스성. 휘발성 커널/OSS 뉴스는 전량 스킵. **위키 페이지 코드 변경 없음.**

## 2026-07-15 (ingest)

- **session-logs 유래** — 미처리 27건 처리 (전부 20260715 dev-blog cron: Research Dossier + Newsletter Write, 테마 Linux Daily/Android Kernel/Opensource Trending/Opensource Curation/AI Coding Agents 각 1쌍 + Linux Kernel Lens 6렌즈). raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재·원본 미변경 — sidecar 부재로 source_sha256 미설정 유지, 멱등 스킵).
  - 갱신 1건: `analyses/research-write-agent-separation` — 봇 차단 스레드에 **Anubis PoW v1.25.0 기전 구체화 + 자율 솔버 시도의 비신뢰성** 한 줄 보강. kernel-security 렌즈(9ac3)에서 Anubis v1.25.0 이 SHA256 nonce·difficulty 4 PoW 를 요구, 에이전트가 Python 솔버(~2500 nonce)를 즉석 작성했으나 부분 성공에 그침 → 07-07 "PoW 가 curl 까지 차단" 의 다음 escalation. 솔버 작성은 비신뢰 폴백이고 우아한 강등(미러·commitMessage+WebSearch 교차검증·confidence 강등+openQuestions)이 유일한 안정 경로임을 재확인. index.md 변경 없음(신규 페이지 없음).
  - 스킵 27건 (뉴스성 — 4대 runner 병렬 트리아지로 전건 확인, 파이프라인 메타는 아래 전부 기수록 패턴의 재현):
    - **write 단계 더블런 5쌍** (Android edeb→7876, Opensource Trending 7076→5085, Opensource Curation 0659→1756, Kernel Lens toolchain 6b27→66e8, arch-platform 6306→3d9e) — 1차가 파일 산출/완료 후 약 3~4분 뒤 동일 dossier(`generatedAt` 동일) 재발사, 2차는 대체로 `assistant_turns:0` no-op 또는 경미 edit. 07-05 "더블런 4쌍·maxAttempts=2"·07-07 "write 스텝 더블런 지속" 과 동일 현상 → [[research-write-agent-separation]]/[[prompt-schema-pipeline-coupling]] 기수록.
    - **write silent fail** (Linux Daily 96c6, Curation research f71c, kernel-security write 9979, perf-rt write 2433 등 `assistant_turns:0`) — 6/10 이래의 비결정적 고착 지속, 기수록.
    - **JSON 스키마 표류** (linux-distro-stable write 03df 에서 highlights[3] 이후 `verifyLink` 필드에 markdown 본문 혼입) — 05-13 `highlights[].action` 표류·07-07 dossier 정합성 손상과 동류, [[llm-content-quality-guards]]/[[highlights-action-validator-schema-drift]] 기수록.
    - **Anubis 봇 차단** (lore.kernel.org 포괄 + git.kernel.org, 다수 evidence url `https://***:***@` 마스킹, verified:false 다수) — v1.25.0 PoW escalation 만 위 갱신으로 흡수, 나머지 폴백 흐름은 6/18~7/7 사다리와 동일.
    - AI Coding Agents dossier(6451) 뉴스 토픽(Copilot /security-review·Code Quality 유료화·CC env·MS 도입 연구·소스리크·OpenClaw 과금·성능 회귀)은 이미 [[claude-code-source-leak-internals]]/[[anthropic-oauth-third-party-billing-trap]]/[[ai-coding-agent-cost-and-context-patterns]] 수록 주제의 재탕. 휘발성 커널/OSS 뉴스는 단일 패치의 범용 패턴화=과잉추출로 durable 전량 스킵. **코드 변경 없음.**

## 2026-07-14 (ingest 2차)

- **session-logs 유래** — 미처리 1건 처리 (claude_env opus-orchestration 검토·수정 세션 dfcb). raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재·원본 미변경 — 멱등 스킵).
  - 갱신 1건: `patterns/claude-code-model-tier-orchestration-gate` — 실증 결함 2건 정정: ① 소프트 넛지의 `permissionDecision:"allow"` 는 툴콜을 권한 프롬프트 없이 자동 승인하는 우회 부작용 → `additionalContext` 단독 반환으로 JSON 블록 정정, ② Bash 인플레이스 정규식 감지 오탐 4종(파이프 뒤 `grep -i`·커밋 메시지 안 "sed -i" 문자열·`echo "a > b.py"` 등) → shlex 따옴표·heredoc 인지 토큰화 + 파이프라인 세그먼트 판정 + `bash -c` 재귀 스캔으로 재작성 (FP7·TP9·회귀 23/23). 보강 5건: hard 모드 CC v2.1.196+ 필수(구버전 `agent_id` 부재 → 서브에이전트 편집까지 차단, 위임 자체 붕괴), 모드 지침의 서브에이전트 누출 방어("메인 세션 전용" 헤더), env.sh 모델 핀 드리프트(`/model` 이 env 보다 우선 — hard 모드 메인이 Fable 5 로 동작한 실증), state 파일 prune, install.sh settings.json 파싱 실패 시 중단. 같은 패키지 지식의 후속 등장이라 신규 생성 없이 기존 페이지 갱신.
  - 스킵: rtk 훅이 `ls`·`find` 출력을 삼키는 현상(`rtk proxy` 로 우회) — 단발 도구 트러블슈팅, 재등장 시 승격 검토.

## 2026-07-14 (ingest)

- **session-logs 유래** — 미처리 26건 처리 (인터랙티브 trading 세션 1건 + dev-blog cron 25건). raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재·원본 미변경 — 멱등 스킵).
  - 갱신 1건: `bugs/kis-derivative-etf-order-reject-apbk1497` — 199d(ht_trading 무한매수 매수 실패 조사): 동일 종목 0195S0 이 이번엔 [APBK1681] "기본예탁금 충족한 계좌만 매수주문가능" 으로 거부. 권한 게이트(APBK1497) 다음에 기본예탁금 게이트가 별도로 존재하는 다층 구조 확인 + 주문 거부 백오프(count 2/2 상한 — 7/7 의 24회 → 이번 2회) 동작 실측. 같은 종목·같은 계열 거부의 두 번째 등장이라 승격 규칙 충족 — 신규 생성 대신 기존 문서 갱신.
  - 스킵 25건: 20260714 dev-blog cron 뉴스레터/리서치 dossier 전량 — 뉴스성. runner 트리아지 결과 25건 모두 기대 JSON 출력 완료, 에러/실패/파이프라인 설계 변경 없음 (kernel.org 계열 Anubis 봇 차단 → WebSearch fallback 은 기지의 외부 제약, 정상 대응).

## 2026-07-13 (ingest)

- **session-logs 유래** — 미처리 23건 처리. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음(raw 1건 `claude-code-opus-orchestration-setup.md` 은 summary 기존재·내용 미변경 — sidecar 부재로 source_sha256 미설정 상태 유지, 멱등 스킵).
  - 갱신 1건: `patterns/claude-code-model-tier-orchestration-gate` — ccc7(opus-orchestration 후속 세션)의 wiki 미반영 델타 3종:
    1. **실측 — 네이티브 위임은 알아서 안 일어남**: 강모델 메인은 위임 인센티브 없음(CLAUDE.md 권고는 자주 무시), Sonnet 위임을 강제하는 유일 장치는 hard 게이트 편집 차단, Haiku 조사 위임은 강제 불가·지침 기반. soft의 "굳이 위임 안 함"(구현 자제) 문구가 조사 위임까지 억제 → 구현 자제/대량 조사 권장 분리 + runner description 트리거 보강.
    2. **PreToolUse payload 스키마 확정**: `prompt_id` 는 CC v2.1.196+(구버전은 transcript fallback degrade), `agent_id`/`agent_type` 은 서브에이전트 컨텍스트에서만 채워짐, 서브에이전트 툴콜도 훅을 타므로 제외 로직 필수.
    3. **claude_env 멱등 install 패키징**: 플러그인은 rc·CLAUDE.md 못 건드림 → git repo + 멱등 install.sh 가 전체 커버, 재설치 시 기존 모드 유지(무조건 off 리셋 금지), 레거시 `on` 자동 매핑 금지(하드블록→soft 의미 반전 위험), 가짜 HOME 실행 검증(신규/재설치/업그레이드/빈 HOME).
  - 세션 전반부 지식(3모드 토글·소프트 넛지·[[hermes-single-model-delegation]] 신설)은 해당 세션이 실시간으로 이미 wiki 에 반영 완료 — 이번엔 후반부 델타만 추가하고 플래그 정리.
  - 스킵 22건: 20260713 dev-blog cron 뉴스레터/리서치 dossier 전량 — 뉴스성. 표본 3건(82f6 AI Coding·956f Curation·9739 Kernel Weekly) 확인 결과, WebFetch silent fail·프롬프트 스키마 vs 출력 구조 편차 등 운영 메타는 기존 문서 계열([[research-write-agent-separation]]·[[highlights-action-validator-schema-drift]])과 동일 주제로 신규성 없음. 956f 가 평소(4K)보다 큰 35.7K 인 것은 후보 5개 레포 병렬 에이전트 조사 로그로 정상 동작.

## 2026-07-12 (ingest)

- **session-logs 유래** — 미처리 28건 처리(1건은 아래 드리프트 보정).
  - 신규/갱신 처리 2건:
    - `20260712-000307-1627`(llm_wiki vs llm_wiki2 비교) → `analyses/personal-llm-wiki-curation`에 "v1→v2 재편 사례" 절 추가(선별이관 37.6%=73/194·미이관 121건은 v1 보존 원칙·변경 이력의 세션 링크화), `projects/gieok`에 "Vault 재개명" 절 추가(LaunchAgent 3개+훅 7곳 경로 갱신, plist 재로드는 수동 실행 필요 교훈).
    - `20260712-002737-9413`(ht_dde 성공 매매전략 도출, 26거래일 전수 감사) → `analyses/signal-overfit-date-dispersion-check`에 "vol_surge 승률 착시" 사례 추가(기존 "승률 64~82%" 근거를 재검증, 스냅샷 행 대비 독립 이벤트는 약 10건 수준으로 축소), 신규 `analyses/surge-chasing-exclusion-filter`(급등 추격 배제 필터만 시장 대비 초과수익, confidence medium — 단일 하락장 레짐 표본), `projects/ht-dde`에 "2026-07-12 전략 전수 감사" 절 추가(방어 규칙 3종 확정 + `vol_surge300_eod`·`combo_guard` 신규 구현, 테스트 113개 통과).
  - 스킵 25건: 20260712 dev-blog cron 뉴스레터/리서치 dossier 전량(Linux Daily·Android Kernel·Opensource Trending·Opensource Curation·AI Coding Agents·Linux Kernel Lens) — 전부 뉴스성. 파이프라인 메타 지식은 기존 문서(research-write-agent-separation·llm-newsletter-rewrite-metadata-grounding·llm-content-quality-guards·dev-blog)에 이미 흡수. `ingested` 플래그만 갱신.
  - **플래그 드리프트 보정**: `20260702-235052-ea52`가 `ingested: false`로 남아 있었음 — 내용은 이미 07-04(`analyses/personal-llm-wiki-curation` 최초 생성)·07-04·07-08(`projects/gieok` "Vault 전환 절차"·"토큰 비용 모델" 절)에 소스로 완전 반영돼 있었으나 플래그 갱신만 누락. 신규 내용 없이 플래그만 정리.

- **raw-sources 유래** — `raw-sources/claude-code-opus-orchestration-setup.md`(읽기 전용, 본문 지시문은 실행하지 않고 참고 정보로만 취급) → 신규 `summaries/claude-code-opus-orchestration-setup` 1건(신설 `summaries` 카테고리). `patterns/claude-code-model-tier-orchestration-gate`와 상호 링크 + 명세서의 `deep-reasoner`(Sonnet 실행 역할 명명) vs 실제 구축본의 `implementer`/`deep-reasoner`(Opus 에스컬레이션 역할) 명명 불일치를 각주로 기록(모순 아님, 기존 내용 변경 없음).

## 2026-07-11 (ingest)

- **session-logs 유래** — 미처리 23건 처리. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 대상 없음(디렉터리 비어 있음, .cache/raw-md-sha 도 공백).
  - 신규/갱신 0건. index.md 변경 없음.
  - 스킵 23건: 20260711 dev-blog cron 뉴스레터/리서치 dossier 전량 (Linux Daily 2 · Android Kernel 3 · Opensource Trending 2 · Opensource Curation 2 · AI Coding Agents 2 · Linux Kernel Lens 12) — 전부 뉴스성. 파이프라인 메타 지식은 기존 문서(research-write-agent-separation·llm-newsletter-rewrite-metadata-grounding·llm-content-quality-guards·dev-blog)에 이미 흡수.
  - AI Coding Agents dossier 본문 실사: 후보가 GitHub Copilot 모바일 필터·Claude Code v2.1.206 릴리스 노트(버전별 픽스 changelog)·Cursor side-chat changelog·HN 스토리(스테가노그래피 마킹, Source Leak "fake tools/undercover mode", OpenClaw 차단, 3.7 Sonnet 등) — 전부 뉴스성 롤업. Source Leak 계열은 이미 [[claude-code-source-leak-internals]], OpenClaw 는 [[openclaw-acp-runtime-internals]]·[[openclaw-telegram-group-setup]] 로 추출됨. 스테가노그래피 마킹 건은 07-05 이후 반복적으로 원 블로그 403·Anthropic 공식 미확인이라 "출처 없는 정보 기정사실화 금지" 원칙상 기록하지 않음. 버전별 릴리스 노트는 재조회 가치 없어 스킵.

- **session-logs 유래 (수동 ingest, 추가 1건)** — 위 cron 처리 후 남아 있던 `ingested: false` 1건(`1af9 최근 디스크 사용 상태 확인해줘`, disk_monitor 프로젝트) 처리. raw-sources/·.cache/extracted/·fetched/·mcp-note 대상 없음(전부 공백).
  - 신규/갱신 0건. index.md 변경 없음.
  - 스킵 1건: `1af9` — macOS 디스크 여유공간 급락·회복 진단 세션. 내용은 실질적임(전체 여유공간 타임라인에서 드롭→며칠 뒤 회복 패턴 반복 → 실제 파일이 아니라 **purgeable / 로컬 APFS 스냅샷** 신호 → `tmutil listlocalsnapshots` 로 `com.apple.os.update-*`(MSUPrepareUpdate, 스테이징된 macOS 업데이트)가 원인임을 확정). 그러나 **두 도메인(ai-agent·trading) 밖의 macOS 시스템 진단 + 일회성 상태 조회**라 수집 기준 미달. 07-06 `d1fe 디스크 사용 상태 확인`("일회성 상태 조회") 스킵과 동일 부류 — disk_monitor 는 반복 등장하지만 도메인 게이트가 승격을 차단. CLAUDE.md "두 도메인 밖 주제는 기록하지 않는다" 원칙에 따라 ingested 플래그만 갱신.

## 2026-07-10 (ingest)

- **session-logs 유래** — 미처리 24건 처리. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 대상 없음(디렉터리 비어 있음).
  - 신규 1건: trading `bugs/flask-jsonify-infinity-breaks-browser-json` — ht_dde `/rs` 종목추천 3표가 전부 공백. 원인은 오늘 스캔에 직전 20일 평균거래량 0 종목(금호에이치티)이 들어와 `거래량/0 = inf` → `/api/rs/latest` 응답에 `"max_vol_ratio":Infinity` → 브라우저 `response.json()`(JSON.parse) 거부 → `render()` 미실행. Flask `jsonify` `allow_nan` 기본 True 라 curl·Python 확인 시엔 정상으로 보여 오진 유발("서버로 보면 정상, 브라우저만 공백"). 2겹 방어(scanner: avg=0→NaN 재발방지, server: 응답 직전 inf→"" 즉시복구·전방위) + 회귀 테스트 + launchd kickstart 재기동·node 엄격파서 검증. 일반 교훈: 서버-브라우저 관측 비대칭이면 직렬화 경계 의심·`allow_nan=False` 로 fail-fast·비율은 분모0 가드 (출처 58a3).
  - 갱신 2건 (출처 58a3):
    - `analyses/scoring-system-ic-validation` — "라이브 out-of-sample 2차 확증 (ht_dde 종이거래)" 절: 05-10 백테스트(5일)의 두 결론(모멘텀=역신호, 단일 강신호 > 합산)이 +30분 horizon·4주 연속 스냅샷에서 독립 재현. 점수↑→+30분 수익률·승률 단조 감소(점수5 −0.27%/승률35%, 06-18/25·07-02/09 재현), 점수0·5 동일 유니버스·시각이라 시장 베타 상쇄 → 모멘텀 추격=단기 평균회귀. 거래량증가율만 양(400%↑ +1.01%/64% n=58)이라 `vol_surge` 단일규칙 격리, 리웨이트 8변형 ±0.05%p 로 가중치 미세조정=노이즈 2차 실증.
    - `projects/ht-dde` — "4주 동작 검토 & Infinity 버그 & vol_surge 슬롯" 절: 세 서브시스템 전부 손실이나 원인이 스코어 역예측(4주 재현)임을 검증 데이터로 확정, `vol_surge` 슬롯 신설(20거래일 사전등록·전역가드 max_day_change 7% 겹침), RS 매일 새 꼭지 매수 능동회전·소형주 되돌림 진단, 실거래 매핑(선정=n-stock-info / 실행=ht-trading).
  - 스킵 23건: 20260710 dev-blog cron 뉴스레터/리서치 dossier 전량 (Linux Daily 2 · Android Kernel 1 · Opensource Trending 2 · Opensource Curation 4 · AI Coding Agents 2 · Linux Kernel Lens 12) — 전부 뉴스성. 서브에이전트 병렬 스캔으로 전건 확인: 파이프라인 메타 지식은 기존 문서(research-write-agent-separation·llm-newsletter-rewrite-metadata-grounding·llm-content-quality-guards·dev-blog)에 이미 흡수. 유일한 경계선이던 **Anubis 봇 차단(lore/git.kernel.org, `/raw` 포함)으로 소스 라이브 취득 불가 → collect 단계가 원문을 payload 에 선캡처해 research 가 embedded-text+WebSearch+미러 교차검증으로 우아하게 강등** 패턴도 이미 [[research-write-agent-separation]] 07-07 항목(curl 차단·WebFetch 네트워크 전면차단 의심)에 흡수돼 신규성 없음 → 스킵. AI Coding Agents dossier 표본도 Copilot/Claude Code 체인지로그·스테가노그래피 스토리 등 뉴스 콘텐츠라 재조회 가치 없음.

## 2026-07-09 (ingest)

- **session-logs 유래** — 미처리 26건 처리. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 대상 없음(디렉터리 비어 있음).
  - 신규/갱신 0건. index.md 변경 없음.
  - 스킵 26건: 20260709 dev-blog cron 뉴스레터/리서치 dossier 전량 (Linux Daily 2 · Android Kernel 3 · Opensource Trending 3 · Opensource Curation 2 · AI Coding Agents 2 · Linux Kernel Lens 14) — 전부 뉴스성. 파이프라인 메타 지식은 기존 문서(research-write-agent-separation·llm-newsletter-rewrite-metadata-grounding·llm-content-quality-guards·dev-blog)에 이미 흡수.
  - AI Coding Agents dossier·newsletter 표본 확인: GitHub Copilot/Codex changelog·Claude Code 릴리스 노트(v2.1.203/204 버전 픽스)·스테가노그래피 마킹(`seenBefore: true`, 어제 다룸)·Source Leak(이미 `analyses/claude-code-source-leak-internals` 로 추출됨) — 신규 재조회 가치 없음. 스테가노그래피 마킹 건은 원 블로그 403·Anthropic 공식 미확인(confidence medium)이라 "출처 없는 정보 기정사실화 금지" 원칙상 기록하지 않음.

## 2026-07-08 (ingest)

- **session-logs 유래** — 미처리 49건 처리. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 대상 없음(디렉터리 비어 있음).
  - 신규 1건: trading `bugs/kis-derivative-etf-order-reject-apbk1497` — 무한매수에 추가한 레버리지 ETF `0195S0` 이 매수 신호는 정상인데 30분 사이클마다 **24회 전부 거부**. 원인은 코드/설정이 아니라 **계좌 파생ETF 미신청** (`[APBK1497]` 선택확인서 미징구 + 레버리지 ETP 사전교육 미이수). 일반 주식은 무관. 교훈: 신호 정상인데 주문만 100% 거부 → 브로커 에러코드부터 보고, 계좌 권한은 종목 유형별로 다르며 파생/레버리지 편입은 계좌 상품 권한이 선결 (출처 2c24).
  - 갱신 5건 (전부 출처 2c24 · ea52):
    - `projects/ht-trading` — ① 0195S0 24회 거부 [APBK1497] 절(신규 버그 링크). ② **매수 시점 스코어 감사 로그** 절: 추천 소스 n_stock_info 의 일자 멱등 재작성(:20/:50 cron DELETE→INSERT)이 매수 시점 스냅샷을 덮어써 사후 DB 추적 불가 → `BuyCandidate.report_date` + 소수점 점수·현재가·기준일 전용 감사 로그 도입(`test_scoring_buy_audit_log.py`, 393 테스트). 2단계 컷(추천 55 vs 매수 62)·`%.0f` 반올림 은폐 기록.
    - `projects/n-stock-info` — §5 에 "하류 관측성 비용" 절: 일자 멱등 저장이 point-in-time 이력을 파괴해 다운스트림 매수 종목이 사후 소실됨. 자체 추천 컷 55 vs ht_trading 매수 컷 62.
    - `analyses/stock-screening-score-design` — §5 에 두 번째 실증(소비 측 감사 로그로 저장 정책 불변한 채 재현성 확보).
    - `projects/gieok` — **토큰 비용 모델** 절: "LLM 미호출 ≠ 토큰 0". wiki 목차 주입은 `claude -p` 는 안 부르지만 매 세션·매 턴 입력에 index 전문이 실림 → 비용 = index 크기 × 세션 수. v1→v2 index 85% 축소(55KB→9KB) 실측.
    - `patterns/claude-code-token-optimization` — 관련 맥락에 "SessionStart 주입 컨텍스트 = 반복 입력 비용" 원칙 한 줄.
  - **인덱스 드리프트 보정**: 07-07 저녁 cron(commit 20260707-2227)이 fe2f·ac9d 를 이미 인제스트하며 5개 페이지를 생성했으나 index.md 미등록 상태였음 → index 에 `dca-intraday-buy-timing`·`kis-minute-chart-trs`·`naver-finance-news-referer-required`·`pykrx-krx-login-required`·`relative-stop-benchmark-stale-price` 추가.
  - **이미 인제스트되어 플래그만 갱신 (신규 없음)**: fe2f(무한매수 0195S0 추가 — ht-trading §종목추가·dca-intraday-buy-timing·kis-minute-chart-trs·relative-stop 에 완전 반영), ac9d(RS EOD 스캐너 — ht-dde §RS·pykrx·naver-referer·optimal-strategy §5 에 완전 반영). 07-07 cron 이 내용은 넣었으나 `ingested` 플래그를 못 넘긴 것을 이번에 정리.
  - 스킵 45건: dev-blog cron 뉴스레터/리서치 dossier (Linux Daily·Android Kernel·Opensource Trending/Curation·AI Coding Agents·Linux Kernel Lens) 전량 — 뉴스성, 파이프라인 메타 지식은 기존 문서(research-write-agent-separation·llm-newsletter-rewrite-metadata-grounding·llm-content-quality-guards·dev-blog)에 이미 흡수. AI Coding Agents dossier 표본 확인 결과 신 릴리스·체인지로그 조사 산출물로 재조회 가치 없음.
  - 참고: 2c24 세션에서 사용자가 실수로 붙여넣은 `DART_API_KEY` 값은 비밀 정보이므로 wiki 어디에도 기록하지 않음.

## 2026-07-06 (ingest)

- **session-logs 유래** — 미처리 29건 처리. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 대상 없음(디렉터리 비어 있음).
  - 신규 1건: ai-agent `bugs/stale-process-attributeerror-inprocess-coupling` — hermes-webui(:8787) `'ToolEntry' object has no attribute 'dynamic_schema_overrides'` 사례. 디스크 코드에는 속성이 있는데 런타임만 AttributeError → **코드 버그가 아니라 stale 장수 프로세스**. 진단은 `ps lstart/etime`(기동시각) ↔ `stat %Sm`(파일 mtime) ↔ `git log -S`(속성 도입시점) 3종 대조로 "프로세스가 파일 갱신보다 먼저 떴음" 확정, 해결은 재시작. launchd 관리 데몬은 `kill`=자동 respawn 이라 `ctl.sh start` 불필요(포트 충돌). in-process import 강결합([[self-hosted-agent-webui-integration]] 방식 A "버전 동기화 필수")의 실제 발현이므로 그 분석에 역링크 추가 (출처 3336).
  - 갱신 1건: `analyses/self-hosted-agent-webui-integration` (방식 A 단점에 "파일 갱신해도 프로세스 미재시작 시 런타임 AttributeError" 절 + related 추가).
  - 스킵 28건: dev-blog cron 뉴스레터/리서치 dossier 26건 (뉴스성 — 파이프라인 메타 지식은 기존 문서에 이미 흡수), `d1fe 디스크 사용 상태 확인` (일회성 상태 조회), `02ad 새 OCI 계정 무료티어` (오라클 클라우드 가입·VM 생성·SSH·재시도 스크립트 — 두 도메인 밖 인프라 셋업). 승격 규칙상 첫 등장이나 stale-process 버그는 도메인 핵심 운영 지식이라 즉시 기록.

## 2026-07-05 (ingest)

- **session-logs 유래** — 미처리 28건 처리 (인터랙티브 대형 세션 3건 + dev-blog cron 25건). raw-sources/·.cache/extracted/·fetched/·mcp-note 는 대상 없음.
  - 신규 12건:
    - trading (ht_trading 7/4 전면 개선 세션 2dad): `bugs/absolute-stop-loss-elif-dead-code` (red-link 해소 — Rule1a -20% 무조건 백스톱, 백스톱은 스윕 최적값이 아니라 tail 보험), `bugs/order-post-retry-double-fill` (비멱등 주문 POST 재시도 이중 체결 + 취소 오판 상태 재조회 확정), `patterns/backtest-clock-injection` (벽시계 오염 → 거래 43→249건·수익 +2.4%→+20% 재측정), `analyses/backtest-fill-model-adverse-selection` (즉시체결 가정이 역선택 은폐, limit ratio V자 스윕 + 장중 리플레이 검증), `patterns/startup-dependency-crash-loop` (주말 배포 크래시 루프 — startup/steady-state 오류 비대칭)
    - trading (ht_dde 목표 검토 세션 8c43): `analyses/optimal-strategy-search-preconditions` (목적함수·레짐·counterfactual·비용 우선의 4 선결 조건), `patterns/mirror-config-drift-guard-test` (원본 yaml 직접 읽어 자동 대조 + allowlist)
    - ai-agent (hermes 멀티에이전트 실험 세션 e509): `analyses/weak-model-agent-reliability-compounding` (0.9⁵≈59% 신뢰도 복리 붕괴 → 결정적 워크플로우 + 검증 게이트), `analyses/multi-agent-orchestration-taxonomy` (delegate/칸반/조직 3분류 + 선형 코딩 파이프라인은 조율 비용이 이득 잠식), `analyses/multi-agent-shared-wiki-concurrency` (다중 동시 쓰기는 실험적 확장 — 단일 큐레이터·git·lint cron), `patterns/single-dispatcher-per-queue` (칸반 claim churn — 보드당 dispatcher 하나)
    - ai-agent (2dad+5338 두 번째 등장으로 승격): `patterns/parallel-review-adversarial-fix-workflow` (병렬 리뷰 → 교차검증 → TDD 수정 → 적대적 검증 + exit-code masking 함정)
  - 갱신 13건: `projects/ht-trading` (7/4 커밋 20여 개 종합 — limit_price_ratio 1.005 재채택 근거 포함), `projects/ht-dde` (목표 재정렬·실거래 미러링 절), `analyses/risk-control-exemption-and-failed-attempt-accounting` (손실 한도 SELL 차단 + 배치 스냅샷 투영), `analyses/scoring-version-comparison-methodology` (재가중 무효 조건 + 실험 슬롯 운영), `analyses/stock-screening-score-design` (급등 꼭지 편향 §4 + 멱등 vs point-in-time §5), `concepts/hermes-agent` (위임 이벤트 실측·API_SERVER_KEY·FTS·dispatcher·클론 정체성), `analyses/hermes-paperclip-adapter` (패키지판 builtin 어댑터 정정), `analyses/multi-profile-cli-agent-isolation` (§5 정체성은 SOUL 에), `projects/hermes` (update=origin/main 함정), `patterns/launchd-secret-management` (ssh-agent 부재 절), `analyses/oauth-refresh-token-rotation-multi-client` (run.failed 발현 경로), `projects/hermes-dashboard` (7/4 세션 이력), `projects/dev-blog` + `analyses/research-write-agent-separation` (7/5 사이클 — write "에이전트형 표류"로 stdout 계약 파괴, 3주 silent fail 서사의 상태 변화)
  - 스킵: 20260705 cron 뉴스 콘텐츠 전량 (뉴스성 — 기존 결정 동일), AI Coding dossier 의 스테가노그래피 마킹 등 단발 제품 뉴스, eslint↔tsc 충돌·vite dev 미들웨어 shadowing (도메인 밖 프론트 툴체인), ruff 도입·DRY 리팩터링·좀비 vite 프로세스 등 일회성. 서브에이전트 4대 병렬 트리아지로 기존 문서와 중복 대조 완료.

## 2026-07-04 (ingest)

- **GitHub 프로젝트 분석 유래** — `NousResearch/hermes-paperclip-adapter` 를 ai-agent 분석 문서로 승격.
  - 신규 1건: `analyses/hermes-paperclip-adapter` (Paperclip 이슈/heartbeat 를 Hermes CLI 실행으로 연결하는 런타임 어댑터, 논문 scraper 아님, 적용 조건은 Paperclip+Hermes 동시 운영, 장점은 AI agent 를 작업 큐 worker 로 붙이는 것, 주요 리스크는 비대화형 실행을 위한 `--yolo`).

- **session-logs 유래** — 미처리 26건 처리 (v2 첫 정식 ingest 사이클). raw-sources/·.cache/extracted/·mcp-note 는 대상 없음.
  - 신규 2건: `analyses/personal-llm-wiki-curation` (v1 write-only 창고 실패 → 도메인 한정·2회 등장 승격·재조회율 기준·지식 3분류, 출처 ea52), `analyses/holding-period-signal-mismatch` (같은 진입신호가 보유기간에 따라 손실↔초과수익 역전 + 검증 엣지를 청산 규칙이 잘라내지 않는 설계, 출처 5338)
  - 갱신 5건: `projects/gieok` (vault v1→v2 전환 연결 지점 3곳 + hook 세션 시작 고정 함정 + 보존 정책 07:40), `projects/ht-dde` (afternoon_eod 실전화 + 워크플로 심층 분석 발견), `decisions/shared-broker-appkey-token-cache` (구현 검증 + 비원자적 캐시 쓰기 리스크), `analyses/research-write-agent-separation` (evidence-stuffing / openQuestions 자동 주입 / 마스킹 span 파괴 3건), `projects/dev-blog` (7/3·7/4 사이클 운영 관찰 — write 로그 소실 확대·후보 공급 정체·MIME 미디코딩)
  - 스킵: 뉴스레터 cron 로그 24건의 뉴스 콘텐츠 전량 (뉴스성 — 수집 기준 밖), 파이프라인 메타 지식만 위 문서들로 흡수. 서브에이전트 트리아지로 기존 3문서(research-write-agent-separation·llm-newsletter-rewrite-metadata-grounding·llm-content-quality-guards)와 중복 대조 완료.

## 2026-07-02

- llm_wiki v2 초기 셋업. v1의 주제 산개 교훈을 반영해 도메인을 ai-agent·trading 2개로 한정.
- 수집 기준·승격 규칙·읽기 경로를 CLAUDE.md에 명문화.
- v1에서 73건 이관 완료 (ai-agent 44 · trading 29). 선별 기준: 참조 5+ 또는 변경 이력 2+ 또는 도메인 고유 지식. 상세는 MIGRATION.md.
- frontmatter `domain` 을 `ai-agent | trading` 으로 전건 갱신, index.md 전건 등록.
