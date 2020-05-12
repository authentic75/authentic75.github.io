---
title: "정보보안기사: 어플리케이션 보안"
last_modified_at: 2020-05-12T16:20:02-05:00
categories:
  - Security
tags:
  - 정보보안기사
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
* CC인증(국제 표준 인증, 상호 인증)  
* SDLC (폭포수), 원형을 이용한 ~(Proto type)  
* 빈출: 개발 보안, DRM, Water Marking, OTP, IPSEC, FTP(항상 나옴)  
{: .notice--info} 
---
### FTP
---
* 명령 21번 포트, Active 데이터 전송 20번 포트, Passive 1024 이상 서버가 랜덤 지정  
* **/etc/ftpusers**: 파일에 적용된 사용자 접근제한 (root가 기본적으로 등록)  
* **/etc/hosts.deny**: 특정 IP의 접근 제한  
* **/etc/hosts.allow**: 특정 IP의 접근 허용  
* **xperlog**:  FTP log 파일, 필드 의미 알아 둘 것! t업로드 o는 다운로드  
	* cat /var/log/xferlog   
{: .notice} 	
```console
Mon May 31 07:48:23 2010 1 x.x.x.x 0 /home/byoungguk/1 b _ o r byoungguk ftp 0 * c
Mon May 31 07:48:25 2010 1 x.x.x.x 0 /home/byoungguk/2 b _ o r byoungguk ftp 0 * c
Mon May 31 07:48:53 2010 1 x.x.x.x 0 /home/byoungguk/3 b _ i r byoungguk ftp 0 * c
```
시간/전송(초)/접속 ip/크기/파일이름/전송/액션/direction/access/유저/서비스/인증/상태
{: .notice} 
* **전송 형태**: a(ASCII), b(binary)
* **액션 Flag**: C(압축), U(압축x), T(tar), _(아무 액션이 없다)
* **Direction**: 전송의 지시 o(outgoing), i(incoming), d(delete)
* **Access-mode**: a(anonymous), g(guest passwd 소유), r(real 인증 가능한 local user)
* **Service-name**: 보통은 FTP
* **Authentication-method**: 0(none), 1(authenticated)
* **Completion-status**: 전송상태 나타냄, c(완전한 전송), i(불완전한 전송)
{: .notice--warning} 
* tftp (UDP 이용), sftp(암호화)
* TCP 기반 (3way handshaking)
* wget으로 여러 파일 동시에 다운 가능하다.
* bounce Attack 포트 스캐닝: 가짜 메일을 보내서 익명의 ftp 서버 경유한다(Nmap -b)
{: .notice} 
---
### 웹서버 (80)
---
* **디렉토리 리스팅**: index.* 파일 없을 때 해당 경로의 모든 파일 디렉토리 노출
* **서버에 대한 정보**: 서버 토큰, 서버 signature
* **웹서버 로그 파일**: access.log
{: .notice} 
```
211.36.215.78 -  manager [22/Jun/2000:23:09:09 +0900] "GET / HTTP/1.1" 200 5  
```
요청IP/신원/user id/요청을 마친 시간/요청 데이터/상태 코드/헤더를 제외한 크기  
* 신원: identd가 제공할 클라이언트의 신원 (내부 네트웍 아니면 사용 x)
* Userid: 상태 코드가 401이면 사용자가 인증을 거치지 않은 것
* 상태 코드: 2xx(요청 성공), 4xx(클라이언트 오류), 5xx(서버 오류)
{: .notice--warning} 
---
### 소프트웨어 보안
---
* SDLC 단계별 Software 보안 활동
* Secure SDLC
* 보안기능(요구사항): 에러처리, 세션통제, …
{: .notice}
---
### SQL Injection
---
SQL  
* DDL: create table, alter table, drop table
* DML: insert, delete, update, select
* DCL: grant, revoke
{: .notice}

```
username: ' having 1=1--
username: ' group by users.id having 1=1--
username: ' union select sum(username) from users--
```
---
### 크로스 사이트 스크립트
---
* Stored: 게시판에 직접 게시
* reflective: 메일로 보내서 클릭하면 공격
* 블라인드 injection도 있다(참, 거짓을 이용하여 일일이 대입)
{: .notice}

**java, PHP: prepare statement**  
```java
PreparedStatement stmt = conn.prepareStatement("select count(*) from member where userid=? and password=?");
stmt.setString(1, userid);
stmt.setString(2, password); 
ResultSet rs = stmt.executeQuery();
```

---
### 개발 보안 입력 값 검증 및 표현
---
* 보안기능  
* 에러코드  
* 캡슐화  
* API 5형  
{: .notice}

---
### E-Mail 보안(PGP, PEM, S/MIME)
---
* PGP (Pretty Good Privacy): MIME 객체에 암호화와 전자서명 기능을 추가한 암호화 프로토콜이다.
	* 전자서명: DSS/SHA, RSA/SHA
	* 메시지 암호화: CAST-128, 3DES 1회용 
	* 세션키 생성: Difiie-Helman 혹은 RSA
	* 세그멘테이션(메시지 최대 사이즈 제한)
* PEM(Privacy Enhanced Mail): 중앙집중화된 키 인증 방식으로 높은 보안성 (군사, 은행)
* S/MIME: 표준 보안 메일 규약
	* 송/수신자 인증, 메시지 무결성
	* 첨부파일포함
	* 메일 전체를 암호화한다
	* 인터넷 MIME 메시지에 전자서명과 함께 암호화를 더함
	* RSA 암호 사용
	* CA로부터 공개키를 보증하는 인증서를 받아야한다
	* S/MIMEv3 (DSS, DES, SHA-1)
{: .notice}
---
#### Sendmail 보안
---
* 운영모드: /usr/sbin/sendmail 로 실행
	* bd: 데몬모드
	* bt: test 모드
	* bp (print), -bv (view), -bs (SMTP 실행), -bi (초기화)
* 접근 파일 포맷 (/etc/mail/access)
* 메일주소	(REJECT, DISCARD, OK, RELAY)
{: .notice}
---
#### Spam Mail 차단 방법
---
* RBL (Real Time Blocking List): RBL 서버에 특정 IP 등록 후 차단
* SPF (Sender Policy Framework): SPF 레코드를 DNS에 등록
{: .notice}

---
#### 웹서버 보안
---
* Httpd 프로세스 중 하나만 root로 실행 나머지는 apache가 실행(fork)  
* Apache 웹 서버 설정 파일: /etc/httpd/conf/httpd.conf  
보안설정  
* 주요 디렉토리 및 파일 접근 권한    
{: .notice}
```console
# chown0.bin conf logs
# chgrp0.bin conf logs
# chmod 755.bin conf logs
# chmod 511 /usr/local/httpd/bin/httpd
```

불필요한 파일 삭제  
* /var/www/manual 및 /var/www/cgi.bin 삭제
* Directory Listing
	* Index.html이 없거나 Listing을 보여주는 옵션이 설정되었는지 확인
* 심볼릭 링크에 접근하여 Root 권한 획득
	* 우선순위 결정 (index.cgi>index.html>index.htm 순서
* Server Tokens 최소한의 정보만 보이도록 설정
* Server Signature on으로 설정된 경우 아파치 버전, 이름 노출
* 클라이언트 이름, IP 등을 이용해 접근 제어 수행
{: .notice}

---
#### 웹 로그
---
접속로그
* /var/log/httpd/access_log: 서버에서 발생하는 로그 및 CGI 같은 스크립트 정보 기록
{: .notice}
```
#TransferLog /var/log/access.log  #지시어를 사용하여 로그 위치를 설정
#Isof -p 1350(포트번호)로 조회 가능
```
요청IP/신원/user id/요청을 마친 시간/요청 데이터/상태 코드/헤더를 제외한 크기  
신원: identd가 제공할 클라이언트의 신원 (내부 네트웍 아니면 사용 x)  
Userid: 상태 코드가 401이면 사용자가 인증을 거치지 않은 것  
상태 코드: 2xx(요청 성공), 4xx(클라이언트 오류), 5xx(서버 오류)  
{: .notice--warning}

에러로그
* /var/log/httpd/error_log: 에러로그 파일 기록  
* syslog: 에러 발생 시 로그를 syslog에 기록  
{: .notice}
```
#Local7.warn /var/log/httpd.warn.log #에러단계를 warn으로 설정
```
* 에이전트로그  
* 참조로그  
{: .notice}

---
#### DNS 보안
---

|   영   역   |   도  메  인   |       
| ---------- | ------------ |
| Root domain | COM, ORG, KR |
| Top level | GOOGLE, FreeBDS, NE|
| Second level | WWW, FTP(GOOGLE), WWW, KR(FREEBSD), NOBREAK(NE) |
| Third of Subdomains |WWW, FTP(KR), WWW(NOBREAK) |
| Subdomains | (*원래는 트리구조)  |

* Root domain(.): 모든 도메인의 근본이 되는 최상 level Domain  
* Top Level Domain: com, org, kr 등의 국가, 지역
* Second Level: 사용자가 도메인명을 신청해서 등록할 수 있는 영역  
{: .notice--warning}

1. Recursive Query
2. Iterative Queries
3. Response from Root Name Server
4. Query to Top Level Name Server  
5. Response from Top Level Name Server 
6. Query to Second-level Name Server
7. Response from Second-level Name Server  
8. DNS Client Get IP
{: .notice}

```console
#dnsspoof 도구로 DNS Spoofing을 할 수 있다.
#dnsspoof -I eth0 -f dns.host
```





































