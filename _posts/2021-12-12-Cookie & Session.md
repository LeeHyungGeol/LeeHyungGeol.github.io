---
layout: single
title: "[Network] Cookie & Session"
categories: Network
tag: [Network, HTTP, Cookie, Session]
toc: true
author_profile: false
search: true
---

## HTTP 프로토콜의 특징

`비연결성(connectionless)`  : HTTP는 클라이언트가 요청(Request)을 서버에 보내고, 서버는 클라이언트에게 적절한 응답(Response)을 주고, **연결(Connection)을 끊는 특성**이 있습니다.
- Header에 `keep-alive` 라는 값을 줘서 Connection을 재활용하는데 `HTTP 1.1` 에서는 이것이 `default`입니다. `(persistent connection)`
- HTTP가 TCP 위에서 구현되었기 때문에 (HTTP 2 까지, HTTP 3는 Quic(UDP 기반)) 네트워크 관점에서 `keep-alive`는 옵션으로 `connectionless`의 연결비용을 줄이는 것을 장점으로 **비연결성**이라 합니다. 

`비상태성(stateless)` : **연결(Connection)을 끊는 순간 Client와 Server의 통신이 끝나며 상태(State) 정보는 유지하지 않는 특성**

## Cookie와 Session의 필요성
- HTTP는 connectionless,stateless 의 특성으로 **모든 요청 간 의존관계가 없습니다.**
  - **현재 접속한 사용자가 이전에 접속했던 사용자와 같은 사용자인지 아닌지 알 수 있는 방법이 없습니다.** 
- 계속해서 connection을 유지하지 않기 때문에 resource 낭비가 줄어드는 것이 장점이지만, 
- 통신할 때 마다 새로운 connection을 생성하기 때문에 Client는 매 요청(request)마다 **인증**을 해야 한다는 단점이 있습니다.

이전 요청(request)과 현재 요청이 같은 사용자의 요청인지 알기 위해서는 **상태(state)를 유지**해야 합니다.

HTTP 프로토콜에서 **상태를 유지하기 위한 기술**로 `Cookie`와 `Session`이 있습니다.

## Cookie

- Cookie는 `Server`가 **사용자의 Web Browser(Client)에 저장하는 데이터를 칭합니다.**
- **Client Local에 저장**되는 **Key와 Value의 쌍으로 String 형태**로 이루어진 임시 파일입니다.
- Web Browser마다 저장되는 Cookie는 다릅니다.(Chrome으로 남긴 Cookie는 IE에서 사용할 수 없습니다.)
- Cookie는 정보들을 Web Browser를 이용하고 있는 컴퓨터에 저장하고, 사용자가 요청(request)을 할 때, 그 정보를 함께 보내서 Server가 사용자를 식별할 수 있게 해줍니다.
- Client에 300개 쿠키저장 기능, 하나의 도메인 당 20개의 값만 가질 수 있음, 하나의 쿠키 값은 4KB까지 저장 가능합니다. 
- 쿠키는 사용자가 따로 요청하지 않아도 Web Browser가 Request 시에 Request Header를 넣어서 자동으로 Server에 전송합니다. 

단, Web Browser내에서는 Request된 URL을 대표이름으로 Cookie를 저장하기 때문에 초기 접속 URL이 그대로 사용되어야 한다.
- http://localhost:9000/test/test.jsp
- http://127.0.0.1:9000/test/test.jsp
- 위의 2개의 URL을 Web Browser에서 서로 다른 것으로 인식


### Cookie의 구성요소
- 쿠키의 이름(name)
- 쿠키의 값(value)
- 쿠키의 만료시간(Expires)
- 쿠키를 전송할 도메인 이름(Domain)
- 쿠키를 전송할 경로(Path)
- 보안 연결 여부(Secure)
- HttpOnly 여부(HttpOnly)

### Cookie의 동작 방식
1. Web Browser가 Server에 요청(Request)
2. State를 유지하고 싶은 값을 Cookie로 생성
3. Server가 응답할 때 HTTP Header(Set-Cookie)에 Cookie를 포함해서 전송
  - `Set−Cookie: id=lee`
4. 전달받은 Cookie는 Web Browser에서 관리하고 있다가, 다음 요청 때 Cookie를 HTTP Header에 넣어서 전송
5. Server에서는 Cookie 정보를 읽어 이전 상태 정보를 확인한 후 응답(Response)
   - `cookie: id=lee` 

### Cookie가 사용되는 예시

- ID 저장, Login 상태 유지
  - Cookie가 있기 때문에 여러 page를 이동할 때 마다 login을 하지 않고 사용자 정보를 유지할 수 있는 것입니다.
- 최근 검색한 상품들을 광고에서 추천
- 쇼핑몰 장바구니 기능

### Cookie의 단점

1. 방문했던 웹 사이트에 대한 정보 및 개인정보가 기록되기 때문에 **사생활을 침해할 소지**가 있습니다.
   - 이를 해소하기 위해서 웹 브라우저 자체에 `쿠키 거부 기능`이 있습니다. 
   - 하지만, 쿠키에 대한 거부가 웹 브라우저에 설정되어 있으면, **쿠키 본래의 목적인 웹 브라우저와의 연결을 지속시키는 기능을 수행할 수 없는 경우가 발생합니다.**
2. Server가 가지고 있는 것이 아니라 Web Browser에게 저장되기 때문에, 임의로 고치거나 지울 수 있고 가로채기도 쉬워 **보안이 취약합니다.** 
   - 따라서, **Cookie에는 민감하거나 중요한 정보를 담는 것은 위험합니다.**

그래서 이런 단점을 보완해주는 것이 Session입니다.

## Session

- `일정 시간` 동안 같은 Web Browser로부터 들어오는 요청을 하나의 상태로 보고 **그 상태를 유지하는 기술**
- **Session은 Cookie를 기반**하고 있지만, 사용자 정보 파일을 브라우저에 저장하는 쿠키와 달리 세션은 서버 측에서 관리합니다.
- Server에서는 Client(Web Browser)를 구분하기 위해 `Session ID`를 부여하며 **Web Browser가 Server에 접속해서** `Browser를 종료`할 때까지 **인증상태를 유지합니다.**
  - **접속 시간에 제한**을 두어 일정 시간 응답이 없다면 정보가 유지되지 않게 설정이 가능 합니다.
- 사용자에 대한 정보를 `서버에 저장`하기 때문에 쿠키보다 보안에 좋지만, **사용자가 많아질수록 서버 메모리를 많이 차지하게 됩니다.**
  - 동접자 수가 많은 웹 사이트인 경우 **서버에 과부하를 주게 되므로 성능 저하의 요인이 됩니다.**

### Session의 동작 방식

Session도 `Cookie`를 사용하여 값을 주고 받으며 Client의 상태 정보를 유지한다.

**즉, 상태 정보를 유지하는 수단은 Cookie 이다.**

1. Web Browser가 서버에 요청
2. 서버가 해당 Web Browser(클라이언트)에 유일한 ID(Session ID)를 부여함
3. 서버가 응답할 때 HTTP 헤더(Set-Cookie)에 Session ID를 포함해서 전송
   - Cookie에 Session ID를 JSESSIONID 라는 이름으로 저장
   - `Set−Cookie: JSESSIONID=xslei13f`
4. Web Browser는 이후 **Web Browser를 닫기까지** 다음 요청 때 `부여된 Session ID`가 담겨있는 **Cookie**를 HTTP Header에 넣어서 전송
  - `Cookie: JSESSIONID=xslei13f`
5. Server는 Session ID를 확인하고, 해당 세션에 관련된 정보를 확인한 후 응답

## Cookie와 Session의 차이점

**저장 위치: 중요**💡
- 쿠키 : Client(Web Browser)
- 세션 : Server

**보안**
- 쿠키 : 클라이언트에 저장되므로 보안에 취약하다.
- 세션 : 쿠키를 이용해 Session ID만 저장하고 이 값으로 구분해서 서버에서 처리하므로 비교적 보안성이 좋다.

**라이프사이클(LifeCycle): 중요**💡
- 쿠키 : 만료시간에 따라 브라우저를 종료해도 계속해서 남아 있을 수 있다.
- 세션 : 만료시간을 정할 수 있지만 브라우저가 종료되면 만료시간에 상관없이 삭제된다.

**속도**
- 쿠키 : 클라이언트에 저장되어서 서버에 요청 시 빠르다.
- 세션 : 실제 저장된 정보가 서버에 있으므로 서버의 처리가 필요해 쿠키보다 느리다.

## 세션을 주로 사용하면 좋은데 왜 굳이 쿠키를 사용할까?

Session은 **Server의 자원을 사용** 
- 무분별하게 만들다보면 Server의 메모리가 감당할 수 없어질 수가 있고 속도가 느려질 수 있기 때문에 **쿠키가 유리한 경우가 있습니다.**

## 쿠키/세션은 캐시와 엄연히 다르다!

- 캐시는 이미지나 css, js파일 등을 브라우저나 서버 앞 단에 저장해놓고 사용하는 것입니다.
- 한번 캐시에 저장되면 브라우저를 참고하기 때문에 서버에서 변경이 되어도 사용자는 변경되지 않게 보일 수 있는데 이런 부분을 캐시를 지워주거나 서버에서 클라이언트로 응답을 보낼 때 header에 `캐시 만료시간`을 명시하는 방법등을 이용할 수 있습니다.

### 참고 문헌 및 사이트
- https://interconnection.tistory.com/74
- https://doooyeon.github.io/2018/09/10/cookie-and-session.html
- https://devuna.tistory.com/23
- https://hahahoho5915.tistory.com/32