---
title: "Network: VLAN 과 Trunk"
last_modified_at: 2020-06-30T20:20:02-05:00
categories:
  - Network
tags:
  - Routing protocol
  - EIGRP
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---


VLAN(Virtual LAN)이란 논리적으로 분할된 스위치를 말한다.
{: .notice}

* 특징
* 스위치에 접속된 장비들의 성능 향상 및 보안성 증대
* 브로드캐스트 프레임이 전송되는 범위(브로드캐스트 도메인 크기)를 줄여준다
	* 불필요한 프레임 처리 감소
	* L3 장비에서 VLAN 간 트래픽을 제어하여 보안성 제고
{: .notice}

---
### 토폴로지 기본 설정 
---

<figure class="half">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/VLAN.JPG" alt="">
  <figcaption>VLAN을 위한 기본 토폴로지 설정</figcaption>
</figure> 

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#host R1
R1(config)#no ip domain-lookup
R1(config)#
R1(config)#line console 0
R1(config-line)#logg sync
R1(config-line)#exec-t 0
R1(config-line)#
R1(config-line)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#
R1(config-line)#int fa 0/0
R1(config-if)#ip address 1.1.10.1 255.255.255.0
R1(config-if)#no shut
```

```
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#host R2
R2(config)#no ip domain-lookup
R2(config)#
R2(config)#line console 0
R2(config-line)#logg sync
R2(config-line)#exec-t 0
R2(config-line)#
R2(config-line)#line vty 0 4
R2(config-line)#password cisco
R2(config-line)#
R2(config-line)#int fa 0/0
R2(config-if)#ip address 1.1.10.2 255.255.255.0
R2(config-if)#no shut
```

```
R3#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R3(config)#host R3
R3(config)#no ip domain-lookup
R3(config)#
R3(config)#line console 0
R3(config-line)#logg sync
R3(config-line)#exec-t 0
R3(config-line)#
R3(config-line)#line vty 0 4
R3(config-line)#password cisco
R3(config-line)#
R3(config-line)#int fa 0/0
R3(config-if)#ip address 1.1.20.3 255.255.255.0
R3(config-if)#no shut
```

---
### VLAN 설정 
---


R1, R2, R3 설정을  끝내고 SW1에서 몇가지 명령어를 실행해보자
{: .notice}

```
SW1#show cdp neighbors
Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                  S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone,
                  D - Remote, C - CVTA, M - Two-port Mac Relay

Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
R2               Eth 0/2           93              R S I  3745      Fas 0/0
R3               Eth 0/3           92              R S I  3745      Fas 0/0
R1               Eth 0/1           172             R S I  3745      Fas 0/0
SW1#
```

```
SW1#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0, Et0/1, Et0/2, Et0/3
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup
```

* VLAN은 번호로 구분한다
* 사용가능한 번호는 1~4094
	* 1 ~ 1005는 normal VLAN 이라고 한다
	* 1002 ~ 1005 토큰링과 FDDI 프로토콜용으로 사용 (실제로 사용 x )
	* 이더넷에서 사용할 수 있는 번호 1 ~ 1001
* 기본적으로 스위치의 모든 포트는 VLAN 1에 소속되어있다
* 1006 ~ 4094는 extended VLAN이라고 하다 
{: .notice--warning}


```
SW1#conf t
SW1(config)#vlan 10
SW1(config-vlan)#name ADMIN
SW1(config-vlan)#exit
SW1(config)#vlan 20
SW1(config-vlan)#name SALES
SW1(config-vlan)#exit
```

* VLAN 10을 생성하고 이름을 ADMIN으로 지정했다
* VLAN 20을 생성하고 이름을 SALES로 지정했다
{: .notice}

```
SW1(config)#interface range e0/1-2
SW1(config-if-range)#switchport mode access
SW1(config-if-range)#switchport access vlan 10
SW1(config-if-range)#exit
SW1(config)#
SW1(config)#interface e0/3
SW1(config-if)#switchport mode access
SW1(config-if)#switchport access vlan 20
```

* e0/1과 e0/2의 설정 모드로 들어간 후 하나의 vlan에만 소속되도록 액세스 포트로 지정합니다
	* 액세스 포트로 지정하지 않을 시, DTP 프로토콜이 동작하여 인접한 장비와의 협상으로 포트 모드가 결정된다
* Static DTP 포트: 명시적으로 액세스 포트 지정
* 협상과정을 거치는 Dynamic DTP 포트의 경우 보안상 취약하다
{: .notice}

```
SW1#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0
10   ADMIN                            active    Et0/1, Et0/2
20   SALES                            active    Et0/3
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup
SW1#
```

show vlan brief 명령어로 다음과 같이 설정된 vlan 정보를 확인할 수 있다.
이때 주목해야할점은 e0/3은 e0/1, e0/2와 다른 vlan에 있어서 arp가 사용하는
브로트캐스트 프레임이 전송되지 않아서 통신이 되지 않는다. 
{: .notice}


---
### VLAN 간의 라우팅
---

<figure class="half">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/VLAN2.JPG" alt="">
  <figcaption>R4를 추가하고 서브인터페이스를 만들고 VLAN을 할당했다</figcaption>
</figure> 

VLAN 간의 통신이 필요할 때에 사용하는 방법으로 trunking을 이용한다. R4를 SW1에 연결하고 R4의 인터페이스를
논리적으로 분할하여 각각 VLAN 10 과 VLAN 20에 할당한다. 이때 분할된 인터페이스를 서브 인터페이스라고 부른다.
VLAN 10에 소속된 R1과 R2는 게이트웨이를 R4의 VLAN 10에 할당된 서브 인터페이스로 설정하고 VLAN 20에 소속된
R3는 R4의 VLAN 20에 할당된 서브 인터페이스로 설정한다.
{: .notice--warning}

```
R4#conf t
R4(config)#interface f0/0
R4(config-if)#no shut
R4(config-if)#interface f0/0.10
R4(config-subif)#encapsulation dot1Q 10
R4(config-subif)#ip address 1.1.10.254 255.255.255.0
R4(config-subif)#exit

R4#conf t
R4(config)#interface f0/0.20
R4(config-subif)#encapsulation dot1Q 20
R4(config-subif)#ip address 1.1.20.254 255.255.255.0
R4(config-subif)#end
```
interface f0/0.10 과 f0/0.20 으로 서브인터페이스 생성함  
ip address로 각각 다른 주소를 할당해줌  
{: .notice}

```
SW1#conf t
SW1(config)#interface e0/0
SW1(config-if)#switchport trunk encapsulation dot1Q
SW1(config-if)#switchport mode trunk
```

R4와 연결된 인터페이스 e0/0을 트렁크로 설정해준다
{: .notice}

```
R1(config)# ip route 0.0.0.0 0.0.0.0 1.1.10.254
R2(config)# ip route 0.0.0.0 0.0.0.0 1.1.10.254
R3(config)# ip route 0.0.0.0 0.0.0.0 1.1.20.254
```
모르는 목적지의 패킷이 오면 정해놓은 게이트웨이로 보내도록 설정 (디폴트 게이트웨이)
{: .notice}


```
R1#ping 1.1.20.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.20.3, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 28/42/48 ms
```

R1과 R3의 통신이 가능해졌다
{: .notice}


---
### 트렁킹 이란?
---

**트렁크 또는 트렁크 포트란** 복수개의 VLAN에 소속된 포트를 말한다.
그리고 트렁크가 사용하는 프로토콜을 트렁킹 프로토콜이라고 부른다.
하나의 VLAN에 소속된 포트를 **액세스 포트**라 부른다.
{: .notice}

같은 VLAN에 속해있는 SW1이 트렁크 포트를 통해 SW2로 프레임을 전송한다고 가정하자.
일반 이더넷 프레임에는 VLAN 번호를 표시하는 필드가 없어서 SW2는 SW1에서 수신한
브로드캐스트 프레임을 어느 VLAN으로 플러딩해야할지 알 수 없다. **브로드캐스트 프레임을
 전송할 때 VLAN 번호를 표시해야한다. 이 때 필요한 것이 트렁킹 프로토콜이다.**
{: .notice}

* 트렁킹 프로토콜이 동작하는 방식
* 액세스 포트를 통하여 프레임을 수신하면 스위치는 해당 프레임이 소속된 VLAN 번호를 알 수 있다
* SW1이 트렁크 포트를 통하여 해당 프레임을 전송하면서 VLAN 번호를 표시한다
	* 프레임을 수신한 SW2는 VLAN 표시를 확인하고 프레임이 소속된 VLAN 번호를 알게된다
* SW2는 프레임의 VLAN 정보를 제거하고 VLAN 10에 소속된 모든 포트로 프레임을 전송한다
	* 장비들은 VLAN의 존재를 인식하지 못한다
* VLAN 표시 방법은 ISL 혹은 802.1Q 두가지를 사용한다
{: .notice}

---
### 802.1Q 트렁킹
---

* 802.1Q 트렁킹은 IEEE 802.1Q에서 정의된 표준 트렁킹 프로토콜 입니다 
* 이더넷 프레임의 출발지 주소 다음에 4바이트 길이의 802.1Q 태그를 추가하여 VLAN 번호와 기타정보를 표시한다
{: .notice}

* **802.1Q 프레임 태그** 
* 이더타입: 현재 프레임이 802.1Q 프레임이라는 것을 표시. 항상 값이 0x8100
* 우선순위: 프레임의 우선순위를 표시한다. 0~7 값을 가지며 클수록 우선순위가 높다
* VLAN 번호: 프레임의 VLAN 번호 표시. 12비트로 4096개의 VLAN이 지원된다
{: .notice}

ISL은 시스코에서 개발한 트렁킹 프로토콜이다 802.1q와 달리 extended VLAN을 지원하지 못한다. 프레임 앞에 26바이트의 헤더를 추가하는 방식
그리고 이더넷 FCS 뒤에 ISL FCS를 붙이는 것이 특징. 간단히 하고 넘어가겠다.
{: .notice}


```
SW1(config-if)# switchport trunk encapsulation dot1q
```

* **네이티브 VLAN**
* 802.1q 에서 지원한다
* 한 트렁크 포트로 전송시 VLAN 표시를 하지 않는 기능이다
* 표시가 되지 않은 프레임을 수신한 SW는 해당 프레임을 네이티브 VLAN인 VLAN 10 으로만 전송한다
* 802.1q 를 사용하지 않는 기본 네이티브 VLAN은 1번이며 보안상 변경하는 것이 좋다
{: .notice}

---
### DTP 모드 
---

**DTP(Dynamic Trunking Protocol)**란 시스코 스위치에서 
상대 스위치와 트렁크 관련 사항을 협상할 때 사용되는 프로토콜이다. DTP는 트렁크 포트 전환 여부와
트렁크 포트로 동작시 사용할 프로토콜을 협상한다. 
{: .notice}

* **정적포트**
* 액세스 모드: 항상 액세스 포트로 동작
* 트렁크 모드: 항상 트렁크 포트로 동작
* **동적 포트**
* 다이내믹 다자이어러블: 상대가 액세스 포트인 경우에만 액세스 포트로 동작  
* 다이내믹 오토: 상대가 액세스나 다이내믹 오토인 경우 액세스 포트로 동작, 나머지 경우는 트렁크 포트로 동작
{: .notice}

```
SW1(config-if)# switchport mode access
```

액세스 포트 설정
{: .notice}

```
SW1(config-if)# switchport trunk encapsulation dot1q
SW1(config-if)# switchport mode trunk
```

트렁크 포트 설정 
{: .notice}

이더스위치 모듈에서는 access와 trunk 모드만 지원됩니다. 
{: .notice--warning}



















