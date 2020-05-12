---
title: "정보보안기사: 네트워크보안"
last_modified_at: 2020-05-11T16:20:02-05:00
categories:
  - Security
tags:
  - 정보보안기사
toc: true 
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
---
### HTTP
---
---
#### HTTP1.0 헤더
---
**HTTP Request**  
Request Method: GET, POST, PUT, DELETE, TRACE, CONNECT
Accept: 웹브라우저에서 사용 가능한 미디어 타입, text/*, text/html 등
Accept-Language: 웹브라우저가 인식 할 수있는 언어
User-Agent: 웹브라우저 정보를 의미
Accept-Encoding: 웹브라우저에서 제공되는 인코딩 방식
Host: 웹 서버의 기본 URL 의미
Connection: 연결 지속 및 연결 끊기를 나타내며 Keep Alive 혹은 close
{: .notice--info}
**Response Header**  
Status Code: HTTP 응답코드를 의미하고 200은 성공적으로 처리한 것을 의미  
Server: 서버 프로그램 이름과 버전 전송   
Expires: 자원의 만기 일자  
Cache Control: 캐시 사용 여부  
Pragma: 캐시 사용 여부를 나타냄  
Content-Encoding: 응답되는 메세지의 인코딩 방식  
Content-Length: 바이트 단위로 응답되는 리소스의 크기   
Keep-Alive: 연결 유지 시간을 의미하며 timeout, max 설정  
Connection: Keep-Alive 사용 여부를 의미  
Content-Type: 응답되는 미디어 타입  
{: .notice--info}
---
#### HTTP 상태코드
---
**100번대 조건부 응답**으로 요청을 받으면 작업을 계속한다.  
**200번대 클라이언트**가 요청한 동작을 수신 후 성공적으로 처리   
* 204: 서버가 요청을 성공적으로 처리했지만 콘텐츠를 제공하지 않는다.  
**300번대 리다이렉션 완료**, 클라이언트의 추가 동작이 필요하다.  
**400번대 요청 오류**로 클라이언트에 오류가 있음을 의미한다.  
* 400: 서버가 요청의 구문을 인식하지 못하였다.  
* 404: 서버가 요청한 페이지를 찾을 수 없다.  
**500번대 서버 오류**로 서버가 유효한 요청을 수행하지 못했다.  
* 500: 서버에 오류가 발생하여 요청을 수행 할 수 없다.  
{: .notice}
---
#### HTTP1.1 헤더
---
HTTP/1.0과 1.1 Entity 차이  
entity-hader의 헤더 필드가 다수 추가되었다.  
{: .notice}
Allow  
Content-Encoding  
Content-Length  
Content-Type  
Expires  
Last-Modified  
{: .notice}
**HTTP/1.1에서는 위 필드에 더해 다음과 같은 것들이 추가되었다.**  
Content-Base  
Content-Language  
Content-Location  
Content-MD5  
Content-Range  
ETag  
{: .notice}
---
#### HTTP1.1 특징
---
성능의 향상과 요구되는 시간의 최소화  
persistent connection  
pipeline  
데이터의 압축  
proxy server와 cache의 사용  
{: .notice}



































