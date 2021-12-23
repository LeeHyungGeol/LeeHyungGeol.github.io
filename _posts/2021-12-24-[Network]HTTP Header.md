---
layout: single
title: "[Network] HTTP Header"
categories: Network
tag: [Network, HTTP, HTTP Header]
toc: true
author_profile: false
search: true
---

# HTTP Header

> **header-field = field-name ":" OWS field-value OWS (OWS: 띄어쓰기 허용, field-name은 대소문자 구문 없음)**

**HTTP 전송에 필요한 모든 부가정보**
- EX) message-body의 내용, message-body의 크기, 압축, 인증, request client(browser) 정보, server application 정보, 캐시 관리 정보...
- 표준 헤더가 너무 많음
- 필요시 임의의 헤더 추가 가능
  - EX) `testHeader: test`
- EX) `Content-Type: text/html;charset=UTF-8 Content-Length:3423`



