---
title: "Security: ASA 방화벽의 글로벌 액세스 리스트와 오브젝트 그룹"
last_modified_at: 2020-08-24T20:20:02-05:00
categories:
  - Security
tags:
  - Firewall
  - ASA
  - ACL
  - Object Group
  - Global Access List
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/asa_basic4.jpg" alt="">
  <figcaption> </figcaption>
</figure>

---
### 글로벌 액세스 리스트
---

ACL은 특정 인터페이스에서 트래픽 흐름을 제어하는 **인터페이스 ACL**과 방화벽의 모든 인터페이스에 공통적인 보안 정책을 적용하는
 **글로벌 ACL**이 있다. 글로벌 ACL은 in 또는 out 옵션 대신 **global 옵션**을 사용한다. **모든 인터페이스에 일일이 정책을 적용해야하는 경우 편하게 사용할 수 있다.**
{: .notice}

인터페이스 ACL과 글로벌 ACL 설정을 비교해보자.
{: .notice--warning}

---
#### 인터페이스 ACL
---

```console
//기존의 Access-list를 지워준다
FW1(config)# no access-group OUTSIDE-IN in interface outside
FW1(config)# clear config access-list

FW1(config)# show access-list
access-list cached ACL log flows: total 0, denied 0 (deny-flow-max 4096)
            alert-interval 300
```
```console
//access-list 규칙 생성
FW1(config)# access-list OUTSIDE-IN deny icmp host 1.1.2.2 any
FW1(config)# access-list OUTSIDE-IN permit icmp host 1.1.20.2 any
FW1(config)# access-list OUTSIDE-IN permit tcp host 1.1.20.2 any eq telnet
FW1(config)# access-list OUTSIDE-IN deny ip any any
```
```console
//생성된 규칙 보기
FW1(config)# show run access-list
access-list OUTSIDE-IN extended deny icmp host 1.1.2.2 any
access-list OUTSIDE-IN extended permit icmp host 1.1.20.2 any
access-list OUTSIDE-IN extended permit tcp host 1.1.20.2 any eq telnet
access-list OUTSIDE-IN extended deny ip any any
```

아래와 같이 인터페이스 적용시 "in" 옵션을 사용하여 적용한다.
{: .notice--warning}

```console
//규칙을 인터페이스에 적용
FW1(config)# access-group OUTSIDE-IN in int outside
```

1.1.20.2에서 출발하는 icmp에 대해서 permit 하는 정책을 추가했기 때문에 source 1.1.20.2를 입력해야만 ping이 성공한다.
{: .notice--warning}

```console
//R2에서 Ping 및 telnet 테스트
R2#ping 10.1.10.1 source 1.1.2.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.10.1, timeout is 2 seconds:
Packet sent with a source address of 1.1.2.2
.....
Success rate is 0 percent (0/5)
R2#ping 10.1.10.1 source 1.1.20.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.10.1, timeout is 2 seconds:
Packet sent with a source address of 1.1.20.2
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/32/44 ms
R2#telnet 10.1.10.1
Trying 10.1.10.1 ... Open

User Access Verification

Password:
Password:
R1>exit
```

---
#### 글로벌 ACL
---

R2에 http 연결과 telnet 연결을 위해 server을 활성화 해주고 패스워드 설정을 해준다.
{: .notice}

```console
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#ip http server
R2(config)#line vty 0 4
R2(config-line)#password cisco
R2(config-line)#login
R2(config-line)#end
```

R1에서 R1로 telnet 연결 확인
{: .notice}

```
R1#telnet 1.1.20.2
Trying 1.1.20.2 ... Open

User Access Verification

Password:
R2>
```

Global ACL 설정을 해준다.
{: .notice--warning}

```console
FW1(config)# access-list GLOBAL-ACL deny tcp any any eq telnet
FW1(config)# access-group GLOBAL-ACL global
```

```console
R1#telnet 1.1.20.2
Trying 1.1.20.2 ...
% Connection refused by remote host
```

R1 에서 R2로 telnet 접속이 되다가 GLOBAL-ACL를 적용해주고 난 뒤에는 안되는 것을 볼 수 있다.
{: .notice--warning}

```console
FW1(config)# show access-list
access-list cached ACL log flows: total 0, denied 0 (deny-flow-max 4096)
            alert-interval 300
access-list OUTSIDE-IN; 4 elements; name hash: 0x9ccc1a31
access-list OUTSIDE-IN line 1 extended deny icmp host 1.1.2.2 any (hitcnt=5) 0x097a1220
access-list OUTSIDE-IN line 2 extended permit icmp host 1.1.20.2 any (hitcnt=6) 0xd994f4d7
access-list OUTSIDE-IN line 3 extended permit tcp host 1.1.20.2 any eq telnet (hitcnt=1) 0x7dbee2c4
access-list OUTSIDE-IN line 4 extended deny ip any any (hitcnt=0) 0x502c4bfb
access-list GLOBAL-ACL; 1 elements; name hash: 0xf07a8f76
access-list GLOBAL-ACL line 1 extended deny tcp any any eq telnet (hitcnt=1) 0xbd7e27ee
FW1(config)#
```

access-list를 보면 GLOBAL-ACL이 잘 적용된 것을 볼 수 있다. hit count 수를 초기화하고 실험을 해보자
{: .notice--warning}

```console
FW1(config)# clear access-list OUTSIDE-IN counters
FW1(config)# clear access-list GLOBAL-ACL counters
FW1(config)# show access-list
access-list cached ACL log flows: total 0, denied 0 (deny-flow-max 4096)
            alert-interval 300
access-list OUTSIDE-IN; 4 elements; name hash: 0x9ccc1a31
access-list OUTSIDE-IN line 1 extended deny icmp host 1.1.2.2 any (hitcnt=0) 0x097a1220
access-list OUTSIDE-IN line 2 extended permit icmp host 1.1.20.2 any (hitcnt=0) 0xd994f4d7
access-list OUTSIDE-IN line 3 extended permit tcp host 1.1.20.2 any eq telnet (hitcnt=0) 0x7dbee2c4
access-list OUTSIDE-IN line 4 extended deny ip any any (hitcnt=0) 0x502c4bfb
access-list GLOBAL-ACL; 1 elements; name hash: 0xf07a8f76
access-list GLOBAL-ACL line 1 extended deny tcp any any eq telnet (hitcnt=0) 0xbd7e27ee
```

그리고 나서 R1 에서 R2(1.1.20.2)로 Telnet 접속 시도 후 어느 정책의 hit count가 증가하는지 지켜 볼 것이다.
{: .notice--warning}
```console
FW1(config)# show access-list
access-list cached ACL log flows: total 0, denied 0 (deny-flow-max 4096)
            alert-interval 300
access-list OUTSIDE-IN; 4 elements; name hash: 0x9ccc1a31
access-list OUTSIDE-IN line 1 extended deny icmp host 1.1.2.2 any (hitcnt=0) 0x097a1220
access-list OUTSIDE-IN line 2 extended permit icmp host 1.1.20.2 any (hitcnt=0) 0xd994f4d7
access-list OUTSIDE-IN line 3 extended permit tcp host 1.1.20.2 any eq telnet (hitcnt=0) 0x7dbee2c4
access-list OUTSIDE-IN line 4 extended deny ip any any (hitcnt=0) 0x502c4bfb
access-list GLOBAL-ACL; 1 elements; name hash: 0xf07a8f76
access-list GLOBAL-ACL line 1 extended deny tcp any any eq telnet (hitcnt=1) 0xbd7e27ee
```

글로벌 설정에는 모든 telnet을 거부하는 룰이 있고 OUTSIDE-IN에서는 1.1.20.2 에 대해서는 telnet을 허용하는 룰이 있다.  
개별적으로 인터페이스에 적용되는 룰이 먼저 적용되고 GLOBAL 은 추가적인 룰이다. 그래서 GLOBAL은 무시된다.
{: .notice--warning}

이번엔 R2에서 Telent 10.1.10.1 했을 때의 결과를 보자. 
{: .notice--warning}

```console
FW1(config)# show access-list
access-list cached ACL log flows: total 0, denied 0 (deny-flow-max 4096)
            alert-interval 300
access-list OUTSIDE-IN; 4 elements; name hash: 0x9ccc1a31
access-list OUTSIDE-IN line 1 extended deny icmp host 1.1.2.2 any (hitcnt=0) 0x097a1220
access-list OUTSIDE-IN line 2 extended permit icmp host 1.1.20.2 any (hitcnt=0) 0xd994f4d7
access-list OUTSIDE-IN line 3 extended permit tcp host 1.1.20.2 any eq telnet (hitcnt=1) 0x7dbee2c4
access-list OUTSIDE-IN line 4 extended deny ip any any (hitcnt=0) 0x502c4bfb
access-list GLOBAL-ACL; 1 elements; name hash: 0xf07a8f76
access-list GLOBAL-ACL line 1 extended deny tcp any any eq telnet (hitcnt=1) 0xbd7e27ee
```

(Access-list OUTSIDE-IN deny ip any any) 규칙이 기본인데. OUTSIDE-IN에 명시적으로 설정이 안되어있으면, GLOBAL-ACL 맨뒤에 가서 적용이 된다. 
원래는 OUTSIDE 맨 뒤에서 동작하다가 GLOBAL 이 생성되는 경우 Deny에 any any에 의해 막힐 수 있으니 자동으로 GLOBAL 뒤로 가는 것이다.  
{: .notice--warning}

방화벽 설정을 다 지워주자.
{: .notice}

```
FW1(config)# clear config access-list
FW1(config)# show run access-list
FW1(config)# show run access-group
```

---
### 오브젝트 그룹
---

**오브젝트 그룹은** 특정한 IP 주소나 네트워크, 프로토콜을 그룹으로 정의하고 방화벽 정책 설정시 불러서 사용할 수 있다.
오브젝트 그룹을 사용하면 복잡한 설정을 간단하게 정의 할 수 있다는 장점이 있다.
{: .notice}

```console
//오브젝트 그룹의 종류는 6가지로 나눌 수 있다.
FW1(config)# object-group ?

configure mode commands/options:
icmp-type	Specifies a group of ICMP types, such as echo
network		Specifies a group of host or subnet IP address
protocol	Specifies a group of protocols, such as TCP, etc
service		Specifies a group of TCP/UDP ports/services
security	Specifies idenrity attributes such as security-group
user		Specifies single user, local or import user group
```

예시를 들어보자, 아래와 같은 두줄을 한 줄로 만들면 여러 인터페이스에 적용하기 훨씬 쉬워진다.
{: .notice--warning}

```console
Access-list OUTSIDE-IN permit tcp any host 10.10.10.10 eq 80
Access-list OUTSIDE-IN permit tcp any host 10.10.10.10 eq 443
```
```console
Access-list OUTSIDE-IN permit tcp any object Web-Server Proto1
```
---
#### icmp 오브젝트 그룹
---

object-group 중에서 icmp-type에 대한 그룹을 한번 살펴보자.
{: .notice--warning}
```console
FW1(config)# object-group icmp-type ?

configure mode commands/options:
  WORD < 129 char  Specifies object-group ID
```
```console
FW1(config)# object-group icmp-type Ping_Group
FW1(config-icmp-object-group)# ?

  description   Specify description text
  group-object  Configure an object group as an object
  help          Help for ICMP object-group configuration commands
  icmp-object   Configure an ICMP-type object
  no            Remove an object or description from object-group
```
object-group icmp-type <그룹이름> 을 입력하여 icmp 그룹 설정 모드로 들어간다.
{: .notice--warning}

```console
FW1(config-icmp-object-group)# icmp-object ?

icmp-object-group mode commands/options:
  <0-255>               Enter ICMP type number (0 - 255)
  alternate-address
  conversion-error
  echo
  echo-reply
…
```

그룹화할 icmp의 type 번호를 아래와 같이 적어서 추가해준다.
{: .notice--warning}

```console
FW1(config-icmp-object-group)# icmp-object 0
FW1(config-icmp-object-group)# icmp-object 8
FW1(config-icmp-object-group)# exit

//묶은 결과 보기
FW1(config)# show object-group
object-group icmp-type Ping_Group
 icmp-object echo-reply
 icmp-object echo
```
---
#### network 오브젝트 그룹
---

network 주소와 대역대도 오브젝트로 그룹화 하여 관리할 수 있다.
{: .notice--warning}

```console
FW1(config)# object-group network Internal_Users
FW1(config-network-object-group)# ?

  description     Specify description text
  group-object    Configure an object group as an object
  help            Help for network object-group configuration commands
  network-object  Configure a network object
  no              Remove an object or description from object-group
```

```console
FW1(config-network-object-group)# network-object ?

network-object-group mode commands/options:
  Hostname or A.B.C.D                     Enter an IPv4 network address
  Hostname/<0-128> or X:X:X:X::X/<0-128>  Enter an IPv6 prefix
  host                                    Enter this keyword to specify a
                                          single host object
  object                                  Enter this keyword to specify a
                                          network object
```

먼저 object-group network <그룹이름> 을 입력하여 설정 모드로 들어온 후 network-object를 추가해보자.
{: .notice--warning}

```console
FW1(config-network-object-group)# network-object 10.1.10.0 255.255.255.0
FW1(config-network-object-group)# network-object 10.10.10.0 255.255.255.0
FW1(config-network-object-group)# exit
```

show object-group 명령어를 통해서 보면 생성했던 object-group들이 나타난다. 
{: .notice--warning}

```console
FW1(config)# show object-group
object-group icmp-type Ping_Group
 icmp-object echo-reply
 icmp-object echo
object-group network Internal_Users
 network-object 10.1.10.0 255.255.255.0
 network-object 10.10.10.0 255.255.255.0
```

---
#### Protocol 오브젝트 그룹
---

```console
FW1(config)# object-group protocol Routing_Group
FW1(config-protocol-object-group)# protocol-object ?

protocol-object-group mode commands/options:
  <0-255>  Enter protocol number (0 - 255)
  ah
  eigrp
  esp
  gre
  icmp
  icmp6
  igmp
  igrp
  ip
  ...
  
FW1(config-protocol-object-group)# protocol-object tcp
FW1(config-protocol-object-group)# protocol-object udp 
```

**protocol-object <프로토콜 이름 혹은 프로토콜 ID>** 로 정책을 추가할 수 있다.
{: .notice--warning}

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/asa_basic5.jpg" alt="">
  <figcaption> </figcaption>
</figure>

여기서 말하는 프로토콜 ID란 IP 헤더에 있는 Type of Service를 말한다.
{: .notice--warning}

```console
FW1(config)# show object-group
object-group icmp-type Ping_Group
 icmp-object echo-reply
 icmp-object echo
object-group network Internal_Users
 network-object 10.1.10.0 255.255.255.0
 network-object 10.10.10.0 255.255.255.0
object-group protocol Routing_Group
 protocol-object tcp
 protocol-object udp
```

---
#### Service 오브젝트 그룹
---

```console
FW1(config)# object-group service My_Service
FW1(config-service-object-group)# service-object tcp ?

dual-service-object-group mode commands/options:
  destination  Keyword to specify destination
  source       Keyword to specify source

FW1(config-service-object-group)# service-object tcp dest eq 80
FW1(config-service-object-group)# service-object tcp dest eq 443
FW1(config-service-object-group)# exit
```

service-object <프로토콜> <출발지/목적지> <포트번호> 형식으로 추가할 수 있다.
{: .notice--warning}

```console
FW1(config)# show object-group
object-group icmp-type Ping_Group
 icmp-object echo-reply
 icmp-object echo
object-group network Internal_Users
 network-object 10.1.10.0 255.255.255.0
 network-object 10.10.10.0 255.255.255.0
object-group protocol Routing_Group
 protocol-object tcp
 protocol-object udp
object-group service My_Service
 service-object tcp destination eq www
 service-object tcp destination eq https
```

---
### 오브젝트 그룹 실습1
---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/asa_basic4.jpg" alt="">
  <figcaption> </figcaption>
</figure>

R1에 10.1.10.2~5 주소를 추가해주고 이를 통해 실습을 할 것이다.
{: .notice}

```console
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int fa 0/1
R1(config-if)#ip add 10.1.10.2 255.255.255.0 secondary
R1(config-if)#ip add 10.1.10.3 255.255.255.0 secondary
R1(config-if)#ip add 10.1.10.4 255.255.255.0 secondary
R1(config-if)#ip add 10.1.10.5 255.255.255.0 secondary
```

잘 추가 되었는지 ping을 통해서 test 한다.
{: .notice}

```console
FW1(config)# ping 10.1.10.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.10.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/28/40 ms
FW1(config)# ping 10.1.10.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.10.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/20/40 ms
FW1(config)# ping 10.1.10.4
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.10.4, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 10/24/50 ms
FW1(config)# ping 10.1.10.5
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.10.5, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 10/24/30 ms
FW1(config)#
```

* 다음과 같이 설정 할 것이다.
* Web-Server 10.1.10.1
* http, https 10.1.10.2
* 6 10.1.10.3
* telnet-Server 10.1.10.4
* telnet 10.1.10.5
* 2 10.1.10.5
{: .notice--info}

network object를 통해 10.1.10.1~3를 그룹으로 묶는다.
{: .notice}

```console
FW1(config)# object-group network Web-Servers
FW1(config-network-object-group)# network-object host 10.1.10.1
FW1(config-network-object-group)# network-object host 10.1.10.2
FW1(config-network-object-group)# network-object host 10.1.10.3
FW1(config-network-object-group)# exit
```

service object로 80과 443 포트번호를 그룹으로 묶는다.
{: .notice}

```console
FW1(config)# object-group service Web-Protocols
FW1(config-service-object-group)# service-object tcp dest eq 80
FW1(config-service-object-group)# service-object tcp dest eq 443
FW1(config-service-object-group)# exit
```

생성한 object를 사용해서 확장 acl을 설정한다. object-group <오브젝트 이름> 을 이용하여 불러온다는 것을 기억하자.
포트번호 80, 443에 접속하는 서비스이고 10.1.10.1~3 호스트에 접속하는 건에 대해서 허용해준다.
{: .notice}

```console
access-list acl_global extended permit object-group Web-Protocols any object-group Web-Servers
```

Telnet을 통해 접속할 서버주소 10.1.10.4~5를 network object로 묶는다.
{: .notice}
```console
FW1(config)# object-group network Telnet-Servers
FW1(config-network-object-group)# network-object host 10.1.10.4
FW1(config-network-object-group)# network-object host 10.1.10.5
FW1(config-network-object-group)# exit
```

10.1.10.4~5에 23포트로 접속하는 건에 대해서 허용하고, global 옵션을 이용하여 global ACL에 적용해준다.
{: .notice}

```console
access-list acl_global permit tcp any object-group Telnet-Servers eq 23
access-group acl_global global
```

결과로 10.1.10.1~3 까지는 telnet을 통해 80 에 접속시 잘된다. 10.1.10.4~5는 그냥 telnet 접속시 잘된다.
{: .notice}

```console
R2#telnet 10.1.10.1 80
Trying 10.1.10.1, 80 ... Open
exit()
HTTP/1.1 400 Bad Request
Date: Fri, 01 Mar 2002 04:22:30 GMT
Server: cisco-IOS
Accept-Ranges: none

400 Bad Request

R2#telnet 10.1.10.2 80
Trying 10.1.10.2, 80 ... Open
eixt
HTTP/1.1 400 Bad Request
Date: Fri, 01 Mar 2002 04:22:52 GMT
Server: cisco-IOS
Accept-Ranges: none

400 Bad Request
```

```console
R2#telnet 10.1.10.4
Trying 10.1.10.4 ... Open


User Access Verification

Password:
R1>exit

R2#telnet 10.1.10.5
Trying 10.1.10.5 ... Open


User Access Verification

Password:
R1>exit
```















