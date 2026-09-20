# IDS / IPS Custom Ruleset

쇼핑몰 서비스 환경 기반 통합 보안 인프라 프로젝트에서 작성·관리한 **Snort 3 Custom Rule Set**입니다.

공격 유형과 SID 대역을 기준으로 룰을 관리했으며, GitHub에서는 원본 룰을 삭제하지 않고 **100 / 200 / 300 / 400 / 500번대의 5개 파일**로만 단순화했습니다.

> 📑 **[전체 IDS / IPS 룰셋 관리 시트 (Google Sheets)](https://docs.google.com/spreadsheets/d/1kWFIV0rrfKYCkRP-9SwVA2ZwHGIBzbJ3ftCtJ9l05fY/edit?usp=sharing)**

## Repository Structure

```text
.
├── README.md
├── .gitignore
└── rules/
    ├── 100-protocol.rules
    ├── 200-web.rules
    ├── 300-network.rules
    ├── 400-scanning.rules
    └── 500-system.rules
```

## Rule Set

| SID Series | File | Scope | Rules |
|---|---|---|---:|
| `100xxxx` | `100-protocol.rules` | Protocol / Infrastructure | 190 |
| `200xxxx` | `200-web.rules` | Web Application | 39 |
| `300xxxx` | `300-network.rules` | Network Attack | 4 |
| `400xxxx` | `400-scanning.rules` | Scanning / Reconnaissance | 24 |
| `500xxxx` | `500-system.rules` | System / Host Attack | 8 |
| **Total** |  |  | **265** |

### 100xxxx — Protocol / Infrastructure

`FTP`, `HTTP`, `SNMP`, `DNS`, `SSH`, `SMB`, `DHCP`, `Pydio`, `ownCloud` 관련 룰을 포함합니다.

- **190 rules**
- Categories: FTP, HTTP, SNMP-cacti, SNMP-mrtg, DNS, SSH, SMB, DHCP, pydio, owncloud

### 200xxxx — Web Application

로그인, SQL Injection, Command Injection, Stored XSS, Open Redirect, File Upload, Broken Access Control, IDOR, RLO 등 웹 공격 탐지 룰을 포함합니다.

- **39 rules**
- Categories: 로그인 평문, 로그인 실패, 주문 취소  요청, SQL Injectiom, Command Injection, Stored XSS, Redirect External, File Upload, Broken Access Control, IDOR, RLO

### 300xxxx — Network Attack

Brute Force 및 ARP Spoofing 탐지 룰을 포함합니다.

- **4 rules**
- Categories: Brute Force, ARP Spoofing

### 400xxxx — Scanning / Reconnaissance

Nmap 기반 SYN / Connect / FIN / NULL / XMAS / UDP / Version / NSE / ACK-Window / IP Protocol Scan 및 OS Fingerprinting 탐지 룰을 포함합니다.

- **24 rules**
- Categories: TCP SYN, TCP Connect Scan, FIN Scan, TCP NULL, Xmas Scan, UDP Scan, Version Scan, NSE Scan, TCP ACK/Window Scan, IP Protocol Scan, OS Fingerprinting

### 500xxxx — System / Host Attack

Reverse Shell, Shell Command, Sensitive File Access 및 sudo 명령 탐지 룰을 포함합니다.

- **8 rules**
- Categories: Reverse Shell, Shell Command, File Access, sudo, su 명령

## Rule Design

룰 작성 시 단일 문자열 탐지만으로 공격을 확정하기보다 다음 조건을 조합해 탐지 범위를 설계했습니다.

- `flow` / TCP flags를 활용한 통신 방향 및 세션 상태 확인
- `http_uri`, `http_client_body`, `http_header` 등 HTTP buffer 기반 분석
- `content` / `pcre` 기반 payload pattern 탐지
- `detection_filter`를 활용한 반복 접근 및 스캔 탐지
- 공격 시나리오에 따라 IDS 탐지와 IPS 대응 후보를 분리하여 관리

## Environment Variables

일부 룰은 프로젝트 환경에서 사용한 Snort 변수를 참조합니다.

```text
$DHCP_SERVER
$EXTERNAL_NET
$HOME_NET
$HTTP_PORTS
$HTTP_SERVER
$HTTP_SERVERS
$SNMP_AGENTS
$SNMP_MANAGERS
```

실행 환경에 맞게 `snort.lua`에서 네트워크/서버 및 서비스 변수를 정의한 뒤 사용해야 합니다.

## Notes

- 이 저장소의 `.rules` 파일은 **원본 스프레드시트의 Snort 3 룰 본문을 삭제하지 않고 그대로 수록**했습니다.
- 운영 환경 적용 전에는 서비스 구조와 트래픽 특성을 기준으로 오탐/미탐 검증이 필요합니다.
- Web Application 룰셋은 프로젝트에서 구현한 서비스의 URI, 요청 파라미터 및 공격 시나리오를 기반으로 작성되어 해당 서비스 환경에 맞게 조정되어 있습니다.

## Portfolio Context

이 룰셋은 다음 흐름으로 수행한 통합 보안 인프라 프로젝트의 일부입니다.

```text
Attack Scenario
      ↓
Snort IDS / IPS Detection
      ↓
Alert & PCAP Analysis
      ↓
Server / Application Log Correlation
      ↓
Detection Rule & Response Policy Improvement
```
