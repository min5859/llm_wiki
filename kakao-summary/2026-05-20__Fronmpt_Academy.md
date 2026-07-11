<!-- kakao-db: ai-summary v1 -->
# AI 요약

다음은 제공하신 카카오톡 일부만 근거로 정리했습니다.

---

## 인사이트·노하우

- **거래 자동화와 인디케이터**: 후행성 지표가 많아서 초·밀리초 단위로 돌린다 해도 자동매매가 쉬워지지 않는다는 주의.(“후행성 지표” 관점의 실무 통찰)
- **노트북 액체 유입·애플 케어**: 참가자 전달 정보로는 “물 들어간 순간 무조건 **교체**하라고” 안내 받았다는 취지. 실제 적용 가능 여부·정책은 매장·보증 조건 따라 다르므로 **후속 검증 필요**. 백업 후 재방문이 언급됨.(원문: “무조건 교체하라고 하두라구요”, “백업하고 다시 가야해여”)

## 추천·자료

- **Qwen 3.7 Max**: 알리바바 계열 최신 라인 업데이트로 보이며([공식 블로그 안내 맥락](https://qwen.ai/blog?id=qwen3.7)), 모델 릴리즈 추적 시 참고할 만한 소스.
- **Agent Cat**(agentcat.app): 링크만 제시되어 **불명**—어떤 제품군인지 대화 내 미설명(HN 토픽과 함께 나열됨).
- **Grok OAuth Proxy**([yelixir-dev/grok-oauth-proxy](https://github.com/yelixir-dev/grok-oauth-proxy)): Grok 사용 시 OAuth/프록시 레이어로 쓰일 수 있는 **오픈소스 레퍼런스**로 보임(직접 사용 경위는 로그 없음).
- **nolanx-ai 스킬 모음**(GitHub `skills`): “AI 영상 제작자를 위한 프롬프트/스킬”과 함께 공유되어, **영상 작업 워크플로에 재사용 가능한 스킬 템플릿 저장소**로 유용할 수 있음—상세 기능은 레포 확인 필요([경로 내 skills](https://github.com/nolanx-ai/nolanx.ai/tree/main/skills)).
- **AI 영상용 20가지 프롬프트**(티스토리 글): 위 스킬과 병행해 **프롬프트 아이디어 수집**용 링크로 제시됨([javaexpert.tistory.com 글](https://javaexpert.tistory.com/1760)).
- **Motion**(motion.so): “Video Agent for Motion Design”이라는 설명만 있어 **모션 디자인·영상 자동화** 축 에이전트/툴로 보임—정확한 범위는 사이트 확인 필요.
- **Grok × OpenClaw**: x.ai 공식 뉴스로 Grok과 OpenClaw 연동 정보를 줄 수 있는 **공식 채널**로 활용 가능.

## 의미있는 링크

- [Qwen 3.7 Max 출시 공지(블로그)](https://qwen.ai/blog?id=qwen3.7)
- [가족 계정 등록 관련 매체/글(bemyb)](https://bemyb.kr/contents/?bmode=view&idx=171376425)
- [가족 계정 4개월 무료 등록 방법 안내(X)](https://x.com/beulsatang35/status/2056733965290229887)
- [Agent Cat](https://agentcat.app/)
- [Agent Cat 등 관련 HN 토픽(hada.io)](https://news.hada.io/topic?id=29649)
- [Grok OAuth Proxy 레포지토리](https://github.com/yelixir-dev/grok-oauth-proxy)
- [영상 프롬프트 스킬 모음 레포(skills 디렉터리)](https://github.com/nolanx-ai/nolanx.ai/tree/main/skills)
- [AI 영상 제작용 프롬프트 글](https://javaexpert.tistory.com/1760)
- [Motion — Video Agent for Motion Design](https://motion.so/)
- [Use Grok in OpenClaw(공식 뉴스)](https://x.ai/news/grok-openclaw)
- [x.ai X 포스트(Grok/OpenClaw 맥락으로 추가 공유된 링크)](https://x.com/xai/status/2056826183745253663)

## 결정·약속·일정

- **토스증권 나오면** “100만원 테스트”처럼 **자동매매 시험을 해보겠다**는 의견만 있고, 확정 일정·시간·참석자 등 **구체 약속은 없음.**

## 반응이 컸던 화제

- **Codex 토큰 리셋**: “또 리셋됐네요?”, “아껴쓰지 말아야겠군요” 등 여러 명이 호응. **예산·크레딧 정책에 대한 불안·허탈감**(“아껴 쓰면 손해보는 느낌”)이 공감대를 만든 것으로 보임.
- **노트북 물 쏟음 사고**: 현실적 패닭·복구 이야기로 짧지만 집중도가 있었던 듯—“절대 물 쏟지 마세요”, Apple Care 방문 등.

## 자동화·시스템 적용 후보

- **영상 워크플로**: 블로그 20종 프롬프트 + nolanx `skills`를 **영상 에이전트용 프롬프트/도구 카탈로그**로 정리해 팀 레포나 Wiki에 싱글 소스화.
- **Grok 이용 자동화**: `grok-oauth-proxy` + OpenClaw 뉴스 링크를 묶어, **허브(Claw)·Grok 호출 패턴 문서화** 후보.(실구성 단계 로그 없음 → PoC 필요)

## 질문·미해결

- **Codex 토큰 리셋**이 사용자 정책인지 버그인지, 주기와 조건 대화에는 **설명 없음**.
- 특정 참가자 **맥북 모델·수리 결과**(“수리가 잘 됐을까?”)에 대한 **후속 결과 미기재**.
- “Agent Cat”의 정확한 역할과 **HN 토픽 `29649`의 핵심 논점** 본문은 로그 밖이라 **불명**.

---

## 요약 통계

- 메시지 수: 20
- 기간: 2026-05-20 00:04 ~ 2026-05-20 23:51

### URL (11개)

- https://qwen.ai/blog?id=qwen3.7
- https://bemyb.kr/contents/?bmode=view&idx=171376425
- https://x.com/beulsatang35/status/2056733965290229887
- https://agentcat.app/
- https://news.hada.io/topic?id=29649
- https://github.com/yelixir-dev/grok-oauth-proxy
- https://javaexpert.tistory.com/1760
- https://github.com/nolanx-ai/nolanx.ai/tree/main/skills
- https://motion.so/
- https://x.ai/news/grok-openclaw
- ... +1 more

### 멘션

- @에르메스단의: 1

---

# Fronmpt Academy — 2026-05-20

- 메시지 수: 20

**00:04** [P4]
@에르메스단의 팝콘춘식이 
아이디 뭔가요 ㅋㅋㅋㅋ

**07:55** [P11]
codex 토큰 또 리셋됐네요?

**08:24** [P2]
와 괜히 억울하네요. 아껴쓰고 있었는데 ㅋㅋ

**09:03** [P24]
아껴쓰지말아야겠군요 ㅋㅋ

**09:06** [P7]
자동매매 100만원 테스트해봐야겟다 토스증권나오면 ㅎ

**09:10** [P33]
대부분 인디케이터가 후행성 지표이기에 자동매매 1초~ 0.01초 단위로 해도 쉽진 않을겁니다.

**14:20** [P1]
Qwen 3.7 Max 출시

<https://qwen.ai/blog?id=qwen3.7>

**15:40** [P4]
코덱스는 진짜 신인가요
토큰 리셋 또해줬네요;;

**15:43** [P2]
티보 형을 믿고 막 써야겠어요. 아껴 쓰면 손해보는 느낌... ㅎㅎ

**15:47** [P4]
사진 2장
절대 물 쏟지 말아요 어려분 ㅠㅠ
여친건데 .. 애플 케어덕을 보러 갑니다 다시 

**15:50** [P30]
이 맥북 언제 모델이죠? 이 맥북에 설치해서 쓰시나요?

**15:51** [P4]
M3 mac pro 입미다 ㅠ
오늘 명동 다녀왓네요 

**16:03** [P19]
물 들어갔는데 수리가 잘 됐을까요?

**16:04** [P4]
백업하고 다시 가야해여 ㅋㅋㅌ
물 들어간 순간 무조건 교체하라고 하두라구요

**17:26** [P1]
<https://bemyb.kr/contents/?bmode=view&idx=171376425>

**17:59** [P1]
가족 계정 4개월 무료 등록 방법

<https://x.com/beulsatang35/status/2056733965290229887>
Agent Cat

<https://agentcat.app/>

<https://news.hada.io/topic?id=29649>
Grok OAuth Proxy

<https://github.com/yelixir-dev/grok-oauth-proxy>

**18:04** [P1]
AI 영상 제작자를 위한 20가지 프롬프트 추천 스킬

<https://javaexpert.tistory.com/1760>

<https://github.com/nolanx-ai/nolanx.ai/tree/main/skills>

**21:55** [P3]
감사합니다!!!

**23:19** [P1]
Video Agent for Motion Design

<https://motion.so/>

**23:51** [P1]
Use Grok in OpenClaw

<https://x.ai/news/grok-openclaw>

<https://x.com/xai/status/2056826183745253663>

