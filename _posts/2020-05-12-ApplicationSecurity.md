---
title: "정보보안기사: 어플리케이션 보안"
last_modified_at: 2020-05-11T16:20:02-05:00
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
보안기능  
에러코드  
캡슐화  
API 5형  
{: .notice}