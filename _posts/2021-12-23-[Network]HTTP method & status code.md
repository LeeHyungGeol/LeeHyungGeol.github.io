---
layout: single
title: "[Network] HTTP method & status code"
categories: Network
tag: [Network, HTTP, HTTP method, HTTP status, HTTP API 설계, status code]
toc: true
author_profile: false
search: true
---

# HTTP method

```
- HTTP API 설계
- HTTP 메서드 - GET, POST, PUT, PATCH, DELETE
- HTTP 메서드의 속성
- HTTP 메서드 활용
- HTTP 상태 코드
```

# HTTP API 설계

## 요구사항에 따른 API URI 설계

회원 정보 관리 API를 설계
- **회원** 목록 조회 (/read-member-list) -> **(/members)** 
- **회원** 조회 (/read-member-by-id) -> **(/members/{id}) -> 어떻게 구별할까? -> 행위로 구분**
- **회원** 등록 (/create-member) -> **(/members/{id} -> 어떻게 구별할까? -> 행위로 구분**
- **회원** 수정 (/update-member) -> **(/members/{id}-> 어떻게 구별할까? -> 행위로 구분**
- **회원** 삭제 (/delete-member) -> **(/members/{id}-> 어떻게 구별할까? -> 행위로 구분**
- **cf) 계층 구조상 상위를 컬렉션으로 보고 복수단어 사용 권장(member -> members)**

> 자원(resource)의 의미는 뭘까?

- 회원을 등록, 수정, 삭제하는게 자원(resource)가 아니다!!
- EX) 게시글을 등록 -> 게시글이 자원(resource)
- **회원이라는 개념 자체가 바로 자원(resource)이다.**

> 자원(resource)을 어떻게 식별하는게 좋을까?

- 회원을 등록, 수정, 조회, 삭제 하는 것을 모두 배제
- **회원(member)이라는 자원(resource)만 식별하면 된다!!! -> 회원 resource를 URI에 Mapping**

> 자원(resource)과 행위를 분리

**가장 중요한 것은 자원(resource)를 식별하는 것!!**
- **URI는 자원(resource)만 식별!!**
- **자원**과 해당 자원을 대상으로 **행위**를 분리
  - 자원(resource) : 회원
  - 행위 : 조회, 수정, 등록, 삭제
- 자원(resource)는 명사, 행위는 동사

# HTTP 메서드 - GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS, CONNECT, TRACE

> - GET : 자원(resource)을 조회, **가져오는 것(SELECT)**
> - POST : 요청 데이터 처리, 주로 등록에 사용, **서버의 값이나 상태를 바꾸기 위한 용도의 메서드, 수행하는 것(INSERT, UPDATE, DELETE)**
> - PUT : 자원(resource)를 대체(완전히 새로 생성해서 데체), 해당 자원(resource)가 없으면 생성
> - PATCH : 자원(resource) 부분 변경
> - DELETE : 자원(resource) 삭제
> - HEAD : GET과 동일하지만 message-body를 제외하고, status-line과 header만 반환
> - OPTIONS : 대상 자원(resource)에 대한 통신 가능 옵션(method)을 설명 **(주로 CORS에서 사용)**
> CONNECT : 대상 자원(resource)에 대한 경로를 따라 메시지 루프백 테스트를 수행

## GET

> **GET** : 자원(resource)을 조회, **가져오는 것(SELECT)**

- 서버에 전달하고 싶은 데이터는 `query(query paramter, query string)` 를 통해서 전달
- **message-body를 사용해서 데이터를 전달할 수 있지만!!, 지원하지 않는 곳이 많아서 권장하지 않는다.** 

### **GET 사용 방법**

URL의 끝에 '?'가 붙고, 요청 정보가 (key=value)형태의 쌍을 이루어 ?뒤에 이어서 붙어 서버로 전송한다.
- 요청 정보가 여러 개일 경우에는 '&'로 구분한다.
- EX) `www.urladdress.xyz?name1=value1&name2=value2`

### **GET 특징**
1. URL에 요청 정보를 붙여서 전송한다.
2. URL에 요청 정보가 이어붙기 때문에 길이 제한이 있어서 **대용량의 데이터를 전송하기 어렵다.**
   - 한 번 요청 시 전송 데이터(주솟값 + 파라미터)의 양은 **255자로 제한된다. (HTTP/1.1은 2048자)**
3. POST 방식보다 보안상 취약하다. (URL에 요청 정보가 노출되기 때문에)
4. HTTP 메시지의 message-body는 없다.
   - 앞에서 말했듯이 사용할 수 있지만, 지원하지 않는 곳이 많아서 잘 사용하지 않는다.
5. POST 방식보다 빠르다.
   - **GET 방식은 캐싱을 사용할 수 있어, GET 요청과 그에 대한 응답이 Web Browser에 의해 캐쉬된다.**

## POST

> **POST : 요청 데이터 처리, 서버의 값이나 상태를 바꾸기 위한 용도의 메서드, 수행하는 것(INSERT, UPDATE, DELETE)**

- **message-body를 통해 서버로 요청 데이터 전달**
- 서버는 message-body를 통해 들어온 데이터를 처리하는 모든 기능을 수행한다.
  - 주로 전달된 데이터를 신규 리소스 등록, 프로세스 처리에 사용한다.

### **POST 사용 방법**

**요청 정보를 HTTP 메시지의 message-body 안에 숨겨서 서버로 전송한다.**
- request header의 `Content-Type`에 해당 `data-type`이 표현되며, 전송하고자 하는 data-type을 적어주어야 한다.
  - Default: `application/octet-stream`
  - 단순 txt의 경우: `text/plain`
  - 파일의 경우: `multipart/form-data`
  - json일 경우: `application/json`

### **POST 특징**
1. Body 안에 숨겨서 요청 정보를 전송하기 때문에 **대용량의 데이터**를 전송하기에 적합하다.
2. **GET 방식보다 보안상 안전**하다.

### **POST 주 사용처**
1. 새 리소스 생성(등록)
    - 서버가 아직 식별하지 않은 채 리소스 생성
2. 요청 데이터 처리
    - 단순히 데이터를 생성, 변경하는 것이 아닌 프로세스를 처리하는경우
    - EX) 주문에서 결제완료 -> 배달시작 -> 배달완료 처럼 단순히 값 변경을 넘어 - 프로세스의 상태가 변경되는 경우
    - POST의 결과로 새로운 리소스가 생성되지 않을 수 있음
    - EX) POST /orders/{orderId}/start-delivery `(컨트롤 URI)`
3. 다른 메서드로 처리하기 애매한 경우
    - JSON으로 조회 데이터를 넘겨야 하는데, GET 메서드를 사용하기 어려운 경우
4. 애매하면 POST

## PUT

> **PUT : 자원(resource)을 완전히 대체**

### **PUT 특징**

**1. 자원(resource)을 완전히 대체**
- 자원(resource)이 있으면 대체(기존 내용을 삭제하고 완전히 다시 생성)
- 자원(resource)이 없으면 생성

**2. 중요💡 Client가 resource를 식별(이미 알고있다)**
- Client가 resource 위치를 알고 URI를 지정
- **이것이 POST와의 차이점이다.**
- EX) `POST /members HTTP/1.1` - `PUT members/100 HTTP/1.1`
  - POST는 members의 100일지 101번일지 모르지만, PUT은 정확하게 memebers의 100번이라고 알고 있다.

## PATCH

> **PATCH : 자원(resource)의 부분 변경**

- 요즘은 거의 다 지원이 되지만, 간혹 가다가 HTTP 에서 PATCH를 받아들이지 못하는 경우, POST를 사용하여 부분 변경을 수행하면 된다.

EX) 
```
PATCH /members/100 HTTP/1.1
Content-Type: application/json
{
    "age" : 50
}
```

## DELETE

> **DELETE : 자원(resource)을 삭제**

EX) `DELETE /members/100 HTTP/1.1`

# HTTP 메서드의 속성

```
- 안전(Safe Methods)
- 멱등(Idempotent Methods)
- 캐시가능(Cacheable Methods)
```

![HTTP 메서드 특성](https://user-images.githubusercontent.com/56071088/146878824-b04c7c5d-551b-4b69-89ce-afcd3b547840.JPG)

## 안전(Safe)

> **안전(Safe) : 1번 이상 호출해도 자원(resource)를 변경하지 않는다.**

**안전(Safe)한 메서드를 계속 호출해서, 로그 같은게 쌓여서 장애가 발생한다면?**
- 안전은 해당 자원(resource)만 고려한다. 그런 부분까지 고려하지 않는다.
- 해당 자원(resource)이 변하냐, 변하지 않냐만 고려한다.

## 멱등성(Idempotent)

> **멱등성(Idempotent) : 1번 이상, 100번 이상 호출해도 결과가 똑같다.**
> **F(F(x)) = F(x)**

### **멱등 메서드**
- GET: 1번 조회하든, 100번 조회하든 같은 결과가 조회된다.
- PUT: 결과를 대체한다. 따라서 같은 요청을 여러번 해도 최종 결과는 같다.
- DELETE: 결과를 삭제한다. 같은 요청을 여러번 해도 삭제된 결과는 동일하다.
- POST: 멱등 X

### **멱등성(Idempotent) 활용**
- 자동 복구 매커니즘
  - **Server가 TIMEOUT 등으로 정상 응답을 못주었을 때, Client가 같은 요청을 다시 해도 되는가? 의 판단 근거가 된다.**
- **재요청 중간에 다른 곳에서 자원(resource)를 변경해버린다면??**
  - 멱등은 외부 요인으로 중간에 resource가 변경되는 것 까지는 고려하지 않는다.
  - **동일한 사용자가 똑같은 요청을 여러 번 수행했을 때 멱등한 것만을 고려한다.**

## 캐시가능(Cacheable)

> **캐시가능(Cacheable) : 응답 결과 resource를 cache해서 사용해도 되는가?**

**Web Browser에서 용량이 큰 web page를 요청할 때, 2번 이상 같은 요청을 할 필요가 없이 local pc에 web browser가 저장을 할 수 있냐, 없냐**

- GET, HEAD, POST, PATCH 캐시 가능
- 실제로는 `GET`, `HEAD` 정도만 캐시로 사용
- 캐시(cache)를 하려면 똑같은 resource라는 `key`가 맞아야 한다. 
- GET은 URL만 key로 잡고 캐시 하면 된다.
  - POST, PATCH는 본문 내용까지 캐시 키로 고려해야 하는데, 구현하기 쉽지 않다.
  - POST, PATCH는 message-body까지 고려해줘야 하는데 쉽지 않다.

### 조회하기 위한 용도 POST가 아닌 GET 방식을 사용하는 이유?

**1. 설계 원칙에 따라 GET 방식은 서버에게 여러 번 요청을 하더라도 동일한 응답이 돌아와야 한다. (Idempotent, 멱등성)**
   - GET 방식은 가져오는 것(Select) 으로, 서버의 데이터나 상태를 변경시키지 않아야 한다.
   - POST 방식은 수행하는 것 으로, 서버의 값이나 상태를 바꾸기 위한 용도이다.

**2. 웹에서 모든 리소스는 Link할 수 있는 URL을 가지고 있어야 한다.**
   - 어떤 웹페이지를 보고 있을 때 다른 사람한테 그 주소를 주기 위해서 주소창의 URL을 복사해서 줄 수 있어야 한다.
   - 즉, 어떤 웹페이지를 조회할 때 원하는 페이지로 바로 이동하거나 이동시키기 위해서는 해당 링크의 정보가 필요하다.
   - 이때 POST 방식을 사용할 경우에 값(링크의 정보)이 Body에 있기 때문에 URL만 전달할 수 없으므로 GET 방식을 사용해야한다. 그러나 글을 저장하는 경우에는 URL을 제공할 필요가 없기 때문에 POST 방식을 사용한다.

# HTTP 메서드 활용

## Client에서 Server로 데이터 전송

> **데이터 전달 방식은 크게 2가지**
> 
> **1. query parameter(query string)를 통한 데이터 전송**
> - `GET`
> - 주로 정렬 혹은 필터(검색어)
> 
> **2. message-body를 통한 데이터 전송**
> - `POST`, `PUT`, `PATCH`
> - 회원가입, 상품주문, 리소스 등록, 리소스 변경

### **Client에서 Server로 데이터 전송하는 4가지 상황**

#### **1. 정적 데이터 조회(GET)**
- 추가적인 데이터 전송 없이 URI(resource path) 만으로 조회 가능
- 이미지, 정적 텍스트 문서
- **query parameter 미사용**
- `GET`을 사용

#### **2. 동적 데이터 조회(GET)**
- **query paramter 사용**
- query parameter를 기반으로 정렬 필터해서 결과를 동적으로 생성
- **검색, 게시판 목록 정렬 필터 할 때 사용합니다(조회 조건을 줄여주거나 정렬 하는데 주로 사용)**
- `GET`을 사용

#### **3. HTML form tag를 통한 데이터 전송**

```html
EX)
<form action="/save" method="post" enctype="multipart/form-data">
    <input type="text" name="username"/>
    <input type="text" name="age"/>
    <input type="file" name="file1"/>
    <button type="submit">전송</button>
</form>
```
- **Web Browser가 form submit 버튼을 누르면 form tag를 해석 후, http message를 생성해준다.**  
- **HTML form tag는 GET, POST만 지원** 
- `POST` 전송 => `message-body`로 전송, 전송 데이터를 url encoding 처리 : Default 값 : `Content-Type: application/x-www-form-unlencoded`
- `GET` 전송 => URL 경로 : `query paramter`로 전송
- file 전송 => file upload와 같이 binary data 전송시 사용, `message-body`로 전송 : `Content-Type: multipart/form-data`

#### **4. HTTP API를 통한 데이터 전송**

EX) Android, iPhone과 같은 application에서 client에서 server로 data를 바로 전송해야 할 때 사용한다.
- 보통 각 Client에서 HTTP Message를 만들어주는 Library들이 있다.

**어디에서 사용하는가?**
- Server to Server 통신
- Mobile Client(Android, iPhone)
- Web Client
  - **HTML form tag를 통한 전송 대신 JavaScript를 통한 통신(AJAX)에 사용**
  - EX) React, Vue와 같은 Web Client와 API 통신
- `Content-Type: application/json`을 주로 사용

## HTTP API 설계 예시

> **HTTP API - 컬렉션(Collection)**
> - **POST 기반 등록**
> - EX) 회원 관리 API 제공
> 
> **HTTP API - 스토어**
> - **PUT 기반 등록**
> - EX) 정적 컨텐츠 관리, 원격 파일 관리
> 
> **HTML form tag 사용**
> - Web Page 회원 관리
> - GET, POST만 지원

중요💡) `POST`기반 등록 / `PUT`기반 등록의 **차이점**을 아는 것이 중요하다!

### API 설계 - POST 기반 등록 (회원 관리 시스템)

> **Client가 등록될 resource의 URI를 알지 못함**
> **Server에서 새로 등록된 resource의 URI를 결정하고 만들어준다!!**
> **컬렉션(Collection) : Server가 관리하는 리소스 디렉토리** 

- **회원** 목록 : `/members` -> **GET**
  - 정렬, 검색어가 필요할 시에 `query paramter`를 사용하면 된다.
- **회원** 등록 : `/members` -> **POST**
  - `/members` -> **컬렉션(Collection)**
- **회원** 조회 : `/members/{id}` -> **GET**
- **회원** 수정 : `/members/{id}` -> **PATCH, PUT, POST**
  - PUT = 덮어쓰기 (게시글 수정 등, 전체를 모두 수정하는 경우)
  - PATCH = 일부 수정
  - POST = 애매할 때
- **회원** 삭제 : `/members/{id}` -> **DELETE**

#### POST - 신규 자원 등록 특징

**1. Client가 등록될 resource의 URI를 모른다.**
- 회원 등록 : `/members` -> `POST`
- `POST /members`

**2. 중요💡) Server에서 새로 등록된 resource의 URI를 결정하고 만들어준다!!**

```
HTTP/1.1 201 Created
Location: /members/100
```
- Location에 값이 정해질 수도 있고, message-body의 id 값에 정해질 수도 있다.

**3. 컬렉션(Collection) : Server가 관리하는 리소스 디렉토리**
- Server가 resource의 URI를 생성하고 관리
- 여기서 Collection은 `/members`

### API 설계 - PUT 기반 등록 (파일 관리 시스템)

> **Client가 URI 를 알고 있어야 한다.**
> **Client가 직접 resource의 URI 를 알고 있어야 한다.**
> **스토어(Store) : Client가 관리하는 resource 저장소** 

- **파일** 목록 : `/files` -> **GET**
- **파일** 조회 : `/files/{filename}` -> **GET**
- **파일** 등록 : `/files/{filename}` -> **PUT**
- **파일** 삭제 : `/files/{filename}` -> **DELETE**
- **파일** 대량 등록 : `/files` -> **POST**
  - PUT을 이용하여 file을 등록하기 때문에, POST는 개발자가 임의로 의미를 부여하여 사용할 수 있다.
  - 여기서는 '대량 등록'의 의미로 POST를 사용했다.

#### PUT - 신규 자원 등록 특징

**1. Client가 resource URI 를 알고 있어야 한다.**
- 파일 등록 : `/files/{filename}` -> **PUT**
- `PUT /files/chicken.jpg`

**2. 중요💡) Client가 직접 resource URI 를 지정한다.**

**3. 스토어(Store) : Client가 관리하는 리소스 저장소**
- Client가 resource의 URI를 알고 관리
- 여기서 스토어는 `/files`

### API 설계 - HTML form tag 사용

> **중요💡) 컨트롤 URI : 동사로 된 resource 경로 사용** 

**HTML form tag는 GET, POST 만 지원** (AJAX의 도움으로 다른 method도 사용 가능)
- 여기서는 순수하게 HTML, HTML form 만 사용
- method로 행위를 식별해줄 수가 없기 때문에, **URI 로 식별(컨트롤 URI)**

#### 컨트롤 URI
- **동사로 된 resource 경로 사용** 
- EX) POST의 `/new`, `/edit`, `/delete`
- HTTP 메서드로 해결하기 애매한 경우 사용 (HTTP API 포함)
- 실무에서도 많이 쓰인다!!
- 컨트롤 URI 는 무식하게 쓰면 안되고 정말 해결 안될 때 사용.

**설계**
- **회원** 목록 : `/members` -> GET
- **회원** 등록 폼 : `/members/new` -> GET
- **회원** 등록 : **`/members/new`** , `/members` -> POST
  - **Validation 문제** : Server에 문제가 있어서 POST의 최종 결과를 다시 회원 등록 폼으로 돌려줘야 할 때가 있다.
  - 이때, URI를 똑같이 하면, 경로가 안바뀌고 문제가 깔끔하게 해결된다. 
- **회원** 조회 : `/members/{id}` -> GET
- **회원** 수정 폼 : `/members/{id}/edit` -> GET
- **회원** 수정 : **`/members/{id}/edit`** , `/members/{id}` -> POST
- **회원** 삭제 : `/members/{id}/delete` -> POST

### 참고하면 좋은 URI 설계 개념 

- **문서(Document)**
  - 단일 개념(파일 하나, 객체 인스턴스, 데이터베이스 row)
  - EX) `/members/100`, `/files/star.jpg`
- **컬렉션(Collection)**
  - 서버가 관리하는 리소스 디렉터리
  - 서버가 리소스의 URI를 생성하고 관리
  - EX) `/members`
- **스토어(Store)**
  - 클라이언트가 관리하는 자원 저장소
  - 클라이언트가 리소스의 URI를 알고 등록
  - EX) `/files`
- **컨트롤러(Controller), 컨트롤 URI**
  - 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행(데이터를 조작하거나 변경하는 일)
  - 동사를 직접 사용
  - EX) `POST /orders/{id}/edit`
  - EX) HTML form 뿐만이 아닌 HTTP API에서도 많이 사용한다. -> 회원의 주문 상태를 변경하라 -> `orders/{id}/delivery`

# HTTP 상태 코드(Status Code)

> **HTTP 상태 코드(Status Code) : Client가 보낸 요청의 처리 상태를 응답에서 알려주는 기능**

```
1xx(Informational) - 요청이 수신되어 처리중
2xx(Successful) - 요청 정상 처리
3xx(Redirection) - 요청을 완료하려면 추가 행동이 필요
4xx(Client Error) - 클라이언트 오류
5xx(Server Error) - 서버 오류
```

## 만약 모르는 상태 코드가 나타나면?

Client가 인식할 수 없는 상태 코드를 서버가 반환하면? -> **Client는 상위 상태코드로 해석해서 처리한다.**
- 미래에 새로운 상태 코드가 추가되어도 Client를 변경하지 않아도 된다.
- EX)
- 299 ??? -> 2xx(Successul)
- 467 ??? -> 4xx(Client Error)

## 1xx(Informational)

> **1xx(Informational) : 요청이 수신되어 처리중**
- 거의 사용하지 않으므로 생략

## 2xx(Successful) 

> **2xx(Successful) : 성공, Client의 요청을 성공적으로 처리**

- `200 OK` : **요청 성공**
  - GET resource 조회 -> 200 OK + resource 전달
- `201 Created` : **요청 성공해서 새로운 resource가 생성됨**
  - `POST` 신규 resource 등록 요청 -> COLLECTION : Server에서 URI 생성, **생성된 resource는 Response의 Location Header 필드로 식별**
```
HTTP/1.1 201 Created
Content-Type: application/json
Location: /members/100
{
  "username": "young",
  "age": 20
}
```
- `202 Accepted` : **요청이 접수되었으나 처리가 완료되지 않았음**
  - Batch 처리 같은 곳
  - EX) 요청 접수 후 1시간 뒤에 Batch Process가 요청을 처리함
  - 사실 잘 사용하지는 않는다.
- `204 No Content` : **Sercer가 요청을 성공적으로 수행했지만, 응답 payload 본문에 보낼 데이터가 없음**
  - EX) 웹 문서 작성 페이지의 save 버튼
  - save 버튼의 결과로 아무 내용이 없어도 된다.
  - save 버튼을 눌러도 같은 화면을 유지해야 한다.
  - 결과 내용이 없어도 204 메시지(2xx)만으로 성공을 인식할 수 있다.

### 200, 201, 202, 204 상태 코드 모두 사용하는 것이 바람직할까?

모두 사용하는 것이 꼭 바람직하지는 않다. 
- 성공은 200 코드 하나만 사용할 수도 있고,
- 200, 201 2개만 사용할 수도 있다.
- 개발할 때, 각 팀들이 내부적으로 어떤 것들을 사용할지 미리 범위를 정하고 개발하는 것이 좋다. 상태 코드는 너무 많은데 그것을 다 관리하기가 쉽지도 않고, 효율적이지도 않다.

## 3xx - Redirection

> **3xx(Redirection) : 요청을 완로하기 위해 User Agent(Client Program : Web Browser)의 추가 조치 필요**

```
300 Multiple Choices
301 Moved Permanently
302 Found
303 See Other
304 Not Modified
307 Temporary Rerirect
308 Parmanent Rerirect
```

### Redirection 이해

- Web Browser는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동합니다.(Redirect)

**종류**

**영구 리다이렉션 : 특정 리소스의 URI가 영구적으로 이동**
- EX) `/members` -> `/users`
- EX) `/event` -> `/new-event`

**일시 리다이렉션 : 일시적인 변경**
- 주문 완료 후 주문 내역 화면으로 이동 등
- **`PRG: Post/Redirect/Get`** (패턴이다)

**특수 리다이렉션 : 결과 대신 캐시를 사용**
- EX) Client에 cache가 있고 cache기간이 만료된 것 같을 때, cache기간이 만료된 것이 맞는지 cache와 관련된 정보를 Server한테 넘겨준다.
- cache 만료기간이 지나지 않았다면, **Server는 Client에게 cache를 그대로 사용하라고 한다.**

### 영구 Redirection(301, 308)

> **영구 Redirection(301, 308) : resource의 URI가 영구적으로 이동**

- 원래의 URL을 사용X, 검색 엔진 등에서도 변경 인지
- `301 Moved Permanently`
  - **리다이렉트시 요청 메소드가 GET으로 변경되고, 본문이 제거될 수 있음(MAY)**
    - 처음에 보낼 때 POST 로 보내면서 리소스를 함께 보냈다면, 리다이렉트 할 때는 리소스가 날아갈 수 있어요.
- `308 Permanent Redirect`
  - 301과 기능이 같다!
  - 리다이렉트시 요청 메소드와 본문 유지 (처음 POST를 보내면 리다이렉트도 POST 유지)
    - 처음에 입력한 그대로 리다이렉션 되서 넘어갑니다.

사실상 실무에서는 `/event` -> `/new-event` 로 URI가 바뀌면, **내부적으로 전송해야할 data들이 다 바뀐다.** 
- 웬만하면 `301 Moved Permanently` 을 사용하여, `POST` 로 요청이 와도, 다시 `GET` 으로 변경시킨다.
- `301, 308`은 사실 실무에서 많이 쓰이지 않는다.

### 일시적 Redirection(302, 303, 307)

> **일시적 Redirection(302, 303, 307) : resource의 URI가 일시적으로 변경**
> 따라서, 검색 엔진 등에서 URL을 변경하면 안됨

- `302 Found`
  - **리다이렉트 요청시 메소드가 GET으로 변경되고, 본문이 제거될 수 있음(MAY)**
  - 대부분 GET으로 변경한다. 명확하지는 않다.
- `303 See Other`
  - 302 와 기능 같음
  - **리다이렉트시 요청 메소드가 GET으로 변경(MUST)**
- `307 Temporary Rerirect`
  - 302 와 기능 같음
  - **리다이렉트시 요청 메소드와 본문 유지(요청 메서드를 변경하면 안된다. MUST NOT)**

**일시적인 Redirection 예시**
- **`PRG: Post/Redirect/Get`**
- **PRG 사용전**
- POST로 주문 후에 Web Browser를 새로고침할 경우, 새로고침은 기존의 주문 요청을 다시 수행한다. -> 중복 주문이 된다.
- 사실 이런 오류는 Server에서 미리 잘 막아야 한다. orderId를 미리 만들어 놓고, 중복 주문이 오면, 이미 사용된 주문번호 등을 이용하여 Server에서 미리 막아야 한다.
- 그래도 Client에서도 미리 이러한 요청을 막아줄 필요가 있다.
  - **해결법**
  - **PRG 사용**
  - **Post로 주문 후에 주문 결과 화면을 GET으로 Redirect**
    - 새로고침 해도 결과화면을 GET 으로 받아서 resource를 조회한다. 그래서 중복 주문이 들어가지 않는다.

### 특수 Redirection(300, 304)

> **300 Multiple Choices : 안쓴다.**
> **304 Not Modified : cache, 조건부 요청에 사용한다.**

`304 Not Modified`
- **캐시를 목적으로 사용**
- **조건부 GET, HEAD 요청시 사용**
- Client에서 resource(cache가 만료되지 않았다.)가 수정되지 않았음을 알려준다. 
- 따라서 Client는 Local PC에 저장된 cache를 재사용한다.(cache로 Redirect한다.) 
- `304 응답`은 응답에 message-body를 포함하면 안된다.(Local cache를 사용해야 하므로)

### 302,303,307 중에 뭘 써야 하는가?

역사
- 처음 302 스펙의 의도는 HTTP method를 유지하는 것
- 그런데 Web Browser들이 대부분 GET으로 바꾸어 버린다.(물론, 일부는 다르게 동작하는 것도 있다.)
- 그래서 모호한 302 대신해서 명확한 303, 307 이 등장했다.(301을 대신해서 308도 등장했다.)

현실
- 희망하기는 보통 명확한 `303, 307`을 희망한다.
- 하지만, 실무에서는 여전히 302도 사용된다. 자동 Redirection에서 GET으로 변경되어도 상관없는 경우에는 302를 사용하기도 한다.

## 4xx - Client Error

> **4xx (Client Error) : Client의 요청에 Server가 요청을 수행할 수 없음**
> **오류의 원인이 Client에 있다!!**
> 중요💡) Client가 이미 잘못된 요청, data를 보내고 있기 때문에, 똑같은 재시도가 실패한다.
> 4xx오류는 Client 자체에 문제가 있기 때문에, 그대로 보내면 똑같이 오류가 발생한다. 요청을 무조건 수정해서 보내야 한다.

### 400 Bad Request

**Client가 잘못된 요청을 해서 Server가 요청을 처리할 수 없음**
- Client는 요청 내용을 다시 검토하고, 보내야 한다.
- EX) request paramter가 잘못됨, API 스펙이 맞지 않을 때(문자를 보내야 하는데, 숫자를 보낼 때)

### 401 Unauthorized

**Client가 해당 resource에 대한 인증이 필요함(Login 자체가 안되었다.)**
- **인증(Authentication)되지 않음**
- 401 오류 발생시 : 응답에 `WWW-Authenticate Header`와 함꼐 인증 방법을 설명
  - 참고
  - 인증(Authentication): 본인이 누구인지 확인(Login이 되어야 한다. Login이 안되면 인증이 안된다.)
    - **Login을 할 수 있다, 없다를 확인**
  - 인가(Authorization): 권한 부여(ADMIN 권한처럼 특정 resource에 접근할 수 있는 권한, 인증이 있어야 인가가 있다)
    - **특정 resource에 대해 접근할 수 있는지, 없는지에 대한 등급**
  - 오류 메시지가 Unauthorized 이지만 **인증에 대한 오류**임

### 403 Forbidden

**Server가 요청을 이해했지만 승인을 거부함**
- 주로 인증 자격 증명은 있지만(Login은 되었지만, Authentication은 되었지만), 접근 권한이 불충분한 경우(Authorization이 안된 경우)
- EX) ADMIN 등급이 아닌 사용자가 Login은 했지만, ADMIN 등급의 resource에 접근한 경우

### 404 Not Found

**요청 resource를 찾을 수 없음**
- 요청 resource가 Server에 없음
- 또는 Client가 권한이 부족한 resouce에 접근할 때 해당 resource를 숨기고 싶을 때

## 5xx - Server Error

> **5xx (Server Error) : Server 내부 문제로 오류 발생**
> - Server에 문제가 있기 때문에 재시도 하면 성공할 수도 있음(복구가 되거나 등등)

### 500 Internal Server Error

**Server 문제로 오류 발생, 애매하면 500 error**
- 애매하면 500 error

### 503 Service Unavailable

**서비스 이용 불가**
- 서비스가 일시적인 과부하 또는 예정된 작업으로 잠시 요청을 처리할 수 없음
- `Retry-After Header 필드`로 얼마 뒤에 복구되는지 보낼 수도 있음

### 진짜 Server의 오류일 때 5xx 오류를 사용해야 한다!!

비즈니스 로직상에 잘못된 요청(고객의 잔고 부족, 고객의 심사(20살 이상만 주문 가능할 때, 20살이 아닌 사람이 주문할 때))
- **5xx Error를 내면 안된다!!**
- 진짜 Server에 문제가 있을 때 내야 한다.
- Monitoring tool도 5xx Error이 발생하면, Server에 심각한 오류가 있다고 판단
- 비즈니스 로직 상의 문제는 예외 케이스인거지 -> 4xx오류로 해결
- query 오류, NullPointerException, DB 다운이 되었을 때가 진짜 5xx Error

### 참고 문헌 및 사이트

- https://ko.wikipedia.org/wiki/HTTP
- https://restfulapi.net/resource-naming/