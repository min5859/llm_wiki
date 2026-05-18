---
title: "python-pptx + 디자인 토큰 PPTX 자동 생성 패턴"
domain: personal
sensitivity: public
tags: ["analysis", "pptx", "python-pptx", "ooxml", "design-tokens", "korean-font-fallback", "chart-aspect-ratio", "ppt-quality-metrics"]
created: 2026-05-17
updated: 2026-05-18
source_session: 20260516-212048-3947-json,-yaml,-markdown-형태의-컨텐츠-디스크립션과-수치등이-들어있는-자료를.md
sources:
  - "session-logs/20260516-212048-3947-json,-yaml,-markdown-형태의-컨텐츠-디스크립션과-수치등이-들어있는-자료를.md"
  - "session-logs/20260518-232803-4c55-지금-프로그램으로-자동으로-생성된-out-samsung_full_apple.pptx,-ou.md"
related:
  - "wiki/projects/auto-pipe-ppt.md"
  - "wiki/analyses/multi-llm-provider-adapter-pattern.md"
---

## 개요

JSON/YAML 같은 구조화 입력 + 디자인 토큰 (`DESIGN.md`) 으로부터 **편집 가능한 멀티슬라이드 PPTX** 를 자동 생성하는 범용 패턴 정리. Claude Web/Desktop 의 PPT 생성이 사실은 `python-pptx + 절대좌표 도형 + OOXML 직접 조작` 이라는 점에서 출발해, 그것을 재사용 가능한 컴포넌트 라이브러리로 응집하는 방법.

본 문서는 [[auto-pipe-ppt]] 프로젝트에서 추출한 일반 지식이다.

## PPTX 자동 생성 라이브러리 비교

| 도구 | 편집 가능 PPT | 차트 자유도 | 디자인 자유도 | 비용 | 추천 시나리오 |
|------|-------------|------------|-------------|------|-------------|
| **python-pptx** | ◎ | △ (콤보·이중축 어려움) | ◯ | 무료 | 일반적 1순위. 차트는 기본 종류만 |
| **python-pptx + matplotlib 이미지** | ◎ 텍스트만, 차트는 PNG | ◎ | ◎ | 무료 | 차트가 복잡한데 PPT 편집 가능성도 필요할 때 |
| **python-pptx + OOXML 직접 작성** | ◎ | ◎ | ◎ | 무료 | 콤보·레이더 차트가 필요하고 운영 가능 (XML 검증 부담) |
| **Aspose.Slides for Python** | ◎ | ◎ | ◎ | 유료 (비쌈) | 라이선스 비용을 수용 가능한 엔터프라이즈 |
| **HTML+CSS → Playwright → 이미지 PPT 매립** | ✕ (이미지 한 장만) | ◎ | ◎ (CSS 그대로) | 무료 | 편집 가능 PPT 필요 없을 때만. 사실상 PDF 와 동급 |
| **LibreOffice UNO** | ◎ | ◯ | △ | 무료 | 운영 부담 (~700MB), CI 헤드리스만 권장 |

**Claude Web/Desktop 의 PPT 도 사실은 python-pptx 다.** Claude 가 생성한 PPTX 를 unzip 하면:

- `placeholder` 미사용. `<p:sp>` 30~40개를 절대좌표 (EMU) 로 직접 배치
- 임베디드 차트는 `<p:graphicFrame>` + `chart1.xml` + 작은 Excel 워크시트
- 폰트는 1~2 종 (Inter Display / Inter 등) 만 사용

따라서 비결은 라이브러리가 아니라 **"절대좌표 도형으로 직접 디자인"** + **재사용 가능한 컴포넌트 라이브러리** 다. Claude 는 매번 LLM 이 좌표를 즉석에서 짜고, 자동화 파이프라인은 미리 만든 컴포넌트에 입력만 갈아끼운다.

## 디자인 토큰의 이중 입력 어댑터

`DESIGN.md` 양식이 여러 가지일 수 있다 — 한 쪽은 YAML frontmatter, 다른 쪽은 마크다운 본문 + CSS `:root` 변수. **마크다운 표 파싱은 깨지기 쉽다** — 대신 두 양식의 파서를 각각 두고, 같은 `DesignTokens` 트리로 통합한다.

```python
# 통합 후 트리 (예시)
DesignTokens(
    colors={'primary': '#0066cc', 'ink': '#1D1D1F', ...},
    typography={'hero-display': FontSpec(face='SF Pro Display', size_pt=56, weight=700, ...)},
    spacing={'gutter': '24px', ...},
    rounded={'card': '12px', ...},
    components={'button-primary': {'bg': '{colors.primary}', ...}},
)
```

토큰 본문의 `{section.name}` 참조 표현은 3회 반복으로 해석 (순환 회피). `chart_palette()` 는 primary 기반 hue rotation 5색을 자동 생성 (primary 가 없으면 무채색 fallback).

## 컴포넌트는 role 만 참조 (resolver 패턴)

컴포넌트 (`KpiRow`, `Insight`, `Verdict` 등) 가 디자인별 토큰 이름 (Apple 의 `ink` vs Minimalissimo 의 `inkwell` 등) 을 직접 알면 디자인 추가 시마다 컴포넌트를 다 고쳐야 한다. **resolver.py** 가 role → 디자인별 token name 의 매핑을 단일 지점에 두면 컴포넌트는 role 만 안다:

```python
# resolver.py
def color_for(role, tokens):
    # role: 'ink' | 'muted' | 'primary' | 'canvas' | 'hero' | 'body' | ...
    aliases = {
        'ink': ['ink', 'inkwell', 'foreground'],
        'muted': ['muted', 'body-muted', 'storm-gray'],
        ...
    }
    for name in aliases[role]:
        if c := tokens.colors.get(name):
            return c
    return DEFAULT[role]

def tone_color(tone, tokens):
    # tone: 'positive' | 'negative' | 'warning' (delta 표시 등)
    return tokens.colors.get(tone) or {'positive': '#0a8a3a', 'negative': '#c43b3b', ...}[tone]
```

`font_role` 도 동일 (`kpi-value`, `kpi-label`, `body-sm`, `grade`).

## 한글 폰트 fallback: `<a:latin>` 만으론 부족

`python-pptx` 의 기본 run 은 OOXML 의 `<a:latin>` typeface 만 설정한다. 결과: 한글 문자가 macOS 시스템 명조체로 떨어져 산세리프 의도와 정반대로 렌더링.

**원인**: PowerPoint 는 글자별로 latin / east-asian / complex-script 를 분기해 폰트를 골라 적용한다. `<a:latin>` 만 있으면 한글은 시스템 EA 기본 (명조체) 으로 fallback.

**해결**: 모든 run 에 `<a:ea>` (East Asian) 와 `<a:cs>` (Complex Script) 까지 추가 박는다.

```python
# python-pptx run 의 rPr 에 ea/cs 추가
from pptx.oxml.ns import qn
from copy import deepcopy

def _set_typeface(run, latin_face: str, ea_face: str = 'Apple SD Gothic Neo'):
    rPr = run._r.get_or_add_rPr()
    for child_qn in (qn('a:latin'), qn('a:ea'), qn('a:cs')):
        existing = rPr.find(child_qn)
        if existing is not None:
            rPr.remove(existing)
    for tag, face in [('a:latin', latin_face), ('a:ea', ea_face), ('a:cs', ea_face)]:
        e = rPr.makeelement(qn(tag), {'typeface': face})
        rPr.append(e)
```

이러면 PowerPoint 가 한글에는 ea typeface 를 자동 적용, Roman 문자엔 latin 을 사용한다. macOS 표준은 `Apple SD Gothic Neo`, Windows 는 `Malgun Gothic`, 크로스플랫폼 폴백은 `Pretendard` (오픈소스).

**회귀 테스트**: 슬라이드 XML 을 unzip 해 `<a:ea typeface="Apple SD Gothic Neo"/>` 가 모든 run 에 들어 있는지 검증.

## 절대좌표 캔버스의 단위 함정 (EMU)

PowerPoint OOXML 은 EMU (English Metric Unit) 좌표:
- 1 inch = 914,400 EMU
- 1 cm = 360,000 EMU
- 1 pt = 12,700 EMU
- 1 px @96dpi = 9,525 EMU

디자인 토큰은 보통 px / pt 로 적혀 있으므로 변환 헬퍼 `emu.py` 가 필수:

```python
def px_to_emu(v): return int(v * 9525)
def pt_to_emu(v): return int(v * 12700)

def parse_size(value: str | int | float) -> int:
    # "56px", "-0.28px", 1.07, "11px 22px" 모두 처리
    ...
```

1280×720 (16:9) 캔버스는 EMU 로 12,192,000 × 6,858,000.

## 시각 검증: macOS qlmanage 의 한계

PPTX 생성 후 자동 시각 검증:

```bash
qlmanage -t -s 1600 -o $OUT_DIR out.pptx
```

→ `out.pptx.png` (첫 슬라이드 썸네일만) 반환. 즉 첫 페이지만 자동 회귀 검증할 수 있다.

전체 페이지 자동 검증이 필요하면:
- **LibreOffice 헤드리스** (`soffice --headless --convert-to pdf out.pptx`) — ~700MB 설치, CI 표준
- **PowerPoint Automation** (Windows 만, COM 기반) — 비추
- **Keynote applescript** — macOS GUI 의존, CI 부적합

CI 에 LibreOffice 깔리는 게 사실상 필수. 로컬 개발은 PowerPoint/Keynote 직접 열기 + qlmanage 첫 슬라이드 자동 회귀의 조합.

## KPI / 카드 컴포넌트의 텍스트 wrap 함정

긴 한글 숫자 (예: "3,336,059억") 가 1줄 폭을 초과하면 자동 wrap 되는데, 카드 높이가 1줄 가정으로 설정되어 있으면 잘려 보임 ("3" 만 보이는 식). **해결 패턴**:

1. KPI value 박스 높이를 **모든 카드에 걸쳐 사전 측정** 해 가장 긴 값에 맞춰 통일
2. 2줄까지 허용하고 그 이상은 줄임표 표시 (또는 글자 크기 감소)
3. 데모 / 테스트에서 가장 긴 케이스 (천만 단위 + 단위 한글) 을 반드시 포함

디자인 토큰 시스템은 색·폰트뿐 아니라 **좌표·치수도 데이터로 다뤄야** 일관성이 깨지지 않는다.

## PPTX 품질 객관 지표 (unzip 기반)

생성 PPTX 의 품질을 LLM·디자이너의 주관 평가 없이 객관 지표로 비교하려면 **PPTX 를 zip 으로 풀어 슬라이드 XML 의 도형·텍스트·차트 메타를 직접 카운트** 한다. 외부 도구 없이 stdlib 만으로 가능 (`zipfile` + `xml.etree.ElementTree`).

핵심 지표:

| 지표 | 추출 방법 | 해석 |
|---|---|---|
| 슬라이드 수 | `ppt/slides/slide*.xml` 파일 수 | 정보량 |
| 슬라이드당 도형 수 | `<p:sp>` 카운트 / 슬라이드 | 디자인 밀도. 정보형 1장 = 30~40, 데이터형 = 15~25 |
| 글자 크기 단계 | `a:rPr/@sz` 의 unique 카운트 | **타이포 hierarchy** — 9단계 (8~80pt) = 완성도 高, 4~5단계 = 평탄 |
| 도형색 종류 | `a:solidFill/a:srgbClr/@val` unique | **컬러 어휘** — 10색 이상 = 인포그래픽급, 5~7색 = 가벼움 |
| 폰트 종류 | `<a:latin/@typeface>` unique | display + body 2 종이 표준 |
| 차트 위치·크기 비율 | `<p:graphicFrame>` 의 `a:xfrm` (EMU → px) | 막대·콤보는 1.5~2.5, 레이더는 1.0 정사각 |
| graphicFrame vs 사진 | `<p:graphicFrame>` vs `<p:pic>` | 차트 vs 이미지 비중 |

샘플 PPT 와 자동 생성 PPT 의 진짜 차이가 어디서 오는지 — Claude/디자이너가 만든 인포그래픽 1장이 디자인 완성도가 높아 보이는 *객관적* 이유는:

1. **컬러 어휘** — `AA2D00 / 0A2E0E / F5E9D4` 같은 채도 있는 카드 fill 이 시각 앵커. 자동 생성물이 `F5F5F7` 단일 회색 카드만 쓰면 단조로워 보임
2. **타이포 단계** — 8/9/10/11/12/14/36/40/80pt 9단계의 hierarchy. 자동 생성물이 10pt 본문 100+회로 폭주하고 헤드라인 25/42pt 만 있으면 중간 단계 (14·18·24pt) 가 비어 평탄
3. **디테일 소자** — 1px 디바이더 (`DDDDDD`), 12×12 컬러 칩 범례, 헤더 메뉴 텍스트 같은 잔여소자가 정돈된 인쇄물 느낌. 자동 생성물은 보통 누락

다만 **총 정보가치는 자동 생성물이 보통 압승** — 인포그래픽 1장 vs 10장 구조 + 콤보차트·레이더 같은 고난도 차트는 비교 대상이 다르다. 1장당 디자인 완성도와 총 정보가치는 *분리해 평가*. 자동화 ROI 가 가장 높은 개선은 ① 컬러 카드 컴포넌트 ② 14/18pt 중간 타이포 단계 ③ 빈약한 슬라이드 (도형 < 10) 보강.

## 차트 비율 함정 (가로로 너무 긴 차트)

「풀폭 차트 + 좁은 높이」 는 자동 생성 PPT 의 자주 발생하는 함정. 1280×720 캔버스에서 좌우 마진만 64px 빼고 차트가 1152px 풀폭을 차지하면 높이는 220~260px 만 남는다. 차트 비율 4.4~5.2 의 슬림한 띠 형태가 되어 X축 라벨 + 범례 + 데이터 라벨이 들어갈 세로공간이 부족해 *답답한* 시각.

### 차트 종류별 권장 비율 (w:h)

| 차트 | 권장 비율 | 풀폭 1152px 일 때 권장 높이 | 자주 발생하는 실수 |
|---|---|---|---|
| **막대 / 컬럼 / 콤보 (이중축)** | 1.5 ~ 2.5 | 460~770 (불가능) | 풀폭 그대로 220~260 으로 펼침 |
| **선그래프** | 1.5 ~ 2.5 | 460~770 (불가능) | 풀폭 그대로 펼침 |
| **레이더** | **1.0 정사각** | 풀폭 부적합 | 가로로 늘려 타원형 왜곡 |
| **파이 / 도넛** | **1.0 정사각** | 풀폭 부적합 | 가로로 늘려 타원형 |
| **scatter** | 1.0 ~ 1.5 | — | — |

풀폭 = 차트 부적합. **차트는 절대로 풀폭 점유하지 않는다** 가 1차 룰.

### 좌우 split 패턴 (B+C)

전통 인포그래픽이 풀폭 차트를 피하는 방법: `[차트 | 사이드 카드]` 의 7+5 분할 (또는 6+6).

```
+---- 슬라이드 1152 ----+
|                       |
| [차트   620~700 ]  [Insight Card 380~470 ]  |
|                       |
+----------------------+
```

차트 폭 ~660px / 사이드 카드 ~470px 로 두면 비율 ~2.0 의 정상치 + 차트 옆에 인사이트 카드가 같이 보여 가독성 ↑. 컴포넌트 시그니처:

```python
# 추천: Canvas 헬퍼로 split 분리
def split(self, left_span: int, right_span: int) -> tuple[Box, Box]:
    """12 컬럼 그리드를 left+right 로 쪼개 두 영역 (x, y, w, h) 를 반환."""
    ...

# 사용 (render.py)
left, right = canvas.split(left_span=7, right_span=5)
draw_chart(slide, ..., x_px=left.x, w_px=left.w)         # 차트
draw_insight_card(slide, ..., x_px=right.x, w_px=right.w) # 카드
```

기존 `draw_chart` / `draw_insight` 는 위치 옵션을 받게 확장하되 기본은 풀폭 유지로 하위호환. 인사이트가 풀폭 한 줄로 깔리던 슬라이드를 카드 형태로 바꾸면 자연스럽게 **컬러 카드 컴포넌트** + **중간 타이포 단계 (14·18pt)** 도입의 자리도 생긴다 (위 PPT 품질 절의 결함 3종 중 2종이 동시 해소).

### 레이더는 무조건 정사각

레이더는 가로로 늘리면 축 간 각도가 왜곡돼 데이터 거짓말. 풀폭 영역의 가운데 정렬 + 정사각 (420×420) + 우측에 비교 카드:

```
+---- 슬라이드 1152 ----+
|       420×420 정사각  |     [비교 카드]
|       (가운데 정렬)   |
+----------------------+
```

financial 프리셋 차원에서 차트 유형별 `aspect: "square"` / `"wide"` 메타를 두면, render 가 위치/크기 분기를 자동 처리할 수 있다.

### 회귀 가드

좌우 split 패턴 도입 후엔 회귀 테스트로 못박는다:

```python
def test_chart_aspect_ratio_within_bounds():
    for slide in slides_with_chart:
        w, h = chart_extent_px(slide)
        ratio = w / h
        if chart_type(slide) == "radar":
            assert 0.9 <= ratio <= 1.1, f"radar must be square, got {ratio:.2f}"
        else:
            assert 1.5 <= ratio <= 2.5, f"chart too wide/narrow: {ratio:.2f}"

def test_slide_not_sparse(slide_idx):
    """빈약 슬라이드 회귀 방지 — 도형 ≥ 7, 글자 ≥ 150."""
    shapes = root.findall(".//p:sp", NS)
    chars = sum(len(t.text or "") for t in root.findall(".//a:t", NS))
    assert len(shapes) >= 7, f"slide {slide_idx}: shapes {len(shapes)} (≥ 7 필요)"
    assert chars >= 150, f"slide {slide_idx}: chars {chars} (≥ 150 필요)"
```

`≥ 15` 같은 너무 높은 임계값은 카드 디자인을 강제로 비대하게 만든다 — 실제 측정값보다 약간 위로 잡아 빈약 회귀만 막는 게 합리적. 카드 본문을 줄바꿈으로 분리하면 도형·글자 수가 자연 증가한다.

## 결론

- **Claude 의 "PPT 생성 비결" 은 라이브러리가 아니라 절대좌표 + 컴포넌트 라이브러리** 이다. python-pptx 면 충분
- 디자인 토큰은 두 양식 (YAML frontmatter / CSS `:root` 변수) 을 한 트리로 통합하는 어댑터를 둔다
- 컴포넌트는 role 만 알게 하고 `resolver.py` 가 디자인별 토큰 이름 매핑을 단일 지점에 둔다
- **한글이 명조체로 떨어지면 `<a:ea>` / `<a:cs>` 미설정을 의심**. `Apple SD Gothic Neo` (macOS) / `Pretendard` (크로스플랫폼) 박기
- macOS 자동 시각 검증은 qlmanage 의 첫 슬라이드만. 전체 페이지엔 LibreOffice 헤드리스가 사실상 필수
- KPI 값 잘림은 카드 높이를 컬럼 전체에 걸쳐 사전 측정해 통일
- **차트는 풀폭 점유 금지** — 막대·콤보는 비율 1.5~2.5 (좌우 split), 레이더는 정사각 (1.0). 회귀 테스트로 못박을 것
- PPT 품질은 zip 풀어 도형·텍스트·차트 카운트로 *객관 측정* 가능. 자동 생성물의 부족분은 보통 ① 컬러 어휘 ② 타이포 단계 ③ 디테일 소자

## 관련 페이지

- [[auto-pipe-ppt]] — 본 패턴을 구현한 프로젝트
- [[multi-llm-provider-adapter-pattern]] — 비슷한 어댑터 응집 패턴 (LLM provider)
- [[csv-roundtrip-backup-restore]] — 입력 양식이 여럿이라도 단일 정규화 트리로 통합하는 패턴
