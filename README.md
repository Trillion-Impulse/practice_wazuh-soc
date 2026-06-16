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