---
title: "Security: Cisco ASA 기본 기능"
last_modified_at: 2020-08-19T20:20:02-05:00
categories:
  - Security
tags:
  - Firewall
  - ASA
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

---
### GNS3 설치 및 설정
---

* **필요한 사항**
* GNS3 2.1.12 버전 이상
* ASAv(ASA virtual) 이미지 파일
{: .notice--warning}

* **설치**  
* GNS3 2.1.12 설치 시작
* 설치 항목 선택: winpcap , wire shark 제외 (이미 사용하고 있는 경우)
* 별도의 솔라윈드 설치 x, Set up 과정은 넘어간다
{: .notice--info}

* **설정**  
GNS3 프로젝트들을 저장하기 위해 D 드라이브를 생성해두었다. (5~10GB 정도)  
Edit > General, Binary Images 탭에 아래와 같이 경로를 입력한다.  
D:\GNS3\projects  
D:\GNS3\symbols  
D:\GNS3\configs  
D:\GNS3\appliances  
D:\GNS3\images  
{: .notice--info}

* **라우터 등록**
* 라우터 Image 파일 c1700과 c3745를 준비해둔다.
* IOS routers 탭에 가서 
* new > c1720 등록 > 완료
* new > c3745 등록 > wic에 2T 추가 (확장) > 완료
{: .notice--info}

* **Qemu(asa 추가)**
* Qemu VMs 탭에서 New 선택
* Name: ASAv 9.6(2)
* RAM: 2048MB
* Console Type: telnet
* Disk Image > New Image 사용할 Image 파일 선택
* **configuration**
	* Default name format: FW{0}
	* Symbol: /symbols/asa.svg
	* vCPUs: 2
* Network 탭에서
	* Adapters: 6 
	* Name format: Gi{0} 
	* Type: Gigabit Ethernet 
* Advanced Settings
	* Additional settings 에서 Option을 입력해줘야한다.
	* -cpu Haswell -smp 4,sockets=4,cores=1,threads=1 
	* CPU 마다 조금씩 다르므로 꼭 체크 하고 입력할 것
	* Use as a linked base VM 체크
{: .notice--info}

* **잊지말고 처음 설치시 해야할 사항**
* 프로젝트를 생성하여 Router1 과 Router2를 올리고 마우스 오른쪽 키를 눌러 auto idle PC 설정을 해준다.
* CPU 점유율이 5% 아래로 내려갈 때까지 둘다 설정해준다. 안그러면 매번 실행 할 때마다 CPU 사용률이 엄청 올라간다.
{: .notice--danger}


---
### ASA의 ip 할당
---

```
>enable
#conf t
#hostname FW1
#int gi 0/0
#nameif inside
#ip add 1.1.1.2 255.255.255.0
#no shut
#ping 1.1.1.1
```

라우터와 ASA의 명령어는 살짝 다르다. 일반적으로 라우터에서 ip라는 단어가 빠진 형태라고 생각하면된다. 
show ip route는 show route 로 ip route 는 route 다. 
단, show ip int brief 는 show int ip brief로 사용한다.   
**눈여겨 볼 점은 asa에서는 nameif 라는 명령어로 security level을 설정 할 수 있다.**
{: .notice}

---
### 트랜스패런트 모드, 라우터 모드
---

방화벽은 L2 또는 L3로 동작 시킬 수 있다. L2로 동작 시 트랜스패런트 모드라고 하고 L3로 동작시 라우터 모드라고 한다.
현재 동작 상태를 확인 하기 위해 show firewall을 입력해보자.
{: .notice}

```
FW1# show firewall
Firewall mode: Router
FW1# conf t
FW1(config)# firewall transparent
WARNING: Disabling auto import may affect Smart Licensing
Creating trustpoint "_SmartCallHome_ServerCA" and installing certificate...

Trustpoint CA certificate accepted.
ciscoasa(config)# show firewall
Firewall mode: Transparent
```

스위치는 브로드캐스트를 나눌 수 없다. 그런데 스위치에 VLAN을 2개 줬다고 가정하고 각각 12개씩 포트를 줬다고 하면
 각각의 VLAN을 통해 브로드캐스트 도메인이 생성된다. 그리고 두 도메인은 서로 통신을 못한다.
{: .notice}

---
### 단일 컨텍스트와 다중 컨텍스트
---

asa 에서는 하나의 방화벽으로 복수개의 방화벽 처럼 동작하도록 할 수 있다.
 이처럼 다수 개의 가상 장비로 동작하는 것을 다중 컨텍스트라고 부른다.  
예로, 다중 컨텍스트가 적용된 경우 방화벽을 2개로 나누어서 포트를 연결하고 (VLAN 처럼) 각각 다른 정책을 설정하는 개념과 비슷하다
{: .notice}

---
### 기본 동작 테스트를 위한 토폴로지 구성
---

기본적인 토폴로지를 구성해보자, 왼쪽 끝에는 Cloud 를 올리고 설정에서 VirtualBox #2 네트워크로 설정해준다. 아이콘도 PC로 바꿔준다.
{: .notice}

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/asa_basic.jpg" alt="">
  <figcaption> </figcaption>
</figure>

```
#R1
conf t
int f0/0
ip add 10.10.10.1 255.255.255.0
no shut
int f0/1
ip add 10.1.10.1 255.255.255.0
no shut

#R2
conf t
int f0/0
ip add 1.1.20.2 255.255.255.0
no shut

#FW1
ciscoasa> enable
Password:
ciscoasa# conf t
ciscoasa(config)# hostname FW1
```

---
### Security level (inside, outside, dmz)
---

* asa 방화벽에는 아래와 같이 security level 이 정해져 있다.
* Inside 100
* Dmz 50
* Outside 0
* Inside -> Dmz(허용) inside->outside(허용) dmz->outside(허용) 
	* 높은레벨에서 낮은레벨로 접속허용
{: .notice--info}

Nameif로 인터페이스 이름(별명)을 지정해주는 동시에 보안 레벨을 설정해준다. 그러면 사용자 설정으로도 만들수 있나..? 가능하다!
 레벨이 높은데서 낮은데로 못간다는건 거의 deny all의 의미와 비슷하다. ACL로 원하는 부분은 허용해주는 방식으로 주로 사용하게 된다.
{: .notice}

nameif를 사용하여 gi 0/0와 gi 0/1 인터페이스를 설정해보자. 내부망은 inside로 외부망은 outside로 설정해준다.
{: .notice}

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/asa_basic.jpg" alt="">
  <figcaption> </figcaption>
</figure>

---
### 추가 설정 및 ping 테스트
---

FW1에서 inside와 outside를 설정하고 ping을 해보자.
{: .notice}


```
FW1(config-if)# int gi 0/0
FW1(config-if)# nameif inside
FW1(config-if)# ip add 10.1.10.10 255.255.255.0
FW1(config-if)# no shut
FW1(config-if)#
FW1(config-if)# ping 10.1.10.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.10.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 10/26/70 ms
```
```
FW1(config-if)# int gi 0/1
FW1(config-if)# nameif outside
FW1(config-if)# ip add 1.1.20.10 255.255.255.0
FW1(config-if)# no shut
FW1(config-if)# ping 1.1.20.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.20.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/16/30 ms
FW1# ping 10.1.10.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.10.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 10/20/30 ms
```

일반 라우터에서는 show ip int brief 로 설정한 값을 조회했지만, asa에서는 show int ip brief로 조회한다.
 asa 명령어는 보통의 경우 router 명령어에서 ip 라는 단어를 뺀 형태가 많지만 이 명령어는 예외다.
{: .notice}

```
FW1# show int ip brief
Interface                  IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0         10.1.10.10      YES manual up                    up
GigabitEthernet0/1         1.1.20.10       YES manual up                    up
GigabitEthernet0/2         unassigned      YES unset  administratively down up
GigabitEthernet0/3         unassigned      YES unset  administratively down up
GigabitEthernet0/4         unassigned      YES unset  administratively down up
Management0/0              unassigned      YES unset  administratively down up
```

window10 컴퓨터의 주소를 잠시 10.10.10.10/24로 설정하고 cmd 창을 켜서 10.10.10.1로 ping을 보내보자.
 10.10.10.1은 ping이 잘되지만 10.1.10.1은 안될 것이다.
{: .notice}

```
>route ADD 10.0.0.0 MASK 255.0.0.0 10.10.10.1
>route ADD 1.1.20.0 MASK 255.255.255.0 10.10.10.1
```
위와 같이 라우팅 테이블을 추가해주면 longest match rule에 의해서 10.1.10.1에 도달 할 수 있다.
하지만 그 건너인 10.1.10.10 에는 도달 할 수 없다. 오로지 직접 연결된 곳이나 라우팅 테이블에 추가된 부분만 닿을 수 있다.
{: .notice}

이러한 문제를 해결해주기 위해 모르는 주소로 부터 방화벽으로 패킷이 도달하면 1.1.20.2 로 보내도록 설정해준다.
{: .notice}

```
FW1# conf t
FW1(config)# route outside 0.0.0.0 0.0.0.0 1.1.20.2
FW1(config)# show route

Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, V - VPN
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, + - replicated route
Gateway of last resort is 1.1.20.2 to network 0.0.0.0

S*       0.0.0.0 0.0.0.0 [1/0] via 1.1.20.2, outside
C        1.1.20.0 255.255.255.0 is directly connected, outside
L        1.1.20.10 255.255.255.255 is directly connected, outside
C        10.1.10.0 255.255.255.0 is directly connected, inside
L        10.1.10.10 255.255.255.255 is directly connected, inside
```

그리고 이어서 ospf 설정을 해준다.
{: .notice}

```
FW1(config)# router ospf 1
FW1(config-router)# network 10.1.10.0 255.255.255.0 area 0
FW1(config-router)# default-information originate
FW1(config-router)# end
```
```
R1(config)#router ospf 1
R1(config-router)#network 10.10.10.0 0.0.0.255 area 0
R1(config-router)#network 10.1.10.0 0.0.0.255 area 0
R1(config-router)#
*Mar  1 01:03:18.723: %OSPF-5-ADJCHG: Process 1, Nbr 10.1.10.10 on FastEthernet0/1 from LOADING to FULL, Loading Done
R1(config-router)#do show ip route | begin Gate
Gateway of last resort is 10.1.10.10 to network 0.0.0.0

     10.0.0.0/24 is subnetted, 2 subnets
C       10.1.10.0 is directly connected, FastEthernet0/1
C       10.10.10.0 is directly connected, FastEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 10.1.10.10, 00:00:11, FastEthernet0/1
R1(config-router)#
```

이로써 Windows 10에서 10.1.10.10으로 ping이 가능해 진다.  
반대로 R2에서는 모르는 곳으로 부터 패킷을 받으면 1.1.20.10 으로 보내도록 설정해주자
{: .notice}

```
R2# conf t
R2(config)# ip route 10.0.0.0 255.0.0.0 1.1.20.10
```

---
### 기본적인 방화벽의 동작 방식
---

* 보안 레벨이 높은 인터페이스에서 낮은 인터페이스로의 트래픽은 모두 허용
* 보안 레벨이 낮은 인터페이스에서 높은 인터페이스로의 트래픽은 모두 차단
* 보안 레벨이 높은 곳에서 낮은 곳으로 이동하는 경우, TCP/UDP 같이 Stateful한 트래픽은 돌아오는 패킷들도 허용
	* 외부에서 오는 패킷은 포트 번호 등 정보 확인 후 내부에서 시작된 패킷이면 자동으로 허용해준다.
	* ICMP는 Stateful 하지 않다. ping을 이용하려면 따로 추가 설정을 해줘야한다.
* 보안 레벨이 동일한 인터페이스 간에도 기본적으로 트래픽을 차단한다. 허용하려면 same-security-trffic 명령어를 이용한다.
{: .notice}






































