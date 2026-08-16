<!-- kakao-db: ai-summary v1 -->
# AI 요약

## 인사이트·노하우

- **하네스 엔지니어링(Harness Engineering)에 대한 P9의 관점**: 기획·실행·평가·검증 구조로 에이전트 코딩 결과물을 보완하는 장치(code review, eval test용 단일 모델 추가, hook/script 덧대기). 그러나 P9는 *"컨텍스트 관리 + 프롬프트 잘 입힌 단일 모델 + 도메인 지식"이 하네스 엔지니어링보다 파급력이 크다*고 봄. 결국 도메인 지식과 올바른 아키텍처 설계·방향성 제시가 더 중요하며, 퀄리티 있는 결과물은 직접 손을 대야 한다는 입장. *"클로드코드나 코덱스 자체가 하네스 엔지니어링이 탑재된 툴"* 이라고 정리.
- **Anthropic × SpaceX 컴퓨팅 제휴 해석(P9)**: 주간 한도가 늘어난 것이 아니라 **5시간 세션 제약이 2배가 된 것**. SpaceX(xAI)가 낮은 활용률로 방치하던 GPU 자원을 Anthropic이 효율적으로 인수한 구도로 해석되며, AI 경쟁의 초점이 GPU 확보 → '활용 효율성'으로 이동 중.
- **YouTube 전체화면 우회 팁(P3)**: `youtube.com` 대신 `yout-ube.com` 도메인을 쓰면 전체화면 시청 가능.

## 추천·자료

### 도구·앱
- [Galaxy Connect (MS Store)](https://apps.microsoft.com/detail/9ngw9k44gq5f?hl=ko-KR) — 갤럭시 ↔ Windows 연결 도구.
- [voice-pro](https://github.com/abus-aikorea/voice-pro) — 음성 처리 오픈소스(한국인 작자).
- [Wave Terminal](https://www.waveterm.dev/) — 오픈소스 모던 터미널.
- [Carousels Generator](https://carousels-generator.com) — 캐러셀(SNS용 슬라이드) 생성기.
- [Project Quenq](https://quenq.com/) — 불명(생산성 툴 추정).
- [Notable People](https://tjukanovt.github.io/notable-people) — 위인 데이터 시각화.
- [ToolFK](https://www.toolfk.com/) — 각종 웹 도구 모음.
- [Flowstep AI](https://flowstep.ai/) — AI 디자인 엔지니어.
- [GhostWriterrr](https://ghostwriterrr.com/) — LinkedIn/X용 AI 고스트라이터.
- [LocalSend](https://github.com/localsend/localsend) — 로컬 네트워크 파일 전송.
- [DeepSeek TUI](https://github.com/Hmbown/DeepSeek-TUI) — DeepSeek 터미널 UI(기여자 모집 중, 스타 급상승).
- [pdfcraft](https://github.com/PDFCraftTool/pdfcraft) — PDF 도구 모음.
- [DeepClaude](https://github.com/aattaran/DeepClaude) — Claude 관련 도구.
- [RepoBar](https://github.com/steipete/RepoBar) — 레포 정보를 메뉴바에 띄우는 도구.
- [SuperSet 2](https://superset.sh/) — AI 에이전트용 코드 에디터(대기 신청).
- [pay.sh](https://pay.sh/) / [solana-foundation/pay](https://github.com/solana-foundation/pay) — 에이전트가 API 결제를 처리하는 시스템.
- [Autopus-ADK](https://github.com/Insajin/autopus-adk) — 에이전트 개발 키트.
- [agent-skills-eval](https://github.com/darkrishabh/agent-skills-eval) — Claude Skills의 효과를 경험적으로 측정하는 평가 툴.

### Claude Code 플러그인 패키지(P3, Nate Herk 영상에서 추천)
```
/plugin install skill-creator@claude-plugins-official
/plugin install superpowers@claude-plugins-official
npx get-shit-done-cc --claude --global
/plugin marketplace add mksglu/context-mode
/plugin install context-mode@context-mode
/plugin marketplace add thedotmack/claude-mem
/plugin install claude-mem
/plugin install frontend-design@claude-plugins-official
```

### API·레퍼런스
- [public-apis/public-apis](https://github.com/public-apis/public-apis) — ★42만, 50+ 카테고리 무료 API 모음. "뭘 만들지?"에 대한 재료 창고로 활용 가능(P8: 환율+주식+뉴스 → 투자 대시보드 등 조합 예시 제시).
- [public-apis-4Kr](https://github.com/yybmion/public-apis-4Kr) — 위 레포의 **한국 버전**(P2 공유).

### 콘텐츠·자료
- [Long Black 노트 1972](https://longblack.co/note/1972?ticket=NT2619a852d1fbbae1faedc8594d458479c073) — 불명(P3 공유).
- [Code with Claude 키노트 영상](https://youtube.com/watch?v=MIaGudVvagw&si=YurlL4ZT2MzYvJBM)
- [Hermes for Android (유튜브)](https://youtu.be/xw4afp4-j9o?si=TdjyO1K-qBkEaCmd)
- [Programbench](https://programbench.com/) — Meta AI 벤치마크.
- [Unity AI Open Beta 공식 영상](https://youtu.be/QyGY6QZuAj0)
- [Claude Skills 추천 — Nate Herk](https://youtu.be/eRS3CmvrOvA?si=-2IVvWKvZwm_6FVO)
- [Lattice Log — codewiki × TradingAgents 분석 글](https://lattice-log.vercel.app/blog/2026-05-06-codewiki-tradingagents-finance-review)
- [Claude Code 입문서(한국어)](https://lattice-log.vercel.app/blog/2026-05-06-claude-code-korean-books)
- [Lattice Live 모닝](https://lattice-log.vercel.app/news/2026-05-07-morning) / [이브닝](https://lattice-log.vercel.app/news/2026-05-07-evening) / [나이트](https://lattice-log.vercel.app/news/2026-05-07-night) — Radariq(P2)의 AI 큐레이션.

## 의미있는 링크

- [Anthropic × SpaceX 컴퓨팅 한도 발표](https://www.anthropic.com/news/higher-limits-spacex) — 5시간 세션 제약 2배 확장, 채팅방에서 가장 활발히 분석된 뉴스.
- [엉클잡스(P9) Threads — Anthropic·xAI 협력 해설](https://www.threads.com/@unclejobs.ai/post/DYBFcHUkzX3?xmt=AQG0P3aCLNPLJ2jsLz6BX8C8QFCQ_VJ9_NMc6lOevBWsBg) — *"한 달 전까지 앙숙이었던 머스크가 손을 뻗어 앤트로픽 벨트를 풀어줬다"* 비유.
- [엉클잡스 Threads — Claude 컨퍼런스 라이브 해설](https://www.threads.com/@unclejobs.ai/post/DYBIcAMEwnG?xmt=AQG0YBKNW6rhid14CV7cwlYHrie6MEPc2MfE7_viU9S9Fg)
- [네이버·컬리 동맹 강화(MSN)](https://www.msn.com/ko-kr/news/other/손잡은-네이버-컬리-더-깊어진다-단골력-키우며-쿠팡-견제/ar-AA22u7Qr) — "네컬리" 신조어로 호응.
- [Anthropic SI 설립(byline.network)](https://byline.network/2026/05/621/)
- [한국 사찰의 휴머노이드 로봇 입교식(Korea JoongAng Daily)](https://koreajoongangdaily.joins.com/news/2026-05-06/business/tech/Zen-and-the-art-of-robot-maintenance-Humanoid-robot-joins-Buddhist-faith-in-Seoul-temple-ceremony-/2585731) — 화제성 높음.
- [Supabase, Git 없는 브랜칭 기본 채택](https://api.daily.dev/r/HVK11oOMZ)
- [GDG Seoul "1인 빌더를 위한 하네스 엔지니어링"(이정민)](https://www.linkedin.com/posts/jyoung105_gdgseoulbuildwithai2026jeongminlee-ugcPost-7457947894415958016-x62D) — 채팅방에서 하네스 엔지니어링 토론 촉발.
- [@mingikim_ajung 스레드 — "현실토큰" 충전 영상](https://www.threads.com/@mingikim_ajung/post/DYCdVKxib8D?xmt=AQG0IouIXC6THTUgiGOE1kAly0wEVuqunrS3lOfzZY_BHw) — 사모펀드(MBK 커넥트웨이브 인수) 매각금이 10억씩 입금되는 장면. P2가 *"창업가에겐 가장 짜릿한 순간"* 이라며 본인도 곧 경험 예정이라 밝힘.
- [OpenSearch-VL 논문(arXiv)](https://arxiv.org/abs/2605.05) — HuggingFace Daily Papers 61업보트, 멀티모달 검색 에이전트 오픈 레시피.

## 결정·약속·일정

- **2026-05-09(토) 20:00 — P9(엉클잡스/응제) 유튜브 라이브**: 주제 *"클로드코드로 Notion MCP 연동해서 블로그 자동화 — 개쩌는 품질의 아티클 만드는 법"*. 다수 참여자 *필참* 의사 표명.

## 반응이 컸던 화제

- **하네스 엔지니어링 vs 단일 모델+컨텍스트 관리 논쟁**: P9의 *"하네스 엔지니어링은 하나의 방법론일 뿐, 도메인 지식과 아키텍처 설계가 더 본질적"* 입장에 다수가 반응. P14가 추가 설명을 요청해 P9가 길게 부연.
- **중국 AI/제조 강세 vs 한국 의대 쏠림**: P8의 중국 출장 후기에서 *"중국은 사람이 AI, 저작권 개념도 없고 0베이스 없이 100부터 시작"*, *"로봇의 90%를 뽑아내는 이유"* 발언 → P3가 KBS 다큐 *"공대에 미친 중국 ↔ 의대에 미친 한국"* 으로 호응.
- **쿠팡 적자 전환 화제**: P9 *"쿠팡 적자 전환되었다"* vs P6 *"로켓배송 도파민에 빠지면 절대 못 빠져나옴, 절대 망할 수 없음"* — 가벼운 논쟁.
- **"현실토큰" 영상**: 사모펀드 매각금 10억씩 입금되는 스레드 영상에 다수가 *"불나겠다", "현실토큰 짱"* 등으로 호응. P2가 본인도 곧 경험할 예정임을 암시.
- **P9 라이브 얼굴 공개 여부**: *"존못인데 개봉해야 하나"* 농담에 다수 호응.

## 자동화·시스템 적용 후보

- **Lattice Live 포맷(P2/Radariq)**: 시간대별(모닝/이브닝/나이트) AI·개발 뉴스를 *✱ 제목 → 본문 → 왜 지금 → 써먹기 → 출처* 구조로 큐레이션. 항목별 "vibe-coder가 사이드 프로젝트에 어떻게 쓸지" 한 줄을 강제하는 템플릿이 인상적 — 뉴스 다이제스트 자동화 워크플로 참고.
- **Public APIs 조합형 토이 프로젝트 패턴(P8)**:
  - 환율 + 주식 + 뉴스 → 투자 대시보드
  - 날씨 + 캘린더 + 지도 → 나들이 플래너
  - 이메일 검증 + IP + 안티멀웨어 → 가입 보안 게이트
  - 공휴일 + 환율 → 해외출장 비용 자동계산기
- **Claude Skills 평가 자동화**: `agent-skills-eval`로 SKILL.md 작성 후 모델 성능 향상 여부를 경험적으로 검증하는 워크플로.
- **MCP 결제 계층(devto)**: 에이전트 간 작업 요청에 대한 결제 표준 부재가 지적됨 → `pay.sh` 류 솔루션과 결합 가능성.
- **eBPF로 MCP 도구 모니터링**: MCP 도구가 실제로 어떤 시스템 자원을 건드리는지 eBPF로 관찰하는 접근.

## 질문·미해결

- P25의 *"P9에게 AI 집중 과외 받고 싶다"* → P9가 *"과외 서비스 론칭해야겠다"* 로 답했으나 구체 결정 없음.
- P7의 *"AI Expo 후기 있을까요?"* 질문 → 답변 없이 종료.
- 5개 동시 진행 유튜브 라이브 동시 녹화 방법 — P3 *"방법을 모릅니다"*, P9 *"복잡하긴 하겠다"* 로 미해결.

---

## 요약 통계

- 메시지 수: 119
- 기간: 2026-05-07 00:40 ~ 2026-05-07 23:59

### URL (63개)

- https://apps.microsoft.com/detail/9ngw9k44gq5f?hl=ko-KR
- https://github.com/abus-aikorea/voice-pro
- https://longblack.co/note/1972?ticket=NT2619a852d1fbbae1faedc8594d458479c073
- https://www.anthropic.com/news/higher-limits-spacex
- https://youtube.com/watch?v=MIaGudVvagw&si=YurlL4ZT2MzYvJBM
- https://www.threads.com/@unclejobs.ai/post/DYBFcHUkzX3?xmt=AQG0P3aCLNPLJ2jsLz6BX8C8QFCQ_VJ9_NMc6lOevBWsBg
- https://news.naver.com/mnews/article/092/0002421495
- https://youtu.be/xw4afp4-j9o?si=TdjyO1K-qBkEaCmd
- https://programbench.com/
- https://www.youtube.com/watch?v=~~~
- ... +53 more

### 해시태그

- #쿠팡: 1

### 멘션

- @claude: 3
- @unclejobs: 2
- @Radariq: 1
- @context: 1
- @mingikim_ajung: 1

---

# Fronmpt Academy — 2026-05-07

- 메시지 수: 119

**00:40** [P3]
갤럭시 커넥트 

<https://apps.microsoft.com/detail/9ngw9k44gq5f?hl=ko-KR>

**00:41** [P2]
<https://github.com/abus-aikorea/voice-pro>

**00:46** [P3]
<https://longblack.co/note/1972?ticket=NT2619a852d1fbbae1faedc8594d458479c073>

**00:53** [P7]
드론님 올려 주신 내용 다른 분들에게 공유해도 괜찮을까요?

**00:53** [P14]
전 초등학생이 부럽..ㅠ 하하

**07:40** [P3]
Claude * SpaceX 협력

<https://www.anthropic.com/news/higher-limits-spacex>
Code with Claude 키노트

<https://youtube.com/watch?v=MIaGudVvagw&si=YurlL4ZT2MzYvJBM>

**07:49** [P14]
SpaceX는 커서하고 썸씽있었는데
클로드하고도 뭘 하나보네요

**07:58** [P1]
Anything but OpenAI 아닐까요

**08:07** [P14]
그럴듯요

**09:16** [P9]
<https://www.threads.com/@unclejobs.ai/post/DYBFcHUkzX3?xmt=AQG0P3aCLNPLJ2jsLz6BX8C8QFCQ_VJ9_NMc6lOevBWsBg>
한달전까지 앙숙이었는데
일론 머스크가 손을 뻗어서 앤트로픽의 벨트를 풀어줬습니다
컴퓨팅 자원 확보 협약을 맺었죠 

**09:17** [P14]
잠깐 사이가 안좋았나봐요?!

**09:17** [P9]
주간 한도가 늘어난 건 아닙니다 5h 세션 제약이 2배가 된 겁니다

**09:17** [P14]
아 그것때문인가 
이란전쟁 관련해서

**09:17** [P9]
오늘 발표를 자세히 풀어보았습니다

**09:17** [P14]
감사합니다!

**09:20** [P3]
<https://news.naver.com/mnews/article/092/0002421495>
Hermes for Android

<https://youtu.be/xw4afp4-j9o?si=TdjyO1K-qBkEaCmd>
Meta AI Benchmark

<https://programbench.com/>

**09:33** [P3]
<https://www.youtube.com/watch?v=~~~>

=> 전체화면으로 볼 경우

<https://www.yout-ube.com/watch?v=~~~>

**09:40** [P14]
OpenAI 비영리, 영리 소송이 아직 ~ing군요

**09:45** [P9]
아직입니다 ㅋㅋ
<https://www.threads.com/@unclejobs.ai/post/DYBIcAMEwnG?xmt=AQG0YBKNW6rhid14CV7cwlYHrie6MEPc2MfE7_viU9S9Fg>
클로드 컨퍼런스가 열리고 있는데 라이브로 볼 수 있습니다
전략과 컨퍼런스에 관한 가벼운 터치를 해보았습니다 ㅎ

**09:49** [P14]
곧 서울에서도 열릴 가능성이 있겠네요

**10:07** [P19]
일이 많으니 정리된 뉴스를 기대합니다!
ㅎ 에이전트야 해줘

**10:11** [P2]
📡 Lattice Live

▸ AI / Models / Papers

✱ 오픈코드 v1.14.40 릴리즈
오픈코드의 최신 버전인 v1.14.40이 출시되었습니다. 이 버전에서는 여러 가지 개선과 버그 수정이 포함되어 있습니다. 특히, `.well-known/opencode` 구성 파일을 지원하고, CORS 헤더를 적용하여 브라우저 클라이언트의 접근성을 개선하였습니다. 또한, 여러 가지 버그를 수정하여 안정성을 높였습니다. 이러한 업데이트는 개발자들이 더욱 안정적이고 효율적인 개발 환경을 제공합니다.
왜 지금: 최신 버전의 오픈코드를 사용하면 개발 효율성을 높이고 안정성을 개선할 수 있습니다.
써먹기: vibe-coder는 사이드 프로젝트에서 오픈코드의 최신 버전을 사용하여 개발 환경을 최적화할 수 있습니다.
출처: opencode_rel · <https://github.com/anomalyco/opencode/releases/tag/v1.14.40>

✱ Anthropic SDK, Agent 기능 강화
Anthropic TypeScript SDK v0.95.0가 릴리즈되었습니다. 이번 업데이트로 Managed Agents의 멀티 에이전트 지원, 웹훅, Vault 검증 기능이 추가되었습니다. 더 복잡하고 동적인 AI 에이전트 구축을 위한 기반이 마련되었습니다. 특히 에이전트 간의 상호작용과 외부 시스템 연동이 더욱 유연해졌습니다.
왜 지금: AI 에이전트의 복잡성과 연동성이 중요해지는 시점에서, SDK 차원의 기능 강화는 필수적입니다. 이는 LLM 기반 서비스 개발의 생산성 향상과 직결됩니다.
써먹기: 팀 협업 에이전트나 외부 서비스 연동이 필요한 사이드 프로젝트에 적용하여 에이전트의 확장성과 연동성을 높여보세요.
출처: anthropic_ts_rel · <https://github.com/anthropics/anthropic-sdk-typescript/releases/tag/sdk-v0.95.0>

✱ 클라우드 코드 2026
Anthropic에서 개최한 Code w/ Claude 2026 행사에서 Simon Willison이 진행한 라이브 블로그 포스팅이 공개되었습니다. 이 포스팅에서는 Claude와 관련된 다양한 주제들이 논의되었습니다. Claude는 Anthropic에서 개발한 AI 모델로, 다양한 코드 작성 및 개발 작업을 자동화하는 데 사용됩니다. 이 행사에서는 Claude의 최신 기능과 개발자들이 Claude를 활용하여 어떻게 더 효율적인 개발 환경을 구축할 수 있는지에 대한 정보가 공유되었습니다.
왜 지금: 클라우드 코드 2026 행사에서 공유된 정보는 개발자들이 최신 기술 트렌드를 따라가고 Claude를 활용하여 개발 효율성을 높이는 데 도움이 됩니다.
써먹기: vibe-coder는 사이드 프로젝트에서 Claude를 활용하여 자동화된 코드 작성 및 개발 작업을 수행할 수 있습니다.
출처: simonw · <https://simonwillison.net/2026/May/6/code-w-claude-2026>

✱ 바이브 코딩과 에이전트 공학
사이먼 윌리슨이 지적한 대로, 직관적인 '바이브 코딩'과 구조화된 '에이전트 기반 공학'의 경계가 모호해지고 있다. AI 도구가 진화하면서 개발자는 점점 더 명시적 지시보다 맥락 기반 상호작용을 통해 작업을 진행한다. 이는 생산성 향상과 동시에 코드 품질 관리의 새로운 도전을 의미한다. 사이드 프로젝트에서도 단순 자동화를 넘은 지능형 워크플로우 설계가 필요해질 전망이다.
왜 지금: 에이전트 기반 도구가 실사용 단계에 진입하면서 개발 패러다임이 재정의되고 있다.
써먹기: 자신의 프로젝트에 에이전트 메모리(mem0)와 브라우저 제어(browser-use)를 결합해 반자동 워크플로우를 구축해보라.
출처: simonw · <https://simonwillison.net/2026/May/6/vibe-coding-and-agentic-engineering>

▸ Dev / Tools / Community

✱ React 19.2.6
React의 최신 버전인 19.2.6이 출시되었습니다. 이 버전에서는 React Server Components에 대한 타입 강화와 성능 개선이 이루어졌습니다. 이러한 업데이트는 개발자들이 더 효율적이고 안정적인 코드를 작성할 수 있도록 도와줍니다. 특히, 성능 개선은 사용자 경험을 향상시키는 데 중요한 역할을 합니다. 개발자들은 이 버전을 통해 더 나은 성능과 안정성을 제공하는 애플리케이션을 개발할 수 있습니다.
왜 지금: 최신 버전의 React를 사용하면 성능과 안정성이 향상됩니다.
써먹기: 새로운 프로젝트에서 React 19.2.6를 사용하여 더 효율적이고 안정적인 코드를 작성할 수 있습니다.
출처: react_rel · <https://github.com/facebook/react/releases/tag/v19.2.6>

전체 보기: <https://lattice-log.vercel.app/news/2026-05-07-morning>

실시간 AI 큐레이션 오픈카톡방:
<https://open.kakao.com/o/peJMmUsi>

**10:14** [P3]
Carousels Generator

<https://carousels-generator.com>
Project Quenq

<https://quenq.com/>
Notable People

<https://tjukanovt.github.io/notable-people>
Various Tools Collection

<https://www.toolfk.com/>

**10:22** [P3]
AI Design Engineer

<https://flowstep.ai/>
AI GhostWriter for LinkedIn & X

<https://ghostwriterrr.com/>

**10:29** [P2]
<https://lattice-log.vercel.app/blog/2026-05-06-codewiki-tradingagents-finance-review>

코드위키로 LLM 트레이딩 에이전트 분석

**10:58** [P3]
Wave Open Source Terminal

출시된지는 조금 오래되었어요~~

<https://www.waveterm.dev/>

**11:33** [P3]
Unity AI Open Beta 공식

<https://youtu.be/QyGY6QZuAj0>

**12:16** [P3]
Claude Skills 추천 | Nate Herk

<https://youtu.be/eRS3CmvrOvA?si=-2IVvWKvZwm_6FVO>

/plugin install skill-creator@claude-plugins-official

/plugin install superpowers@claude-plugins-official

npx get-shit-done-cc --claude --global

/plugin marketplace add mksglu/context-mode
/plugin install context-mode@context-mode

/plugin marketplace add thedotmack/claude-mem
/plugin install claude-mem

/plugin install frontend-design@claude-plugins-official


**12:28** [P3]
한국에서 로봇이 공식적으로 불교에 입교하여 공식적인 입교 의식 거행됨

<https://koreajoongangdaily.joins.com/news/2026-05-06/business/tech/Zen-and-the-art-of-robot-maintenance-Humanoid-robot-joins-Buddhist-faith-in-Seoul-temple-ceremony-/2585731>
Branching Without Git Is Now The Default | Supabase의 새로운 기본 방식

<https://api.daily.dev/r/HVK11oOMZ>

**12:54** [P3]
Autopus-ADK

<https://github.com/Insajin/autopus-adk>

<https://youtu.be/VKPOoHuiM5o>

**17:16** [P9]
이번주 주말에 유튜브 라이브 진행하려고 합니다
주제는 클로드코드로 블로그 자동화 하기 ㅋㅋㅋ

**17:16** [P6]
닥치고 참여 !!!

**17:16** [P2]
필참합니다. 

**17:22** [P25]
몇시에 하십니꺼

**17:23** [P9]
이번주 토요일 20시에 하려고 합니다 ㅋㅋ 

**17:23** [P2]
얼굴 공개입니까

**17:23** [P25]
오호 .......
녹화해도되나요 ... 아마도 ... 시댁일거같아서요 ... 하하하 ...

**17:23** [P9]
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ 존못인데 개봉해야하나요
유튜브 라이브로 할거랔ㅋ

**17:23** [P25]
개봉박두

**17:23** [P2]
그거보려고 들어가는건데

**17:24** [P25]
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

**17:24** [P9]
일시 : 5월 9일 20시 
주제 : 클로드코드로 notion mcp 연동해서 블로그 자동화

아티클 장인으로서 개쩌는 품질의 콘텐츠 만드는 법 알려드림

**18:00** [P9]
ㅋㅋ 귀엽네요 햄토리

**18:09** [P9]
라이브 썸네일입니다 

**18:10** [P14]
오
잘생긴 얼굴은 잠깐 내려두셨군요

**18:16** [P9]
몬난이 임니다 ㅎ

**18:22** [P2]
📡 Lattice Live

▸ AI / Models / Papers

✱ NVIDIA TensorRT-LLM v1.3.0rc14
NVIDIA TensorRT-LLM v1.3.0rc14 버전이 출시되었습니다. 이 버전에서는 Mamba 하이브리드 모델, Qwen3.5, Nemotron Super V3 등 다양한 모델의 지원이 개선되었습니다. 또한, VisualGen 서빙과 분산 서빙, 라우팅 기능이 향상되었습니다. 이 업데이트는 LLM의 성능과 효율성을 개선하는 데 중점을 둔 것으로 보입니다.
왜 지금: 최신 LLM 기술을 활용하여 성능과 효율성을 개선하고자 하는 개발자들에게 의미 있는 업데이트입니다.
써먹기: vibe-coder는 이 업데이트를 통해 자신의 사이드 프로젝트에서 더 빠르고 효율적인 LLM 모델을 구축하고, 더 나은 성능을 달성할 수 있습니다.
출처: tensorrt_llm_rel · <https://github.com/NVIDIA/TensorRT-LLM/releases/tag/v1.3.0rc14>

✱ MHPR 벤치마크
MHPR은 인간 중심의 장면에 대한 인식 및 추론을 평가하는 새로운 벤치마크입니다. 이 벤치마크는 다양한 데이터 세트와 자동 캡션 생성 파이프라인을 제공하여 인간의 인식과 추론 능력을 평가합니다. MHPR은 현재의 비전-언어 모델의 능력을 평가하고 향상시키는 데 도움이 될 것입니다. MHPR은 인간의 이해를 평가하는 새로운 방법을 제공합니다. MHPR은 다양한 인간 중심의 장면을 평가합니다.
왜 지금: 현재의 비전-언어 모델의 능력을 평가하고 향상시키는 데 도움이 됩니다.
써먹기: 사이드 프로젝트에서 MHPR을 사용하여 인간의 인식과 추론 능력을 평가하고 모델의 성능을 향상시킬 수 있습니다.
출처: arxiv_csai · <https://arxiv.org/abs/2605.03485>

✱ AI 에이전트, 환상에서 현실로
AI 에이전트 논의가 '환상'에서 '실용'으로 옮겨왔다. 컨텍스트 유지, 비용 최적화, 워크플로우 통합 등 실제 비즈니스 가치를 찾는 질문이 늘었다. Reddit에서 2026년 5월 현재 AI 에이전트 스택의 현실적인 신호 10개를 추렸다.
왜 지금: AI 에이전트 구축 시점, 가장 실용적인 접근법을 파악해야 한다. hype를 넘어선 실제 성공 사례와 기술적 난제 해결 과정을 알아야 한다. 이를 통해 미래 AI 에이전트 개발의 방향성을 잡을 수 있다. ',,,
출처: devto_ai · <https://dev.to/dana_pierce_9fce5c36fb5db/mcp-memory-and-real-roi-10-reddit-threads-mapping-the-ai-agent-shift-3ggl>

✱ LVLM 저작권 콘텐츠 망각 벤치마크
거대 비전 언어 모델(LVLM)은 웹 데이터 학습 과정에서 저작권이 있는 캐릭터나 로고를 기억하고 재생성할 위험이 있습니다. 머신 언러닝이 해결책으로 제시되지만, 복합적인 멀티모달 LVLM 환경에서 그 효과를 평가하기는 어려웠습니다. CoVUBench는 LVLM의 저작권 콘텐츠 망각 성능을 평가하기 위해 고안된 최초의 벤치마크입니다. 합성 데이터와 체계적인 시각 변형을 활용해 망각 효율성 및 모델 유용성 유지 여부를 엄격하게 측정합니다.
왜 지금: AI가 생성하는 콘텐츠의 저작권 이슈가 점점 커지는 상황에서, LVLM의 윤리적이고 법적인 활용을 위한 필수 기술이기 때문입니다.
써먹기: 자신만의 LVLM 파인튜닝 시, 의도치 않은 저작권 침해 가능성을 미리 점검하고 제거하는 데 CoVUBench 접근법을 적용해 볼 수 있습니다.
출처: arxiv_csai · <https://arxiv.org/abs/2605.03547>

✱ AI 상호작용 모델 벤치마크 'iWorld-Bench'
AI 에이전트의 지능 향상에 필수적인 상호작용 월드 모델 연구가 활발하지만, 평가를 위한 통일된 벤치마크가 부족했습니다. iWorld-Bench는 33만 개의 비디오 클립 기반 데이터셋과 6가지 태스크 유형을 제공하여, 실제 환경에서의 인지, 추론, 행동 능력을 통합적으로 평가합니다. 이를 통해 기존 모델의 한계를 파악하고 향후 연구 방향을 제시합니다.
왜 지금: AGI 달성을 위한 실질적인 에이전트 능력 평가의 필요성이 대두되고 있기 때문입니다. LLM 기반 에이전트가 실제 환경과 상호작용하는 능력을 객관적으로 측정하는 것이 중요해졌습니다. iWorld-Bench는 이러한 평가의 표준을 제시합니다.
출처: arxiv_csai · <https://arxiv.org/abs/2605.03941>

✱ Anthropic, SpaceX와 AI 컴퓨팅 파워 확보 '빅딜'
Anthropic이 SpaceX와 대규모 AI 컴퓨팅 클러스터 접근 계약을 체결했다. 이는 Claude 모델 개발 및 확장에 필요한 막대한 연산 자원을 확보하는 결정적 계기가 될 것이다. 최고 수준의 AI 모델과 최첨단 컴퓨팅 인프라의 결합은 AI 기술 발전의 새로운 지평을 열 것으로 기대된다.
왜 지금: AI 모델 성능의 한계는 결국 하드웨어, 특히 컴퓨팅 파워에 달려 있음을 보여주는 상징적인 사건이다. 최신 AI 모델을 다룬다면 하드웨어 요구사항을 반드시 고려해야 한다. LLM 개발 경쟁은 단순히 모델 아키텍처를 넘어 인프라 확보전으로 확산되고 있다.
출처: r_anthropic · <https://www.reddit.com/r/Anthropic/comments/1t61zpi/anthropic_just_signed_a_deal_with_spacex_to>

▸ Dev / Tools / Community

✱ 에이전트 스킬 평가
에이전트 스킬을 평가하는 툴인 agent-skills-eval이 나왔습니다. 이 툴은 에이전트 스킬이 모델의 성능을 실제로 향상시키는지 측정할 수 있습니다. SKILL.md 파일을 작성하고 평가를 추가하면, 에이전트 스킬이 모델의 성능을 향상시키는지 여부를 경험적으로 확인할 수 있습니다. 이 툴은 에이전트 스킬을 개발하는 개발자들에게 유용한 도구가 될 수 있습니다.
왜 지금: 에이전트 스킬의 성능을 정확하게 평가하기 위해
써먹기: vibe-coder는 사이드 프로젝트에서 에이전트 스킬의 성능을 평가하고 개선하는 데 이 툴을 사용할 수 있습니다.
출처: hn_top · <https://github.com/darkrishabh/agent-skills-eval>

✱ 브라우저에서 실행되는 LispE
LispE는 브라우저에서 실행되는 Lisp 언어입니다. 사용자는 브라우저에서 직접 Lisp 코드를 작성하고 실행할 수 있습니다. 예를 들어, FizzBuzz 함수를 정의하여 1부터 100까지의 숫자 중에서 15의 배수인 숫자에 대해 'fizzbuzz'를 출력하도록 할 수 있습니다. 이 기능은 개발자들이 브라우저에서 Lisp 언어를 쉽게 사용할 수 있도록 해줍니다.
왜 지금: 현재 브라우저에서 실행되는 Lisp 언어를 사용하여 개발 효율성을 높일 수 있습니다.
써먹기: vibe-coder는 사이드 프로젝트에서 브라우저에서 실행되는 LispE를 사용하여 간단한 알고리즘을 구현하고 테스트할 수 있습니다.
출처: lobsters · <https://naver.github.io/lispe>

전체 보기: <https://lattice-log.vercel.app/news/2026-05-07-evening>

**18:26** [P25]
엉클님한테 ai 집중과외 받고싶 .... 

**18:29** [P3]
1인 빌더를 위한 하네스 엔지니어링 문서

<https://www.linkedin.com/posts/jyoung105_gdgseoulbuildwithai2026jeongminlee-ugcPost-7457947894415958016-x62D>

**18:30** [P9]
ㅋㅋ과외서비스 론칭해야겟구만요

**18:31** [P14]
킹응제님

**18:31** [P9]
이정민님은 스모어톡 cso로 알고있는데
1인 빌더를 위한 전략은 
뭔가 좀 주제가 .. ㅋㅋ
참고로 다들 요새 하네스 엔지니어링이라는 주류를 보시겠지만 파급력과 효과는 , 컨텍스트 관리와 프롬프트를 잘 입힌 단일 모델 + 기본 도메인 지식(개발관련 등)을 갖추는 것보단 약하긴 합니다

**18:33** [P2]
동의합니다

**18:34** [P14]
응제님 실례지만 조금 더 구체적으로 설명 부탁드려도 될까요?

**18:38** [P9]
하네스 엔지니어링이 기업마다 정의하는 게 좀 다르긴 하지만

기획 실행 평가 검증 등이 뒷받침 되는 구조로 기존 에이전트 코딩이 만드는 결과물을 보완하게 하는 장치라고 보시면 됩니다

code review, eval test 등을 위해 각종 단일 모델을 추가하거나 hook, script로 확인하는 구조를 덧대는것이죠

그런데 결국 사용자, 유저의 의도대로 되느냐 물어보면, 
코드를 제대로 짠 것과 에이전틱 코딩으로 나온 산출물의 퀄리티는 기대와 종종 다릅니다 

그래서
실제 보여지는 결과물간의 간극이랑 코드 적합성을 줄이는 방안으로 작년에 컨텍스트 엔지니어링, 올해는 하네스 엔지니어링이 나오는데 결국은 하나의 방법론일뿐이라는 뜻입니다
그러니 도메인 지식과 올바른 아키텍쳐 설계라던가 방향성 제시를 잘하는것보다 못하다고 생각합니다 ㅋㅋ
그걸 구축 잘하시는분들은 월등한 결과물을 잘 내실수도 있겟지만 저는 퀄리티 있게 만들려면 직접 손을 대야한다고 보는 주의라서 이러한 의견도 있구나라고 봐주시면 좋겠습니다!

**18:41** [P14]
결국 마냥 특별한 무언가는 아니라는 말씀이군요
감사합니다

**18:54** [P9]
클로드코드나 코덱스 자체가 하네스 엔지니어링이 탑재된 툴이라고 보시면 됩니다 ㅋㅋ

**19:06** [P14]
넵!

**19:33** [P3]
Code Editor for AI Agents | SuperSet 2 대기신청

<https://superset.sh/>
Agents Pay for Any API

<https://pay.sh/>

<https://github.com/solana-foundation/pay>

**19:37** [P19]
응제님이 엉클잡스 님과 동일인물인가요?

**19:38** [P13]
네 ㅋㅋ

**19:38** [P19]
아하!? 

**19:42** [P3]
손잡은 네이버·컬리, 더 깊어진다

<https://www.msn.com/ko-kr/news/other/손잡은-네이버-컬리-더-깊어진다-단골력-키우며-쿠팡-견제/ar-AA22u7Qr>

**19:44** [P13]
네컬리!

**19:49** [P9]
쿠팡 적자 전환돼다던데 ㅋㅋㅋ
슈퍼레스트도 좋슴니다 추천
ㅋㅋㅋㅋㅋㅋ 이름바까야게ㅛ네여ㅕㅕ

**19:51** [P3]
샵검색: #쿠팡 적자

**20:07** [P6]
쿠팡은 절대 망할수가 없음 ㅋㅋㅋ
로켓배송의 도파민에 한 번 빠지면, 그냥 절대 못 빠져나옴
실수로 주문하면, 바로 순식간에 환불까지 !

**20:25** [P3]

보고 싶은 YouTube 라이브가 있는데... 5개 전부 20시 시작 ㅋㅋㅋㅋ


**20:25** [P6]
ㅎㄷㄷㄷㄷ
시작했겠네요 ㅋㅋㅋ

**20:29** [P3]
근데 5개 전부 포기하고 다른거 보고 있어요~~ ㅋㅋㅋㅋ

**20:33** [P6]
혹시 링크 공유는 좀 곤란하시죠? ㅎㅎ
그냥 궁금해서, 여쭤봤습니당 ㅎㅎ

**20:37** [P3]
라이브 링크 인가요?

**20:38** [P9]
5개 녹화 켜놓으시면 .. ㅋㅋ

**20:40** [P3]
그게 한번에 가능해요? 전 방법을 모릅니다.

**20:51** [P9]
ㅋㅋㅋ 그렇네요 복잡하긴하겟네여 

**21:40** [P7]
요즘 다들 라이브 많이 하시네요 ㅋㅋㅋ

**21:52** [P3]
Anthropic SI 설립

<https://byline.network/2026/05/621/>
@Radariq 요즈음 Lattice 잘보고 있습니다. ㅎㅎ

Claude Code 입문서
<https://lattice-log.vercel.app/blog/2026-05-06-claude-code-korean-books>

**21:58** [P2]
제가 얻는 도움이 더 많습니다.
감사합니다!

**22:00** [P6]
저도 감사합니다 ^^
지금 들어가보니, 너무 좋네요 ㅎㅎ

**22:01** [P2]
저희 커뮤니티도 들려주십쇼~
저의 자유로운 일기장입니다..ㅎㅎ
📡 Lattice Live

▸ AI / Models / Papers

✱ 클로드, 22만 GPU로 진화
Anthropic이 22만 개의 NVIDIA GPU로 구성된 'Colossus 1' 인프라를 확보하고 클로드 인퍼런스 성능을 대폭 강화했습니다. 머스크가 설립한 xAI가 낮은 활용률로 방치했던 자원을 효율적으로 인수한 것으로, Anthropic의 운영 독립성보다는 실질적인 스케일링 능력이 승부를 가를 수 있음을 시사합니다. 이는 AI 경쟁의 핵심이 GPU 확보에서 '활용 효율성'으로 이동했음을 의미합니다.
왜 지금: AI 경쟁의 판도를 바꿀 수 있는 인프라 전략이 현실화되고 있습니다.
써먹기: 고성능 인퍼런스를 요구하는 사이드 프로젝트라면 vLLM이나 Ollama로 유사한 효율을 로컬에서도 실험해보세요.
출처: r_anthropic · <https://www.reddit.com/r/Anthropic/comments/1t67z6s/anthropic_just_got_220000_gpus_from_the_man_who>

✱ MCP, 에이전트 간 통신 표준
MCP는 Anthropic에서 오픈소스로 공개한 에이전트 간 통신 표준이다. 웹에서 HTTP가 정보를 주고받는 표준이었듯, MCP는 에이전트들이 서로 정보를 주고받는 표준으로 자리 잡고 있다. 현재 여러 플랫폼에서 MCP를 채택하고 있지만, 에이전트 간의 금융 거래를 위한 결제 계층이 부족하다. 이 결제 계층은 에이전트들이 서로 작업을 요청하고 그 대가를 지불하는 방식을 표준화할 수 있다.
왜 지금: 에이전트 간의 통신과 협력이 점점 더 중요해지고 있기 때문에 MCP를 알아야 한다.
써먹기: vibe-coder는 사이드 프로젝트에서 MCP를 사용하여 에이전트 간의 통신과 협력을 구현할 수 있다.
출처: devto_ai · <https://dev.to/kirothebot/mcp-is-the-http-of-ai-agents-wheres-the-payment-layer-54kc>

✱ MCP 도구와 eBPF
MCP 도구는 새로운 API 인터페이스입니다. eBPF는 이 도구가 실제로 어떤 자원을 사용하는지 확인할 수 있습니다. MCP 도구는 에이전트의 관점에서 함수와 입력 스키마로 구성됩니다. 그러나 이 도구가 호출될 때 시스템에서 발생하는 실제 작업은 매우 복잡합니다. eBPF를 사용하면 이 도구가 시스템의 어떤 부분을 접근하는지 자세히 확인할 수 있습니다.
왜 지금: 최근 MCP 서버가 대량으로 출시됨에 따라, 이 도구의 사용법과 내부 동작을 이해하는 것이 중요해졌습니다.
써먹기: vibe-coder는 사이드 프로젝트에서 MCP 도구와 eBPF를 사용하여 시스템의 자원 사용을 최적화하고, 에이전트의 동작을 더 잘 이해할 수 있습니다.
출처: devto_ai · <https://dev.to/ingero/mcp-tools-are-new-api-surfaces-ebpf-sees-what-they-actually-touch-29fi>

▸ Dev / Tools / Community

✱ Node.js 26.1.0
Node.js 26.1.0 버전이 출시되었습니다. 이 버전에는 실험적인 node:ffi 모듈이 포함되어 있습니다. 이 모듈은 동적 라이브러리를 로딩하고 네이티브 심볼을 JavaScript에서 호출하는 기능을 제공합니다. 또한 버퍼와 암호화 관련 기능이 개선되었습니다. 이 버전은 개발자들이 Node.js를 더 강력하고 유연하게 사용할 수 있도록 도와줍니다.
왜 지금: 최신 기능과 보안 패치를 얻기 위해
써먹기: 새로운 node:ffi 모듈을 사용하여 네이티브 라이브러리와 상호 작용하는 사이드 프로젝트를 개발할 수 있습니다.
출처: nodejs_rel · <https://github.com/nodejs/node/releases/tag/v26.1.0>

전체 보기: <https://lattice-log.vercel.app/news/2026-05-07-night>

**22:07** [P2]
[AI] 멀티모달 검색 에이전트 오픈 레시피 'OpenSearch-VL' 공개

오늘 HuggingFace Daily Papers에서 61개 업보트를 기록한 'OpenSearch-VL'은 텍스트와 이미지를 함께 이해하는 멀티모달 검색 에이전트(Multimodal Search Agent — 복수 모달 정보를 검색·추론하는 AI 에이전트)를 구축하는 오픈 레시피를 제시한다. 단순히 모델 가중치만 공개하는 방식이 아니라 데이터 구성·훈련 파이프라인·평가 프레임워크를 모두 포함한 '레시피(recipe)' 전체를 오픈소스로 제공해, 독자적인 멀티모달 검색 에이전트를 처음부터 만들고자 하는 팀이 참조할 수 있는 완전한 청사진을 제공한다. RAG(검색 증강 생성) 파이프라인에 시각 이해를 결합하거나, 문서·이미지를 동시에 검색·추론하는 에이전트를 설계하는 방향을 고민하고 있다면 구조적 참고자료로 살펴볼 만하다.

(출처: hf_papers — <https://arxiv.org/abs/2605.05>

**22:09** [P8]
중국에 출장다녀와서 이제 글씁니다:)

새로운 AI 모델도 아니고, 화려한 프레임워크도 아니에요.
그냥 무료 API들 모아놓은 깃헙 레포 하나.

근데 별이 42만개.

public-apis/public-apis 라는 레포입니다.
50개 넘는 카테고리에 수백 개 API가 정리되어 있어요.

날씨, 환율, 주식, 암호화폐, 책, 영화, 음식, 지도, 번역,
이메일 검증, IP 조회, 보안, 캘린더, 폰트, QR코드...
어지간한 건 다 있습니다.

음식점으로 치면
하나하나는 그냥 소소한 사이드 메뉴들입니다.
계란말이, 김치, 무생채.

근데 잘 섞으면 한 상 차림이 됩니다.

환율 + 주식 + 뉴스 → 나만의 투자 대시보드
날씨 + 캘린더 + 지도 → 주말 나들이 플래너
이메일 검증 + IP + 안티멀웨어 → 간단한 가입 보안 게이트
공휴일 + 환율 → 해외출장 비용 자동계산기

지금 시대에 "이거 만들어줘" 한 줄이면
웬만한 토이 프로젝트는 한두 시간이면 끝납니다.

진짜 문제는 항상 똑같아요.
"뭘 만들지?"

이 레포는 그 "뭘"의 재료 창고입니다.

**22:11** [P2]
오!
저는 그럼.. 한국 버전을 공유하겠습니다.
<https://github.com/yybmion/public-apis-4Kr>

**22:11** [P13]
<https://github.com/public-apis/public-apis>
이거군요

**22:12** [P8]
몰러유. 바보 멍청이라서용. 

**22:12** [P13]
좋은 정보감사합니다^^
중국의 AI 발전이… 정말 무섭네요
다른이야기지만 ㅋ

**22:12** [P8]
중국은 사람이 AI. 
저작권의 개념도 없고…
거의 공유 경제. 
0베이스 없구요. 100부터 시작. 

**22:13** [P3]
공공 API들은 지원한지 꽤 지났죠~~

**22:14** [P8]
로봇의 90프로를 뽑아내는 이유가 있지요. 
중국은 하드웨어 강국인데,
소프트웨어도 집중하고 있습니다. 

**22:14** [P2]
로봇님
홍콩도 다녀오셨나요?

**22:15** [P8]
뭐 전세계 다닙니다만…

**22:15** [P2]
중국 가시면 다들 홍콩 들르시지 않으시나요?

**22:15** [P8]
전혀요. 굳이…

**22:15** [P2]
아하!
제가 아는 교수님이 홍콩에서 로봇 전문 교수님 
뵜어가지고 여쭤본겁니다. 

**22:19** [P8]
네…최근에 미국에서 로봇으로 유명하신분이 홍콩에서 강연하긴했습니다.

**22:19** [P2]
그분이신줄 알았습니다..ㅎㅎ

**22:31** [P6]
너무하셔 ㅋㅋㅋㅋ

**23:02** [P3]
"공대에 미친 중국 <=> 의대에 미친 한국" KBS 다큐가 생각나네요 ㅋㅋㅋㅋ

**23:02** [P2]
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ
북경대, 칭화대만 봐도 알수 있죠
중국에서 누가 의사되고 싶어합니까~

**23:03** [P9]
<https://www.threads.com/@mingikim_ajung/post/DYCdVKxib8D?xmt=AQG0IouIXC6THTUgiGOE1kAly0wEVuqunrS3lOfzZY_BHw> 
현실토큰 충전되는 속도 장난아님니다 

**23:05** [P2]
번호공개 레전드네 마지막

**23:06** [P9]
강의팔걸 그랫나.. ㅋㅋ

**23:13** [P6]
ㅋㅋㅋㅋㅋㅋㅋㅋ

**23:17** [P26]
ㅋㅋㅋㅋㅋㅋㅋ

**23:17** [P7]
불나겠네요 

**23:18** [P26]
역시 현실토큰이..짱..

**23:19** [P9]
10억씩 쌓이는중 ㅋㅋ

**23:19** [P7]
와...
저거 뭐죠 ㅋㅋㅋㅋ

**23:19** [P2]
사모펀드 매각금입니다. 
저렇게 꽂혀요. 10억씩
나눠서
저기 커넥트웨이브는 mbk가 인수한 기업이고
가장 짜릿한 순간이죠 창업가한텐

**23:21** [P9]
해보셧다는..? 

**23:22** [P2]
이제 해봐야죠
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

**23:23** [P7]
화이팅입니다 ㅋㅋㅋㅋ
ai expo 후기 있을까요?? 

**23:28** [P6]
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

**23:44** [P3]
LocaSend

<https://github.com/localsend/localsend>
DeepSeek TUI

<https://github.com/Hmbown/DeepSeek-TUI>
PDF Tools

<https://github.com/PDFCraftTool/pdfcraft>
DeepClaude

<https://github.com/aattaran/DeepClaude>
RepoBar

<https://github.com/steipete/RepoBar>

**23:47** [P2]
쩐다...

**23:52** [P3]
더 드리고 싶지만 꾹 참는 것으로 ㅋㅋㅋㅋ

**23:53** [P2]
네 내일도 보내셔야죠

**23:58** [P9]
딥식 tui 기여자 찾는다고 봣는데
어느새 스타수가 ;; 

**23:59** [P2]
velocity 미쳤네요

