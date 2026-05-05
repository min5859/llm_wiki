---
title: "KakaoTalk for Mac (App Store 26.x) 데이터·sqlcipher DB 위치"
domain: both
sensitivity: public
tags: ["kakao", "kakaotalk", "macos", "sqlcipher", "sqlite", "wal", "sandbox", "tcc"]
created: "2026-05-06"
updated: "2026-05-06"
sources:
  - "session-logs/20260505-235703-d859-https---www.gpters.org-dev-post-kakaotalk-macro-er.md"
confidence: medium
related:
  - "wiki/projects/kakao-db.md"
  - "wiki/patterns/macos-tcc-full-disk-access.md"
---

# KakaoTalk for Mac (App Store 26.x) 데이터·sqlcipher DB 위치

App Store 빌드 KakaoTalk for Mac (`com.kakao.KakaoTalkMac`, TeamID `L75WVXX68A`, 검증 시점 v26.3.0) 의 메시지 본체는 sqlcipher 로 암호화되어 sandbox 의 Application Support 폴더에 저장된다. 평문 SQLite 시그니처가 없고 확장자도 없는 80자 hex 파일명이라 일반적인 `*.db` / `*.sqlite*` 글롭으로는 잡히지 않는다.

## 검증 환경

- App: `/Applications/KakaoTalk.app` (App Store 다운로드)
- Bundle ID: `com.kakao.KakaoTalkMac`
- Version: 26.3.0 (`defaults read /Applications/KakaoTalk.app/Contents/Info CFBundleShortVersionString`)
- TeamID: `L75WVXX68A`
- Container 기준 경로: `~/Library/Containers/com.kakao.KakaoTalkMac/Data/`

## 디렉터리 구조 (요지)

```
~/Library/Containers/com.kakao.KakaoTalkMac/Data/Library/
├── Application Support/
│   ├── com.kakao.KakaoTalkMac/
│   │   ├── 70a275...<80hex>          ← 메시지 본체 (sqlcipher, 약 90MB)
│   │   ├── 70a275...<80hex>-shm
│   │   ├── 70a275...<80hex>-wal      ← WAL 동반 → SQLite WAL 모드
│   │   ├── d6d8e93b...<40hex>/       ← 친구/방/별명 등 추가 sandbox 데이터
│   │   │   └── 0fbe1d19...<40hex>/, 5cabaa42.../ ...
│   │   └── Emoticon/, com.crashlytics/
│   └── Google/Measurement/
│       ├── google-app-measurement.sql           ← 평문 SQLite (분석용)
│       └── google_experimentation_database.sql  ← 평문 SQLite
├── Caches/Cache.db                              ← 평문, 메시지 아님
└── HTTPStorages/, WebKit/, ResourceMonitorThrottler/  ← 평문 부속 DB
```

## 핵심 식별 패턴

### 1) 80자 hex 무확장자 파일 + `-wal` / `-shm` 동반

메시지 본체는 `Application Support/com.kakao.KakaoTalkMac/<80자 hex>` 형태. 확장자가 없고 자체 파일에 `SQLite format 3` 평문 매직이 없으므로 일반 도구로 잡히지 않는다. 식별 단서는:
- 같은 prefix 의 `-shm`, `-wal` 파일 동반 → SQLite WAL 모드
- 큰 파일 크기 (50MB+)
- `-wal` 파일은 평문 SQLite 시그니처를 가짐 (sqlcipher 페이지 헤더는 암호화되지만 WAL 파일은 일부 평문)

### 2) `[wal]` / `[magic]` / `[ext]` 휴리스틱 분류

`kakao-db inspect` 가 채택한 분류 ([[kakao-db]]):

- `[magic]` SQLite 평문 매직 시그니처 (`SQLite format 3`) 16바이트 매칭 → Google Measurement 등 평문 SQLite 가 잡힘
- `[wal  ]` `-wal` / `-shm` 동반 + 자체 파일에는 평문 매직 부재 → **sqlcipher 메시지 DB 후보**
- `[ext  ]` 확장자 매칭 (`.db` / `.sqlite*`) — Cache/HTTPStorages/WebKit 부속 DB

App Store 26.3.0 환경에서 메시지 본체는 `[wal]` 분류에서만 잡히고 8개 후보 중 1개만 해당.

## 다른 위치는?

- `~/Library/Group Containers/`, `~/Library/Application Support/`, `~/Library/Preferences/` 에는 KakaoTalk 메시지 DB 가 보이지 않음 (App Store 빌드 한정).
- `~/Library/Containers/com.kakao.KakaoTalkMac/Data/Library/Caches/Cache.db` 는 카톡 인프라용 캐시이지 메시지 DB 가 아님.

## 운영 시 주의

1. **TCC 권한**: iTerm/Terminal 이 `~/Library/Containers/com.kakao.KakaoTalkMac/` 에 접근하려 하면 macOS 가 매번 토스트 팝업을 띄움. 시스템 설정 → 개인정보 보호와 보안 → 전체 디스크 접근에서 터미널 앱을 ON 하고 **완전 종료 후 재시작** 필요. [[macos-tcc-full-disk-access]]
2. **DB 락 회피**: KakaoTalk 앱 실행 중 메시지 DB 를 직접 open 하면 락·무결성 위험. 작업 위치 (예: `~/.kakao-db/cache/` 또는 `~/Documents/kakao-snapshot/`) 로 3종 세트 (`<hex>`, `-wal`, `-shm`) 를 cp 한 read-only 사본을 사용 권장.
3. **빌드별 차이**: App Store 빌드와 카카오 공식 사이트 다운로드 빌드의 sandbox/스키마가 다를 수 있음. 본 문서는 App Store v26.3.0 기준.
4. **경로의 hex 값은 환경별로 다름**: 80자 prefix `70a275...`, 40자 sub-prefix `d6d8e93b...` 는 사용자/설치별 고유 값. 휴리스틱 (크기 + WAL 동반 + 매직 부재) 으로 탐색해야 한다.

## 키 도출 (sqlcipher PRAGMA `key=`)

이 문서 작성 시점에는 키 자동 추출은 **미해결 spike** 항목. App Store sandbox 빌드는 macOS Keychain / Sandbox Keychain 에 키를 저장할 가능성이 높지만, 아직 검증되지 않음. `kakao-db` 의 M1 단계 결정 (B3) 은 **사용자가 키를 환경변수 또는 macOS Keychain (`security`) 으로 직접 주입**하는 방식이며, 자동 추출은 별도 spike 로 분리.

## 관련 맥락

- [[kakao-db]] — 본 패턴을 활용해 sqlcipher 를 직접 여는 Rust 어댑터
- [[kakao-mem]] — `kakaocli` 의존 Python CLI. App Store 빌드의 메시지 DB 본체는 `kakaocli` 가 잡지 못해 한계가 있음.
- [[macos-tcc-full-disk-access]] — sandbox 침범 시 발생하는 TCC 토스트 팝업 처리

## 변경 이력

- 2026-05-06: 최초 생성 (session-logs/20260505-235703-d859) — App Store v26.3.0 환경 검증, 8 후보 중 메시지 본체 식별 패턴 정리
