---
title: "정보보안기사: 시스템보안"
last_modified_at: 2020-05-12T16:20:02-05:00
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
---
### 운영체제
---
**운영체제 5계층**  
프로세서 관리  
메모리 관리  
프로세스 관리  
주변장치 관리  
파일 관리  
{: .notice}

---
### 캐시메모리
---
`캐시메모리 교체 알고리즘`

|    종  류        | 상세 내용          | 특  징          |  
| ---------- | --------------- | ------------ |  
| Random | 교체될 Page를 임의 신청 | Over 헤드가 적다 |  
| FIFO | 캐시 내 오래있었던 page 교체 | 자주 사용되는 Page가 교체될 우려 |  
| LFU | 사용 횟수가 가장 적은 Page | 최근 적재된 page가 교체될 우려 |  
| LRU | 가장 오랫동안 사용되지 않은 Page 교체 | Time Stamping에 의한 Overhead |  
| Optimal | 가장 참조되지 않을 Page 교체 | 실현 불가능 |  
| NUR | 참조 비트와 Modify 비트로 미사용 Page 교체 | 최근 사용되지 않은 페이지 교체 |  
| SCR | 최초 참조 비트 togle (1로 set, 이후 0으로 set 그리고 0일때 교체) | 기회를 한 번 더 줌 |  

---
### DMA, I/O 장치
---
**DMA(Direct Memory Access)**  
CPU의 개입 없이 I/O 장치와 기억장치 사이의 데이터 전송  
CPU의 간섭을 배제하고 메모리와 주변장치를 직접 관리하며 속도가 빠르다  
{: .notice}
**DMA 동작 방식**  
Cycle Stealing: DMA제어기와 CPU가 BUS를 공유, CPU가 BUS를 사용하지 않는 사이클에만 접근,  
CPU보다 높은 우선순위 가진다.
Burst Mode: DMA 제어기가 BUS를 점유하며, 동작 완료 후 BUS의 점유를 해제한다.
{: .notice}


---
### 가상메모리
---

`가상메모리 관리 기법`

|    종  류        | 상세 내용         | 유  형       |  
| ---------- | -------------- | --------- |  
| 할당  기법 | 프로세스에게 할당되는 메모리 블록의 단위 결정 | 고정 할당, 가변 할당, Paging, Segmentation |  
| 호출  기법 | 보조기억장치에서 주기억장치로 적재할 시점 결정 | Demand Fetch, Pre Fetch |  
| 배치  기법 | 요구된 페이지를 주기억장치의 어느 곳에 적재할지 결정 | First fit, Best fit, Next fit, Worst fit |  
| 교체  기법 | 주기억장치 공간 부족 시 교체 대상 결정 | Random, FIFO, LRU, LFU, NUR, SCR, Optimal |  

`이때 메모리에 프로그램을 올리면서 빈공간 생성됨`  
  
**슬랙 공간**: 저장매체의 논리적 구조와 물리적 구조의 차이로 발생하는 낭비공간  
램 슬랙(섹터 단위로 저장 후 남은 공간)  
드라이브 슬랙(클러스터를 사용하기 때문에 낭비되는 공간)  
파일 시스템 슬랙(파일 시스템 마지막 부분, 악성코드 은닉에 사용)  
볼륨 슬랙: 전체 볼륨크기가 할당된 파티션 크기 차이로 발생되는 낭비 공간
{: .notice}


---
#### DAT
---
**DAT(Dynamic Address Translation)**  
프로세스에서 사용되는 가상 주소는 주기억장치의 실 주소로 변환됨  
하드웨어를 통해서 구현되어 고비용이지만 고속의 처리, 캐시 적중률 우수, 유연성은 떨어짐  
연관사상 > 직접/연관 > 직접사상
{: .notice}
---
### 쓰레드와 프로세스
---

`프로세스 실행 구조`

|  상위 메모리 주소  |  
| -------------- |  
|  argv(문자열)  |  
|  argv(포인터)  |  
|  argc  |  
|  Stack Area  |  
|  ...  |  
|  Heap Area  |  
|  Data(.bss)  |  
|  Data(.data)  |  
|  Code (txt)  |  
| ------------- |  
|  하위 메모리 주소  |  

argv(포인터) 프로그램 실행시 입력값  
Stack Area 지역변수와 함수 복귀 주소 저장  
Heap Area 동적 메모리 할당  
Data(.bss) 초기화 되지 않은 변수  
Data(.data) 초기화된 변수  
{: .notice--warning}

---
#### 비교
---

|    구  분       | Thread          | Process         |  
| ---------- | --------------- | ------------ |  
| 상호  통신 | Library Call, 요청 Thread만 Blocking | System Call, Call 종료시까지 전체 자원 Blocking |  
| 처리  방식 | CPU를 이용하는 기본작업, 단위로 구분 | 자원 할당을 위한 기본 구분 단위 |  
| 부하 | 프로세스보다 부하 발생 적음 | Context Switching으로 인한 부하 발생, 프로세스 자원 할당 시 부하 발생 |  

---
#### PCB
---
**PCB(Process Control Block)**
운영체제가 프로세스를 제어하기 위해 정보를 저장해 놓은 곳(구조체)  
프로세스 상태관리, 문맥교환을 위해 필요하다  
프로세스 생성시 만들어지며 주기억장치에 유지된다  
{: .notice}

**PCB에 유지되는 정보**  
PID  
포인터: 다음 실행 프로세스  
상태: 준비, 대기, 실행  
Register save area: 레지스터 정보  
Priority: 스케줄링 및 프로세스 우선순위  
Account: CPU 사용시간, 실제 사용 시간  
입출력상태 정보  
할당된 자원 정보  
{: .notice--warning}

* 레이스컨디션
	* 두 개 이상의 프로세스들이 공유 자원에 동시에 접근하여 읽기 및 쓰기를 못하게 해야한다
	* 여러 번 실행되는 과정에서 실행순서가 뒤바뀌어 실행자가 원하는 결과를 얻는다
	* 임시파일을 사용하여 공격한다
	* 심볼릭 링크를 사용한다
{: .notice}

---
#### 스케줄링
---
* 비선점
	* 순서가 바뀌지 않는다
	* FCFS, SJF, HRN, 우선순위, 기한부
* 선점
	* 라운드로빈, SRT, 다단계 큐, 다단계 피드백 큐

---
#### 다중스레드
---
**Heavy weight process**: 한 프로세스에 속한 스레드가 여러 자원을 공유하고 프로세스의 주고 공간과 열린 파일을 공유해 수행 효율 높인다.  
**Light weight process**: 스레드를 일컫는 말. 프로세스 속성 공유  
**멀티 스레드 기법**: 멀티 디스크, 멀티 프로세서 시스템에서 효율성 극대화  
{: .notice}
---
#### 인터럽트
---
**인터럽트**  
비동기 인터럽트: 입출력, 하드웨어 이벤트, 키보드, 마우스 등 이벤트에 의해 발생  
클럭 인터럽트: 클럭 시간 간격으로 지정된 시간 동안만 스레드가 CPU 점유  
동기 인터럽트: 명령어의 실행 결과로 나타나는 인터럽트 Trap 이라고도 한다  
		(프로세스를 0으로 나누거나, 보호 메모리 영역을 참조할 때 발생)  	
**외부 인터럽트**: 전원공급, 타이밍 소자 등의 요인에 의해 발생되는 인터럽트  
폴링: 프로그램에 의한 I/O, 연속 감시
{: .notice}
---
#### 교착 상태
---

|  구  분   |  세 부 내 용         |  
| --------- | ---------- |  
| 예방 | 교착상태의 필요조건 부정 (환형대기, 점유와 대기, 상호배제 4가지부정) |  
| 회피 | 적절히 피하는 방법 (은행원 알고리즘) |  
| 탐지 | 교착상태 발생 허용, 원인 규명으로 해결 (자원할당그래프) |  
| 복구 | 교착상태 발견 후 환형대기를 배제시키거나 프로세스 중지하는 메모리 할당기법 |  

---
### 디스크 스케줄링
---
FCFS(first come first served): 개발 용이, 공평성, 이동거리 증가  
SSTF(Shortest-Seek Time First): Seek time 감소, Starvation 발생  
SCAN(엘리베이터): SSTP 탐색 시간 편차 해소  
C-SCAN(Circular Scan): 바깥쪽에서 안쪽  
C-LOOK(Circular look):  
{: .notice}

---
### 리눅스
---
**리눅스 셸**
시그널 처리, 프로그램실행, 파이프/리다이렉션/백그라운드 프로세스 설정  
명령줄 분석, 와일드 카드, 히스토리 문자, 특수문자 분석  
{: .notice}

**커널 종류/모드**
사용자 모드:  
커널 모드: 시스템 콜에 의한 요청 처리  
마이크로 커널: 핵심기능만 포함, 아날로그 연속 입력을 디지털로 변환  
모놀리식 커널: 커널이 만든 기능을 포함하고 있는 커널 아키텍쳐  
{: .notice}
---
#### 리눅스 파일 시스템
---
부트 블록: 파일 시스템으로부터 리눅스 커널 적재
슈퍼 블록: 블록 리스트, 다음 블록 인덱스, 
inode 목록, 빈 inode 수와 목록, 빈블록과 inode 목록에 대한 lock 필드
슈퍼 블록들이 수정되었는지에 대한 flag, 파일시스템이름, 파일 시스템 디스크이름  
아이노드: 파일이나 디렉터리에 대한 모든 정보를 가지고 있는 구조, 파일 이름은 포함 하지 않는다  
데이터 블록: 삭제 데이터가 저장되어 있는 파일 형태  
{: .notice-warning}
---
#### 리눅스 로그 및 파일
---
**전역 설정 파일**
.bash_profile: 사용자 홈 디렉터리에 있는 파일, 개별적인 셸 환경 설정  
.bashrc: 사용자 정의변수, 함수 alias 정의
.bash_logout: 사용자가 로그아웃할 때 실행되는 파일을 정의  
{: .notice}
utmp 파일은 /var/run/utmp에 존재한다 **w 또는 who로 실행 가능**  
{: .notice}
```console
[root@ns2 run]# stat utmp
  File: utmp
  Size: 1536            Blocks: 8          IO Block: 4096   일반 파일
Device: 17h/23d Inode: 13099       Links: 1
Access: (0664/-rw-rw-r--)  Uid: (    0/    root)   Gid: (   22/    utmp)
Context: system_u:object_r:initrc_var_run_t:s0
Access: 2020-05-12 13:13:08.785992118 +0900
Modify: 2020-05-12 13:13:08.785992118 +0900
Change: 2020-05-12 13:13:08.785992118 +0900
 Birth: -
```
wtmp 파일은 로그인과 로그아웃 정보 **last 명령으로 실행 가능**  
{: .notice}
wtmp가 기록하고 있는 정보  
사용자 로그인 및 로그아웃 정보  
시스템 관련 정보  
시스템 종료 및 부팅 정보  
재부팅 정보  
telnet 및 ftp 등을 통한 로그인 정보  
{: .notice--warning}
btmp 파일은 리눅스 로그인시 실패한 정보 **lastb로 실행가능**  
{: .notice}
syslog는 리눅스 운영체제에 대한 로그를 기록하는 데몬 프로세스로 syslogd에 의해서 기록된다  
/etc/syslogd.conf 파일 참고
{: .notice}
---
#### 리눅스 명령어
---
`find 명령어로 악성코드 탐지`  
```console
#find / -name *.php (root Shell 찾기)
#find . -name “[A-Z]*”
#find /var/www -name “[a-z][a-z][0-9]*”
#find . -name “* *” -exec rm -f { }\;

#find / -perm +6000 2>/dev/null
#find /var/www -mtime -20
#find / -user root | more
#find / -nouser -o -nogroup 2>/dev/null
```

`tripwire 파일 무결성 검사`  
```console
#tripwire –init	해시 값을 저장한 데이터베이스를 초기화
#tripwire –check 무결성 검사를 실행
```

**iptables 패킷 필터링**  
체인 종류: INPUT, FORWARD, OUTPUT  
옵션: -A 추가 -N 새로운체인 -X 체인제거 -P 정책변경 -L 규칙상태보기 -F 모든규칙제거  
-Z 체인내의 모든 규칙의 패킷과 바이트의 카운트를 0으로 초기화  
-D 규칙을 삭제 -R 새로운 규칙으로 대체 -I 가장 처음에 규칙 추가 -E 체인이름 변경  
제어 옵션: -s 출발지 -d 목적지 - -sport 출발 포트 - -dport 목적지 포트 -p 프로토콜  
-i 들어오는 인터페이스 -o 나가는 인터페이스 -f fragment패킷 -j 규칙에 해당하는 패킷을 어떻게 할지 명시  
{: .notice}
```console
#iptables -A INPUT -s 127.0.0.1 -p icmp -j DROP
#iptables -A FORWARD -p udp -m udp - -dport 53 -j DROP
#iptables -I INPUT -p tcp - -dport 21 -j ACCEPT
```
**패스워드 크래킹 도구**  
Johan the ripper  
L0phtcrack  
Pwdump  
DDos 도구  
WinNuke  
{: .notice--info}
---
#### 유닉스 PAM 인증
---
PAM(Pluggable Authentication Module)은 리눅스 연결 인증 모듈로 서비스 접속과 관련하여 인증을 지원한다.  
{: .notice}
**인증과정**    
/etc/pam.d/* 서비스 인증 설정 파일  
/lib/security/* PAM 인증 파일  
/etc/security/* PAM 인증 파일의 설정파일  
{: .notice}
**인증 순서**  
사용자 서비스 접근  
서비스는 PAM에게 인증을 요청  
PAM은 요청한 서비스의 설정 파일확인  
서비스는 설정 파일 안에 있는 절차에 맞게 인증 수행  
서비스는 인증 결과 내용을 토대로 서비스 진행 또는 거부  
{: .notice}
---
#### 패스워드 암호화
---
해시함수로 패스워드 암호화시 임의로 추가되는 값은?
{: .notice}
```
$salt = "this is a salt";
$password = 'this is an password';
$hash = sha1($salt.$password);
```
---
#### 유닉스 시스템
---
유닉스에서의 권한은 소유자와 그룹(다른 사용자)로 분류된다.  
home 디렉터리 소유자와 그룹은 rw권한을 가진다.  
{: .notice}
/user/home rw- rw-  
  
/etc/group  
Security:504:user1,user2,user3
{: .notice--info}
---
### 윈도우 프로세스
---
**윈도우 인증 프로세스**  
Winlogon: 윈도우 로그인 프로세스  
GNA(msgina.dll): Winlogon이 이것을 로딩하여 사용자가 입력한 계정과 암호를 LSA에 전달
LSA(lsas.exe): 계정과 암호를 검증하기 위해서 NTLM(암호화)모듈을 로딩하고 계정을 검증  
SRM이 작성한 감사로그를 기록  
SAM: 사용자 계정정보(해시값)에 저장  
리눅스의 /etc/shadow 파일과 같은 역할 수행  
SRM: 사용자에게 고유 SID를 부여하고 SID에 권한을 부여하고
{: .notice}
**윈도우 운영체제 프로세스**  
winint.exe: 윈도우 시작 프로그램    
services.exe: 윈도우 서비스  
lsm.exe: Local Session Manager   
시스템 관리 작업, 주요 함수 실행, 호스트 컴퓨터와 서버의 연결을 관리  
lsass.exe: Local Security Authority Subsystem Service  
사용자 로그인 검사, 비밀번호 변경, 액세스 토큰 생성  
Window Security Log 작성  
svchost.exe: 서비스를 관리하기 위한 프로세스  
conhost.exe: 키보드, 마우스 입력 허용, 문자 출력, 콘솔 API등 셸의 기본 기능을 수행  
{: .notice}

**공유폴더 삭제**  
#net share test /delete  
{: .notice--warning}

---
### 윈도우 레지스트리
---
**루트키**  
HKEY_CLASSES_ROOT: 파일의 각 확장자에 대한 정보와 파일과 프로그램 간의 연결에 대한 정보  
HKEY_LOCAL_MACHINE: 설치된 하드웨어와 소프트웨어 설치 드라이버 설정  
HKEY_USERS: 사용자에 대한 정보  
HKEY_CURRENT_CONFIG: 디스플레이 설정과 프린트 설정  
{: .notice--info}
**하이브 파일**  
레지스트리는 대부분 하이브라고 부르는 파일의 세트에 들어있다  
SYSTEM: 시스템 부팅에 필요한 시스템 전역 구성정보를 가지고 있음  
SOFTWARE: 시스템 부팅에 필요 업슨 시스템 전역 구성정보로 소프트웨어 정보를 가지고 있음  
SECURITY: 시스템 보안 정책과 권한 할당 정보로 시스템 계정만 접근 가능  
SAM: 로컬 계정 정보와 그룹정보로 시스템 계정만 접근 가능  
HARDWARE: 시스템 하드웨어 디스크립션과 모든 하드웨어의 장치 드라이버 매핑 정보를 가지고 있음  
COMPONENTS: 설치된 컴포넌트와 관련된 정보 관리  
BCD0000000000: 부팅 환경 데이터를 관리하는 것은 과거 윈도우 XP의 Boot_ini가 없어지고 대체됨  
{: .notice--info}

* 윈도우 설정 정보는 %SystemRoot%\System32\Config에 저장
	* SMA 사용자 그룹 계정 정보
	* Security 보안 및 권한 관련 정보
	* ntuser.dat 사용자 설정 정보
{: .notice--info}

---
#### 레지스트리 통한 보안
---
* HKLM\SYSTEM\CurrentControlSet\Services\lanmanserver\parameters\AutoShareServer
	* 윈도우 서버 기본 공유 제거
* HKLM\SYSTEM\CurrentControlSet\Services\lanmanserver\parameters\NullSession
* HKLM\SYSTEM\CurrentControlSet\Services\lanmanserver\parameters\restrictanonymous
* HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Current\Vision\Run
	*악성코드 감염이 의심될때 윈도우 부팅시 자동 실행을 수행하는 레지스트리
{: .notice--info}	

1. 공유목적 관리폴더 (제거해야 할 것)
	* C$, D$
	* ADMIN $
	* IPC$
	* PRINT$
	* FAX$
	* net share 명령어를 통해 모든 공유폴더를 확인
2. Null Session 접근 제어
	* HKLM\SYSTEM\CurrentControlSet\Control\Lsa > restrictanonymous 
3. Autologon 비활성화
	* HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\WInlogon
4. 서비스 거부 공격 예방
	* HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters
	* 출처: https://www.ahnlab.com/kr/site/securityinfo/secunews/secuNewsView.do?curPage=81&seq=11818
{: .notice--info}

---
### 윈도우 로그
---

* Syslogd.conf 파일을 사용하며 메시지를 표시하는 필드 및 기록위치를 표시하는 필드로 구성된다.
* 시스템 로그, 보안 로그, 부팅 로그 등을 기록한다
* 로그에 대한 메세지 표준, 다양한 프로그램들이 생성하는 메세지 저장
* 이 메세지를 사용해서 분석 기능을 제공하는 로그 메시지
* 프로그램, 장비 등의 문제점과 성능 확인 가능
* 시스템 관리, 보안, 시스템 정보, 디버깅 메시지 등의 정보를 제공한다
* 이러한 로그를 기록하는 프로세스 이름이 syslogd 이다
{: .notice}

---
### 이벤트 뷰어
---
* 윈도우 이벤트 로그
* 이벤트로그는 응용, 보안, 시스템이 있다.
* 응용프로그램  
* 시스템  
* 보안 이벤트 로그: 로그인 실패 기록, 게정의 잘못된 사용, 잠김, 터미널 공격, 사용자 추가, 사용자 계정 암호 변경 로그가 있다. 
* 기본적으로 경로는 C:\Windows\System32\winevt\Logs 이다.
* 윈도우 시스템 로그를 실시간으로 기록하고 날짜, 시간, 사용자, 컴퓨터, 이벤트 ID, 이벤트 종류, 정보, 경고, 오류 등을 관리한다.
* 로그파일형식 .evt 텍스트파일 형식 .txt 그리고 csv가 있고 system.evt는 로그파일이다. 
{: .notice--info}

---
### ASLR(동적주소)
---

**ASLR(Address Space Layout Randomization)**  
실행파일이 메모리에 로드될 때 동일한 메모리 주소를 가지면 공격자에게 취약한 문제점이 발생된다  
악성코드 적재가 더 수월해진다  
따라서, 윈도우 Vista 부터는 메모리 주소를 항상 동적으로 할당한다  
{: .notice}

`ASLR 해제`
```
sysctl -w kernel.randomize_va_space=0
```
`ASLR 설정`
```
sysctl -w kernel.randomize_va_space=1
```
---
### 랜섬웨어
---

* 랜섬 + 소프트웨어의 합성어이다
* 문서를 암호화하여 경제적 이득을 취한다
* 랜섬웨어 방법으로 크립토락커가 있다
* RSA와 AES로 사용자 문서를 암호화 시켜서 돈을 요구
* SD카드에 사용자 정보를 저장해도 모든 파일을 암호화 한다
{: .notice--info}

---
### 도메인
---

* 도메인은 컴퓨터 계정과 사용자 계정 같은 객체들을 지역적으로 모아놓은 것을 의미한다.
* 도메인 컨트롤러는 액티브 디렉터리 정보의 복사본을 가지고 있는 컴퓨터다.
* 액티브 디렉터리와 정보요청에 응답하고 네트워크 통해 사용자인증, DNS 통합 을 수행한다.
{: .notice}



---
### 그 외
---
`패스워드`  
* **rlogin**
	* ID와 Passwd 없이도 로그인이 가능하다.
	* r-command를 통해서 인증없이 중요 정보 유출, 공격 가능
	* 사용되는 파일 : host.equiv / .rhost 이다 / 권한 600 이하 그리고 + 설정 없어야한다.
* 유닉스 시스템에서 패스워드는 /etc/passwd 파일에 해시값으로 보유하고 있다. 이러한 해시값을 보유하기위해서 임의적으로 필드를 늘려서 해시값을 보이지 않게 한다.
* **shadow 파일**은 사용자 패스워드를 보관하는 파일로 md5를 사용한 해시값을 가지고 있다. 이것은 salt 값을 포함하고 있다.
	* 첫번쨰 $는 사용자가 만든 **해시값**이고 두번째 $는 **salt** 값이다.
{: .notice}






