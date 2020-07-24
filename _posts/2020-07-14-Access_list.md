---
title: "Network: Access list"
last_modified_at: 2020-07-14T20:20:02-05:00
categories:
  - Network
tags:
  - DHCP
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

---
### 토폴로지 기본 설정 
---

<figure class="half">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/Access_list.jpg" alt="">
  <figcaption>VLAN을 위한 기본 토폴로지 설정</figcaption>
</figure> 

라우터 설정
{: .notice}

```
#R1
Conf t
Int fa 0/0
Ip add 1.1.10.1 255.255.255.0
No shut
Int s 0/0
Ip add 1.1.12.1 255.255.255.0
No shut
```
```
#R2
Conf t
Int fa 1/1
Ip add 1.1.12.2 255.255.255.0
No shut
Int s 0/1
Ip add 1.1.23.2 255.255.255.0
No shut
```
```
#R3
Conf t
Int s 0/1
Ip add 1.1.23.3 255.255.255.0
No shut
Int fa 0/0
Ip add 1.1.30.3 255.255.255.0
No shut
Interface lo0
Ip address 1.1.3.3 255.255.255.0
```

---
### OSPF 설정 
---

```
#R1
Router ospf 1
Router-id 1.1.1.1
Network 1.1.10.1 0.0.0.255 area 0
Network 1.1.12.1 0.0.0.255 area 0
```

```
#R2
Router ospf 1
Router-id 2.2.2.2
Network 1.1.10.2 0.0.0.255 area 0
Network 1.1.12.2 0.0.0.255 area 0
```

```
#R3
Router ospf 1
Router-id 3.3.3.3
Network 1.1.10.3 0.0.0.255 area 0
Network 1.1.12.3 0.0.0.255 area 0
```

연결 확인
{: .notice}

```
R1#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     1.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
O       1.1.3.3/32 [110/129] via 1.1.12.2, 00:03:33, Serial0/0
C       1.1.10.0/24 is directly connected, FastEthernet0/0
C       1.1.12.0/24 is directly connected, Serial0/0
O       1.1.23.0/24 [110/128] via 1.1.12.2, 00:04:13, Serial0/0
O       1.1.30.0/24 [110/138] via 1.1.12.2, 00:03:43, Serial0/0
```

```
R3#ping 1.1.10.1 source 1.1.3.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.10.1, timeout is 2 seconds:
Packet sent with a source address of 1.1.3.3
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/3/8 ms
```

---
### Access List
---

* ACL은 특정한 패킷을 차단 또는 허용할때 사용한다.
* IPv4 ACL, IPv6 ACL, MAC ACL 등이 있다.
* 출발지 IP 주소만 참조하는 것을 **표준 ACL**, 출발지/목적지 IP, 프로토콜 번호 및 전송계층의 포트번호까지 참조하는 것을 **확장 ACL** 이라고 한다.
* 번호 또는 이름을 사용하여 정의할 수 있다.
* 라우터의 부담을 줄여준다. 보통 서버가 7계층 방화벽을 통해 일일이 확인하는데 ACL을 사용하면 3~4계층에서 필터링 할 수 있다.
{: .notice}

* **네트워크 보안 장비**
* 방화벽: 사전에 정의된 규칙을 이용하여 트래픽 제어
* VPN 게이트웨이: 공중망을 사설망 처럼 사용할 수 있도록 하는 장비, 패킷의 암호화, 인증, 변조 확인 등 기능 제공
* 침입방지 시스템: 가변적인 공격 트래픽 차단, 탐지 가능 (IDS와 IPS 로 나누기도 한다)
{: .notice}

* **표준 IP ACL**
* 출발지만 가지고 필터링, 제한적
* 보통 목적지에 가까운 곳에 설정 (좋은 설정은 x , 방화벽으로는 잘 사용하지 않는다)
{: .notice}

```
R2(config)#access-list ?
  <1-99>            IP standard access list
  <100-199>         IP extended access list
  <1000-1099>       IPX SAP access list
  <1100-1199>       Extended 48-bit MAC address access list
  <1200-1299>       IPX summary address access list
  <1300-1999>       IP standard access list (expanded range)
  <200-299>         Protocol type-code access list
  <2000-2699>       IP extended access list (expanded range)
  <300-399>         DECnet access list
  <600-699>         Appletalk access list
  <700-799>         48-bit MAC address access list
  <800-899>         IPX standard access list
  <900-999>         IPX extended access list
  dynamic-extended  Extend the dynamic ACL absolute timer
  rate-limit        Simple rate-limit specific access list
```
```
R2(config)#access-list 1 ?
  deny    Specify packets to reject
  permit  Specify packets to forward
remark  Access list entry comment
```

* access-list 다음에 오는 숫자는 그룹 번호라고 생각하면 된다
* R2(config)#access-list 1 remark Test ACL  // 코멘트를 더할 수 있다
{: .notice}

```
R2(config)#access-list 1 permit 1.1.23.3 ?
  A.B.C.D  Wildcard bits
  log      Log matches against this entry
  <cr>
R2(config)#access-list 1 permit 1.1.23.3 0.0.0.0
R2(config)#access-list 1 permit 1.1.30.3 0.0.0.0
R2(config)#do show access-list
Standard IP access list 1
    10 permit 1.1.23.3
    20 permit 1.1.30.3
R2(config)#int s 0/1
R2(config-if)#ip access-group 1 in
```

* access-list 다음에 오는 1은 그룹 번호다
* 여기서 0.0.0.0 은 와일드카드로 255.255.255.255 를 뜻한다.
	* 서브넷 마스크는 반드시 연속된 1을 사용해야한다, 와일드 마스크는 연속될 필요가 없다
	* 와일드 카드는 비연속적 주소도 묶을 수 있다 (0.0.252.255 하면 4의 배수만 뽑아낼수있다)
* 이는 한 개의 주소를 가리키는 것이므로 1.1.23.3 과 1.1.30.3 만 허용한다는 뜻이 된다
* 인터페이스 설정으로 들어가서 ip access-group과 함께 그룹 번호 in 을 치면 적용된다
{: .notice}

허용한 주소만 Ping이 가능하다
{: .notice}

```
R3#ping 1.1.10.1 source 1.1.30.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.10.1, timeout is 2 seconds:
Packet sent with a source address of 1.1.30.3
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/4 ms
```

```
R3#ping 1.1.10.1 source 1.1.3.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.10.1, timeout is 2 seconds:
Packet sent with a source address of 1.1.3.3
UUUUU
Success rate is 0 percent (0/5)
```

---
### access-list 조회
---

```
R2#show run | include access-list
access-list 1 remark Test ACL
access-list 1 permit 1.1.23.3
access-list 1 permit 1.1.30.3
```

```
R2#show access-list
Standard IP access list 1
    10 permit 1.1.23.3 (190 matches) // 몇 개의 패킷이 해당되는지 통계도 나온다
    20 permit 1.1.30.3 (5 matches)
R2#show ip access-list		// IP로 설정된 access-list만 확인한다
Standard IP access list 1
    10 permit 1.1.23.3 (191 matches)
    20 permit 1.1.30.3 (5 matches)
```

잘못설정한 경우 모두 통과 시킨다. 좁은 범위부터 적용해야한다. 넓은 범위 부터 적용하면 공통된 부분에 대한 규칙은 무시함.
{: .notice}
```
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#access-list 2 permit 1.1.0.0 0.0.255.255
R2(config)#access-list 2 permit 1.1.30.0 0.0.0.255
R2(config)#int s 0/1
R2(config-if)#ip access-group 2 in
R2(config-if)#
```

---
### access-list 지우기
---


* 그룹 전체를 지울 수도 있고 따로 지울 수도 있다.
* no access-list 2 1.1.0.0 0.0.255.255
{: .notice}

```
R2(config-if)#no access-list 2
R2(config)#do show ip access-list
Standard IP access list 1
    10 permit 1.1.23.3 (348 matches)
    20 permit 1.1.30.3 (5 matches)
R2(config)#
```

```
R2#conf t
R2(config)#access-list 10 deny host 1.1.3.3
R2(config)#access-list 10 deny host 1.1.12.2
R2(config)#access-list 10 permit any
R2(config)#int s 0/0
R2(config-if)#ip access-group 10 out

R2(config-if)#do ping 1.1.12.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.12.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/3/12 ms
```

---
### 특정 서비스에 대한 access-list
---

```
R2(config)#do show run | include access-list
access-list 99 permit 1.1.10.1
access-list 99 remark Telnet Control
access-list 99 permit 1.1.12.0 0.0.0.255
access-list 100 permit ospf host 1.1.23.3 any
access-list 100 permit tcp host 1.1.23.3 host 1.1.12.1 eq telnet
access-list 100 permit tcp host 1.1.23.3 host 1.1.12.1 eq www
access-list 100 permit ip 1.1.30.0 0.0.0.255 1.1.10.0 0.0.0.255
R2(config)#
```

---
### 이름을 이용한 access-list
---

```
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#ip access-list standard acl_internet_in
R2(config-std-nacl)#permit 1.1.23.3
R2(config-std-nacl)#permit 1.1.30.0 0.0.0.255
R2(config-std-nacl)#exit
R2(config)#int s 0/1
R2(config-if)#ip access-group acl_internet_in in
```
---
### 확장 access-list
---

확장 ACL을 이용한 트래픽 제어
{: .notice}
```
R2(config)#ip access-list extended acl_s0/1_inbound
R2(config-ext-nacl)#permit ospf host 1.1.23.3 any //동적 라우팅 프로토콜 이용시 필수로 입력
R2(config-ext-nacl)#permit tcp host 1.1.23.3 host 1.1.12.1 eq telnet
R2(config-ext-nacl)#permit tcp host 1.1.23.3 host 1.1.12.1 eq 8
R2(config-ext-nacl)#permit tcp host 1.1.23.3 host 1.1.12.1 eq 80
R2(config-ext-nacl)#permit ip 1.1.30.0 0.0.0.255 1.1.10.0 0.0.0.255
R2(config-ext-nacl)#Exit

R2(config-ext-nacl)#interface s 0/1
R2(config-if)#ip access-group acl_s0/1_inbound in
```

```
R2(config)#ip access-list extended option-control// 옵션이 들어간 것을 거부하겠다
R2(config-ext-nacl)#Deny ip any any option record-route
R2(config-ext-nacl)#Permit ip any any
R2(config-ext-nacl)#Exit 

R2(config)#Int e0/1
R2(config-if)#Ip access-group option-control in
```

* Ping 1.1.12.1 size 18024 로 보내면 ping이 잘 가지만  
* Extended ping을 설정해서 보내면 unreachable이 뜬다  
* TTL도 IP헤어에 들어가있는 헤더이다
	* 얼마 이상의 TTL에 대해서 어떻게 제어할것인가
	* ip access-list extended ttl ?
	* lt, gt, 등등
{: .notice}


ACL로그 표시도 할 수 있다.
Icmp 에 대해서만 로그표시를 하도록 만들어 보자
R2#do show ip access-list option-control
#ip access-list extended option-control
#15 permit icmp any any echo
#no 15 	// 중복되면 설정 안되서 15 잘 못설정한 것을 지우자
#15 permit icmp any any echo log
#do show ip access-list option-control

R4#ping 1.1.12.1 repeat 10

보내면
R2에서는 이와 관련하여 메시지가 뜬다
(실시간 모니터링을 위해 쓰는 옵션이다)



#ip access-list extended outside-acl-in
#permit ospf host 1.1.23.3 any  //라우팅 테이블 유지를 위해 R3로부터 오는 ospf 패킷 허용
#permit icmp host 1.1.34.4 host 1.1.12.1 echo// ping으로 R4로부터 패킷 오는 것 허용
#exit

#int e0/1
#ip access-group outside-acl-in in 

예시
R1# conf t
#ip domain-name cisco.com
#crypto key generate rsa modulus 1024
#line vty 0 4
#login local   //계정 정보 기반 인증
#transport in ssh telnet // 내부망에서 ssh telnet으로 오는 것 허용
#exit
#username admin password cisco
#enable password cisco

R2# int fa 0/1
#ip access-group option-control in
#exit

R4# ping 1.1.12.1
#telnet 1.1.12.1
#ssh -l admin 1.1.12.1  // 방화벽 설정 된 것이 없으니 다 잘 될 것이다

R2# ip access-list extended outside-acl-in
#permit ospf host 1.1.23.3 any   //R3의 라우팅 정보허용
#permit icmp any 1.1.12.0 0.0.0.255 echo // 목적지가 1.1.12.0 네트워크면 허용
#exit

#int fa 0/1
#ip access-group outside-acl-in in
설정후에 

R4# ping 1.1.12.1
#telnet 1.1.12.1  허용 안됨
#ssh -l admin 1.1.12.1 허용 안됨

R1# ping 1.1.23.3  허용 안됨 
#ping 1.1.34.4  허용 안됨  외부 네트워크라 


R1          R2             R3             R4
Fa0/0   fa0/0  fa0/1   fa0/0   fa0/1   fa0/0
1.1.12.0           1.1.23.0         1.1.34.0
현재 방화벽은 R2의 fa 0/1 에서 동작하고 있다.
R1에서 R4로 핑을 보내면 왜 막힐까? 
Outbound 설정안해서 일단 R1에서 나가는 것은 통과한다 
그런데 R4에서 Request를 받았으니 reply가 돌아오는데
R2에 암묵적인 모든 패킷을 차단한다는 정책에 따라서 reply가 막히고
R4에게 unreachable 이 간다.

그래서
R2# ip access-list outside-acl-in
#permit icmp any 1.1.12.0 0.0.0.255 echo-reply 를 입력해줘야한다

문제를 내보겠다
R4에서만 R1으로 telnet과 ssh 접속이 가능하도록 설정해봐라
나머지는 거부 되도록 해야한다
Permit [protocol] Src_IP  [src_port] Dst_IP [Dst_port]
	Tcp	host x.x.x.x		eq Num
	Udp	any			gt Num
	Icmp	Net_ID Wildbit		lt Num

#ip access-list ftp-in  //ftp 
#permit tcp host 1.1.12.0 host 1.1.23.0 eq 21
#permit tcp host 1.1.12.0 host 1.1.23.0 eq 20


우선 R2 acl 설정을 다시 보여주겠다
R2(config)#ip access-list extended outside-acl-in
R2(config-ext-nacl)#permit ospf host 1.1.23.3 any
R2(config-ext-nacl)#permit icmp any 1.1.12.0 0.0.0.255 echo
R2(config-ext-nacl)#int fa 0/1
R2(config-if)#ip access-group outside-acl-in in
R2(config-if)#


자 이제 문제로 다시 돌아와서 ~
Protocol 번호는 6 (tcp)
Src IP 는 1.1.34.4
Dst IP는 1.1.12.1

Src Po:1024~
Dst po: 23

우선 Telnet
R2(config-ext-nacl)#permit tcp host 1.1.34.4 gt 1024 host 1.1.12.1 eq 23
다음은 SSH
R2(config-ext-nacl)#permit tcp host 1.1.34.5 gt 1024 host 1.1.12.1 eq 22
이렇게 하면 접근 되어야 한다.  (난 안됨)
R4#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R4(config)#line vty 0 4
R4(config-line)#password cisco
R4(config-line)#login
R4(config-line)#transport in telnet
R4(config-line)#end

R1에서 R4로 Telnet만 접속가능하도록 추가해봐라
R1 > R2 > R4
Src: 1.1.12.1 
Dst: 1.1.34.4
Src_port: 1024 
Dst : 23

R2 < R4  막힌다
Src 1.1.34.4
Dst 1.1.12.1
Src 23
Dst 1024 
Permit tcp host 1.1.34.4 eq 23 host 1.1.12.1 gt 1024

R2#show ip access-list
Extended IP access list outside-acl-in
    10 permit ospf host 1.1.23.3 any (280 matches)
    20 permit icmp any 1.1.12.0 0.0.0.255 echo
    30 permit tcp host 1.1.34.4 gt 1024 host 1.1.12.1 eq telnet
    40 permit tcp host 1.1.34.5 gt 1024 host 1.1.12.1 eq 22
R2#conf t
R2(config)#no ip access-list extended outside-acl-in

문제!  내부망(1.1.12.0/24)에서 외부망 어디든지(any, 0.0.0.0/0)으로 
Ping (ICMP-Echo Request)이 가능하도록 설정하시오. 
# permit icmp any 1.1.12.0 0.0.0.255 echo-reply  이거 같은데
어라 안되네  답은 이거 맞다 
R4가 라우팅 정보를 못받아서 생긴 일이엇다
문제!
조각이난 icmp는 못받게 하고
그러고 나서 나머지 echo request는 받아들인다

R2(config-ext-nacl)#do show ip access-list
Extended IP access list acl_outside_in
    10 permit ospf host 1.1.23.3 host 224.0.0.5 (211 matches)
    30 deny icmp any 1.1.12.0 0.0.0.255 fragments (195 matches)
    40 permit icmp any 1.1.12.0 0.0.0.255 echo (20 matches)

어라 나는 되는데 

이상하게 35가 위에 올라가면 echo-reply 가 인식해서 그게 올라가버린다.
선생님도 모른다 그런데 예상은 fragments 가 조립이 안되어서 무슨 패킷인지 몰라서 걸러버리는것일까 

30 deny ip any 1.1.12.0 0.0.0.255 fragments
35 permit icmp any 1.1.12.0 0.0.0.255 echo-reply  이거를 껴주자
40 permit icmp any 1.1.12.0 0.0.0.255 echo (20 matches)

















