---
title: "LINUX: bash와 script"
last_modified_at: 2020-05-11T16:20:02-05:00
categories:
  - LINUX
tags:
  - bash
  - LINUX
  - shell
  - script
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
author_profile: true #작성자 프로필 출력여부 #false
read_time: false # read_time을 출력할지 여부 1min read 같은것!
---
### bash의 기본
---
**bash의 특징**  
{: .notice}
```console
[root@ns1 ~]# alias lls=”ls -lh”   #명령어에 별칭을 붙여줄 수 있다. 명령어 단축기능
[root@ns1 ~]# history # ! , !!, !-3  히스토리 기능
[root@ns1 ~]# jobs #job control 기능
[root@ns1 ~]# fg % 1 #job control 기능
```
그 외,  
* 명령문 처리 기능  
* 자동완성 기능 (Tab)  
* 프롬프트 제어 기능  
* 연산 기능  
* 명령 편집 기능  
* 환경 변수 기능
{: .notice}
---
### 환경 변수
---
셸은 여러가지 환경 변수를 값을 갖는데, 설정된 환경 변수는 echo $환경변수이름 형식으로 명령을 실행하면 확인할 수 있다.  
$HOME $PS1 $PWD $LANG $HOSTNAME  .. 이러한 환경변수를 사용하여 명령어를 더 쉽게 실행 할 수 있도록 한다.  
env 명령을 사용하면 환경변수가 어떤것들이 있는지 전부 볼 수 있다.  
{: .notice}
```console
[root@ns1 ~]# env | grep HISTSIZE #HISTSIZE가 뭔지 알아보자
[root@ns1 ~]# ls -lh /.bash_history
-rw------- 1 root root 349  5월  6 09:53 /.bash_history
[root@ns1 ~]# cat /.bash_history | tail -n 10
[root@ns1 ~]# HISTZISE=500 #바꿔 줄 수도 있다.
```
변수에는 두가지 종류가 있습니다.  
**지역변수(로컬변수), 쉘 변수**  
**전역변수(글로벌변수), 환경 변수**  
{: .notice}
```console 
[root@ns1 ~]# ABC=”Local Variable”
[root@ns1 ~]# export XYZ=”Global Variable” #Export 사용하면 전역 변수로 사용 할 수 있다.
[root@ns1 ~]# echo $ABC
[root@ns1 ~]# echo $XYZ
[root@ns1 ~]# /bin/ksh 	#Ksh을 실행하여 지역변수와 전역변수 테스트
						#Ksh로 다시 들어가서 $ABC를 부르면 호출이 안된다.
						#하지만 글로별 변수인 XYZ는 사용 가능하다.
[root@ns1 ~]# ABC=”Local Variable2” 생성
[root@ns1 ~]# echo $ABC

[root@ns1 ~]# /bin/tcsh	#tcsh을 실행하여 다시 지역변수와 전역변수를 출력해본다
[root@ns1 ~]# echo $XYZ
[root@ns1 ~]# echo $ABC	#XYZ는 출력가능하지만 ABC는 호출이 안된다
[root@ns1 ~]# ABC=”Local Variable3”
```
---
#### Set과 Env
---
set은 지역, 전역변수를 모두 볼 수있다.  
env는 전역변수만 볼 수있다.
{: .notice}
```console
[root@ns1 ~]# set ABC=”local Variable3”
[root@ns1 ~]# echo $ABC
[root@ns1 ~]# echo $XYZ

[root@ns1 ~]# unset XYZ #환경변수에서 지워진다 (set에서도 env에서도 전부 지워짐)
[root@ns1 ~]# export -n ABC #전역변수에서만 제거 가능하다

[root@ns1 ~]# ABC="test1"
[root@ns1 ~]# export XYZ="test2"
[root@ns1 ~]# set | grep "ABC\|XYZ"
ABC=test1
XYZ=test2
_=XYZ

[root@ns1 ~]# export ABC
[root@ns1 ~]# env | grep "ABC\|XYZ"
ABC=test1
XYZ=test2
[root@ns1 ~]# export -n ABC
[root@ns1 ~]# env | grep "ABC\|XYZ"
XYZ=test2
[root@ns1 ~]# set | grep "ABC\|XYZ"
ABC=test1
XYZ=test2

[root@ns1 ~]# unset XYZ
[root@ns1 ~]# set | grep "ABC\|XYZ"
ABC=test1
_=XYZ
[root@ns1 ~]# env | grep "ABC\|XYZ"
```
