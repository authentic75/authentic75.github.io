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
* rdate -p time.bora.net 시간 서버로부터 시간 확인  
* rdate -s time.bora.net 서버 시간을 set 한다.
{: .notice}
---
### mkdir
---
* mkdir -p dir2/dir20/dir200 부모 디렉토리도 함께 생성된다
* rmdir dir1 디렉토리가 비어있어야 지워짐
* rmdir -p dir1/dir10/dir100 경로 상 있는 부모 디렉토리까지 함께 지움
{: .notice}
---
### wc
---
* wc /etc/services 라인수, 워드수, 문자수 출력
* wc -l 라인수
* wc -w 단어수
* wc -c 글자수
{: .notice}
---
### cat, head, tail, more
---
* cat /etc/hosts 짧은 파일에 적합
* services 파일에 well known port 들 20개 가량 외워야한다
* /etc/services 내 local services 밑에 개인 설정 내용 기록
{: .notice}
* head /etc/services 1-10행 출력
* tail /etc/services 뒤로부터 10행 출력
* tail -n 5 /etc/services 5행 출력
* tail -n 5 /etc/passwd	최근 생성한 계정 출력 (-5 라고 써도 된다)
* more /etc/services 화면 단위로 나누어 출력, 읽고 있는 위치 알려줌
* Enter 행단위로 넘어감, space bar 페이지 단위로 넘어감, q 종료, b 이전페이지
* less /etc/services
* more와 같은 방식으로 동작한다. 추가로 /, ? 를 통한 검색기능도 지원.
{: .notice}
---
### 묶기 명령어
---
tar: 여러 파일을 1개의 파일로 묶는다
gzip: 1개의 파일을 압축한다
{: .notice}
* tar cf file /etc /etc 	디렉토리 파일들을 file로 묶어서 cf 파일 생성
* gzip file 압축 80% 정도의 압축률
* mv file.gz ~ 이동
	* 이와 같은 3단계를 일일이 실행하는 경우 파일크기가 크면 걸리는 단계별로 시간도 증가
{: .notice}
`; 와 &&를 사용 하여 시간을 단축 시켜보자`
```
[root@ns1 ~]# tar cf file1 /etc && gz file1 && mv file1.gz ~
[root@ns1 ~]# tar cf file2 /etc ; gz file2 ; mv file2.gz ~
```
`; 과 && 차이점`
```console
[root@ns1 ~]# touch file1 &&mkdir dir1&&touch file2	//dir1이 이미 존재시 에러, 그 뒤 명령어 수행x
[root@ns1 ~]# touch file2;mkdir dir2;touch file20 //에러가 나도 뒤에 명령어를 계속해서 수행
//파이프
[root@ns1 ~]# cat -n /etc/services | grep http | grep www| grep -v numbers
//(-v 옵션으로 특정 단어나 문자를 제외한다)
```
---
### File 명령어
---

* File 명령어 : 파일 타입 정보를 출력
	* file file3 
		*ASCII English text //텍스트 파일
	* file file.tar	
		*POSIX tar archive //POSIX = 유닉스 호환
	* file file2.tar.gz	
		*gzip compressed data … //압축파일
	* file cat 
		*ELF 32bit LSB excutable … //실행가능한 파일
{: .notice}
Cat 명령어 : 표준입력을 받아 표준 출력하는 명령어
{: .notice}
```
[root@ns1 ~]# cat	//실행시 표준 입력을 받아 출력 하는 모습을 보여줌 (echo 비슷)
>abc		
abc
>123
123
```
[root@ns1 ~]# cat >file1	//내용 새로 입력
[root@ns1 ~]# cat>>file1	//내용 추가 입력	ctrl+D 입력하여 종료
{: .notice}

---
### 사용자 관리와 파일 속성
---
passwd, group, shadow 를 살펴보자
{: .notice}
```
[root@ns1 ~]# more /etc/passwd
#ID : 암호 : UID : GID : comment : home : 기본 shell
```
* 비밀번호가 해싱된 키 값으로 저장된 것을 볼 수 있다.
* root에게만 UID, GID가 0으로 부여된다.
* tail -n 2 /etc/shadow
* tail -n 2 /etc/group
	* GID 찾아보면 그룹명이 나온다
* usermod -G root user1	user1의 그룹을 root에 추가
{: .notice}
`사용자 생성 과정`  
1. /etc/passwd 파일에 사용자 정보 등록  
2. /etc/shadow 파일에 사용자 암호 등록  
3. /home 아래에 사용자 홈 디렉토리 생성  
4. /etc/skel 디렉토리의 파일들을 복사해서 사용자 홈 디렉토리에 넣어준다  
{: .notice}
User : Encrpted_Passwd : create date : Last Passwd : min : max : warning : inactive : expire : unused Create date : 
{: .notice--info}
* 1970-01-01 로부터 지난 일 수
* Min : 패스워드 변경전 최소 사용기간
* Max : 패스워드 변경전 최대 사용기간
* Warning : 경고메세지 제공일
* Inactive : 로그인 차단 접속 일수
* Expire : 로그인 사용 금지 일 수
{: .notice--warning}
```
[root@ns1 ~]# tail -n 2 /etc/gshadow		그룹의 암호 정보 저장
[root@ns1 ~]# userdel -r user1			계정과 홈디렉토리 모두 삭제
```
* 사용자 생성시 자동으로 부여되는 기본 값들
1.	UID는 마지막으로 생성된 UID에서 +1된 값을 부여
2.	사용자 홈디렉토리는 /home 아래에 생성
3.	사용자 기본 shell (bin/bash)
{: .notice}

bsh->csh->tcsh->ksh->bash 순으로 발전되었다.
{: .notice}
```
[root@ns1 ~]# useradd -u 490 user3		//UID 지정
[root@ns1 ~]# useradd -s /bin/ksh user5	//shell 변경
[root@ns1 ~]# useradd -g root user6		//group을 root로 지정
```
```
[root@ns1 ~]# useradd -G root user2		 //추가 그룹
//passwd 파일로는 확인 안됨. Groups로 봐야지 나온다
[root@ns1 ~]# useradd -G user1, user2 user8 //두개 이상의 그룹 나열 가능
```
```
[root@ns1 ~]# useradd -d [홈디렉토리]		//홈디렉토리 지정 가능
```

* 일반계정으로 로그인 후 su 명령어로 root 권한 사용 가능  
* user1의 UID를 0으로 바꿔주면 root 계정 처럼 사용 가능하다.  
{: .notice}
```
[root@ns1 ~]# useradd -u 0 -o user20		//UID를 root와 같이 0으로 변경 -o는 중복 값 허용
```
* user1의 GID가 0으로 바뀜 그룹이 root로 변경됨.
* user2의 UID가 0으로 바뀜 그룹은 user2와 root 둘 다 가진다.
{: .notice}
```
[root@ns1 ~]# useradd -d /home2 user2	로 home2에 계정 생성이 가능하다
[root@ns1 ~]# useradd -D 		/etc/default/useradd (환경변수) 출력
[root@ns1 ~]# useradd -D -b /home2		기본 home을 home2로 변경
[root@ns1 ~]# useradd -m user5	수동으로 home 생성 지시
[root@ns1 ~]# useradd -M user5	home을 만들지 않도록 지시
```
-s는 기본 shell 변경
{: .notice}

* /etc/login.defs에 계정 관련 설정된 값들이 기록됨  
* PASS_MAX_DAYS 99999  
* PASS_MIN_DAYS 0  
* UID 및 GID 범위 등등  
* /etc/shadow 에서 볼 수 있는 값들이 주로 기록되어있다. Shadow 파일이 login.defs 파일을 참고하여 계정 정보를 생성한다는 뜻이다.
* UID_MIN과 GID_MIN을 1000으로 변경하면 이후 생성하는 계정의 UID, GID가 1000부터 생성
* rdate -s time.bora.net 으로 날짜 세팅이 가능하다.
{: .notice}

*/etc/login.defs
	* USERDEL_CMD /usr/sbin/userdel_local 은 userdel 하면 자동으로 실행되는 부분이다.
	* UMASK 설정도 가능 하다
{: .notice}
```
[root@ns1 ~]# groupadd -g 401 admin
[root@ns1 ~]# groupadd -g 402 product
[root@ns1 ~]# groupadd -g 403 sales

[root@ns1 ~]# useradd user1
[root@ns1 ~]# useradd -G product user2

[root@ns1 ~]# usermod -G sales user1
[root@ns1 ~]# usermod -d /home2/user1 user1
[root@ns1 ~]# usermod -s /bin/ksh user2
```








