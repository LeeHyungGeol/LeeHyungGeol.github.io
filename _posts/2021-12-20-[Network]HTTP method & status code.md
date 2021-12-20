---
layout: single
title: "[Network] HTTP method & status"
categories: Network
tag: [Network, HTTP, HTTP method, HTTP status]
toc: true
author_profile: false
search: true
---

# HTTP method

```
- HTTP API 설계
- HTTP 메서드 - GET, POST, PUT, PATCH, DELETE
- HTTP 메서드의 속성
```

## HTTP API 설계

### 요구사항에 따른 API URI 설계

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

> 자원(resource)와 행위를 분리

**가장 중요한 것은 자원(resource)를 식별하는 것!!**
- **URI는 자원(resource)만 식별!!**
- **자원(resource)**와 해당 자원(resource)를 대상으로 **행위**를 분리
  - 자원(resource) : 회원
  - 행위 : 조회, 수정, 등록, 삭제
- 자원(resource)는 명사, 행위는 동사

## HTTP 메서드 - GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS, CONNECT, TRACE


> - GET : 자원(resource)을 조회 **가져오는 것(Select)**
> - POST : 요청 데이터 처리, 주로 등록에 사용, **서버의 값이나 상태를 바꾸기 위한 용도의 메서드, 수행하는 것(Insert, Update, Delete)**
> - PUT : 자원(resource)를 대체(완전히 새로 생성해서 데체), 해당 자원(resource)가 없으면 생성
> - PATCH : 자원(resource) 부분 변경
> - DELETE : 자원(resource) 삭제
> - HEAD : GET과 동일하지만 message-body를 제외하고, status-line과 header만 반환
> - OPTIONS : 대상 자원(resource)에 대한 통신 가능 옵션(method)을 설명 **(주로 CORS에서 사용)**
> CONNECT : 대상 자원(resource)에 대한 경로를 따라 메시지 루프백 테스트를 수행

### GET


