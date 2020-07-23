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
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/Access_list.JPG" alt="">
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

OSPF 설정
{: .notice}

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

access-list 명령어로 옵션들을 살펴보자
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

* 여기서 0.0.0.0 은 와일드카드로 255.255.255.255 를 뜻한다.
* 이는 한 개의 주소를 가리키는 것이므로 1.1.23.3 과 1.1.30.3 만 허용한다는 뜻이 된다
* 인터페이스 설정으로 들어가서 ip access-group과 함께 그룹 번호 in 을 치면 적용된다
{: .notice}

```
R3#ping 1.1.10.1 source 1.1.3.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.10.1, timeout is 2 seconds:
Packet sent with a source address of 1.1.3.3
UUUUU
Success rate is 0 percent (0/5)
```
























