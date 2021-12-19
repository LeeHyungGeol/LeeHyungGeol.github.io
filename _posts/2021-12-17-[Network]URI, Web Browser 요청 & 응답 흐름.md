---
layout: single
title: "[Network] URI, Web Browser 요청 & 응답 흐름"
categories: Network
tag: [Network, URI, URL, URN, Protocol, Http Request Message, Http Response Message, Socket Library]
toc: true
author_profile: false
search: true
---

## URI(Uniform Resource Identifier)

`URI`
- 자원(Resource)을 식별(Identify)하는 통합된(Uniform) 방법
- URI는 `L`ocator, `N`ame 또는 둘 다 추가로 분류될 수 있다.
- `URL(Uniform Resource Locator)` , `URN(Uniform Resource Name)` 은 URI에 **포함되는 관계**이다.
  - `Uniform` : 자원(Resource)을 식별하는 통일된 방식
  - `Resource` : 자원, URI로 식별할 수 있는 모든 것(제한 없음)
  - `Identifier` : 다른 항목과 구분하는데 필요한 정보

`URL(Uniform Resource Locator)`
- **URL는 자원(Resource)의 위치(Location)**
- `Locator` : 자원(Resource)가 있는 **위치(Location)를 지정**
- **위치는 변할 수 있다.**
- EX) `https://www.google.com/search?q=hello&hl=ko`

`URN(Uniform Resource Name)`
- **URN은 자원(Resource)의 이름(Name)**
- `Name` : 자원(Resource)에 **이름(Name)을 부여**
- **이름은 변하지 않는다.**
- URN 이름만으로 실제 자원(Resource)를 찾을 수 있는 방법이 보편화 되지 않음.
- EX) `urn:example:animal:ferret:nose`

### URL 전체 문법

`sheme://[userinfo@]host[:port][/path][?query][#fragment]`
- `https://www.google.com/search?q=hello&hl=ko`

| 규약 | 예시 |
|---|---|
| Protocol | https |
| Host Name | www.google.com |
| Port Number | 443 |
| Path | /search |
| Query Parameter | q=hello&hl=ko |  

**Scheme**
- 주로 Protocol 사용
- `Protocol` : **어떤 방식으로 자원(Resource)에 접근할 것인가 하는 약속, 규칙**
  - http :  80 port, https : 443 port를 주로 사용
  - **port는 생략 가능**
  - https는 http에 Secure 추가 (SSL, TLS)

**userinfo**
- URL에 사용자 정보를 포함해서 인증
- 거의 사용하지 않음

**host**
- 호스트명(Host Name)
- **도메인명(Domain Name) 또는 IP 주소(IP Address)를 직접 사용 가능**

**PORT**
- 접속 포트(PORT)
- **일반적으로 생략**, http:80, https:443

**Path**
- 자원(Resource)의 경로(Path), **계층적 구조**
  - EX)
  - `/home/file2.jpg`
  - `/members`
  - `/members/99` , `/items/iphone8` 

**query**
- `key:value`의 형태
- `?`로 시작, `&`로 추가 가능
- **query paramter, query string** 으로 불린다. Web Server에 제공하는 Parameter. 문자 형태(string)
- EX) `?key1=value1&key2=value2`

**fragment**
- html 내부 북마크 등에 사용
  - html 내부에서 한번에 중간으로 이동하고 싶을 때 사용
- Server에 전송하는 정보 아님
- EX) `https://docs.spring.io/spring-boot/doscs/current/refernce/html/getting-started.html`**#getting-started-introducing-spring-boot**

## Web Browser의 요청(Request) 흐름

![Network  request, response](https://user-images.githubusercontent.com/56071088/146681532-5b7f48cd-3fb1-40fd-a8d4-cd09866bc313.png)


**Client Web Browser에서의 요청(Request) 흐름**
1. URL -> http Request Message 만들기
   1. DNS를 조회.
   2. **IP, PORT정보**를 가져옴. 
2. Web Browser는 위에서 찾아온 정보를 가지고 `http Request Message`를 생성
   - `http method`, `path`, `query paramter`, `http version`, `host`
3. Client와 Server간 연결
   1. **SOCKET 라이브러리를 통해서 Server와 TCP/IP 연결(IP, PORT)을 합니다.**
      - **SOCKET 라이브러리 : Transport Layer에서 3-Way Handshaking이 일어나도록 실행을 해주는 역할** 
   2. TCP/IP Packet이 생성(HTTP Message 포함), 
   3. TCP/IP Packet이 서버를 향해 전송.
4. TCP/IP Packet이 Server에 도착하면 Packet 껍데기는 버리고 http message를 찾아냄.
5. Server는 http message를 읽고 Request을 처리.
6. 처리가 끝난 후에는 http response message를 생성.
7. http response message를 TCP/IP Packet으로 변환
8. TCP/IP Packet을 다시 Client으로 전송.
9. 마지막으로 Web Browser가 TCP/IP Packet을 받아서 html Rendering을 해서 화면(View)을 보여줍니다.

### 참고
- https://techblog.woowahan.com/5268/ 