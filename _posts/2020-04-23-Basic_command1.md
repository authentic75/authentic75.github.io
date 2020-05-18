---
title: "LINUX: 기본명령어3"
last_modified_at: 2020-04-23T16:20:02-05:00
categories:
  - Security
tags:
  - LINUX
  - rdate
  - cat
  - head
  - tail
  - more
  - pipe
  - tar
  - file
  - useradd
  - usermod
  - groupadd
  - groupmod
  - passwd
  - gpasswd
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
---
### rdate
---
* rdate -p time.bora.net	시간 서버로부터 시간 확인  
* rdate -s time.bora.net	서버 시간을 set 한다.
{: .notice}
---
### mkdir
---
* mkdir -p dir2/dir20/dir200	부모 디렉토리도 함께 생성된다
* rmdir dir1	디렉토리가 비어있어야 지워짐
* rmdir -p dir1/dir10/dir100	경로 상 있는 부모 디렉토리까지 함께 지움
{: .notice}
---
### wc
---
* wc /etc/services		라인수, 워드수, 문자수 출력
* wc -l			라인수
* wc -w			단어수
* wc -c			글자수
{: .notice}
---
### cat, head, tail, more
---
* cat /etc/hosts	짧은 파일에 적합
* services 파일에 well known port 들 20개 가량 외워야한다
* /etc/services 내 local services 밑에 개인 설정 내용 기록
{: .notice}
* head /etc/services	1-10행 출력
* tail /etc/services		뒤로부터 10행 출력
* tail -n 5 /etc/services	5행 출력
* tail -n 5 /etc/passwd	최근 생성한 계정 출력 (-5 라고 써도 된다)
* more /etc/services	화면 단위로 나누어 출력, 읽고 있는 위치 알려줌
* Enter 행단위로 넘어감, space bar 페이지 단위로 넘어감, q 종료, b 이전페이지
* less /etc/services
* more와 같은 방식으로 동작한다. 추가로 /, ? 를 통한 검색기능도 지원.
---
### 묶기 명령어
---
