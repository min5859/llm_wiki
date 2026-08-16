<!-- kakao-db: ai-summary v1 -->
# AI 요약

## 추천·자료

- [AI Prompting Course with Andrew Ng](https://drive.google.com/file/d/1jfp3Upp8DeHxpKweMz-_jQA3uveXx2wN/view) — Andrew Ng의 AI 프롬프팅 강의 자료. 프롬프트 엔지니어링 입문/심화 학습용.
- [Hallmark (Nutlope)](https://github.com/Nutlope/hallmark) — 불명(저장소 설명 없음). AI 관련 도구로 추정.
- [프롬프트 한 줄로 화면이 나오는 시대 — 당근마켓 블로그](https://medium.com/daangn/프롬프트-한-줄로-화면이-나오는-시대-당근스러운-화면을-만드는-법-0bc268f819c7) — 당근마켓이 AI 프롬프트로 UI를 생성하는 방법론을 설명한 글. 디자인 일관성 유지 노하우 포함.
- [Claude Code와 함께한 웹 성능 개선 이야기 — SK devocean](https://devocean.sk.com/blog/techBoardDetail.do?id=168230) — Claude Code를 실무 웹 성능 개선에 적용한 사례.
- [AI 스페셜리스트와 자동사냥 — 하네스로 제어하는 AI 파이프라인 (무신사 테크블로그)](https://techblog.musinsa.com/ai-스페셜리스트와-자동사냥-하네스로-제어하는-ai-파이프라인-6c578f8bd1fb) — AI 에이전트를 하네스(harness) 구조로 제어하는 파이프라인 설계 사례.
- [Harness Graph (GitHub)](https://github.com/hong-seongmin/harness_graph) — 위 무신사 파이프라인과 연관된 하네스 그래프 구현체.
- [바이브코딩으로 수익화 SaaS 만들기 — 바이브마피아 (YouTube)](https://youtu.be/hGtPrLQ6Fxk) — 6시간 이상 분량의 바이브코딩으로 SaaS를 실제 수익화하는 과정 영상.
- [Giving Agents Computers — Ivan Burazin, Daytona (Latent Space)](https://www.latent.space/p/daytona?publication_id=1084089) — AI 에이전트에게 컴퓨터 환경을 제공하는 Daytona 접근법 소개.
- [Wave Terminal](https://www.waveterm.dev/) — 현대적인 터미널 도구. AI 워크플로에 활용 가능하다는 언급.
- [wmux (GitHub)](https://github.com/openwong2kim/wmux) — cmux의 Windows 버전 터미널 멀티플렉서.
- [Qwen WebWorld-8B (HuggingFace)](https://huggingface.co/Qwen/WebWorld-8B) — Qwen의 웹 환경 특화 8B 모델.
- [ByteDance Lance (HuggingFace)](https://huggingface.co/bytedance-research/Lance) — ByteDance 리서치팀의 모델.
- [Cohere command-a-plus-05-2026-w4a4 (HuggingFace)](https://huggingface.co/CohereLabs/command-a-plus-05-2026-w4a4) — Cohere의 최신(2026-05) 모델, w4a4 양자화 버전.
- [CrustData WarmIntro](https://tools.crustdata.com/warmintro) — 본인/회사 LinkedIn을 입력하면 유사 회사를 검색해주는 서비스. 영업·네트워킹용.

---

## 인사이트·노하우

- **도구 종속 탈피 권장**: IDE 기반 AI 코딩 툴(Antigravity 등)이 버전 업 시 작업 방식이 크게 바뀌는 문제 → "도구에 종속되지 않는 터미널 환경의 Claude Code 또는 Codex 사용" 을 권장. IDE 변화에 영향받지 않는 CLI 기반 워크플로가 더 안정적.
- **Claude Code 병렬 에이전트로 토큰 급소진**: Claude Code에서 에이전트를 병렬로 실행하면 맥스 요금제를 20분 안에 소진 가능. 토큰 사용량 계획 시 병렬 실행 규모를 반드시 고려해야 함.
- **리팩터링 반복의 오버엔지니어링 위험**: 오래된 프로젝트를 AI로 10회 반복 리팩터링 → "완전 오버엔지니어링" 발생. AI 리팩터링은 횟수 제한과 목표 범위를 명확히 설정해야 함.
- **Antigravity 2.0 변경 이슈**: Antigravity가 "AI 기반 IDE"에서 2.0으로 넘어오면서 기존 작업방식이 크게 달라짐. 기존 방식 유지 방법은 [Reddit 스레드](https://www.reddit.com/r/google_antigravity/comments/1thvz53/wtf_is_antigravity_20_where_did_my_ide_go/?tl=ko) 참고.
- **특정 키워드 기반 학습 자료 활용법**: 키워드 중심으로 구성된 자료는 "무료 Gemini에 키워드를 던져주고 상세 설명 요청"하는 방식으로 보완 가능.

---

## 의미있는 링크

- [Microsoft, Claude Code 지원 중단 — The Verge](https://www.theverge.com/tech/930447/microsoft-claude-code-discontinued-notepad) — 채팅 말미에 공유된 뉴스. Microsoft가 Claude Code를 중단했다는 내용으로 추정. AI 개발 도구 생태계 변화 관련.
- [eteho_labs Threads 포스트 (에르메스단 프롬프트 공유)](https://www.threads.com/@eteho_labs/post/DYoerGMgODh?xmt=AQG0Z6pU785WV0iq8tsP-3Nk0pTj2fjyyb7_PhMDbYEZ6w) — "덕후가 이긴다"는 맥락에서 공유된 프롬프트 자료. 하단에 프롬프트 공유 포함.
- [unclejobs.ai — Zed 다중 모델 에이전트 추천 Threads](https://www.threads.com/@unclejobs.ai/post/DYotFGYCfy4?xmt=AQG06grQhe40Nax6qmXCDaSJMGo-czeMyxdahMwgJ6UatA) — "다중 모델 에이전트를 굴린다면 Zed를 꼭 추천"이라는 실무 추천 포스트.
- [Web Academy 자료 모음](https://web-academy-4237b.web.app/) — 비전공자 개발 입문을 위한 핵심 키워드 기반 자료 모음. Claude Code 맥스 요금제 분량을 써서 제작.

---

## 결정·약속·일정

- **2기 모집 시작**: Fronmpt Academy 2기 모집 공지 (2026-05-22). 기존 멤버에게는 별도 연락 예정.
- **차주 라이브 예정**: @KTREE 및 운영진 라이브 방송 예정 (구체적 날짜 미명시).

---

## 반응이 컸던 화제

- **Web Academy 자료 모음 공개**: 여러 멤버가 "집대성", "알차다", "아껴봐야겠다" 등 긍정 반응. 비전공자도 접근 가능한 AI 개발 입문 자료를 Claude Code로 직접 제작했다는 점에서 공감과 호기심이 집중됨.
- **Zed + 다중 모델 에이전트**: "새로운 표준으로 자리잡을 것", "프로젝트별로 다르게 띄울 수 있어 멀티 세션 유지 짱" → 즉각적인 팔로업(설치 의향 표명)이 나왔고 터미널 도구 목록(zed, wave, cmux, wmux, warp)으로 대화가 확장됨.

---

## 자동화·시스템 적용 후보

- **하네스(Harness) 기반 AI 파이프라인 패턴**: 무신사 사례처럼 AI 에이전트를 하네스로 제어하는 구조. 여러 에이전트를 안전하게 연결·감독하는 아키텍처로 도입 검토 가치 있음.
- **Zed 멀티 세션 운영**: 프로젝트별로 다른 모델 조합을 독립 세션으로 띄우는 방식. 다중 모델 에이전트 워크플로에 적합.
- **Claude Code 병렬 에이전트 활용**: 대규모 코드 생성·변환 작업 시 병렬 에이전트로 속도 극대화 가능 (단, 토큰 소진 속도 주의).

---

## 질문·미해결

- **Antigravity 2.0에서 기존 IDE 작업방식 유지 방법**: P21이 질문했고 [Reddit 링크](https://www.reddit.com/r/google_antigravity/comments/1thvz53/wtf_is_antigravity_20_where_did_my_ide_go/?tl=ko)와 [dcinside 링크](https://gall.dcinside.com/mgallery/board/view/?id=thesingularity&no=1198925)가 참고로 공유됐으나, 구체적인 해결 절차는 대화 내에서 확인되지 않음.
- **AI 리팩터링 + 문서 검증 자동화 방법**: P20이 "리팩터링과 전체 문서 검증을 어떻게 하는지" 질문했으나 명확한 답변 없이 대화가 마무리됨.

---

## 요약 통계

- 메시지 수: 65
- 기간: 2026-05-22 01:27 ~ 2026-05-22 22:41

### URL (23개)

- https://drive.google.com/file/d/1jfp3Upp8DeHxpKweMz-_jQA3uveXx2wN/view
- https://github.com/Nutlope/hallmark
- https://medium.com/daangn/프롬프트-한-줄로-화면이-나오는-시대-당근스러운-화면을-만드는-법-0bc268f819c7
- https://devocean.sk.com/blog/techBoardDetail.do?id=168230
- https://techblog.musinsa.com/ai-스페셜리스트와-자동사냥-하네스로-제어하는-ai-파이프라인-6c578f8bd1fb
- https://github.com/hong-seongmin/harness_graph
- https://youtu.be/hGtPrLQ6Fxk
- https://www.reddit.com/r/google_antigravity/comments/1thvz53/wtf_is_antigravity_20_where_did_my_ide_go/?tl=ko
- https://gall.dcinside.com/mgallery/board/view/?id=thesingularity&no=1198925
- https://youtu.be/rg3kpGMr4js?si=mHwN3UueDU6s9j-b
- ... +13 more

### 멘션

- @unclejobs: 2
- @KTREE: 1
- @all: 1
- @eteho_labs: 1

---

# Fronmpt Academy — 2026-05-22

- 메시지 수: 65

**01:27** [P7]
AI Prompting Course with Andrew Ng

<https://drive.google.com/file/d/1jfp3Upp8DeHxpKweMz-_jQA3uveXx2wN/view>
Hallmark

<https://github.com/Nutlope/hallmark>

**01:36** [P7]
프롬프트 한 줄로 화면이 나오는 시대, ‘당근스러운 화면’을 만드는 법

<https://medium.com/daangn/프롬프트-한-줄로-화면이-나오는-시대-당근스러운-화면을-만드는-법-0bc268f819c7>
Claude Code와 함께한 웹 성능 개선 이야기

<https://devocean.sk.com/blog/techBoardDetail.do?id=168230>
AI 스페셜리스트와 자동사냥 — 하네스로 제어하는 AI 파이프라인

<https://techblog.musinsa.com/ai-스페셜리스트와-자동사냥-하네스로-제어하는-ai-파이프라인-6c578f8bd1fb>
Harness Graph

<https://github.com/hong-seongmin/harness_graph>

**02:10** [P7]
바이브코딩으로 수익화 SaaS 만들기 by 바이브마피아

현재 6시간 넘게 하고 있습니다. ㅎㄷㄷ

<https://youtu.be/hGtPrLQ6Fxk>

**07:57** [P21]
아니, aintigravity가 기존에 'AI 기반 코드 편집기(IDE)'였다가, 2.0넘어오면서...완전 바뀌어 버렸는데...기존의 작업방식을 유지하려면 어떻게 해야하죠?

<https://www.reddit.com/r/google_antigravity/comments/1thvz53/wtf_is_antigravity_20_where_did_my_ide_go/?tl=ko>

**09:10** [P18]
이사님! <https://gall.dcinside.com/mgallery/board/view/?id=thesingularity&no=1198925>
공유드린 방법대로 한번 시도해보시죠!

**09:35** [P21]
감사합니다. ^^

**09:43** [P18]
이사님! 무엇보다 도구에 종속되지않는 터미널 환경의 클로드 코드 혹은 코덱스 사용을 추천드립니다 ^^

**11:22** [P7]
<https://youtu.be/rg3kpGMr4js?si=mHwN3UueDU6s9j-b>
<https://youtu.be/-wcdSZywJ1I?si=_YDfbFxtlsDhr7UO>

**11:41** [P7]
Giving Agents Computers — Ivan Burazin, Daytona

<https://www.latent.space/p/daytona?publication_id=1084089>

**14:17** [P1]
<https://www.threads.com/@unclejobs.ai/post/DYoPTswCfDn?xmt=AQG0dMmnKycmSJcuxmbBUWNIZM1Kh6xb5gDLLuZC6x_WaQ>
@all 오래 기다리셨습니다
2기 모집을 시작합니다
더 강력하게 업그레이드 되어서 돌아왔습니다
기존 멤버분들께는 별도로 연락드리겠습니다!

**15:09** [P1]
처음오신분들 반갑습니다 
왕초보 분들은 공지사항 가볍게 읽어보시면
도움이 되는 정보들 많으실거에요

이상, 콘텐츠왕 엉클잡스 올림
ㅋㅋㅋㅋㅋㅋㅋㅋ

**15:09** [P20]
와 대박..

**15:10** [P18]
반갑습니다~
무엇이 필요하신지 잘몰라서 일단 공유드립니다!
<https://web-academy-4237b.web.app/>
ㅋㅋㅋ

**15:11** [P1]
AI계 효자손입니다 제가 ㅋㅋ

**15:12** [P20]
존경함나

**15:29** [P10]
엥 이거머죠
그야말로 집대성이네요 
잡스님은
AI계의 황제
아닙니까
하하

**15:30** [P22]
유용한 자료 감사합니다

**15:30** [P18]
제 맥스 요금제를 20분만에 소진시킨!!
실제로 필요한 핵심 항목으로만 구성했습니다 ㅋ
저 또한 비전공으로 개발을 시작했거든요^^

**15:31** [P10]
아 요금내야하나요 

**15:31** [P18]
아뇨

**15:31** [P10]
맥스요금제 20분만에 소진이라고 하셔서

**15:31** [P23]
wow 감사합니다 내용 알찬데요?!

**15:31** [P18]
그냥 보시면됩니다!

**15:31** [P23]
만드는데 20분만에 요금 다 쓰셨다는 의미같아요

**15:32** [P18]
아 ㅋㅋ 저 컨텐츠 만드는데 많은 토큰이 소모되었다는뜻이었스빈다^^
네네^^

**15:32** [P10]
와
감사합니다
이거
아껴봐야겠는데요
맛있는거 사드리고싶네요 나중에

**15:33** [P18]
특정 키워드 기반이라 상세 설명이 필요하다면 무료 제미나이에게 키워드 던져주고 상세 설명 요청하시면됩니다!

**15:35** [P10]
머스크가 만약에 보면
칭찬할듯

**15:40** [P1]
@KTREE 차주에 라이브있을 예정입니다 ;) 
아 물론 저도 ㅎ

**15:51** [P10]
책으로 출간하셔야겠는데요
잘 보겠습니다

**16:15** [P20]
맥스요금제 어떻게 그렇게 잘 쓰죠,,,

**16:20** [P7]
엄청나십니다. ㅎㅎㅎㅎ

**16:36** [P18]
ㅋㅋㅋㅋㅋㅋ에이전트가 병렬로 실행을.했습니다 ㅋ

**16:52** [P10]
오픈클로로 하셨나요

**16:53** [P18]
클로드 코드로 하였습니다!

**16:53** [P10]
넵
I Respect U

**16:54** [P5]
오래되고 운영 안하는 프로젝트 10회 리팩터링 실시 하니까 토큰 소진 되던데요 ㅎㅎ

**16:56** [P18]
저번에 리펙토링 계속 돌려보니깐. 와.. 완전 오버엔지니어링 되더라구요 ㅋ

**16:56** [P5]
맞습니다.. 소생불가라 한 번 어찌 만드나 테스트할때 써봤어요

**16:57** [P18]
네네^^ 

**17:00** [P20]
리팩터링이랑 과정이 
전체적인 문서 검증하는걸까요??
저도 그런거하고싶은데 당최 못하네요,,

**17:49** [P1]
덕후가 이깁니다
<https://www.threads.com/@eteho_labs/post/DYoerGMgODh?xmt=AQG0Z6pU785WV0iq8tsP-3Nk0pTj2fjyyb7_PhMDbYEZ6w>
아래 프롬프트도 공유되어 있으니 관심있으신분들은 한번 보시길 권해드립니다!
에르메스단 열일합니다 ㅋㅋ

**17:50** [P24]
ㅋㅋ

**17:50** [P5]
헐

**18:33** [P1]
<https://www.threads.com/@unclejobs.ai/post/DYotFGYCfy4?xmt=AQG06grQhe40Nax6qmXCDaSJMGo-czeMyxdahMwgJ6UatA>
새로운 표준으로 자리잡게 될것같습니다
다중 모델 에이전트를 굴리신다면 zed를 꼭 추천드립니다

**18:58** [P25]
크으..! 좋아요!리포완료! 밤에 당장 깔아서 써봐야겠습니다!

**19:00** [P1]
프로젝트별로 다르게 띄울수 있어서 멀티 세션 유지 짱입니다 ㅎ

**19:11** [P7]
zed, wave, cmux, wmux, warp 등 너무 좋은 것 같아요~~

**19:33** [P1]
wave, wmux는 처음들어보네요 

이거 터미널 관련 툴만 콘텐츠 다뤄도 한달내도록 뽑을듯 하것어요 ㅋㅋ

**21:05** [P7]
wmux => cmux의 윈도우 버전

wave는 진짜 ㅎㅎㅎㅎ
<https://www.waveterm.dev/>
<https://github.com/openwong2kim/wmux>

**21:40** [P26]
안녕하세요 ^^

**21:40** [P18]
빈갑습니다!!^^

**21:41** [P1]
오 반갑습니다
뭔가 브랜드사 대표님 같은 느낌이시네요 ㅎㅎ

**22:07** [P26]
저밖에없어요 ㅠㅠ ... 그래서 바이브코딩열심히 해보려고 공부중입니다

**22:08** [P18]
멋집니다!

**22:08** [P26]
감사합니다^^ 좋은 공간 마련해주셔서 감사합니다 

**22:10** [P18]
저는 스킬 만들고 .. 이제 런닝 가려고합니다 ㅋ

**22:15** [P7]
와우~~ 이 시간에 러닝이라니

**22:19** [P18]
네네 ㅋ 밤에 뛰기 좋아요!ㅋ

**22:20** [P7]
전 이른 아침에 러닝을 합니다. ㅎㅎ

**22:25** [P2]
러닝 너무 좋습니다~

**22:25** [P7]
<https://www.theverge.com/tech/930447/microsoft-claude-code-discontinued-notepad>

**22:25** [P18]
맞아요! 숨이 턱까지 차오르면 모든 잡념이 사라지죠~

**22:26** [P7]
Qwen Web World

<https://huggingface.co/Qwen/WebWorld-8B>
ByteDance Lance

<https://huggingface.co/bytedance-research/Lance>
Cohere

<https://huggingface.co/CohereLabs/command-a-plus-05-2026-w4a4>

**22:41** [P7]
본인/회사 링크드인 입력하면 유사 회사 검색해주는 서비스

<https://tools.crustdata.com/warmintro>

