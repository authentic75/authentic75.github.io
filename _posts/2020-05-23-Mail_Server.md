---
title: "LINUX: sendmail server 생성"
last_modified_at: 2020-05-25T20:20:02-05:00
categories:
  - LINUX
tags:
  - DNS
  - 메일서버
  - 서버구현
  - LINUX
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

---
### MX, PTR 레코드 설정
---

* 각자 자신의 메일 서버만 만들어보자 
* Send mail server의 구현
* 메일을 전송할 때 MX 레코드 받을 때 는 메일 발송자가 확인을 위해 PTR 레코드를 확인한다
{: .notice}

Zone 파일에서 xxx.co.ki.zone 에 들어가서 설정을 바꾸자
{: .notice}

```
[root@ns2 named]# cat kjy.co.ki.zone
$TTL    1D
$ORIGIN kjy.co.ki.
@       IN SOA          ns2     root (
                        2020052500
                        10M
                        3M
                        1W
                        1D      )
        IN NS           ns2
        IN NS           ns1
        IN MX   10      mail

ns2     IN A    192.168.0.164
ns1     IN A    192.168.0.114

www     IN A    192.168.0.114
ftp     IN A    12.12.12.12
mail    IN A    192.168.0.164
```
```
[root@ns2 named]# rndc reload
```

설정이 잘 되었다면 아래와 같이 설정이 되어야한다.
{: .notice}

```
[root@ns2 named]# dig kjy.co.ki mx +short
10 mail.kjy.co.ki.
[root@ns2 named]# dig mail.kjy.co.ki a +short		//a는 기본 값이라 설정 바꿔도 된다.
192.168.0.164
[root@ns2 named]#
```

조회가 안될 경우 캐쉬를 지워도 된다.
{: .notice}

* 오류가 날 경우 서비스 동작 유무를 가장 먼저 체크해보자
	* Ps aux | grep named  -> 동작중인 경우?  Named.conf 가 나타남
	* Systemctl status named.service 로  확인 가능 -> active 출력  서비스 된다는 뜻.
	* 그럼에도 불구하고 루프백으로 조회해도 안되는가? 
	* Reload 한다 그래도 안되는가? 설정의 문제다
	* 그런데 zone 파일도 문제가 없다 ??
	* vi /etc/named.conf  리슨이 안되어있거나 allow의 문제는 아닌지 확인해보자
{: .notice}


* Computer 이름이 일치하지 않으면 에러가 나기도 하니까 Hostname을 일치 시켜주자
* 재구동 해도 유지되길 원한다면 /etc/hostname 에서 바꾸자 
* mail.kjy.co.ki
{: .notice}

---
### 호스트네임 설정
---


```
[root@ns2 named]# hostname mail.kjy.co.ki
[root@ns2 named]# hostname
mail.kjy.co.ki
```
```
[root@ns2 named]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.0.164   ns2.kjy.co.ki   mail.kjy.co.ki
[root@ns2 named]#
```

Local host names 의 역할은 외부에서 메일이 오게되면 임시 디렉토리(mqueue)에 쌓인다. 그러면 local host names 에서 확인해보고 MX 레코드로 조회한다 그리고 라우팅을 하고 메일함에 저장하는 것이다.
{: .notice}

---
### sendmail 설정
---

* sendmail package 설치
* /etc/mail/local-host-names 에는 @kjy.co.ki 꼴로 메일이 오기때문에 kjy.co.ki 추가 해주자 
{: .notice}

```
[root@ns2 named]# yum install sendmail -y -q
[root@ns2 named]# vi /etc/mail/local-host-names
```

```
[root@ns2 named]# tail -n 6 /etc/sysconfig/network-scripts/ifcfg-enp0s3
IPADDR="192.168.0.164"
PREFIX="24"
GATEWAY="192.168.0.1"
DNS1="192.168.0.150"
DNS2="192.168.0.100"
IPV6_PRIVACY="no"
[root@ns2 named]#
```

변수명을 이용해서 수정 가능 
{: .notice}


Sendmail 도 DNS 와 비슷하게 listen-on port 53 {127.0.0.1;}; 같은 부분이 있다  
메일서버도 비슷하게는 127.0.0.1 끼리의 통신만 허용하고 있어서 listen 127.0.0.1 으로 쓰여 있다  
이부분을 수정해야한다  
{: .notice}

```
[root@ns2 named]# vi /etc/mail/sendmail.cf
```

:Set nu 해서 보자    
85행 Cwlocalhost  
우리가 수신하고자 하는 이메일에 대한 컴퓨터명을 이 파일에 담는다  
원래는 Cwlocalhost 설정이 되어있는데  localhost로부터 오면 자기 자신한테 온 것이라는 설정을 했다.  
Cw도메인명 - > Cwkjy.co.ki 추가   
{: .notice}

a라는 계정이 b라는 계정에게 메일을 보낼 때  
a$ mail b@localhost 와 같은 명령어로 또는  
mail b@kjy.ac.ki로 보내기 때문에 localhost를 지우지 않고 우리 도메인명을 추가한다  
원래대로라면 서비스하고자하는 주소를 계속 나열해야했는데 이게 너무 지저분해서 활용하게 된 것이 Fw/etc/mail/local-host-names 파일이다  
Cw는 도메인 하나만 지정할 때, Fw는 파일째로 설정할 때 사용한다  
{: .notice}

/etc/mail/local-host-names에 한줄에 하나씩 나열   
Naver.com  
Google.com  
…  
{: .notice}


267 O DaemonPortOptions=Port=smtp,Addr=127.0.0.1, Name=MTA //을 보자   
//어떤 IP로 날아오는 메일에 대해서 수신할것인가  
{: .notice}

메일을 받았을 때 남겨야할지 다른곳으로 라우팅 할지 MTA가 결정한다. 이때 어떤 주소로 온 메일을 남길지 적는다.  
즉, 서버의 IP를 지정한다는 뜻이다  
267 O DaemonPortOptions=Port=smtp,Addr=127.0.0.1, Name=MTA  
268 O DaemonPortOptions=Port=smtp,Addr=192.168.0.164, Name=MTA  
우리는 우리의 IP를 추가해서 수신받을 인터페이스를 등록해줄 것이다. 보안상 그게 좋다.  
{: .notice}

우리에게 메일을 발송할 주소 -> 서버 (발송자) DaemonPortOptions 
수신자 ( 클라이언트 ) -> allow  
{: .notice}

---
#### Access DB와 관련한 설정
---

나에게 메일을 보낼 수 있는 대상과 보낼 수 없는 대상을 누적해서 관리한다  
{: .notice}

```console
[root@ns2 mail]# ls
Makefile         domaintable       mailertable     sendmail.mc   trusted-users
access           domaintable.db    mailertable.db  spamassassin  virtusertable
access.db        helpfile          make            submit.cf     virtusertable.db
aliasesdb-stamp  local-host-names  sendmail.cf     submit.mc
```

ls 해서 보면 access와 access.db 를 볼 수 있다. Access.db가 실제 동작하는 파일이다.  
Access.db는 바이너리 파일이다   
{: .notice}

```
#파일 형태를 조회해보자
[root@ns1 mail]# file access.db
access.db: Berkeley DB (Hash, version 8, native byte-order)
```
다음과 같이 access.db 파일을 수정 할 수 있다.
{: .notice}

```
[root@ns2 mail]# makemap hash access.db
192.168.0.0/24          RELAY
co.ki                   RELAY
@naver.com              RELAY
user@                   RELAY
@spam.com               DISCARD
[root@ns2 mail]#
```

* RELAY는 받거나 다른 쪽으로 넘겨준다는 의미 
* 직접 표준입력으로 적어준다
* co.ki를 포함하는 것들에 대한 모든 것을 허용 
* @naver.com은 naver 앞에 아무것도 안붙은 것으로 naver.com 만 허용
* user@ 어떤 도메인이던간에 user 계정명이면 허용해주겠다
* @spam.com discard 한다. 보내는 입장에서는 받았나 안 받았나 알 수 없다
* Reject는 거절한 이유를 설명해준다
{: .notice}

> 리다이렉션 표준 출력 방향 전환  
< 표준 입력의 방향 전환  
{: .notice}

```
[root@ns2 mail]# makemap hash access.db
[root@ns2 mail]# strings access.db
RELAY
co.ki
DISCARD
@spam.com
RELAY
192.168.0.0/24
RELAY
user@
RELAY
@naver.com
```

access.db파일은 쓸때마다 overwite 된다  
cat 대신에 strings 명령어로 읽을 수 있다  
makemap hash access.db < test 와 같은 방법으로 입력도 가능하다  
{: .notice}

```
[root@ns2 mail]#vi access
# By default we allow relaying from localhost...
Connect:localhost.localdomain           RELAY
Connect:localhost                       RELAY
Connect:127.0.0.1                       RELAY
co.ki                                   RELAY
192.168.0.0/255.255.255.0               RELAY
192.168.0.0/24                          RELAY

[root@ns2 mail]# systemctl restart sendmail.service

---
### mail 조회하기
---

```
[root@ns2 mail]# ls -lh /var/spool/mail/
합계 0
-rw-rw----. 1 kjy mail 0  4월 21 15:49 kjy
-rw-rw----. 1 rpc mail 0  4월 21 15:33 rpc
[root@ns2 mail]# ls -lh /var/spool/mqueue/
합계 0
[root@ns2 mail]# rm -f /var/spool/mail/*
```

```
[root@ns2 mail]# mail kjy@localhost
Subject: Hi JY
Ho!Hey!Ho!
EOT
```


```
[root@ns2 mail]# su - kjy
[kjy@mail ~]$ mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/kjy": 1 message 1 new
>N  1 root                  Thu May 21 17:31  22/783   "Hi JY"
&1 // 숫자를 입력하면 아래와 같이 출력됨

Message  1:
From root@ns2.kjy.co.ki  Thu May 21 17:31:59 2020
Return-Path: <root@ns2.kjy.co.ki>
From: root <root@ns2.kjy.co.ki>
Date: Thu, 21 May 2020 17:31:58 +0900
To: kjy@ns2.kjy.co.ki
Subject: Hi JY
User-Agent: Heirloom mailx 12.5 7/5/10
Content-Type: text/plain; charset=us-ascii
Status: R

Ho!Hey!Ho!
EOT
```

그 다음 종료는 q 치고 엔터  
Root로부터 온 메일만있다 보통 이경우에는 반송을 뜻한다  
옆사람에게 메일을 보내보자  
{: .notice}

```
[kjy@mail ~]$ dig kjy.co.ki mx +short
10 mail.kjy.co.ki.
[kjy@mail ~]$ 조회가 되는지 확인
[kjy@mail ~]$ dig mail.kjy.co.ki +short
192.168.0.164
//주소와 ifconfig에 주소와 같은지도 보자
```

```
[root@ns2 mail]# firewall-cmd --add-port=25/tcp
success
[kjy@mail mail]$ mail jyj@jyj.co.ki
Subject: HaHaHa
HoHoHoHo
Hee Hee
Ha Ha Ha
EOT
[kjy@mail mail]$
```

Su kjy  
Su – kjy 차이가 있다 !! 중요!!!  
Su kjy만 하면 PATH가 root인 상태로 계정만 바뀌어서   
kjy에게 온 메일을 볼 수가 없다  
이것 때문에 설정하다가 혼란을 겪었다!  
{: .notice}

---
#### mail Server 구축 순서
---

메일 서버 동작 과정  
네임서버 ns2.kjy.co.ki  
메일서버 mail.kjy.ac.ki  
어디선가 메일을 발송한다고 가정하고 시작  
{: .notice}
1.	메일 발신지에서 DNS 조회를 통해 kjy.ac.ki 영역에 대한 mx 레코드를 조회하고 조회한 도메인의 a(IPv4) 레코드에 등록된 서버로 메일을 발송한다  
#dig kjy.co.ki mx  
10 mail.kjy.co.ki  
#dig mail.kjy.ac.ki a  
192.168.0.100  
{: .notice}

```
[root@ns2 ~]# vi /var/named/kjy.co.ki.zone
$TTL    1D
$ORIGIN kjy.co.ki.
@       IN SOA          ns2     root (
                        2020052500
                        10M
                        3M
                        1W
                        1D      )
        IN NS           ns2
        IN NS           ns1
        IN MX   10      mail

ns2     IN A            192.168.0.164
ns1     IN A            192.168.0.114
mail    IN A            192.168.0.114	// Centos 5주소

www     IN A            192.168.0.114
ftp     IN A            12.12.12.12
```
```
[root@ns2 ~]# date 0525170630
2030. 05. 25. (토) 17:06:00 KST
[root@ns2 ~]#
```
시간 좀 맞추자
{: .notice}
```
[root@ns2 ~]# rndc reload
server reload successful
[root@ns2 ~]# dig @192.168.0.164 kjy.co.ki soa +short
ns2.kjy.co.ki. root.kjy.co.ki. 2020052500 600 180 604800 86400
[root@ns2 ~]# dig @192.168.0.114 kjy.co.ki soa +short
ns2.kjy.co.ki. root.kjy.co.ki. 2020052500 600 180 604800 86400
[root@ns2 ~]#
```
114와 164 둘 다 바뀐 것을 확인 해야 한다.
{: .notice}
```
[root@ns2 ~]# dig kjy.co.ki mx +short
10 mail.kjy.co.ki.
[root@ns2 ~]# dig mail.kjy.co.ki +short
192.168.0.114
[root@ns2 ~]#
```

2.	메일서버에서 수신시 방화벽 TCP 25 포트가 열려있어야한다.
{: .notice}
```
[root@ns2 ~]# iptables -L  //로 확인해본다
```
또는
{: .notice}
```
[root@ns2 ~]# iptables -nL
```
확인 후 포트 열어준다 ~
{: .notice}
```
[root@ns2 ~]# iptables -I INPUT -p tcp --dport 25 -j ACCEPT
```
또는 firewall-cmd –addport=25/tcp  
CentOS 5에서도 동일하게 iptables 한다  
{: .notice}
```
[root@ns1 ~]# iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:25
ACCEPT     udp  --  0.0.0.0/0            0.0.0.0/0           udp dpt:53
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:953
RH-Firewall-1-INPUT  all  --  0.0.0.0/0            0.0.0.0/0
```
telnet mail.kjy.co.ki 25 실행 해보는 방법도 있다.
{: .notice}

3.	방화벽을 통과하면 SMTP 서비스(sendmail) 서비스와 만나게 된다.
{: .notice}
```
[root@ns1 ~]# telnet 127.0.0.1 25
Trying 127.0.0.1...
Connected to localhost.localdomain (127.0.0.1).
Escape character is '^]'.
220 ns1.kjy.co.ki ESMTP Sendmail 8.13.8/8.13.8; Thu, 21 May 2020 16:06:05 +
```

4.	/etc/mail/sendmail.cf 265행   
265 O DaemonPortOptions=Port=smtp,Addr=127.0.0.1, Name=MTA  
Addr을 제거할 경우 모든 interface 수신 허용, 유지하는 경우 localhost 허용   
그 외, interface를 추가할 경우 아래 1행을 복사하여 추가할 ip주소를 추가한다  
Addr=192.168.0.114 추가하자  
{: .notice}
5.	그 다음에 access.db를 만나게된다  
{: .notice}
```
[root@ns1 mail]# vi access
//Co.ki에 대해서만 Relay해주자
[root@ns1 mail]# makemap hash access.db < access  //실행
[root@ns1 mail]# strings access.db
RELAY
co.ki
RELAY
connect:localhost.localdomain
RELAY
connect:127.0.0.1
RELAY
connect:localhost
```
makemap hash access < access 로 access.db 에서 db를 생략했다.
{: .notice}

6.	Access.db에서 허용된 메일은 /var/spool/mqueue 에 쌓여있게 되어있다.  
메일은 임시저장되고 순서대로 처리되기 시작한다.  
{: .notice}
7.	/etc/mail/sendmail.cf. 또는 /etc/mail/local-host-names 파일에 등록된 도메인은 최종적으로 수신자의 메일함에 저장되고, 그 외 도메인은 다른 곳으로 메일 라우팅 처리된다.  
/etc/mail/sendmail.cf  
85행 Cw도메인 또는  
/etc/mail/local-host-names 에   
Kjy.co.ki 추가한다 (우리는 후자)  
{: .notice}

설정이 끝났다.
{: .notice}
```
[root@ns1 mail]# service sendmail restart
```
