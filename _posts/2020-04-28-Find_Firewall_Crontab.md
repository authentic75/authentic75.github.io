---
title: "LINUX: 파일 위치 검색, 예약 작업"
last_modified_at: 2020-04-28T16:20:02-05:00
categories:
  - LINUX
tags:
  - LINUX
  - Find
  - which
  - whereis
  - Firewall
  - Crontab
  - at
toc: true 
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
---
### 파일 위치 검색
---
* Which	: PATH에 등록된 경로들만 검색하여 명렁어를 찾아주는 명령어
* Locate : 파일목록이 저장 되어있는 DB내용을 기반으로 모든 종류의 파일을 찾아주는 명령어
	* (실제 검색을 하지 않고 색인(DB)만 검색하기 때문에 검색 결과가 틀릴 수 있다)
* Whereis : 명령어와 문서(man)을 찾아주는 명령어
* Find	: 파일명, 권한, 소유주(그룹), 날짜, 용량 등 다양한 옵션을 이용하여 검색하는 명령어. 
검색된 결과를 추가적으로 후처리까지 가능하다
{: .notice}
```console
[root@ns1 ~]# which cat
```
```console
[root@ns1 ~]# whereis -b useradd
[root@ns1 ~]# whereis -m useradd
```
```console
[root@ns1 ~]# locate telnet # 색인 갱신이 안되어있으면 검색 결과에 나타나지 않음
[root@ns1 ~]# updatedb
```
---
### Find
---
#find [경로] [-옵션] [조건] [액션]
{: .notice}
* 경로: 지정한 경로에서부터 하위 디렉토리를 검색
* 옵션 [조건]
	* name 파일명, 디렉토리명	: 파일명으로 검색
	* user 사용자			: 소유주로 검색
	* group 그룹			: 그룹으로 검색
	* newer 파일			: 지정한 파일 보다 최신 파일 
	* mtime/ -atime [+ -] N : modify accesstime 으로 검색
		* N 지금으로부터 N일 전
		* +N 지금으로부터 N일 초과 
	* N N일 미만
	* size [+ -]용량[kMG]		: 용량으로 검색
	* perm 권한			: 권한을 기준으로 파일을 검색
	* perm -권한			: 특정 권한만을 기준으로 파일을 검색
	* empty	: 비어있는 파일만 출력함
	* type [d , f] : 파일 형태에 따라서 검색
* 액션
	* print	: (기본값) [경로]파일명만 출력. UNIX에서는 기본값이 아니어서 안 쓰면 에러가 난다.
	* ls	: ls -lis 결과로 출력된다
	* delete	: 검색된 결과를 지워준다.
	* exec 명령어 {파일,파일… } \; : 원하는 명령어로 검색된 파일을 처리 할 수 있다.
* 연산자
	* or	
	* and	
	* not	
{: .notice--info}
```console
[root@ns1 ~]# find /etc -name ifcfg*
[root@ns1 ~]# find /etc -empty ifcfg*
[root@ns1 ~]# find /etc -empty -and -type d
[root@ns1 ~]# find /etc -empty -and -type f
[root@ns1 ~]# find /data -group user1 -delete

[root@ns1 ~]# find . -newer file4
[root@ns1 ~]# ls -lh --time=atime
[root@ns1 ~]# find . -mtime 4 # 4일전에 변경된 파일을 찾아줘 -4 4일 안쪽 +4 4일 지난
[root@ns1 ~]# find . -atime 2

[root@ns1 ~]# find / -perm u=rwxs, go=rx -ls
[root@ns1 ~]# find / -perm 4755 -ls
[root@ns1 ~]# find / -perm 4000 -ls
[root@ns1 ~]# find / -perm -2000 -ls -or -perm -1000 -ls
[root@ns1 ~]# find / \(-perm -2000 -or -perm -1000\) -ls

[root@ns1 ~]# find /etc/ -size 2M -ls
[root@ns1 ~]# find /etc -size +2M -exec ls -lh {} \;
[root@ns1 ~]# find /etc -size -2M -exec cp {} . \;
```
---
### Firewall 설정
---
```console
[root@ns1 ~]# Iptables
[root@ns1 ~]# Firewall-cmd
[root@ns1 ~]# /etc/services
[root@ns1 ~]# ntsysv
[root@ns1 ~]# chkconfig NetworkManager on
```
---
### 예약 작업
---
---
#### at
---
at	:		1번의 예약 작업을 설정
at -l	:		예약 작업 목록
at -r	<번호> :	예약 작업 삭제
atrm  <번호>:		예약 번호 삭제
{: .notice--info}
```console
[root@ns1 ~]# at now +5 min		#5분뒤
[root@ns1 ~]# at new +2 hour	#1시간뒤
[root@ns1 ~]# at 22:00			#오후 10시
[root@ns1 ~]# at 10:00 pm		#오후 10시
[root@ns1 ~]# at 22:00 tomorrow	#내일 오후 10시
[root@ns1 ~]# at 22:00 april 29	#4월 29일 오후 10시에 실행
```
---
#### cron
---
* cron	:	주기(반복)적인 예약 작업을 설정
* crontab -e : 예약 작업을 편집(등록, 삭제)한다.
* crontab -l : 예약 작업 목록을 한다.
* crontab -r: 예약 작업을 제거한다.
* contab -e 명령어로 편집화면으로 들어감. Vi 편집와 유사하다.
{: .notice--info}
```console
[root@ns1 ~]# crontab -e
[root@ns1 ~]# crontab -l
0 4 * * *	date > /backup/result
0 4 * * 1-5	date > /backup/$DATE
0 4 * * 6,7	date > /backup/$DATE_Weekend
```
분(0-59)	시(0-23)	일(1-31)	월(1-12)	요일(0-7) 예약작업
{: .notice--warning}

```console
[root@ns1 ~]# echo $LANG
ko_KR.UTF-8
[root@ns1 ~]# date 04281414
[root@ns1 ~]# date +%y
[root@ns1 ~]# date +%Y -%m -%d -%H -%M
```
```console
[root@ns1 ~]# DATE= `date +%Y -%m -%d`
2020-04-28
```
date 출력 포맷 바꾸기
{: .notice}
```console
[root@ns1 ~]# touch workday.sh weekday.sh
[root@ns1 ~]# cat workday.sh
#!/bin/bash
PATH=/bin
LANG=en_US.UTF-8
DATE=`date +%Y-%m-%d`
date > /backup/$DATE-workday.txt
[root@ns1 ~]# workday.txt > weekday.txt
[root@ns1 ~]# vi weekday.sh
[root@ns1 ~]# cat weekday.sh
#!/bin/bash
PATH=/bin
LANG=en_US.UTF-8
DATE=`date +%Y-%m-%d`
date > /backup/$DATE-weekday.txt
[root@ns1 ~]# crond restart
```
```console
[root@ns1 ~]# crontab -e
[root@ns1 ~]# crontab -l
*/2 * * * *	date > /backup/result #짝수분 마다
*/5 * * * 1-5	date > /backup/$DATE #5분 마다
```


























