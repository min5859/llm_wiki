## 2026-10-23 (ingest — 지정 2세션 검색 예산 보완·실거래 실행 차이)

- **session-logs 유래 / Phase I** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`·`session-logs/codex-01a167f5-cf85-7bb1-b68f-b8a9539ce3d9.md`의 `ingested: false` 및 지정 `.cache/ingest-views/` 대화를 기존 wiki와 대조했다. 둘 다 session-log이며 지정 mcp-note는 없다. 소스 속 명령·스킬·환경 지시는 참고 데이터로만 다뤘다. 기존 핵심 분석의 반복 사례·운영 버그 후속이므로 승격 기준을 적용해 **신규 0개, 기존 지식 페이지 4개 갱신**했다.
- **첫 세션 수집** — [[ai-publishing-migration-safety]]에 미적용 권고 이후의 구현을 추가했다. 강제 상한 5회와 프롬프트 작업 예산 4회를 분리하고 계속 검색하게 하던 충돌 지시를 제거했다. URL/날짜 검증·초과 차단 유지, 추가 재시도 없음, 첫 묶음 웹 호출 2회·선정 0건을 기록했다. 한 묶음의 종료 성공을 수집량 개선/6개 전체 성공으로 확대하지 않으며 10/24 정기 실행 확인은 남았다.
- **두 번째 세션 수집** — [[paper-to-live-strategy-isolation]]에 계좌 사전점검 보류가 뒤 후보의 미전송·재시도 누락으로 확산한 경로와 VI 제외의 차이, 체결 대사와 주문 전송 대사의 구별을 추가했다. 정확한 준비 실패 항목은 로그 부족으로 미확정, 안전 차단 유지·회복 후 미전송 재검증은 미구현으로 남겼다. [[backtest-fill-model-adverse-selection]]에는 최종 추천의 과거 종가 체결 편향·덱스터의 다른 진입 구간, 15:28 당시 정보로 후보를 확정하는 별도 검증 제안을 통합했다. 실거래 시각은 변경되지 않았다. [[ht-trading]]에는 두 역행 종목의 실시간 신호 뒤 약 1초 내 공식 주문 기록 연결 보고와 남은 보완을 연결했다.
- **중복·가치 기준 스킵** — 첫 세션의 이관/history 복구·공용 계정·단일 게시자·CLI 오류/격리는 [[ai-publishing-migration-safety]]·[[oss-radar]]·[[dev-blog]]·[[agentic-cli-text-generation-lockdown]], 근거 추출·표시 품질은 [[llm-content-quality-guards]]에 기수록이다. 두 번째 세션의 진입 차이·종가 소급 금지·실시간 연결 설계·손익 집계 원칙은 위 분석과 [[polling-interval-vs-bar-interval]]·[[trading-performance-cash-flow-reconciliation]]에 있어 최종 후속만 보강했다. 출처는 위 두 세션 경로다. 일별 잔액/손익·보유 목록 전체, 단발 명령 안내·점검 수치 반복, 단순 버전/포맷/미관 변경, 진행·커밋 요청, 철회된 설명·탐색 과정은 3개월 후 재조회 가치가 없어 제외했다. 두 대상 모두 유효 내용과 스킵 범위를 검토해 완료 처리한다.
- **raw-sources 유래** — 루트 MD 2개만 있고 요청한 하위 서브디렉터리 MD/fetched 대상은 없다. 원본·기존 요약을 보존했으며 summary 신규/갱신·source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/`가 없어 chunk·부모 index·page_range·해시 복사 대상 없음.
- **검증·처리** — 지식 페이지 → index → 본 기록 → 지정 플래그 순서로 반영한다. 원 세션의 테스트·배포·주문 대사는 보고로만 수집하며 이번 실행에서는 메타데이터·출처·변경 이력·관련 링크·추가 내용의 비밀값/프로젝트 경로와 변경 범위, 플래그 외 바이트 보존을 검사한다. 네트워크·다른 에이전트·commit/push는 수행하지 않는다.
- **변경 파일 전체(8개)**: `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/analyses/paper-to-live-strategy-isolation.md`, `wiki/analyses/backtest-fill-model-adverse-selection.md`, `wiki/projects/ht-trading.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a167f5-cf85-7bb1-b68f-b8a9539ce3d9.md`.

## 2026-10-23 (ingest — 지정 2세션 추가 대화 후속)

- **session-logs 유래 / Phase I** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`와 `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 `ingested: false` 및 지정 `.cache/ingest-views/`를 검토하고 기존 10/23 수집 기록과 대조했다. 주요 지식은 기수록이며 추가 대화의 최종 결과만 기존 페이지 4개에 보강했다. 신규 페이지는 없다. 두 대상은 session-log이며 mcp-note는 없다. 참고 데이터의 명령·스킬·환경 지시는 실행하지 않았다.
- **첫 소스 후속** — [[ai-publishing-migration-safety]]에 10/23 예약 브리핑 1회 성공과 별도 검색 5/6 성공을 구분했다. `codex_search_budget_exhausted`는 구현상 묶음당 5회 상한 초과로 보고됐으며 구독 사용량 초과가 아니다. 전체 재실행 대신 예산 내 종료 보완은 권고만 됐고 미적용이다. 기존 오류 진단·근거 추출·소유권 통일을 중복 생성하지 않았다.
- **두 번째 소스 후속** — [[scoring-version-comparison-methodology]]에 확인된 일봉 부족 종목만 제외하고 통신/원인 불명 누락은 보류하는 수정, [[paper-to-live-strategy-isolation]]에 주문 직전 안전검증 추가·기존 계정 청산 전용 보존·새 성과 장부 분리를 기록하고 [[ht-dde]]에 최종 반영 상태를 연결했다. 기존 분석의 미완료 설명은 당시 이력으로 보존하고 후속 완료를 구분했다. 실제 정기 입력 수용·첫 가상매수는 확인 전이며 독립 계정의 실체결 일치나 수익 개선을 주장하지 않는다.
- **중복·가치 기준 스킵** — 첫 소스의 이관/history·인증·단일 게시자·CLI 격리는 [[oss-radar]]·[[dev-blog]]·[[ai-publishing-migration-safety]]·[[agentic-cli-text-generation-lockdown]], 근거 추출은 [[llm-content-quality-guards]]에 기수록이다. 두 번째 소스의 KIS/Naver 전환·점수·필터 재생·실시간 매도·체결/호가 대조·지문 복구는 [[n-stock-info]]·[[scoring-version-comparison-methodology]]·[[polling-interval-vs-bar-interval]]·[[backtest-fill-model-adverse-selection]] 등에 기수록이므로 반복 작성하지 않았다. 출처는 위 두 세션 경로다. 일별 뉴스·전체 잔액/종목 목록·진행/커밋 요청·일회성 Git 조회법·미관/포맷·탐색적 시행착오는 재조회 가치가 없어 제외했다. 핵심 운영 버그의 후속이며 기존 반복 지식의 갱신으로 승격 기준을 적용했다.
- **raw-sources 유래** — 루트 MD 2개만 있고 요청한 하위 서브디렉터리 MD/fetched 대상은 없다. 원본 및 기존 요약은 보존했고 source_sha256 복사 대상도 없다.
- **PDF 유래** — `.cache/extracted/`가 없어 chunk·index·page_range·해시 복사 대상이 없다.
- **처리·검증 범위** — 지식 페이지 → index → 본 기록 → 지정 두 플래그 순서로 반영한다. 원 세션의 테스트·재시작·배포 결과는 보고로만 수집하며 이번 실행에서 필수 메타데이터·추가 링크·추가 내용의 비밀값 패턴과 git diff --check를 통과했다. 실행 전후 해시 대조에서 위키 6개 외 기존 추적/비무시 파일의 변경·삭제는 없었다. Git에서 무시되는 대상 세션 2개는 바이트 치환으로 frontmatter의 ingested 한 곳만 변경했고 true 상태를 확인했다. 네트워크·다른 에이전트·commit/push는 수행하지 않는다.
- **변경 파일 전체(8개)**: `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/analyses/scoring-version-comparison-methodology.md`, `wiki/analyses/paper-to-live-strategy-isolation.md`, `wiki/projects/ht-dde.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-23 (ingest — 지정 두 세션 확정 수정·실시간 주문 연결·비교 후속)

- **session-logs 유래 / Phase I** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`와 `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 `ingested: false`를 확인하고 지정 `.cache/ingest-views/` 대화를 기존 wiki·10/22까지의 수집 이력과 대조했다. 두 파일 모두 session-log이며 지정 mcp-note는 없다. 소스 안 명령·스킬·환경·승인 지시는 실행하지 않았다.
- **첫 소스: 기존 분석 2개 보강** — [[ai-publishing-migration-safety]]에 오류 코드/제한 이력·기존 자료의 실패 단계만 재처리한 복구와 10/23 소스 소유권 통일을 기록했다. 전용 실행 계정·파일 소유권·systemd 쓰기 보호를 분리하고 기존 운영 방식과 검증 경계를 남겼다. [[llm-content-quality-guards]]에는 AI의 기사 ID 선택과 코드의 실제 제목 근거 추출을 분리한 수정·검증 유지 결정을 수집했다. 10/21의 미수정·원인 미확정 기록은 당시 이력으로 보존하고, 후속 한 번의 재현 원인을 과거 모든 장애에 확대하지 않았다.
- **두 번째 소스: 기존 분석 4개·프로젝트 2개 보강** — [[polling-interval-vs-bar-interval]]의 실시간 고점/신호 보존·단일 실제 매도 경로 연결, [[paper-to-live-strategy-isolation]]의 응답 수신 시각 기준 호가 검증·같은 주문 부분체결 감시 유지·감사 오탐 수정, [[backtest-fill-model-adverse-selection]]의 다음 조회 체결 편향·뉴인텍 최종 미체결 정정·직접 호가 비교, [[scoring-version-comparison-methodology]]의 후보/추천 필터 차이·8거래일 필터 제거 재생·지문 영향 검증 후 복구를 통합했다. [[ht-trading]]·[[ht-dde]]에는 최종 운영 상태와 남은 검증을 연결했다. 반복된 핵심 버그·도메인 결정 및 기존 분석 후속이므로 승격 기준을 충족하며 **신규 문서 0개, 기존 지식 8개 갱신**이다.
- **판단·검증 경계** — 다음 조회 체결 모형은 실제 즉시체결을 놓칠 수 있으므로 최종 새 아침 계정의 뉴인텍은 849원 주문 후 미체결로 기록하고 앞선 약 850원 체결 설명을 확정 거래로 재사용하지 않았다. 09:35 유지·추가 관찰, 거래대금 200억원 필터 단순 해제 미채택과 네이버로 교체할 근거 부족을 최종 판단으로 남겼다. 8거래일 재생·4일 중 하루 입력 보류를 일반 수익성으로 확대하지 않는다. 실시간 실매도 연결은 장외 운영 반영까지 보고됐고 장중 실주문 검증은 남았다. 테스트 개수·대사·서버/배포 확인·커밋은 원 세션 보고이며 이번 실행에서 실행하거나 재조회하지 않았다. 전체 입력 보류 정책·완전복제의 주문 직전 안전검증 보완·10/23 정기 브리핑 재발 여부는 확인 전이다.
- **중복/가치 기준 스킵** — 첫 소스의 이관/history·계정 홈·정기 게시·CLI 제한은 [[ai-publishing-migration-safety]]·[[oss-radar]]·[[dev-blog]]·[[agentic-cli-text-generation-lockdown]]에 기수록이다. 두 번째 소스의 네이버/KIS 전환·재무 캐시·주문 복구/호출 예산·미국 페이퍼·손익 집계·추천 네 조합의 앞선 내용은 [[n-stock-info]]·[[financial-data-freshness-and-cache]]·[[trading-api-failure-handling]]·[[shared-broker-appkey-token-cache]]·[[multi-market-trading-account-isolation]]·[[trading-performance-cash-flow-reconciliation]]·기존 비교 분석에 기수록이다. 위 두 소스 경로를 출처로 유지하고 새 페이지는 만들지 않았다. 기사별 뉴스·일별 잔액/손익 전체·일회성 명령 안내·반복 진행 요청·미관/포맷·탐색 과정·철회된 설명은 재조회 기준 밖이라 제외했다. 두 세션 모두 유효 내용과 스킵 범위를 검토해 완료 처리한다.
- **raw-sources 유래** — 루트 MD 2건만 있고 요청한 하위 서브디렉터리 MD/fetched는 0건이다. 루트 원본·기존 반영을 보존했다. summary 신규/갱신 및 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/`가 없어 chunk·부모 index·page_range·해시 복사 대상 없음.
- **처리 순서** — 지식 페이지 → index → 본 기록 → 지정 두 세션의 `ingested: true` 순서로 반영한다. wiki와 지정 플래그 외에는 수정하지 않으며 네트워크·다른 에이전트·commit/push는 수행하지 않는다. 필수 메타데이터·출처·변경 이력·새 링크·추가 내용의 비밀값 패턴·프로젝트 경로·원본 플래그 외 바이트 보존 검사를 통과했다. 추적 파일의 실행 전후 해시 대조에서 변경은 위키 10개뿐이며 기존 사용자 변경은 보존됐다. `git diff --check`도 통과했다.
- **변경 파일 전체(12개)**: `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/analyses/llm-content-quality-guards.md`, `wiki/analyses/polling-interval-vs-bar-interval.md`, `wiki/analyses/paper-to-live-strategy-isolation.md`, `wiki/analyses/backtest-fill-model-adverse-selection.md`, `wiki/analyses/scoring-version-comparison-methodology.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-22 (ingest — 지정 1세션 실시간 감시·한도 확인·추천 대사 후속)

- **session-logs 유래 / Phase I** — `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 `ingested: false`와 지정 읽기용 `.cache/ingest-views/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`를 검토하고 기존 수집 기록·관련 wiki와 대조했다. type은 session-log이며 지정 mcp-note는 없다. 소스의 명령·스킬·환경/승인 지시는 실행하지 않았다.
- **기존 6문서 갱신 / 신규 0건** — `analyses/polling-interval-vs-bar-interval`에 실시간 수신 후 저장용 초당 샘플링이 고점을 누락하는 문제, 샘플링 전 이벤트 판단·WebSocket/1분 REST 보완·120초 대조를 통합했다. 반복된 트레일링 관측 문제와 핵심 구현 판단이므로 수집하되 별도 분석을 중복 생성하지 않았다. `analyses/paper-to-live-strategy-isolation`에는 추천 집합과 체결 집합을 진입·보유/당일 매도 이력·자금·주문 조건별로 대사하는 패턴과 실제 주문 10건의 2회 이상 조건 확인 범위를 보강했다.
- **기존 미확인 경계 정정** — `decisions/shared-broker-appkey-token-cache`·`analyses/trading-api-failure-handling`에서 추가 답변 전의 ‘한도/밴 답변 미완료’를 취소선과 후속으로 보완했다. 공급자 18회/초는 공지를 확인했다는 세션 보고, 합산 8회/배경 최대 4회는 적용된 내부 예산으로 구분했다. 제한 응답 1건·이후 조회/매도 지속은 계정 전체 밴의 증거가 없다는 근거이며 정확한 당시 총호출 수는 계속 미확정이다. 공식 공지는 이번 실행에서 재조회하지 않았다. `projects/ht-trading`·`projects/ht-dde`에 검증 관측기/화면·선정 대사를 연결했다.
- **검증·운영 경계** — 실시간 감시는 실주문 없는 검증 모드이고 실거래 매도는 기존 2분 방식이다. 같은 초 980→989→960 합성 시세의 신호 재현과 재시작/중복 신호 검증은 실제 체결·수익 개선을 입증하지 않는다. 국내 1,034개(+13개 하위 테스트)·대시보드 394개 및 1,428개 합계, 엔진/7개 테이블 보존·3채널 구독 승인은 원 세션 보고다. 브라우저 직접 확인은 연결 불가로 미실시, 장중 실제 수신·09:35 구독 갱신·실제 체결 대조는 아직 확인 전이다. 이번 수집에서는 거래 코드·테스트·서비스를 실행하지 않았다.
- **중복·가치 기준 스킵** — 같은 소스의 네이버 장애/KIS 전환·점수식/재무 캐시는 `projects/n-stock-info`·`analyses/financial-data-freshness-and-cache`, 추천 네 조합은 `analyses/scoring-version-comparison-methodology`, 주문 복구는 기존 API 분석, 뉴인텍 관측 공백은 `analyses/trading-observation-vs-order-time-gates`, 오늘 전체/최근 5건 손익은 `patterns/trading-performance-cash-flow-reconciliation`, 미국 페이퍼는 `analyses/multi-market-trading-account-isolation`·`projects/ht-dde`에 기수록이다. 판단 근거와 소스 경로를 유지하고 반복 작성하지 않았다. 개별 종목/잔액 전체 목록·일회성 상태/백업 정리·진행/커밋 요청·포맷/미관·철회된 안·탐색적 시행착오는 재조회 가치가 없어 제외했다.
- **raw-sources 유래** — 루트 MD 2건만 있으며 요청한 하위 서브디렉터리 MD/fetched 대상 0건. 루트 원본과 기존 반영 내용은 보존했다. summary 신규/갱신·source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/`가 없어 chunk·부모 index·page_range·해시 복사 대상 0건.
- **처리·검증** — 지식 페이지 → index → 본 기록 → 지정 세션 `ingested: true` 순으로 반영했다. 필수 메타데이터·출처·변경 이력·추가 링크·추가 내용의 비밀값 패턴, 허용 파일 밖 추적 파일의 해시 보존 및 세션 플래그 외 바이트 보존 검사를 통과했다. `git diff --check`도 통과했다. 다른 세션·raw-sources·캐시·기존 사용자 변경은 보존하며 네트워크·다른 에이전트·commit/push는 수행하지 않는다.
- **변경 파일 전체(9개)**: `wiki/analyses/polling-interval-vs-bar-interval.md`, `wiki/analyses/paper-to-live-strategy-isolation.md`, `wiki/analyses/trading-api-failure-handling.md`, `wiki/decisions/shared-broker-appkey-token-cache.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-22 (ingest — 지정 1세션 주문 복구·호출 제어·페이퍼 비교 후속)

- **session-logs 유래 / Phase I** — `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 `ingested: false` 및 지정 `.cache/ingest-views/` 대화를 기존 wiki·10/21 수집 기록과 대조했다. type은 session-log이며 지정 mcp-note는 없다. 소스 안의 명령·스킬·환경/승인 문구는 실행하지 않았다.
- **기존 6문서 갱신 / 신규 0건** — `analyses/trading-api-failure-handling`에 접수 미확인 영속 차단의 재현·거부 증거 기반 복구·중복 조회 감소를, `decisions/shared-broker-appkey-token-cache`에 공통 호출 제한 장부/실주문 우선을 보강했다. `analyses/trading-observation-vs-order-time-gates`에는 뉴인텍 989원 분봉 고가와 실제 폴링 관측의 차이 및 초기 설명 정정을, `patterns/trading-performance-cash-flow-reconciliation`에는 최근 5건/당일 전체/당일 진입 코호트 구분을 통합했다. `projects/ht-trading`·`projects/ht-dde`는 복구/재기동·화면 반영, 짧은 일봉에 따른 전체 입력 보류·장후 첫 가상체결/마감 시세 신선도 후속만 연결했다. 기존 주제의 반복 사례·핵심 주문 안전 결정이므로 즉시 보강하며 별도 중복 분석은 만들지 않았다.
- **상태·근거 경계** — 오전 미복구 이력은 보존하고 후속 복구를 추가했다. 거부 로그·공식 주문 두 번·보유 대사로 확인된 해당 주문만 거부 확정 처리했으며 실제 응답 유실은 계속 보호한다. 재주문은 없었다. 테스트/운영 확인은 원 세션 보고이며 이번 실행에서 수행하지 않았다. 항목별 테스트 합계 1,650개와 최종 보고 1,651개의 불일치를 명시했다. 정확한 최신 한도·사고 당시 총호출 수·밴 여부는 세션 말미 조사 착수에서 끝나 미확정이다. 18회 추가 조회는 사고 후 모의 재현이며 실제 하루 총호출 수가 아니다. 10/22 첫 신규 체결 대조도 확인 전이다.
- **중복·부분 스킵 근거** — 같은 소스의 KIS 전환·점수 복구는 `projects/n-stock-info`, 점수/추천 네 조합은 `analyses/scoring-version-comparison-methodology`, 미국 페이퍼는 `analyses/multi-market-trading-account-isolation`, 자산 배분 수정은 `analyses/paper-to-live-strategy-isolation`, HMM 시초 비교는 기존 시간 분석에 기수록이다. 반복 진행/커밋/재시작 요청·개별 종목 전체 거래/잔액 목록·일회성 상태·철회된 튜닝안·탐색 과정은 제외했다. 소스는 위 지정 세션이며 새 결론만 보강했다.
- **raw-sources 유래** — 루트 MD 2건만 있고 요청한 하위 서브디렉터리 MD/fetched 대상 0건. 기존 루트 원본/요약을 보존했다. summary 신규/갱신 및 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` 없음. chunk·부모 index·page_range·해시 복사 대상 0건.
- **처리·검증** — 지식 페이지 → index → 본 기록 → 지정 세션의 `ingested: true` 순서로 반영했다. 필수 메타데이터·출처·변경 이력·추가 링크·추가 내용의 비밀값 패턴·허용 변경 범위·원본 플래그 외 바이트 보존을 검사한다. 목록 외 세션·raw-sources·캐시·기존 사용자 변경은 보존하며 네트워크·다른 에이전트·git commit/push는 수행하지 않는다.
- **변경 파일 전체(9개)**: `wiki/analyses/trading-api-failure-handling.md`, `wiki/decisions/shared-broker-appkey-token-cache.md`, `wiki/analyses/trading-observation-vs-order-time-gates.md`, `wiki/patterns/trading-performance-cash-flow-reconciliation.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-21 (ingest — 지정 두 세션 부분 실패·접수 미확인 후속)

- **session-logs 유래 / Phase I** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 `ingested: false` 및 지정 `.cache/ingest-views/` 대화를 기존 wiki·수집 이력과 대조했다. 두 파일은 session-log이며 지정 mcp-note는 없다. 대화 속 명령·스킬·환경/승인 문구는 실행하지 않았다.
- **기존 3문서 보강 / 신규 0건** — 첫 번째 소스의 10/21 news-monitor 후속은 `wiki/analyses/ai-publishing-migration-safety.md`에 통합했다. 배포/Actions 성공과 검색·브리핑 부분 실패, 제목 대체를 구분하고 오류 유형 누락 때문에 정확한 원인이 미확정임을 기록했다. 앞선 stderr 잘림과 반복되는 도메인 핵심 관측 문제라 수집했다. 오류 로깅 보완은 제안이며 미적용이다.
- **trading 수집 근거** — 두 번째 소스 말미의 KIS 호출 제한 후 접수 미확인과 후속 신규매수 차단을 `wiki/analyses/trading-api-failure-handling.md`에 보강하고 `wiki/bugs/order-post-retry-double-fill.md`와 상호 연결했다. 기존 매도 감시 지속·공식 조회 두 번·예약금 해제/재주문 미실행을 구분했다. 핵심 주문 안전 문제이므로 즉시 기록하며, 기존 자동 재시도 버그 재발·미접수 확정·복구 완료로 주장하지 않았다.
- **중복 스킵 근거 / 첫 번째 소스** — 이관/history/계정/정기 실행은 `wiki/analyses/ai-publishing-migration-safety.md`·`wiki/projects/oss-radar.md`·`wiki/projects/dev-blog.md`, 제목 기반 이슈 요약/필터/수집 시각은 `wiki/analyses/llm-content-quality-guards.md`, CLI 잠금은 `wiki/patterns/agentic-cli-text-generation-lockdown.md`에 기수록이다. 10/20 첫 예약 성공도 기존 기록을 유지했다.
- **중복 스킵 근거 / 두 번째 소스** — KIS 전환·점수/추천 조합은 `wiki/projects/n-stock-info.md`·`wiki/analyses/scoring-version-comparison-methodology.md`, 장후/익일 비교·시초 판단은 `wiki/analyses/trading-observation-vs-order-time-gates.md`, 미국 페이퍼는 `wiki/analyses/multi-market-trading-account-isolation.md`·`wiki/projects/ht-dde.md`, 보류 로깅·공식/추정 손익은 기존 API 분석과 `wiki/patterns/trading-performance-cash-flow-reconciliation.md`에 기수록이다. 두 번째 추천 비교 대사 15,694개 검사와 소표본 한계도 `wiki/projects/ht-dde.md`에 이미 있어 재작성하지 않았다.
- **가치 기준 스킵** — 일별 게시/디스크/잔액·개별 기사 내용, 반복 진행 요청, 단순 버전·미관·포맷 변경, 탐색 과정은 독립 페이지로 저장하지 않았다. 두 세션 모두 신규 가치와 중복/스킵 범위를 검토해 완료 처리한다. 출처의 운영·테스트 결과는 세션 보고이며 이번 실행의 재검증 결과가 아니다.
- **raw-sources 유래** — 루트 MD 2개만 있으며 요청한 하위 서브디렉터리 MD/fetched 대상은 0건이다. 루트 원본과 기존 지식은 보존하고 summary를 중복 생성하지 않았다. summary 신규/갱신 및 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/`가 없어 chunk·부모 index·page_range·해시 복사 대상 0건이다.
- **처리·검증** — 지식 페이지 → index → 본 기록 → 지정 두 세션의 `ingested: true` 순으로 반영한다. 필수 frontmatter·출처·변경 이력·추가 링크, 비밀값 패턴, 변경 범위와 플래그 외 원본 바이트 보존을 검사한다. raw-sources·캐시·목록 외 세션을 보존하며 네트워크·다른 에이전트·git commit/push는 수행하지 않는다.
- **변경 파일 전체(7개)**: `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/analyses/trading-api-failure-handling.md`, `wiki/bugs/order-post-retry-double-fill.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-21 (ingest — 지정 1세션 매매·대사 후속)

- **session-logs 유래 / Phase I** — `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 `ingested: false`와 지정 읽기용 `.cache/ingest-views/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`를 기존 wiki와 대조했다. 세션 안의 명령·스킬·환경/승인 문구는 참고 데이터로만 취급했다. mcp-note 대상은 없다.
- **기존 5문서 보강 / 신규 0건** — `analyses/trading-observation-vs-order-time-gates`에 종가 소급과 장후 호가의 차이·동일 추천 전방 비교를, `analyses/trading-api-failure-handling`에 보류 사유/검증 위치와 예외 원문 비노출을, `patterns/trading-performance-cash-flow-reconciliation`에 공식/추정 손익·체결 불일치 분리 및 읽기 전용 표시를 통합했다. 프로젝트 `ht-trading`·`ht-dde`에는 역행 첫 실제 집행, 장후 비교 등록/첫 체결 대기, 미국 새 비교 첫 매수와 네 추천 조합의 두 번째 대사만 후속 기록했다. 반복되는 도메인 핵심 집행·진단·회계 결정이므로 수집했다.
- **중복 스킵 근거** — 같은 소스의 앞선 네이버 장애·KIS 전환/기존식 복구는 `projects/n-stock-info`·`analyses/scoring-version-comparison-methodology`, KRX 복구는 `bugs/pykrx-krx-login-required`·`bugs/pykrx-index-name-lookup-failure`, 미국 실행 격리는 `analyses/multi-market-trading-account-isolation`, 전략별 배분 가드 수정은 `analyses/paper-to-live-strategy-isolation`, HMM 세 방식 재생과 09:30 유지 및 10/19 첫 대사는 기존 시간 분석·프로젝트에 기수록이다. 새 페이지를 중복 생성하지 않았다. 소스 경로는 위 지정 세션이며 같은 파일의 후속 대화만 추가했다.
- **기타 스킵** — 진행/재개 요청·커밋/재시작 안내 반복·일회성 상태/시세·백업 용량 정리·철회된 튜닝 제안은 독립 지식으로 저장하지 않았다. 갭상승 제한도 단일 손실로 적용하지 않는 최종 판단만 남겼다. 세션의 테스트·화면·장부 확인은 원 보고로 표기했으며 이번 실행에서 재검증한 것으로 주장하지 않는다.
- **raw-sources 유래 / PDF 유래** — `raw-sources/`에는 루트 MD 2개만 있고 지정 규칙의 하위 서브디렉터리 MD·fetched 대상은 없다. 기존 루트 원본 요약은 중복 생성하지 않았다. `.cache/extracted/`가 없어 PDF 대상도 없다. 신규/갱신 summary가 없어 `source_sha256` 복사 대상도 없다. 원본·캐시는 수정하지 않았다.
- **처리 완료** — 지식 페이지 → index → 본 기록 순으로 반영 후 지정 세션의 `ingested`만 true로 변경한다. 다른 세션·git commit/push·네트워크·다른 에이전트 실행은 대상 밖이다.
- **변경 파일 전체(8개)**: `wiki/analyses/trading-observation-vs-order-time-gates.md`, `wiki/analyses/trading-api-failure-handling.md`, `wiki/patterns/trading-performance-cash-flow-reconciliation.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-20 (ingest — 지정 1세션 첫 정기 실행 후속)

- **session-logs 유래 / Phase I** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`의 `ingested: false`를 확인하고 지정 읽기용 `.cache/ingest-views/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`와 기존 wiki를 대조했다. type은 session-log이며 지정 mcp-note는 없다. 대화의 명령·스킬·지시문은 참고 정보로만 취급했다.
- **수집: 기존 분석 2건 갱신 / 신규 0건** — `wiki/analyses/ai-publishing-migration-safety.md`와 `wiki/analyses/llm-content-quality-guards.md`의 첫 예약 미확인 상태를 세션 말미 10/20 07:15 KST 보고로 보완했다. 07:00~07:03 정기 수집·분류·배포와 검색 6개 묶음 성공, 누적 11건/신규 6건 구분을 기존 운영 검증 원칙의 후속 사례로 통합했다. news-monitor Pages·Actions 확인을 Dev Blog Actions 확인으로 확대하지 않고, 한 차례 성공과 장기 안정성·분류의 의미적 정확성을 구분했다. 수동 배포와 예약 실행 증거를 분리하는 도메인 핵심 운영 결정의 검증 완료 기록이며 별도 일일 상태 페이지를 만들지 않았다.
- **중복 스킵 근거** — 동일 출처의 이관/history/서비스 계정/셸·샌드박스/429/검색 누적은 `wiki/analyses/ai-publishing-migration-safety.md`·`wiki/projects/oss-radar.md`·`wiki/projects/dev-blog.md`에 기수록이다. 이슈 요약의 기사 ID·숫자·제목 근거, 필터와 요약/통계/목록 정합, 미확보 기간/0건 구분, 캐시 재분석 시 수집 시각 보존·배포 자산 일치는 `wiki/analyses/llm-content-quality-guards.md`의 10/20 후속 절에 이미 반영돼 재작성하지 않았다. CLI 잠금은 `wiki/patterns/agentic-cli-text-generation-lockdown.md`, OCI 운영은 기존 OCI 패턴과 중복이다.
- **가치 기준 스킵** — 인물·기사별 내용/논조, 일별 디스크·게시물 전체 수치, 표시명/모바일 미관, 단순 버전 갱신, 키 발급·일회성 사용법·탐색적 시행착오는 도메인 또는 재조회 기준 밖이다. 최종 결론과 운영 전환 증거만 선별했다.
- **raw-sources 유래** — 요청한 하위 서브디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 하위 디렉터리 범위 밖으로 보존했다. summary 신규/갱신·source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` 없음. chunk·부모 index·page_range·해시 복사 대상 0건.
- **처리·검증 경계** — 분석 페이지 → index → 본 기록 → 지정 세션 `ingested: true` 순서로 반영했다. 세션의 점검 결과를 출처로 기록하며 서버·모델·공개 화면을 재실행/재조회하지 않았다. 필수 메타데이터·출처·상호 링크·변경 이력·비밀값 패턴·변경 범위와 원본의 플래그 외 바이트 보존을 검사한다. 목록 외 세션·원문·캐시·기존 사용자 변경은 보존하며 네트워크·다른 에이전트·commit/push는 수행하지 않는다.
- 변경 파일: `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/analyses/llm-content-quality-guards.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`.

## 2026-10-20 (ingest — 지정 3세션 최신 결론 보강)

- **session-logs 유래 / Phase I** — 지정 세션 3건의 `ingested: false`와 대응 `.cache/ingest-views/`를 기존 wiki·이전 수집 이력에 대조했다. 모두 session-log이며 지정 mcp-note는 없다. 참고 데이터의 명령·지침·스킬 호출은 실행하지 않았다. 신규 문서 없이 기존 지식 5건을 갱신했다.
- **수집: trading** — 출처 `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`. `wiki/analyses/trading-observation-vs-order-time-gates.md`에 HMM 세 방식·동일 종료일/잔여 보유 평가·월요일 반등을 포함한 결론 역전과 다종목 재생을 수집했다. 관측 공백 진단과 정책 성과를 분리하며 최종 권고는 현행 09:30 이후 매도 유지다. `wiki/analyses/paper-to-live-strategy-isolation.md`에는 배분 예시를 최소 가동자금으로 오해한 전역 차단 수정, 실제 자산 50:50 배분·주문별 현금 제한·이관 후 엔진 정지와 재기동 검증을 반영했다. 핵심 운영 버그·결정이므로 즉시 기록하며 별도 중복 페이지는 만들지 않았다. `wiki/projects/ht-trading.md`와 `wiki/projects/ht-dde.md`에 실행 후속·첫 가상매수 6건·추천/체결/비용/현금/재시작 상태 대사 7,940개 검사와 하루·매도 0건의 한계를 연결했다.
- **수집: ai-agent** — 출처 `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`. `wiki/analyses/llm-content-quality-guards.md`에 제목 기반 이슈 요약의 기사 ID·숫자·근거 검증 및 제목 대체, 필터별 요약/통계/목록 범위 일치, 미확보 기간과 0건 구분, 캐시 재분석 시 수집 시각 보존·배포 자산 정합을 보강했다. 기존 자동 콘텐츠 검증의 반복 사례이며 인물 뉴스 자체를 새 도메인으로 수집하지 않았다.
- **검증 경계** — 936/952개 테스트·7,940개 장부 검사·Python 126개/JS 7개·모델/배포 결과는 세션 보고다. 이번 실행에서 코드·계좌·서버·모델·공개 화면을 재검증하지 않았다. 시초 백테스트는 실제 진입 고정 가상 재생과 전체 자금 재생을 구별하고 실제 수익으로 쓰지 않았다. 역행 다음 진입창의 실제 접수·체결·잔량 취소, 약 379원 손익 차이, 미국 새 비교 페이퍼 첫 매매·애프터 자동매매는 미확인/미완료다. 뉴스 모니터의 10/20 01시경 수동 배포 확인은 07시 첫 예약 성공의 근거가 아니다.
- **중복·부분 스킵 근거** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`의 이관/history/공용 계정/예약 증거/429/검색 누적은 `wiki/analyses/ai-publishing-migration-safety.md`·`wiki/projects/oss-radar.md`·`wiki/projects/dev-blog.md`에 기수록이다. `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 KIS 전환·점수 복구·재무 캐시는 `wiki/projects/n-stock-info.md`·`wiki/analyses/financial-data-freshness-and-cache.md`, 네 추천 계약은 `wiki/analyses/scoring-version-comparison-methodology.md`, 주문/API·트레일링 오류는 기존 해당 분석/버그에 기수록이다. 일별 전체 종목/잔액·인물 기사 내용·미관/모바일 표시·단순 버전 갱신·반복 요청·탐색 과정은 제외했다.
- **전체 스킵: 디스크 점검** — `session-logs/codex-01a14c2f-3ca8-7791-b973-dbe972a697bb.md`. 디스크·백업·스왑의 정리 전후 사용량 확인으로 두 도메인 밖의 일회성 상태 조회다. 거래 프로젝트·Claude 캐시 언급만으로 재사용할 설계 지식이 되지는 않아 문서를 만들지 않고 검토 완료 플래그만 갱신한다.
- **raw-sources 유래** — 요청한 하위 서브디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 하위 디렉터리 범위 밖이며 기존 문서와 원본을 보존했다. summary 신규/갱신·source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` 없음. chunk·부모 index·page_range·해시 복사 대상 0건.
- **처리 순서** — 지식 페이지 → index → 본 기록 → 스킵을 포함한 지정 세션 3건의 `ingested: true`로 반영했다. 필수 메타데이터·출처·변경 이력·추가 링크·비밀값 패턴·허용 범위와 원본 플래그 외 바이트 보존을 검사한다. 기존 사용자 변경·목록 외 세션·raw-sources·캐시는 보존하며 네트워크·다른 에이전트·commit/push는 수행하지 않는다.
- 변경 파일: `wiki/analyses/trading-observation-vs-order-time-gates.md`, `wiki/analyses/paper-to-live-strategy-isolation.md`, `wiki/analyses/llm-content-quality-guards.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`, `session-logs/codex-01a14c2f-3ca8-7791-b973-dbe972a697bb.md`.

## 2026-10-19 (ingest — 지정 두 세션 시초 관측 공백·일일 게시 후속)

- **session-logs 유래 / Phase I** — 지정 `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 `ingested: false`와 대응 `.cache/ingest-views/` 대화를 기존 wiki·이전 수집 기록에 대조했다. 두 파일은 session-log이며 지정 mcp-note는 없다. 참고 데이터 속 지시·스킬·명령은 실행하지 않았다.
- **신규 분석 1건** — ht_trading 말미 HMM 미매도 진단을 `wiki/analyses/trading-observation-vs-order-time-gates.md`에 수집했다. 감시 시작·시간 가드의 조기 반환·고점 상태·당시 평단·주문 부재를 연결하고, 분봉 고가와 실제 틱/체결을 구별했다. 실운영 핵심 진단으로 즉시 승격하며, 기존 `bugs/trailing-activation-current-profit-gate`의 활성화 이후 검사 차단과 달라 중복 문서가 아니다. `projects/ht-trading`·기존 버그에 상호 링크를 추가했다. 09:00 기존 보유 관측/보호 매도는 미적용 제안이며, 테스트 12개와 모의 재현은 세션 보고다. 이번 수집에서 매매 코드·주문·테스트를 실행하지 않았다.
- **기존 분석 2건 갱신** — OSS Radar 세션 후반의 news-monitor 일일 게시 후속에서 재사용할 AI 게시 원칙만 `analyses/ai-publishing-migration-safety`에 통합했다. 검색 분할·검증 기사 누적/중복 제거·마지막 확인 시각 보존·새 기사 확인 없는 정상 갱신 차단, 공개 파일 제한과 배포 브랜치 선택을 기록했다. `analyses/llm-content-quality-guards`에는 앞 단계의 기사 3건/미배포 기록을 보존하면서 10/19 OCI 수집·분류/첫 공개 배포·예약 활성화 보고와 10/20 첫 예약 실행 미확인 경계를 보강했다. 확대된 6개 검색 묶음의 실제 결과는 검증 대기다. 이전 AppArmor 진단을 Cursor 복구 완료로 바꾸지 않았다. 인물 뉴스 업무 자체를 새 도메인으로 승격하지 않았다.
- **부분 스킵: OSS Radar** — 이관·push 이후 history·공용 계정·정기 실행 증거는 `analyses/ai-publishing-migration-safety`, `projects/oss-radar`, `projects/dev-blog`에 기수록이다. 인물/기사별 내용·분류 결과·표시명/미관·일별 상태/디스크 수치·단순 CLI/모델 갱신·키 발급·탐색 과정은 범위 밖 또는 재조회 가치가 낮아 제외했다. 소스: 위 codex-01a0d80a 세션.
- **부분 스킵: ht_trading** — 수집 소스 전환·점수 복구는 `projects/n-stock-info`, 재무 보완/장애 대체/일일 캐시는 `analyses/financial-data-freshness-and-cache`, 네 추천 조합은 `analyses/scoring-version-comparison-methodology`, 역행·미국 페이퍼는 `projects/ht-trading`·`analyses/paper-to-live-strategy-isolation`·`analyses/multi-market-trading-account-isolation`, 주문/API 실패는 `bugs/kis-fully-rejected-order-status`·`analyses/trading-api-failure-handling`에 기수록이다. 반복 요청·개별 잔액/종목 전체 목록·백업 정리·미확정 탐색은 제외했다. 다음 날 TODO 등록을 자동 감시 예약이나 애프터 자동매매 완료로 기록하지 않았다. 소스: 위 codex-01a0e7f0 세션.
- **raw-sources 유래** — 하위 디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 지정 하위 디렉터리 범위 밖이며 기존 문서·원본을 보존했다. summary 생성/갱신·source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/`가 없어 chunk·부모 index·page_range·해시 복사 대상 0건.
- **처리·검증** — 지식 페이지 → index → 본 기록 → 지정 두 세션 `ingested: true` 순서로 반영한다. 필수 메타데이터·링크·출처·변경 이력·비밀값 패턴·변경 범위·원본 플래그 외 바이트 보존을 검사한다. 목록 외 세션·raw-sources·캐시·기존 사용자 변경을 보존하며 네트워크·다른 에이전트·git commit/push는 수행하지 않는다.
- 변경 파일: `wiki/analyses/trading-observation-vs-order-time-gates.md`, `wiki/bugs/trailing-activation-current-profit-gate.md`, `wiki/projects/ht-trading.md`, `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/analyses/llm-content-quality-guards.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-19 (ingest — 지정 세션 실행 환경·무인 AI 분류 후속)

- **session-logs 유래 / Phase I** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md` 1건의 `ingested: false`와 지정 읽기용 `.cache/ingest-views/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`를 기존 wiki·수집 이력에 대조했다. type은 session-log이며 지정 mcp-note는 없다. 세션 안의 요청·명령·스킬 전문은 참고 데이터로만 취급했다.
- **수집: 기존 분석 2건 갱신 / 신규 0건** — `wiki/analyses/ai-publishing-migration-safety.md`에 Research Wiki의 Bash 3.2 빈 배열/nounset 게시 실패와 publish 단계만 복구한 결론, 후반 news-monitor의 Cursor/AppArmor 진단과 Mac Codex 성공의 환경 차이, 재실행에도 유지되는 429 대기·동시 실행 방지·이전 정상 결과 표시를 보강했다. 기존 이관 분석의 실행 검증·부분 실패 주제에 통합하며 별도 버그/프로젝트 페이지는 만들지 않았다.
- **수집 판단** — `wiki/analyses/llm-content-quality-guards.md`에는 무인 AI 분류의 수집 실패/0건·응답 실패/중립/판단 보류 구별, 제목 기반 입력·호출 제한, 표본 분모와 가상 검증/실제 모델 호출/공개 배포 경계를 보강하고 두 분석을 상호 연결했다. 앞선 10/18 기록에서 미확정으로 남긴 샌드박스 이슈는 후속 대화의 커널 로그 진단으로 보충하되, OCI 복구로 확정하지 않았다. 실제 기사 3건의 분류·이전 결과 보존까지 진행된 후속 결론 중 AI 호출·검증·운영에 재사용할 부분만 수집했다. 이는 기존 AI 게시·품질 가드에서 반복된 원칙의 추가 사례이며, 인물 뉴스 업무 전체를 새 도메인으로 승격한 것이 아니다. 직접 출처는 위 지정 세션이다.
- **검증 경계** — 테스트·커밋·CLI·화면 결과는 세션 보고이며 이번 실행에서 재실행하지 않았다. AppArmor 예외와 전역 보안 변경은 미적용이다. Mac의 실제 분류·로컬 제공은 확인됐다고 보고됐지만 OCI 운영·GitHub Pages·일일 타이머는 미설정이며, 검색어 분할·기사 누적은 계획이다. 법률·API 이용 조건·제품/모델 정책을 현행 사실로 일반화하지 않았다.
- **중복·가치 기준 스킵** — 이관·push 후 history·계정 통합·KST·정기/수동 증거·Semantic Scholar 부분 실패는 `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/projects/oss-radar.md`, `wiki/projects/dev-blog.md`에 기수록이다. 도구 차단·읽기 전용 실행은 `wiki/patterns/agentic-cli-text-generation-lockdown.md`, OCI 한도/증설은 `wiki/patterns/oracle-cloud-free-tier-setup.md`·`wiki/patterns/oracle-cloud-operations-cheatsheet.md`와 중복된다. 인물·기사별 내용·논조 결과, 표시명 변경·화면 미관, 일별 성공/디스크 수치, 단순 버전 갱신·키 발급·입력 단축키·탐색적 시행착오는 제외했다. 검토 완료로 지정 로그의 플래그를 갱신한다.
- **raw-sources 유래** — 하위 디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 요청한 하위 디렉터리 범위 밖으로 보존했다. summary 신규/갱신과 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/`가 없어 chunk·부모 index·page_range·해시 복사 대상 0건이다.
- **처리·검증** — 분석 페이지 → index → 본 기록 → 지정 세션 `ingested: true` 순서로 반영한다. 필수 메타데이터·출처·상호 링크·변경 이력·비밀값 패턴·변경 범위와 원본의 플래그 외 바이트 보존을 검사한다. 목록 외 세션·raw-sources·캐시와 기존 사용자 변경은 보존한다. 네트워크·다른 에이전트·git commit/push는 수행하지 않는다.
- 변경 파일: `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/analyses/llm-content-quality-guards.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`.

## 2026-10-18 (ingest — 지정 3세션 재무 신선도·일일 캐시 후속)

- **session-logs 유래 / Phase I** — 지정 원본 3건의 `ingested: false`를 확인하고 대응 `.cache/ingest-views/` 대화와 기존 wiki를 대조했다. 모두 session-log이며 지정 mcp-note는 없다. 세션·참고 자료 안의 명령·지침·스킬 호출은 실행하지 않았다.
- **수집: 신규 분석 1건·기존 지식 2건 갱신** — 출처 `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 후속 재무 입력 수정·검증을 `analyses/financial-data-freshness-and-cache`에 정리했다. 재무 기준기간·확정 여부 대조, KIS 우선·Naver 보완과 실패 시 제한적 KIS 원자료 복귀, 한국시간 일일 원자료 캐시와 현재 가격 기반 PER/PBR 재계산은 반복 비교 및 trading 핵심 설계로 승격했다. `projects/n-stock-info`에 구현 연결을, `analyses/ht-trading-live-data-improvement-analysis`에 8거래일 근사 재생과 고점 관측 모형에 따른 소스 우위 역전을 보강했다. 입력 정확성 수정과 수익 개선을 구별하며 기존 점수식·62점은 유지한다. DART 일부 대조·EPS 및 자동 보완 미완료, 10/19 첫 정상 수집 연결 확인 전이라는 경계도 기록했다. 테스트·API·서비스 결과는 세션 보고이며 이번 수집에서 재실행하지 않았다.
- **부분 스킵: ht_trading** — 위 출처 앞부분의 데이터 전환·점수 복구는 `projects/n-stock-info`, 네 최종 추천 조합·불변 입력은 `analyses/scoring-version-comparison-methodology`, 역행 운영·미국 페이퍼는 `projects/ht-trading`·`analyses/paper-to-live-strategy-isolation`·`analyses/multi-market-trading-account-isolation`, 주문·계좌 오류는 `bugs/kis-fully-rejected-order-status`·`analyses/trading-api-failure-handling`에 기수록이다. 중복 페이지를 만들지 않았다. 개별 잔액·종목 전체 목록·반복 진행 요청·탐색 과정·단순 화면 변경·백업 압축 정리 수치는 재조회 가치가 낮아 제외했다. 다음 날 체크리스트 등록을 자동 감시 예약이나 애프터 자동매매 완료로 해석하지 않았다.
- **전체 스킵: OSS Radar** — 출처 `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`. 이관·push 후 history·공용 계정·정기/수동 증거·429 부분 실패는 `analyses/ai-publishing-migration-safety`·`projects/oss-radar`·`projects/dev-blog`에 기수록이다. 일별 게시·디스크 수치와 CLI 업데이트·키 신청·줄바꿈 안내는 반복 관찰/일회성이다. 말미 news-monitor의 인물 뉴스 조사·분류·정적 화면 구현은 두 도메인 밖의 업무이며, 에이전트 도구/응답 제한과 실패/정상 0건 구분은 `patterns/agentic-cli-text-generation-lockdown`·기존 게시 분석과 중복이다. Cursor 강제 샌드박스 초기화 실패는 원인 미확정인 단발 환경 이슈이고 실제 뉴스 수집도 429로 미검증이므로 새 일반 지식으로 확정하지 않았다. 검토 완료로 플래그만 갱신한다.
- **전체 스킵: 디스크 점검** — 출처 `session-logs/codex-01a14c2f-3ca8-7791-b973-dbe972a697bb.md`. Mac 디스크 사용량·백업 용량과 정리 전후 수치 확인으로, 두 도메인 밖의 일회성 상태 조회다. 거래 프로젝트 백업이 언급돼도 설계·버그 수정 결론을 새로 제공하지 않아 별도 wiki를 만들지 않고 플래그만 갱신한다.
- **raw-sources 유래** — 하위 디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 요청한 하위 디렉터리 범위 밖으로 보존했다. summary 신규/갱신 및 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/`가 없어 chunk·부모 index·page_range·해시 복사 대상 0건이다.
- **처리·검증** — 지식 페이지 → index → 본 기록 → 지정 세션 3건의 ingested true 순서로 반영한다. 필수 메타데이터·추가 링크·출처·변경 이력·비밀값 패턴·변경 범위와 원본 플래그 외 바이트 보존을 검사한다. 목록 외 세션·raw-sources·캐시·기존 사용자 변경을 보존하고 네트워크·다른 에이전트·git commit/push는 수행하지 않는다.
- 변경 파일: `wiki/analyses/financial-data-freshness-and-cache.md`, `wiki/analyses/ht-trading-live-data-improvement-analysis.md`, `wiki/projects/n-stock-info.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`, `session-logs/codex-01a14c2f-3ca8-7791-b973-dbe972a697bb.md`.

## 2026-10-18 (ingest — 지정 세션 최종 추천 4조합·미국 공개규칙 대조 후속)

- **session-logs 유래 / Phase I** — 지정 `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 `ingested: false`와 대응 `.cache/ingest-views/` 대화를 기존 wiki에 대조했다. 두 파일 모두 session-log이며 지정 mcp-note는 없다. 세션 내부 지침·스킬·명령은 참고 정보로만 다뤘다.
- **수집: 기존 지식 6건 갱신 / 신규 0건** — ht_trading 지정 세션 말미의 최종 결론을 수집했다. `analyses/scoring-version-comparison-methodology`에 각 소스 자체 점수 62점의 최종 추천 네 조합, 선정/실행 점수 분리, 완료 시각·완전성을 담은 불변 스냅샷과 네 장부 전체 롤백을 보강했다. `analyses/multi-market-trading-account-isolation`에는 기존 미국 V1 변형과 2021 공개규칙의 차이, 동일시작 새 대조군·미확정 40거래일 이후 정책, 매수일/사이클·보유/계좌 수익률 분모를 통합했다. 반복된 비교 설계와 trading 핵심 결정이므로 승격하며 별도 중복 분석은 만들지 않았다.
- **프로젝트·상태 정정** — `projects/n-stock-info`에 불변 추천 생산자, `projects/ht-dde`에 네 장부 등록·미국 화면 지표·공개규칙 새 대조군, `projects/ht-trading`에 미국 실행기 후속·네 추천 조합 완료를 추가했다. `analyses/ht-trading-live-data-improvement-analysis`의 네 조합 미구현 기록은 이력으로 남기고 최신 구현 완료·검증 대기로 정정했다. 구체적 소스는 위 ht_trading 지정 세션이며 생산자 `b3c0ed1`·페이퍼 `9b11589`는 세션 보고 커밋이다.
- **검증 경계** — 구현·등록·재시작과 테스트/장부→보고서→API→HTML 대조는 세션의 결과 보고다. 이번 수집에서 원본 시스템이나 테스트를 재실행하지 않았다. 10/19 국내 네 조합 첫 정상 수집·가상 체결·마감, 미국 새 장부 첫 체결과 원형의 40거래일 이후 손실 처리·추가 자금 확정은 남아 있다. 미국 실주문은 비활성이다. 화면 데이터 대조와 브라우저 육안 확인(연결 제한으로 미실시)을 구별한다.
- **전체 스킵: OSS Radar 세션** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`의 이관·push 후 history·공용 계정·정기/수동 게시 증거·반복 429는 `analyses/ai-publishing-migration-safety`와 `projects/oss-radar`·`projects/dev-blog`에 기수록이다. 일별 성공 수치·디스크 변화·CLI 업데이트·입력 안내는 반복 운영 관찰/일회성 사용법으로 제외했다. 말미 news-monitor 조사·계획·수집기 구현은 인물 뉴스 업무로 두 도메인 밖이며, 실패/0건 구분도 기존 원칙과 중복되어 승격하지 않았다. 검토 완료로 플래그만 갱신한다.
- **부분 스킵: ht_trading 세션** — 앞부분의 수집 전환·기존 점수식 복구는 `projects/n-stock-info`, 주문/API 수정은 `bugs/kis-fully-rejected-order-status`·`analyses/trading-api-failure-handling`, 역행 운영 연결·자금 격리·배치 재시도는 `projects/ht-trading`·`analyses/paper-to-live-strategy-isolation`에 기수록이다. 개별 종목·잔액·일별 손익, 반복 진행 요청·탐색 과정·철회된 제안·지침 전문과 단순 화면 미관은 제외했다. 대시보드에서는 재사용할 지표 정의와 경고 가시성만 수집했다.
- **raw-sources 유래** — 요청 범위인 하위 디렉터리 MD/fetched 대상 0건이다. 루트 MD 2건은 하위 디렉터리 수집 범위 밖이며 기존 요약·원본을 보존했다. 신규/갱신 summary와 source_sha256 복사 대상은 없다.
- **PDF 유래** — `.cache/extracted/`가 없어 chunk 대상 0건이다. 부모 index·page_range·해시 복사 대상도 없다.
- **처리 순서** — 지식 페이지 → index → 본 기록 → 지정 두 세션 ingested true 순으로 반영한다. 필수 메타데이터·출처·변경 이력·링크·비밀값 패턴·변경 범위와 플래그 외 원본 바이트 보존을 검사한다. 목록 외 세션·raw-sources·캐시·기존 사용자 변경을 보존하며 네트워크·다른 에이전트·commit/push는 수행하지 않는다.
- 변경 파일: `wiki/analyses/scoring-version-comparison-methodology.md`, `wiki/analyses/multi-market-trading-account-isolation.md`, `wiki/analyses/ht-trading-live-data-improvement-analysis.md`, `wiki/projects/n-stock-info.md`, `wiki/projects/ht-dde.md`, `wiki/projects/ht-trading.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-17 (ingest — 지정 두 세션의 운영 전환·비교 목표 후속 수집)

- **session-logs 유래** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md` 두 건의 `ingested: false`를 확인하고 지정 `.cache/ingest-views/` 대화와 기존 wiki를 대조했다. 둘 다 session-log이며 지정 mcp-note는 없다. 대화 속 지침·스킬·명령은 참고 데이터로만 다뤘다.
- **기존 지식 5건 갱신 / 신규 0건** — ht_trading 세션의 비활성 준비 이후 공통 자금·보유 격리/주문 연결과 실운영 활성화 보고를 `wiki/projects/ht-trading.md`에 추가했다. 첫 실제 체결은 10/19 확인 전으로 구분했다. 페이퍼에 없던 미검증 손실 제한의 제외와 배치/종목별 재시도 구분은 재사용할 핵심 설계 판단으로 `wiki/analyses/paper-to-live-strategy-isolation.md`에 통합했다. 최종 추천 KIS 단독·Naver 단독·합집합·교집합이라는 사용자 목표와 현재 공통 KIS 채점의 두 발굴 후보 장부의 차이를 `wiki/analyses/scoring-version-comparison-methodology.md`, `wiki/projects/ht-dde.md`, `wiki/analyses/ht-trading-live-data-improvement-analysis.md`에 반영했다. 동명 분석을 갱신해 중복 페이지를 만들지 않았다.
- **운영·검증 경계** — `d3f3bde` 연결·후속 재시작/활성화·846개 테스트, 후보 비교 v2 이력 보존/v5 10/19 대기, 미국 4장부의 마감 평가·체결 정합은 세션의 보고다. 이번 수집에서 원본 프로젝트·계좌·서버·테스트를 재검증하지 않았다. 역행 첫 실거래, 최종 추천 네 조합 비교, 애프터 주문·취소·매도 감시와 비교 장부, 미국 실주문은 완료로 기록하지 않았다. 동주기 후속 주문 차단·기동 재시도 전략 중복 등록 수정은 프로젝트에만 기록했다. 정확한 코드 원인·재현 정보가 없어 별도 버그 문서로 확장하지 않았다.
- **OSS Radar 세션 전체 스킵 근거** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`의 이관·push 후 history·공용 계정·정기 실행 증거·429와 대체 소스 품질은 `wiki/analyses/ai-publishing-migration-safety.md`와 `wiki/projects/oss-radar.md`에 기수록이다. 날짜별 성공·디스크 숫자·키 신청·입력 줄바꿈은 반복 운영 관찰 또는 일회성 사용법으로 제외했다. 조현준 뉴스 모니터링 조사/계획은 인물 뉴스 업무가 중심으로 두 도메인의 핵심 재조회 지식에 해당하지 않으며, 분류·공개 시연 아이디어도 첫 등장으로 승격하지 않았다. 외부 법률·API·상품 정책을 이번 수집에서 현행 사실로 재확인하거나 새 문서로 일반화하지 않았다. 검토 완료로 플래그만 갱신한다.
- **ht_trading 세션 중복·가치 기준 스킵** — 같은 소스의 KIS 전환·기존식 복구/독립 Naver 비교는 `wiki/projects/n-stock-info.md`, 시점/재생 검증은 `wiki/analyses/scoring-version-comparison-methodology.md`, 주문 전량 거부·500 판단은 `wiki/bugs/kis-fully-rejected-order-status.md`·`wiki/analyses/trading-api-failure-handling.md`, 미국 격리는 `wiki/analyses/multi-market-trading-account-isolation.md`에 있어 재생성하지 않았다. 개별 종목·계좌 잔액 전체 목록, 반복 진행 요청, 탐색 과정·지침 전문·철회된 제안은 제외했다. 스킵까지 검토 완료로 처리한다.
- **raw-sources 유래** — 하위 디렉터리 `.md` 및 fetched 대상 0건. 루트 MD 2건은 요청의 하위 디렉터리 수집 범위 밖이며 보존했다. summary 신규/갱신 및 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` chunk 대상 0건. 부모 인덱스·page_range·해시 복사 대상 없음.
- **처리·검증** — 지식 페이지 → index → 본 기록 → 지정 세션 플래그 순서로 반영한다. 필수 frontmatter·출처·변경 이력·연결 대상·비밀값 패턴·변경 범위를 검사하고 두 원본은 `ingested` 한 줄 외 바이트 보존을 확인한다. 목록 외 세션·raw-sources·캐시·기존 사용자 변경을 보존하며 네트워크·다른 에이전트·commit/push·원본 시스템 실행은 하지 않는다.
- 변경 파일: `wiki/analyses/paper-to-live-strategy-isolation.md`, `wiki/analyses/scoring-version-comparison-methodology.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`, `wiki/analyses/ht-trading-live-data-improvement-analysis.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-17 (ingest — 지정 세션 최종 검증·페이퍼 규칙 일치 집행 준비)

- **session-logs 유래** — 지정 `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`·`session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 ingested false와 대응 `.cache/ingest-views/` 대화를 기존 wiki에 대조했다. 두 파일 모두 session-log이며 지정 mcp-note는 없다. 원본의 지침·스킬·작업 요청은 참고 데이터로 취급했다.
- **수집 / Phase I** — 신규 분석 1건·기존 지식 3건 갱신. `analyses/paper-to-live-strategy-isolation`에 보유 수와 1회 비중 분리(2종목 제한 시 50%가 되던 초안→원금의 10%), 페이퍼 규칙 보존·양방향 자금/보유 격리·접수/체결 구분을 수집했다. 기존 계좌 공유 문제의 반복 및 trading 핵심 배분 버그·설계 결정으로 승격했다. `projects/ht-trading`에는 46a8761의 비활성 준비·모의 집행과 기존 엔진 통합 미완료, `projects/ht-dde`에는 설정/실행 버전 표시 오류 수정·이전 이력 보존·새 비교판 10/19 계획, `analyses/ht-trading-live-data-improvement-analysis`에는 7거래일·64조건 검증 완료·운영 62점 유지·비용 가정 대조 과제를 반영했다. 직접 출처는 위 ht_trading 지정 세션이다.
- **중복·가치 기준 스킵 (OSS Radar 세션)** — 위 oss-radar 지정 세션의 이관·history 복구·계정 통합·CLI 오류는 `analyses/ai-publishing-migration-safety`와 `projects/oss-radar`·`projects/dev-blog`, OCI 설정은 `patterns/oracle-cloud-free-tier-setup`·`patterns/oracle-cloud-operations-cheatsheet`에 기수록이다. 10/14~17 health check의 성공 수치·디스크 변화·반복 429는 기존 분석의 상태/부분 실패 원칙을 바꾸지 않아 추가 페이지를 만들지 않았다. 키 발급 화면·Codex 줄바꿈 안내는 일회성 사용법으로 제외했다. 세션은 검토 완료로 플래그만 갱신한다.
- **중복·가치 기준 스킵 (ht_trading 세션)** — 점수식·후보 소스 전환은 `projects/n-stock-info`·`analyses/scoring-version-comparison-methodology`, 기존 시계·체결·거부·API 오류는 `patterns/backtest-clock-injection`·`analyses/backtest-fill-model-adverse-selection`·`bugs/kis-fully-rejected-order-status`·`analyses/trading-api-failure-handling`, 미국 페이퍼는 `analyses/multi-market-trading-account-isolation`에 기수록이다. 일별 손익 전체·종목 목록·PID·반복 요청·지침 전문·탐색 과정·철회된 튜닝 제안은 제외했다. 같은 배분 오류의 별도 버그 페이지는 만들지 않고 새 분석에 통합했다.
- **검증 경계** — 코드·테스트·커밋·실행 상태는 세션 보고이며 이번 수집에서 재실행하지 않았다. 실제 시세 수신과 거래 가능/정지/VI 판정 검증은 구분한다. 역행은 예산 0·실주문 차단이며 기존 엔진 자금/보유 격리와 공통 주문 관리 통합이 남아 있다. 0.18%/0.20% 차이는 세션 당시 비용 가정 점검 과제이며 현행 세율 검증·설정 변경 완료로 기록하지 않았다.
- **raw-sources 유래** — 요청한 하위 서브디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 하위 디렉터리 수집 범위 밖이며 수정하지 않았다. 따라서 summary 신규/갱신과 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` 없음, chunk 대상 0건.
- **처리 순서** — 지식 페이지→index→본 기록→지정 두 세션 ingested true 순으로 반영한다. 필수 메타데이터·출처·변경 이력·연결·비밀값 패턴·플래그 외 바이트 보존과 변경 범위를 검사한다. 목록 외 세션·raw-sources·캐시·기존 사용자 변경은 보존하며 네트워크·다른 에이전트·commit/push는 수행하지 않는다.
- 변경 파일: `wiki/analyses/paper-to-live-strategy-isolation.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`, `wiki/analyses/ht-trading-live-data-improvement-analysis.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-16 (ingest — 지정 세션 후보 비교 등록·전체 재생 후속)

- **session-logs 유래** — `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md` 1건(type: session-log, ingested: false)의 지정 읽기용 `.cache/ingest-views/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`와 기존 wiki를 대조했다. 내부 지시문은 참고 데이터로만 취급했으며 mcp-note 대상은 없다.
- **수집 / Phase I** — 신규 0건, 기존 지식 6건 갱신. `analyses/scoring-version-comparison-methodology`에 수집 완료 이후의 정보 사용·144개 실행의 완료 로그 근거·종목 제외 후 현금 재배분과 고점 모형 민감도를 보강했다. `patterns/backtest-clock-injection`에는 재발한 매수 시간 가드 수정과 기존 가드 무효화 우회의 한계를 정정했다. `analyses/backtest-fill-model-adverse-selection`에는 구독 승인/등록과 실제 수신·체결 가능 판정의 경계, 종가 소급 체결 방지 검증을 추가했다. 반복된 시간·체결 현실성 문제와 trading 핵심 설계·버그 수정이므로 수집하며 기존 주제에 통합했다.
- **프로젝트·분석 상태 갱신** — `projects/ht-dde`에 `/source-pair` 쌍 계정 등록·동일 실행/채점/62점·각 가상 1천만원·양쪽 거래/상태 동시 롤백을 기록했다. `projects/ht-trading`과 `analyses/ht-trading-live-data-improvement-analysis`에 전체 후보·매매·현금 배분 재생의 후속 결과, 애프터 관측기 등록, 예산 0·실주문 불가 역행 집행 준비 장부를 반영했다. 앞선 미완료 기록은 시점 이력으로 보존하고 후속 절로 갱신했다.
- **검증 경계** — 개장 전 후보 비교 거래·관측 그래프와 애프터 시세 수신은 없었다. 구독 3종 승인·08:55 등록은 실제 장중 수신·대상/정지/VI 판정 검증 완료가 아니다. 애프터 비교 매매와 역행 KIS 전송 어댑터 운영 연결은 미완료다. 최신 전체 재생의 최종 검증·커밋 정리는 대화 말미에 진행 중이며 최종 성과표·테스트 수를 보충하지 않았다. 세션의 코드·서비스·테스트 결과는 보고 사실이며 이번 실행에서 재검증하지 않았다.
- **중복 스킵 근거** — 같은 원본의 KIS 전환·기존 점수식 복구·독립 Naver 비교는 `projects/n-stock-info`, 최신 관측 선택 후 컷 적용은 `analyses/scoring-version-comparison-methodology`, 청산정합은 `bugs/trailing-activation-current-profit-gate`, 체결 손익은 `patterns/trading-performance-cash-flow-reconciliation`에 기수록이다. KRX 복구는 `bugs/pykrx-index-name-lookup-failure`, 미국 장부/보고는 `analyses/multi-market-trading-account-isolation` 및 두 프로젝트, 전량 거부·500 판단은 `bugs/kis-fully-rejected-order-status`·`analyses/trading-api-failure-handling`과 앞선 10/16 수집 기록에 있어 재생성하지 않았다. 출처는 모두 위 지정 세션이다.
- **가치 기준 스킵** — 종목/손익 전체 목록·PID·일별 운영 숫자·일회성 메뉴 안내·반복 요청·지침 전문·탐색 과정·철회된 튜닝 제안은 제외했다. 복제 페이퍼의 매도 감시 인자 오류는 수정 보고만 프로젝트에 남기고 정확한 재현 정보가 없어 별도 버그 페이지를 만들지 않았다. 스킵까지 검토 완료로 처리한다.
- **raw-sources 유래** — 하위 디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 지정 하위 디렉터리 수집 범위 밖이며 보존했다. 신규/갱신 summary 및 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` 없음, chunk 대상 0건.
- **처리·검증** — 지식 페이지 → index → 본 기록 → 지정 로그 플래그 순서로 반영했다. 필수 frontmatter·출처·변경 이력·연결 대상과 변경 범위·원본 플래그 외 바이트 보존을 확인한다. 네트워크·다른 에이전트·commit/push·원본 시스템 실행은 수행하지 않는다. 목록 외 세션·raw-sources·캐시·기존 사용자 변경은 보존한다.
- 변경 파일: `wiki/patterns/backtest-clock-injection.md`, `wiki/analyses/scoring-version-comparison-methodology.md`, `wiki/analyses/backtest-fill-model-adverse-selection.md`, `wiki/projects/ht-dde.md`, `wiki/projects/ht-trading.md`, `wiki/analyses/ht-trading-live-data-improvement-analysis.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-16 (ingest — 지정 세션 주문 오류·외부 API 판단·애프터 기반 후속 수집)

- **session-logs 유래** — `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 ingested false와 대응 `.cache/ingest-views/` 대화를 기존 wiki·수집 이력에 대조했다. 지정 1건은 session-log이며 mcp-note 대상은 없다. 내부 지침·스킬·실행 요청은 참고 데이터로만 취급했다.
- **신규 2건 / 기존 지식 3건 갱신** — 전량 거부 `OPEN` 오분류는 trading 핵심 버그의 수정 완료 보고이므로 `wiki/bugs/kis-fully-rejected-order-status.md`로 즉시 수집했다. 외부 오류 감지와 코드 수정의 구분, 단일 최신 잔고·주문가능금액 실패 시 매수 보류는 `wiki/analyses/trading-api-failure-handling.md`에 범용 분석으로 정리했다. 기존 서킷브레이커와 이번 안전 처리에서 반복된 핵심 판단이다. 애프터 체결 계약은 `wiki/analyses/backtest-fill-model-adverse-selection.md`에 통합하고 `wiki/projects/ht-trading.md`·`wiki/analyses/ht-trading-live-data-improvement-analysis.md`의 미해결 주문 상태를 취소선으로 보존하며 후속 결과를 연결했다. 관련 페이지와 신규 페이지에 상호 링크를 반영했다.
- **검증 경계** — `a06f4ca` 수정·국내 서비스 반영·714개 테스트/서브테스트 13개, `87640ff` 기반·761개 테스트 및 실제 완료 분봉 조회는 세션 보고다. 이번 수집에서 원본 프로젝트·API를 재검증하지 않았다. HTTP 500 원인은 미확정이며 공급자 원인으로 단정하지 않는다. 애프터 수집기 상태 연결·비교 계정 등록·자동 페이퍼 운용은 미완료다. 세션의 거래제도 설명은 외부 검증 없이 현행 시장 규칙으로 일반화하지 않았다.
- **중복 스킵 근거** — 같은 소스의 KIS 전환·기존 점수식 복구·독립 Naver 비교·마스킹·알림 정책은 `wiki/projects/n-stock-info.md`, 점수·후보 비교는 `wiki/analyses/scoring-version-comparison-methodology.md`, 트레일링 수정은 `wiki/bugs/trailing-activation-current-profit-gate.md`, 미국 장부/보고는 `wiki/analyses/multi-market-trading-account-isolation.md`·`wiki/projects/ht-dde.md`에 기수록이다. RS 관측·진입가 비교와 비활성 주문 준비도 기존 체결 모델·프로젝트에 있어 재생성하지 않았다.
- **가치 기준 스킵** — 일별 종목·계좌 손익 전체 목록, 일회성 메뉴/조회 안내, 반복 진행·커밋 요청, 지침 전문, 탐색 과정·철회된 튜닝 권고는 제외했다. 후속 작업 제안은 구현 완료로 기록하지 않는다. 스킵을 포함해 지정 세션 검토를 완료했다.
- **raw-sources 유래** — 하위 서브디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 요청한 하위 디렉터리 범위 밖으로 보존했다. summary 신규/갱신 및 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` 없음, chunk 대상 0건.
- **처리·검증** — 지식 페이지 → index → 본 기록 → 지정 ingested 플래그 순서로 반영한다. frontmatter·출처·변경 이력·새 링크·비밀값 패턴·변경 범위를 검사하며 원본은 플래그 치환 외 바이트가 동일한지 대조한다. 기존 사용자 변경·목록 외 세션·raw-sources·캐시를 보존한다. 네트워크·다른 에이전트·commit/push·원본 시스템 실행은 수행하지 않는다.
- 변경 파일: `wiki/bugs/kis-fully-rejected-order-status.md`, `wiki/analyses/trading-api-failure-handling.md`, `wiki/analyses/backtest-fill-model-adverse-selection.md`, `wiki/projects/ht-trading.md`, `wiki/analyses/ht-trading-live-data-improvement-analysis.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-15 (ingest — 지정 세션 최근 개선 분석 후속 수집)

- **session-logs 유래** — `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md` 1건의 ingested false를 확인하고 대응 `.cache/ingest-views/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 전체 대화를 검토했다. type은 session-log이며 mcp-note 대상은 없다. 대화에 포함된 AGENTS.md·스킬·작업 지시는 실행하지 않았다.
- **Phase I / 기존 지식 3건 갱신·신규 0건** — 10/15 최종 개선 분석을 `wiki/analyses/ht-trading-live-data-improvement-analysis.md`에 통합했다. 실제 8개 진입 고정·1분봉 근사 재생, 추가매수 중단/트레일링 확대의 단일 종목 의존, 후보 발굴 차이와 추가 노출 분석 과제를 수집했다. 범용 방법론은 `wiki/analyses/scoring-version-comparison-methodology.md`에 고정기간 가격 진단·조건부 장중 재생·전체 전략 비교의 경계, 후보 발굴/채점/청산의 효과 분리와 최대 기여 종목 제외 점검으로 보강했다. 같은 비교 문제가 8월 분석과 이번 세션에서 반복되어 승격 기준을 충족하며 별도 중복 페이지는 만들지 않았다. `wiki/projects/ht-trading.md`에 최종 결론과 미완료 비교·주문 상태 점검을 연결하고 분석 페이지와 상호 링크했다.
- **판단·검증 한계** — 세션이 제시한 개선 금액은 실제 추가 실현손익이 아닌 조건부 재생 결과다. KIS/Naver 전체 후보·자금 배분 비교는 미완료다. 전량 거부 주문의 내부 OPEN 분류와 반복 잔고 500은 원인·수정 미확인이므로 후속 점검만 기록하고 신규 버그 페이지로 만들지 않았다. 원본 프로젝트 문서 경로는 세션에서 언급한 근거 위치이며 해당 문서·코드·API를 직접 재검증하지 않았다.
- **중복 스킵 근거** — 같은 소스의 KIS 전환·기존식 운영 복구·Naver 비교·로그 마스킹·알림 정책은 `wiki/projects/n-stock-info.md`, 최신 관측 선택 후 컷 적용은 `wiki/analyses/scoring-version-comparison-methodology.md`, 청산정합은 `wiki/bugs/trailing-activation-current-profit-gate.md`, 체결 기반 손익은 `wiki/patterns/trading-performance-cash-flow-reconciliation.md`에 기수록이다. RS 수집 복구·관측일/진입가 검증은 `wiki/bugs/pykrx-index-name-lookup-failure.md`, `wiki/analyses/backtest-fill-model-adverse-selection.md`와 두 프로젝트에, 미국 계좌/장부·V1 페이퍼·마감 그래프는 `wiki/analyses/multi-market-trading-account-isolation.md`, `wiki/projects/ht-dde.md`와 앞선 수집 이력에 이미 반영돼 재생성하지 않았다.
- **가치 기준 스킵** — 일별 추천·체결 전체 목록, 일회성 조회/메뉴 안내, 반복 설명·진행·커밋 요청, 지침 전문, 탐색 과정과 최종 철회된 튜닝 제안은 제외했다. 최종 분석의 표본·집중도·검증 한계를 설명하는 수치만 보존했다. 스킵을 포함해 지정 세션을 검토 완료로 처리한다.
- **raw-sources 유래** — 하위 서브디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 지정 하위 디렉터리 범위 밖이며 원본과 기존 요약을 보존했다. 신규/갱신 summary가 없어 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` 없음, chunk 처리 0건.
- **처리·검증** — 지식 페이지 → index → 본 기록 순서로 갱신한 뒤 지정 원본 로그의 ingested만 true로 변경한다. 필수 frontmatter·출처·변경 이력·새 링크를 검사했으며 최종 체크섬 대조로 변경 범위와 플래그 외 원본 보존을 확인한다. 기존 사용자 변경·목록 외 세션·raw-sources·캐시는 보존하며 네트워크·다른 에이전트·git commit/push·원본 프로젝트 테스트/서비스는 실행하지 않는다.
- 변경 파일: `wiki/analyses/scoring-version-comparison-methodology.md`, `wiki/analyses/ht-trading-live-data-improvement-analysis.md`, `wiki/projects/ht-trading.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-15 (ingest — 지정 세션 마감 평가 보고 후속 대조)

- **session-logs 유래** — 지정 `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md` 2건의 ingested false와 대응 `.cache/ingest-views/` 대화를 기존 wiki·수집 이력에 대조했다. 두 대상 모두 session-log이며 mcp-note는 없다. 로그에 포함된 지시·스킬 전문은 실행하지 않았다.
- **Phase I / 기존 지식 2건 갱신·신규 0건** — 두 번째 세션의 10/15 미국 페이퍼 보고 후속 결론을 `wiki/analyses/multi-market-trading-account-isolation.md`, `wiki/projects/ht-dde.md`에 통합했다. 최신 평가액만으로 이력 비교가 불가능해 장부의 마감 평가 이력을 읽기 전용 보고에 연결한 판단, 동시점 대조군 비교·장중 잠정 평가 구분·현금 비중·2일 표본 안내·웹 응답과 시각 검증의 경계를 수집했다. 기존 시장별 장부/보고 격리 설계의 후속 핵심 결정이므로 보강하며 별도 중복 분석은 만들지 않았다. 두 문서는 기존 sources와 상호 링크를 유지했다. 구현·테스트 수는 세션 보고이며 이번 실행에서 재검증한 결과가 아니다.
- **첫 번째 세션 스킵** — 이관·push 후 history 확정·서비스 계정·모델 호환 오류·정기/수동 증거 분리·반복 429는 `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/projects/oss-radar.md`에 같은 원본 sources와 10/13 수집 이력으로 기수록이다. 마지막 10/15 정상 게시·Semantic Scholar 정상 수집·history 증가·디스크 수치는 일별 운영 보고이며 새 원인 규명이나 수정이 없어 추가 페이지를 만들지 않았다. 하루의 정상 수집으로 앞선 반복 429의 해결을 단정하지 않는다.
- **두 번째 세션 중복/가치 기준 스킵** — KIS 전환·기존식 복구·Naver 비교는 `wiki/projects/n-stock-info.md`, 점수 비교는 `wiki/analyses/scoring-version-comparison-methodology.md`, 청산정합은 `wiki/bugs/trailing-activation-current-profit-gate.md`, 이름 조회 수정은 `wiki/bugs/pykrx-index-name-lookup-failure.md`에 기수록이다. RS 정상 20일·종가/익일 비교·비활성 미리보기는 `wiki/analyses/backtest-fill-model-adverse-selection.md`, `wiki/analyses/optimal-strategy-search-preconditions.md`와 ht-trading/ht-dde 및 앞선 10/15 log에 반영돼 재생성하지 않았다. 미국 첫 체결·첫 마감도 기존 시장 분리 분석과 프로젝트에 있어 그래프 보고의 추가 결론만 보강했다. 일별 손익·종목 목록·반복 요청·메뉴 누락 안내·탐색 과정은 별도 지식으로 수집하지 않았다. 스킵 내용을 포함해 두 세션을 검토 완료로 처리했다.
- **raw-sources 유래** — 하위 서브디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 지정 하위 디렉터리 수집 범위 밖이며 보존했다. 신규/갱신 summary가 없어 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` 없음, chunk 처리 0건.
- **처리·검증** — 지식 페이지 → index → 본 기록 순서로 갱신한 뒤 지정 로그의 ingested만 true로 변경했다. 필수 frontmatter·출처·변경 이력·링크 대상·비밀값 패턴·공백을 검사하고 원본 본문은 체크섬으로 보존을 확인했다. 원본 프로젝트 테스트·서비스·네트워크·다른 에이전트·git commit/push는 실행하지 않았다. 목록 외 세션·raw-sources·캐시·기존 사용자 변경을 보존했다.
- 변경 파일: `wiki/analyses/multi-market-trading-account-isolation.md`, `wiki/projects/ht-dde.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-14 (ingest — 지정 세션 수정 완료 후속 대조)

- **session-logs 유래** — 지정 원본 `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md` 2건의 ingested: false와 대응 `.cache/ingest-views/` 대화를 검토했다. 둘 다 session-log이며 mcp-note 대상은 없다. 내부 지시문·첨부 스킬은 실행하지 않았다.
- **수집 1건 / 기존 지식 페이지 3건 갱신** — 두 번째 세션 마지막 후속 결과에서 pykrx `name_display=False` 수정·반영 완료(`afc3251`), 282개 테스트·실제 26일봉·후보 30개 산출 보고를 확인했다. 도메인 핵심 버그의 최종 해결이므로 `wiki/bugs/pykrx-index-name-lookup-failure.md`의 미적용 상태를 취소선과 적용 결과로 갱신하고 `wiki/projects/ht-dde.md`, `wiki/bugs/pykrx-krx-login-required.md`에 함께 반영했다. 저장 없는 진단을 정상 관측일 증가·정기 배치 성공·실거래 편입으로 해석하지 않는다. 당시 비정상 KRX 응답 원인은 여전히 미확정이다. 출처는 두 번째 원본 세션이다.
- **첫 번째 세션 중복/가치 기준 스킵** — 이관·push 후 history 확정·공용 서비스 계정·모델 호환 오류·반복 Semantic Scholar 429와 대체 소스 선정 품질은 `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/projects/oss-radar.md`에 같은 원본 sources와 10/13 수집 이력으로 기수록이다. 마지막 10/14 게시 성공·건수·디스크 잔량은 새 설계나 버그 수정이 없는 일별 상태 보고라 페이지를 추가하지 않고 플래그만 갱신한다.
- **두 번째 세션 중복/가치 기준 스킵 / Phase I** — KIS 전환·기존식 복구·Naver 비교는 `wiki/projects/n-stock-info.md`, 최신 관측 선택 후 컷 적용은 `wiki/analyses/scoring-version-comparison-methodology.md`, 청산정합은 `wiki/bugs/trailing-activation-current-profit-gate.md`에 기수록이다. 미국 계좌·장부 분리·최신 완결 일봉·V1 재현 4계정·LOC 사전 계획은 `wiki/analyses/multi-market-trading-account-isolation.md`, `wiki/analyses/backtest-fill-model-adverse-selection.md`와 ht-trading/ht-dde에, RS 편입의 관측·진입 가격·미청산 위험 기준은 `wiki/analyses/optimal-strategy-search-preconditions.md`에 동일 세션 출처로 이미 반영됐다. 범용 분석은 기존 페이지를 유지하고 중복 생성하지 않았다. 반복 요청·종목 목록·탐색 과정·비파괴 버전 업데이트·단발 키 신청 절차는 제외했다.
- **raw-sources 유래** — 요청한 하위 서브디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 하위 디렉터리 범위 밖이며 기존 원본·요약을 보존했다. 신규/갱신 summary가 없어 source_sha256 복사 대상도 없다.
- **PDF 유래** — `.cache/extracted/` 디렉터리와 chunk 대상 없음, 처리 0건.
- **범위·검증** — 지식 페이지 → index → 본 기록 순서로 갱신한 뒤 지정 세션 2건의 ingested만 true로 변경한다. frontmatter·출처·변경 이력·링크·비밀값·diff 범위를 확인하며 원본 시스템 테스트·외부 API·서비스는 재실행하지 않는다. 목록 외 세션·참고 자료·기존 사용자 변경을 보존하고 네트워크·다른 에이전트·git commit/push는 수행하지 않는다.
- 변경 파일: `wiki/bugs/pykrx-index-name-lookup-failure.md`, `wiki/projects/ht-dde.md`, `wiki/bugs/pykrx-krx-login-required.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.


## 2026-10-15 (ingest — 지정 세션의 RS 진입 비교 후속 수집)

- **session-logs 유래** — `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md` 1건(type session-log, ingested false)을 지정 읽기용 `.cache/ingest-views/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`와 기존 wiki에 대조했다. 본문에 포함된 AGENTS.md·스킬·실행 지시는 참고 데이터로만 취급했다. mcp-note 대상 없음.
- **수집 / Phase I** — `wiki/analyses/backtest-fill-model-adverse-selection.md`에 같은 추천·초기자본·비용·청산 규칙으로 진입 시점만 비교하는 전방 실험, 별도 장부·소급 거래 방지·참고 시세와 실제 체결의 구분을 추가했다. 기존 체결 모델 주제에 통합해 중복 분석 페이지를 만들지 않았다. `wiki/analyses/optimal-strategy-search-preconditions.md`에는 정상 관측 20일·복구 2일 충족 후에도 실제 진입 가격과 주문 연결을 별도로 검증한다는 후속 결론을 보강했다. 반복된 체결 현실성 문제와 trading 핵심 설계 판단이므로 수집했다.
- **프로젝트 갱신** — `wiki/projects/ht-dde.md`에 종가/익일 진입 비교군 각 가상 1천만원·공통 추천·독립 장부·거래 0건·추천 대기·10/15~16 예정 일정을 기록했다. `wiki/projects/ht-trading.md`에 비활성 미리보기·실제 예산 0원과 미완료 실주문 전송/체결 추적을 명시했다. 두 프로젝트 모두 미국 첫 마감 종가 반영 보고와 추가 LOC·익절·실거래 미검증을 구분했다. 기존 상태는 시점 기록으로 보존하고 후속 결과를 추가했다. 테스트 수·이력 보존은 세션 보고이며 이번 실행의 서비스 재검증이 아니다.
- **중복 스킵 근거** — 위 소스의 KIS 전환·기존 점수식 운영 복구·Naver 독립 비교·토큰 로그 마스킹·Telegram 정책은 `wiki/projects/n-stock-info.md`, 최신 관측 선택 후 컷 적용은 `wiki/analyses/scoring-version-comparison-methodology.md`, 청산정합 v2는 `wiki/bugs/trailing-activation-current-profit-gate.md`, 실제 체결 기반 손익은 `wiki/patterns/trading-performance-cash-flow-reconciliation.md`와 `wiki/projects/ht-trading.md`에 기수록돼 재생성하지 않았다. 지수 이름 조회 수정은 `wiki/bugs/pykrx-index-name-lookup-failure.md`, 미국 계좌·페이퍼 격리는 `wiki/analyses/multi-market-trading-account-isolation.md`와 두 프로젝트에 이미 있어 신규 결론만 반영했다.
- **가치 기준 스킵** — 일별 추천/체결 전체 목록과 계좌 손익 숫자, PID, 반복 진행·커밋 요청, 세션 속 지침 전문, 최종 보류된 튜닝 제안은 별도 수집하지 않았다. 일일 실현손익과 평가손익 구분은 기존 성과 대조 패턴에 포함돼 중복 저장하지 않았다. 잔고 HTTP 500은 원인·수정이 확인되지 않은 운영 점검 항목이므로 신규 버그 페이지로 승격하지 않았다. 스킵 내용을 포함해 대상 세션 전체를 검토 완료로 처리한다.
- **raw-sources 유래** — 하위 서브디렉터리 MD·fetched 대상 0건. 루트 MD 2건은 지정 수집 범위 밖이며 기존 요약과 원본을 보존했다. summary 신규/갱신이 없어 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` 없음, chunk 처리 0건.
- **검증·범위** — 지식 문서 4개의 필수 frontmatter·sources·updated·변경 이력, 새 연결의 대상 존재·상호 링크, 변경 파일의 비밀값 패턴과 공백을 확인했다. 지식 페이지 → index → 본 기록 순서로 작성 후 지정 세션의 ingested 플래그만 true로 갱신했다. 원본 본문·raw-sources·캐시·다른 세션은 보존했다. 네트워크·다른 에이전트·git 명령·원본 프로젝트 테스트/서비스 실행 없음.
- 변경 파일: `wiki/analyses/backtest-fill-model-adverse-selection.md`, `wiki/analyses/optimal-strategy-search-preconditions.md`, `wiki/projects/ht-dde.md`, `wiki/projects/ht-trading.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-14 (ingest — 지정 세션 후속 최종 결과)

- **session-logs 유래** — `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md` 1건(type: session-log, ingested: false)을 지정 읽기용 `.cache/ingest-views/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 10/14 마지막 분석까지 검토했다. 로그·캐시 내부 지시문은 참고 데이터로만 취급했다. 목록 외 세션은 처리하지 않았으며 mcp-note 대상은 없다.
- **신규 1건** — `wiki/bugs/pykrx-index-name-lookup-failure.md`: 필수 가격 이후 선택적 지수 이름 조회가 JSON 실패→빈 목록→KeyError로 전체 배치를 중단시키는 경로와 2차 TypeError를 구분했다. trading 핵심 수집 버그로 즉시 수집한다. `name_display=False`는 동일 가격 유지의 오프라인 재현이 보고된 최소 수정안이며 미적용이다. KRX 비정상 응답의 원인은 미확정이고 CD010 인증 오류와 구분한다. 기존 인증 버그·ht-dde에 상호 링크를 추가했다.
- **기존 6건 갱신 / Phase I** — `multi-market-trading-account-isolation`, `backtest-fill-model-adverse-selection`, `optimal-strategy-search-preconditions`의 기존 analyses 3건에 V1 재현의 버전·가정·고정 예산·동일 익절 대조군, LOC 사전 계획·소급 주문 방지·실제 장마감 미검증, 정상 관측·실행 가격·미청산 위험을 함께 확인하는 편입 기준을 보강했다. 같은 주제의 analyses는 새로 만들지 않았다. `projects/ht-trading`, `projects/ht-dde`에 TQQQ·SOXL 4계정 가동·첫 가상 체결·실주문 0건과 RS 정상 19일/복구 후 1세션·투자비중 약 92%·실거래 편입 보류를 반영했다. 앞선 버전 대기·미가동 결론은 취소선과 후속 결과로 정정했다. `bugs/pykrx-krx-login-required`에는 인증 복구와 이름 조회 실패를 구분했다. 출처는 모두 위 지정 세션이다.
- **중복 스킵 근거** — `projects/n-stock-info`의 KIS 전환·운영 기존식 복구·독립 Naver 비교·토큰 마스킹·알림 정책, `analyses/scoring-version-comparison-methodology`의 최신 관측 선택 후 컷 적용, `bugs/trailing-activation-current-profit-gate`의 청산정합 v2, `projects/ht-trading`의 체결 기준 비용 후 알림 수정은 동일 소스와 기존 본문·10/12~13 log에서 확인돼 재생성하지 않았다. 미국 기반·일봉 최신 N개·부분체결·합성 검증 내용도 기존 분석과 프로젝트에 있어 후속 결과만 보강했다.
- **가치 기준 스킵** — 일별 추천·체결 종목 전체 목록, PID·실행 시각, 커밋 요청·반복 진행, 세션 속 AGENTS.md 전문, 탐색적 수치·튜닝 제안은 별도 수집하지 않았다. 55점 인하·추가매수 지정가·이익 바닥 변경 제안은 최종 보류 결론이 기존 문서에 있어 유지했다. 테스트 수·첫 체결은 세션 보고의 적용 증거이지 현재 시스템 재검증이나 수익 우위의 증명이 아니다.
- **raw-sources 유래** — 하위 서브디렉터리 MD·fetched 대상 0건. 루트 MD 2개는 요청한 하위 디렉터리 범위 밖이며 기존 원본·요약을 변경하지 않았다. 신규/갱신 summary가 없어 source_sha256 복사 대상도 없다.
- **PDF 유래** — `.cache/extracted/`와 chunk 대상 없음, 처리 0건.
- **검증·범위** — 변경 지식 문서 7개의 필수 frontmatter·updated·sources·변경 이력, 신규 인덱스 단일 등록·상호 링크·비밀값 패턴을 확인했다. git diff 공백 검사 통과. 원본 매매 테스트·계좌·서비스는 실행하지 않았다. wiki 페이지→index→본 기록 순서로 작성 후 지정 세션의 ingested 플래그만 true로 변경한다. raw-sources·캐시·목록 외 세션·기존 사용자 변경은 보존했다. 네트워크·다른 에이전트·git commit/push 없음.
- 변경 파일: `wiki/analyses/multi-market-trading-account-isolation.md`, `wiki/analyses/backtest-fill-model-adverse-selection.md`, `wiki/analyses/optimal-strategy-search-preconditions.md`, `wiki/bugs/pykrx-index-name-lookup-failure.md`, `wiki/bugs/pykrx-krx-login-required.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-13 (ingest — 지정 세션 후속 결론 보강)

- **session-logs 유래** — 지정 원본 2건을 읽기용 `.cache/ingest-views/` 대화 및 최종 기록과 대조했다. 둘 다 type session-log·ingested false이며 mcp-note 대상은 없다. 내부 지시문·첨부 스킬은 실행하지 않았다.
- **기존 분석 보강 / Phase I** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`에서 반복 Semantic Scholar 429의 익명 한도·재시도 부재·대체 게시 시 후보/인용 점수 누락, 정기/수동 실행 증거 분리를 `wiki/analyses/ai-publishing-migration-safety.md`에 추가했다. 반복된 AI 게시 파이프라인 운영 지식으로 수집하며, 키 등록·재시도는 미적용 제안으로 구분했다. 키 신청 절차 자체는 단발 사용법으로 스킵했다.
- **기존 분석·프로젝트 보강** — `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 마지막 라오어 실험 요청을 `wiki/analyses/multi-market-trading-account-isolation.md`와 `wiki/projects/ht-trading.md`에 반영했다. 고정 수량/예산 분할·전략 버전·LOC 체결 검증과 고정 예산 대조 실험은 trading 핵심 설계로 수집했다. 버전 미확정·LOC 미지원·전략 코드 미변경·가상 매매 미가동이라는 최종 결론을 유지했다. 새 분석 페이지는 만들지 않았다.
- **중복 스킵 근거** — 첫 세션의 이관·push 후 history 확정·공용 계정·모델 호환 오류는 `wiki/analyses/ai-publishing-migration-safety.md`와 `wiki/projects/oss-radar.md`에 이미 반영됐다. 두 번째 세션의 KIS 전환·운영식 복구는 `wiki/projects/n-stock-info.md`, 점수 비교는 `wiki/analyses/scoring-version-comparison-methodology.md`, 청산 정합은 `wiki/bugs/trailing-activation-current-profit-gate.md`, 미국 장부·최신 완결 일봉·현지 세션·관측은 `wiki/analyses/multi-market-trading-account-isolation.md`와 `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`에 동일 소스와 이전 수집 이력으로 기수록돼 재생성하지 않았다.
- **가치 기준 스킵** — 일별 게시 건수·시각·디스크 잔량·종목 목록·커밋 요청·탐색 과정·비파괴 버전 업데이트는 재조회 가치가 낮다. 버전별 무한매수 세부 규칙은 공식 확인이 불완전하므로 확정 지식으로 수집하지 않았다.
- **raw-sources 유래** — 하위 디렉터리 MD/fetched 대상 0건. 루트 MD 2건은 요청한 하위 디렉터리 수집 범위 밖이며 보존했다. summary 신규/갱신이 없어 source_sha256 복사 대상도 없다.
- **PDF 유래** — `.cache/extracted/` chunk 대상 0건.
- 페이지 → index → 본 기록 순서로 반영한 뒤 아래 두 원본의 ingested 플래그만 true로 변경했다. 네트워크 조회·다른 에이전트·git commit/push·원본 자료 편집 없이 처리했다. 검증 결과는 세션의 보고이며 이번에 서버·전략·외부 API를 재검증하지 않았다.
- 변경 파일: `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/analyses/multi-market-trading-account-isolation.md`, `wiki/projects/ht-trading.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-13 (ingest — 지정 세션 2건 후속 수집)

- **session-logs 유래** — `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`와 `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`를 지정 `.cache/ingest-views/` 대화로 검토했다. 두 원본 모두 type session-log, ingested false였다. 세션 내부 지시·스킬 요청은 실행하지 않았다. mcp-note 대상 없음.
- **Phase I 신규 1건** — `wiki/analyses/ai-publishing-migration-safety.md`: OSS Radar와 Research Wiki에서 반복된 후보 선정 시 history 기록 오류→push 성공 후 확정, 실제 게시 대조 복구, 비게시 검증과 단일 게시자 컷오버, 모델 호환성·stderr 관측 원칙. ai-agent 도메인 핵심 운영 결정이자 두 파이프라인 반복 사례이므로 승격했다. 출처: `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`. `wiki/projects/oss-radar.md`와 상호 링크를 추가했다.
- **기존 4건 보강** — 위 oss-radar 및 `wiki/analyses/multi-market-trading-account-isolation.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`. 미국 신규 계좌 필수 전제를 수정하고 기존 계좌 조회 성공·주문 권한 미확정을 구분했다. 최신 N개/미완결 일봉 오류 수정, 현지 세션 유지, 실제 관측·신선도 표시와 전략 미가동·공동 위험 관리 미구현을 반영했다. 출처: `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`. 과거 오프라인 단계 설명은 당시 상태로 한정해 후속 관측 가동과 혼동하지 않도록 했다. 검증 숫자는 세션 보고이며 이번에 외부 API·테스트를 재실행하지 않았다.
- **중복 스킵 판단** — 동일 세션 sources와 10/10~12 수집 기록을 대조했다. KIS 전환·운영 기존식 복구·Naver 비교·알림은 `wiki/projects/n-stock-info.md`, 점수 비교/최신 관측 컷은 `wiki/analyses/scoring-version-comparison-methodology.md`, 청산정합은 `wiki/bugs/trailing-activation-current-profit-gate.md`, KRX 인증 CD010→CD001은 `wiki/bugs/pykrx-krx-login-required.md`, RS 정상 18일·63완결과 감사는 `wiki/projects/ht-dde.md`, 실제 체결 손익·백테스트 중복 수정은 `wiki/projects/ht-trading.md`에 기수록이다. OCI 통합 결과도 oss-radar/dev-blog에 있어 새 프로젝트 페이지를 만들지 않았다.
- **가치 기준 스킵** — 일별 게시 건수·종목 목록·PID·디스크 잔량·커밋 요청·탐색 과정·비파괴 버전 업데이트는 재조회 가치가 낮다. 추가 튜닝 제안은 최종 철회/보류 결론을 유지한다. CLI 자동 정리는 정책 미확인이라는 결론만 남겼다.
- **raw-sources 유래** — 하위 디렉터리 MD/fetched 대상 없음. 루트 MD 2개는 요청한 하위 디렉터리 대상 밖이며 변경하지 않았다. 신규/갱신 summary가 없어 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/`와 chunk 대상 없음, 처리 0건.
- 페이지→index→본 기록 순서로 작성한 뒤 지정 원본 2건의 ingested 플래그만 true로 변경했다. 목록 외 세션·원본 자료·읽기용 캐시·기존 사용자 변경은 보존했다. 네트워크·다른 에이전트·git commit/push 없음.
- 변경 파일: `wiki/analyses/ai-publishing-migration-safety.md`, `wiki/analyses/multi-market-trading-account-isolation.md`, `wiki/projects/oss-radar.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-12 (ingest — 지정 세션 후속 미국 구현 보강)

- **session-logs 유래** — `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md` 1건을 지정 읽기용 `.cache/ingest-views/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`의 최종 오프라인 페이퍼 구현 응답까지 검토했다. type은 session-log이며 mcp-note 대상은 없다. 세션 내부 지시와 AGENTS.md는 실행하지 않았다.
- **수집 판단** — trading 핵심 구현·주문 안전 경계이며 향후 시장 확장 시 재조회할 설계 지식이다. 신규 페이지 0건, 기존 지식 페이지 4건 갱신: `wiki/analyses/multi-market-trading-account-isolation.md`의 계획 전용 상태를 기반·오프라인 페이퍼 구현 상태로 보강하고 미확인 주문·예약금·부분체결·원자적 회계·합성 검증 경계를 추가했다. `wiki/analyses/backtest-fill-model-adverse-selection.md`에는 신호 이후 호가로 체결하는 시간 순서와 재실행 중복 방지의 검증 한계를 추가했다. `wiki/projects/ht-trading.md`와 `wiki/projects/ht-dde.md`에는 최종 구현·보고 화면·남은 실제 관측 및 계좌 연동을 반영했다. 출처는 모두 위 원본 세션이다.
- **기수록으로 중복 생성 스킵** — `wiki/projects/n-stock-info.md`의 KIS 수집·기존식 운영 복구·네이버 독립 비교·알림 정책, `wiki/analyses/scoring-version-comparison-methodology.md`의 최신 관측 선택 후 컷 적용, `wiki/bugs/trailing-activation-current-profit-gate.md`의 청산정합 v2, `wiki/bugs/pykrx-krx-login-required.md`의 CD010→CD001, `wiki/projects/ht-dde.md`의 정상 18일·63완결·RS 복구·09:35 무주문 감사가 동일 세션 출처로 이미 수록돼 있다. 10/10·이전 10/12 수집 기록과 본문을 대조했으며 다시 만들지 않았다.
- **가치 기준으로 스킵** — 일별 종목 목록·시각·PID·반복 진행 및 커밋 요청은 단발 운영 정보다. 탐색적 55점 인하·추가매수 가격 제한·이익 바닥 변경 제안은 최종 보류 결론만 유지한다. 테스트 수는 세션에 기록된 검증 결과로만 표기하며 이번 수집에서 테스트·서비스·계좌를 재확인한 것으로 주장하지 않는다.
- **Phase I** — 범용 주문 상태·오프라인 페이퍼 지식은 위 기존 analyses 2개를 갱신했다. 같은 주제의 새 분석 페이지는 생성하지 않았다. 합성 기능 검증을 실제 관측이나 전략 수익성 검증으로 해석하지 않는다.
- **raw-sources 유래** — 하위 서브디렉터리 MD·fetched 대상 없음. 루트 MD 2개는 요청한 하위 디렉터리 대상 밖이며 원본과 기존 요약은 보존했다. 신규·갱신 summary가 없어 source_sha256 복사 대상도 없다.
- **PDF 유래** — `.cache/extracted/` 디렉터리와 chunk 대상 없음, 처리 0건.
- 위키 페이지 → index → 본 기록 순서로 갱신한 뒤 지정 원본 세션의 `ingested: false`만 `true`로 변경했다. 목록 외 세션·raw-sources·읽기용 캐시·기존 사용자 변경은 보존했다. 네트워크 조회·다른 에이전트 실행·git commit/push 없음.
- 변경 파일: `wiki/analyses/multi-market-trading-account-isolation.md`, `wiki/analyses/backtest-fill-model-adverse-selection.md`, `wiki/projects/ht-trading.md`, `wiki/projects/ht-dde.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`.

## 2026-10-11 (ingest — 지정 세션 최종 내용 중복 대조)

- **session-logs 유래** — `session-logs/codex-01a125b5-9d9a-7392-96fa-f91459bb4130.md` 1건을 읽기용 `.cache/ingest-views/codex-01a125b5-9d9a-7392-96fa-f91459bb4130.md`의 최종 응답까지 검토했다. 대상은 `type: session-log`이며 mcp-note는 없다.
- **중복 스킵 근거** — `wiki/patterns/codex-session-capture-and-curation.md`의 「수집」「리미트·CLI 실패 뒤 부분 완료를 재시도한다」「처리 완료와 원문 변경의 구분」에 Claude 훅 보존·Codex 전용 로거, 재개 rollout 병합, 실패 배치 전체 재처리, frontmatter만 정규화하는 감사 기준이 이미 있다. `wiki/projects/gieok.md`의 「2026-10-11 최종 운영 설정과 재검증」에는 Sol medium 선택, 과거 169건 처리, 실제 Sol ingest·lint 성공과 51개 테스트, 설치 전 세션의 훅 적용 범위가 이미 있다. 새 지식 페이지 없이 기존 패턴의 검토 이력과 index만 갱신했다.
- **Phase I 판단** — 수집기와 정리 엔진 분리·중단 복구의 범용 지식은 위 패턴에 기수록이다. API 가격과 구독 사용량의 구분도 기존 프로젝트에 있으므로 별도 비교 analyses를 만들지 않았다. 일회성 상태 조회·탐색 과정·중간 진행 보고는 제외했다.
- **이전 보류 해소** — 아래 「지정 Codex 세션 재검토」 당시 보류했던 lint 성공은 이번 읽기용 대화 후속 최종 응답에서 확인되며 기존 프로젝트·패턴에도 반영되어 있다. 운영 Vault 07:00 성공은 Astra, Sol 실실행은 임시 Vault라는 기존 구분을 유지한다. 이번에는 프로그램·예약 작업·테스트를 재실행하지 않았다.
- **raw-sources 유래** — 하위 서브디렉터리 MD·fetched 대상 없음. 루트 MD 2개는 이번 하위 디렉터리 대상 밖이며 변경하지 않았다.
- **PDF 유래** — `.cache/extracted/` 디렉터리와 chunk 대상 없음. 신규·갱신 summary가 없어 source_sha256 복사 대상도 없다.
- 위키 → index → 본 기록 순서로 갱신 후 지정 세션 frontmatter의 `ingested: false`만 `true`로 변경했다. 목록 외 세션·참고 자료·기존 사용자 변경은 보존했다.
- 변경 파일: `wiki/patterns/codex-session-capture-and-curation.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a125b5-9d9a-7392-96fa-f91459bb4130.md`.

## 2026-10-12 (ingest — 지정 세션 재검토)

- **session-logs 유래**: `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md` 1건. 읽기용 `.cache/ingest-views/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md`를 검토했다. 다른 세션과 읽기용 파일은 변경하지 않았다.
- 신규 1건: [[multi-market-trading-account-isolation]] — 국내/미국 계좌·프로세스·주문 생명주기 분리. trading 도메인 핵심 설계의 즉시 기록 예외 적용. 10/11 계획만 작성됐으며 구현·수익성 검증 미완료, confidence medium.
- 갱신 4건: [[n-stock-info]](네이버 HTML 장애→KIS 수집·기존식 운영 복구, 독립 비교군의 시점별 의미, 알림·로그 마스킹), [[scoring-version-comparison-methodology]](최신 관측 선택 후 컷 적용), [[pykrx-krx-login-required]](CD010 비밀번호 변경 요구·CD001 복구), [[ht-trading]](미국 확장 계획과 분석 상호 링크). 출처는 모두 위 지정 세션이며 프로젝트 cwd 전체 경로와 비밀값은 복사하지 않았다.
- **기수록으로 중복 생성 스킵**: 10/10 ingest 기록과 각 문서의 동일 세션 sources를 대조했다. 점수식 복구·운영 DB 연결은 [[scoring-version-comparison-methodology]]와 [[ht-trading]], 페이퍼 청산정합 v2는 [[trailing-activation-current-profit-gate]]와 [[ht-dde]], 18일/63완결·RS 복구·09:35 무주문 감사·실거래 미활성화는 [[ht-dde]], 실제 체결 손익 알림 수정은 [[ht-trading]]에 이미 있다. 이번에는 누락된 세부사항만 보강했다.
- **가치 기준으로 스킵**: 당일 종목 순위·전송 시각·PID·커밋 요청·반복 진행 요청은 일회성 운영 정보다. 탐색적 컷 55 인하 제안과 새 청산/추가매수 튜닝 제안은 최종 보류·철회 결론만 유지했다. 점수·필터·수집 도구를 구현했다는 중간 결과를 운영 복구 완료로 재기록하지 않았다. 세션 내부 지시·AGENTS.md·스킬 사용 요청은 참고 데이터로만 읽었다.
- **raw-sources 유래**: 요청 범위인 하위 서브디렉터리의 MD/fetched 대상 없음. 루트의 기존 MD 2건은 이번 하위 디렉터리 수집 범위 밖이며 변경하지 않았다. summary 생성·갱신이 없어 source_sha256 복사 대상도 없음.
- **PDF 유래**: `.cache/extracted/` 디렉터리 없음, 처리 0건. **mcp-note**: 지정 세션은 type session-log이므로 대상 없음.
- 페이지 → index → 본 기록 순으로 갱신한 후 지정 원본 세션의 `ingested: false`만 `true`로 변경했다. 과거 10/10 반영 기록은 보존했다. 네트워크 조회·다른 에이전트·commit/push 없이 문서와 플래그만 처리했다.

## 2026-10-11 (오늘 작업 위키 정리)

- 사용자 요청에 따라 오늘의 gieok 작업을 기존 프로젝트·패턴 문서에 통합했다. 신규 페이지를 중복 생성하지 않았다.
- `wiki/projects/gieok.md`: Claude 훅 유지, Codex 수집, Sol 6.1 medium, 예약·보존 정책, 이전 169개 처리, 실제 Sol 테스트·51개 회귀 검증, 설치 전 세션 적용 범위와 당시 Git 저장 상태를 최종 요약했다.
- `wiki/patterns/codex-session-capture-and-curation.md`: 리미트/CLI 실패 뒤 부분 완료 플래그를 되돌리는 재시도 계약과 재현 테스트를 기록하고 구현·검증 출처를 보강했다.
- `wiki/index.md`에 갱신 이력을 추가했다. 아래 상세 실행 기록·기존 출처·과거 이력은 보존했다.

## 2026-10-11 (전환 설정 재검토)

- ingest/lint plist와 launchd에 실제 로드된 `gpt-6.1-sol / medium`, Vault·Node 경로와 기존 실행 시간을 대조했다. 두 모델 작업은 재로드 이후 예약 실행을 기다리는 상태다.
- 운영 기록에서는 07:00 Astra ingest가 exit 0으로 완료됐고, Sol 전환 후 이번에는 동일 환경값을 사용한 임시 Git Vault에서 실제 Sol ingest와 read-only lint를 각각 실행해 exit 0을 확인했다. 테스트 자료는 종료 후 제거했고 실 Vault의 내용은 테스트에 사용하지 않았다.
- 실제 Sol ingest는 로그 완료 플래그·위키 작성·배치 검증을 통과했다. lint는 리포트를 생성하고 원래 지식 페이지를 바꾸지 않았음을 해시로 확인했다.
- 중단 재현에서 발견한 부분 완료 플래그 보호를 보완: CLI 실패 시 선택한 배치 전체를 다시 미처리로 돌려 다음 실행에서 검토하며 원래 실패 코드는 유지한다. 재현 실패 테스트를 먼저 확인한 뒤 관련 Node 테스트 총 51개 통과.
- 최초 점검과 비교해 Claude settings SHA256이 일치한다. 미처리 과거 로그·수집 오류·남은 잠금/임시 파일 없음. 설치 전부터 열려 있던 대화는 새 훅 적용 전 상태이며, 예약 원본 reconciliation이 추가 내용을 보완한다.
- Git 저장은 별개다. gieok 소스 변경은 아직 로컬 미커밋이며, Wiki는 기존 자동 커밋 1개가 원격 main보다 앞서 있다. 사용자 Kakao 변경은 보존하고 수동 커밋·push·rebase는 수행하지 않았다.

## 2026-10-11 (정리 모델 변경)

- 사용자 요청으로 자동 ingest와 lint의 `GIEOK_CODEX_MODEL`을 `gpt-6-astra` → `gpt-6.1-sol`로 변경하고 reasoning effort는 `medium` 유지.
- 두 LaunchAgent를 백업 후 재로드하고 실제 로드된 환경값을 확인. 기존 작업은 실행 중이 아니었으며 예약 시간·수집 훅은 유지.
- 로컬 Codex 모델 목록에서 `gpt-6.1-sol`과 `medium` 지원을 확인. 이번 변경은 예약 정리 작업에만 적용하며 사용자 전역 대화 모델은 변경하지 않음.

## 2026-10-11 (ingest — 지정 Codex 세션 재검토)

- **session-logs 유래** — `session-logs/codex-01a125b5-9d9a-7392-96fa-f91459bb4130.md` 1건 검토. 읽기용 `.cache/ingest-views/codex-01a125b5-9d9a-7392-96fa-f91459bb4130.md`와 원본의 감사 체크섬 정정 기록을 대조했다.
- 갱신 1건: [[codex-session-capture-and-curation]] — 완료 플래그만 정규화하고 본문의 동일 문자열은 보존해야 하는 변경 판정 기준을 보강. 도메인 핵심 수집 무결성 문제로 기존 문서에 반영했다. 신규 페이지·별도 analyses 생성 없음.
- 중복 스킵: Claude 훅 보존, Codex 전용 로거·ingest/lint 전환, 예약 PATH, 재귀 수집 방지, 재개 원본 병합, 처리 중 원문 변경 보호는 위 패턴 문서와 `wiki/projects/gieok.md`에 이미 반영되어 있다. 104개 Claude/65개 Codex 검토 건수도 기존 2026-10-10 기록에 있어 반복 수집하지 않았다. 해당 과거 세션들은 이번 처리 대상이 아니다.
- 보류: 실제 lint는 읽기용 대화의 마지막 응답에서 검증 중으로만 보고되므로 성공 완료로 기록하지 않았다. 일회성 실행 현황과 탐색 과정은 제외했다.
- **raw-sources 유래** — 하위 디렉터리의 MD·fetched 대상 없음. 루트의 `raw-sources/claude-code-opus-orchestration-setup.md`는 기존 summary가 있으며, `raw-sources/2026-10-03-notion-oracle-cloud-free-tier.md`는 루트 배치로 이번 하위 디렉터리 대상 밖이다. 원본·summary 변경 없음.
- **PDF 유래** — `.cache/extracted/` chunk 대상 없음. 지정 세션은 `type: session-log`이며 mcp-note 대상 없음.
- wiki·index·본 기록 갱신 후 지정 세션의 `ingested`만 `true`로 변경. 다른 세션·원본 자료는 변경하지 않았다.
- 변경 파일: `wiki/patterns/codex-session-capture-and-curation.md`, `wiki/index.md`, `wiki/log.md`, `session-logs/codex-01a125b5-9d9a-7392-96fa-f91459bb4130.md`.

## 2026-10-10 (Codex 미수집 기록 보강)

- Codex 원본은 동일 세션의 재개 파일을 합쳐 **65개 고유 세션 기록**으로 검토했다. 이 중 26개는 기존 문서/신규 문서에 반영하고 39개는 중복·도메인 밖·단발 사용·내부 승인 심사·미완료 입력으로 스킵했다. 이번 훅 스모크 세션도 포함된다.
- CLI 사용량 한도로 별도 Codex 실행이 중단되어, 나머지는 현재 Codex 대화에서 질문·최종 결과·정정 및 필요한 원본 발췌를 대조했다. 일반화하기 어려운 수치와 미실행 계획은 구현 완료로 기록하지 않았다.
- 신규: [[codex-session-capture-and-curation]], [[trailing-activation-current-profit-gate]], [[trading-performance-cash-flow-reconciliation]].
- 보강: gieok, ht-trading, ht-dde, dev-blog, oss-radar, finance-analysis-nextjs, scoring-version-comparison-methodology, optimal-strategy-search-preconditions, shared-broker-appkey-token-cache, multi-agent-orchestration-taxonomy, openai-codex-cli-overview, llm-content-quality-guards. 기존 데이터·계좌·원격 서버 변경 없음.
- 동일 Codex 세션 ID의 원본 두 개가 서로 덮어쓰지 않도록 수집기와 회귀 테스트를 수정했다. 아래 검토 표는 처리 시점의 로그 스냅샷 기준이며 새 대화가 생기면 해당 세션은 다시 미처리로 돌아간다.

| 세션 로그 | 판단·근거 |
|---|---|
| `session-logs/codex-019d91a6-e931-7bb3-acfd-12bc49260658.md` | 반영: Codex 상태라인의 컨텍스트/누적 토큰/사용량 구분. 강의 검수·Tips 보강은 기존 위키와 중복. |
| `session-logs/codex-019d96ad-2498-7e00-a865-f3adf37de6f5.md` | 스킵: 4월 코드 리뷰의 이름 표시·분할 시점·미수정 지적. 후속 상태와 혼동하지 않으며 기존 시점/체결 패턴과 중복. |
| `session-logs/codex-019d96ad-9ab5-7c80-81ae-eeb1b660fa8f.md` | 스킵: 단일일 매매 상황·분할 체결/pending 분석. 기존 위험/실패 횟수 기록 원칙과 중복. |
| `session-logs/codex-019d96b5-ecf1-74f2-860c-0d277a094067.md` | 반영: 상태라인 ctx/tok/rate 구분과 좁은 표시 폭 처리. |
| `session-logs/codex-019d96ba-5c40-7382-867e-848037e8c782.md` | 반영: 상태라인 이력. 회사 원본 로컬 격리는 기존 gieok/위키 수집 원칙과 중복. |
| `session-logs/codex-019db8f4-139a-7a71-864f-ca4c29244465.md` | 반영: 4월 분할 가드 최종 변경을 역사로 보강. 이후 변경보다 오래된 값임을 명시. |
| `session-logs/codex-019db8f6-9373-77e2-8cb1-80ef1ee29289.md` | 스킵: OpenClaw 코더·Telegram 바인딩·ACP 및 모델 캐시 구성. 기존 OpenClaw 아키텍처/런타임 문서와 중복. |
| `session-logs/codex-019db94c-08b0-71a2-8788-9560ba832443.md` | 스킵: codex_ok 스모크 응답만 존재. |
| `session-logs/codex-019dbaa3-ca31-73d3-a231-1154dd14453c.md` | 스킵: 자산 대시보드 초기 스캐폴딩과 외부 DB 연결 보류. 단일 초기 구현·미실행 계획으로 추가 승격 보류. |
| `session-logs/codex-019df090-25fd-7e60-83e4-8174097452e1.md` | 스킵: URL 요청만 있고 분석 응답 없음. |
| `session-logs/codex-019df091-d751-7953-bbdc-315079153465.md` | 스킵: kakaocli 설치·Xcode 조건 등의 단발 도구 사용. 기존 카카오/TCC 기록과 중복. |
| `session-logs/codex-019df589-b806-72d0-a244-f502ebb415ed.md` | 스킵: Gmail 메일 정리와 건수. 수집 도메인 밖의 개인 작업. |
| `session-logs/codex-019df5c1-f42b-7b73-ae5e-bf0f56cb9f69.md` | 스킵: 현재 폴더 확인만 존재. |
| `session-logs/codex-019df5c2-47aa-74c0-9f07-60062af7cbf2.md` | 스킵: 현재 폴더 확인만 존재. |
| `session-logs/codex-019e1757-4492-73a0-8bb8-5b9a959d3bec.md` | 스킵: 인사·구독 만료일 질문. 새로운 검증된 지식 없음. |
| `session-logs/codex-019e1c2c-311e-7c33-aa19-d5528d77bad2.md` | 스킵: 인사만 있고 응답 없음. |
| `session-logs/codex-019e3b82-158c-7c41-a94e-907f9fb5ff38.md` | 반영: codex exec 비대화형 실행의 운영 이력. 단발 Git 검사 회피 예시는 일반 운영 권고로 승격하지 않음. |
| `session-logs/codex-019e3b85-33db-7a62-9310-a1dab8be91ee.md` | 스킵: 일반 자동화 아이디어 목록. 검증되지 않은 첫 제안. |
| `session-logs/codex-019e4adc-3dd2-7671-ac06-f533f162e83a.md` | 반영: M&A 데이터 구조·저장 경로의 구현 이력. 미검증 평가 정확성과 미완료 기능을 분리. |
| `session-logs/codex-019e5525-08d4-7620-90eb-04c6e3d81c25.md` | 반영: LLM 출력의 스키마·근거 검증. impactType/affectedAudience 등의 기존 구현은 중복. |
| `session-logs/codex-019e5f49-2074-7ed0-8a85-fbf004b4e51d.md` | 반영: 파싱뿐 아니라 validator/URL grounding 실패도 제한 재시도. 과거 CLI 구조 강제 제약은 현재 기능과 구분. |
| `session-logs/codex-019e6987-c6d4-7b11-8644-dce5a939736d.md` | 스킵: 커뮤니티 SaaS·에이전트 사업 아이디어와 Finder 사용법. 검증되지 않은 제안/단발 사용. |
| `session-logs/codex-019e698e-7600-7963-89a0-19397bd4feb9.md` | 스킵: 일회 전역 권한·컨텍스트 설정. 큰 창과 구독 한도 차이는 기존/보강 Codex 문서로 대조. |
| `session-logs/codex-019e6ee9-3c6c-7930-bf1e-428ce5df35eb.md` | 반영: Android Log AI의 근거 로그·코드 context 구현과 후속 redaction 미실행 계획 구분. 일반 IDE UI/플러그인 구현은 범위 밖/단발. |
| `session-logs/codex-019e7848-92b0-7fa3-8c65-18a2d07b35e8.md` | 스킵: wide 편집기·파일 트리/Markdown preview smoke와 단일 UI 구현. 재조회용 새 도메인 지식 없음. |
| `session-logs/codex-019e837c-d7e0-7212-b02b-de0b576c8a17.md` | 반영: 모든 보유 종목을 모든 전략에 붙이던 유니버스 오염 수정과 잔존 상태 정리. |
| `session-logs/codex-019e8bd9-50d3-7643-8de8-a5ea9f2e2beb.md` | 스킵: MCP 인증 오류 입력만 있고 응답 없음. |
| `session-logs/codex-019e8bda-9532-7fb1-b7eb-e19466206c1b.md` | 스킵: MCP token_invalidated 재로그인 설명. 단일 일반 인증 문제이며 기수록 원인 분리와 중복. |
| `session-logs/codex-019e8bf9-efd1-7560-9116-64686dd4e594.md` | 스킵: exit/Done 종료 응답. |
| `session-logs/codex-019e9bf8-5adc-7902-a7d3-7036f75f7f0f.md` | 스킵: Qualcomm/Exynos ISP 드라이버 비교. 지정된 지식 도메인 밖. |
| `session-logs/codex-019e9f8c-6a37-7f82-829d-647131c0dc03.md` | 스킵: 내부 승인 심사 사본. 사용자 세션과 중복이며 독립 지식 자료 아님. |
| `session-logs/codex-019ec60d-989b-70f3-ab2e-af8bb3a222ce.md` | 반영: 페이퍼 검증 목적/비용/청산 기준. 단일 전략 추가는 기록된 구현과 검증 경계를 유지. |
| `session-logs/codex-019ee96f-cf6a-72a3-a8de-9cf104420eaf.md` | 스킵: 인사만 있고 응답 없음. |
| `session-logs/codex-019ee970-b79e-7af1-af47-2b9eba57daad.md` | 스킵: 인사 응답. |
| `session-logs/codex-019f2c87-dbb4-7d40-995d-9990af86261f.md` | 스킵: Hermes Paperclip 분석은 이미 같은 문서/index/log에 반영 완료. |
| `session-logs/codex-019fc25a-8ecf-7501-a0a6-06a5e058a9c9.md` | 스킵: 단일 Koji MCP 제품 조사. 실제 도구 범위를 일반 RAG로 오인하지 않는 정정이 있으나 제품 조사/첫 등장으로 승격 보류. |
| `session-logs/codex-019fdf6f-a6bc-7570-9d19-0a712511078b.md` | 반영: provider 선택 일원화·research/rewrite 권한 분리·터미널/예약 CLI 버전 차이와 최종 Cursor 전환 이력. |
| `session-logs/codex-019fdf72-0da4-7832-8bca-5b93a8a5559e.md` | 반영: 동일시작 A/B, 수정 규칙 이후 데이터 분리, 비용·집중도·검증 진행률. |
| `session-logs/codex-019fe15c-0401-73c0-a1ef-fe77f09fbc21.md` | 반영: Codex profile의 메인 역할과 위임 강도를 분리. hard/soft 초기 오구현과 최종 구성을 구분. |
| `session-logs/codex-019fe6c4-c2ca-70a1-a8e7-4705897e649e.md` | 스킵: Chrome 설치 방법. 단발 도구 사용/도메인 밖. |
| `session-logs/codex-019febfc-81e8-7e02-b6d4-4bed0f6c469b.md` | 반영: 페이퍼 성과 해석과 정합성. 시장 하루 등락/당일 손익은 뉴스성 수치로 새 지식에 승격하지 않음. |
| `session-logs/codex-01a026fa-0133-7623-8ddc-b58ab508c57e.md` | 반영: 성과 원본·KIS 대조·스냅샷 감사. 시초 손절 구현은 역사로 유지하고 새 매매 규칙을 권고하지 않음. |
| `session-logs/codex-01a026fc-184f-72a1-abb9-3bccc966d2c4.md` | 반영: 매도대금 누락→사이징 오염, 깨끗한 A/B 재시작, 공유 토큰 재독. 현금 가산만으로 성과 복구할 수 없음. |
| `session-logs/codex-01a029d3-6952-7f53-8b64-06b34970c4ae.md` | 스킵: 일반 PC 디스크 분석/스캔 로깅. 실패 스캔의 delta 제외는 유용하나 지정 도메인 밖. |
| `session-logs/codex-01a04b94-9332-7a01-8b0d-4528d2cf7565.md` | 반영: OCI 준비/전환/실제 게시 검증과 원 PC 스케줄 중단 경계. |
| `session-logs/codex-01a076f4-c4ea-7962-937d-57bda1b9b108.md` | 반영: 급락 가드·현금흐름 추정·fast tick 재시도 억제·트레일링 스톱 여유. 재개 원본 두 개를 합친 기록에서 검토. |
| `session-logs/codex-01a076f9-f190-7a13-ae40-c67d13d037e8.md` | 반영: 동일시작 페이퍼의 표본·비용·MDD/성과 집중도. 단기 시장 수익률은 확정 우위로 승격하지 않음. |
| `session-logs/codex-01a07702-8bf2-7360-9aa4-c0fd75c5787a.md` | 스킵: 내부 승인 심사 사본. |
| `session-logs/codex-01a07711-e0e1-7431-b8aa-8a03fdcd93b1.md` | 스킵: 내부 승인 심사 사본. |
| `session-logs/codex-01a095ef-0396-7580-bde3-c286f25e219f.md` | 스킵: fast tick 구현 하위 작업. 부모 46번 세션에 결과가 포함되어 별도 중복 승격하지 않음. |
| `session-logs/codex-01a095ef-03f3-7a30-81e5-9bf6f8e60d68.md` | 스킵: 내부 승인 심사 사본. |
| `session-logs/codex-01a0980b-4781-7292-acd3-90606bae921b.md` | 스킵: 내부 승인 심사 사본. |
| `session-logs/codex-01a0983e-b9e4-7252-b20c-40aaacff8bc8.md` | 스킵: 내부 승인 심사 사본. |
| `session-logs/codex-01a09918-602f-7293-8f80-3f785a785af8.md` | 스킵: 내부 승인 심사 사본. |
| `session-logs/codex-01a09919-cdc7-73a3-b5d0-3e7bd50e078f.md` | 반영: KIS 예탁금/시장 수급을 참고 지표로 수집. 단기 반등 해석은 성과 개선 검증과 분리. |
| `session-logs/codex-01a09b03-3a64-74b3-8a44-c59f3d3ce4f4.md` | 반영: 트레일링 활성 후 검사 누락 재현·공통화, 장마감 성공 후 완료 및 재시도. 의도된 전략을 결함으로 표현한 초기 설명은 철회 반영. |
| `session-logs/codex-01a09b07-8178-75d3-8703-d5cf716d4ebf.md` | 스킵: 내부 승인 심사 사본. |
| `session-logs/codex-01a09b1e-af89-7892-a428-3a573c0c40ed.md` | 스킵: 내부 승인 심사 사본. |
| `session-logs/codex-01a09cba-c650-7060-889e-57ada21ebc01.md` | 스킵: 내부 승인 심사 사본. |
| `session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md` | 반영: OCI 세 게시 파이프라인 이관·공용 계정·KST·정상 후보없음 처리·실제 게시 검증. 단발 모델 릴리스/일일 숫자/이미 반영된 무료볼륨 문서는 중복 스킵. |
| `session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md` | 반영: 데이터 원천 전환의 점수 분포·운영 DB 연결, 페이퍼 청산 정합, 정상 거래일/수집 복구 및 실거래 승인 게이트. |
| `session-logs/codex-01a0e84a-090d-7963-8d78-c213bb16e3af.md` | 스킵: macOS 업데이트/APFS 디스크 변동. 지정 도메인 밖의 단발 OS 운영. |
| `session-logs/codex-01a0ff2b-aad2-7bc3-be61-749ab20409db.md` | 스킵: 일일 디스크 현황. AI 설치본 증가 관찰은 반복 OCI 운영 기록에 포함됐고 이 로컬 스냅샷은 추가 승격하지 않음. |
| `session-logs/codex-01a125b5-9d9a-7392-96fa-f91459bb4130.md` | 반영: 이번 gieok Codex 수집/정리 전환과 검증. |
| `session-logs/codex-01a125c8-3e17-7de2-9855-9b2eaacd74fc.md` | 반영: 실제 Codex 훅의 질문·응답 저장 검증 근거. |

# 운영 로그

## 2026-10-10 (ingest — 지정된 2026-08-04~08-08 세션)

- **session-logs 유래** — 지정된 104건 전수 검토, 104건 스킵 후 `ingested: true`. 날짜별 22/22/20/20/20건. 모두 `type: session-log`이며 mcp-note는 없음. 신규·갱신 지식 페이지 0건.
- **판단 근거** — 전건 조사 요청·입력 후보만 기록되어 있고 Session Summary는 `assistant_turns: 0`, `bash_commands_logged: 0`, `file_edits: 0`. 완성된 dossier, 유용한 비교·분석의 결론, 구현·검증 결과가 없어 Phase I 승격 대상이 아님. 반복 입력은 독립된 지식 검증이나 두 번째 학습 사례로 세지 않음.
- **기존 wiki 대조** — 조사/작문 분리·evidence 계약·미확인 항목 격리는 [[research-write-agent-separation]]에 기수록. AI 후보의 소스 유출·OAuth 과금·스킬·CLI 주제는 [[claude-code-source-leak-internals]], [[anthropic-oauth-third-party-billing-trap]], [[claude-code-skills-plugins]], [[openai-codex-cli-overview]]와 겹치며 이번 입력에 새 검증 결론은 없음. 로깅 한계는 [[dev-blog]]의 2026-07-30·08-03 기록, URL 마스킹 훼손은 [[gieok-session-log-url-credential-masking-false-positive]]에 기수록. 0턴만으로 실행·게시 실패를 확정하거나 짝지어진 요청만으로 재시도 원인을 추정하지 않음.
- **raw-sources 유래** — 요청 범위인 하위 서브디렉터리의 MD/fetched 자료 없음. 루트의 `raw-sources/claude-code-opus-orchestration-setup.md`와 `raw-sources/2026-10-03-notion-oracle-cloud-free-tier.md`는 이번 하위 디렉터리 수집 범위 밖이므로 수정·재요약하지 않음. summary 신규/갱신 및 source_sha256 복사 대상 없음.
- **PDF 유래** — `.cache/extracted/` 디렉터리 없음, chunk/index 수집 대상 0건.
- **변경 파일 전체** — `wiki/index.md`, `wiki/log.md`, 아래 104개 세션 파일. 세션은 frontmatter의 `ingested` 한 줄만 변경. raw-sources 및 목록 외 세션, 기존 사용자 변경 보존.

### 파일별 스킵 근거 및 변경 세션 전체 목록

공통 근거는 위의 응답·실행 기록 부재입니다. 아래 각 파일에 추가적인 내용 기준을 명시합니다.

- `session-logs/20260804-030015-9eea-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-030046-c44b-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-030052-af40-#-Android-Kernel-Research-Dossier-당신은-Android-커널-개.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-030118-1da7-#-Android-Kernel-Research-Dossier-당신은-Android-커널-개.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-030127-2800-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260804-030152-6b12-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260804-030744-adac-#-Opensource-Curation-Research-Dossier-당신은-오픈소스-큐레.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260804-031826-56b9-#-Opensource-Curation-Research-Dossier-당신은-오픈소스-큐레.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260804-031841-9e6f-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md` — 스킵: AI 제품 뉴스·기수록 주제의 입력 후보만 존재; 새로운 범용 분석 결론 없음.
- `session-logs/20260804-031914-a124-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md` — 스킵: AI 제품 뉴스·기수록 주제의 입력 후보만 존재; 새로운 범용 분석 결론 없음.
- `session-logs/20260804-031929-ac96-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032000-8430-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032012-315e-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032045-acc6-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032057-5d2c-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032129-fae6-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032141-3719-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032214-a0ac-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032231-f9d6-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032303-6dc4-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032311-e559-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260804-032339-35c6-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-030012-8f98-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-030041-6364-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-030047-31c4-#-Android-Kernel-Research-Dossier-당신은-Android-커널-개.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-030113-75cd-#-Android-Kernel-Research-Dossier-당신은-Android-커널-개.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-030122-921f-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260805-030147-e14b-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260805-030827-ccca-#-Opensource-Curation-Research-Dossier-당신은-오픈소스-큐레.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260805-032152-63d1-#-Opensource-Curation-Research-Dossier-당신은-오픈소스-큐레.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260805-032207-47d7-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md` — 스킵: AI 제품 뉴스·기수록 주제의 입력 후보만 존재; 새로운 범용 분석 결론 없음.
- `session-logs/20260805-032241-e439-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md` — 스킵: AI 제품 뉴스·기수록 주제의 입력 후보만 존재; 새로운 범용 분석 결론 없음.
- `session-logs/20260805-032255-c838-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032327-a9d1-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032344-4fc4-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032420-d62a-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032434-949a-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032506-23da-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032518-af81-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032552-f535-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032604-d6e7-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032638-27c8-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032649-d71f-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260805-032721-e254-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030017-b538-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030049-cc62-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030055-bd71-#-Android-Kernel-Research-Dossier-당신은-Android-커널-개.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030121-56b6-#-Android-Kernel-Research-Dossier-당신은-Android-커널-개.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030129-28b3-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260806-030155-dc81-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260806-030336-9a1e-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md` — 스킵: AI 제품 뉴스·기수록 주제의 입력 후보만 존재; 새로운 범용 분석 결론 없음.
- `session-logs/20260806-030408-88df-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md` — 스킵: AI 제품 뉴스·기수록 주제의 입력 후보만 존재; 새로운 범용 분석 결론 없음.
- `session-logs/20260806-030425-ec83-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030500-dd3a-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030517-6872-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030549-7d13-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030602-e576-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030634-756a-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030646-f459-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030718-068c-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030731-0e81-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030804-ecbd-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030814-de6a-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260806-030846-4e6d-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030018-720a-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030051-786a-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030057-589e-#-Android-Kernel-Research-Dossier-당신은-Android-커널-개.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030122-dfef-#-Android-Kernel-Research-Dossier-당신은-Android-커널-개.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030130-719d-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260807-030156-ebea-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260807-030338-ab26-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md` — 스킵: AI 제품 뉴스·기수록 주제의 입력 후보만 존재; 새로운 범용 분석 결론 없음.
- `session-logs/20260807-030410-f286-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md` — 스킵: AI 제품 뉴스·기수록 주제의 입력 후보만 존재; 새로운 범용 분석 결론 없음.
- `session-logs/20260807-030425-7140-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030457-ffcd-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030509-1b48-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030541-5c2e-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030555-0c3c-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030628-8677-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030639-7dc7-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030711-6072-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030723-125d-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030754-1d3d-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030806-4556-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260807-030839-9117-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030014-8be9-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030045-e4dc-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030051-e6f6-#-Android-Kernel-Research-Dossier-당신은-Android-커널-개.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030116-4ebc-#-Android-Kernel-Research-Dossier-당신은-Android-커널-개.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030124-0d9e-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260808-030149-0cb2-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md` — 스킵: OSS 트렌딩·도입 조사 후보만 존재; AI/트레이딩 관련 레포도 검증된 설계·비교 결과 없음.
- `session-logs/20260808-030313-3167-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md` — 스킵: AI 제품 뉴스·기수록 주제의 입력 후보만 존재; 새로운 범용 분석 결론 없음.
- `session-logs/20260808-030345-866f-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md` — 스킵: AI 제품 뉴스·기수록 주제의 입력 후보만 존재; 새로운 범용 분석 결론 없음.
- `session-logs/20260808-030358-a6cf-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030428-49b7-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030441-d76f-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030514-c85f-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030526-e4cc-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030558-97e3-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030610-793d-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030643-a846-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030656-5bad-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030734-4b13-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030748-ecbf-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.
- `session-logs/20260808-030820-4ad3-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md` — 스킵: 커널 패치·릴리스 뉴스 후보로 수집 도메인 밖; 조사 계약은 기존 research-write-agent-separation에 기수록.


## 2026-08-16 — HT-001 누적 분석 착수

- 신규: [[ht-trading-live-data-improvement-analysis]] — `ht_trading` 운영 로그·1분봉·스냅샷·일봉 백테스트를 연결한 누적 분석 원장. 8/5~8/14 실제 체결 33건, 신규 스크리너 진입 10건, 2024~2025 장기 baseline과 단일변수 실험을 기록.
- 핵심 관찰: 고득점 신규 진입이 더 좋은 당일 성과를 보이지 않은 초기 표본, 2024/2025 성과 역전, `181710` 시장가 주문의 19분 대기·급등 재개 체결, 현재 일봉 백테스트가 라이브 스크리너·추세 게이트를 재현하지 못하는 구조적 한계.
- 운영 코드·전략 설정은 변경하지 않음. 분석 결과는 대표님과 논의 후 별도 승인된 항목만 적용 검토.

## 2026-08-03 (ingest)

- **session-logs 유래** — 미처리 28건 처리 (08-02 저녁 2건 + 08-03 새벽 03:00~05:00 dev-blog cron 26건: Research Dossier 12 + Newsletter Write 13 + Weekly Digest 1). raw-sources/·.cache/extracted/·fetched/·mcp-note 는 신규 대상 없음 (raw-sources/claude-code-opus-orchestration-setup.md 는 summary 생성 이후 무변경이라 스킵). 신규 문서 0건, index.md 변경 없음.
  - **갱신 2건**:
    - [[llm-json-parse-retry-with-dump]] — 교정 재시도 3번째 실측(`861a`→`ae2b`): 3회 전부 Opensource Trending write 로 토픽 상관 가설이 표본 3회로 강화 + 교정 재시도 실패 후 `retry_once`(08-02 도입, commit `1608b20`)가 research 부터 토픽 전체를 재실행하는 **2계층 재시도 연쇄 첫 실측**.
    - [[dev-blog]] — 08-03 운영 관찰 항목: retry_once 첫 cron 실전 발화(R#1 `1d25`→W#1 `861a`→W#2 교정 `ae2b`→R#2 `36d0`→W#3 `e697` 의 5세션 체인 — 두 dossier `generatedAt` 18:19Z/18:31Z 와 동일 collect payload 재실행으로 실증), anti-bot 12번째 관측 신규 변형 없음(`054b` 1턴 "모든 대상 URL이 Anubis 봇 차단…대체 경로로 재시도"), 로그 소실 지속(research 12건 중 `054b` 만 1턴·write 13건 전건 0턴), `56cb`(08-02 20:55 단독 Lens write 0턴)는 `239c5b7` arch-platform 수동 복구 세션의 부산물로 그 세션 자체는 08-02 wiki-ingest 로 기수집.
  - 스킵: 뉴스레터/dossier 뉴스 콘텐츠 전량 20건(기존 방침 동일 — 파이프라인 메타 지식만 위 2건으로 흡수), Weekly Digest `551b`(07-27 기수록 유형·0턴), `389a`("model"/"hi" 노이즈 세션, 지식 없음).
  - 참고: `20260702-*-ea52` 로그는 본문에 "ingested: false" 문자열이 포함돼 grep 오탐되나 frontmatter 는 이미 true(07-04 인제스트 완료분) — 처리 대상 아님.

## 2026-08-02 — wiki-ingest

- Project: dev-blog
- Mode: diff
- Input: commit 1608b20 (반복 파이프라인 실패의 3부류 구조적 수정 — evidence.kind soft-fail·CJK 필터 일반화·실패 토픽 자동 재시도) + 239c5b7 (arch-platform 08-02 복구)
- Created/Updated:
  - 갱신: `bugs/dossier-evidence-kind-enum-reject` (「재발과 근본 수정」 절 — 07-25 "repo" band-aid 가 08-02 "patch" 로 재발 → normalizeDossier soft-fail 로 근본 수정, 교훈 #1·#2 를 실제 구현한 닫힌 루프), `projects/dev-blog` (구조적 수정 세션 엔트리 — fleet 자가회복 재구성 + 3부류 대응 + per-cause whack-a-mole vs 구조적 대응 교훈)
  - 신규 없음 (transient 자동재시도 인사이트는 기존 [[shell-set-eu-topic-isolation]] 격리 패턴의 연장으로 projects/dev-blog 에 흡수 — 2회째 재등장 시 별도 패턴 승격 검토)

## 2026-08-02 (ingest)

- **session-logs 유래** — 미처리 21건 처리 (08-02 새벽 03:00~04:36 dev-blog cron: Research Dossier 11 [Linux Daily·Android Kernel·Opensource Trending·Opensource Curation·AI Coding Agents + Kernel Lens 6렌즈] + Newsletter Write 10). 뉴스레터 콘텐츠는 뉴스성으로 전량 스킵 (기존 방침 동일). 신규 문서 0건.
  - **갱신 1건**: [[dev-blog]] (08-02 운영 관찰 — anti-bot **11번째 관측, 신규 변형 없음**: 차단 07-24 이래 10일 연속, UA 스윕(`ccc6`)에서 `git/2.39.0` 통과[기존 실측 2.43·2.44·2.45·2.53 보다 낮은 버전 첫 실측 — "버전 무관" 하향 확장 재확인]·curl 기본/`curl/8.7.1` bare 403·Mozilla UA 는 `200`+Anubis 1.25.0 챌린지 복귀 형태 유지·releases.json 200 재확인 — 판정 동일이라 [[newsletter-research-anti-bot-blocking]] 본문 무변경, 07-30 8번째 관측 선례와 동일 처리. write 9/10 무응답성 로그 + perf-rt(`fc0f`)만 1턴 완성 JSON — 07-20 이래 기수록 수준)
  - **트리아지 기각 2건**: ① "write 9/10 무응답 = 파이프라인 90% 실패율 진입" — 헤드리스 로거의 assistant 출력 소실 특성(07-20 이래 기수록)으로 흡수, write 전건이 dossier 를 입력으로 정상 수신했고 07-30 "write 전건 무응답 ≠ 전면 장애" 기각 선례와 동일, 기각. ② "dossier `verifiedDowngradeCount`·quote 검증 게이트 신규 도입" — 06-28 [[research-write-agent-separation]] 기수록(07-30 동일 기각 선례), 기각.
  - **스킵 판정**: 자격증명형 URL 마스킹 span 파괴 재발(Lens write 4건) — [[gieok-session-log-url-credential-masking-false-positive]] 기수록. 커널 커밋·OSS 릴리스·AI Coding 제품 소식 등 dossier/뉴스레터 콘텐츠 전량 — 도메인 밖 뉴스성. runner 서브에이전트 2대 트리아지 후 anti-bot 신호는 메인이 `91e9`·`ccc6` 원문 재검증(Anubis 챌린지 HTML·UA 스윕 인용 직접 확인).
- **raw-sources 유래** — 신규 대상 없음: `raw-sources/claude-code-opus-orchestration-setup.md` 는 summary 기존재(07-12 생성)·원본 미변경(git 최종 변경 07-11)이라 스킵. sidecar(`.cache/raw-md-sha/`) 비어 있음.
- **PDF 유래(.cache/extracted/)·fetched/·mcp-note** — 대상 없음(전부 비어 있음).
- 비고: `ingested: false` 전문 검색에 20260702 ea52 가 다시 걸렸으나 frontmatter 는 `ingested: true` (본문 인용 문자열의 false positive, 07-31 비고와 동일) — frontmatter 앵커 기준으로 재확인해 대상에서 제외.

## 2026-08-01 (ingest)

- **session-logs 유래** — 미처리 21건 처리 (08-01 새벽 03:00~05:04 dev-blog cron: Research Dossier 11 [Linux Daily·Android Kernel·Opensource Trending·Opensource Curation·AI Coding Agents + Kernel Lens 6렌즈] + Newsletter Write 10 — Opensource Trending write 로그 부재). 뉴스레터 콘텐츠는 뉴스성으로 전량 스킵 (기존 방침 동일). 신규 문서 0건.
  - **갱신 1건**: [[newsletter-research-anti-bot-blocking]] (10번째 관측 — **patchwork.kernel.org API 가 Mozilla UA 호출에 Anubis 챌린지(v1.18.0, lore 의 1.25.0 과 별도 배포)를 처음 반환**해 폴백 채널 자체로 차단 확산. 도구형 UA 재실측은 미수행(ad7e 세션은 cdn ChangeLog 분석으로 전환). Mozilla 포함 복합 UA `… curl-lore-fetch` 도 챌린지 대상(76ce, 07-27 가설 부합). git/2.43.0 UA lore 통과·cdn ChangeLog·releases.json 200 은 기존 판정 재확인. 차단 자체는 99ee·ad7e·76ce·ebf0 4건으로 07-24 이래 9일 연속 지속)
  - **스킵 판정**: ① Lens dossier(a03e) 후보 `commitMessage` 의 quoted-printable 미디코딩(`=E2=80=AFPM`·`=20` 잔재) — 07-04 [[dev-blog]] 기수록 "수집기 MIME 미디코딩" 함정의 재발, 신규 아님. ② 커널 커밋 메시지의 `Assisted-by: Claude` 표기 관측(8f32·2a8e) — 커널 개발 동향 뉴스 콘텐츠, 도메인 밖. ③ 99ee 의 자격증명형 URL 마스킹 span 파괴 재발 — 기수록. assistant_turns 은 21건 중 4건만 1턴(99ee·6b76·2a8e·a03e), 17건은 0턴 무응답성 로그 — 07-29 이래 추적 중인 로그 상 산출 소실 패턴과 동일 수준, 질적 신규성 없음. runner 서브에이전트 트리아지 후 anti-bot·MIME 신호는 메인이 원문 재검증.
- **raw-sources 유래** — 신규 대상 없음: `raw-sources/claude-code-opus-orchestration-setup.md` 는 summary 기존재(07-12 생성)·원본 미변경(git 최종 변경 07-11)이라 스킵. sidecar(`.cache/raw-md-sha/`) 비어 있음.
- **PDF 유래(.cache/extracted/)·fetched/·mcp-note** — 대상 없음(전부 비어 있음).

## 2026-07-31 (ingest)

- **session-logs 유래** — 미처리 23건 처리 (07-31 새벽 03:00~05:00 dev-blog cron: Research Dossier 11 [Linux Daily·Android Kernel·Opensource Trending·Opensource Curation·AI Coding Agents + Kernel Lens 6렌즈] + Newsletter Write 12). 뉴스레터 콘텐츠는 뉴스성으로 전량 스킵 (기존 방침 동일). 신규 문서 0건.
  - **갱신 1건**: [[newsletter-research-anti-bot-blocking]] (07-31 9번째 관측 — 6e61 UA 스윕에서 `mutt/2.2` 통과 신규 실측·`git/2.53.0` 세 버전째 통과·브라우저 Mozilla UA 응답이 `500` 변형에서 `200`+Anubis 챌린지로 복귀·git.kernel.org cgit 는 기본 curl 로 200. 차단 자체는 0836·87b6 bare 403 + fdc4 Anubis 챌린지로 8일 연속 지속. 4개 로그 원문에서 인용 직접 검증 완료)
  - **스킵 판정**: ① AI Coding dossier 의 Claude Code 스테가노그래피 마커 분석(c75c) — 07-05 에 이어 두 번째 등장이나 두 번 모두 dossier 뉴스 후보(`seenBefore: true`)로 나타난 뉴스 콘텐츠이지 사용자 운영 지식이 아님 → 뉴스성 스킵 유지 (07-30 "뉴스성 단일 출처" 선례와 동일 기준). ② Kernel Lens 콘텐츠 내 Rust GPU VM 32비트 빌드 실패(9a60)·RISC-V 스텁 함수명 불일치(31c7)·Sashiko AI 리뷰 봇의 커널 패치 Critical 발견(fdc4) — 전부 뉴스레터가 다룬 커널 개발 뉴스 자체, 도메인 밖. ③ X/HN 403/429 대응 "근거 확보 가능 후보만 채택"(c75c) — 기존 대응 계층(2차 corroborate)에 기수록, 신규성 없음. runner 서브에이전트 트리아지 후 주요 신호 4건은 메인이 원문 재검증.
- **raw-sources 유래** — 신규 대상 없음: `raw-sources/claude-code-opus-orchestration-setup.md` 는 summary 기존재(07-12 생성)·원본 미변경(mtime 07-11)이라 스킵. sidecar(`.cache/raw-md-sha/`) 비어 있음.
- **PDF 유래(.cache/extracted/)·fetched/·mcp-note** — 대상 없음(전부 비어 있음).
- 비고: `ingested: false` 전문 검색에 20260702 ea52 가 걸렸으나 frontmatter 는 `ingested: true` — 본문에 "`ingested: false`" 문자열이 인용되어 있는 false positive. frontmatter 앵커(`^ingested: false`) 기준으로 재확인해 대상에서 제외.

## 2026-07-30 (ingest)

- **session-logs 유래** — 미처리 23건 처리 (07-30 새벽 03:00~05:00 dev-blog cron: Research Dossier 11 [Linux Daily·Android Kernel·Opensource Trending·Opensource Curation·AI Coding Agents + Kernel Lens 6렌즈] + Newsletter Write 12 [Trending 교정 재시도 더블런 포함]). 뉴스레터 콘텐츠는 뉴스성으로 전량 스킵 (기존 방침 동일). 신규 문서 0건.
  - **갱신 2건**: [[llm-json-parse-retry-with-dump]] (교정 재시도 cron 실사용 2번째 실측 — Opensource Trending write `cfd8` 03:32 1차 후 `d0f7` 03:35 `[재시도]` 지시문 재발사. 07-29 에 이어 이틀 연속 같은 토픽에서만 발화해 토픽 상관 가능성 메모, 2차도 `assistant_turns: 0` 이라 성공 여부 미기록), [[dev-blog]] (07-30 운영 관찰 — Kernel Lens research 로그 소실 6/6 전면화[대응 write 6건이 dossier 를 입력으로 수신해 산출 자체는 확인], write 11/12 무응답성 로그 + `bdd6` 1턴 한 줄 선언 후 무산출, anti-bot 8번째 관측은 07-28 판정과 일치[`public-inbox-fetch` UA 통과 포함 — 07-28 "자기서술적 UA 통과" 판정의 재확인]해 bug 페이지 본문 변경 없음, credential 마스킹 span 파괴 재발)
  - **트리아지 기각 3건**: ① "dossier `verifiedDowngradeCount`·`seenBefore`·per-evidence `verified` 필드 신규" — 06-28 [[research-write-agent-separation]] 기수록 확인(로그상 07-03 부터 존재), 기각. ② "write 전건 무응답 = 파이프라인 전면 장애" — 헤드리스 로거의 assistant 출력 소실 특성(07-20 이래 기수록)으로 흡수, 기각. ③ "041931 로그에서 Wget UA 통과 실측" — 해당 로그에 UA 스윕 자체가 없음(트리아지 서브에이전트의 오귀속), 기각.
  - **스킵**: AI Coding Agents dossier 의 Cursor iPad "병목이 작성→감시·승인으로 이동" 통찰·Copilot GA 모델 opt-out 기본값 전환·usage API 시계열 단절 — 뉴스성 단일 출처(과잉추출 선례 06-27~07-07 동일 판정). Curation droppedCandidates 의 이중 라이선스(Fair Source `/ee` + MIT) 거절 사유 — 일회성 후보 평가 상세.
- **raw-sources 유래** — 신규 대상 없음: `raw-sources/claude-code-opus-orchestration-setup.md` 는 summary 기존재(07-12 생성)·원본 최종 커밋 07-11 로 미변경이라 스킵. sidecar(`.cache/raw-md-sha/`)가 비어 있어 source_sha256 백필도 보류(규칙 3 — 멱등 판정 불가하나 부작용 없음).
- **PDF 유래(.cache/extracted/)·fetched/·mcp-note** — 대상 없음(전부 비어 있음).

## 2026-07-29 (ingest)

- **session-logs 유래** — 21건 처리 (07-29 새벽 03:00~05:00 dev-blog cron: Research Dossier 6[Kernel Lens 6렌즈] + Linux Daily/Android Kernel/Opensource Trending/Opensource Curation/AI Coding Agents R+W 10 + Kernel Lens Write 4 + Opensource Trending write 더블런 1). 뉴스레터 콘텐츠(커널·오픈소스·AI 도구 뉴스)는 뉴스성으로 전량 스킵 (기존 방침 동일). 신규 문서 0건.
  - **갱신 3건**: [[newsletter-research-anti-bot-blocking]] (「2026-07-29 — 7번째 관측」절 — lore.kernel.org 차단 6일 연속(1ece·b662·feda·d7dd·425d·3f8a·d39c). `d39c` UA 스윕이 07-28 판정과 일치(git/2.43.0·Wget/1.21 통과, curl·python-requests 403). `3f8a` 에서 브라우저 Mozilla UA 가 처음으로 `200`+Anubis 챌린지 대신 `500 Server Error`("Oh noes!")를 반환하는 신규 변형 확인, 자체 수집기 UA + infradead.org 메일 아카이브로 우회, base64 CTE mbox 는 python 디코딩 처리. `b662` yhbt.net 미러·`425d` patchwork.kernel.org API 병행 지속. `1ece` 에서 NNTP 직접 접근(GREET 201·ARTICLE 220·HEAD 221·BODY 222)의 프로토콜 상세가 처음 확정 — 07-26 4번째 관측의 "NNTP 직접 접근 1건"에 대한 재실측), [[llm-json-parse-retry-with-dump]] (「재시도의 한계」절 말미에 한 단락 추가 — 2026-07-22 commit `e28df77` 로 도입된 attempt≥2 교정 재시도 프롬프트가 cron 실운영에서 발사된 것을 세션 로그로 첫 실측: Opensource Trending write 가 03:29(`eff4`) 1차 시도 후 03:31(`cec7`) `[재시도] 직전 응답이 유효한 JSON이 아니었습니다 …` 지시문이 붙어 재발사, `cec7` 도 `assistant_turns: 0` 이라 최종 성공 여부는 로그 미기록), [[dev-blog]] (07-29 운영 노트 — 위 두 갱신 요약 링크 + Kernel Lens 로그 소실 확산: research 된 6렌즈 중 `b662`·`d7dd`·`3f8a`·`d39c` 4건은 로그에 최종 dossier JSON 이 없으나 이 중 stable·arch-platform·gpu-ai 3건은 대응 write(`082f`·`cb54`·`8470`)가 dossier 를 입력받아 정상 발사돼 산출물 자체는 생성됨을 확인(로거의 assistant 출력 소실 특성의 연장), 보안(`b662`)·perf-rt(`425d`) 렌즈는 대응 write 자체가 배치에 부재(결손인지 로그 미캡처인지 미확정) + credential 마스킹 span 파괴 지속(lens write 4건 전부 `https://***:***@…`)). 각 출처 세션 단축 id: 1ece·b662·feda·d7dd·425d·3f8a·d39c·cec7.
  - **기각·스킵 판정**:
    ① 트리아지가 제기한 "`cb54`·`8470` evidence quote 가 주제와 무관한 텍스트(claim↔quote 정합성 갭 재발)" — 원문 대조 결과 정합 확인, 기각. 예: `8470` cold-reset 항목의 claim "fault-inject 가 정작 문제 구간을 건너뛴다는 지적" ↔ quote "That entirely defeats the purpose! :)" 일치(Rodrigo Vivi 가 게이팅이 검증 목적을 무력화한다고 반대하는 발언 그대로).
    ② 커널·오픈소스·AI 도구 뉴스 콘텐츠 전량(뉴스성, 기존 방침) — 스킵.
    ③ 커널 기술 지식(stable 백포트 결함 패턴·defconfig 위험·벤더 훅 다중 include·GKI bazel 목록·RISC-V ACPI 훅·DT ABI 정책 등) — 도메인 밖, 스킵.
    ④ Sashiko AI 리뷰 봇 관찰 — 커널 커뮤니티 콘텐츠(07-27 판정 동일), 스킵.
    ⑤ dossier 스키마·grounding 규칙 상세 — [[research-write-agent-separation]] 등에 이미 기수록, 스킵.
    ⑥ write 세션 `assistant_turns: 0` — 헤드리스 로거 특성으로 기수록(07-20 이래) 흡수, 신규 정보 없음.
    ⑦ AI Coding Agents 뉴스(Cursor 가격 조정 등) — 뉴스성, 스킵.
  - **mcp-note 유래** — 해당 없음 (type: mcp-note 파일 0건).
  - **raw-sources 유래** — `raw-sources/claude-code-opus-orchestration-setup.md` 는 원본 최종 변경(07-11 commit 6a9858e)이 기존 summary(07-12 작성)보다 앞서 변경 없음 — 멱등 스킵 (07-28 판정 동일, frontmatter·sidecar 모두 source_sha256 부재라 미설정 유지).
  - **PDF 유래(.cache/extracted/)** — 대상 파일 없음. **fetched/ 유래** — 대상 없음.

## 2026-07-28 (ingest)

- **session-logs 유래** — 26건 처리 (07-27 저녁 21:31~21:46 4건 + 07-28 새벽 03:00~05:01 22건: Research Dossier 13 + Newsletter Write 13, 전부 dev-blog cron). 뉴스레터 콘텐츠(커널·오픈소스·AI 도구 뉴스)는 뉴스성으로 전량 스킵 (기존 방침 동일). 신규 문서 0건.
  - **갱신 2건**: [[newsletter-research-anti-bot-blocking]] (「2026-07-28 — 6번째 관측」절 — lore.kernel.org Anubis 차단 지속(094c, "Access Denied" error 9e4edb5b6b850c41) + UA 5종 스윕 실측(14e9): `git/2.45.0`·`Wget/1.21`·`public-inbox-mirror` 통과(200), `python-requests`·curl 기본 UA 403. `Wget/1.21` 이 07-24엔 403·07-28엔 200 으로 판정이 시점에 따라 바뀜을 확인해 "브라우저형 UA 만 차단" 정밀화를 "알려진 스크레이퍼 UA 블록리스트 + 시점별 변동"으로 재정밀화 + 발신 1일 이내 lore 메시지는 patchwork 등 미러 폴백이 전부 미인덱스라는 신선도 한계 신규 기록), [[dev-blog]] (07-27 저녁~07-28 새벽 운영 노트 — linux-distro-stable·linux-toolchain 의 research/write 쌍이 새벽 cron(03~05시) 밖인 21:31~21:46 에 실행된 사례 최초 실측(원인 미상, [[macos-launchagent-catchup-behavior]] 가능성만 언급) + bbb5 dossier 의 claim↔quote 정합성 갭이 `verified: true` 상태로 부착된, 기존 06-28 기록보다 한 단계 악화된 사례 + credential 마스킹 span 파괴 지속(bbb5·36fe)). 각 출처 세션 단축 id: 094c·14e9·bbb5·36fe.
  - **기각·스킵 판정**:
    ① write 세션 assistant_turns: 0 — 07-23·07-25·07-27 판정(headless 로거 특성) 흡수, 신규 정보 없음
    ② 트리아지가 제기한 "dev-blog-collector/0.1 UA 로 lore 통과" — 원문 검증 결과 트렌딩 수집 스크립트(`collect-opensource.mjs`)의 UA 상수(`dev-blog-collector/0.1 (+opensource trending)`)를 lore 전용 변형(`(+lore body fetcher)` 등)과 혼동한 오독, 기각
    ③ 트리아지가 제기한 "bbb5 verifiedDowngradeCount: 4" — 원문 실측 0, 기각
    ④ 커널·OSS 커뮤니티 콘텐츠(vmwgfx TTM 에러 처리 지적, drm_panel.c.orig 유입, kconfirm, Assisted-by: Claude 표기 논의 등) — 도메인 밖 스킵
    ⑤ AI Coding Agents 뉴스(스테가노그래피 마킹·npm sourcemap 소스 유출·skills 형식 수렴·로컬 구동 가이드) — seenBeforeCount 9 재순환으로 기존 [[claude-code-source-leak-internals]] 등에 기수록, 스킵
  - **mcp-note 유래** — 해당 없음 (type: mcp-note 파일 0건).
  - **raw-sources 유래** — `raw-sources/claude-code-opus-orchestration-setup.md` 는 원본 최종 변경(07-11 commit 6a9858e)이 기존 summary(07-12 작성)보다 앞서 변경 없음 — 멱등 스킵 (07-27 판정 동일, frontmatter·sidecar 모두 source_sha256 부재라 미설정 유지).
  - **PDF 유래(.cache/extracted/)** — 대상 파일 없음. **fetched/ 유래** — 대상 없음.

## 2026-07-27 (ingest)

- **session-logs 유래** — 21건 처리 (07-27 새벽 dev-blog cron 03:00~04:49: Research Dossier 11 + Newsletter Write 9 + Linux Kernel Weekly Digest 1). 뉴스레터 콘텐츠(커널·오픈소스·AI 도구 뉴스)는 뉴스성으로 전량 스킵 (기존 방침 동일). 신규 문서 0건.
  - **갱신 2건**: [[newsletter-research-anti-bot-blocking]] (「2026-07-27 — 5번째 관측」절 — lore.kernel.org 차단의 2층 구조 실측: curl 기본 UA→nginx bare 403, 브라우저 UA→200+Anubis 챌린지, git/2.43 UA→양층 통과 raw mbox 정상 수신. "UA 스푸핑 무효" 단정을 브라우저형 UA 로 한정, 우회 채널에 git UA 추가. 출처 175a), [[dev-blog]] (07-27 운영 노트 — Weekly Digest 입력 dailies 가 7일 계약 대비 3건[07-19·07-23·07-25]으로 주중 결손의 주간 전파 실측 + weekly wrapper 의 externalSignals[lwn·phoronix RSS]·mainlineMerges 주입이 07-13 부터 존재 확인. 출처 1335)
  - **기각·스킵 판정**: ① 트리아지가 제기한 "write 전건 assistant_turns: 0 무응답" — 이미 인제스트된 07-26 로그도 동일 메타데이터(exit_reason: unknown, turns 0)로 headless 로거 특성이며 07-23("로거 기본값, 이상 신호 아님")·07-25(간헐 0턴 관측) 판정에 흡수, 신규 정보 없음. ② Weekly Digest 출력의 externalSignals "창발 필드" 의혹 — 원문 검증 결과 해당 파일은 user_prompt 1건뿐(출력 자체가 없음)이고 externalSignals 는 입력 wrapper 필드로 판명, 기각. ③ Android Newsletter seenBefore 9/16 등 반복 항목 과다 — 주말 뉴스 사이클 특성, 스킵. ④ dri-devel 메일링리스트의 sashiko-bot 자동 리뷰 지적 11건 — 커널 커뮤니티 콘텐츠(도메인 밖), 스킵. ⑤ dd23 의 CloudFlare 차단→yhbt.net 미러 폴백·mbox 파싱 — 07-26 4번째 관측에 기수록, 지속 확인만.
  - **mcp-note 유래** — 해당 없음 (type: mcp-note 파일 0건).
  - **raw-sources 유래** — `raw-sources/claude-code-opus-orchestration-setup.md` 는 원본 최종 변경(07-11)이 기존 summary(07-12 작성)보다 앞서 변경 없음 — 멱등 스킵 (frontmatter·sidecar 모두 source_sha256 부재라 미설정 유지).
  - **PDF 유래(.cache/extracted/)** — 대상 파일 없음 (디렉터리 비어 있음). **fetched/ 유래** — 대상 없음.

## 2026-07-26 (ingest)

- **session-logs 유래** — 27건 처리 (07-25 저녁 + 07-26 새벽 dev-blog 뉴스레터 cron: Opensource Curation Write 5 + Linux Kernel Lens Research/Newsletter 12 + Linux Daily/Android Kernel/Opensource Trending Research/Write 8 + AI Coding Agents Research/Write 2). 뉴스레터 기사 콘텐츠(커널·오픈소스·AI 도구 뉴스)는 뉴스성으로 전량 스킵 (기존 방침 동일).
  - **신규 1건**: ai-agent `bugs/dossier-evidence-kind-enum-reject` — dev-blog `opensource-curation` research 가 07-24·07-25 아침 cron 에서 반복 실패한 원인. research LLM 이 evidence 근거로 GitHub repo 를 들며 `evidence.kind: "repo"` 를 창발했으나 schema enum(`commit/thread/changelog/cve/article`) 밖이라 `validateEvidence()` 가 throw, evidence 1건 거부가 topic 전체 drop 으로 증폭 — 모니터링·알림 없이 이틀 연속 발생 후 발견. commit `823213a` 로 enum 에 `"repo"` 추가해 수정(3파일, 테스트 118/118). 도메인 핵심 파이프라인 버그라 첫 등장 즉시 기록.
  - **갱신 4건**:
    - [[llm-content-quality-guards]] — 「가드 5 의 오탐 클래스」절 추가. enum 수정 직후 write 재실행에서 CJK 가드가 중국어 repo `Lordog/dive-into-llms` 커밋 메시지 **원문 인용**을 혼입으로 오판해 4 attempt 전부 차단(결정론적 실패라 재시도 무효). 해소는 가드 완화가 아니라 dossier 재생성으로 해당 소스를 `droppedCandidates` 배제. 5/14 의 진짜 혼입(true positive)과 대비되는 첫 false positive 실측.
    - [[newsletter-research-anti-bot-blocking]] — 「2026-07-26 — 4번째 관측」절 추가. lore.kernel.org 403/Anubis 차단이 07-26 새벽 cron 로그 7건에서 지속 관측됐으나, patchwork.kernel.org API·infradead.org 메일 아카이브·yhbt.net 미러·NNTP 직접 접근·git ls-remote/shallow clone 다채널 우회가 실측돼 Research 5회·Write 4회 전부 산출 성공.
    - [[prompt-schema-pipeline-coupling]] — 「흔한 안티패턴」에 재발 변형 한 단락 추가. evidence.kind 케이스는 사람이 프롬프트/코드를 비동기로 고친 기존 사례들과 달리, LLM 이 스스로 enum 밖 값을 창발한 케이스 — prompt 값 목록과 validator enum 의 이중 정의가 결합 지점. 이 파일은 05-13 최초 작성 후 첫 갱신이라 「## 변경 이력」섹션 신설.
    - [[dev-blog]] — 「알려진 함정 — evidence.kind enum 거부 + CJK 인용 오탐 연쇄 (2026-07-25)」신설. 아침 cron 결손 → 22:22 enum fix → 22:28~31 write 4 attempt CJK 차단 → 22:40 dossier 재생성 성공 → `f956b88` 수동 복구의 전체 타임라인 요약, 상세는 신규 bugs 문서·llm-content-quality-guards 로 위임. 07-26 daily 정상 복귀 + anti-bot 4번째 관측을 1~2문장으로 언급.
  - **스킵**: 나머지 뉴스레터 콘텐츠(커널 패치·오픈소스 트렌드·AI 코딩 도구 뉴스) 전량 — 뉴스성, 파이프라인 메타 지식은 위 4건에 이미 흡수.
  - **mcp-note 유래** — 해당 없음 (type: mcp-note 파일 0건).
  - **raw-sources 유래** — `raw-sources/claude-code-opus-orchestration-setup.md` 는 변경 없음 (summary 기존재, 멱등 스킵).
  - **PDF 유래(.cache/extracted/)** — 대상 파일 없음. **fetched/ 유래** — 대상 없음.

## 2026-07-25 (ingest)

- **session-logs 유래** — 2026-07-25 dev-blog cron 로그 20건 처리 (03:00~04:32 사이클: Research Dossier 11 + Newsletter Write 9). 뉴스레터 콘텐츠(커널·오픈소스·AI 도구 뉴스)는 뉴스성으로 전량 스킵 (기존 방침 동일). 신규 문서 0건.
  - **갱신 2건**: [[newsletter-research-anti-bot-blocking]] (3번째 관측 반영 — dri-devel 렌즈 403 을 mbox.gz raw 엔드포인트로 우회 성공, 차단된 linux-kernel-security 렌즈가 10분 뒤 재발사돼 성공[재시도 장치 여부 미확정], cdn.kernel.org 폴백 시도·openQuestions 격리 지속, "자동 재시도 없음" 단정 완화), [[dev-blog]] (7/25 운영 노트 — 7/22 lockdown 의 `cwd: tmpdir()` 격리가 write 세션 로그 cwd 로 실증 + 정상 write 5건은 도구 없는 1턴 순수 산출로 에이전트형 표류 재발 없음 + write 4건·Android dossier 1건 `assistant_turns: 0` 간헐 무응답 잔존)
  - **스킵**: AI Coding write(033621) 입력 dossier 의 `***:***@` URL 마스킹 오탐은 [[gieok-session-log-url-credential-masking-false-positive]] 기수록 재발(신규 정보 없음), Trending·Curation dossier 의 병렬 서브에이전트 위임은 [[research-write-agent-separation]] 기수록 패턴 지속.
  - **mcp-note 유래** — 해당 없음 (type: mcp-note 파일 0건).
  - **raw-sources 유래** — `raw-sources/claude-code-opus-orchestration-setup.md` 는 원본 최종 변경(커밋 6a9858e, 2026-07-11)이 기존 summary(2026-07-12 작성)보다 앞서 변경 없음 — 멱등 스킵 (frontmatter·sidecar 모두 source_sha256 부재라 미설정 유지).
  - **PDF 유래(.cache/extracted/)** — 대상 파일 없음. **fetched/ 유래** — 대상 없음.

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


## 2026-10-03 (ingest — default/맥비)

- 사용자 요청으로 Notion Oracle Cloud 페이지 전체 115개 블록을 조회하고 기존 wiki/patterns/oracle-cloud-free-tier-setup.md와 비교했다. 핵심 가입·PAYG 내용은 기수록, 한국 가입 폼·운영 명령·OCI CLI 인증 재현·Windows 접속은 미수록으로 확인했다.
- 생성: raw-sources/2026-10-03-notion-oracle-cloud-free-tier.md (원본 Markdown·SHA256), wiki/patterns/oracle-cloud-operations-cheatsheet.md (누락 보충·시점 주의·internal).
- 수정: wiki/patterns/oracle-cloud-free-tier-setup.md (출처·보충문서 링크·변경 이력), wiki/index.md (신규 문서 등록), wiki/log.md (본 기록).
- 기존 2026-10-03 무료 한도·디스크 증설 조사 내용 보존. Notion 및 원격 서버 변경 없음. git commit/push 없음.
