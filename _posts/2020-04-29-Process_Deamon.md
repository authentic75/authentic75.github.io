---
title: "LINUX: 프로세스와 데몬"
last_modified_at: 2020-04-29T16:20:02-05:00
categories:
  - LINUX
tags:
  - LINUX
  - 프로세스
  - 데몬
toc: true 
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
---
### 프로세스 명령어
---
`프로세스 확인: ps, pstree, top, pgrep`  

* ps u	: 사용량(%)을 함께 출력
* ps a	: 터미널 프로세스를 출력
* ps x	: 모든 프로세스 목록 출력
* ps -f	: 부모 프로세스 정보를 같이 출력
* ps -e	: 모든 프로세스 목록 출력
* ps -p , ps --pid : pid를 볼 수 있다
{: .notice}  

```console
[root@ns1 ~]# ps u
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root      2632  0.0  0.0   1760   448 tty1     Ss+  10:28   0:00 /sbin/mingetty
root      2633  0.0  0.0   1760   444 tty2     Ss+  10:28   0:00 /sbin/mingetty
```
```console
[root@ns1 ~]# ps a
  PID TTY      STAT   TIME COMMAND
 2632 tty1     Ss+    0:00 /sbin/mingetty tty1
 2633 tty2     Ss+    0:00 /sbin/mingetty tty2
```
```console
[root@ns1 ~]# ps -f
UID        PID  PPID  C STIME TTY          TIME CMD
root      3096  3091  0 10:31 pts/2    00:00:00 -bash
root      3136  3096  0 10:33 pts/2    00:00:00 ps -f
```

`전체 프로세스 목록중 특정 프로세스 목록 출력`  

 * ps aux l grep 프로세스명
 * ps -ef l grep 프로세스명
 * pgrep 패턴(문자열)	 : 패턴과 일부 일치하는 프로세스 검색. 결과는 PID로 출력  
 * pgrep -x 패턴(문자열): 패턴과 정확히 일치하는 프로세스 검색. 결과는 PID로 출력  
 * 위에 출력된 PID를 ps -p, ps --pid 명령어 조건에 같이 포함하여 검색
 * ps -p `pgrep 패턴(문자열)`
 * ps --pid $(pgrep 패턴(문자열))
{: .notice}
```console
[root@ns1 ~]# ps aux |grep tty
root      2632  0.0  0.0   1760   448 tty1     Ss+  10:28   0:00 /sbin/mingetty tty1
root      2633  0.0  0.0   1760   444 tty2     Ss+  10:28   0:00 /sbin/mingetty tty2
```
S+는 포그라운드에서 돌아가고 있는 프로그램을 뜻하고
Ss는 세션리더(사용자 시작 프로세스)를 뜻한다 -bash가 대부분 Ss
{: .notice--warning}
```console
[root@ns1 ~]# ps -ef | grep tty
root      2632     1  0 10:28 tty1     00:00:00 /sbin/mingetty tty1
root      2633     1  0 10:28 tty2     00:00:00 /sbin/mingetty tty2
```
```console
[root@ns1 ~]# pgrep tty
2632
2633
2634
```
* 프로세스 종류
	* 포그라운드 프로세스: 화면에 나타나서 사용자와 상호작용
	* 백그라운드 프로세스: 뒤에서 실행되는 프로세스
		* done 이라는 메세지를 볼 수 있다
* 인터럽트: 프로세스 실행 중 Ctrl + C 키 (프로세스 종료)
{: .notice}
`pstree 명령어`
```console
[root@ns1 ~]# pstree -p
```
`process의 종속 관계를 볼 수 있다`
```console
[root@ns1 ~]# ps -p `pgrep -x ping`
```
홑따옴표(`)를 활용하여 특정 명령어에 대한 결과값을 입력값으로 활용 할 수 있다  
A = `date` 또는 A = $(date)
{: .notice}
---
### Top 명령어
---
top: 전체 프로세스 목록을 Priority, Nice, CPU(%) ,RAM(%)과 함께 실시간(3초)으로 출력  
{: .notice}
* top -d 1 로 딜레이를 바꿀 수도 있다
* 대문자 P 입력시 우선순위 별로 정렬 < > 로 이동
* #nice --20 tar cfz usr.tar.gz /usr &을 실행하면 낮은 PR 값을 가질 수 있다.
* RES 리얼 메모리 /SHR 공유메모리 /S 스탑 상태 /CPU 사용량/ TIME은 CPU 사용시간
* ps u 로 보면 Stat이 보여지는데 이것으로도 상태를 볼 수 있다.
{: .notice}
```console
[root@ns1 ~]# top
top - 10:31:50 up 4 min,  2 users,  load average: 0.00, 0.03, 0.00
Tasks: 113 total,   1 running, 112 sleeping,   0 stopped,   0 zombie
Cpu(s):  0.0%us,  0.0%sy,  0.0%ni, 98.8%id,  0.0%wa,  0.2%hi,  1.0%si,  0.0%st
Mem:   2074972k total,   406116k used,  1668856k free,    32080k buffers
Swap:  4128696k total,        0k used,  4128696k free,   290692k cached

  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
    1 root      15   0  2172  640  548 S  0.0  0.0   0:00.47 init
    2 root      RT  -5     0    0    0 S  0.0  0.0   0:00.01 migration/0
```
`출력되는 정보`  
* PR(Pririoty)는 직접 조정을 불가능하며, nice를 통해 간적적으로 제어가능한다.
* nice -[값] 명령어 -20 ~ 19 사이에 값으로 설정가능하며, 낮을수록 우선순위 가 좋아(낮아)진다.
* S(Status)상태 필드는 R, S, T, D, Z 상태가 출력될 수 있고,
* R	실행
* S	슬리핑
* T	정지
* D	대기
* Z	좀비
* s	세션리더(사용자 시작 프로세스)
* +	foreground (현재 작업중인 프로세스)
{: .notice--warning}
```console
[root@ns1 ~]# nice --20 tar cfz usr.tar.gz /usr &
[root@ns1 ~]# ping 168.126.63.1 > ping1.txt &
```
---
### kill 명령어
---
```console
[root@ns1 ~]# kill -l 
```
`kill -l 입력시 다음과 같은 지원 기능들을 볼 수 있다`  

1. SIGHUP, HUP	: 프로세스 재구동효과(캐쉬, 버퍼)
2. SIGINT, INT	: [Ctrl] + [c], 인터럽트
3. SIGQUIT, QUIT	: [Ctrl] + [\], 종료
9. SIGKILL, KILL	: 죽임(Kill)
15. SIGTERM, TERM	: 정상종료(Terminate), 기본값
17. SIGCHLD, CHLD	: 자식 프로세스 정리(Zombie)
19. SIGSTOP, STOP	: [Ctrl] + [z], 정지
{: .notice--info}
```
# kill [-종류] PID
# Kill [-종류] %작업번호
```
---
### jobs
---
```console
[root@ns1 ~]# ping 168.126.62.1 > result1.txt &
# 또는 Ctrl + Z 키를 누르면 백그라운드로 돌릴 수 있다
```
* 사용자의 작업목록 확인
* [1], [2] 같은 작업번호를 확인할 수 있으며, BackGround 에 넘어간 순서대로 순차적으로 부여됨
* 가장 최근에 BackGround에 넘어간 작업에 + 기호가 부여된다.
* - 기호는 2번째 전에 넘어간 작업에 부여된다.
* bg %작업번호	: Stop상태로 BackGround에 넘어간 작업을 Running상태로 전환
* fg %작업번호	: BackGround의 작업을 ForGround의 작업으로 전환
{: .notice}
---
### Daemon
---
* CentOS ~6 : init + xinetd
* CentOS 7~ : systemd
* 데몬(서비스) 실행 스크립트 위치
* CentOS ~6 : /etc/init.d/
* CentOS 7~ : /usr/lib/systemd/system/
{: .notice}

`1. 데몬(서비스) 구동/정지/상태`
```
[root@ns1 ~]# service  데몬명  [start/stop/restart/status]
[root@ns1 ~]# systemctl  [start/stop/restart/status]  데몬명
[root@ns1 ~]# systemctl  [start/stop/restart/status]  데몬명.service
```
`2. 데몬(서비스) 자동 시작 등록/제거`
```
[root@ns1 ~]# chkconfig  데몬명  [on/off]
[root@ns1 ~]# systemctl  [enable/disable]  데몬명
```
`3. 데몬(서비스) 자동 시작여부 확인`
```
[root@ns1 ~]# ntsysv
[root@ns1 ~]# systemctl list-unit-files
```
 ```console
[root@ns1 ~]# Systemctl start httpd.service
[root@ns1 ~]# Systemctl stop httpd.service
[root@ns1 ~]# Systemctl status httpd.service
```
```console
[root@ns1 ~]# ls -ld /etc/init.d/ #우리가 실행하는 스크립트들을 볼 수 있다
[root@ns2 ~]# /usr/lib/system/system/
```
* CentOS 5 에서는 재부팅 후 service httpd status 를 보면 정지됨 나옴  
	* 그 이유는 ntsysv 에서 자동으로 등록이 안되어 있어서 이다  
	* 직접 체크를 할 수도 있지만 #chkconfig httpd on 으로 체크를 해줄 수도 있다  
* CentOS 8
	* systemctl list-unit-files 에서 서비스 리스트 볼 수 있다.
	* systemclt [enable/disable] httpd.service
{: .notice--info}

* inetd (인터넷 데몬)에 보안을 강화하여 xinetd(eXtended (인터넷 데몬)이 되었으며, CentOS7 이후부터는 init데몬과 xinetd 데몬을 결합하여 systemd 데몬으로 변경되었다.
* inetd, xinetd: 인터넷 데몬, 수퍼 데몬으로도 불린다.
{: .notice}

* **데몬은 StandAlone형과 SuperDaemon형으로 나뉜다.**
* StandAlone형은 독립적으로 동작하는 데몬들이며,SuperDaemon형은 스스로 동작하지 않고, StandAlone에 의해 동작하는 데몬이다.
* StandAlone(sshd)데몬: 항상 프로세스상태여야하며, 서비스 요청시 즉시 응답이 가능하다. 주로 자주 요청되는 서비스에 적용된다.
* SuperDaemon (telnetd)데몬: 평소에는 프로그램(저장)상태에서 서비스 요청시 StandAlone에 의해서 동작(프로세스)을 시작하며, 서비스 종료와 함께 프로스세는 종료된다.
{: .notice}

```console
[root@ns1 ~]# service sshd restart  #독립적인 데몬이라 독립적으로 껏다 켤 수 있다.
[root@ns1 ~]# vi /etc/xinetd.d/telnet #수정 disable no
[root@ns1 ~]# service sshd restart #후 pgrep sshd 로 검색 하자
[root@ns1 ~]# Ps u -p `pgrep -x sshd`
[root@ns1 ~]# ps aux | grep telnetd   #그러면 그랩한거만 보임
```
* pstree 실행해보면 sshd가 독자적으로 동작중인 것이 나온다. 
* Telenetd는 아직 실행은 안되었지만 구동 되는순간 xinetd 뒤에 종속됨
* 그리고 구동 되는 순간 in.teletd 라고 앞에 in 이 붙는다
{: .notice}
