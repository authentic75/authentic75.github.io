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
`CPU 스케줄링 = Process State`    
모든 프로그램이 실행이 되면 메모리에 올라간다 -> 프로세스가 됨  
우리가 쓰는 운영체제는 Multi Task, Multi Programming 형태, 여러 프로세스 실행
{: .notice}
`CPU 상태 전이 (State)`
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
리눅스 보안 (악성코드 대응, 공격 실습 준비 해야한다)  
리눅스 파일 시스템 구조, 권한 관리 (chmod 명령어), 로그 파일 종류별 특징  
침입 흔적 찾기(find 명령어), 운영체제 기능 (Process/Memory 관리, Multi user)  
Shell의 기능: 명령어 칠 수 있게 해준다. 표준 Shell은 Bash Shell  
Shell Shock: 보안 취약점, 빈 함수/환경변수 호출 시 root 권한 획득  
{: .notice--info}
---
#### Linux 파일 시스템 Inode
---
`Boot Stage`  
1단계 부팅  
BIOS: Boot sequence, MBR 읽는다  
MBR: Sector 1, 512KB, Boot loader 실행 (취약점: 3.20 MBR 파괴 사건)  
{: .notice}
2단계 부팅  
GRUB(MBR 이름이다) & LILO: /boot, kernel image(OS) 압축 풀고 올린다.  
Init Process: Process ID 1, fork(프로세스 복제) -> 세션리더 Bash  
Run level 실행: 3 (multi user, multi task, network)  
**5: xwindow , 6: reboot /etc/rc.d/init.d에 들어있다.**
{: .notice}

`리눅스 권한관리`  
-권한: Owner, Group, Other User  
-권한 리스트: Read, Write, Execute  
-명령어: chmod  
-디폴트 권한: umask = 0022 – 666(파일) = 644, 0022-777(디렉토리)=755  
**디렉토리는 실행권한이 있어야 이동이 가능하기 때문이다.**  
FTP와 연관 문제 또는 일반 권한 문제 출제됨  
{: .notice}
---
#### 바이러스 유형
---
-매크로 바이러스: 운영체제 관계없이 사용(word, ppt), 누구나 쉽게 만들 수 있다.  
-ASLR 동적주소 (실기 출제)  
-APT 공격, 절차  
-바이너리 디핑: 멀웨어를 탐지하는 오픈 소스, APT 공격을 탐지하는 오픈 소스  
-표적 기반/지능형 공격 등등  
{: .notice}
---
#### 디지털 포렌식
--- 
가장 먼저 수집해야 하는 정보는?  
-휘발성 데이터 (event Viewer는 휘발성이 아니다)  
{: .notice}
가장 먼저 수집해야 하는 Data?  
-레지스터(CPU)  
{: .notice}
포렌식 절차  
준비>획득>이송>분석>증거 분석서 작성  
{: .notice}

---
### 과목2 네트워크 보안
---
TCP/IP  
공격법  
장비: Fire wall, IDS(snort 라는 도구의 Rule, Action 종류 등 필기/실기), End-Point (NAC로 출제), NAT 솔루션  
TCP/IP에서 봐야할 것  
netstate 커맨드: TCP state  
netstate -e: 통계 정보  
HTTP, TCP, UDP 헤더 읽을 줄 알아야 한다.  
{: .notice--info}

*TCP (신뢰성)
	-3-way handshaking
	-세션 값 받음 (취약점: 세션하이재킹 hand Shaking 우회)
	-seq, ack 주고 받음(비동기), Go Back N (ARQ), Check sum(무결성 검사)
*DDos (Packet Header 보고 알 수 있다)
	-TCP SYN Flooding: TCP half open 이용, Syn에 대한 Ack 안준다)
	-DrDos: TCP half open 이용, 반사 공격의 특징(SRC) = IP변조하여 보냄
	-smurf: *.*.*.255 ICMP Echo Request(SRC) = source IP 변조
	-Slow HTTP GET(POST) Flooding: Content_Length = 5000 (임의의 큰 값)
					 그리고 1Byte씩 전송


*공격기법/보안장비(솔루션)
	-Sniffing 가장 기본적
		Normal 모드: 목적지 주소가 수신자의 주소와 같은 것만 탐지
		무차별 모드: 같은 Segment (네트워크) 내 모든 패킷 탐지
	#ifconfig etho0 promisc		무차별 모드
	#ifconfig etho0 -promisc	보통 모드로
	MAC 주소는 Sniffing 가능 하지만 데이터 링크 계층 Data는 불가능
	-tcpdump, tshark 커맨드 문제, 패킷 해석

	-보안 솔루션
		Firewall(Packet 필터링, 블랙 리스트 IP 차단, Access-list)
			Screening: Router 패킷 필터
			Dual Home: 내부/외부 망 분리
			Screened Host: Screening + Dual Home
			Screened subnet: Screening + Dual + Screened Host 전부 사용
		Web Firewall – HTTP 대상

		IDS(침입 탐지)
			HIDS(호스트)/NIDS(네트워크)
이상탐지(정상 외 모두 공격), 오용탐지(Signature, Rule 공격 인식)
			Time interval(일정 데이터), Real Time(패킷 몇 개), Active IDS(IPS)
			*snort Rule, yara Rule (첨부파일 검사)
*TMS(위협 감지 시스템)라고 출제 되기도함
IPS
		SSO(자동로그인, 운영 간단, 비용절감), EAM, IAM
		ESM, SIEM(관제 시스템, SIEM은 event/bigdata 활용)
		NAC(End Point 보안 기술)
	*IDS 절차 출제

---
### 과목3 어플리케이션 보안
---

---
### 과목4 정보보안일반
---

---
### 과목5 정보보안관리 및 법률
---