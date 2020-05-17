---
title: "LINUX: 패키지와 압축"
last_modified_at: 2020-04-27T16:20:02-05:00
categories:
  - LINUX
tags:
  - LINUX
  - rpm
  - yum
  - tar
toc: true 
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
---
### rpm 명령어 옵션
---
* Source Code	- Compile -> Binary Code
* 사람이 이해 - 기계가 이해
* Redhat RPM파일, Linux 시스템에 맞게 컴파일이 미리 되어진 파일
{: .notice}
* 파일명: telnet-server-0.17-39.el5.i386.rpm
* 패키지명-버전-릴리즈.아키텍쳐.rpm
* 패키지명: telnet-server
* 버    전: 0.17
* 릴리즈명: 39.el5 /fc : Fedora Core 
* 아키텍쳐: i386 /el : Enterprise Linux
	* i386 : intel 32bit CPU
	* x86_64: 64bit CPU
{: .notice}	
* 설치(install)
* rpm -i RPM파일명 : 설치
	* -iv, -ivv	: 설치과정을 (더)상세히 출력
	* -ih : 설치 진행률이 함께 출력
	* --replacepkgs	: 동일 버전을 다시 설치(덮어쓰기)
	* --replacefiles : 다른 버전을 추가 설치
	* --nodeps : 의존성을 무시하고 단독으로 설치
{: .notice}

* 업그레이드(Upgrade)
	* rpm -U RPM파일명 : 업그레이드(설치)
	* -iv, -ivv	: 설치과정을 (더)상세히 출력
	* -ih : 설치 진행률이 함께 출력
	* --oldpackage : 다운그레이드
    * 강제 옵션(무조건 설치할 때 사용)
    * --force : --replacepkgs, --replacefiles, --oldpackage
{: .notice}

* 제거(erase)
* rpm -e 패키지명
* rpm -e 패키지명-버전-릴리즈명 : 동일 패키지가 2개이상(다른버전)으로 설치된 경우
{: .notice}

* 검사(Query)
* rpm -q  패키지명
* rpm -qp RPM파일명
{: .notice}

* rpm -qi  패키지명 : 설치된 패키지의 정보를 출력
* rpm -qpi RPM파일명 : 설치전 RPM파일의 정보를 출력
{: .notice}

* rpm -ql  패키지명 : 설치된 패키지의 파일(설정,문서) 목록 출력
* rpm -qc  패키지명 : 설치된 패키지의 파일(설정) 목록 출력
* rpm -qd  패키지명 : 설치된 패키지의 파일(문서) 목록 출력
{: .notice}

* rpm -qf  파일명	: 파일이 어떤 패키지에 의해서 설치되었는지 확인
{: .notice}

* rpm -qR  패키지명 : 설치된 패키지가 의존적인 모든 패키지들을 출력
* rpm -qpR RPM파일명	: 설치전 RPM파일이 의존적인 모든 패키지들을 출력
{: .notice}

```console
[root@ns1 ~]# rpm -qip telnet-server-0.17-39.el5.i386.rpm
[root@ns1 ~]# rpm -qa telnet
[root@ns1 ~]# rpm -qa telnet-server
[root@ns1 ~]# rpm -qa | wc -l
[root@ns1 ~]# rpm -ivh telnet-server-0.17-39.el5.i386.rpm
[root@ns1 ~]# rpm -Uvh telnet-server-0.17-39.el5.i386.rpm
```

rpm 명령어의 단점은 의존성의 문제가 생길 수 있다  
예로, xinetd 와 telnet-server는 의존성 문제를 일으킬 수 있다.  
{: .notice}

* yum remove xinetd -y				일단 xinetd 삭제
* rpm -qa telnet-server xinnetd		띄어쓰기로 여러 개의 패키지 검색
* rpm -I telnet-server-버전-릴리즈명
{: .notice}

Failed dependencies  
Xinetd is needed by telnet-server  
Xinetd가 우선적으로 설치된 후에 telnet이 설치되어야 한다는 것을 알 수 있다.  
마찬가지로 xinetd는 삭제 불가능하다 xinetd에 의존하고 있는 패키지부터 지워야 삭제 가능하다.  
{: .notice}

* rpm -ivh 텔넷-버전-릴리즈 xinetd-버전-릴리즈 처럼 한번에 설치 시 알아서 의존성에 맞게 순서대로 설치해준다.
* rpm -nodeps 패키지명 	옵션으로 의존성 상관없이 설치 할 수도 있다 (잘 사용 안함)
* rpm -qi [패키지명] 설치된 패키지의 정보 출력
* rpm -qpi [rpm파일명] 설치된rpm file 정보를 알려준다
{: .notice}

패키지가 분산되어서 설치되었을 수 있다. 이경우 어디 어디에 무엇이 설치되었는지 알기 위해서 사용하는 옵션 -ql , -qd, -qc, -qf
{: .notice}

* rpm -qf /bin/ping	파일이 어떤 패키지에 의해서 설치되었는지 확인
* rpm -ql iputils | wc -l
* rpm -qR 패키지명 	설치된 패키지가 의존적인 모든 패키지들을 출력
* rpm -qpR RPM 파일명	 설치전 RPM파일이 의존적인 모든 패키지들을 출력
{: .notice}

---
### YUM(DNF) 사용법
---
* Rpm이 없으면 yum도 없고 DNF도 없다.
* 둘 다 Rpm 파일이 쉽게 설치될 수 있도록 도와주는 유틸리티이다.
* 둘다 Yum repository(etc/yum/repos.d 를 사용한다.
* DNF는 yum 명령어가 대폭 개선된 유틸리티다.
{: .notice}
* 검사
* yum list [개별패키지명]	: 설치됨, 업데이트, 미설치 
* 개별패키지들의 상태를 출력
	* yum list installed : 설치된 패키지만 출력
	* yum list updates : 업데이트된 패키지만 출력
	* yum grouplist [패키지그룹명] : 설치됨, 미설치, 패키지그룹들의 상태를 출력
{: .notice}
* 설치
* yum install [개별패키지명]: 최신버전의 패키지를 설치, 의존성 있는 패키지도 같이 설치
* yum groupinstall [패키지그룹명] : 최신버전의 패키지그룹을 설치
* yum localinstall [RPM파일명] : 원하는 RPM파일을 설치하며, 의존성만 인터넷으로 해결
	* -y : 모든 질문에 대해 yes 처리
	* --quiet : 설치과정을 출력을 하지 않는다.
{: .notice}
* 업데이트
* yum check-update : 현재 업데이트가 필요한 패키지 검사
* yum update [개별패키지명]	: 전체[개별] 패키지 업데이트
{: .notice}
* 제거
* yum remove [개별패키지명]	: 설치된 개별패키지 제거
	* -y : 모든 질문에 대해 yes 처리
	* --quiet : 제거과정을 출력을 하지 않는다. 의존성 있는 패키지도 같이 제거
* yum groupremove [패키지그룹명] : 설치된 패키지그룹 제거
{: .notice}
* 정보확인
* yum info [개별패키지명]	: 패키지 정보 출력
* yum provides [파일명]	: rpm -qf, 파일이 어떤 패키지에 의해서 설치되었는지 확인
* yum clean all : 기존 저장소 캐쉬 모두 제거
* 다시 yum을 실행하면 저장소갱신
{: .notice}
```console
[root@ns1 ~]# yum remove xinetd  #실행 시 , 자동으로 telnet-server도 삭제할지 묻는다.
[root@ns1 ~]# rpm -qa telnet-server xindetd 

[root@ns1 ~]# yum -y install telnet-server  #설치 중 묻는 질문에 모두 y 처리하여 설치
[root@ns1 ~]# yum -y remove telnet-server xinetd  #마찬가지로 모든 질문에 대해 y 처리하여 제거
[root@ns1 ~]# yum -y –-quiet install telnet-server  #메시지 없이 조용히 설치 remove도 마찬가지다

[root@ns1 ~]# yum list installed  #설치된 목록들만 볼 수 있다.
[root@ns1 ~]# yum list updates  #업데이트가 필요한 목록들만 볼 수 있다.
# yum install telnet-server 39 release~~ 로 설치하면 Available을 아래에 함께 보여주며 최신 버전까지 보여준다.
```
* yum list는 크게 4가지로 나뉨
	* Updates는 cd 버전 이후에 업데이트가 발생한 것 (업데이트된 버전으로 설치할 준비가 되었다)
	* Base는 cd 이래로 업데이트가 없는 것 (기존 버전 그대로 설치할 준비가 되었다)
	* Extras cd에 없는 것?
	* installed는 설치된 패키지들 
{: .notice}
* 이제까지는 개별적 패키지를 다루었고 그룹 패키지가 따로 있다. 
* yum grouplist | tail -n 20 을 해보았다.
* yum groupinstall  “Java 개발용 도구”	
{: .notice}
---
### 파일 묶기 tar(Tape ARchive)
---
* tar 생성
* tar cf TAR파일명 원본1 원본2 ...	: 나열된 원본들을 TAR파일명으로 묶기(생성)
	* v, vv : 묶기 과정을 (더)자세히 출력
	* -C 기준경로 : 기준경로 다음에 상대적경로의 파일명을 나열
	* z : gzip 으로 추가 압축
	* j : bzip2 로 추가 압축
	* J : xz 으로 추가 압축
{: .notice}
* tar 풀기
* tar xf TAR파일명 : 현재 디렉토리에 TAR파일 풀기
* tar tf TAR파일명 : TAR파일 풀기 테스트
	* v, vv : 풀기 과정을 (더)자세히 출력
	* -C 경로 : 지정된 경로 뒤에 TAR파일 풀기
{: .notice}
* tar 추가, 업데이트
* tar uf TAR파일명 [수정된원본]  : TAR파일의 내용과 비교하여 수정된 원본을 추가
* tar rf  TAR파일명 원본	: TAR파일에 새로운 원본을 추가
{: .notice}	