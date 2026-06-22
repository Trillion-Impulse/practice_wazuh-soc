# practice_wazuh-soc

## 1. 프로젝트 개요
- 이 프로젝트는 Wazuh를 활용한 SOC(Security Operations Center) 프로젝트입니다.
- 공격 시나리오를 재현하여 보안관제 업무 흐름을 경험하고 학습하기 위함입니다.

## 2. 프로젝트 목표
- Linux 환경 구축 및 운영 경험 습득
- Wazuh 설치 및 운영 경험 습득
- SSH 로그인 실패 이벤트 탐지
- SSH Brute Force 공격 탐지
- Linux 인증 로그(auth.log) 분석
- 보안 이벤트(Alert) 분석 경험 습득
- SOC 업무 흐름 이해

## 3. 프로젝트 환경

### 시스템 환경
- Host OS: Windows 11
- Virtualization: VirtualBox
- Attack VM: Ubuntu Server 22.04 LTS
- Target VM: Ubuntu Server 22.04 LTS
- Wazuh VM: Ubuntu Server 22.04 LTS

### 보안 도구
- SIEM: Wazuh (Manager + Agent + Dashboard)

### 로그 소스
- Log Source: Linux auth.log

### 네트워크
- Host-Only Network
- NAT


## 4. 프로젝트 구조
- 구조
    ```
    practice_wazuh-soc
    ├── README.md
    ├── architecture/
    ├── screenshots/
    ├── rules/
    ├── logs/
    └── report/
    ```

## 5. 아키텍처
- 아키텍처
    ```
    ┌─────────────────────┐
    │ Attack VM           │
    │ Ubuntu Server       │
    └─────────┬───────────┘ 
              │ SSH 
              ▼ 
    ┌─────────────────────┐ 
    │ Target VM           │ 
    │ Ubuntu Server       │ 
    │ auth.log            │ 
    │ Wazuh Agent         │ 
    └─────────┬───────────┘ 
              │ 
              │ Log Forwarding ▼ 
    ┌─────────────────────┐ 
    │ Wazuh VM            │ 
    │ Wazuh Manager       │ 
    │ Wazuh Dashboard     │ 
    └─────────────────────┘
    ```
    - 로그 흐름
        1. Attack VM에서 SSH 접속 시도
        1. Target VM에서 auth.log 생성
        1. Wazuh Agent가 로그 수집
        1. Wazuh Manager가 로그 분석
        1. Wazuh Dashboard에서 Alert 확인 

## 6. 공격 시나리오
- SSH Login Failure 탐지
    - 목표
        - SSH 로그인 실패 이벤트 탐지
        - auth.log 분석
        - Wazuh Alert 확인
- SSH Brute Force 탐지
    - 목표
        - 반복적인 로그인 실패로 Brute Force 공격 탐지

## 7. 진행 현황
- [x] 문서 스켈레톤 작성
- [ ] VirtualBox 설치
- [ ] Attack VM 구축
- [ ] Target VM 구축
- [ ] Wazuh VM 구축
- [ ] SSH 구성
- [ ] Wazuh 설치
- [ ] SSH Login Failure 탐지
- [ ] SSH Brute Force 탐지
- [ ] 탐지 결과 정리

## 8. 탐지 결과

## 9. 관련 지식

### VM 생성
- 하드웨어 메모리, CPU 개수를 정하는 기준
    - VM은 실제 PC 자원을 나눠 쓰는 것이라서 적절히 배분해야 함
    - RAM = 작업 공간
    - CPU = 계산하는 사람
    - VM이 하는 일에 따라 유동적으로 자원 배분
- 가상 하드디스크(Virtual Hard Disk)
    - VM 내부에 들어가는 가상의 하드디스크
    - 운영체제 파일, 로그 파일, Wazuh 뎅이터, 설정 파일 등을 저장하기 위한 공간
    - 디스크 타입
        - VDI (VirtualBox Disk Image)
            - VirtualBox 전용, 안정적, 관리 쉬움
    - 동적 할당
        - 최대 크기만 지정 후 사용량에 따라 변동
    - 고정 크기
        - 고정된 크기를 사용하지 않은 공간까지 모두 할당
            - 공간 낭비

### VM 설정
- VM 실행 중에는 일부 설정 UI를 사용할 수 없는 경우 존재
    - VM 종료 후 설정

### Ubuntu profile configuration
- Your name
    - 사용자 실명 또는 별칭
    - Ubuntu 내부에서는 주로 설명용으로 사용
    - 터미널 작업이나 서버 운영에는 거의 영향 없음
- Your server's name
    - Hostname이라고 부름
    - 서버의 이름
    - 로그인 후 터미널에 `username@hostname:~$` 이렇게 나옴
    - 네트워크에서도 사용됨
- Pick a username
    - 실제 로그인 계정
    - SSH 접속 시에도 사용
    - sudo 사용 시에도 사용
    - ID의 역할

## 10. 명령어

### ip
- Linux의 네트워크 관리 명령어
- 네트워크 인터페이스, IP 주소, 라우팅, ARP 등을 관리하는 명령어
- 기본 형태: `ip [객체] [동작]`
- `ip a` = `ip address`
    - 현재 IP 주소
    - 네트워크 인터페이스 이름
    - MAC 주소
    - 인터페이스 활성화 상태(UP/DOWN)
    - 명령어 사용시 나오는 정보
        - lo
            - Loopback 인터페이스
            - 자기 자신을 의미
            - localhost
        - enp0s3
            - 네트워크 카드 이름
            - 첫 번째 랜카드
        - enp0s8
            - 두 번째 랜카드
        - inet
            - 현재 IP 주소
            - `inet 10.0.2.15/24` 여기서 `10.0.2.15`가 실제 IP
        - UP
            - 네트워크가 활성화된 상태
        - DOWN
            - 네트워크 연결 안 됨
        - NAT
            - VirtualBox 기본값
            - 인터넷 사용 가능
            - 외부에서 VM 접속 어려움
        - Host-Only
            - 내 PC ↔ VM 통신 가능
            - 인터넷 사용 불가
        - Bridged
            - 실제 네트워크에 참여
            - 다른 PC에서도 접근 가능
- `ip link`: 네트워크 인터페이스 확인
- `ip r`: 라우팅 테이블 확인
- `ip neigh`: ARP 테이블 확인 
- `ip addr show`: IP 정보 상세 조회