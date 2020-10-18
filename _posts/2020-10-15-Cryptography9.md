---
title: "Cryptography: 웹해킹"
last_modified_at: 2020-10-13T00:26:02-05:00
categories:
  - Cryptography
tags:
  - 암호학
  - 파이썬
  - 암호와 해킹 1판
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

`이 게시물은 화이트해커를 위한 암호와 해킹 1판을 참고하여 작성하였습니다.`

---
### HTTP에 대해 알아보자
---

* HTTP프로토콜을 이용하여 서비스 요청을 받고 서비스를 제공한다.
* 웹브라우저는 서비스를 요청하는 대표적인 도구다.
* 웹서버는 웹브라우저의 요청에 대해 HTML 페이지를 구성하여 HTTP 프로토콜을 이용하여 응답한다.
{: .notice--info}

현재 웹은 가장 인기있는 공격 대상이 되었다. 공격자들은 웹브라우저를 유용한 공격 도구로 활용하고 있다. 따라서 HTTP에 대한 이해가 필요하다.
{: .notice}


HTTP는 응용 계층 프로토콜로 일반 텍스트로 되어있는 Stateless Protocol 이다. Stateless Protocol의 특징은 이전 요청과는 무관하게 독립된 트랜잭션을 구성한다.
현재 가장 많이 사용되는 버전은 HTTP/1.1 버전이다.
{: .notice--info}

```
#HTTP 요청 메세지 구조
Request-Method Request-URI HTTP-Version(CRLF)
HTTP-Headers(CRLF)(CRLF)

Body
```

HTTP 요청 메세지의 구조는 위와 같다. 여기서 CRLF는 줄바꿈 문자다.
{: .notice}

```
#요청 메세지 예시
GET /index.html HTTP/1.1(CRLF)
Host:www.site.com(CRLF)(CRLF)
```

요청할 리소스의 경로 /index.html 를 입력하고 웹호스트 도메인을 지정해서 전송한다.
{: .notice}

```
#HTTP 응답
HTTP-Version Status-Code Reason-Phrase(CRLF)
HTTP-Headers(CRLF)(CRLF)

Body
```

콜론을 통해 헤더와 값을 구분한다.
{: .notice}

```
#응답 메세지 예시
HTTP/1.1 200 OK
Date:
Server:
Last-Modified:
ETag:
Content-Type:
Content-Length:
Accept-Ranges:
Connection:

<html>
...

</html>
```

---
### HTTP/1.1 스펙의 일부를 살펴보자
---

| 메소드 | 설명 |  
| ---- | ----------------------------- |  
| GET | 지정된 리소스 요청 |
| POST | 웹 서버에 저장하거나 업데이트 할 데이터를 전송 |
| HEAD | 응답 헤더만 전송 요청 |
| PUT | 지정된 리소스를 생성하거나 업데이트 |
| DELETE | 지정된 리소스를 삭제 |























