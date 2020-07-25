---
title: "Security: Access list"
last_modified_at: 2020-07-16T20:20:02-05:00
categories:
  - Security
tags:
  - Access list
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
  <figcaption>기본 토폴로지 설정</figcaption>
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

---
### 새로운 토폴로지 설정 
---

<figure class="half">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/Access_list2.jpg" alt="">
  <figcaption>기본 토폴로지 설정</figcaption>
</figure>

R1~4까지 설정하고 OSPF로 라우팅 정보를 받도록 해보자
{: .notice}

```
R1#enable
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface fa 0/0
R1(config-if)#ip address 1.1.12.1 255.255.255.0
R1(config-if)#no shut
R1(config-if)#exit
```
```
R2#enable
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#interface fa 0/0
R2(config-if)#ip address 1.1.12.2 255.255.255.0
R2(config-if)#no shut
R2(config-if)#exit
R2(config)#interface fa 0/1
R2(config-if)#ip address 1.1.23.2 255.255.255.0
R2(config-if)#no shut
R2(config-if)#exit
```
```
R3#enable
R3#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R3(config)#interface fa 0/0
R3(config-if)#ip address 1.1.23.3 255.255.255.0
R3(config-if)#no shut
R3(config-if)#exit
R3(config)#interface fa 0/1
R3(config-if)#ip address 1.1.34.3 255.255.255.0
R3(config-if)#no shut
R3(config-if)#exit
```
```
R4#enable
R4#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R4(config)#interface fa 0/0
R4(config-if)#ip address 1.1.34.4 255.255.255.0
R4(config-if)#no shut
R4(config-if)#exit
```

``` 
R1(config)#router ospf 1
R1(config-router)#network 1.1.2.1 0.0.0.0 area 0
R1(config-router)#
```
```
R2(config-if)#router ospf 1
R2(config-router)#network 1.1.12.2 0.0.0.0 area 0
R2(config-router)#network 1.1.23.2 0.0.0.0 area 0
R2(config-router)#
```
```
R3(config)#router ospf 1
R3(config-router)#network 1.1.23.3 0.0.0.0 area 0
R3(config-router)#network 1.1.34.3 0.0.0.0 area 0
R3(config-router)#
```
```
R4(config)#router ospf 1
R4(config-router)#network 1.1.34.4 0.0.0.0 area 0
```
```
Routing Table
R1#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     1.0.0.0/24 is subnetted, 3 subnets
C       1.1.12.0 is directly connected, FastEthernet0/0
O       1.1.23.0 [110/20] via 1.1.12.2, 00:03:24, FastEthernet0/0
O       1.1.34.0 [110/30] via 1.1.12.2, 00:03:24, FastEthernet0/0
```

---
### 확장 Access List 
---

확장 Access List를 보기전 실험에 필요한 Ping에 대해서 보도록 하자.
{: .notice}

* Nomal Ping
* #Ping 1.1.12.1 repeat 1
{: .notice--info}

* Extended Ping
* CLI에 그냥 ping만 입력하면 옵션을 위한 입력을 받는데, 좀더 상세한 Ping 설정을 할 수 있다.
{: .notice--info}

```
#ping
>프로토콜 :
>IP주소 : 1.1.12.1
>Repeat Count: 1
>Datagram 크기: 
>timeout seconds: 
>command: y 
>Source address or interface
>Type of service:
>Set DF bit in IP header [no]:  MTU 관련 옵션
>Validate reply data? [no]: 점검 할지 안할지
>Data Pattern :
>Loose, Strict, Record, Timestamp, Verbose: 
				//여기서 record를 사용하면 default 9개 홉(경로)를 기억한다
>Record route [9]: 기억할 경로(홉) 수
```
이러한 것들이 IP 헤더에 들어간다
{: .notice--info}

**Extended Ping**을 막기 위한 ACL들을 작성해보자
{: .notice}
```
R2(config)#ip access-list extended option-control
					//이름을 이용한 ACL
R2(config-ext-nacl)#Deny ip any any option record-route
					//Record-route 옵션이 들어간 것을 거부하겠다
R2(config-ext-nacl)#Permit ip any any
R2(config-ext-nacl)#Exit 
R2(config)#Int e0/1
R2(config-if)#Ip access-group option-control in
```
Ping 1.1.12.1 size 18024 로 보내면 ping이 잘 가지만 Extended ping을 설정해서 보내면 unreachable이 뜬다
{: .notice}

* **TTL**
* #ip access-list extended ttl ? 을 이용하여 일정 이상 또는 이하의 TTL을 가지는 패킷을 막을 수 있다.
* gt 와 lt 같은 옵션으로 범위가 지정 가능하다 
{: .notice}

* **ACL 로그**
* ACL은 로그도 지원한다
{: .notice}
```
#do show ip access-list option-control
#ip access-list extended option-control
#15 permit icmp any any echo log
#do show ip access-list option-control

R4#ping 1.1.12.1 repeat 10
```

---
### 확장 Access List 예시
---

예시 1
{: .notice--warning}
```
#ip access-list extended outside-acl-in
#permit ospf host 1.1.23.3 any  //라우팅 테이블 유지를 위해 R3로부터 오는 ospf 패킷 허용
#permit icmp host 1.1.34.4 host 1.1.12.1 echo// ping으로 R4로부터 패킷 오는 것 허용
#exit

#int e0/1
#ip access-group outside-acl-in in
```

예시 2-1
{: .notice--warning}

```
R1# conf t
#ip domain-name cisco.com
#crypto key generate rsa modulus 1024
#line vty 0 4
#login local   //계정 정보 기반 인증
#transport in ssh telnet // 내부망에서 ssh telnet으로 오는 것 허용
#exit
#username admin password cisco
#enable password cisco
```

```
R2(config)#ip access-list extended option-control
R2(config-ext-nacl)#Deny ip any any option record-route
R2(config-ext-nacl)#Permit ip any any
R2(config-ext-nacl)#Exit 
R2# int fa 0/1
R2(config-if)#Ip access-group option-control in
#ip access-group option-control in
#exit
```
Record-route 옵션을 가진 패킷을 제어한다 
{: .notice}

```
R4# ping 1.1.12.1
#telnet 1.1.12.1
#ssh -l admin 1.1.12.1
```
일반 ping, telnet, ssh 에 대해서는 ACL이 설정된것이 없으므로 잘 실행된다
{: .notice}

```
R2# ip access-list extended outside-acl-in
#permit ospf host 1.1.23.3 any   //R3의 라우팅 정보허용
#permit icmp any 1.1.12.0 0.0.0.255 echo // 목적지가 1.1.12.0 네트워크면 허용
#exit

#int fa 0/1
#ip access-group outside-acl-in in
```
그렇다면 R3로 부터 나오는 ospf를 허용하고 1.1.12.0 으로 오는 ping에 대해서만 허용한다면
{: .notice}

```
R4# ping 1.1.12.1
#telnet 1.1.12.1  		//허용 안됨
#ssh -l admin 1.1.12.1 	//허용 안됨

R1# ping 1.1.23.3  		//허용 안됨 
#ping 1.1.34.4  		//허용 안됨
```

허용했던 부분만 적용되고 나머지는 허용이 안된다
{: .notice}

예시 2-2
{: .notice--warning}

```
R1          R2             R3             R4
Fa0/0   fa0/0  fa0/1   fa0/0   fa0/1   fa0/0
1.1.12.0           1.1.23.0         1.1.34.0
```

현재 방화벽은 R2의 fa 0/1 에서 동작하고 있다.  
그렇다면 fa 0/1 로 들어오는 패킷에 대해서만 방화벽이 적용되어야 하는데,  
R1에서 R4로 핑을 보내면 왜 막힐까?   
{: .notice}

사실은, Outbound 설정안해서 일단 R1에서 나가는 것은 통과한다.  
그런데 R4에서 ICMP Request를 받았으니 ICMP Reply가 돌아오는데  
R2의 fa 0/1 에서 적용한 ACL 외 암묵적으로 모든 패킷을 차단한다는 정책에 따라서 reply가 막히고  
R4에게 unreachable 이 간다.
{: .notice--danger}

그래서,  
R2# ip access-list outside-acl-in   
#permit icmp any 1.1.12.0 0.0.0.255 echo-reply 를 따로 입력해줘야한다  
{: .notice--danger}

---
### Access List 문제
---

문제 1  
문제를 내보겠다. R4에서만 R1으로 telnet과 ssh 접속이 가능하도록 설정해봐라 나머지는 거부 되도록 해야한다.
{: .notice--warning}

```
//예시 참고
Permit [protocol] Src_IP  [src_port] Dst_IP [Dst_port]
		Tcp			host x.x.x.x			eq Num
		Udp			any						gt Num
		Icmp		Net_ID Wildbit			lt Num

#ip access-list ftp-in  //ftp 
#permit tcp host 1.1.12.0 host 1.1.23.0 eq 21
#permit tcp host 1.1.12.0 host 1.1.23.0 eq 20
```

R2 에 설정한 acess-list을 일단 지우고 시작하자 
{: .notice}

```
R2#show ip access-list
Extended IP access list outside-acl-in
    10 permit ospf host 1.1.23.3 any (280 matches)
    20 permit icmp any 1.1.12.0 0.0.0.255 echo
    30 permit tcp host 1.1.34.4 gt 1024 host 1.1.12.1 eq telnet
    40 permit tcp host 1.1.34.5 gt 1024 host 1.1.12.1 eq 22
R2#conf t
R2(config)#no ip access-list extended outside-acl-in
```

자 이제 문제로 다시 돌아와서    
Protocol 번호는 6 (tcp)  
Src IP 는 1.1.34.4  
Dst IP는 1.1.12.1  
Src Po:1024  
Dst po: 23  
{: .notice}

우선 Telnet  
{: .notice--danger}
```
R2(config-ext-nacl)#permit tcp host 1.1.34.4 host 1.1.12.1 eq 23
```

다음은 SSH
{: .notice--danger}
```  
R2(config-ext-nacl)#permit tcp host 1.1.34.5 host 1.1.12.1 eq 22
```

문제 2 
{: .notice--warning}

R4에 다음과 같은 설정을 해주고 시작하자
{: .notice}

```
R4#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R4(config)#line vty 0 4
R4(config-line)#password cisco
R4(config-line)#login
R4(config-line)#transport in telnet
R4(config-line)#end
```

R1에서 R4로 Telnet만 접속가능하도록 추가해봐라  
R1 > R2 > R4  
Src: 1.1.12.1   
Dst: 1.1.34.4  
Src_port: 1024 (gt)  
Dst : 23  
{: .notice--warning}
```
Permit tcp host 1.1.12.1 gt 1024 host 1.1.34.4 eq 23
```

R2 < R4에서 막힌다  
Src 1.1.34.4  
Dst 1.1.12.1  
Src 23  
Dst 1024   
{: .notice--warning}
```
Permit tcp host 1.1.34.4 eq 23 host 1.1.12.1 gt 1024 해줘야한다
```

**ACL 설정에서 생각해야하는건 나가는 Packet이 아니라 들어오는 패킷이다**    
**예로, Telnet을 허용하려면 접속을 시도하는 곳에서 접속을 기다리는 곳으로 들어오는 패킷을 허용하면 된다**  
{: .notice}


문제 3  
내부망(1.1.12.0/24)에서 외부망 어디든지(any, 0.0.0.0/0)으로   
Ping (ICMP-Echo Request)이 가능하도록 설정하시오. 
{: .notice--warning}

```
# permit icmp any 1.1.12.0 0.0.0.255 echo-reply
```

문제 4  
조각이난 icmp는 못받게 하고 그러고 나서 나머지 echo request는 받아들인다  
{: .notice--warning}

```
R2(config-ext-nacl)#do show ip access-list
Extended IP access list acl_outside_in
    10 permit ospf host 1.1.23.3 host 224.0.0.5 (211 matches)
	35 permit icmp any 1.1.12.0 0.0.0.255 echo-reply
    30 deny ip any 1.1.12.0 0.0.0.255 fragments (195 matches)
    40 permit icmp any 1.1.12.0 0.0.0.255 echo (20 matches)
```
위와 같이 설정했더니 조각난 fragments는 인식을 하지 못했다. 왜냐하면 fragment된 ping을 살펴보면
마지막 fragment에  정보가 포함된 헤더가 전송되는데 앞서 도착한 fragments들이 일반 echo-reply로 인식되어
Block 되었기 때문이다.
{: .notice--danger}


```
R2(config-ext-nacl)#do show ip access-list
Extended IP access list acl_outside_in
    10 permit ospf host 1.1.23.3 any (302 matches)
    15 deny ip any 1.1.12.0 0.0.0.255 fragments (6 matches)
    20 permit icmp any 1.1.12.0 0.0.0.255 echo-reply (2 matches)
    40 permit icmp any 1.1.12.0 0.0.0.255 echo (3 matches)
```
따라서 위와 같이 순서를 바꿔줘서 fragments들이 도착한후 fragment 인지 확인하는 rule 부터 거치도록 해야한다.
잘 필터링 되었다면 Time exeeded 에러가 뜰 것이다. 
{: .notice--danger}


문제 5-1  
일단 다음과 같은 이름의 access-list를 만들고나서   
R2(config-if)#ip access-list extended acl_outside_in2  
{: .notice--warning} 

1.	R3(1.1.23.3)    ospf  any(0.0.0.0/0) 허용  
2.	Any(0.0.0.0/0)  icmp,type 0  1.1.12.0 허용   
(내부망에서 ping을 요청한 후 다시 들어올 수 있도록 설정    
3.	any(0.0.0.0)  http  1.1.12.3 허용   
4.	R4(1.1.34.4)  ssh  1.1.12.3 허용   
5.	R4(1.1.34.4) telnet 1.1.12.3 허용   
{: .notice--warning}

```
permit ospf host 1.1.23.3 any
permit icmp any 1.1.12.0 0.0.0.255 echo-reply
permit tcp any host 1.1.12.3 eq 80
```
```
permit host 1.1.34.4 host 1.1.12.3 eq 22
permit host 1.1.34.4 host 1.1.12.3 eq 23
```

문제6  
Any ftp 1.1.12.3 허용   
Control과 data 둘다 허용할 것    
(active, passive)   
{: .notice--warning}

```
R2(config-ext-nacl)#permit tcp any host 1.1.12.3 eq 21
R2(config-ext-nacl)#permit tcp any host 1.1.12.3 eq 20
R2(config-ext-nacl)#permit tcp any host 1.1.12.3 range 50000 51000
```

* ACTIVE
	* 클라이언트가 gt 1024 로부터 서버의 21포트로 연결 요청을 한다 (서버로 들어오는 패킷)
	* 서버의 21에서 클라이언트의 포트로 응답을 보낸다 
	* 서버의 20번 포트에서 클라이언트의 gt 1024포트로 데이터를 전송  
	* 클라이언트는 데이터에 대한 응답을 서버의 20번 포트에 보낸다 (서버로 들어오는 패킷)
* PASSIVE
	* 클라이언트에서 서버의 21번 포트로 연결요청 (들어오는 패킷)
	* 서버의 21번 포트에서 클라이언트로 응답 
	* 클라이언트의 gt 1024포트에서 서버의 gt 1024포트로 데이터 전송 (들어오는 패킷)
	* 서버에서 클라이언트로 응답 
* ACTIVE와 PASSIVE MODE에서 들어오는 포트를 보면 20, 21, gt 1024가 쓰이니 이 세 포트를 permit 해준다
{: .notice--info}






