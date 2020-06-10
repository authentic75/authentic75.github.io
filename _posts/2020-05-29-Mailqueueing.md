---
title: "Mail queueing"
last_modified_at: 2020-05-29T20:20:02-05:00
categories:
  - LINUX
tags:
  - MailServer
  - MailQueue
  - LINUX
toc: false
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
---
### Mail queueing
---
* 여러명이서 mail queueing을 구현해 볼 것이다
* 한명이 NS 역할을 해야한다
* 메일 서버 3대 mx1 mx2 mx3를 만들고 차등 순위를 부여할 것이다 10 20 30 
{: .notice}

* 큐잉 메일 서버 예상 동작
* mx1에 에러가 생겼을 시, mx2 와 mx3 가 메일을 받고(임시보관) mx1이 복구가 되면 다시 보냄
* 최종적으로는 mx1 에만 메일이 남는다
* 계정도 mx1 에만 있으면 된다
{: .notice--info}

.com의 역할을 하는 사람이 만들어야할 zone 파일의 예다
{: .notice}

```
$TTL ID
$ORIGIN com.
@ IN SOA  ns  root (
	2020052900
	3H
	15M
	1W
	1D)
IN NS ns
Ns IN A 192.168.0.100

Kt.com. IN NS ns.kt.com
Ns.kt.com. IN A 192.168.0.101

Kitri.com. IN NS ns. kitri.com
Ns.kitri.com. IN A 192.168.0.110

lg.com. IN NS ns.lg.com
Ns.lg.com. IN A 192.168.0.103

abc.com. IN NS ns.abc.com
Ns.abc.com. IN A 192.168.0.115 
...
```

여기서 나는 kitri.com 의 두번째 메일서버인 mx2.kitri.com 역할을 맡았다
{: .notice}
우선 아래와 같이 hostname, /etc/sysconfig/network, /etc/hosts 에서 호스트네임을 바꿔줍니다
{: .notice}
```
[root@mx2 ~]# hostname mx2.kitri.com
[root@mx2 mail]# vi /etc/sysconfig/network
NETWORKING=yes
NETWORKING_IPV6=no
HOSTNAME=mx2.kitri.com
GATEWAY=192.168.0.1
~
```

```
[root@mx2 ~]# vi /etc/hosts
# Do not remove the following line, or various programs
# that require network functionality will fail.
127.0.0.1       localhost.localdomain localhost
::1             localhost6.localdomain6 localhost6
192.168.0.114   mx2.kitri.com mx2
```
iptables로 25번 포트를 열어줍니다
{: .notice}
```
[root@mx2 ~]# iptables -I INPUT -p tcp --dport 25 -j ACCEPT
[root@mx2 ~]#
[root@mx2 ~]# iptables -nL | grep 25
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:25
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0           icmp type 255
ACCEPT     udp  --  0.0.0.0/0            224.0.0.251         udp dpt:5353
```
/etc/mail 디렉토리로 이동하여 access에서 메일을 허용할 주소를 추가해줍니다
{: .notice}
```
[root@mx2 ~]# cd /etc/mail
[root@mx2 mail]#
[root@mx2 mail]# vi access
# Check the /usr/share/doc/sendmail/README.cf file for a description
# of the format of this file. (search for access_db in that file)
# The /usr/share/doc/sendmail/README.cf is part of the sendmail-doc
# package.
#
# by default we allow relaying from localhost...
Connect:localhost.localdomain           RELAY
Connect:localhost                       RELAY
Connect:127.0.0.1                       RELAY
kitri.com                               RELAY
test.com                                RELAY
kt.com                                  RELAY
lg.com                                  RELAY
abc.com                                 RELAY
```
```
[root@mx2 mail]# makemap hash access.db < access
```
아래 파일들 설정도 다음과 같이 수정해줍니다
{: .notice}
```
[root@mx2 mail]vi /etc/mail/local-host-names
//허용된 도메인을 추가합니다
[root@mx2 mail]vi +260 sendmail.cf
265 O DaemonPortOptions=Port=smtp,Addr=0.0.0.0, Name=MTA
[root@ns1 mail]# vi /etc/sysconfig/sendmail //3m 으로 바꿔줘라
```
```
[root@ns1 mail]# mail ljm@kt.com
Subject: test
123
123
123
Cc:
//다른 사람에게 메일을 한번 보내보자
```
```
[root@ns1 mail]# service sendmail stop
[root@ns1 mail]# mailq
//메일을 정지하고 mx3에게 메일이 가는지 확인해본다
```
mx1 mx2 mx3의 우선순위를 다 10 으로 바꾸면 무작위로 메일을 쌓게 된다
{: .notice}
