<!-- kakao-db: ai-summary v1 -->
# AI 요약

## 인사이트·노하우

### AI 이미지 생성 품질 향상 전략 (P53 공유)

**핵심 원칙: 스타일과 콘텐츠를 분리한 역공학 방식**

- 레퍼런스를 한번에 왕창 넣으면 "이도 저도 아닌" 결과 → **각 요소를 따로 분리해서 뽑은 후 합성**
  1. 그림체(스타일) 먼저 뽑기
  2. 광원 느낌, 재질 따로 뽑기
  3. 최종 합치기
- 레퍼런스는 최소 3장 이상 제공, 분석 요청 후 프롬프트 생성
- 결과물과 원하는 것을 비교하며 "원하는 부분 / 원하지 않는 부분"을 명시적으로 피드백 → AI가 스스로 조정
- 추상적 단어보다 **세부적·구체적 지시**가 핵심: "내 머릿속에 그려진 결과물을 최대한 자세하게 지시"
- 외곽선 규칙, 색상 팔레트, 광원 방향 등 세부 파라미터까지 지정해야 쓸만한 퀄리티 확보

### Claude Code / AI 디자인 도구 품질 한계

- Claude Code 및 일반 AI의 디자인 결과물은 "천편일률적" — 프롬프트 방식을 바꿔야 차별화 가능
- **울트라코드 + OMC 모드**로 시안 5개 요청 시 퀄리티 차이가 큼 (P51 경험)
- 디자인 전문 AI 툴(예: 오픈디자인, 캔바 AI) 병행 활용 검토 가치 있음

### AI 도입 시 조직 차원의 병목 문제

- 개인 생산성은 증가하지만, **어중간한 결과물이 대량 생산되어 의사결정 병목**이 생기는 역효과 존재 (P55)
- AI 설계(기획)를 잘못하면 "안 쓰는 게 더 나을 정도"의 결과 → **기획 역량이 AI 활용의 핵심 변수**

---

## 추천·자료

- **오픈디자인**: 디자인 역량이 없는 사용자도 "이게 웹이구나" 수준의 결과물을 낼 수 있는 AI 디자인 툴 — 디자인 0인 사람의 웹 프로젝트 시작점으로 적합
- **캔바 AI**: 기업에서 많이 도입 중인 AI 기반 디자인 도구 — 빠른 마케팅/콘텐츠 제작에 유용

---

## 의미있는 링크

- [Claude Code 창시자가 프롬프팅을 멈추고 루프와 하네스로 150개 PR을 처리하는 방법](https://pub.towardsai.net/claude-codes-creator-stopped-prompting-claude-he-writes-loops-and-merges-150-prs-from-his-phone-b096ba603c13) — 미래 개발자의 역할이 "바이브코딩"을 넘어 AI 하네스 관리자로 전환된다는 논의의 발단

---

## 반응이 컸던 화제

### 1. AI로 인한 직군 경계 해체
- 개발자↔디자이너, PM↔디자이너 경계가 무너지고 있다는 공감대 → "니꺼내꺼 없는 흐름"
- P53의 실제 사례(PM이 디자이너 없이 직접 결과물 제작, 디자이너 반나절 작업을 10분으로 단축)가 여러 참가자의 공감과 후속 질문 유발
- "상대방에게 직접 말하면 결투 신청" — 예민한 현실 인식이 공감 유발

### 2. AI 이미지 프롬프트 기법 공유 (장문 토론)
- P53이 구체적인 이미지 프롬프트 템플릿(`[PROJECT INPUT BLOCK]`, `[STYLE RECONSTRUCTION BLOCK]` 등)을 공유하며 P56과 20분 이상 심층 대화
- 서로 다른 접근법 비교(레퍼런스 일괄 분석 vs. 요소 분리 역공학)가 구체적 노하우로 이어짐

---

## 자동화·시스템 적용 후보

### AI 이미지 생성용 구조화 프롬프트 템플릿 (P53 제공)

스타일 일관성을 유지하는 이미지 생성을 위한 블록 구조:

```
[PROJECT INPUT BLOCK]
Subject: main subject / environment / key objects
Purpose: goal / target audience
Message Control: primary message / emphasized text / must-include / immutable elements
Visual Priority: focal point / secondary elements

[STYLE RECONSTRUCTION BLOCK]
Shape Language: base form / proportion rules / silhouette
Line Quality: thickness / variation / continuity / outline emphasis
Color System: palette / saturation / contrast / shadow color
Lighting Model: light source / direction / shadow hardness / shading method
Texture: surface quality / brush style / detail density
Composition: framing / perspective / layout / negative space
Detail Logic: pattern repetition / decoration style / complexity distribution
Negative Constraints: forbidden elements

[SIGNATURE SYSTEM]
→ Proportion / Line / Color / Detail 각각 고정값 명시

[CONSISTENCY ENFORCER]
→ 동일 아티스트 스타일 강제, 스타일 드리프트·랜덤성 거부

[FINAL GENERATION INSTRUCTION]
→ 위 세 블록을 종합하여 생성
```

**활용 팁**: 그림/사진/분야에 따라 블록 조합 변경. 먼저 간단한 프롬프트로 "간보기" 후 결과 검토, 이후 본격 세부 지시.

### 레퍼런스 기반 디자인 시스템 구축 워크플로 (P56 사례)
1. 기존 디자이너 작업물 전체를 레퍼런스로 수집
2. 디자인 요구 상황 분석 → MD 파일로 매핑
3. 동일 구조를 프롬프트 제어에 재사용

---

## 질문·미해결

- **`-p` 옵션 제거 이슈**: Claude Code 또는 관련 CLI 도구에서 `-p` 옵션이 제거되어 불만이 나왔으나, 구체적 맥락·대안·공식 해결책은 대화에서 논의되지 않음 — 후속 확인 필요
- AI 이미지 생성 만족도 한계: P56의 "프롬프트 내용은 정확히 지켰는데 뭔가 부족한" 현상 — 스타일·콘텐츠 분리 역공학 방식 외에 추가적인 해결책 미확정

---

## 요약 통계

- 메시지 수: 65
- 기간: 2026-06-07 14:12 ~ 2026-06-07 23:20

### URL (1개)

- https://pub.towardsai.net/claude-codes-creator-stopped-prompting-claude-he-writes-loops-and-merges-150-prs-from-his-phone-b096ba603c13

---

# 🐱 AI 반려봇 덕후방 | GPTers x 뽀짝이 — 2026-06-07

- 메시지 수: 65

**14:12** [P5]
미래 개발자의 역할은 바이브코딩조차 넘어서 AI 스스로 일을 잘 할 수 있도록 하네스만 잘 잡아주는 것이겠네요.

<https://pub.towardsai.net/claude-codes-creator-stopped-prompting-claude-he-writes-loops-and-merges-150-prs-from-his-phone-b096ba603c13>

**15:15** [P6]
 -p 없애거라며 나쁜놈들 ㅋㅋ

**15:36** [P40]
-p 없애는거 너무속상해여..

**16:21** [P46]
사실상 하네스 관리자... 세부 코딩은 정말 할일이 없어질듯요.

**16:21** [P47]
요즘 디자이너는 다시 채용시작한곳이 좀 보이던데
개발 영역은 그런 소식을 못들었네요..

**16:26** [P48]
디자인은 코덱스 클로드 것들이 참 구린거같아요,, 나름 세련되게 한다고 노력한 티는 나는데.. 천편일률적인 결과물

**16:28** [P47]
하지만 그리 멀지않은것 같아요 디자인 계열도..

**16:28** [P49]
쿨로드 디자인도 구려요?

**16:28** [P47]
저는 그렇더라구요
다 제 부족함 때문이라고 생각하고 있지만서도.. ㅋㅋ

**16:30** [P48]
저도 클로드 구려요. 저도 제 부족함 이라고 생각했는데 혹시나 클로드로 디자인 예쁘게 잘 된다. 하시는 분 프럼 포트 좀 조언 부탁드릴게요.
프롬프트

**16:32** [P49]
클코로 만들면 다 디자인이 비슷하게 나와서 클코말고 디자인쓰먄 다르지않을까 생각만 하고있었습니다

**16:32** [P48]
디자인 전문 ai 툴 말씀하시는걸까요.. ??

**16:33** [P49]
그 클로드 웹에만 있는 디자인 말씀입니다

**16:33** [P50]
오픈디자인 좋은거 같아여…어차피 저는 디자인 능력이 0이라 오픈디자인으로 만들면
그래도 보기에는 아 이게 웹이구나 싶을정도는 되는거 같아서...

**16:35** [P47]
요즘 회사들 캔바 ai 많이 쓰는것 같더라구여

**16:44** [P48]
아 맞다!! 이거 안써봤는데 도전해 봐야겠어요 ㅎㅎ

**16:44** [P51]
저도 그래서 개인 프로젝트를 만들고 나서 맘에안들어서 클코에서 울트라코드 켜고 OMC로 디자인을 좀 완성도 있고 이쁘게 시안 다섯개 만들어봐 했더니 퀄차이가 엄청 나긴했습니다. 

**16:44** [P48]
오옹 오픈디자인..메모!
메모…222!!
오머나 그렇게 하면 잘하는군요!
헛 그렇군요?! 똑같은걸 똑같은 방식으로 시켰는데 그랬던 거죠?
아하…!! 너무나 신기하군요. 그들에게 디자인에 대한 좋다 나쁘다의 감각이 우리랑 달라서 그런가봐요

**16:50** [P48]
오 2번과 5번을 섞은 선택 너무 좋은거같아요
정말 근데 꽤괜이네요 디쟌이
알려주셔서 감사합니다!

**18:41** [P52]
맥미니 중고로 구매하신 분 계실까요? ^^

42만원 m1 8g 256이면 가격 적당한지 궁금해요 ^^

**18:44** [P33]
그정도 됩니다
예전엔 더 저렴했는데 중고가 올랐더라고요

**18:47** [P52]
오~ 그렇군요 답변 감사해요
당근으로 얼른 업어와야겠네요 ^^

**19:26** [P53]
저흰 좀 다를 수 있는데 디자이너가 필요 없어졌어요 기획자가 원하는 결과물이 뭔지 정확히만 알먄 비슷한 사진이나 이미지, 디자인 넣고 분석해달라 그래요 그래서 원하는 대로 프롬프트 짜면 바로 나와서 PM이 디자이너 잡고 씨름하는 것보다 직접 하는게 빨라요 예전에 설명하는데 들인 시간보다 비교도 안되게 삘라짐

다만 제작자가 디자인에 대한 지식이 있어야 해서 다들 다른 분야 공부 하고 있어요;;

**19:33** [P48]
헛
그렇군요 시상에… 

**19:36** [P23]
개발자는 디자이너가 필요 없어졌고
디자이너는 개발자가 필요 없어졌고…
디자이너 개발자가 아니라 다른 포지션을 넣어도 마찬가지지요.

흔히 얘기하는 니꺼내꺼 없는 그런 흐름으로 가고 있는거 같습니다.
다만 그걸 상대 앞에서 직접 얘기하는건 결투신청으로 받아들여지기도 하니 조심해야 합니다 ㅎㅎ

**19:41** [P53]
요새 유튜브 등에도 많이 돌아다니는 1인 사업체제도 얼마든지 가능하겠다는 생각이 들어요 같은 일을 해도 쓰는 사람의 역량에 따라서 천차 만별이고 감각?만 있으면 짧은 시간에 정말 괜찮은 결과물이 엄청 나와서...

다만 사용되는 곳등의 특성에 따라 갈리겠지만 정말 엄청나게 좋은 퀄리티의 결과물을 원하는게 아니라면 소위 말해 각 분야 찍먹 정도 해본 지식정도만 있어도 진행이 되더라구요

**19:41** [P48]
결투 ㅋㅋㅋㅋ 맞죠.. 저도 오늘 모각할 하러 만난 어떤분이 이제 백엔드 개발자는 티오가 줄겠어요. 프론트는 몰라도 라고 하셔서 순간 급 흥분했었네요 ㅋㅋ 

**19:41** [P53]
ㅎㅎㅎㅎ
상대방에게 직접적으로 그렇게 얘기하면 결투하자는거죠 근데 디자이너가 먼저 그렇게 느낌...ㅜㅜ
살아 남는 디자이너는 소위 말하는 곤조가 없고 상대방이 원하는걸 미리 다 제시해주는 사람이겠더라구요

**19:43** [P48]
오오.. 저도 지금보다 200배는 둥글어지도록 해야겠어요

**19:43** [P53]
기획도 그렇고 정말 체감하는 쪽은 정부입찰요..

**19:43** [P48]
협업 소프트스킬과

**19:43** [P53]
예전에는 많아야 3개정도 밖에 못들어올 정도의 분야라면...

**19:43** [P48]
서비스 정신을 장착.. !

**19:43** [P53]
이젠 10개 넘어요

**19:43** [P48]
그렇군요 ㅠ

**19:43** [P53]
제안서 작성도 엄청 손쉬워진거죠 올해초부터 그래요
제가 아는 프리랜서 디자이너 분중에 AI로 엄청 바빠지신 분이 계신데 앉은 자리에서 바로 보여주세요 느낌만 보라고 바로바로 저해상도로 이미지 뽑아서 보여주세요
그리고 예전보다 빠른 시간안에 샘플을 100장도 넘게 보내주심...이중에 니가 원하는거 있다..이런식으로.;;
회의하면서 바로 바로 진행이 되고 하니 다른 분들도 엄청 찾으시나봐요

**19:48** [P7]
저는 저러면
결국 모두가 줄고 인간도 줄겠네요
라고 말못하게...ㅋㅋㅋ

**19:49** [P53]
다들 그러시겠지만 인원은 동일한데 일만 폭발적으로 늘어나네요;;
사람을 최대한 안 뽑는 선에서 어디까지 업무량을 늘릴 수 있나...로 가는듯?ㅜㅜ

**19:51** [P7]
AI시대에 일이 절대 줄어들수 없다더라구요
그만큼 더할거라고 ㅋㅋㅋ
할게 없으면 노는게 일이 되기도 할거라더라구요

**20:03** [P54]
AI를 쓰는 개인, 기업의 생산성이 그렇지 않은 기업이란 비교가 안되겠네요. 그런데 또 조금만 지나면 AI를 쓰는 개인끼리, 기업끼리의 경쟁이 되면서 생산성은 늘지만 눈높이는 훨씬 높아지는..

**20:11** [P29]
ai가 좋지만 저희는 아직 현장 생산까지는 연결이 안되네요…
현장 데이터 수집 개발에 활용밖에 아직 없습니다
빨리 로봇이 풀려야…

**20:17** [P55]
아직까진 AI를 쓰는게 개인의 생산성이 엄청 높아보이는거 같지만. 회사차원에서는 실제로는 그렇지 않다는말도 있습니다.
AI로 완성도 있는 결과물이 아니라 어중간한 결과물만 많이 생산해서 결국 의사결정의 병목만 만드는 경우가 많은 것 같습니다.

**20:21** [P33]
AI 설계를 어떻게 하느냐에 따라 결과물이 천차만별이라.. 이상하게 만들면 안쓰는게 더 나을정도죠. 그래서 기획이 중요

**20:25** [P53]
그렇죠 해당 분야에 대해 어느정도 알아야 내가 내야할 결과물을 정확히 알고 그에 맞게 기획을 해서 만들 수 있으니까요
저희쪽에서 만드는 이미지의 퀄리티가 그리 높은편은 아니었지만 그래도 출력도 하고 해야해서 일러스트는 기본적으로 쓸줄 알아야 하고 구도나 색도 어느정도 알아야 하는데 그런거 다 필요없어졌어요..예전에 디자이너가 반나절 걸리던거 이젠 10분이면 다 끝남..

**20:49** [P56]
원하는 대로 잘 나오시나요? 전 이미지는 아직도 분투 중이라..
그냥저냥 괜찮아서 됐다 하고 넘긴 건 거의 대부분인데
와 이거 제대로 말한대로 됐다 싶었던 건 진짜 딱 하나 있어요
그렇게 이미지 갈다가 모르겠다 이쯤 만족하자 하고 넘긴 게 그 한번 빼고 대부분이라 이런 쪽 디자이너분들이 가장 대체하기 어렵다고 생각하고 있어서… 팁이 있다면 좀 얻어가고 싶네요

**20:55** [P52]
저도 레퍼런스 보여주면서 디자인 시스템 프롬프트 작성해 달라고 하면 잘 정리해줘서 편하게 활용하고 있어요~ ^^

**20:57** [P53]
도움이 될런지 모르겠는데 저도 위에 송아영님 처럼 일단 AI를 갈궈요

**20:57** [P56]
음 그렇게 레퍼런스 보여주고 뽑아도 꽤 만족할 만한 걸 찾기가 힘들더라구요 한 80%정도 만족한다는 느낌이 늘 들어서

**20:58** [P53]
제가 원하는 느낌의 레퍼런스부터 찾아요 그리고 최소 3장이상을 주고 제가 원하는 결과물에 대한 내용과 함께 해당 이미지를 분석해달라고 해요

**20:58** [P56]
ㅋㅋㅋ 계속 갈고 갈고 가는데 그래도 썩… 뭔가 다른 접근 방향들이 있으실까 해서 여쭤봤습니다

**20:58** [P53]
제가 만들어야할 결과물에 대한 내용을 주고 그걸 프롬프트로 만들라고 그래요

**21:01** [P56]
디자이너가 하는 만큼의 만족도가 나오시나요? 저는 계속 아쉬움이 커서 ㅜㅜ 궁금했습니다

**21:02** [P53]
얼마나 세부적으로 지시하는가에 따라 결과가 달라지더라구요

**21:02** [P56]
저는 일단 프로젝트 쳐낼 만큼은 된다 같은 정도의 만족도 같아요

**21:03** [P53]
그래서 위에 PM들이 다른 분야 공부도 한다는게...딱 제가 아는 만큼 일을 시킬수 있다는 느낌정도?
그게 아니면 AI한테 끌려가는거고 제가 원하는 결과물이 제 머릿속에 그려지면 최대한 자세하게 지시를 하는거죠 추상적인 단어가 가아니라..

**21:04** [P56]
어 제가 궁금한 건 그러니까 그 자세한 지시에 대한 만족도긴 해요
그 아웃풋이 디자이너만큼인가요?

**21:04** [P53]
넵
디자이너와 씨름 안해도 된다는게 일단 너무 좋습니다
결과의 퀄리티가 디자이너 이상입니다 제 기준으론..
만들다보면 필요한 내용은 대동소이하게 정해지는지라 아마 서로 다른 디자인에 대한 얘기를 하는 것같아 제가 뭐라고 말씀드리기 조심스럽긴하지만 제가 하는 작업에 대해선디자이너 보다 낫습니다
그리고 기존에 작업했던 결과물들이 있으니 비슷한 작업을 할땐 더더욱 좋네요

**21:07** [P56]
ㅠㅠ 그렇군요 저는 사실 지금 디자이너분이 안 계신 상태가 돼서 정리해 가면서 쳐내고 있는 중인데 예전만큼의 아웃풋이나 제가 구상화했던 이미지를 옮겨두는 작업에 있어서 아쉬움이 아직 커서 한번 여쭤봤습니다
ㅋㅋㅋ 어느 정도는 만족이 되는데 다시 디자이너분을 뵙고 싶네요

**21:07** [P53]
그 디자이너분 역량이 출중하셔서 그럴수도 있겠네요 ㅎㅎ
일단 가장 간단한거긴 한데...
우선 이미지를 제 생각대로 보고 싶을떄 이정도로 간단한 프롬프트로 시작하거든요 그리고 결과물이랑 ai의 제안을 같이 검토해서 본격적으로 시작하는 편이라
[PROJECT INPUT BLOCK]

Subject:
- main subject: {{SUBJECT}}
- environment/background: {{BACKGROUND}}
- key objects: {{KEY_OBJECTS}}

Purpose:
- goal of image: {{PURPOSE}}
- target audience: {{AUDIENCE}}

Message Control:
- primary message: {{MAIN_MESSAGE}}
- emphasized text (if any): {{EMPHASIS_TEXT}}
- must-include elements: {{MANDATORY_ELEMENTS}}
- immutable elements (must not change): {{IMMUTABLE_ELEMENTS}}

Visual Priority:
- focal point: {{FOCAL_POINT}}
- secondary elements: {{SECONDARY_ELEMENTS}}

---

[STYLE RECONSTRUCTION BLOCK]

Shape Language:
- base form: {{SHAPE_BASE}}
- proportion rules:
  - head-to-body ratio: {{RATIO}}
  - exaggeration: {{EXAGGERATION_RULE}}
- silhouette characteristics: {{SILHOUETTE}}

Line Quality:
- thickness: {{LINE_THICKNESS}}
- variation: {{LINE_VARIATION}}
- continuity: {{LINE_CONTINUITY}}
- outline emphasis: {{OUTLINE_RULE}}

Color System:
- palette: {{COLOR_PALETTE}}
- saturation: {{SATURATION}}
- brightness contrast: {{CONTRAST}}
- shadow color rule: {{SHADOW_COLOR}}

Lighting Model:
- light source: {{LIGHT_SOURCE}}
- direction: {{LIGHT_DIRECTION}}
- shadow hardness: {{SHADOW_TYPE}}
- shading method: {{SHADING_STYLE}}

Texture:
- surface quality: {{TEXTURE_TYPE}}
- brush style: {{BRUSH_TYPE}}
- detail density: {{DETAIL_DENSITY}}

Composition:
- camera framing: {{FRAMING}}
- perspective: {{PERSPECTIVE}}
- layout: {{LAYOUT}}
- negative space: {{NEGATIVE_SPACE}}

Detail Logic:
- pattern repetition: {{PATTERN_RULE}}
- decoration style: {{DECORATION_STYLE}}
- complexity distribution: {{COMPLEXITY}}

Negative Constraints:
- forbidden elements: {{FORBIDDEN_ELEMENTS}}

---

[SIGNATURE SYSTEM - CRITICAL]

Apply strict artist signature consistency:

1. Proportion Signature:
- fixed ratio system: {{RATIO}}
- exaggeration locked: {{EXAGGERATION_RULE}}

2. Line Signature:
- outer contour rule: {{OUTLINE_RULE}}
- pressure behavior: {{LINE_VARIATION}}

3. Color Signature:
- palette locked: {{COLOR_PALETTE}}
- shadow fixed color: {{SHADOW_COLOR}}

4. Detail Signature:
- repeating pattern: {{PATTERN_RULE}}
- density focus: {{COMPLEXITY}}

---

[CONSISTENCY ENFORCER]

style consistency priority: maximum
treat output as from a single artist

strictly enforce:
- identical line behavior across entire image
- restricted and consistent color palette
- uniform shading technique
- preserved shape exaggeration rules
- no stylistic mixing

reject:
- randomness
- style drift
- unintended realism
- additional unrequested details

---

[FINAL GENERATION INSTRUCTION]

Generate an image that strictly follows:
- project input block (content intent)
- style reconstruction block (visual structure)
- signature system (artist identity)

Output must:
- appear as created by the same artist
- maintain full internal consistency
- reflect the intended message clearly
그림이냐, 사진이냐, 또 어떤 분야이냐에 따라 쓰는게 다르지만 일단 이정도로 간단하게 분석하고 나서 거기에 맞춰서 진행하는 편입니다
여기에 워낙 고수분들이 많으시니 제가 이런걸 올리기도 민망스럽지만 저 프롬프트에서도 외곽선 규칙을 넣을것인가 말것인가 이런 자세한것들을 다 지정해서 해야 쓸만한 퀄리티가 나오고 이전에 대화식으로만 진행했을땐 저도 뭔가 많이 아쉬웠었어요

**21:15** [P56]
프로세스 공유 감사합니다 ㅎㅎ 저 같은 경우에는 기존 디자이너분 작업 레퍼 전부 넣어 두고 / 디자인 요구 상황 분석한 걸 따라 md로도 매핑을 따로 빼 뒀어요.

그리고 그 요구 상황 분석 구조를 똑같이 따서 프롬프트 제어해서 처리하는 것 같습니다만은 아 이게 맞긴 한데 아닌데 이런 게 계속 나오더라구요
프롬프트 내용은 잘 지켰고 정확해요 근데 뭔가 부족한

**21:16** [P53]
스타일과 콘텐츠를 분리하는 등 하나의 결과물로 바로 뽑지 않고 역공학을 통해 다 분리한다음 필요한 부분을 섞어서 최종적으로 쓰시는걸 추천드려요
해당 작업 레퍼 전부를 한번에 분석하고 그대로 만들어달라고 하면 결과가 좋지않은 경우가 많았어요

**21:17** [P20]
오... 디자인 뽑기가 진짜 어렵던데
많이 배웁니다

**21:17** [P53]
내가 필요하고 중점적으로 해야할 부분을 다 따로 분석해서 뽑아낸 다음 합쳐서 만드는 방식이 이때까진 제일 낫더라구요
그림체를 먼저 뽑고 그다음 내가 원하는 광원의 느낌이나 재질을 따로 뽑고...
한번에 왕창 뽑으면 이도 저도 아닌게 계속 나오더라구요
저건 그림체 뽑을 때 우선적으로 하는 것 중 일부라서;;ㅎㅎ
제대로 원하는 그림체가 뽑히는지 간보는 정도? 라고 보시면 될 것 같네요
그리고 안되면 계속 전 물어봐요 제가 준 레퍼랑 만들어준거랑 비교해서 내가 원하는 부분, 원하지 않았는데 나오는 부분 이렇게 해서 원하는 부분만 나오고 원하지 않는 부분 안나오게...그럼 지가 알아서 잘해줌;; 그래서 요즘 제 일은 ai 갈구기가 대부분인듯;

**22:47** [P56]
ㅋㅋㅋㅋㅋ 저희 동네 에이전트한테도 분노하기가 일상이 되어버린 것 같아요
공유 감사합니다 다른분들 어떻게 사용하시는지 얘기 들을 때마다 좋네요 ㅎㅎ

**23:20** [P2]
아직 안보이는데요 ㅠ

