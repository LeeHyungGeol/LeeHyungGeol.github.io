---
layout: single
title: "[Network] Internet Network(IP, TCP, UDP, PORT, DNS)"
categories: Network
tag: [Network, Internet, IP, TCP, UDP, PORT, DNS]
toc: true
author_profile: false
search: true
---

## 인터넷 통신

- 클라이언트와 서버가 바로 붙어있으면 바로 데이터 전송
- 인터넷망을 통해 메세지가 클라이언트에서 서버로 통하는 과정이 복잡함 **( 중간 노드를 거쳐 메세지가 발송됨 )**
- 이런 메세지가 안전하게 발송되는것은 `IP(Internet Protocol)` 을 통해 가능

## IP(Internet Protocol) -  Network Layer(OSI 7 Layer)(네트워크 계층)

**인터넷망을 통해 메세지를 전송하기 위한 최소한의 규칙을 IP를 통해 지정**

**인터넷 프로토콜의 역할**
- 지정한 IP 주소(IP Address)에 데이터 전달
- `Packet`이라는 **통신 단위** 로 데이터 전달
  - `IP Packet 구성요소` : **출발지 IP, 목적지 IP, 전송 데이터, 등등..**
- 클라이언트가 서버로 Packet을 인터넷 망에 전송 
- 전송받은 서버는 메세지를 응답받을 경우 클라이언트에게 다시 전달

**IP 프로토콜의 한계**

`비연결성(connectionless)`
- **Packet을 박을 대상이 없거나 서비스 불능 상태여도 Packet 전송**
- EX) 서울에 친구가 있겠지 하고, packet을 보내는 상황. 정작, 그 친구의 컴퓨터가 꺼져 있어도 packet 전송 가능

`비신뢰성(unreliability)`
- 전송 중간에 Packet이 사라지는 경우
- 여러 개의 Packet이 순서대로 안오는 경우 (Packet의 용량이 클 경우, Packet을 여러 개로 끊어 보낸다.)(1500 byte)

`프로그램 구분`
- 같은 IP를 사용하는 서버에서 통신하는 **애플리케이션이 여러개일 경우**
- 같은 PC에서 게임도 하고, 채팅도 하고, 유튜브도 보는 경우

`IP`의 문제를 해결하기 위해 **TCP(Transmission Control Protocol) 사용**

## TCP(Transmission Control Protocol), UDP(User Datagram Protocol)

**Internet Protocol 스택의 4계층(4 Layer)**

| 계층(Layer)) | 대표 Protocol |
|---|---|
| **애플리케이션 계층(Application Layer)** | `HTTP`, `FTP` |
| **전송 계층(Transport Layer)** | `TCP`, `UDP` |
| **인터넷 계층(Internet Layer)** | `IP` |
| **네트워크 인터페이스 계층** | `LAN Driver`, `LAN 카드`, `LAN 장비` 등등.. |

**메세지 전송 순서**

1. `애플리케이션`에서 **메세지** 생성 
2. `SOCKET 라이브러리`를 통해 `OS` 에 메시지 전달
3. `OS`에서 **TCP** 정보를 **메세지**에 씌움
4. `OS`에서 **IP** 정보를 **TCP** 정보에 씌움 -> `IP Packet 생성`
5. `LAN 카드`로 전달, `Ethernet Frame`정보를 씌운 후 나간다. (Ethernet Frame : LAN 카드에 등록된 MAC 주소 포함, 물리적인 정보 포함 등등..)

`Packet = Package(수하물) + Bucket(덩어리) 의 합성어`

## TCP(Transmission Control Protocol)

### TCP Segment 포함 정보

- **출발지 PORT, 목적지 PORT, 전송 제어, 순서, 검증 정보, 전송 데이터** 등등..

### TCP 특징

**신뢰**할 수 있는 프로토콜, 현재는 대부분 TCP를 사용

- `연결지향적` - TCP 3 way handshake **(가상연결)**
  - 1. Client가 먼저 `SYN` 이라는 접속 요청 메시지를 보냄
  - 2. 메시지를 받은 Server 쪽에서도 요청을 수락 하는 `SYN + ACK 메시지`를 함께 Client로 보냄.
  - 3. SYN 메시지를 받은 Client도 다시 `ACK 메시지`를 Server로 보냅니다. **(ACK를 보낼 때 Data도 함께 전송한다.)**
  - **연결(Connection)을 확실히 확인**
    - 이 연결은 **진짜 물리적인 연결은 X. 논리적, 개념적 연결이다.**
    - 인터넷 연결망에 둘 다 살아 있구나! 하는걸 아는 것 이라고 생각하면 될 것 같습니다.

- `데이터 전달 보증` 
  - 1. Client -> Server 데이터 송신
  - 2. Server -> Client 데이터 정상 수신했다고 송신
  - **Packet이 중간에 누락된 경우, Server에서 아무 응답이 없을테니 Client가 누락된 사실을 알 수 있음**
  
- `순서를 보장`
  - Client -> Server : Packet1, Packet2, Packet3 순서로 송신
  - Server : Packet1, Packet3, Packet2 순서로 수신
  - Server -> Client : **Packet2 부터 재전송 요청**
  - 물론, Server에서 최적화 하는 것도 가능하다. 내부적으로 Driver에서 Packet들을 모아두었다가, 1,3,2 라면 1,2,3 순으로 최적화 할 수 있다. 
  - 데이터가 커서 패킷을 쪼개서 보냈을 경우, **전송제어, 순서, 검증 정보들을 확인**하기 때문에 **순서가 보장**됩니다.

## UDP(User Datagram Protocol)

이렇다 할 기능이 없다(하얀 도화지에 비유)

- IP와 거의 동일 **(출발지 IP, 목적지 IP, 전송 데이터, 등등..)**
- **PORT만 추가 (패킷 구분용으로 사용)**
- **ckecksum 추가 (데이터 확인용으로 사용)**
- 단순하고 빠르다.
- **3way handshake가 없고 전송하는 데이터 크기가 작아서 최적화 가능**
- Application Layer에서 추가 작업 필요
- `http3` 에서 최적화 `(Google 의 Quic)` 를 하면서 UDP 프로토콜을 사용함

## PORT

Client PC가 한번에 여러 개의 Server에 연결해야 한다면? `TCP/IP Packet`을 보내는데 어떠한 프로그램에 응답을 보내야 하는지 구분이 필요하다.
- EX) Client PC가 게임도 하고, 화상 통화도 하고, 웹 브라우저 `요청(request, TCP/IP Packet)` 도 Server에게 보내는 상황, 이때 Server는 어떤 프로그램에 적절하게 `응답(response, TCP/IP Packet)`을 보내야 할지 구분해야 한다.

`TCP Segment` 내에는 `출발지 PORT(Source PORT), 목적지 PORT(Destination PORT)`가 있다.

`PORT` : **같은 IP 내에서 Process 구분!!**
- `IP` : 아파트, `PORT` : 몇동 몇호
- `0 ~ 65535` 까지 할당 가능
- `0 ~ 1023` 까지는 잘 알려진 PORT, **사용하지 않는 것이 좋다.**
  - `FTP` : 20,21
  - `TELNET` : 23
  - `HTTP` : **80**
  - `HTTPS` : **443**

## DNS(Domain Name System)

**DNS의 배경**
- **IP는 기억하기 어렵다**
  - EX) IP:`100.100.100.1` , IP:`200.200.200.2`
- **IP는 변경될 수 있다.**
  - EX) IP:200.200.200.2 -> 200.200.200.3
  - 실제로 IP는 자주 변경된다.
- 이렇기 때문에 IP를 알려줄 무엇인가 필요하다!! -> **DNS(Domain Name System)** 

**DNS(Domain Name System)**
- **전화번호부처럼 활용**
- **Domain명을 IP 주소로 변환!!**

EX) DNS Server

| 도메인 명 | IP |
|---|---|
| google.com | 200.200.200.2 |
| aaa.com | 210.210.210.3 |

**DNS 동작**

1. DNS 서버에 도메인 명으로 IP 요청
2. DNS 서버로부터 IP를 응답 받음
3. 해당 IP로 연결
