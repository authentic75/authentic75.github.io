---
title: "정보보안기사: 네트워크보안"
last_modified_at: 2020-05-12T16:20:02-05:00
categories:
  - Security
tags:
  - 정보보안기사
toc: true 
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
---
### HTTP
---
---
#### HTTP1.0 헤더
---
**HTTP Request**  
* Request Method: GET, POST, PUT, DELETE, TRACE, CONNECT  
* Accept: 웹브라우저에서 사용 가능한 미디어 타입, text/*, text/html 등  
* Accept-Language: 웹브라우저가 인식 할 수있는 언어  
* User-Agent: 웹브라우저 정보를 의미  
* Accept-Encoding: 웹브라우저에서 제공되는 인코딩 방식  
* Host: 웹 서버의 기본 URL 의미  
* Connection: 연결 지속 및 연결 끊기를 나타내며 Keep Alive 혹은 close  
{: .notice--info}
**Response Header**  
* Status Code: HTTP 응답코드를 의미하고 200은 성공적으로 처리한 것을 의미  
* Server: 서버 프로그램 이름과 버전 전송   
* Expires: 자원의 만기 일자  
* Cache Control: 캐시 사용 여부  
* Pragma: 캐시 사용 여부를 나타냄  
* Content-Encoding: 응답되는 메세지의 인코딩 방식  
* Content-Length: 바이트 단위로 응답되는 리소스의 크기   
* Keep-Alive: 연결 유지 시간을 의미하며 timeout, max 설정  
* Connection: Keep-Alive 사용 여부를 의미  
* Content-Type: 응답되는 미디어 타입  
{: .notice--info}
---
#### HTTP 상태코드
---
* 100번대 조건부 응답으로 요청을 받으면 작업을 계속한다.  
* 200번대 클라이언트가 요청한 동작을 수신 후 성공적으로 처리   
204: 서버가 요청을 성공적으로 처리했지만 콘텐츠를 제공하지 않는다.  
* 300번대 리다이렉션 완료, 클라이언트의 추가 동작이 필요하다.  
* 400번대 요청 오류로 클라이언트에 오류가 있음을 의미한다.  
400: 서버가 요청의 구문을 인식하지 못하였다.  
404: 서버가 요청한 페이지를 찾을 수 없다.  
* 500번대 서버 오류로 서버가 유효한 요청을 수행하지 못했다.  
500: 서버에 오류가 발생하여 요청을 수행 할 수 없다.  
{: .notice}
---
#### HTTP1.1 헤더
---
HTTP/1.0과 1.1 Entity 차이  
entity-hader의 헤더 필드가 다수 추가되었다.  
{: .notice}
Allow  
Content-Encoding  
Content-Length  
Content-Type  
Expires  
Last-Modified  
{: .notice}
**HTTP/1.1에서는 위 필드에 더해 다음과 같은 것들이 추가되었다.**  
Content-Base  
Content-Language  
Content-Location  
Content-MD5  
Content-Range  
ETag  
{: .notice}
---
#### HTTP1.1 특징
---
* 성능의 향상과 요구되는 시간의 최소화  
* persistent connection  
* pipeline  
* 데이터의 압축  
* proxy server와 cache의 사용  
{: .notice}
---
#### httpd.conf
---
* Order Deny, Allow / Deny from all
* Allow from 211.1.1.1
* indexes: 디렉터리 리스팅 차단
* FollowSymLinks: 심볼링크 차단
* ServerToken: 최소한 정보 노출
* Server Signature: Apache 정보노출
* indexes: 디렉터리 리스팅 차단
{: .notice--info}
---
### 쿠키
---
쿠키에 대한 보안을 수행하기 위해서 HTTP ONLY 옵션, 암호화 쿠키를 사용한다  
HTTP ONLY 옵션은 HTTP Request 요청시에만 쿠키를 전송하는 옵션이다  
{: .notice}
---
### SMTP
---
* MUA(Mail User Agent): 메일을 작성하여 보내는 프로그램  
* MTA(Mail Transfer Agent): 이용자로부터 메일을 받아서, 외부로 전달 (sendmail)  
* MDA(Mail Delivery Agent): 전송받은 메일을 해당 사용자에게 전달 (procmail, mail)  
* POP3(Post Office Protocol): 메일을 전송받을 때 사용되는 프로토콜  
* IMAP(Internet Mail Access Protocol): 메일을 전송받을 때 사용되는 프로토콜  
{: .notice}
---
### SMTP 동작
---
SMTP도 TCP 기반 프로토콜이라 **3way hand shaking** 과정을 거친다.  
1. TCP
2. 220, Ready  
3. **EHLO(세션초기화, 확정 목록 요청)**  
4. 250, OK  
5. Data 전송  
6. QUIT  
7. 221, Good bye  
{: .notice--info}  
**메일 전송**  
1. **MAIL FROM(보내는이 알림)**  
2. 250, OK
3. **RCPT TO(받는이 주소 지정)**  
4. 250, OK   
5. Data 전송  
6. 마침표 전송  
7. 250, OK  
{: .notice--info}

---
### 전자우편 보안
---
* 프로토콜
	*SMTP(25, 메일발송) , POP3(110, 메일 읽기, MBOX삭제), IMAP/IMAP4(143, 메일읽기, MBOX 삭제 안함)
* 보안기법
	* PGP: 분산키 관리, RSA, IDEA, MD5 사용
	* PEM: 중앙집중 키 관리, DES-CBC, MD5
	* S/MIME: RSA 개발, 서명 기능
* 스팸차단
	* RBL: KISA에 IP 등록
	* SPF: DNS에 발신 IP 등록
	* Spamassasin: 점수 기반
	* Inflex: 첨부파일 필터링
{: .notice--info}
---
### 라우팅 프로토콜
---
**라우팅 프로토콜 종류**  
* Distance Vector: Hop Count를 정보로 최단 경로를 결정, 메모리 부담  
RIP, IGRP,  EIGRP 
* Link State: Link 변화 발생 시 Hop Count 및 Bandwidth, Delay 등 다양한 Link 정보를 가지고 경로 설정, Traffic 유발    
OSPF, EIGRP  
* Path Vector: (하이브리드) 정책기반으로 라우팅 정보 업데이트  
BGP  
{: .notice--info}
---
#### RIP
---
* 거리 벡터 라우팅 프로토콜  
* 16 Hop 이상은 폐기  
* 180초 이내에 새로운 라우팅 정보 수신이 안되면 경로 이상으로 간주  
* 거리값을 비교하여 라우팅 테이블 변경  
* 모든 망에 변경사항 적용(큰규모 망에는 적합하지 않다)  
{: .notice}
---
#### IGRP
---

---
#### OSPF
---
* Link State Routing Protocol  
* 대규모 IP 망  
* 전송 시간을 링크 비용으로 사용  
* 변화 발생시 링태 상태 정보 교환  
* Hop 수 제한 없음, delay, throughput, relability 이용  
* 네트워크를 Area로 구분하여 성능저하 예방, 대역폭 절약  
* Link 변화 감지시 해당 Link에 대한 정보만을 즉시 모든 라우터에 전달  
* Supernetting 라우터 메모리 절약, 성능 향상 및 대역폭 절약  
{: .notice}
---
#### EIGRP
---
* Hybrid, Link State 라우팅 프로토콜처럼 동작하는 Distance Vector 라우팅 프로토콜, Link State의 특징: 부분적 라우팅 정보 업데이트와 네이버 발견등의 장점이 있다.  
{: .notice}
**DUAL(Diffusing Update Algorithm)**  
* 빠른 수렴을 수행  
* 주기적 라우팅 정보 갱신을 하지 않아 적은 대역폭을 소요  
* Protocol Dependent Module을 통해 다양한 라우팅 프로토콜을 지원  
* RTP(Reliable Transport Protocol)을 사용하여 효과적인 신뢰성  
(필요한 경우만 Ack 가능, Hello와 Ack패킷이 이에 해당)  
* 특히 OSPF에 대비 쉬운 설정이 가능  
* Unequal cost 부하 분산을 지원  
* 매트릭 값이 다른 다수개의 경로를 동시에 사용(대역폭의 확장)  
{: .notice}

**용어**  
* **Neighbor Table**: 인접(Adjacent) 라우터들의 정보가 저장된다  
* **Topology Table**: 도달 가능한 모든 경로에 대한 정보가 있다. 인접 라우터에게서 수신한 네트워크와 그 네트워크의 메트릭 정보를 저장  
* **Routing Table**: 최적의 경로를 선택하여 Successor로 선택한다  
* **Successor**: 라우팅 루프를 갖지 않고 특정 목적지에 대해 가장 우선하는 경로  
* **Feasible Successor(FS)**: 현재 Successor를 경유하는 라우트의 FD보다 작은 RD를 가지는(RD<FD) 넥스트 홉 라우터를 발한다. 백업경로로서 Topology Table에 존재, Successor에 문제 발생 시 Successor의 역할을 수행한다  
* **Feasible Distance(FD)**: 현재 라우터에서 특정 목적지 네트워크까지의 최적 메트릭  
* **Reported Distance(RD)**: 넥스트 홉 라우터에서 목적지 네트워크까지의 메트릭값. Advertised Distance(AD)라고도 한다. EIGRP 라우터들은 넥스트 홉 라우터가 알려주는 RD값을 토폴로지 테이블에 저장한다.  
* **Autonomous System(AS)**: 특정 단체에 속해있는 모든 라우터들의 집합을 의미한다  
{: .notice}
**EIGRP의 다섯가지 패킷 종류**
* **Hello**: 이웃(Neighbor)을 구성, 유지, 발견하는데 사용하며, 멀티캐스트(224.0.0.10)를 사용한다. 인접 라우터에게 주기적으로 헬로 패킷을 전송하고, 헬로 주기의 3배에 해당하는 기간동안에 헬로 페킷을 받지 못하면 인접 라우터에 문제가 발생했다 간주하고 관계를 해제하는데, 이 시간을 홀드시간(Hold time)이라고 한다. 이더넷에서의 헬로주기는 5초이고, 홀드시간은 이의 3배인인 15초이다.  
* **Update**: 라우터에 부분적인 라우팅 정보 변화가 생겼을 경우 이를 전달하는데 사용된다. 경로가 발견되거나 갱신이 끝나을 때 또는 인접라우터와 이웃관계가 구성되면 자신의 토폴로지 테이블에 있는 최적 경로를 업데이트 패킷을 이용하여 네이버에게 전달한다. 유니캐스트 혹은 멀티캐스트(224.0.0.10)를 이용하여 보내진다.   
* **Query**: 라우팅 정보를 요청할때 사용되는 유니/멀티캐스트 패킷이다. 자신의 라우팅 테이블에 있는 경로가 다운되거나 메트릭값이 증가하고, 토폴로지 테이블에 대체 경로(Feasible Successor)를 발견하지 못하면 이웃(Neighbor)한 라우터에게 이 패킷을 보내어 정보를 요청한다.   
* **Reply**: 이 패킷은 Queries 패킷에 대한 응답시 사용되며, Query패킷에 대해 유니캐스트를 사용한다.  
* **Acknowledgement**: 이 패킷은 위에 설명한 패킷 종류 이외의 패킷에 대해 확인의 의미로 사용된다.  
* EIGRP 라우터간 이웃 관계를 맺는 절차는, 상대방에게서 헬로 패킷만 수신하면 바로 상대방을 이웃이라 간주하고, 이웃에게 라우팅 정보를 전송한다. 그러나 상대 라우터가 현재 라우터에게서 헬로 패킷을 수신하기 전이면 상대 라우터는 현재 라우터를 이웃으로 간주하지 않고 라우팅 정보도 전송하지 않는다.  
{: .notice}
**메트릭 요소(vector metric)**  
* Bandwidth(대역폭): EIGRP 프로토콜에서 사용되는 메트릭 요소 중 하나로, 링크의 수용 가능한 능력을 말하며, 기본 단위는 Kilobit이다. 대역폭은 동적으로 인식하지 않고 정적으로 인식한다. 즉, 관리자가 실제로 1544Kbps의 대역폭을 1000Kbps로 라우터의 인터페이스에서 설정할 수 있다. - Bandwidth(EIGRP)=10,000,000/해당 Bandwidth(Kbps) * 256  
* Delay(지연): 메트릭 요소 중 하나로, 네트워크상에서 패킷이 시작점에서 최종 목적지까지 도달하는데 걸리는 시간을 말한다. 기본 단위는 microsecond이다. - Delay(EIGRP) = Delay/10(usec) * 256  
* Reliablilty(신뢰성): 네트워크 상에서 각 링크의 장애율, 즉 , 시작점과 도착점 사이의 신뢰성을 의미하며, 1부터 255사이의 값을 선택할 수 있다.  
* Load(부하): 네트워크 상에서 해당 링크가 어느 정도 활용되고 있는지를 나타낸다. 1부터 255 사이의 값을 선택할 수 있다.  
* Maximum Transmission Unit(MTU, 최대 전송 단위) : 목적지까지 가는 각 인터페이스의 MTU중에서 가장 작은 최대 전송 단위이다.  
{: .notice}
* **Diffusing Update Algorithm(DUAL)과 Finite State Machine(FSM)**  
* EIGRP는 Diffusing Update Algorithm(DUAL)을 이용하여 매 순간 라우트 계산을 통해 루프를 방지한다. DUAL 알고리즘은 모든 라우터들이 동일 시간에 토폴로지 변화를 동기화 할 수 있게 한다. 그리고 토폴로지 변화에 영향을 받지 않는 라우터들은 라우터 재계산에 참여하지 않는다.  
출처: https://raptor-hw.net/xe/know/17234
{: .notice}

---
#### BGP
---
* AS 간의 Routing에 적용되는 프로토콜, ISP 사업자들 간에 사용  
* EGP (inter domain routing) 특징을 가짐  
* 경로 벡터 라우팅 방법 적용, 경로 벡터 라우팅 테이블 유지  
* TCP 기반 routing  
* 변화가 있을때 neighbor에게 갱신정보 advertising함  
* 다양한 routing metric 사용  
* CIDR 지원  
* IBGP(internal BGP): 동일한 AS에 속한 라우터  
* EBGP(External BGP): 서로 다른 AS에서 동작  
{: .notice}
---
#### CIDR
---
Classes Inter Domain Routing  
기존 클래스 마스크보다 더 적은 마스크 사용하여 요약  
(IPv4 주소 공간을 더 효율적으로 사용)  
-> 불필요한 라우팅 업데이트 트래픽 감소  
{: .notice}
* default 클래스풀 마스크 보다 더 적은 마스크들을 사용해서 요약한다.  
* 라우팅 업데이트 안에 있는 엔트리들의 수를 감소   
* 로컬 라우팅 테이블에 있는 엔트리들의 수를 감소   
* 라우팅 업데이트들에 대한 대역폭 이용률을 감소  
* 더 빠른 라우팅 테이블 룩업을 가능  
{: .notice}
IGRP + CIDR, VLSM = EIGRP 개발  
RIPv2 부터 CIDR 지원  
Ex) /24, /22 꼴로 나타냄  
{: .notice--info}

---
### DDos
---
* Syn Flooding : Half Open  
* Get Flooding: Half Open x  
* TCP SYN Flooding:    
* ICMP Flooding:    
* Tear Drop (Ping of death): ICMP 사용하여 패킷 분할 유도 Half Open x  
* smurf: ICMP 사용, ICMP Flooding, Half Open x  
* Land Attack    
* HTTP Get Flooding  
* UDP Flooding
	* 대응방법: 외부 유입(inbound) Broadcast 차단, DDos 대응 솔루션, chrgen 또는 **echo 서비스 중단** 
*Switch Jamming: 유도된 MAC주소를 네트워크에 지속적으로 흘리고 스위치 허브 주소를 오버플로 시킴
* 등등  
{: .notice--info}
---
### DRDoS
---
* 3 way handshaking 취약점 이용하여 공격자가 출발지 IP 주소를 공격대상의 IP 주소로 위조해서 SYN 패킷을 정상적인 TCP 서버에게 전송
* IP Spoofing 으로 은닉 공격 할 수 있다
* 반사체라는 제 3자를 통해서 수행한다
* 
{: .notice--info}
---
### 포트 스캐닝
---

**NMAP 포트 스캐닝**
* TCP connection() Scan: 3-way handshaking 수립, 쉽게 탐지  
* TCP SYN Scan: SYN/ACK 받으면 OPEN, RST/ACK 받으면 Close, Stealth Scanning  
* TCP FIN Scan:  RST 받으면 Close 
* TCP Null: 모든 플래그 지운다, RST 받으면 Close  
* TCP X-MAS Tree Scan: FIN, URG, PSH 패킷 전송, RST 받으면 Close  
{: .notice}
**NMAP 옵션**
SCAN Type (-s*)  
-sS TCP SYN SCAN -sT TCP Connection SCAN -sU UDP SCAN  
-sF TCP FIN SCAN -sN TCP Null SCAN -sA TCP ACK SCAN  
{: .notice}
Port Option (-p)  
-p [번호/프로그램이름/포트범위/T:tcp/U:udp]
{: .notice}
---
### 스니핑 공격
---
무차별 모드 실행
{: .notice}
```console
# ifconfig eth0 promisc	#무차별 모드
# tcpdump -i eth0			#스니핑 시작
```
tcpdump 옵션
{: .notice}
```
# tcpdump [ -AdDefIKlLnNOpqRStuUvxX ][ -B buffer_size ][ -c count ][ -C file_size ][ -G rotate_seconds ][ -F file ][ -i interface ][ -m module ][ -M secret ][ -r file ][ -s snaplen ][ -T type ][ -w file ][ -W filecount ][ -E spi@ipaddr algo:secret,... ][ -y datalinktype ][ -z postrotate-command ][ -Z user ]
```
예시
{: .notice}
```console
# tcpdump -i eth0				=> 인터페이스 eth0 을 보여줌
# tcpdump -w tcpdump.log		=> 결과를 파일로 저장, txt 가 아닌 bin 형식으로 저장됨
# tcpdump -r tcpdump.log		=> 저장한 파일을 읽음
# tcpdump -i eth0 -c 10			=> 카운터 10개만 보여줌
# tcpdump -i eth0 tcp port 80		=> tcp 80 포트로 통신하는 패킷 보여줌
# tcpdump -i eth0 tcp port 80		=> tcp 80 포트로 통신하는 패킷 보여줌
# tcpdump -i eth0 src 192.168.0.1	=> source ip 가 이것인 패킷 보여줌
# tcpdump -i eth0 dst 192.168.0.1	=> dest ip 가 이것인 패킷 보여줌
```
`출처: https://moyaria.tistory.com/`
---
### 세션하이재킹
---
* TCP 시퀀스 넘버가 잘못되면 이를 바로잡기 위한 작업을 하는데, TCP 세션 하이재킹은 서버와 클라이언트에 각각 잘못된 시퀀스 넘버를 위조해서 연결된 세션에 잠시 혼란을 준 뒤 자신이 끼어들어 가는 방식이다  
* 세션, 텔넷, FTP 등 TCP 를 이용한 거의 모든 세션의 갈취  
* 인증 문제 해결을 위한 일회용 패스워드, 토큰 기반 인증을 이용한 세션도 갈취  
{: .notice}
세션하이재킹도구들  
* Hunt: 네트워크상의 감시, 가로채기  
* Arpspool: 공격자의 주소로 속이는 행위  
* IP Watcher: 네트워크상의 연결, 감시 및 세션 가로채기  
* Ferret: 세션 정보를 가로채는 도구  
* Hamster: Proxy 서버 상태로 만들어 주는 도구  
* Paroc: 웹 Proxy 서버로서 쓸 수 있는 도구  
* Can & Abel: 스푸핑과 스캐닝 등 다양한 기능이 있는 도구  
* WireSharl  
{: .notice--warning}
대책  
* 재전송 증가 탐지  
* ACK Strom 탐지  
* 암호화방식  
* 지속적 인증  
{: .notice}
---
#### 세션하이재킹 순서
---
1. 클라이언트와 서버는 연결된 상태, 스니핑 진행 중  
2. 공격시점에 비동기화 상태중 세션이 끊어지지 않는 시퀀스 넘버 범위에서 RST 패킷 생성 후 서버로 전송  
서버는 클라이언트가 연결을 끊은 줄 알고 잠시 Close, 클라이언트는 유지  
3. 공격자는 A_Client_My_Seq를 생성하여 서버에 보냄  
4. 서버는 새로운 연결인 A_Client_My_Seq를 받아들이고, Server_MY_Seq를 재생성하여 공격자에게 보낸 후 Syn_Receive 상태가됨  
5. 정상 연결 처럼 시퀀스 넘버 교환, Establish 상태가 됨
{: .notice--info}
---
### 스푸핑
---
`arpspoof`  
```console
# arpspoof -i eth0 -t 110.15.241.121 110.15.241.1
# -i 인터페이스 -t 타겟과 게이트웨이  -s 감시
```
snort: Rule과 동일한 패킷을 탐지하는 침입 탐지 시스템  
snort의 Payload 검사 : Content, Dept, Offset, Nocase, Rawbytes, Within, Uricontent, Urilen, isdataat, pcre
{: .notice}
```console
# snort -c /etc/snort/rules/test.rules
# vi /etc/snort/rules/test.rules
```
---
### VLAN
---
VLAN (Virtual LAN)  
* port기반 VLAN은 스위치 포트에 VLAN 할당   
* MAC기반 VLAN은 각 호스트들의 MAC주소를 VMPS에 등록한 후, 호스트가 스위치에 접속하면 등록된 정보를 바탕으로 VLAN을 할당  
* 네트워크 주소기반 VLAN은 주소별로 VLAN을 구성하여 같은 네트워크에 속한 호스트 간에만 통신이 되도록 하는 방법  
* 프로토콜 기반 VLAN 같은 프로토콜을 가진 호스트 간에만 통신이 되도록 함  
{: .notice}

---
### DNS 보안
---

* 사용자는 도메인으로 접속하게 되면 도메인 안에 네임서버 도메인과 IP가 지정되어 있습니다
* 이러한 네임서버에서는 1차부터 4차까지 무작위로 네임서버의 접근하여 사용자가 접속한 도메인의 레코드 정보를 찾게 됩니다
{: .notice}

|   영   역   |   도  메  인   |       
| ---------- | ------------ |
| Root domain | COM, ORG, KR |
| Top level | GOOGLE, FreeBDS, NE|
| Second level | WWW, FTP(GOOGLE), WWW, KR(FREEBSD), NOBREAK(NE) |
| Third of Subdomains |WWW, FTP(KR), WWW(NOBREAK) |
| Subdomains | (*원래는 트리구조)  |

* Root domain(.): 모든 도메인의 근본이 되는 최상 level Domain  
* Top Level Domain: com, org, kr 등의 국가, 지역
* Second Level: 사용자가 도메인명을 신청해서 등록할 수 있는 영역  
{: .notice--warning}

1. Recursive Query
2. Iterative Queries
3. Response from Root Name Server
4. Query to Top Level Name Server  
5. Response from Top Level Name Server 
6. Query to Second-level Name Server
7. Response from Second-level Name Server  
8. DNS Client Get IP
{: .notice}

```console
#dnsspoof 도구로 DNS Spoofing을 할 수 있다.
#dnsspoof -I eth0 -f dns.host
```

* **DNSSEC**: DNS 캐시 포이즈닝과 DNS의 보안 취약점을 보완하기 위해 등장,
DNS 응답 정보에 전자서명 값을 첨부하여 보내고 수신층이 서명 값 검증. DNS 위/변조 방지   
* DNS SEC은 DNS 인증 기능을 추가하여 인증된 메세지에 대해서만 DNS가 동작하는 것으로 위의 지문에서는 파밍과 관련된다.  
{: .notice--info}

* DNS UDP 53번 포트에 무한 도메인 질의를 수행하면 DNS는 무한 질의의 결과값을 전송하여 네트워크 및 DNS 서버에 부하를 발생시킨다.  
* ARP spoofing Attack으로 외부의 DNS 서버로 보내는 쿼리를 DNS 서버로 가기 전에 가로채어 공격한다  
* Master DNS와 Slave DNS 간의 Zone Transfer 정보를 변조하여 Slave DNS의 DNS Cache 값을 변조한다
* arpspoof 도구를 이용하여 arp cache를 변조 하는것을 poisoning 이라고 부른다.
{: .notice--info}

레코드
* SOA (Start Of Authority) 레코드: 도메인의 모든 정보와 권한을 의미합니다. 
* NS (Name Server) 레코드: 도메인의 네임서버를 지정하는 레코드 입니다.  
* A (Address Mapping Records) 레코드: 별명(별칭)을 지정해주는 레코드이고 도메인 위임으로도 합니다.  
* CNAME (Canonical NAME) 레코드: 별명(별칭)을 지정해주는 레코드이고 도메인 위임이라고도 합니다.   
* MX (Mail eXchanger) 레코드: 메일서버의 연동시 메일의 소유를 확인하는 레코드로 쓰입니다  
* SPF (Sender Policy Framework) 레코드: 레코드 TXT 레코드에 안에서 사용되며, 메일 스푸핑을 방지하는데 사용되는 레코드 입니다.   
{: .notice--warning}
---
### nslookup
---
* 해당 HostName의 해당 IP를 알려주는 명령  
* DNS 레코드를 확인 할 수 있는 대화식 프로그램. 윈도우와 리눅스 모두 사용 가능   
* ANY옵션은 호스트관련 모든 레코드 정보를 확인 한다  
* DNS에 대해서만 서비스한다  
{: .notice}
---
### 보안 솔루션
---
---
### 방화벽
---
* Screening Router 내부 네트워크에서 외부 네트워크로 나가는 패킷 트래픽을 허가 및 거절하거나 혹은 외부 네트워크에서 내부 네트워크로 진입하는 패킷 트래픽의 진입 허가 및 거절, 패킷 필터링
* Dual Home 두개의 네트워크 인터페이스 를 가진 Bastion 호스트, Proxy
* Screened Subnet 외부와 내부 네트워크 완충지대 구축, 완충 지대에 DMZ 위치
* Screened Host 스크린된 호스트 게이트웨이는 Dual-Homed 게이트웨이와 스크리닝 라우터를 혼합하여 사용한 방화벽 시스템
* DPI OSI 전 계층에서 필터링 수행
* 서킷 게이트웨이: 트랜스포트 계층, TCP 중계 역할 수행
{: .notice--warning}
---
#### VPN
---
VPN (Virtual Private Network)  
* 공중망을 이용하여 사설망과 같은 효과를 얻는 프로토콜  
* 패스워드 인증/ USB 인증 -> 2 Factor 인증 제공  
* 터널링 -> VPN 클라이언트/서버 간에 암호화키 교환 후 암호화하여 메시지 주고받음  
{: .notice}

SSL VPN  
* 별도 장비 필요 없음, 웹브라우저만으로 VPN 구현, 네트워크 레이어 암호화 방식이라 HTTP 뿐 아니라 NNTP, FTP에도 사용  
* SSl 자체의 부하(암복호화 지연)  
* 인증: X.509 공개키 인증서  
* 무결성: MAC 기업을 사용하여 데이터 변조 여부 확인  
* 기밀성: 대칭키 암호  
* 부인봉쇄: 부가적인 SW로 응용계층에서 전자서명 허용  
{: .notice--info}

IPSEC VPN  
* **터널모드**: 중계 장비가 패킷 전체를 암호화  
* **전송모드**: 패킷 출발지에서 암호화, 목적지에서 복호화, End to End   
  
* **ISAKMP**: Security Association 설정, SA관리와 키교환  
* **IKE**: 키교환 담장, UDP로 전달  
  
* **AH**: 데이터 무결성 (IP 패킷 인증, MAC 기반), 순서번호 제공, MD5, SHA-1 사용, 인증값 검증  
* **ESP**: 전송자료를 암호화하여 전송, 수신자가 받은 자료를 복호화, 선택적 인증, 무결성, 기밀성, AH와 다르게 암호화 제공(DES, 3-DES), Transport 계층 까지 암호화 할 경우 Transprot 모드 사용, 전체 패킷 암호화할 경우 터널 모드 사용   
{: .notice--info}

MPLS VPN  
* 기존의 IP Routing 데이터 전송 방식과 다르게, 데이터의 목적지 IP 주소를 확인하는 대신 ‘Label’사용  
* IP 주소가 어떤 Label를 사용할 것인지를 광고  
* 각각의 라우터는 라우팅 테이블과 동시에 Label 테이블을 학습  
용어  
* VPLS(Virtual Private Lan Service)는 MPLS 기반 터널링 기술 사용한다  
* **Label** : 일반 패켓 헤더에 덧붙여진 추가 정보. MPLS에서는 Label에 저장된 값을 이용하여 스위칭을 실시한다.  
* **Label Switching** : 데이터 전송이 아닌 데이터에 Label를 추가하고 여기에 포함된 값을 이용하여 Layer 2 기반에서 바로 스위칭 하는 방법을 말한다. 
* **LER(Label Edge Router)** : 사용자 및 가입자 네트워크가 연결된 라우터를 뜻한다. LER는 사용자 및 가입자 네트워크에서 들어오는 일반 패켓에 Label를 추가하여 MPLS 패켓으로 전환하는 역할을 맡고 있다.  
* **LSR(Label Switch Router)** : MPLS Provider 네트워크의 백본에 연결되어 있는 라우터를 뜻한다. LER로부터 전달받은 MPLS 패켓에서 Label 정보를 확인하여 자신의 LFIB에서 순차적으로 검사한다.  
* **LSP(Label Switch Path)** : MPLS를 구성한 라우터와 라우터 상호간에 생기는 경로를 뜻한다. MPLS 패켓은 바로 LSP 경로를 이용하여 전송된다.  
* **LDP(Label Dynamic Protocol)** : MPLS를 구성한 라우터와 라우터 상호간에 LSP를 동적으로 생성하는 프로토콜을 뜻한다. 또한, 설정된 회선의 대역폭 사용률에 따라 자동으로 생성할 수 있다.  
{: .notice}
---
#### NAC
---
**네트워크 연결을 제한하는 것으로 IP주소, MAC주소를 사용하여 네트워크 연결을 허용 및 거부하며 무결성 확인도 수행한다**  
{: .notice}
Agent 방식
* 사용자 단말기 NAC Agent를 설치  
* IP, MAC, Port, Protocol별 접근제어  
* 사용자별 접근제어  
* Patch지원, 하드웨어 및 프로세스 정보, 백신 연동 등  
{: .notice}
Agentless 방식
* 별도의 설치 필요 없다
* IP, MAC, Port, Protocol별 접근제어  
* 사용자별 접근제어  
* Agent 설치 없이 IP 관리, 사용자 PC에 연결할 스위치 및 포트 정보
* 호스트명, 도메인만 관리
{: .notice}
---
#### ESM/SIEM
---
SIEM  
* 기업의 모든 자원의 정보 및 로그를 통합해서 수집  
* 보안 시스템, 보안 SW, 네트워크 장비, 어플리케이션 로드, 이벤트, 구성 정보, 시스템 정보, 웹로그 등  
* 사용자, Port, IP, 애플리케이션 등의 작은 단위로 분석이 가능하다  
* APT 공격 및 알려지지 않은 패턴에 대해서 분석한다  
* **빅데이터 기술**인 MapReduce, Indexing 등을 사용한다  
* 고성능의 데이터분석이 가능  
{: .notice} 
ESM  
* 보안 솔루션 위주의 로그 수집 통합  
*  보안 시스템, 서버 시스템 로그, 이벤트 등으로 수집되는 로그가 한정적  
* 시그니처 중심으로 분석  
* IP, Port 단위 분석과 알려진 패턴 위주의 분석  
* Agent 혹은 API를 사용해서 로그 수집  
* **관계형 데이터베이스**의 테이블에 데이터를 입력하고 분석  
{: .notice}
---
#### IAM
---
AWS Identity and Access Management(IAM)  
* AWS 리소스에 대한 액세스를 안전하게 제어할 수 있는 웹 서비스   
* IAM을 사용하여 리소스를 사용하도록 인증(로그인) 및 권한 부여(권한 있음)된 대상을 제어  
* AWS 계정을 처음 생성할 때는 해당 계정의 모든 AWS 서비스 및 리소스에 대한 완전한 액세스 권한이 있는 SSO(Single Sign-In) ID로 시작합 (루트 사용자)  
{: .notice--info}
---
#### DLP
---
DLP(Data Loss Prevention, 데이터 손실 방지)
* 기업 구성원, 프로세스, 기술의 결합을 통해 고객 또는 직원 기록 등의 개인 신원확인 정보(PII), 재무제표, 마케팅 계획과 같은 기업 정보, 제품 계획, 소스 코드와 같은 지적 재산(IP)을 포함하는 기밀 정보 등이 기업 밖으로 유출되는 것을 방지하는 솔루션   
{: .notice}
---
---
#### BYOD 보안
---
VID는 개인의 업무공간을 서버에 할당하고 사용자가 접속하여 사용하는 가상화 솔루션이다  
데스크톱 환경을 중앙으로 통합함  
기사에서 잘 안나오는 문제다  
{: .notice}

---
### 도구명 및 용어
---
`Spoofing 도구`  
* sort
* arpspoof: ARP Spoof 사용하는 툴
{: .notice}

`세션하이재킹 도구`  
* Hunt
* Arpspool
* IP Watcher
* Ferret
* Hamster
* Paroc
* Can & Abel
* WireSharl
{: .notice}

* Email 공격유형
	* Active Contents
	* 트로이 목마: 증식 x 
	* 버퍼 오버플로
{: .notice}
* DNS 싱크홀: 봇에 감염된 PC가 공격자와 연결을 시도할때 싱크홀 서버에 연결하여 공격자에게 공격당하지 않게 하는것.
{: .notice}

* prstat은 어떤 프로세스가 CPU 자원을 소모하는지 확인하는 프로그램이다
	* pstat -s cpu -n 5
* TCP Wrapper TCP 래퍼 (TCP Wrapper)는 호스트 기반 네트워킹 ACL 시스템으로서, 리눅스 또는 BSD 같은 운영 체제의 인터넷 프로토콜 서버에서 네트워크 접근을 필터링하기 위해 사용된다.
* netcat netcat (often abbreviated to nc ) is a computer networking utility for reading from and writing to network connections using TCP or UDP.
* iptables 패킷 필터링 도구, 방화벽, NAT에 사용
* nmap 포트 스캐닝
* driftnet: urlsnar 툴에 의한 URL 이미지 파일을 보여주는 툴  
* urlsnarf: 웹 요청하는 URL을 잡아 주는 툴
* dsniff: 아이디 / 패스워드를 잡아주는 전문 툴
* webspy: urlsnar 툴에의한 URL을 웹브라우저로 전송하는 툴
{: .notice}

`무결성 검사 도구`
* tripwire
* fcheck
* shmhain
{: .notice}

`보안 점검 도구`
* SAINT 유닉스, 네트워크 취약점, HTML 보고서, 원격 취약점 점검
* SARA SATAN을 기반으로 개발, 네트워크 기반 컴퓨터, 서버, 라우터, IDS에 대한 취약점 분석, HTML 보고서
* COPS 시스템 취약성 점검, 시스템 내부의 취약성
* Nessus 클라이언트/서버 구조로 클라이언트의 취약점 점검, 600개 이상의 취약점 점검 가능
* NMAP 
{: .notice}



