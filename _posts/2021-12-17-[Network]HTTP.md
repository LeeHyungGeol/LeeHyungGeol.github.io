---
layout: single
title: "[Network] HTTP"
categories: Network
tag: [Network, HTTP]
toc: true
author_profile: false
search: true
---

## HTTP(HyperText Transfer Protocol)

**HTTP Message에 모든 것을 전송한다!!**
- **거의 모든 형태의 데이터 전송 가능**
  - HTML, TEXT
  - image, sound, video, file
- **Server간에 데이터를 주고 받을 때도(Server끼리 통신할 때) 대부분 HTTP 사용** 
  - JSON, XML (API)

### HTTP 역사

1. `HTTP/0.9(1991년)` : GET 메소드만 지원, HTTP 헤더 X

2. `HTTP/1.0(1996년)` : 메서드, 헤더 추가

3. `HTTP/1.1(1997년)` : **가장 많이사용, 대부분의 기능 추가됨**
   - 장점 : `Persistent Connection(keep-alive connection)`
   - 단점 : `pipelining` -> `Head Of Line(HOL) Blocking` 문제점 발생  
   - RFC2068(1997년) -> RFC2616(1999년) -> RFC7230~7235(2014년)에 개정

4. `HTTP/2(2015년)` 
   - 성능 개선 : `Binary Framing`, `Frame, Message, Stream`, `Multiplexing`, `Header Compression`, `Server Push`
   - 단점 : **TCP**의 `Head Of Line(HOL) Blocking` **문제점**

5. `HTTP/3(진행중)` 
   - TCP 대신 UDP 사용, 성능 개선
   - `Google의 Quic`

**TCP** : HTTP/1.1, HTTP/2.0

**UDP** : HTTP/3

**현재 HTTP/1.1 주로 사용**
- HTTP/2, HTTP/3도 점점 증가

Chrome 개발자 도구(F12) -> Network -> Name의 마우스 우클릭 -> Protocol : HTTP 버전 확인가능

### HTTP 특징
  
- Client-Server 구조(Request, Response 구조)
- 무상태성(stateless), 비연결성(connectionless)
- HTTP Message
- 단순함, 확장가능

#### Client-Server 구조(Request, Response 구조)

**Request, Response 구조**
- Client는 Server에 Request를 보내고, Response를 대기
- Server가 Request에 대한 결과를 만들어서 Response

Client-Server 구조의 장점
- **Client와 Server가 각각 독립적으로 발전할 수 있다!!**
- Client는 단순하게 UI/UX 에만 집중할 수 있다.
- Server는 UI/UX 는 상관없이 비즈니스 로직, 데이터 처리에만 집중할 수 있다.

#### 무상태성(stateless)

`무상태성(stateless)` : **연결(Connection)을 끊는 순간 Client와 Server의 통신이 끝나며 상태(State) 정보는 유지하지 않는 특성**

Server가 Client의 상태(state)를 기억하지 않는다.
- 장점:
  - **Client(고객)가 요청(Request)할 때, 필요한 데이터를 다 담아서 메시지를 보낸다.** 
  - 중간에 다른 Server(점원)로 바뀌어도 된다. 
  - 갑자기 Client(고객)의 요청(Request)이 증가해도 Server(점원)를 대거 투입할 수 있다.
    - 무상태는 응답 서버를 쉽게 바꿀 수 있다.
      - **무한한 서버 증설 가능!!**
      - **scale-out == 수평 확장 가능**
- 단점: 
  - **모든 것을 무상태로 설계 할 수 있는 경우도 있고 없는 경우도 있다!!**
  - 할 수 있는 경우 : EX) 단순한 소개 page
  - 할 수 없는 경우 : EX) `Login` - 로그인한 사용자의 경우, login 했다는 상태(state)를 유지 
    - 일반적으로 `cookie, session` 사용
    - **상태 유지는 최소한만 사용**
  - **Client가 요청 메시지를 보낼 때, 전송할 때 data를 많이 보내야 할 수 있다.**


#### 비연결성(connectionless) 

`비연결성(connectionless)`  : HTTP는 클라이언트가 요청(Request)을 서버에 보내고, 서버는 클라이언트에게 적절한 응답(Response)을 주고, **연결(Connection)을 끊는 특성**이 있습니다.
- 일반적으로 초 단위 이하의 빠른 속도로 응답한다.
- 서버 자원을 매우 효율적으로 사용할 수 있다.

**한계와 극복**

한계:
- TCP/IP 연결을 매번 새로 맺어줘야 한다 - **3 Way Handshaking 시간 추가**
- Web Browser로 사이트를 요청하면 HTML 뿐만 아니라 js, css, image등 수 많은 자원이 함께 다운로드 되는데, 이것을 매번 새롭게 요청할 수 없다.
  
극복:
- Header에 `keep-alive` 라는 값을 줘서 **Connection을 재활용**하는데 `HTTP 1.1` 에서는 이것이 `default`입니다. `(persistent connection)`
  - EX) 사이트를 요청할 때, Client와 Server가 연결을 맺은 후에 HTML, js, css, image등을 응답받을 동안 **계속 연결이 지속된다.**
    - 내부적으로 차이가 있을 수 있지만, 보통 몇십초동안 연결을 유지하기 때문에 웬만한 HTML page를 하나 응답받을 동안 연결이 지속된다.
  - HTTP 1.0 은 각각의 응답마다 계속 새로운 연결을 해줘야 했다. 
- HTTP가 TCP 위에서 구현되었기 때문에 (HTTP 2 까지, HTTP 3는 Quic(UDP 기반)) 네트워크 관점에서 `keep-alive`는 옵션으로 `connectionless`의 연결비용을 줄이는 것을 장점으로 **비연결성**이라 합니다. 

#### HTTP 메시지



