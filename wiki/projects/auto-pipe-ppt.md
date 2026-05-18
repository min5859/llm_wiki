---
title: "auto-pipe-ppt — JSON/YAML → 디자인 토큰 기반 멀티슬라이드 PPTX 자동 생성"
domain: personal
sensitivity: public
tags: ["project", "pptx", "python-pptx", "design-tokens", "ooxml", "claude-cli", "financial-report"]
created: 2026-05-16
updated: 2026-05-18
sources:
  - "session-logs/20260516-212048-3947-json,-yaml,-markdown-형태의-컨텐츠-디스크립션과-수치등이-들어있는-자료를.md"
  - "session-logs/20260518-232803-4c55-지금-프로그램으로-자동으로-생성된-out-samsung_full_apple.pptx,-ou.md"
confidence: high
related:
  - "wiki/analyses/python-pptx-design-token-pipeline.md"
  - "wiki/projects/auto-pipe-blog.md"
  - "wiki/projects/finance-analysis-nextjs.md"
---

# auto-pipe-ppt — JSON/YAML → 디자인 토큰 기반 멀티슬라이드 PPTX 자동 생성

콘텐츠 (JSON / YAML / Markdown) + 디자인 토큰 (`DESIGN.md`) 을 입력하면 편집 가능한 멀티슬라이드 PPTX 를 출력하는 Python 파이프라인. 1차 도메인 타겟은 **재무제표 분석 슬라이드 10장 구성**. Claude Web/Desktop 이 매번 LLM 으로 짜는 PPT 생성 방식을 **재사용 가능한 디자인 토큰 + 컴포넌트 라이브러리** 로 응집하는 것이 목표.

자매 프로젝트인 [[auto-pipe-blog]] (컨셉 1개 → velog 글) 와 같은 「입력 → 산출물」 변환 파이프라인이지만, 본 프로젝트는 PPT 도메인이라 OOXML 직접 조작이 필요한 부분이 다르다.

## 위치 / 진입점

- 디렉터리: `/Users/wooki/project/git/wk/auto-pipe-ppt`
- CLI: `auto-pipe-ppt {render | version | list-designs}`
  - `auto-pipe-ppt render <content.yaml|json> --design <design-dir> --out <out.pptx>`
  - `auto-pipe-ppt list-designs` — `design/<name>/DESIGN.md` 가 있는 폴더 목록
- 의존성: `python-pptx`, `pyyaml`, `python-frontmatter`, `typer`, pytest, ruff

## 디렉터리 구조

```
auto-pipe-ppt/
├── design/
│   ├── Apple/DESIGN.md              # YAML frontmatter 토큰 (Apple)
│   └── Minimalissimo/DESIGN.md      # 마크다운 + CSS :root 변수 토큰
├── src/auto_pipe_ppt/
│   ├── cli.py                       # typer 진입점
│   ├── tokens/                      # 디자인 토큰 로더 (YAML / CSS 양식 통합)
│   ├── schema/                      # Deck / Slide / Block 모델 + JSON+YAML 로더
│   ├── compose/                     # 1280×720 캔버스 + 컴포넌트 디스패처
│   │   └── components/              # Hero, Text, FooterNote, KpiRow, Insight, Verdict, ScoreCard, Conclusion, Table
│   ├── charts/                      # M4 (미구현)
│   └── utils/emu.py                 # px↔pt↔EMU 환산
├── examples/
│   ├── hello.yaml / hello.json      # 등가 데모
│   ├── samsung_summary.json         # 7장 재무 도메인 데모
│   └── financial_minimal.yaml       # 5장 (KPI / Insight / Verdict / ScoreCard / Conclusion / Table)
├── ppt_sample/*.pptx                # Claude 가 만든 참조 PPT (구조 분석용)
├── sample/*.pdf, *.json             # 12페이지 재무 리포트 참조
└── tasks/{todo.md, lessons.md}
```

## 핵심 설계 판단

### 1. 라이브러리 선택: `python-pptx` + 절대좌표 도형 + 일부 OOXML 직접 작성

샘플 PPTX 를 unzip 해 분석한 결과 (Claude Web/Desktop 이 만든 PPT 도 동일 패턴):

- `placeholder` 미사용. `<p:sp>` 30~40개를 절대좌표 (EMU) 로 직접 깔음
- 폰트는 Inter Display / Inter 만 사용
- 차트는 `<p:graphicFrame>` + 임베디드 `chart1.xml` + 작은 Excel 워크시트

라이브러리 비교:

| 도구 | PPTX 편집 가능 | 차트 자유도 | 디자인 자유도 | 비용 |
|---|---|---|---|---|
| **python-pptx** | ◎ | △ (콤보·이중축 어려움) | ◯ | 무료 |
| **python-pptx + matplotlib 이미지** | ◎ 텍스트만, 차트는 이미지 | ◎ | ◎ | 무료 |
| **Aspose.Slides for Python** | ◎ | ◎ | ◎ | 유료 (비쌈) |
| **HTML+CSS → Playwright → 이미지** | ✕ (이미지 한 장) | ◎ | ◎ | 무료 |
| **LibreOffice UNO** | ◎ | ◯ | △ | 무료, 운영 무거움 |

콤보차트 (이중축) / 레이더 같은 차트는 python-pptx 단독으론 까다로움 → **python-pptx + OOXML 직접 작성** 으로 확정. 비상시 matplotlib 이미지 fallback 도 둠. 일반 비교 분석은 [[python-pptx-design-token-pipeline]] 참고.

### 2. 디자인 토큰의 이중 입력 어댑터

- **Apple/DESIGN.md** — YAML frontmatter 에 colors / typography / spacing 토큰
- **Minimalissimo/DESIGN.md** — 마크다운 본문 + 마지막에 `:root { --color-*: ...; }` CSS 변수 블록

두 양식을 `DesignTokens` 단일 데이터 클래스로 통합 — 마크다운 표 파싱이 아니라 **CSS 변수 블록 + YAML frontmatter 두 양식의 파서를 둘 다 제공** 하는 어댑터 접근. `{section.name}` 같은 참조 표현은 3회 반복으로 해석 (Apple `button-primary` 의 `{colors.primary}` → `#0066cc`).

### 3. 컴포넌트는 role 만 참조 (resolver 패턴)

컴포넌트 (`KpiRow`, `Insight` 등) 가 디자인별 토큰 이름 (Apple 의 `ink` vs Minimalissimo 의 `inkwell` 등) 을 직접 알지 않도록, `resolver.py` 가 role (`ink` / `muted` / `primary` / `canvas` / `hero` / `body` / `kpi-value` / `kpi-label` / `body-sm` / `grade`) → 디자인별 token name 의 매핑을 단일 지점에 둔다. positive/negative tone 도 `tone_color()` 가 `#0a8a3a` / `#c43b3b` 안전 기본값으로 fallback.

### 4. 한글 폰트 fallback: `<a:latin>` 만으론 부족

기본 `python-pptx` 의 run 은 `<a:latin>` 만 박는데, 그 결과 한글이 macOS 시스템 명조체로 떨어지는 회귀가 발생 (산세리프 디자인 의도와 정반대). **수정**: 모든 run 에 `<a:ea>` (East Asian) 와 `<a:cs>` (Complex Script) 까지 박고 ea 에 `Apple SD Gothic Neo` 를 명시. PowerPoint 가 한글에는 ea 폰트, Roman 문자엔 latin 을 자동 분기 적용. 회귀 테스트 1건 추가. 일반 패턴은 [[python-pptx-design-token-pipeline]] 참고.

### 5. 시각 검증은 macOS qlmanage 첫 슬라이드만

LibreOffice (`brew install --cask libreoffice`, ~700MB) 가 없으면 페이지 2~N 의 시각 검증은 PowerPoint/Keynote 직접 열기뿐. 자동화에는 `qlmanage -t -s 1600 -o $OUT_DIR <pptx>` 가 가벼움 (첫 슬라이드 썸네일 PNG 1장만 반환). CI 환경엔 LibreOffice 헤드리스가 사실상 필수.

### 6. 마일스톤별 점진 구축

| 단계 | 결과 |
|------|------|
| **M0** | pyproject.toml, CLI 진입점 (`render`/`version`/`list-designs`), 스모크 테스트 4건 |
| **M1** | utils/emu, 두 양식 토큰 로더, `DesignTokens` API + `chart_palette()` primary 기반 hue rotation 5색. 16건 테스트 |
| **M2** | `Deck/Slide/Block` (Hero/Text/FooterNote) + JSON/YAML 로더 + 1280×720 캔버스 (12 컬럼 그리드) + shape 헬퍼 (rect/text/hline) + role resolver. 29건 테스트, 두 디자인 양쪽 PPTX 생성 검증 |
| **M3** | 재무 컴포넌트 6종 (`KpiRow`, `Insight`, `Verdict`, `ScoreCard`, `Conclusion`, `Table`) + L1 한글 폰트 fix + 5장 `financial_minimal.yaml` 데모. 41건 테스트 |
| **M4** | 차트 5종 (bar / line / combo / radar / 3계열 막대) — **미구현** |
| **M5** | 재무 프리셋 어댑터 (`samsung_summary.json` 같은 도메인 입력 → Deck 변환) — **미구현** |
| **M6** | 샘플 재현 검증 (참조 PDF 12페이지 → 10장 PPTX 재현) — **미구현** |

## Phase 1 검증 (2026-05-17 새벽)

- **`samsung_summary.json`** — 7장 재무 데모, Apple/Minimalissimo 양쪽 정상 출력
- **`financial_minimal.yaml`** — 5장 데모 (KPI / Insight / Verdict / ScoreCard / Conclusion / Table)
- **시각 확인**: qlmanage 로 첫 슬라이드 추출 → 두 디자인 모두 토큰 의도대로 분기 (Apple = SF Pro Display + `#0066cc` primary + 56px hero / Minimalissimo = GeistSans + `#000000` + 24px quiet heading)
- **테스트**: 41건 그린 (tokens 16 / schema·compose 13 / m3-components 12)
- **commit 3건**: `f1cd8ab` (M0+M1+M2 scaffold) / `bb29394` (L1 ea/cs 한글 fix) / `ed8d51c` (M3 재무 컴포넌트 6종)

## KPI Row 값 잘림 / Footer 겹침 함정 (2026-05-17)

- "3,336,059억" 같은 긴 숫자가 1줄 폭 초과 → wrap → KPI 카드 높이 부족으로 잘림 → "3" 만 보이는 회귀
- 데모 첫 슬라이드에 너무 많은 컴포넌트 → ScoreCard 와 footer 겹침

**해결**: `kpi_row` 가 KPI value 박스 높이를 모든 카드에 걸쳐 사전 측정해 가장 긴 값에 맞춰 2줄 높이 허용. 데모는 ScoreCard 를 별도 슬라이드로 분리. 디자인 토큰 시스템은 좌표·치수도 데이터로 다뤄야 한다는 교훈.

## 알려진 한계

- **차트 미구현 (M4)** — 현재 출력은 도형·텍스트만. 콤보·레이더는 OOXML 직접 작성 필요
- **MD 입력 로더 미구현** — 휴리스틱이 복잡해 M2 범위에서 JSON/YAML 만 지원
- **qlmanage 는 첫 페이지만** — 2~N 페이지 자동 시각 회귀 검증 부재. LibreOffice 헤드리스가 사실상 필수
- **재무 프리셋 어댑터 부재 (M5)** — `examples/samsung_summary.json` 같은 도메인 입력을 Deck 으로 자동 변환하는 어댑터가 없어 수동으로 schema 매핑

## 차트 비율 정상화 + s9 빈약 보강 (2026-05-18, M6)

생성된 `samsung_full_apple.pptx` 와 샘플 인포그래픽 (`ppt_sample/ev_market.pptx`, `travel_intro.pptx`) 을 unzip 메타 비교로 객관 측정한 결과 발견된 4 결함을 한 작업 (B+C) 으로 동시 해소.

### 발견된 결함

| # | 결함 | 측정 |
|---|---|---|
| 1 | **차트가 가로로 너무 김** | s2/3/5/7/8: **비율 4.43** (1152×260), s4: 4.80, s6: 5.24. 권장 1.5~2.5 |
| 2 | **s9 (업계비교) 빈약** | 도형 4 / 텍스트 47자. 다른 슬라이드 (도형 20~33) 와 격차 |
| 3 | **컬러 어휘 단조** | 도형색 7~8종, `F5F5F7` 단일 회색 카드만. 샘플은 10~11종 + 채도 있는 카드 |
| 4 | **타이포 hierarchy 평탄** | 글자 크기 4~5단계 (10pt 117회 폭주). 샘플은 9단계 (8~80pt) |

### 통합 작업 (B+C 한 번)

| # | 변경 | 위치 | 비고 |
|---|---|---|---|
| 1 | `Canvas.split(left_span, right_span)` 헬퍼 신설 | `compose/canvas.py` | 7+5 split → 차트 ~660px / 카드 ~470px |
| 2 | `Chart` / `Insight` 시그니처에 `x_px`/`w_px` 옵션 추가 | `compose/components/chart.py`, `insight.py` | 기본 풀폭 유지, override 가능 |
| 3 | `render._draw_slide` 가 `[Chart, Insight]` 페어를 좌우 split 으로 처리 | `compose/render.py` | s6 의 `[Chart, Verdict, Insight]` 는 `[Chart, Insight, Verdict]` 로 순서 변경해 split 페어링 + Verdict 풀폭 양립 |
| 4 | radar (s9) 정사각 (420×420) 분기 | `render.py` | 차트 폭 = 높이. 우측에 자동 생성 비교 Insight 카드 |
| 5 | `insight_card` 변형 — accent color + 14/18pt 중간 타이포 | `compose/components/insight.py` | DESIGN.md 의 surface/hairline/radius 토큰 그대로 사용 (임의 색 금지) |
| 6 | 차트 비율 가드 + s9 빈약 가드 회귀 테스트 추가 | `tests/test_m6_chart_split.py` | `assert 1.5 <= ratio <= 2.5` (radar 만 ~1.0), s9 도형 ≥ 7 / 글자 ≥ 150 |

### 검증

- 차트 비율: s2~s8 = 2.07~2.21 (이전 4.43~5.24), s9 = **1.00** 정사각 (이전 2.74)
- s9 보강: 도형 4 → 8, 글자 47 → 158
- 테스트: m6 12 / m5 14 / 전체 78 모두 그린

### 일반 교훈

「풀폭 차트 = 부적합」 이 1차 룰. 막대·콤보는 1.5~2.5, 레이더·파이는 정사각 1.0. 좌우 split 패턴 하나 도입으로 ① 차트 비율 ② 빈약 슬라이드 ③ 컬러 카드 ④ 중간 타이포 단계 4 결함이 동시 해소. 회귀 가드는 임계값을 *측정값보다 약간 위로* 설정 — 너무 높이면 강제로 카드를 비대하게 만들고, 너무 낮으면 회귀 못 막음. 일반 패턴은 [[python-pptx-design-token-pipeline]] 의 「PPTX 품질 객관 지표」/「차트 비율 함정」 두 절로 분리.

## 변경 이력

- 2026-05-17: 신규 프로젝트 생성. M0/M1/M2/M3 진행. `python-pptx` + 절대좌표 도형 + 일부 OOXML 직접 작성 전략 확정. 한글 폰트 ea/cs fallback fix (L1). 재무 컴포넌트 6종 + 5장 데모. 일반 패턴은 [[python-pptx-design-token-pipeline]] 로 분리 (출처: session-logs/20260516-212048-3947-*)
- 2026-05-18: 차트 비율 정상화 + s9 빈약 보강 (M6). 생성 PPT 와 샘플 인포그래픽을 unzip 메타로 비교해 4 결함 (차트 비율 4.43~5.24 / s9 도형 4개 / 컬러 어휘 단조 / 타이포 평탄) 발견. `Canvas.split(7, 5)` 헬퍼 + 차트/Insight 위치 옵션 + radar 정사각 + accent insight_card (DESIGN.md 토큰 그대로) + 차트 비율 가드 회귀 테스트 (`test_m6_chart_split.py`, 12 cases). 차트 비율 2.07~2.21 / s9 정사각 1.00 / s9 도형 4→8·글자 47→158. 전체 78 테스트 그린. 일반 패턴은 [[python-pptx-design-token-pipeline]] 의 「PPTX 품질 객관 지표」 + 「차트 비율 함정」 두 절로 분리 (출처: session-logs/20260518-232803-4c55-*)
