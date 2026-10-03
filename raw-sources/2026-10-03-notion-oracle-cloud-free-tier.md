---
source_url: "https://app.notion.com/p/Oracle-Cloud-394ad7c2dd0a81e494accb1f44744f32?source=copy_link"
ingested: "2026-10-03"
sha256: f64e3a8316ecf8d9d2458098e329078a9948ce5579f134deb0e23158d6a5431c
source_title: "Oracle Cloud 무료 계정 — 가입·설치 가이드"
source_blocks: 115
sensitivity: internal
---
> 핵심: Oracle Cloud 무료 계정으로 받는 것은 macOS PC나 물리 베어메탈이 아니라, 클라우드에 떠 있는 리눅스 가상 서버(VM)다. SSH로 접속해 실제 PC(서버)처럼 쓸 수 있고, ARM 4코어 / RAM 24GB / 디스크 200GB를 평생 무료로 쓴다.

## 1. 무료 계정의 구조 — 두 묶음

- Always Free: 카드 등록만 하면 영구 무료로 쓰는 자원 (무기한)

- Free Trial 크레딧: 가입 시 $300 상당 크레딧, 유료 자원 체험용 (약 30일)

30일이 지나거나 크레딧을 다 써도 Always Free 자원은 계속 유지된다. 베어메탈 등 고급 자원은 크레딧으로만 잠깐 쓸 수 있고 영구 무료는 아니다.

## 2. Always Free 주요 자원

- ARM Ampere A1 컴퓨트: 총 4 OCPU + 24GB RAM (핵심 자원)

- AMD x86 마이크로 VM: 1/8 OCPU + 1GB RAM, 최대 2대

- 블록 스토리지(디스크): 총 200GB — RAM 24GB와는 별개

- 오브젝트 스토리지 약 20GB, Autonomous Database 2개, 로드밸런서 1개, 아웃바운드 전송 10TB/월

## 3. 오해 바로잡기

- ✅ IaaS 맞음 — VM을 받아 OS(Ubuntu 등)를 직접 설치·관리. DB 전용 계정이 절대 아님

- ❌ 베어메탈 아님 — Always Free는 가상화된 VM. 베어메탈은 유료/크레딧 전용

- ❌ macOS PC 아님 — OCI는 macOS 인스턴스를 제공하지 않음 (맥이 필요하면 AWS EC2 Mac, MacStadium 등 별도 유료)

- 기본은 CLI만 제공되지만, VM에 ubuntu-desktop + VNC/RDP를 설치하면 그래픽 데스크탑처럼도 사용 가능 (RAM 24GB면 충분)

## 4. 가입 절차

1. oracle.com/cloud/free 접속 → "Start for free"

1. 이메일·국가 입력, 휴대폰 SMS 인증

1. 신용/체크카드 등록(필수) — 본인 확인 + 자동 과금 방지용. $0~1 가승인 후 취소됨(실제 청구 아님)

1. 홈 리전(Region) 선택 — 이후 변경 불가

1. 가입 완료 → 콘솔(Console) 진입

### 홈 리전 선택 팁

무료 가입 시 서울/춘천 리전이 목록에 없는 경우가 흔하다. 한국에서 가장 가까운 곳은 일본(Tokyo/Osaka)이며 지연시간 약 30~40ms로 체감 차이가 거의 없다. 싱가포르 70~90ms, 인도는 더 멀다. → 일본 권장. 무료 자원 사양은 어느 리전을 골라도 동일하다.

### 가입 폼 입력 함정 (한국 사용자)

- 휴대폰: 맨 앞 0을 뺀다. 국가번호 +82가 이미 선택돼 있으므로 010-1234-5678 → 1012345678 (하이픈·공백 없이)

- 도시(City): 한글이면 검증 실패("구/군/시에 적합한 이름을 입력하십시오"). 영문 로마자로 입력. 예: 수원시 → Suwon

- 주소 예시 — 경기도 수원시 덕영대로 1410 → Address: 1410 Deogyeong-daero / City: Suwon / State: Gyeonggi-do / Country: South Korea

## 5. VM(서버) 만들기

1. 콘솔 → Compute → Instances → Create Instance

1. Image: Canonical Ubuntu 24.04

1. Shape: VM.Standard.A1.Flex → 슬라이더로 4 OCPU / 24GB (무료 최대)

1. Networking: 새 VCN 자동 생성 + 퍼블릭 IP 할당 체크

1. SSH 키 생성 후 공개키 업로드, Create → Public IP 확인

```shell
ssh-keygen -t ed25519 -f ~/.ssh/oci_key
# ~/.ssh/oci_key.pub 내용을 콘솔에 붙여넣기
```

## 6. 접속 및 초기 설치

```shell
# SSH 접속 (Ubuntu 기본 사용자명: ubuntu)
ssh -i ~/.ssh/oci_key ubuntu@<PUBLIC_IP>

# 패키지 업데이트
sudo apt update && sudo apt upgrade -y

# 방화벽 — 웹 포트 예시(80)
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
sudo netfilter-persistent save
```

> 함정 두 개 — (1) ARM 인스턴스 재고 부족: 'Out of capacity'가 자주 뜬다. 다른 리전 선택 또는 시간대 바꿔 재시도. (2) 2단계 방화벽: 포트를 열었는데 접속 안 되면, 콘솔의 Security List(NSG)와 인스턴스 내부 iptables 중 한쪽만 열린 것. 반드시 양쪽 모두 열어야 한다.

---

수치는 2026-01 기준. 가입 직전 oracle.com/cloud/free 공식 표로 재확인 권장.

---

## 🔥 실전 기록 — 도쿄 재고 품귀와 PAYG 해결 (2026-07)

> 핵심 결론: 인기 리전(도쿄·서울)의 무료 ARM(A1.Flex)은 Always Free 재고 풀이 만성 품귀다. Pay As You Go(PAYG) 전환이 사실상 유일한 현실적 해법이다.

### 겉은 흐름

- 도쿄(AD 1개) 가입 → 콘솔·CLI 모두 A1.Flex 생성 시 "Out of capacity" 반복

- 자동 재시도 스크립트: 4 OCPU로 480회(12시간) 실패 → 1 OCPU로 낮춰도 480회 실패 → 며칠간 못 잡음

- PAYG(종량제) 업그레이드 후 재시도 → 841번째에 즉시 확보 성공

### PAYG가 정답인 이유

- 무료 계정은 'Always Free 전용' 좁은 재고 풀만 사용 (인기 리전은 상시 고갈). PAYG는 훨씬 큰 유료 재고 풀에 접근

- Always Free 한도(ARM 4 OCPU/24GB, 디스크 200GB)는 PAYG 후에도 그대로 무료 → 한도 안이면 청구 0원

- 덤으로 유휴 회수도 사라짐 (무료 계정은 7일간 사용률 20% 미만 시 인스턴스 회수 대상)

### PAYG 업그레이드 함정

- Upgrade 버튼 비활성 = 'Add a payment method to upgrade' → 결제 수단(신용카드) 미등록이 원인. Billing & Cost Management → Payment Method 에서 카드 등록

- 가입 시 카드 확인(가승인)과 청구용 결제 수단 등록은 별개. 개인은 Tax registration number 공란. 업그레이드 처리는 수십분~수시간 소요

### 자동 재시도 스크립트 요점 (OCI CLI)

- 인증은 API 키 방식(~/.oci/config). 세션 토큰과 달리 만료 없어 장시간 재시도에 적합

- 재시도 판정을 화이트리스트(인증·파라미터 같은 치명 오류만 중단)로 짜라. capacity 외에도 429 TooManyRequests, 네트워크 timeout, 5xx가 수시로 섯임

- 간격 90~120초 권장(너무 초밀하면 429 유발)

### iptables 규칙 순서 함정

- OCI Ubuntu는 INPUT 체인 끝에 REJECT 규칙이 있다. 80/443 ACCEPT를 REJECT '위'에 넣어야 함. 위치 고정(-I INPUT 6)하면 체인이 짧을 때 REJECT 뒤에 삽입돼 무력화됨

### 최종 확보 결과

- VM.Standard.A1.Flex / 4 OCPU / 24GB RAM / 디스크 45GB / Ubuntu 24.04 aarch64 / Docker 설치 / 방화벽 22·80·443 (도쿄 ap-tokyo-1)

[paragraph]

[paragraph]

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

[paragraph]
