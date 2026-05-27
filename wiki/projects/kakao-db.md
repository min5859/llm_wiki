---
title: "kakao-db — Mac KakaoTalk 로컬 DB + LOCO 어댑터 (Rust)"
domain: personal
sensitivity: internal
tags: ["kakao", "kakaotalk", "rust", "sqlcipher", "loco", "personal-memory", "M0", "M1"]
created: "2026-05-06"
updated: "2026-05-27"
sources:
  - "session-logs/20260505-235703-d859-https---www.gpters.org-dev-post-kakaotalk-macro-er.md"
  - "session-logs/20260527-225019-71d3-지금-프로그램의-AI-provider-로-어떤걸-사용하고-있나요.md"
confidence: medium
related:
  - "wiki/projects/kakao-mem.md"
  - "wiki/analyses/kakao-messaging-automation-options.md"
  - "wiki/analyses/kakaotalk-mac-data-locations.md"
  - "wiki/patterns/macos-tcc-full-disk-access.md"
---

# kakao-db — Mac KakaoTalk 로컬 DB + LOCO 어댑터

`kakao-db` 는 macOS KakaoTalk 의 로컬 sqlcipher DB (장기 히스토리·친구 별명·채팅방) 와 LOCO 서버 (실시간 송수신) 를 결합한 하이브리드 어댑터 프로젝트다. 본인 계정·본인 데이터에 한정한 개인용 자동화를 전제로 한다. 자매 프로젝트 [[kakao-mem]] (Python + `kakaocli` 의존) 의 한계 (kakaocli 미설치, App Store 빌드의 메시지 DB 별도 포맷) 를 우회한다.

## 초기 결정 (2026-05-06)

| 항목 | 선택 | 이유 |
|------|------|------|
| 1. 구현 언어 | **Rust 단일** | 단일 바이너리 + cron 친화. sqlcipher (`rusqlite + bundled-sqlcipher`) / LOCO 양쪽 OSS 참고 충분 |
| 2. LOCO 클라이언트 | **기존 OSS wrap** (`node-kakao` / `agent-messenger` 계열 참고) | M2 빨리 동작 → 필요 시 fork |
| 3. 인터페이스 | **단발 CLI + cron** | 단순. 송신마다 LOCO 재인증 비용은 감수. M5 에서 TUI/데몬 검토 |
| 4. 자격증명 저장 | **macOS Keychain (`security`)** | Mac 전용이므로 자연스럽고 실수 커밋 위험 0 |
| 5. 카톡 클라이언트 버전 | **App Store 26.3.0** (`com.kakao.KakaoTalkMac`, TeamID `L75WVXX68A`) | 사용자 환경 |

## 마일스톤

- **M0**: 디렉토리 스켈레톤 + `.gitignore` (자격증명·DB 사본 차단) + `tasks/todo.md`/`lessons.md` + `README.md` + Rust toolchain (`rustc 1.95.0` aarch64) + `cargo init` + `clap`/`anyhow`/`tracing` 의존성 + `kakao-db --version`/`--help`/기본 실행 동작. **완료 ✅**
- **M1**: DB 리더 (읽기 전용 사본 모드 / `inspect` 서브커맨드 / sqlcipher 키 환경변수·Keychain 입력). **진입 직후**
- **M2**: LOCO 송신 (OSS wrap)
- **M3**: 머지 뷰 (DB + LOCO 통합)
- **M4**: 자동화 (cron / launchd)
- **M5**: TUI / 데몬

## M0 산출물

- `Cargo.toml`: `clap` (derive), `anyhow`, `tracing`, `tracing-subscriber` (env-filter), `rusqlite` (bundled-sqlcipher), `chrono` (serde), `walkdir`. dev: `tempfile`.
- `src/{main.rs,cli.rs,db.rs,inspect.rs}` — 모듈 구조 + `inspect` 서브커맨드 (DB 후보 탐색)
- 단위 테스트 6/6 통과 (합성 sqlcipher DB 로 키 검증 / 휴리스틱 검증)
- `.zshrc` 에 `. "$HOME/.cargo/env"` 1줄 추가 (cron 은 M4 에서 절대경로 처리)

## M1 결정 사항

| 항목 | 선택 | 이유 |
|------|------|------|
| B1. sqlcipher 크레이트 | `rusqlite` + `bundled-sqlcipher` feature | 단일 바이너리 유지 |
| B2. DB 접근 모드 | **읽기 전용 사본** (`~/.kakao-db/cache/` 로 cp, 매 실행 갱신) | 카톡 실행 중 락·무결성 위험 회피 |
| B3. 키 도출 | 사용자가 환경변수 / Keychain 으로 직접 주입 (자동 추출은 별도 spike) | App Store 빌드는 Sandbox Keychain 의존 가능성 높음 |
| B4. 첫 서브커맨드 | `inspect` (DB 파일 목록 + 스키마 dump) | 스키마 미확정 단계니 먼저 들여다보기 |

## inspect 휴리스틱 (메시지 DB 식별)

`inspect` 는 KakaoTalk sandbox 안에서 DB 후보를 다음 방법으로 추정한다 (8 후보 검출):

- `[magic]` SQLite magic 시그니처 (`SQLite format 3` 16바이트) — Google Measurement 등 평문 SQLite 가 잡힘
- `[wal  ]` `-wal` / `-shm` 동반 + 자체 파일에 평문 매직 부재 → sqlcipher 메시지 DB 후보
- `[ext  ]` 확장자 매칭 (`.db`/`.sqlite*`) — Cache/HTTPStorages/WebKit 부속 DB

App Store 26.3.0 의 메시지 본체는 `[wal  ]` 에서만 잡힘. 자세한 위치 패턴은 [[kakaotalk-mac-data-locations]].

## 알려진 함정 (운영)

- **macOS TCC 토스트 팝업**: iTerm 등 터미널이 다른 앱 sandbox (`~/Library/Containers/com.kakao.KakaoTalkMac/`) 에 접근하려 할 때마다 "iTerm 이 다른 앱의 데이터에 접근하려고 합니다" 시스템 토스트가 뜸. 해결: 시스템 설정 → 개인정보 보호와 보안 → 전체 디스크 접근에 iTerm 추가 + iTerm **완전 종료 후 재시작**. [[macos-tcc-full-disk-access]]
- Claude Code 의 권한 팝업 (Bash 명령 첫 토큰 단위) 과는 별개. 빈도가 높으면 `/fewer-permission-prompts` skill 추천.
- KakaoTalk 앱 실행 중 DB 직접 open 은 락/무결성 위험 → B2 결정대로 `~/.kakao-db/cache/` 로 cp 한 사본만 read-only open.

## 운영 모드

사용자 명시 (2026-05-06 00:23): "보안 위험이나 시스템 위험 사항만 묻고, 추천대로 자율 진행". 이를 `tasks/lessons.md` 에 기록. 이후 결정은 추천 옵션을 기본으로 채택하고 보안·시스템 변경 시에만 사용자 확인.

## AI provider 다중화 (2026-05-27)

M4 (자동화) 진행 중, `scripts/run-summary.sh` 의 AI 요약 CLI 를 단일 provider 고정에서 다중 provider 선택 가능하도록 추상화. research-wiki 프로젝트의 multi-provider Python 구현 패턴을 Shell 스크립트에 적용.

**환경 변수**

| 변수 | 기본값 | 설명 |
|------|--------|------|
| `KAKAO_AI_PROVIDER` | `codex` | `claude` \| `cursor` \| `codex` |
| `KAKAO_AI_MODEL` | (빈 값) | 모델 오버라이드 (빈 값이면 각 CLI 기본 모델) |
| `KAKAO_AI_BIN` | — | 레거시: 직접 바이너리 지정 (설정 시 구형 동작 유지) |
| `KAKAO_AI_EXTRA_ARGS` | — | 레거시: `KAKAO_AI_BIN` 과 함께 쓸 추가 인자 |

**Provider별 Shell 함수 (호출 방식 차이)**

```bash
# claude: CLAUDECODE 환경변수 제거로 중첩 세션 방지
ai_run_claude() {
  env -u CLAUDECODE claude -p [--model MODEL] --output-format text "$prompt"
}

# cursor (agent 바이너리):
ai_run_cursor() {
  agent -p --force [--model MODEL] --output-format text "$prompt"
}

# codex: stdin 방식 + 임시 파일 출력
ai_run_codex() {
  printf "%s" "$prompt" | codex exec - -o "$tmpfile" --ephemeral [-m MODEL]
  cat "$tmpfile"
}
```

**주의**: claude 를 cron 자동화 환경에서 호출할 때 `CLAUDECODE` 환경변수가 설정돼 있으면 세션 중첩으로 실패할 수 있다. `env -u CLAUDECODE` 로 제거 필요.

→ Shell 스크립트 기반 AI CLI 어댑터 일반 패턴: [[multi-llm-provider-adapter-pattern]]

## 관련 맥락

- [[kakao-mem]] — Python + `kakaocli` 의존 자매 프로젝트. `kakao-db` 는 그 한계 우회.
- [[kakao-messaging-automation-options]] — 자동화 3 옵션 비교 (옵션 3 하이브리드와 LOCO 옵션의 절충)
- [[kakaotalk-mac-data-locations]] — App Store 26.3.0 의 sqlcipher DB 위치 식별 패턴
- [[macos-tcc-full-disk-access]] — TCC 권한 부여 절차

## 변경 이력

- 2026-05-06: 최초 생성 (session-logs/20260505-235703-d859) — M0 완료 / M1 진입 / 5 결정 / inspect 휴리스틱 / 운영 모드 기록
- 2026-05-27: AI provider 다중화 추가 — `KAKAO_AI_PROVIDER` 환경변수로 claude/cursor/codex 선택, 기본값 codex, provider별 Shell 함수 추상화, 레거시 KAKAO_AI_BIN 하위 호환 유지 (session-logs/20260527-225019-71d3-*)
