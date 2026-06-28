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
- [x] VirtualBox 설치
- [x] Attack VM 구축
- [ ] Target VM 구축
- [ ] Wazuh VM 구축
- [ ] SSH 구성
- [ ] Wazuh 설치
- [ ] SSH Login Failure 탐지
- [ ] SSH Brute Force 탐지
- [ ] 탐지 결과 정리

## 8. 탐지 결과

## 9. 트러블 슈팅

### 이슈: Host-Only 인터페이스에 IPv4가 자동 할당 되지 않음
- 해결 과정
    - Netplan 설정 확인
        - `sudo cat /etc/netplan/*.yaml` 확인
        - enp0s8이 DHCP 관리대상에 추가되어 있지 않는 것을 발견
    - enp0s8: dhcp4: true 추가하는 방법으로 해결 시도
    - 재부팅 후 동일한 문제 발견
    - `ls -l /etc/netplan` 확인
        - 50-cloud-init.yaml 파일 발견
        - 부팅 시 cloud-init에 의해 다시 덮어쓰기 되는 것으로 추정
    - cloud-init을 끄기 | 기존의 cloud-init 파일 제거 | 새로운 netplan 파일 생성으로 해결 시도
    - 부팅 중 멈추는 문제 발생
    - vm 삭제 후 새로 생성 | Ubuntu 설치 전 미리 네트워크 어댑터 NAT, Host-Only 적용 설정으로 해결

## 10. 관련 지식

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

### Wazuh
- 서버, PC, 클라우드 등에서 발생하는 보안 위협을 탐지하고 관리하는 오픈소스 보안 플랫폼
- 회사 내부의 컴퓨터와 서버를 24시간 감시하면서 이상한 행동이 있는지 알려주는 보안 관제 시스템
- Wazuh가 수집하는 정보
    - Wazuh는 각 서버나 PC에 Agent(에이전트) 라는 작은 프로그램을 설치
    - 에이전트는 아래와 같은 정보를 수집
        - 로그(Log)
        - 파일 변경
        - 취약점 정보
        - 악성 행위 탐지
- Wazuh의 역할
    - 로그 수집
    - 위협 탐지
    - 경고 생성
    - 규정 준수
    - 취약점 관리
    - 파일 무결성
- SOC 분석가가 공격 흔적을 발견할 수 있도록 데이터를 수집하고 경고를 발생시키는 플랫폼
- Agent
    - 로그를 수집하는 프로그램
    - Target 서버 안에 설치됨
    - ex: auth.log를 읽어서 로그인 실패 발생 등의 메세지를 Manager에게 보냄
- Manager
    - 로그를 분석하는 프로그램
    - ex: Failed password 라는 로그를 받으면, 규칙(Rule)과 비교해서 SSH 로그인 실패 라고 판단
- Dashboard
    - 웹 브라우저에서 보는 화면
    - ex: Chrome에서 `https://Wazuh-IP`로 접속하면 Alert, Events, Agent, Inventory 등을 볼 수 있음

### 부팅 시 네트워크의 동작
- 순서
    ```
    1. 부팅 시작
    2. cloud-init 실행 (초기 설정 단계)
    3. netplan 설정 읽음
    4. systemd-networkd 또는 NetworkManager 실행
    5. IP 할당 (DHCP or static)
    6. 네트워크 활성화
    ```
- cloud-init 이란?
    - 처음 VM 켤 때 자동 설정해주는 초기화 엔진
    - 하는 일
        - hostname 설정
        - SSH key 생성
        - 사용자 생성
        - 네트워크 설정 생성
        - package 설치
    - 클라우드 환경용 자동 초기화 도구
- netplan 이란?
    - Ubuntu에서 네트워크 설정을 최종적으로 적용하는 도구
    - 역할
        - netplan은 실제로 네트워크를 직접 만들지 않음
            ```
            YAML 설정 읽기
                ↓
            systemd-networkd 또는 NetworkManager에게 전달
                ↓
            실제 네트워크 구성
            ```
    - 설정 파일 위치
        - `/etc/netplan/`
- cloud-init과 netplan의 관계
    - cloud-init은 netplan 설정 파일을 자동으로 만들어주는 역할
    - 실제 흐름
        ```
        cloud-init 실행
             ↓
        네트워크 정보 확인
             ↓
        netplan YAML 생성
             ↓
        /etc/netplan/50-cloud-init.yaml 생성
             ↓
        netplan 실행
             ↓
        IP 설정 완료
        ```
        - `50-cloud-init.yaml`의 의미
            - 50: 우선순위
            - cloud-init: 자동 생성 주체
            - .yaml: 설정 파일
            - Ubuntu/cloud-init에서 관례적으로
                - 00~10: 초기 설치 / 기본 설정
                - 50: cloud-init 자동 설정
                - 99: 사용자가 직접 설정 (override용)
                - 숫자가 작을 수록 먼저 적용 (우선순위 낮음)
                    - 큰 숫자가 작은 숫자를 override (큰 숫자가 우선순위 높음)

## 10. 명령어

### sudo
- SuperUser DO
- 다른 사용자(보통 관리자 권한인 root)로 명령을 실행하는 도구
    - root란?
        - Linux의 최고 관리자 계정
- 왜 sudo를 쓰는가?
    - 예전에는 관리자 작업을 하려면 root로 직접 로그인
    - 하지만 이 방식은
        - root 비밀번호를 여러 사람이 알아야 함
        - 누가 어떤 작업을 했는지 추적하기 어려움

### whoami
- 현재 로그인한 사용자의 이름을 출력하는 명령어

### hostnamectl
- 컴퓨터의 이름(Hostname)을 확인하고 변경하는 명령어
- Hostname(호스트 이름)은 컴퓨터를 구분하기 위한 이름
- 컴퓨터 이름뿐 아니라 시스템 정보도 함께 보여주고, 이름도 변경할 수 있음
- 기본 구조: `hostnamectl [옵션] [서브커맨드]`
- 시스템 정보 보기: `hostnamectl`
- 옵션
    - `--static`: 고정(static) 호스트 이름을 출력하거나 설정
        - Static Hostname이란?
            - 컴퓨터에 저장되어 있는 기본 이름
    - `--transient`: 일시적인 호스트 이름을 출력하거나 설정
        - Transient Hostname이란?
            - 네트워크에서 자동으로 받은 임시 이름
    - `--pretty`: 사람이 보기 좋은 이름을 출력하거나 설정
    - `--version`: 버전 확인
- 서브 커맨드
    - `status`: 시스템 정보를 출력
    - `set-hostname`: 컴퓨터 이름 변경
        - `sudo hostnamectl set-hostname [변경할 이름]`

### pwd
- Print Working Directory
- 현재 내가 있는 폴더(디렉터리)의 위치를 출력하는 명령어

### cd
- Change Directory
- 현재 작업 중인 디렉터리(폴더)를 다른 디렉터리로 이동하는 명령어
- 현재 작업하는 폴더를 다른 폴더로 변경하는 명령어
- 상대경로를 이동
- `cd 또는 cd ~`: 홈 디렉터리로 이동
- `cd ..`: 부모 디렉터리로 이동
- `cd .`: 현재 디렉터리로 이동
- `cd -`: 이전 위치로 이동
- `cd /`: 루트 디렉터리로 이동

### ls
- list
- 현재 디렉터리(폴더)에 있는 파일과 디렉터리 목록을 보여주는 명령어
- `ls [옵션] [디렉터리]`: 현재 위치를 이동하지 않고도 다른 디렉터리의 내용을 볼 수 있음
- 옵션
    - `-l`: Long Format, 기본 ls보다 훨씬 많은 정보를 보여줌
    - `-a`: All, 숨김 파일까지 모두 표시
        - 숨김 파일이란?
            - Linux에서는 이름이 .(점)으로 시작하는 파일을 숨김 파일(Hidden File)이라고 함
    - `-h`: Human Readable, 사람이 읽기 쉬운 크기로 표시
        - 보통 -l과 함께 사용
        - `ls -lh`
    - `-R`: Recursive, 하위 디렉터리까지 모두 출력
    - `-t`: Time, 수정 시간이 최신인 순서대로 정렬
    - `-r`: Reverse, 정렬 순서를 반대로 함
    - `-S`: Size, 파일 크기 순으로 정렬
    - `-d`: 디렉터리 자체의 정보를 출력
        - 디렉터리 안의 내용이 아니라 Documents 디렉터리 자체의 정보를 출력
    - `--color`: 색상을 사용하여 출력
        - 파란색 → 디렉터리
        - 초록색 → 실행 파일
    - 옵션 조합 가능

### grep
- 텍스트에서 원하는 내용을 찾아주는 검색 도구
- 파일 전체나 명령어의 결과까지 검색 가능
- 기본 구조: `grep [옵션] [검색할 내용 or 파일이름]`
- 줄(Line) 단위로 동작
    - 검색할 내용이 포함된 line 전체를 출력
- 옵션
    - `-i`: 대소문자 무시
    - `-n`: 줄 번호 표시
    - `-v`: 반대 결과, 포함하지 않는 줄을 출력
    - `-c`: 개수만 출력
    - `-I`: 파일 이름만 출력
    - `-r`: 하위 폴더까지 검색
    - `-w`: 단어만 정확히 검색
    - `-x`: 줄 전체가 일치
    - `-o`: 찾은 부분만 출력
    - `--color`: 검색된 부분을 색으로 표시
    - 여러 옵션 함께 사용 가능
    - 정규 표현식 사용 가능
    - 파이프(|)와 함께 사용
        - ls와 함께 사용
        - ps와 함께 사용
        - 여러 명령어들과 함께 사용

### ps
- 현재 컴퓨터에서 실행 중인 프로그램(프로세스)의 목록을 보여주는 명령어
- 프로세스(Process)란?
    - 프로그램은 단순한 파일
    - 프로그램을 실행하면 메모리에 올라가면서 프로세스가 됨
- 기본 구조: `ps [옵션]`
- 옵션
    - `-e`: 모든 프로세스 출력
    - `-f`: 자세한 정보 출력
    - `-u`: 사용자 중심 출력, CPU,메모리 사용량 등이 함께 표시
    - `-l`: 긴 형식, 우선순위, 상태 등 더 많은 정보 표시
    - `-x`: 터미널이 없는 프로세스도 표시
    - `-a`: 다른 사용자의 터미널 프로세스도 표시
- `ps`의 결과 설명
    | 항목 | 의미 |
    |-----|-----|
    |PID	| 프로세스 번호 |
    |TTY	| 어느 터미널에서 실행되었는지 |
    |TIME | CPU 사용 시간 |
    |CMD	| 실행한 프로그램 |

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

### ping
- 내 컴퓨터와 다른 컴퓨터(또는 서버)가 서로 통신할 수 있는지 확인하는 명령어
- 기본 구조: `ping [옵션] [도메인 or IP]`
- 옵션
    - `-c`: count, 보낼 횟수를 지정, n번의 ping을 보내고 종료
    - `-i`: ping을 보내는 간격(초)을 지정, default는 1초 마다
    - `-W`: 응답을 기다리는 시간을 지정, n초 동안 기다렸다가 응답이 없으면 실패
    - `-4`: IPv4만 사용
    - `-6`: IPv6만 사용
    - 옵션 여러개 함께 사용 가능

### systemctl
- systemd를 제어하는 명령어
- systemd는 Linux의 시스템 관리자
    - 컴퓨터가 부팅되면 systemd가 가장 먼저 여러 작업을 수행
        - 서비스 시작
        - 네트워크 활성화
        - 로그 관리
        - 사용자 로그인 준비
        - 서비스 상태 감시
- systemctl은 systemd에게 명령을 내리는 도구
- 서비스(Service)란?
    - Linux에서 서비스는 백그라운드에서 계속 동작하는 프로그램
    - SSH 서버, 웹 서버, 데이터베이스 서버, Docker 등
- systemctl은 이런 서비스들을 관리
- 기본 구조
    ```
    systemctl [서브커맨드] [대상]
    ```
    - ex: `systemctl status ssh`
- 서브커맨드
    - status: 상태 확인
    - start: 서비스 시작
    - stop: 서비스 중지
    - restart: 재시작
    - reload: 설정만 재적용하기
    - is-active: 실행 중인지 확인
    - is-enabled: 자동 시작 여부 확인

### cat
- concatenate
- 파일의 내용을 한 번에 모두 출력하는 명령어
- 여러 파일을 이어 붙이는(concatenate) 기능도 있지만, 실제로는 대부분 파일 내용을 출력하는 용도로 많이 사용
- 기본 구조: `cat [옵션] 파일명`
- 옵션
    - `-n`: 줄 번호 출력
    - `-b`: 내용이 있는 줄만 번호 출력
    - `-E`: 줄 끝 
    - `-T`: 탭(Tab)을 표시 (탭 문자: `^I`)
    - `-A`: 모든 특수문자를 표시
    - `-s`: 연속된 빈 줄을 하나로 합침
- 여러가지 사용법
    - `cat [파일1] [파일2]`: 여러 파일을 이어 붙여 출력
    - `cat [파일1] [파일2] > [파일3]`: 파일 합치기, 파일1과 파일2의 내용이 이어 붙여져 파일3에 저장
    - `cat > [파일]`: 새 파일 만들기
    - `cat >> [파일]`: 파일 이어쓰기, 기존파일 뒤에 내용을 추가
- 파일의 줄수가 많으면 전부 한 번에 출력되므로 화면 밖으로 밀려난 줄들을 읽을 수 없음
    - 그래서 less가 등장

### tail
- 파일의 끝부분(tail)을 출력하는 명령어
- 기본적으로 파일의 마지막 10줄을 보여줌
- 기본 구조
    ```
    tail [옵션] [파일명]
    ```
    - ex: `tail -f /var/log/auth.log`
- 옵션
    - `-n`: 출력할 줄 수 지정
    - `-f`: 실시간 모니터링
    - `-F`: 로그 로테이션이 발생해도 계속 추적
    - `-c`: 줄 수가 아니라 바이트 기준 출력

### journalctl
- systemd가 수집한 로그 데이터베이스(journal)를 조회하는 명령어
- 기본 구조
    ```
    journalctl [옵션]
    ```
    - ex: `journalctl` 전체 로그 보기
- 옵션
    - `-u`: 특정 서비스(unit) 로그 보기
    - `-f`: 실시간 모니터링
    - `-n`: 최근 n줄만 출력
    - `-u + -n + -f`: 최근 n줄 출력 + 새 로그 실시간 출력
        - `journalctl -u ssh -n 50 -f`
    - `--since`: 특정 시점 이후
    - `until`: 특정 시점 까지
        - `journalctl --since "09:00" --until "10:00"`
    - `-b`: 부팅 로그
    - `-p`: 심각도 필터
    - `-xe`: 문제 분석용