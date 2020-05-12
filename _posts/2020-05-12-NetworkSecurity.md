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
* 100번대 조건부 응답으로 요청을 받으면 작업을 계속한다.  
* 200번대 클라이언트가 요청한 동작을 수신 후 성공적으로 처리   
204: 서버가 요청을 성공적으로 처리했지만 콘텐츠를 제공하지 않는다.  
* 300번대 리다이렉션 완료, 클라이언트의 추가 동작이 필요하다.  
* 400번대 요청 오류로 클라이언트에 오류가 있음을 의미한다.  
400: 서버가 요청의 구문을 인식하지 못하였다.  
404: 서버가 요청한 페이지를 찾을 수 없다.  
* 500번대 서버 오류로 서버가 유효한 요청을 수행하지 못했다.  
500: 서버에 오류가 발생하여 요청을 수행 할 수 없다.  
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
---
### SMTP
---
MUA(Mail User Agent): 메일을 작성하여 보내는 프로그램  
MTA(Mail Transfer Agent): 이용자로부터 메일을 받아서, 외부로 전달 (sendmail)  
MDA(Mail Delivery Agent): 전송받은 메일을 해당 사용자에게 전달 (procmail, mail)  
POP3(Post Office Protocol): 메일을 전송받을 때 사용되는 프로토콜  
IMAP(Internet Mail Access Protocol): 메일을 전송받을 때 사용되는 프로토콜  
{: .notice}
---
### SMTP
---
SMTP도 TCP 기반 프로토콜이라 3way hand shaking 과정을 거친다.  
1. TCP 3 way handshakig  
2. 220, Ready  
3. **EHLO(세션초기화, 확정 목록 요청)**  
4. 250, OK  
5. Data 전송  
6. QUIT  
7. 221, Good bye  
{: .notice--info}  
메일 전송  
1. **MAIL FROM(보내는이 알림)**  
2. 250, OK
3. **RCPT TO(받는이 주소 지정)**  
4. 250, OK   
5. Data 전송  
6. 마침표 전송  
7. 250, OK  
{: .notice--info}
---
### 라우팅 프로토콜
---
**라우팅 프로토콜 종류**  
Distance Vector: Hop Count를 정보로 최단 경로를 결정, 메모리 부담  
RIP, IGRP  
Link State: Link 변화 발생 시 Hop Count 및 Bandwidth, Delay 등 다양한 Link 정보를 가지고 경로 설정, Traffic 유발    
OSPF, EIGRP  
Path Vector: (하이브리드) 정책기반으로 라우팅 정보 업데이트  
BGP  
{: .notice--info}
---
#### RIP
---
거리 벡터 라우팅 프로토콜  
16 Hop 이상은 폐기  
180초 이내에 새로운 라우팅 정보 수신이 안되면 경로 이상으로 간주  
거리값을 비교하여 라우팅 테이블 변경  
모든 망에 변경사항 적용(큰규모 망에는 적합하지 않다)  
{: .notice}
---
#### IGRP
---

---
#### OSPF
---
Link State Routing Protocol  
대규모 IP 망  
전송 시간을 링크 비용으로 사용  
변화 발생시 링태 상태 정보 교환  
Hop 수 제한 없음, delay, throughput, relability 이용  
네트워크를 Area로 구분하여 성능저하 예방, 대역폭 절약  
Link 변화 감지시 해당 Link에 대한 정보만을 즉시 모든 라우터에 전달  
Supernetting 라우터 메모리 절약, 성능 향상 및 대역폭 절약  
{: .notice}
---
#### EIGRP
---

---
#### BGP
---
AS 간의 Routing에 적용되는 프로토콜, ISP 사업자들 간에 사용  
EGP (inter domain routing) 특징을 가짐  
경로 벡터 라우팅 방법 적용, 경로 벡터 라우팅 테이블 유지  
TCP 기반 routing  
변화가 있을때 neighbor에게 갱신정보 advertising함  
다양한 routing metric 사용  
CIDR 지원  
IBGP(internal BGP): 동일한 AS에 속한 라우터  
EBGP(External BGP): 서로 다른 AS에서 동작  
{: .notice}
---
#### CIDR
---
Classes Inter Domail Routing  
기존 클래스 마스크보다 더 적은 마스크 사용하여 요약  
(IPv4 주소 공간을 더 효율적으로 사용)  
-> 불필요한 라우팅 업데이트 트래픽 감소  
{: .notice}
* default 클래스풀 마스크 보다 더 적은 마스크들을 사용해서 요약한다.  
* 라우팅 업데이트 안에 있는 엔트리들의 수를 감소   
* 로컬 라우팅 테이블에 있는 엔트리들의 수를 감소   
* 라우팅 업데이트들에 대한 대역폭 이용률을 감소  
* 더 빠른 라우팅 테이블 룩업을 가능  
{: .notice}
IGRP + CIDR, VLSM = EIGRP 개발  
RIPv2 부터 CIDR 지원  
Ex) /24, /22 꼴로 나타냄  
{: .notice--info}


















