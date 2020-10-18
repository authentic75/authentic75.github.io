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

주요 요청 메소드
{: .notice--info}

| 메소드 | 설명 |  
| ---- | ----------------------------- |  
| GET | 지정된 리소스 요청 |
| POST | 웹 서버에 저장하거나 업데이트 할 데이터를 전송 |
| HEAD | 응답 헤더만 전송 요청 |  
| PUT | 지정된 리소스를 생성하거나 업데이트 |  
| DELETE | 지정된 리소스를 삭제 |  

주요 요청 헤더
{: .notice--info}

| 요청 헤더 | 설명 |  
| --------- | ---------------------------------- |  
| Accept | 수용 가능한 미디어 타입 |  
| Accept-Charset | 수용 가능한 문자 세트 | 
| Accept-Encoding | 수용 가능한 인코딩 방법 |   
| Accept-Language | 수용 가능한 자연어 세트 |  
| Accept-Datatime | 수용 가능한 날짜, 시간 |  
| Authorization | HTTP 인증을 위한 개인정보 |
| Connection | 현재 연결에 대한 제어 옵션 | 
| Cookie | 키:값 으로 되어있는 쿠키 |   
| Expect | 클라이언트가 필요로 하는 서버의 동작 |   
| Forwarded | HTTP 프록시를 통한 연결에 대한 원래 정보 노출 |   
| From | 요청을 한 사용자의 이메일 주소 |   
| If-Modified-Since | 콘텐츠가 수정되지 않았다면 304 오류를 허용 |   
| If-Range | 지정 엔터티가 수정되지 않았다면 그 부분만 응답하고 수정되었다면 새로운 엔터티 모두 보냄 |   
| if-Unmodified-Since | 지정된 시간 이후 수정되지 않은 콘텐츠만 전송함 |   
| Range | 지정한 범위 만큼 콘텐츠를 요청함 |   
| Via | 요청을 처리한 프록시 서버에 대한 정보 |   
| Warning | 엔터티 body에 있을 수 있는 문제들에 대한 일반적 경고 |  

주요 응답 헤더
{: .notice--info}

| 응답 헤더 | 설명 |  
| --------- | ---------------------------------- |   
| Accept-Ranges | 부분 콘턴츠 범위를 지웒나는 단위(보통 바이트) |   
| Age | 리소스 객체가 프록시 캐시에 머물고 있던 시간(초) |    
| Cache-Control | 서버가 전송한 내용을 클라이언트가 캐시할 시간(초) |   
| Connection | 현재 연결에 대한 제어 옵션 |    
| Content-Encoding | 응답 데이터 인코딩 타입 |   
| Content-Length | 응답 데이터 크기(바이트) |   
| Content-Range | 전송한 데이터가 부분일 경우 전체에서 차지하는 범위 |   
| Content-Type | 전송한 데이터의 MIME 타입 |   
| Date | 응답 메세지가 생성된 날짜와 시간 |   
| Etag | 특정 리소스의 ID, 보통 해시 값으로 지정 |   
| Expire | 응답 데이터의 만료 시간 (날짜, 시간) |   
| Location | 리다이렉션 할 위치 |   
| Server | 웹 서버 이름 |   
| Set-Cookie | HTTP 쿠키 |   
| WWW-Authenticate | 요청 데이터에 접근하기 위한 인증 방법 |    

주요 상태 코드
{: .notice--info}

| Code | 설명 |     
| --------- | ---------------------------------- |     
| 200(OK) | HTTP 요청에 대한 응답 성공 |   
| 301(Moved Permanently) | 앞으로는 지금 응답한 URI로 계속 요청해야 함 |     
| 304(Not Modified) | HTTP 요청에 If-Modified-Since 헤더가 지정된 경우, 리소스가 수정되지 않았음을 알려줌 |     
| 307(Temporarily Redirect) | 응답한 URI로 임시 요청함 |    
| 400(Bad Request) | HTTP 요청이 잘못됨 |   
| 401(Unauthorized) | 인증이 되지 않음. WWW-Authenticate 헤더로 방법을 알려줌 |   
| 403(Forbidden) | 요청한 리소스에 접근 권한 없음 |   
| 404(Not Found) | 요청한 리소스가 존재하지 않음 |   
| 405(Method Not Allowed) | 허용되지 않은 메소드를 사용했음 |   
| 500(Internal Server Error) | 서버 내부 요류로 인한 응답 실패 |   
 
---
### 쿠키 조작하기
---

---
#### 쿠키 스니퍼
---

---
#### 쿠키 스푸핑
---

---
### 웹 링크 크롤러 
---

---
### 서버 스캔
---
