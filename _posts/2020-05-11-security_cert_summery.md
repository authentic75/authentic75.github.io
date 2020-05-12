---
title: "정보보안기사: 요약"
last_modified_at: 2020-05-11T16:20:02-05:00
categories:
  - 정보보안기사
tags:
  - 정보보안기사
  - 보안
  - 해킹
toc: true 
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

---
### 과목1 시스템 보안
---
* 컴퓨터 구조, 운영체제, 리눅스 보안, 윈도우 보안, 디지털 포렌식 개요 출제  
* 책 내용은 기본적으로 전부 암기, 실제 실기도 할 줄 알아야한다.  
* 기억해 둘 것
{: .notice--info}
---
#### 프로세스 State
---
**CPU 스케줄링 = Process State**    
모든 프로그램이 실행이 되면 메모리에 올라간다 -> 프로세스가 됨  
우리가 쓰는 운영체제는 Multi Task, Multi Programming 형태, 여러 프로세스 실행
{: .notice}
**CPU 상태 전이 (State)**  
1. dispatch: 차례가 되면 CPU 점유 (Running)
2. Block: CPU에서 나와 I/O 처리
3. Wake Up: I/O를 끝내면 다시 레디 큐로 이동
4. Time Run Out:  너무 오래 CPU를 점유하면 레디 큐로 이동
{: .notice--info}
---
#### CPU 스케줄링
---
**선점형: 우선 순위가 변경된다.**  
RR(Round Robin): Time slice 단위로 CPU 점유, 할당량 초과시 레디 큐로 이동  
SRT(Shortest Rest Task): 남는 작업이 짧은 프로세스에서 CPU 점유  
Multi-level Queue: RR 방식 + 우선 순위  
Multi-level Feedback Queue: Time Slice가 끝나면 등급을 내린다. (하이브리드)  
{: .notice--info}
**비선점형: 우선 순위가 한번 정해지면 변경 안된다**  
우선 순위 큐, FCFS(FIFO), SJF(Shortest Job First), HRN(Highest Respond-ratio Next)
{: .notice--info}
*RAID (1, 5가 출제되었다)
{: .notice}
---
#### 리눅스 보안
---
**악성코드 대응, 공격 실습 준비 해야한다**   
리눅스 파일 시스템 구조, 권한 관리 (chmod 명령어), 로그 파일 종류별 특징  
침입 흔적 찾기(find 명령어), 운영체제 기능 (Process/Memory 관리, Multi user)  
Shell의 기능: 명령어 칠 수 있게 해준다. 표준 Shell은 Bash Shell  
Shell Shock: 보안 취약점, 빈 함수/환경변수 호출 시 root 권한 획득  
{: .notice--info}
---
#### Linux 파일 시스템 Inode
---
**Boot Stage**  
**1단계 부팅**  
BIOS: Boot sequence, MBR 읽는다  
MBR: Sector 1, 512KB, Boot loader 실행 (취약점: 3.20 MBR 파괴 사건)  
{: .notice}
**2단계 부팅**  
GRUB(MBR 이름이다) & LILO: /boot, kernel image(OS) 압축 풀고 올린다.  
Init Process: Process ID 1, fork(프로세스 복제) -> 세션리더 Bash  
**Run level 실행:**  
3: (multi user, multi task, network) 5: xwindow , 6: reboot  
/etc/rc.d/init.d에 들어있다
{: .notice}

**리눅스 권한관리**   
권한: Owner, Group, Other User  
권한 리스트: Read, Write, Execute  
명령어: chmod  
디폴트 권한: umask = 0022 – 666(파일) = 644, 0022-777(디렉토리)=755  
**디렉토리는 실행권한이 있어야 이동이 가능하기 때문이다.**  
FTP와 연관 문제 또는 일반 권한 문제 출제됨  
{: .notice}
---
#### 바이러스 유형
---
**매크로 바이러스**: 운영체제 관계없이 사용(word, ppt), 누구나 쉽게 만들 수 있다.  
ASLR 동적주소 (실기 출제)  
**APT 공격, 절차**  
바이너리 디핑: 멀웨어를 탐지하는 오픈 소스, APT 공격을 탐지하는 오픈 소스  
표적 기반/지능형 공격 등등  
{: .notice}
---
#### 디지털 포렌식
--- 
가장 먼저 수집해야 하는 정보는?  
휘발성 데이터 (event Viewer는 휘발성이 아니다)  
{: .notice}
가장 먼저 수집해야 하는 Data?  
레지스터(CPU)  
{: .notice}
포렌식 절차  
준비>획득>이송>분석>증거 분석서 작성  
{: .notice}

---
### 과목2 네트워크 보안
---
**장비**:  
Fire wall  
IDS(snort 라는 도구의 Rule, Action 종류 등 필기/실기)  
NAC(End-Point)  
NAT 솔루션
{: .notice--info}
**TCP/IP**:  
netstate 커맨드: TCP state  
netstate -e: 통계 정보  
HTTP, TCP, UDP 헤더 읽을 줄 알아야 한다.  
{: .notice--info}
공격법  
{: .notice--info}
---
#### TCP (신뢰성)
---
**3-way handshaking**  
세션 값 받음 (취약점: 세션하이재킹 hand Shaking 우회)  
seq, ack 주고 받음(비동기), Go Back N (ARQ), Check sum(무결성 검사)  
{: .notice}
---
#### DDos
---
Packet Header 보고 알 수 있다  
**TCP SYN Flooding**: TCP half open 이용, Syn에 대한 Ack 안준다)  
**DrDos**: TCP half open 이용, 반사 공격의 특징(SRC) = IP변조하여 보냄  
**smurf**: *.*.*.255 ICMP Echo Request(SRC) = source IP 변조  
**Slow HTTP GET(POST) Flooding**: Content_Length = 5000 (임의의 큰 값)  
그리고 1Byte씩 전송  
{: .notice}
---
#### 공격기법/보안장비(솔루션)
---
**Sniffing 가장 기본적**  
**Normal 모드**: 목적지 주소가 수신자의 주소와 같은 것만 탐지  
**무차별 모드**: 같은 Segment (네트워크) 내 모든 패킷 탐지  
#ifconfig etho0 promisc 무차별 모드  
#ifconfig etho0 -promisc 보통 모드로  
MAC 주소는 Sniffing 가능 하지만 데이터 링크 계층 Data는 불가능  
tcpdump, tshark 커맨드 문제, 패킷 해석  
{: .notice}
---
#### 보안 솔루션
---
**Firewall**(Packet 필터링, 블랙 리스트 IP 차단, Access-list)  
Screening: Router 패킷 필터  
Dual Home: 내부/외부 망 분리  
Screened Host: Screening + Dual Home  
Screened subnet: Screening + Dual + Screened Host 전부 사용  
Web Firewall – HTTP 대상  
{: .notice}
**IDS(침입 탐지)**  
HIDS(호스트)/NIDS(네트워크)  
이상탐지(정상 외 모두 공격), 오용탐지(Signature, Rule 공격 인식)  
Time interval(일정 데이터), Real Time(패킷 몇 개), Active IDS(IPS)  
snort Rule, yara Rule (첨부파일 검사)  
TMS(위협 감지 시스템)라고 출제 되기도함  
IDS 절차 출제   
{: .notice}
**IPS**  
SSO(자동로그인, 운영 간단, 비용절감), EAM, IAM  
ESM, SIEM(관제 시스템, SIEM은 event/bigdata 활용)  
NAC(End Point 보안 기술)  
{: .notice}
---
### 과목3 어플리케이션 보안
---
CC인증(국제 표준 인증, 상호 인증)  
SDLC (폭포수), 원형을 이용한 ~(Proto type)  
빈출: 개발 보안, DRM, Water Marking, OTP, IPSEC, FTP(항상 나옴)
{: .notice--info}
---
#### FTP와 xferlog
---
**FTP**(명령 21번 포트, Active 데이터 전송 20번 포트, Passive 1024 이상 서버가 랜덤 지정)  
/etc/ftpusers: 파일에 적용된 사용자 접근제한 (root가 기본적으로 등록)  
/etc/hosts.deny: 특정 IP의 접근 제한  
/etc/hosts.allow: 특정 IP의 접근 허용  
**xperlog**:  FTP log 파일, 필드 의미 알아 둘 것! t업로드 o는 다운로드  
{: .notice}
```
cat /var/log/xferlog  
Mon May 31 07:48:23 2010 1 x.x.x.x 0 /home/byoungguk/1 b _ o r byoungguk ftp 0 * c
Mon May 31 07:48:25 2010 1 x.x.x.x 0 /home/byoungguk/2 b _ o r byoungguk ftp 0 * c
Mon May 31 07:48:53 2010 1 x.x.x.x 0 /home/byoungguk/3 b _ i r byoungguk ftp 0 * c
```
시간/전송(초)/접속 ip/크기/파일이름/전송/액션/direction/access/유저/서비스/인증/상태
{: .notice--warning}
전송 형태: a(ASCII), b(binary)
액션 Flag: C(압축), U(압축x), T(tar), _(아무 액션이 없다)
Direction: 전송의 지시 o(outgoing), i(incoming), d(delete)
Access-mode: a(anonymous), g(guest passwd 소유), r(real 인증 가능한 local user)
Service-name: 보통은 FTP
Authentication-method: 0(none), 1(authenticated)
Completion-status: 전송상태 나타냄, c(완전한 전송), i(불완전한 전송)
{: .notice--warning}
tftp (UDP 이용), sftp(암호화)  
TCP 기반 (3way handshaking)  
wget으로 여러 파일 동시에 다운 가능하다.  
{: .notice}
bounce Attack 포트 스캐닝: 가짜 메일을 보내서 익명의 ftp 서버 경유한다(Nmap -b)
{: .notice}
---
#### 웹서버와 access.log
---
**웹서버 (80)**  
디렉토리 리스팅: index.* 파일 없을 때 해당 경로의 모든 파일 디렉토리 노출  
서버에 대한 정보: 서버 토큰, 서버 signature  
웹서버 로그 파일: access.log
{: .notice}
```
127.0.0.1 - frank[10/Oct/2000:13:55:36-0700]"GET/apache_pb.gif HTTP/1.0"200 2326  
```
요청IP/신원/user id/요청을 마친 시간/요청 데이터/상태 코드/헤더를 제외한 크기
{: .notice--warning}
신원: identd가 제공할 클라이언트의 신원 (내부 네트웍 아니면 사용 x)  
Userid: 상태 코드가 401이면 사용자가 인증을 거치지 않은 것  
상태 코드: 2xx(요청 성공), 4xx(클라이언트 오류), 5xx(서버 오류)  
{: .notice--warning}
---
#### SW 개발 보안
---
SDLC 단계별 Software 보안 활동  
Secure SDLC  
보안기능(요구사항): 에러처리, 세션통제, …  
{: .notice}
---
#### SQL Injection
---
**SQL**
DDL: create table, alter table, drop table
DML: insert, delete, update, select
DCL: grant, revoke
{: .notice}
```
공격 1 테이블명 알아내기 (having)
username : ' having 1=1--
```
```
공격 2 필드명 알아내기 (group by)
username : 'group by users.id having 1=1--
```
공격 3 필드 타입 알아내기 (union)
username : ' union select sum(username) from users--
```
```
공격 4 계정만들기 (insert)
username : '; insert into users values(666, 'attacker', 'foobar', 0xffff)--
```
```
공격 5 버전 및 환경 알아내기
username : 'union select @@version,1,1,1--
```
```
공격 6 계정 추출하기
username : 'union select min(username), 1,1,1 from users where username > 'a'--
```
``
공격 7 계정의 패스워드 알아내기
username : 'union select password,1,1,1 from users where username = 'admin'--
```
공격 8 transact-SQL  
select 문으로 직접 쿼리문 전송  
{: .notice}
```
공격 9 shutdown
username : '; shutdown--
```
---
### 과목4 정보보안일반
---

---
### 과목5 정보보안관리 및 법률
---