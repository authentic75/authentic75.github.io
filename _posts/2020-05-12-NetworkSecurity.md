---
title: "정보보안기사: 네트워크보안"
last_modified_at: 2020-05-11T16:20:02-05:00
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
* TCP SYN Flooding  
* ICMP Flooding  
* Tear Drop (Ping of death)  
* Land Attack  
* HTTP Get Flooding  
* 등등  

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
```console
# ifconfig eth0 promisc	#무차별 모드
# tcpdump -i eth0			#스니핑 시작
```
tcpdump 옵션
```
# tcpdump [ -AdDefIKlLnNOpqRStuUvxX ][ -B buffer_size ][ -c count ][ -C file_size ][ -G rotate_seconds ][ -F file ][ -i interface ][ -m module ][ -M secret ][ -r file ][ -s snaplen ][ -T type ][ -w file ][ -W filecount ][ -E spi@ipaddr algo:secret,... ][ -y datalinktype ][ -z postrotate-command ][ -Z user ]
```
예시
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

---
### 스푸핑
---

---
### VLAN
---

---
### VPN
---

---
### NAC
---

---
### ESM/SIEM
---

---
### IAM
---

---
### DLP
---

---
### 도구들
---