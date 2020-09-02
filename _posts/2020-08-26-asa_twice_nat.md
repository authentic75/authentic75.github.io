---
title: "Security: 트와이스 NAT"
last_modified_at: 2020-08-26T20:20:02-05:00
categories:
  - Security
tags:
  - Firewall
  - ASA
  - NAT
  - Twice NAT
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

---
### 트와이스 NAT
---

* **트와이스 NAT(메뉴얼 NAT)**
* 출발지 주소와 목적지 주소를 참고하여 주소 변환을 한다.
* 예로 들어, 특정 주소를 가진 패킷이 특정 목적지를 향할 때 원하는 IP로 변환이 가능하다.
* 메뉴얼 NAT라고 불리는 이유는 NAT 테이블의 정책 순서를 관리자가 직접 설정해야하기 때문이다.
* 오브젝트 방식보다 세부적인 NAT 정책을 설정할 수 있다.
* 단점은 정책 순서가 맞지 않으면 의도한대로 동작하지 않을 수 있다.(오브젝트 방식이 더 편하다)
{: .notice--info}

* **트와이스 NAT 설정 방법**
* real source addr를 지정한다.
* mapped source addr를 지정한다.
* real destination addr를 지정한다.
* mapped destination addr를 지정한다.
* real port를 지정한다.
* mapped port를 지정한다.
* 전체 설정모드에서 주소 변환을 설정한다.
{: .notice--warning}

`동적 NAT는 목적지 주소 변환이 불가하다.`

|  항목  |  정적 NAT  |  정적 PAT  |  동적 NAT  |  동적 PAT  |   
| ------ | ------ | ------- | ------- | ------- |   
| 출발지 주소 변환 | O | O | O | O |  
| 목적지 주소 변환 | O | O | X | X |  


---
### 정적 트와이스 NAT 실습
---

오브젝트 NAT 실습을 하면서 만들어 뒀던 토폴로지를 활용하겠다.
{: .notice}

<figure class="half">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/Topology/asa_nat2.jpg" alt="">
  <figcaption> </figcaption>
</figure>

inside의 특정 사용자 10.1.1.1이 외부 네트워크로 통신하도록 설정해보자  
inside(실제 주소)  
출발지 주소: 10.1.1.1  
목적지 주소: outside 모든 주소   
FW1(mapping 될 주소)     
출발지 주소: 1.1.40.1  
목적지 주소: outside 모든 주소   
{: .notice}

```console 
FW1# clear xlate
FW1# conf t
FW1(config)# clear configure object
FW1(config)# clear configure access-list
```

우선 위와같이 기존의 설정들을 제거하고 네트워크 오브젝트를 생성하자.
{: .notice}

```console
FW1(config)# object network inside_real
FW1(config-network-object)# hsot 10.1.1.1
FW1(config-network-object)# exit

FW1(config)# object network inside_mapped
FW1(config-network-object)# host 1.1.40.1
```

* 여러 주소를 가지고 오브젝트를 만들고자 한다면 subnet 혹은 range 명령어를 사용할 수 있다.
* show run object 명령어로 object 설정 사항들을 볼 수 있다.
* show nat detail 명령어로 nat 설정 사항들을 상세히 볼 수 있다.
{: .notice}

DMZ 인터페이스의 보안 레벨은 50, Outside 인터페이스의 보안 레벨은 0이므로 액세스 리스트를 설정해줘야한다.
텔넷을 허용해보자.
{: .notice}

```console
FW1(config)# access-list Outside-inbound permit tcp host 1.1.4.4 host 10.1.2.2 eq telnet
FW1(config)# access-list Outside-inbound permit tcp host 1.1.4.4 host 10.1.3.3 eq telent

FW1(config)# access-group Outside-inbound in int outsie
```

출발지 주소 변환을 위한 오브젝트를 생성해보자.
{: .notice}

```console
FW1(config)# object network Outside_real
FW1(config-network-object)# host 1.1.4.4
FW1(config-network-object)# exit

FW1(config)# object network Outside_mapped
FW1(config-network-object)# host 10.1.23.100
FW1(config-network-object)# exit
```

목적지 주소 변환을 위한 오브젝트를 생성하자.
{: .notice}


```console
FW1(config)# object network DMZ_real_2
FW1(config-network-object)# host 10.1.2.2
FW1(config-network-object)# exit

FW1(config)# object network DMZ_mapped_2
FW1(config-network-object)# host 1.1.40.2
FW1(config-network-object)# exit

FW1(config)# object network DMZ_real_3
FW1(config-network-object)# host 10.1.3.3
FW1(config-network-object)# exit

FW1(config)# object network DMZ_mapped_3
FW1(config-network-object)# host 1.1.40.3
FW1(config-network-object)# exit
```

정적 트와이스 NAT를 설정해보자.  
src_Real -> src_Mapped dst_Mapped -> dst_Real
4개의 object가 위와 같은 순서로 명령어에 들어간다.  
순서에 주의해야한다.  
{: .notice}

```console
FW1(config)# nat (outside,dmz) source static Outside_real Outside_mapped destination static DMZ_mapped_2 DMZ_real_2
FW1(config)# nat (outside,dmz) source static Outside_real Outside_mapped destination static DMZ_mapped_3 DMZ_real_3
```

* 출발지 주소 1.1.4.4는 10.1.23.100으로 변경되고 목적지 주소 1.1.40.2는 실제 주소인 10.1.2.2고 변환된다.
* 출발지가 1.1.4.4, 목적지가 1.1.40.3인 경우 출발지 주소를 10.1.23.100으로 변환하고 목적지 주소를 실제 주소인 10.1.4.4로 변환한다.
{: .notice}

```console
R4# telnet 1.1.40.2 /source-interface lo 0
R4# telnet 1.1.40.3 /source-interface lo 0
```

telnet 1.1.40.2 /source-interface lo 0를 실행하고 R2에서 show users를 싱행하면 10.1.23.100 주소로 연결된 것을 볼 수 있다.
telnet 1.1.40.3 /source-interface lo 0를 실행한 후에 R3에서 show users를 실행하면 10.1.23.100 주소로 연결된 것을 볼 수 있다.
{: .notice}

---
### 정적 트와이스 NAT 실습2
---


```console
10.1.2.2(dmz_real2) <---> 1.1.40.2(dmz_map2)
10.1.3.3(dmz_real3) <---> 1.1.40.3(dmz_map3)
```

위와 같이 설정이후 R2 R3 에서 R4로 ping telent이 가능한지 테스트 해보자
{: .notice}

```console
FW1
object network dmz_real2
host 10.1.2.2
exit

object network dmz_real3
host 10.1.3.3
exit

object network dmz_map2
host 1.1.40.2
exit

object network dmz_map3
host 1.1.40.3
exit
```
```console
FW1(config)# nat (dmz, outside) source static dmz_real2 dmz_map2
FW1(config)# nat (dmz, outside) source static dmz_real3 dmz_map3
```

R2에서 1.1.4.4 로 ping 가능 R3에서 1.1.40.4에 ping 가능한지 확인하고 telnet 설정을 해주자
{: .notice}

```console
#R2 #R3
conf t
line vty 0 4
password cisco
login
end
```

#R4에서는 아직 ACL이 안열려 있어서 telnet이 안된다.  
1.1.40.2 나 1.1.40.3으로 되도록 ACL을 추가해줘라  
{: .notice}

```console
access-list OUTSIDE-IN permit tcp any object dmz_real2 eq 23
access-list OUTSIDE-IN permit tcp any object dmz_real3 eq 23
access-group OUTSIDE-IN in int outside
```

clear config access-list을 실행하여 ACL을 지우고 아래와 같이 설정해보자.
{: .notice}

```console
object-group network Telnet_Servers
network-object object dmz_real2
network-object object dmz_real3
exit // Group으로 묶어준다

show run object-group
access-list OUTSIDE-IN permit tcp any object-group Telnet_Servers eq 23
access-group OUTSIDE-IN in int outside
show run access-list
```
















