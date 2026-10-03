---
title: "OCI 운영 치트시트 — Notion 보충 기록"
domain: "ai-agent"
sensitivity: internal
tags: ["pattern", "oracle-cloud", "oci", "ssh", "oci-cli", "automation", "windows"]
created: "2026-10-03"
updated: "2026-10-03"
sources:
  - "raw-sources/2026-10-03-notion-oracle-cloud-free-tier.md"
confidence: medium
related:
  - "wiki/patterns/oracle-cloud-free-tier-setup.md"
  - "wiki/projects/dev-blog.md"
---

# OCI 운영 치트시트 — Notion 보충 기록

기존 [[oracle-cloud-free-tier-setup]]에 없던 Notion 페이지의 가입 입력 팁, 운영 명령어,
Claude Code의 OCI CLI 재현 레시피, Windows SSH 접속 방법을 보존한다.
자동화 호스팅 맥락은 [[dev-blog]]와 연결된다.

> **출처·시점 주의:** 아래는 2026-10-03에 읽은 Notion의 과거 실행 기록이다.
> 리전 선택지·지연시간·서버 IP·설치 상태는 이번 작업에서 재검증하지 않았다.
> 현재 무료 한도와 2026-10-03 디스크 점검은 [[oracle-cloud-free-tier-setup]]을 기준으로 한다.
> 명령어는 문서로만 추가했으며 서버 접속·설치·재부팅·방화벽 변경은 실행하지 않았다.
> API 키 방식도 사용자 키 삭제·권한 변경 등으로 무효화될 수 있다.
> Windows 원문의 `%USERNAME%`는 cmd.exe 문법이다. PowerShell에서는 `$env:USERNAME`을 사용한다.
> 원문의 REJPOS 방화벽 예시는 REJECT 규칙이 존재할 때만 적용한다. 적용 전 규칙을 확인한다.
> 서버 IP와 로컬 경로가 포함되어 `internal`로 분류했으며, 개인키·토큰 값은 포함하지 않는다.

## Notion에서 보충한 내용

### 홈 리전 선택 팁

무료 가입 시 서울/춘천 리전이 목록에 없는 경우가 흔하다. 한국에서 가장 가까운 곳은 일본(Tokyo/Osaka)이며 지연시간 약 30~40ms로 체감 차이가 거의 없다. 싱가포르 70~90ms, 인도는 더 멀다. → 일본 권장. 무료 자원 사양은 어느 리전을 골라도 동일하다.

### 가입 폼 입력 함정 (한국 사용자)

- 휴대폰: 맨 앞 0을 뺀다. 국가번호 +82가 이미 선택돼 있으므로 010-1234-5678 → 1012345678 (하이픈·공백 없이)

- 도시(City): 한글이면 검증 실패("구/군/시에 적합한 이름을 입력하십시오"). 영문 로마자로 입력. 예: 수원시 → Suwon

- 주소 예시 — 경기도 수원시 덕영대로 1410 → Address: 1410 Deogyeong-daero / City: Suwon / State: Gyeonggi-do / Country: South Korea

### 최종 확보 결과

- VM.Standard.A1.Flex / 4 OCPU / 24GB RAM / 디스크 45GB / Ubuntu 24.04 aarch64 / Docker 설치 / 방화벽 22·80·443 (도쿄 ap-tokyo-1)

sudo reboot

---

## 📌 자주 쓰는 명령어 치트시트

> 서버 정보 — 퍼블릭 IP: 158.179.177.164 (도쿄) / 사용자: ubuntu / 개인키: ~/.ssh/oci_vm / 사양: 4 OCPU + 24GB RAM + 45GB 디스크

### 1) 서버 접속 (Mac 터미널에서)

```shell
# 별칭으로 간편 접속 (~/.ssh/config 에 Host oci 등록됨, Mac·Windows 동일)
ssh oci

# 또는 전체 명령 (별칭 없이)
ssh -i ~/.ssh/oci_vm ubuntu@158.179.177.164

# 서버에서 나가기
exit
```

```shell
# ~/.ssh/config 내용 (이 파일 만들면 'ssh oci' 로 접속)
#  - Mac: IdentityFile ~/.ssh/oci_vm
#  - Windows: IdentityFile C:\Users\min58\.ssh\oci_vm_win
Host oci
    HostName 158.179.177.164
    User ubuntu
    IdentityFile ~/.ssh/oci_vm
```

### 2) 서버 상태 확인 (서버 안에서)

```shell
nproc            # CPU 코어 수
free -h          # 메모리 사용량
df -h /          # 디스크 사용량
uptime           # 가동 시간·부하
top              # 실시간 프로세스 (q로 종료)
```

### 3) 재부팅 / 종료

```shell
sudo reboot      # 재부팅 (약 1분 뒤 재접속)
sudo shutdown -h now  # 종료 (콘솔에서 다시 켜야 함)
```

### 4) 패키지 관리

```shell
sudo apt update              # 패키지 목록 갱신
sudo apt upgrade -y          # 설치된 패키지 업그레이드
sudo apt install <이름>    # 설치 (예: sudo apt install python3)
sudo apt remove <이름>     # 제거
```

### 5) Docker 기본

```shell
docker ps                    # 실행 중인 컨테이너
docker ps -a                 # 전체 컨테이너(멈춘 것 포함)
docker images                # 받아둔 이미지 목록

docker run -d -p 80:80 nginx # nginx 웹서버 띄우기 (백그라운드)
docker run -it ubuntu bash   # 컨테이너 안으로 들어가기

docker stop <이름/ID>       # 중지
docker rm <이름/ID>         # 삭제
docker logs <이름/ID>       # 로그 보기
```

### 6) 방화벽에 새 포트 열기 (예: 8080)

> OCI는 2단계 방화벽. 서버 안 iptables 만 열면 안 되고, 콘솔 Security List(Networking → VCN → Security Lists)에도 같은 포트 Ingress 규칙을 추가해야 외부에서 접근된다.

```shell
# 서버 안 iptables (REJECT 규칙 '위'에 삽입)
REJPOS=$(sudo iptables -L INPUT --line-numbers | awk '/REJECT/{print $1;exit}')
sudo iptables -I INPUT "$REJPOS" -m state --state NEW -p tcp --dport 8080 -j ACCEPT
sudo netfilter-persistent save
```

---

## 🤖 Claude가 CLI로 자동화하는 법 (재현 레시피)

> 사용자가 줄 것은 단 2가지: (1) Claude가 생성한 공개키를 OCI 콘솔에 등록, (2) 콘솔이 보여주는 config 미리보기를 붙여넣기. '토큰'이 아니라 OCI API 키 방식 (만료 없음).

### 전체 흐름

1. OCI CLI 설치 (Claude 자동)

```shell
brew install oci-cli
oci --version
```

1. API 키 쌍 생성 (Claude 자동) — 개인키는 Mac에만 저장

```shell
mkdir -p ~/.oci && chmod 700 ~/.oci
openssl genrsa -out ~/.oci/oci_api_key.pem 2048
chmod 600 ~/.oci/oci_api_key.pem
openssl rsa -pubout -in ~/.oci/oci_api_key.pem -out ~/.oci/oci_api_key_public.pem
cat ~/.oci/oci_api_key_public.pem   # 이 공개키를 콘솔에 붙여넣음
```

1. 공개키를 콘솔에 등록 (사용자가 할 일)

- 프로필 아이콘 → My profile → API keys → Add API key → 'Paste public key' → 위 공개키 붙여넣기 → Add

1. config 미리보기를 Claude에게 전달 (사용자가 할 일)

- Add 후 뜼는 'Configuration file preview'(user/fingerprint/tenancy/region) 전체를 복사해 Claude에게 붙여넣기

1. ~/.oci/config 작성 + 인증 테스트 (Claude 자동)

```shell
# config 내용(미리보기) 저장 + key_file 경로 연결
# [DEFAULT]
# user=... / fingerprint=... / tenancy=... / region=ap-tokyo-1
# key_file=~/.oci/oci_api_key.pem
chmod 600 ~/.oci/config
oci iam region list   # 성공하면 인증 완료 (키 등록 직후 1~2분 전파 지연 가능)
```

1. 네트워크·이미지·SSH키 준비 + 재시도 스크립트 구동 (Claude 자동)

- VCN+퍼블릭서브넷+인터넷게이트웨이+라우트+보안목록 생성 → Ubuntu aarch64 이미지 OCID 조회 → VM 접속용 SSH 키 생성 → launch 반복 스크립트 백그라운드 구동

### 파일 위치 (모두 repo 밖 로컬 전용)

```shell
~/.oci/config                 # 인증 설정
~/.oci/oci_api_key.pem         # API 개인키 (절대 공유 금지)
~/.oci/freevm/vars.env         # tenancy/AD/subnet/image/ssh 변수
~/.oci/freevm/retry-launch.sh  # 재시도 스크립트
~/.oci/freevm/retry.log        # 진행 로그
~/.ssh/oci_vm(.pub)            # VM 접속용 SSH 키
```

> 다음에 또 필요하면 Claude에게 "OCI 자동 재시도 세팅해줘" 하고, 위 3~4번(공개키 등록 + config 미리보기 붙여넣기)만 해주면 된다. 나머지는 Claude가 자동으로 처리.

---

## 💻 다른 PC(Windows 등)에서 접속하기

> 명령어 형식은 Windows·Linux도 동일하지만, 그 PC에 개인키 oci_vm 파일이 있어야 접속된다. 개인키는 지금 Mac에만 있음 → 옆 PC로 옮기거나, 새 키를 만들어 등록해야 함.

### 방법 1 — 개인키를 Windows로 복사 (간단)

1. Mac의 ~/.ssh/oci_vm 파일을 Windows PC로 안전하게 이동 (USB 등)

1. Windows의 C:\Users\사용자명\.ssh\oci_vm 위치에 저장

1. Windows Terminal(PowerShell)에서 같은 명령으로 접속

```shell
ssh -i ~/.ssh/oci_vm ubuntu@158.179.177.164
```

> 'UNPROTECTED PRIVATE KEY FILE' / 'bad permissions' 에러 시 — Windows는 chmod가 아니라 icacls로 권한을 조여야 함.

```shell
icacls C:\Users\사용자명\.ssh\oci_vm /inheritance:r /grant:r "%USERNAME%:R"
```

### 방법 2 — Windows에서 새 키 만들어 등록 (더 안전, 권장)

- 개인키를 옮기지 않고, PC마다 별도 키를 만들어 공개키만 서버에 추가 (개인키는 각 PC에만 남음 → 유출 위험 ↓)

```shell
# 1) Windows에서 새 키 생성
ssh-keygen -t ed25519 -f $env:USERPROFILE\.ssh\oci_vm_win

# 2) 생성된 공개키(oci_vm_win.pub) 내용을 복사
type $env:USERPROFILE\.ssh\oci_vm_win.pub

# 3) (기존 Mac에서 접속해) 서버의 authorized_keys에 그 공개키 한 줄 추가
#    ubuntu@서버: echo '<oci_vm_win.pub 내용>' >> ~/.ssh/authorized_keys

# 4) Windows에서 새 키로 접속
ssh -i $env:USERPROFILE\.ssh\oci_vm_win ubuntu@158.179.177.164
```

요약: 명령어는 같지만 그 PC에 개인키가 있어야 한다. 개인키 복사(방법1) 또는 새 키 등록(방법2, 더 안전).

## 변경 이력

- 2026-10-03 [default/맥비]: Notion 전체 115개 블록을 읽고 기존 위키와 비교해 미수록 부분을 추가. 현재 무료 한도·디스크 조사 기록은 기존 정본을 유지. 원본은 raw-sources에 보존.
