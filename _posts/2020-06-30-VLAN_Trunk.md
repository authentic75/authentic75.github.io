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

<figure class="align-center">
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
{: .notice}


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
``

show vlan brief 명령어로 다음과 같이 설정된 vlan 정보를 확인할 수 있다.
이때 주목해야할점은 e0/3은 e0/1, e0/2와 다른 vlan에 있어서 arp가 사용하는
브로트캐스트 프레임이 전송되지 않아서 통신이 되지 않는다. 
{: .notice}


---
### VLAN 간의 라우팅
---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/VLAN2.JPG" alt="">
  <figcaption>R4를 추가하고 서브인터페이스를 만들고 VLAN을 할당했다</figcaption>
</figure> 

VLAN 간의 통신이 필요할 때에 사용하는 방법으로 trunking을 이용한다. R4를 SW1에 연결하고 R4의 인터페이스를
논리적으로 분할하여 각각 VLAN 10 과 VLAN 20에 할당한다. 이때 분할된 인터페이스를 서브 인터페이스라고 부른다.
VLAN 10에 소속된 R1과 R2는 게이트웨이를 R4의 VLAN 10에 할당된 서브 인터페이스로 설정하고 VLAN 20에 소속된
R3는 R4의 VLAN 20에 할당된 서브 인터페이스로 설정한다.
{: .notice}

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