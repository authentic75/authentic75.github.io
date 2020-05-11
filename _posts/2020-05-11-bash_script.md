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
```console
[root@ns1 ~]# alias lls=”ls -lh”   #명령어에 별칭을 붙여줄 수 있다. 명령어 단축기능
[root@ns1 ~]# history # ! , !!, !-3  히스토리 기능
[root@ns1 ~]# jobs #job control 기능
[root@ns1 ~]# fg % 1 #job control 기능
```
* 그 외,
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
* 셸은 여러가지 환경 변수를 값을 갖는데, 설정된 환경 변수는 echo $환경변수이름 형식으로 명령을 실행하면 확인할 수 있다.  
* $HOME $PS1 $PWD $LANG $HOSTNAME  .. 이러한 환경변수를 사용하여 명령어를 더 쉽게 실행 할 수 있도록 한다.  
* env 명령을 사용하면 환경변수가 어떤것들이 있는지 전부 볼 수 있다.  
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
login(로그인쉘) > bash(서브쉘) > ksh(서브쉘)  
Local > 사라짐 > 사라짐  
Global > 남아있다 > 남아있다
{: .notice}
```console 
[root@ns1 ~]# ABC=”Local Variable”
[root@ns1 ~]# export XYZ=”Global Variable” #Export 사용하면 전역 변수로 사용 할 수 있다.
[root@ns1 ~]# echo $ABC
[root@ns1 ~]# echo $XYZ
```
```console 
[root@ns1 ~]# /bin/ksh
# pstree | tail -n 10 #bash아래 ksh가 fork됨
# ABC=”Local Variable2” 생성
# echo $ABC
#Ksh을 실행하여 지역변수와 전역변수 테스트
#Ksh로 다시 들어가서 $ABC를 부르면 호출이 안된다
#하지만 글로별 변수인 XYZ는 사용 가능하다
```
```console 
# /bin/tcsh	#tcsh을 실행하여 다시 지역변수와 전역변수를 출력해본다
# echo $XYZ
# echo $ABC	#XYZ는 출력가능하지만 ABC는 호출이 안된다
# ABC=”Local Variable3”
```
---
#### set과 env 명령어
---
**set**은 지역, 전역변수를 모두 볼 수있다.  
**env**는 전역변수만 볼 수있다.
{: .notice}
set ABC=”local Variable3”  
unset XYZ  
export XYZ="test2"    
export -n ABC    
위의 네가지 명령어를 사용해보자
{: .notice}
예제1
```console
[root@ns1 ~]# set ABC=”local Variable3”
[root@ns1 ~]# echo $ABC //지역
[root@ns1 ~]# echo $XYZ //전역

[root@ns1 ~]# unset XYZ #환경변수에서 지워진다 (set에서도 env에서도 전부 지워짐)
[root@ns1 ~]# export -n ABC #전역변수에서만 제거 가능하다

[root@ns1 ~]# ABC="test1"
[root@ns1 ~]# export XYZ="test2"
[root@ns1 ~]# set | grep "ABC\|XYZ" #지역, 전역변수 출력  
```
```
ABC=test1
XYZ=test2
_=XYZ
```
예제2
```console
[root@ns1 ~]# export ABC
[root@ns1 ~]# env | grep "ABC\|XYZ" #전역변수만 출력
```
```
ABC=test1
XYZ=test2
```
예제3
```console
[root@ns1 ~]# export -n ABC
[root@ns1 ~]# env | grep "ABC\|XYZ" #전역변수만 출력
```
```
XYZ=test2			#ABC 삭제됨
```
예제4
```console
[root@ns1 ~]# set | grep "ABC\|XYZ" #지역, 전역변수 출력  
```
```
ABC=test1
XYZ=test2
```
예제5
```console
[root@ns1 ~]# unset XYZ
[root@ns1 ~]# set | grep "ABC\|XYZ" #지역, 전역변수 출력  
```
```
ABC=test1
_=XYZ				#삭제됨
```
예제6
```console
[root@ns1 ~]# env | grep "ABC\|XYZ" #전역변수 출력
```
---
### 쉘 스크립트
---
```console
[root@ns1 ~]# touch /bin/name.sh
[root@ns1 ~]# ls -lh /bin/name.sh
```
```
-rw-r--r-- 1 root root 0  5월  6 11:48 /bin/name.sh
```
```console
[root@ns1 ~]# chmod a+x /bin/name.sh
[root@ns1 ~]# ls -lh /bin/name.sh
```
```
-rwxr-xr-x 1 root root 0  5월  6 11:48 /bin/name.sh
```
필수! sh 작성 후에는 꼭 x 권한을 주자
{: .notice}
```bash
#!/bin/sh
echo "사용자 이름: " $USER
echo "홈 디렉토리: " $HOME
exit 0
```
```
사용자 이름:  root
홈 디렉토리:  /root
```
환경변수를 이용하여 user name과 home 경로를 출력해봤다  
"#!/bin/sh"는  bash shell 스크립트임을 알리는 선언문이다  
**Echo** 명령어 화면에 출력하는 명령어  
**Exit 0** 호출후 제대로 실행되었는지 알기 위해 반환
{: .notice}
---
#### 여러가지 실행 방법
---
```console
[root@ns1 ~]# name.sh	#같은 경로에 있을때만 가능
[root@ns1 ~]# bash name.sh
[root@ns1 ~]# sh name.sh
```
위와 같이 3가지 방법으로 실행 할 수 있다.
{: .notice}
결과
```
사용자 이름:  root
홈 디렉토리:  /root
```
그렇다면, chmod로 x 권한을 없애면 어떻게 될까?
{: .notice}
```console
[root@ns1 ~]# chmod a-x /bin/name.sh
[root@ns1 ~]# sh name.sh
```
```console
[root@ns1 ~]# bash name.sh
```
```
사용자 이름:  root
홈 디렉토리:  /root
```
sh나 bash를 통해서는 실행할 수 있지만 *.sh로는 실행 불가
{: .notice}
```console
[root@ns1 ~]# name.sh
```
```
-bash: /bin/name.sh: 허가 거부됨
```
**경로 상관없이 *.sh를 실행하고 싶다면?**  
{: .notice}
```console
[root@ns1 /]# mv /bin/name.sh /root/name.sh
[root@ns1 /]# /root/name.sh
```
현재 디렉토리로 옮겨오거나
{: .notice}
```console
[root@ns1 /]# PATH=$PATH:/root
[root@ns1 /]# name.sh
```
```
사용자 이름:  root
홈 디렉토리:  /root
```
환경변수 PATH에 현재 경로를 추가해주면 된다!
{: .notice}
---
### 변수의 기본
---
변수=문자열 ( =의 양옆에 공백이 들어가면 안된다)  
변수=”문 자 열” (공백이 있는경우 “ “ 로 묶어야한다  
또는 변수=문\ 자\ 열 (\를 문자열 처리를 위해 공백 앞에 써준다)  
변수=7+5 (7+5라는 문자열이 저장된다)  
{: .notice}
```console
[root@ns1 test]# mkdir "dirA dirB"
[root@ns1 test]# mkdir dirC\ dirD
[root@ns1 test]# ls -lh
```
```
drwxr-xr-x 2 root root 4.0K  5월  6 12:24 dirA dirB
drwxr-xr-x 2 root root 4.0K  5월  6 12:24 dirC dirD
```
두개의 디렉토리가 생성되는 것이 아니라 이름에 공백이 들어감
{: .notice}