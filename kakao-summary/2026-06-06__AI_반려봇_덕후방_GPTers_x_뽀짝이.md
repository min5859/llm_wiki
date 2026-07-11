<!-- kakao-db: ai-summary v1 -->
# AI 요약

## 인사이트·노하우

- **Claude API 529 Overloaded 에러**: `API Error: 529 Overloaded. This is a server-side issue, usually temporary` — 서버 측 일시적 과부하로, 잠시 기다리면 자동 복구됨. 상태 확인은 [status.claude.com](https://status.claude.com) 에서 가능.
- **보급형 로컬 LLM 구성 제안**: M4 Mac Mini(16GB) + Gemma 4 12B + 오픈클로(or 헤르메스) 조합으로 "LLM 토큰 비용 없이" 로컬 AI 에이전트 운영이 가능하다는 실증 사례 공유. LM Studio로 모델 구동, VS Code에서 앱 빌드 테스트.
- **미니PC 트레이딩 연동**: COM ActiveX 방식 증권 API는 맥미니에서 어렵고, 한국투자증권 REST API(한투 API)는 맥미니에서 정상 작동 중이라는 사용자 경험 공유. 자연어 인터페이스로도 한투 API 연동 가능하다는 언급.
- **크롬 자동조작 미해결 이슈**: "다른 확장 UI가 페이지 위에 떠있다"는 이유로 크롬 자동조작이 안 되는 문제 — 재시작해도 미해결 (→ 미해결 항목 참고).

---

## 추천·자료

- **`korean-call-transcriber`** (`pip install korean-call-transcriber`): 바이브코딩으로 제작된 한국어 통화 전사 파이프라인. Whisper GPU 가속 전사 + 화자 분리, LLM 기반 TODO/일정/업체 자동 추출, Obsidian 동기화 + 지식 그래프 기능 포함. [github.com/brood-arch/korean-call-transcriber](https://github.com/brood-arch/korean-call-transcriber)
- **Gemma 4 12B (Google 오픈소스 LLM)**: 16GB Mac Mini에서 구동 가능한 경량 오픈소스 모델. 로컬 LLM 비용 절감을 원하는 개인/소규모 운영 환경에 적합.
- **강남언니 AX(AI 전환) 사례 글**: 조직 내 AI 적용을 4개 페이즈(항구 → 동경 → 가두리 → 표준/문화)로 정리한 실무 사례. AI를 조직 전반에 도입하려는 팀에게 참고할 만한 프레임.

---

## 의미있는 링크

- [Gemma 4 12B on Mac Mini 유튜브 리뷰](https://m.youtube.com/watch?v=PDxKrp-dTDA) — LM Studio + VS Code로 16GB Mac Mini에서 실전 테스트한 14분 영상.
- [Gemma 4 12B 맥미니 한국어 요약 리포트](https://wootom.github.io/news/2026-06-05-gemma4-12b-mac-mini.html) — 위 영상의 성능·메모리·OCR·코딩 검증 내용 정리.
- [2026 AI 에이전트 시장 핵심 트렌드 3가지 슬라이드](https://wootom.github.io/news/slides/20260606-2026-ai-에이전트-시장-핵심-트렌드-3가지-83efdd.html) — 멀티 에이전트 오케스트레이션 주류화 / 에이전트 자율성 확대와 Human-in-the-Loop 재정의 / 에이전트 경제 생태계 형성.
- [강남언니 AX 조직 적용 사례 (요즘IT)](https://yozm.wishket.com/magazine/detail/3781/) — AI 조직 도입 여정을 4단계 페이즈로 서술한 실무 아티클.
- [엔비디아 젠슨 황, 서울 AI 기술센터 건립 뉴스](https://mobile.newsis.com/view/NISX20260605_0003658554) — 한국 AI 인프라 투자 동향.
- [팔란티어 × 구글 클라우드 파트너십 발표](https://kr.investing.com/news/company-news/article-93CH-1972324) — 플랫폼 통합 및 구글 클라우드 마켓플레이스 등록.
- [오픈클로 윈도우 버전 설치 가이드 (MS 공식 지원)](https://youtu.be/B4z8VUUgnEE?si=fZFI6HqKkRBjtzoG) — 윈도우에서 오픈클로 설치 시 참고할 공식 영상.

---

## 결정·약속·일정

- **곡성 AI 워케이션 1기**: 2026-07-03(금) ~ 07-06(월), 전남 곡성 러스틱타운, 18명 소수 정원. 정식 오픈 전 사전 대기 모집 중. [사전대기 페이지](https://gokseong-landing.vercel.app/)

---

## 반응이 컸던 화제

- **Claude 서비스 불안정**: 오전 새벽부터 저녁까지 여러 참가자가 "맛탱이 갔다", "왔다갔다", "529 Overloaded" 등을 반복 언급. 하루 종일 산발적으로 공감 반응이 이어진 것으로 보아 당일 Claude 장애가 채팅방 전반의 공통 불편으로 작용.
- **보급형 Mac Mini 로컬 LLM 구성**: Gemma 4 12B + 맥미니 M4 16GB 조합 소개 후 미니PC 추천·트레이딩 연동 방법 논의로 자연스럽게 이어짐. 비용 부담 없는 로컬 AI 운영에 대한 관심이 집중된 흐름.

---

## 자동화·시스템 적용 후보

- **한국어 통화 전사 파이프라인 패턴**: Whisper(음성→텍스트) → LLM(구조화 추출: TODO·일정·업체) → Obsidian(지식 그래프 동기화) 의 3단 파이프라인. 회의록·고객 통화·인터뷰 자동 정리에 재활용 가능.
- **AI 조직 도입 4-Phase 프레임 (강남언니 사례)**: Phase 0 경영진 인식·인프라 → Phase 1 동경 생성 → Phase 2 가두리(Harness) → Phase 3 표준·문화 정착. 조직의 AX 로드맵 설계 시 참고 템플릿으로 활용 가능.

---

## 질문·미해결

- **크롬 자동조작 불가 문제**: "다른 확장 UI가 페이지 위에 떠있다"는 이유로 자동조작이 막히는 현상 — 재시작 후에도 해결 안 됨. 원인 및 해결책 미확인.
- **Hermes 모델 추천 문의**: Codex 정지 이후 대체 모델로 Hermes를 고려 중인 사용자가 "어떤 버전을 쓰는지" 질문했으나 명확한 답변 없이 대화 종료.
- **미니PC 선택 기준**: 오픈클로 + 시스템트레이딩 + 바이브코딩을 동시에 고려한 미니PC 사양 질문 — 한투 API 관련 부분 답변만 있었고, 최적 기기 추천 결론은 미도출.

---

## 요약 통계

- 메시지 수: 31
- 기간: 2026-06-06 01:11 ~ 2026-06-06 19:58

### URL (10개)

- https://mobile.newsis.com/view/NISX20260605_0003658554
- https://kr.investing.com/news/company-news/article-93CH-1972324
- https://m.youtube.com/watch?v=PDxKrp-dTDA
- https://wootom.github.io/news/2026-06-05-gemma4-12b-mac-mini.html
- https://wootom.github.io/news/slides/20260606-2026-ai-에이전트-시장-핵심-트렌드-3가지-83efdd.html
- https://yozm.wishket.com/magazine/detail/3781/
- https://ai.gpters.org/4fvnr1B
- https://gokseong-landing.vercel.app/
- https://status.claude.com.
- https://youtu.be/B4z8VUUgnEE?si=fZFI6HqKkRBjtzoG

### 해시태그

- #오픈소스: 1
- #음성인식: 1
- #전사: 1
- #파이썬: 1

---

# 🐱 AI 반려봇 덕후방 | GPTers x 뽀짝이 — 2026-06-06

- 메시지 수: 31

**01:11** [P32]
한국어 통화 전사 파이프라인 만들어봤어요. 코딩의 코자도 모르고 바이브코딩으로 만든거라 허접하긴한데... 공유해봅니다.

pip install korean-call-transcriber

🎤 Whisper GPU 가속 전사 + 화자 분리
📋 LLM으로 TODO/일정/업체 자동 추출
📓 Obsidian 동기화 + 지식 그래프
🏭 실제 운영 중인 파이프라인 정리

🔗 github.com/brood-arch/korean-call-transcriber

#파이썬 #오픈소스 #음성인식 #전사

**01:21** [P79]
지금 클로드 맛탱이갔나여?

**01:46** [P35]
맛탱이는 안갓는데
왓다갓다 하네요

**01:58** [P80]
저도 왔다갔다 하네요

**08:13** [P4]
젠슨 황 엔비디아 최고경영자(CEO)가 한국 서울에 인공지능(AI) 기술센터를 건립한다는 뜻을 밝혔습니다.

<https://mobile.newsis.com/view/NISX20260605_0003658554>

**08:25** [P4]
팔란티어는 구글 클라우드와의 플랫폼 통합 및 구글 클라우드 마켓플레이스에 자사 제품을 등록하기 위한 파트너십을 발표했습니다.

<https://kr.investing.com/news/company-news/article-93CH-1972324>

**11:06** [P54]
Gemma 4 12B on a 16GB Mac Mini Is Surprisingly Capable

O 송출채널 : Bart Slodyczka
O 송출일자 : 26년06월04일
O 시청소요시간 : 14분54초

Google just dropped Gemma 4 12B, a free open-source model small enough to run on a 16GB Mac Mini, and in this video I give you an honest breakdown. We use LM Studio to run the model and VS Code to preview the HTML app we build.

<https://m.youtube.com/watch?v=PDxKrp-dTDA>
Gemma 4 12B, 16GB 맥미니에서 의외로 쓸 만하다

구글의 신규 오픈소스 모델을 보급형 M4 Mac Mini(16GB)에서 실전 테스트 — 성능·메모리·OCR·코딩 검증

<https://wootom.github.io/news/2026-06-05-gemma4-12b-mac-mini.html>
구글의 오픈소스 LLM Gemma 4 12B + 맥미니 M4 16G 깡통 도 제법 쓸만하다는 내용입니다

" 보급형 M4 Mac Mini + 로컬LLM + AI에이전트 ( 오픈클로 or 헤르메스 ) " 로 
구축하여 LLM 토큰 지출 비용없이 운영이 가능해 보이네요

상기유튜브 영상을 앱에서 보시면 , 한글자막과 한국어 음성으로 자세히 보실수 있습니다

**11:41** [P81]
🍺맥주모각 안내 

📌 프로그램
- 11:30~12:30 오전 모각
- 12:30~14:00 점심식사
- 14:00~15:30 모션그래픽 실습 (지아코모님)
- 15:30~18:00 자유모각·네트워킹

📌 음료 1잔 쿠폰
- 입구 체크인 때 받아가세요
- 18시 전까지 사용 가능해요
- 무제한 주류·음료(3시간)는 주문 불가
- 알콜·논알콜 모두 주문 가능

📌 점심식사 
- 각자 시간 안에 밖에서 드시고 오면 됩니다
- 비어룸에서도 가능합니다 
- 처음 보는 사람들과 모여서 식사하면 좋아요! 

📌 그 외 안내 
- 1층은 여자화장실 2층은 남자화장실입니다 
- 추가 주문은 QR로 각자 선불결제
- 단체는 테이블별 N빵 자율
- 외부 음식·음료 반입 불가
- 주차 불가, 대중교통 추천

**12:36** [P66]
점심식사 이후에 체크인도 가능한가요?

**12:43** [P82]
네~~!

**12:44** [P66]
감사합니다.

**13:24** [P54]
2026 AI 에이전트 시장 핵심 트렌드 3가지

01트렌드 1. 멀티 에이전트 오케스트레이션 주류화

02트렌드 2. 에이전트 자율성 확대와 'Human-in-the-Loop' 재정의

03트렌드 3. 에이전트 경제(Agentic Economy) 생태계 형성

<https://wootom.github.io/news/slides/20260606-2026-ai-에이전트-시장-핵심-트렌드-3가지-83efdd.html>

**13:29** [P54]
gogo!! AI & AX &  DT & 조직문화

이번 글에서는 성형수술 및 피부 시술 정보 제공 플랫폼 ‘강남언니’가 조직 내 AX를 어떻게 적용해 나가고 있는지, 그 과정과 고민을 소개합니다.

■ 아무도 가보지 않은 바다를 항해하기 위한 AX PHASE 별 주요 질문들

이 글에서는 AI라는 바다를 향해 수많은 구성원들이 배를 띄우고 잘 나아가게 만드는 여정을 정리해 봤습니다. 멋있는 프레임이 아니라, 실제로 우리에게 일어난 일과 일어날 일을 4개 페이즈로 알아보겠습니다.

Phase 0. 항구 - 경영진의 인식과 인프라

Phase 1. “바다로 가보시죠!” - 동경을 만드는 단계

Phase 2. "가는 건 좋은데 조금만 살살.." - 가두리(Harness)의 단계

Phase 3. “우리만의 위대한 항로를 찾아” - 표준을 정하고 문화를 만들기
<https://yozm.wishket.com/magazine/detail/3781/>

**14:00** [P81]
맥주모각 14시 세션 줌 링크 
👉 <https://ai.gpters.org/4fvnr1B>

**14:43** [P79]
다른 확장 UI가 페이지 위에 떠있다면서 크롬 자동조작이 안되는건 어떻게 해야 하나요?ㅠㅠ 종료했다 켜도 안되고 으우..

**16:28** [P82]
사진 4장
지피터스 맥주모각 현장 공유드립니다~🍺 신청 안하셨어도 6시 뒷풀이 때 참여 가능하니 시간 되시는 분들은 강남역 비어룸에서 함께 AI얘기 나눠요🏃‍♀️😂
맥주마시면서 AI공부라니 너무 재밌는 풍경입니다 ㅋㅋㅋㅋ

**16:33** [P83]
오~ 멋있습니다~~
부럽~ 부럽~

**17:11** [P81]
[📣곡성 AI 워케이션 1기 모집]
AI 배우기는 했는데 막상 내 일에 적용하려면 어떻게 시작해야 할지 막막한 분들 많으시죠.

그래서 지피터스가 곡성 AI 워케이션 1기를 준비했어요.

3박 4일 동안 멘토와 함께 내 업무를 하나 꺼내놓고,
실제로 쓸 수 있는 자동화 결과물까지 만들어보는 캠프입니다.

메일 답장 초안, 견적서·보고서 정리, 콘텐츠 초안 생성, 데이터 정리처럼
“이거 매번 하기 귀찮다” 싶었던 일을 AI로 직접 줄여봐요.

내 업무를 잘 아는 상태에서,
AI를 어떻게 적용할 수 있는지 같이 만들어보는 시간이에요.

📅 7/3(금) ~ 7/6(월)
📍 전남 곡성 러스틱타운
👥 18명 소수 정원

아직 정식 오픈 전이라
관심 있는 분들께 먼저 안내드리기 위해 사전대기만 받고 있어요.

관심 있으신 분들은 먼저 살펴보셔도 좋겠습니다 😊

🔗 <https://gokseong-landing.vercel.app/>

**17:26** [P84]
미니PC 뭐가 좋을까요? 오픈클로도하고 시스템트레이딩도 좀하고 바이브코딩도 생각하는데 뭐가 괜찮을까요.맥미니는 트레이딩 이 좀 힝들것 같구요  검색해서 골라봤는데 적당한건지 모르겠어요

**17:31** [P85]
저는 맥미니로 주식투자 돌리는 중이긴 해요

**17:34** [P84]
COM activeX 방식도 맥미니로 될까요? open api 는 가능할것 같은데요

**17:36** [P85]
한투 api로 하고있어요
그런데 굳이 그 방식으로 하는 이유가 있으신가요?
기존에 hts를 쓰시던게 아니라면

**17:41** [P13]
혹시 hermes어떤모델사용하시나요 코덱스 정지먹어서 어필해놓긴했는데 안그래도 클로드코드사용중이어서 다른모델 그냥 api끈어서사용할꺼싶기도해서
한국투자증권 api따서 자연어로도 되더라구요

**17:44** [P84]
최근에는 한투쓰느데 거기도 옛날 방식도 있고 신한꺼도 하던게 좀 있어서요ㅎㅎ

**18:36** [P35]
아따 바쁜데
⏺ API Error: 529 Overloaded. This is a server-side issue, usually temporary — try again in a moment. If it persists, check
  <https://status.claude.com.>
클로드쓰 왜이러는거죠

**18:36** [P86]
무슨일인가요

**18:44** [P35]
클로드 일하다 안하다 그러는군요!?

**18:46** [P87]
ㅠㅠ
클로드야 어서살아나..!
살아났네요

**19:27** [P10]
마이크로소프트에서 지원하는 윈도우 버전 셋업 방법입니다. 
새로 오셨거나 오픈클로 책을 샀는데 윈도우 버전이 없어서 당황하신분들은 꼭 시청하세요 

<https://youtu.be/B4z8VUUgnEE?si=fZFI6HqKkRBjtzoG>

**19:34** [P35]
음음
오늘따라 클로드가 이상하다...

**19:58** [P58]
윈도우에 오픈클로 설치 재도전 하려고했는데 너무 좋네요

