---
title: "Flask jsonify 의 Infinity/NaN 이 브라우저 JSON.parse 를 깨는 침묵 함정"
domain: "trading"
sensitivity: public
tags: ["bug", "trading", "flask", "json", "web", "ht-dde", "silent-fail", "divide-by-zero"]
created: "2026-07-09"
updated: "2026-07-09"
sources:
  - "session-logs/20260709-230951-58a3-현재까지-동작-결과-검토해줘-표면적으로만-보지-말고-가능성과-인사이트까지-감안해서-검토해줘.md"
confidence: high
related:
  - "wiki/projects/ht-dde.md"
  - "wiki/bugs/naver-finance-no-info-selector-drift.md"
  - "wiki/patterns/llm-json-parse-retry-with-dump.md"
---

# Flask jsonify 의 Infinity/NaN 이 브라우저 JSON.parse 를 깨는 침묵 함정

Python `json`·Flask `jsonify` 는 기본이 `allow_nan=True` 라 `Infinity` / `-Infinity` / `NaN` 을 **표준 위반 JSON** 으로 그냥 직렬화한다. `curl` 이나 Python 으로 확인하면 멀쩡히 파싱되지만, **브라우저 `response.json()`(= `JSON.parse`)은 이 리터럴을 거부**한다. 그 결과 fetch → `render()` 체인이 예외로 통째 멈춰 **화면이 텅 빈다**. "서버로 보면 데이터가 있는데 브라우저만 공백" 이라는 비대칭이 이 버그의 지문이다.

## 사례 (ht_dde `/rs` 종목추천 페이지, 2026-07-09)

증상: `/rs` 페이지의 추천 표 3개(초과수익·역행·실적눌림)가 **전부 공백**. "어제까진 보였는데 오늘 안 보임."

관측 비대칭이 결정적 단서였다.

```
# Python/curl 로는 정상 — 데이터 30/30/7행 반환, HTTP 200
curl -s .../api/rs/latest | python -c "import sys,json; d=json.load(sys.stdin); print({k:len(v['rows']) for k,v in d['algos'].items()})"
# {'contrarian': 30, 'earnings_dip': 7, 'weekly_rs': 30}

# 브라우저와 같은 엄격 파서(node)로는 실패
node -e "JSON.parse(require('fs').readFileSync('/tmp/rs.json','utf8'))"
# ★ 파싱 실패

# 원인 값 위치
curl -s .../api/rs/latest | grep -oE '"[a-z_]+":(Infinity|-Infinity|NaN)'
# "max_vol_ratio":Infinity
```

브라우저에서 `await (await fetch('/api/rs/latest')).json()` 이 예외를 던지면 그 뒤 `render()` 가 아예 실행되지 않아 세 표가 전부 초기 빈 상태로 남았다.

## 방아쇠 — 0 나눗셈이 만든 inf

`rs/scanner.py` 의 거래량 배율 계산 `ratio = vol / avg` 에서, **직전 20일 평균 거래량이 0** 인 종목(당일 스캔에 처음 들어온 금호에이치티)을 만나 `거래량 / 0 = inf` 가 됐다. `inf >= vol_mult` 는 True 라 hit 으로 잡히고, `max_vol_ratio = inf` 가 append-only CSV 에 기록 → API 응답에 그대로 실렸다. "어제까진 됐다" 는 그 종목이 **오늘 데이터에 처음** 등장한 탓 — 데이터 의존 버그라 코드 배포 없이도 어느 날 갑자기 터진다.

## 원인 정리

- **`allow_nan` 관측 비대칭**: `json.dumps` / Flask `jsonify` 는 기본 `allow_nan=True`. `Infinity`/`NaN` 을 비표준 리터럴로 출력한다. Python `json.load`(기본 허용)·`curl`(그냥 전송) 은 통과시켜 **서버 쪽 검증에서 정상으로 오인**하기 쉽다.
- **브라우저·엄격 파서는 거부**: `JSON.parse`(브라우저/JS), Go `encoding/json`, Rust `serde_json` 등 RFC 8259 준수 파서는 `Infinity`/`NaN` 에서 예외. 표준 JSON 에 이 값들이 없다.
- **프런트의 fail-closed 렌더**: `json()` 예외가 렌더 함수 전체를 중단시켜 부분 실패가 아니라 **전면 공백**이 된다. try/catch 가 없으면 콘솔 에러 하나 없이 조용히 빈 화면.

## 조치 — 2겹 방어

1. **값 생성부 (재발 방지)** — `rs/scanner.py`: `avg == 0` 이면 배율을 `inf` 대신 `NaN` 으로. 비율/배율 계산의 분모 0 가드. inf 를 애초에 만들지 않는다.
2. **직렬화 경계 (즉시 복구 + 전방위 보호)** — `web/server.py`: `/api/rs/latest` 응답 직전 모든 숫자 필드의 `inf/-inf → NaN(→"")` 치환. 어느 필드에서 비유한 값이 나와도 브라우저를 못 깨게 하고, **이미 오염된 기존 CSV 로도 즉시 복구**된다.
3. **회귀 테스트** — `test_contrarian_zero_avg_volume_no_inf`(0 평균거래량 종목이 inf 를 만들지 않는지). test-first 로 실패 재현 후 수정, 112개 전원 통과.
4. **배포·검증** — launchd `com.wooki.ht-dde` `kickstart -k` 재기동(상태는 SQLite 보존), node 엄격 파서로 `JSON.parse` 성공 재확인. (구 코드를 물고 있던 PID 가 교체됐는지도 확인 — [[stale-process-attributeerror-inprocess-coupling]] 참고.)

## 일반 교훈

- **"서버로 보면 정상, 브라우저만 공백" = 직렬화 경계를 의심하라.** 관측 도구(curl/Python)가 관대해 오진을 부른다. 브라우저와 **같은 엄격도**로 재현하라: `node -e 'JSON.parse(...)'` 또는 Python `json.dumps(obj, allow_nan=False)` / `json.loads(s, parse_constant=…)`.
- **직렬화 경계에서 `allow_nan=False` 를 기본으로.** API 응답을 만들 때 `json.dumps(..., allow_nan=False)` 로 두면 오염 값이 조용히 나가는 대신 서버에서 즉시 터져 조기에 잡힌다(fail-fast). 또는 경계에서 inf/nan 을 스크럽.
- **방어는 값 생성부 + 경계 2겹.** 생성부만 고치면 이미 저장된 오염 데이터가 안 풀리고, 경계만 고치면 다음 지표에서 재발한다. 경계 방어가 있으면 마이그레이션 없이 즉시 복구되는 게 이점.
- **비율/배율은 분모 0 가드 필수.** 20일 평균거래량 0(신규 상장·거래정지 후 재개·데이터 결측)은 실제로 나온다. `x / 0` 은 파이썬 float 에서 예외가 아니라 `inf` 를 반환하므로 조용히 오염된다.
- 같은 "조용한 오염" 계열: [[naver-finance-no-info-selector-drift]](셀렉터 드리프트로 silent 0), [[yahoo-finance-concurrent-silent-fail]](동시호출 필드 누락). 파싱 실패 시 원본 덤프 습관은 [[llm-json-parse-retry-with-dump]].

## 변경 이력

- 2026-07-09: 최초 작성 — ht_dde `/rs` 빈 화면이 응답 JSON 의 `Infinity`(vol/0 배율)로 브라우저 `JSON.parse` 만 깨진 사례를 일반화. Flask `jsonify` `allow_nan` 기본 허용의 관측 비대칭 + 2겹 방어(생성부 NaN 치환 + API 경계 스크럽) (출처: session-logs/20260709-230951-58a3-*)
