---
title: "SQLite 커넥션 스레드 간 공유 — ProgrammingError 로 데몬 스레드 청산 침묵 마비 (threading.local 해법)"
domain: "trading"
sensitivity: "public"
tags: ["bug", "sqlite", "threading", "python", "paper-trading", "daemon-thread", "silent-failure"]
created: "2026-07-22"
updated: "2026-07-22"
sources:
  - "session-logs/20260721-231802-bb66-지금-폴더의-프로그램이-잘-동작하고-있는지-상태-체크좀-해줘.md"
confidence: high
related:
  - "wiki/projects/ht-dde.md"
  - "wiki/bugs/stale-process-attributeerror-inprocess-coupling.md"
  - "wiki/analyses/polling-interval-vs-bar-interval.md"
---

# SQLite 커넥션을 스레드 간 공유하면 ProgrammingError — threading.local 지연 생성으로 해결

## 증상

ht_dde 의 rt(실시간 청산 모니터) 데몬 스레드가 15~20초 폴링마다 아래 예외를 내며 청산 평가(`positions_for()` 의 SELECT, `rs/paper.py`)가 전혀 실행되지 않음. 상태 점검 시점까지 **누적 388회**, 프로세스는 계속 살아 있어 외견상 정상.

```
sqlite3.ProgrammingError: SQLite objects created in a thread can only be used
in that same thread. The object was created in thread id 8439864704 and this
is thread id 6148861952.
```

## 근본 원인

`RsPaperStore.__init__` 이 커넥션 **1개**를 생성(`self.con = sqlite3.connect(...)`)해 인스턴스에 보관 — 메인 스레드에서 만들어진 이 커넥션을 데몬 모니터 스레드가 재사용했다. Python `sqlite3` 는 기본값(`check_same_thread=True`)으로 커넥션을 생성 스레드에서만 쓰도록 강제한다.

구조적 제약: "재시작해도 자산 상태 보존"을 위해 store 인스턴스는 메인 스레드 + 데몬 모니터 + Flask 요청 핸들러가 **공유**해야 한다. 즉 인스턴스 공유는 유지하면서 커넥션만 스레드별로 분리해야 하는 문제.

## 수정 — 인스턴스는 공유, 커넥션은 threading.local 지연 생성

```python
def __init__(self, db_path):
    self.path = str(db_path)          # 경로만 저장
    self._local = threading.local()   # 스레드별 저장소

@property
def con(self) -> sqlite3.Connection:
    con = getattr(self._local, "con", None)
    if con is None:
        con = sqlite3.connect(self.path, timeout=10)
        con.row_factory = sqlite3.Row
        self._local.con = con
    return con
```

- **호출부 무변경** — 기존 `self.con.execute(...)` 형태가 @property 를 통해 그대로 동작.
- 각 스레드가 첫 접근 시 자기 커넥션을 지연 생성 → 같은 DB 파일에 대한 동시 접근 충돌은 SQLite 자체의 WAL·파일 락에 위임.
- 대안인 `check_same_thread=False` + 전역 락은 락 경합·직렬화 비용이 생김 — 스레드별 커넥션이 더 깔끔.
- pytest 24 passed (스레드 안전성 테스트 포함).

## 임팩트 — "죽지 않는 장애"가 A/B 실험을 오염

예외가 로그에만 쌓이고 프로세스는 살아 있어, rt 전략의 장중 청산이 도입(7/17)부터 **4일간 완전 비동작**. 같은 종목·같은 규칙으로 진입한 대조군 tier 는 11건 매도했는데 rt 는 0건(포지션 10개 미정산) → 감시 주기 A/B 비교가 오염돼 **데이터 리셋 후 재시작** ([[ht-dde]] 2026-07-21 절).

> 교훈: 백그라운드 스레드의 반복 예외는 프로세스를 죽이지 않아 상태 점검 전까지 드러나지 않는다. 데몬 스레드를 새로 붙일 때는 ① 스레드에서 접근하는 SQLite 커넥션의 소유권(생성 스레드)을 설계 시점에 확인하고 ② 예외 반복 발생을 카운터/알람으로 표면화할 것.

## 변경 이력

- 2026-07-22: 최초 작성 — ht_dde 상태 점검 세션에서 발견·수정 (출처: session-logs/20260721-231802-bb66-*)
