---
title: "kakao-mem — Mac KakaoTalk 메모리 CLI"
domain: personal
sensitivity: internal
tags: ["kakao", "kakaotalk", "cli", "python", "kakaocli", "summary", "personal-memory"]
created: "2026-05-05"
updated: "2026-05-06"
sources:
  - "session-logs/20260505-232155-9172-지금-코드를-분석해-주세요.md"
confidence: medium
related:
  - "wiki/projects/kakao-db.md"
  - "wiki/analyses/kakao-messaging-automation-options.md"
---

# kakao-mem — Mac KakaoTalk 메모리 CLI

`kakao-mem` 은 macOS 의 KakaoTalk 로컬 데이터를 `kakaocli` 어댑터로 읽어 JSONL 로 보관하고 일일 Markdown 요약을 만드는 read-only Python CLI 다. AGENTS.md 의 설계 의도 (어댑터 격리 / 결정적 파이프라인 / read-only 우선) 와 실제 구현이 잘 일치한다.

## 전체 구조

```
src/kakaomem/
├── cli.py            # argparse: doctor/rooms/recent/collect/collect-all/summarize/launchd-plist/config
├── config.py         # config/local.toml + 환경변수 → AppConfig
├── models.py         # ChatRoom, Message dataclass + 메시지 ID 파생
├── archive.py        # JSONL append + message_id 기반 dedup
├── checkpoint.py     # 룸별 마지막 message_id 저장
├── summary.py        # 링크/질문/멘션/키워드 추출 + Markdown 렌더
├── automation.py     # launchd plist 생성
└── adapters/kakaocli.py  # subprocess 래퍼 + JSON/텍스트 파싱
```

## 잘 설계된 점

- **레이어 분리**: `subprocess` 호출은 `adapters/kakaocli.py` 한 곳에만 있고, 나머지 모듈은 모두 dataclass 위에서 동작.
- **`Message` 정규화**: `message_id` 가 없을 때 `derive_message_id` 로 `(room_id, sent_at, sender, text)` 의 sha256 (24자) 을 만들어 dedup 키로 사용 → 어떤 소스든 idempotent archive.
- **`KakaoCli._try_json_command`**: `kakaocli` 의 명령 시그니처가 버전마다 달라질 수 있다는 현실을 반영해 후보군을 차례대로 시도.
- **테스트 범위**: parser, dedup, summary 추출, launchd plist 직렬화, config 로드 등 결정적 동작은 거의 다 커버 (13 tests, 0.003s 통과).
- **프라이버시 가드레일**: README/AGENTS/setup.md 가 모두 "raw 채팅, `data/`, `config/local.toml` 커밋 금지" 를 반복 강조. `.gitignore` 운영 방침과 일치.

## 잠재 이슈 (우선순위)

1. **`load_messages` 의 JSONDecodeError 미처리** (`archive.py:49-59`)
   - `read_message_ids` 는 깨진 라인 skip 하지만 `load_messages` 는 raise → summarize 가 단 한 줄 손상으로 통째 실패. skip + 경고 로그가 더 안전. 우선순위 1.

2. **`load_messages(Message(**item))` 의 폭주 위험** (`archive.py:58`)
   - 외부 손편집 JSONL 에 추가 필드가 있으면 `TypeError`. 보수적으로 알려진 필드만 골라서 생성하는 편이 안전.

3. **`cmd_summarize` 의 룸 ID sanitize 매핑 부재** (`cli.py:151,201-205`, `archive.py:room_archive_path`)
   - 설정/CLI 인자가 비면 `data/raw/*.jsonl` 글롭으로 룸 ID 추정. sanitize 가 `-`, `_` 외 → `_` 로 바꾸므로 원본 ID 에 한글/콜론이 섞이면 collect 와 summarize 가 다른 키 사용. 안전을 위해 원본 → 안전이름 매핑을 별도 파일로 저장 권장.

4. **`_parse_text_messages` 의 `sender:` 분할 위험** (`adapters/kakaocli.py:200-230`)
   - 본문에 `:` 이 들어가면 첫 토큰이 sender 로 잡힘 (예: `URL https://...`). JSON 경로가 우선이라 fallback 이긴 하지만 sender_name 길이 제한이나 공백 가드 권장.

5. **`is_question` 의 한국어 마커 휴리스틱 false positive** (`summary.py:13`)
   - `"나요"` 가 `"만나요"`, `"지나요"` 같은 평서 맥락에 섞이면 질문으로 잡힘. 결정적 MVP 컨셉엔 부합하나 `?`/`？` 만으로 좁히는 옵션 추가 가치.

6. **`_try_json_command` 가 마지막 에러만 보존** (`adapters/kakaocli.py:86-95`)
   - 8개 후보 모두 실패하면 마지막 에러만 노출 → 디버깅 시 시도 이력이 보이지 않음.

7. **`config/local.toml` 이 git 에 들어가 있음**
   - `setup.md`/`README.md` 는 "ignored by Git" 이라고 적었으나 실제로는 추적 중. 의도가 "예시 사본은 추적, 사용자 사본은 무시" 라면 `local.toml` 은 빼고 `kakao-mem.example.toml` 만 두는 운영이 일관됨.

## 카카오와 직접 통신할 수 있는가 (옵션 분석)

3 옵션 비교의 일반화는 [[kakao-messaging-automation-options]] 참고. 결론:
- 이 프로젝트의 목적 (개인 메모리·요약) 에는 **하이브리드 (읽기는 로컬 DB, 쓰기는 Kakao Developers `나에게 보내기` API)** 가 비용/리스크 대비 가장 합리적.
- LOCO 클라이언트 옵션은 PC 상시 가동 제약을 푸는 유일한 길이지만 약관 위반·계정 정지 리스크가 큼. 별도 카카오 계정으로만 운용하는 게 업계 관행.

## "카톡 앱이 켜져 있어야" 의 의미

- `kakao-mem` 명령 실행 시점에는 KakaoTalk 앱이 떠 있을 필요 없음 — DB 파일만 디스크에 있으면 `kakaocli` 가 읽음.
- 그러나 KakaoTalk 앱이 꺼져 있는 동안 새 메시지는 로컬 DB 에 동기화되지 않음 → 푸시는 모바일/서버에 머무름.
- 따라서 launchd 로 30분마다 `collect-all` 을 도는 자동화가 의미를 가지려면 KakaoTalk Mac 앱이 로그인된 상태로 백그라운드에 떠 있어야 함.
- 실무 권장: KakaoTalk 앱을 시스템 설정 → 로그인 항목에 등록, 항상 켜진 Mac (예: Mac mini) 환경.

## 관련 맥락

- 신규 자매 프로젝트 [[kakao-db]] — sqlcipher 를 직접 열어 메시지 본체를 읽고 LOCO 송신을 추가하는 Rust 어댑터. `kakao-mem` 의 `kakaocli` 의존을 우회하는 진영.
- [[kakao-messaging-automation-options]] — 카카오톡 자동화 3 옵션 비교
- [[kakaotalk-mac-data-locations]] — App Store 빌드의 sqlcipher DB 위치

## 변경 이력

- 2026-05-06: 최초 생성 (session-logs/20260505-232155-9172) — 코드 분석 결과 + 8개 잠재 이슈 + 직접 통신 3 옵션
