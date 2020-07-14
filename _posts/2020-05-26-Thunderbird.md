---
title: "LINUX: Thunderbird"
last_modified_at: 2020-05-26T20:20:02-05:00
categories:
  - LINUX
tags:
  - DNS
  - MUA
  - LINUX
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

---
### 메일 서버 구축
---

1 Hostname 수정 후 부팅  
CentOS ~6: /etc/sysconfig/network  
HOSTNAME=호스트명  
CentOS 7~: /etc/hostname  
호스트명  
{: .notice}
2 /etc/hosts 등록, NS 서버에 MX레코드와 A레코드를 등록  
Mail.kjy.co.ki  
/var/named/영역명.zone  
{: .notice}
```
$TTL    1D
$ORIGIN kjy.co.ki.
@       IN SOA          ns2     root (
                        2020052600
                        10M
                        3M
                        1W
                        1D      )
        IN NS           ns2
        IN NS           ns1
        IN MX   10      mail

ns2     IN A            192.168.0.164
ns1     IN A            192.168.0.114
mail    IN A            192.168.0.114

www     IN A            192.168.0.114
ftp     IN A            12.12.12.12
```

3 Sendmail 설치  
yum update sendmail  
또는 yum install sendmail  
Rpm -qc sendmail 을 입력하면 config 파일들도 볼 수 있다  
{: .notice}

4 /etc/mail/sendmail.cf 에서 수신 받을 IP 주소, 저장할 영역명  
265 O DaemonPortOptions=Port=smtp,Addr=127.0.0.1, Name=MTA  
Addr을 제거할 경우 모든 interface 수신 허용, 유지하는 경우 localhost 허용   
그 외, interface를 추가할 경우 아래 1행을 복사하여 추가할 ip주소를 추가한다  
Addr=192.168.0.114 추가  
{: .notice}
85행의 Cw영역명 추가 또는  
Fw/etc/mal/local-host-names 에 영역명 추가  
{: .notice}
```
[root@ns2 mail]# cat >> local-host-names  
kjy.co.ki  
```

5 /etc/mail/access 파일 편집하여 RELAY, DISCARD, REJECT할 IP대역, 도메인 추가  
/etc/mail/access  
192.168.0.114	RELAY	 : 특정 IP 주소만 릴레이(허용)   
192.168.0.0/24	RELAY	: IP 대역대로 릴레이  
…  
co.ki		RELAY	: *.co.ki 로 끝나는 영역 릴레이  
@naver.com	DISCARD: naver.com 영역에 거부  
spam@		REJECT: spam 계정에 반송  
{: .notice}
우리는 co.ki 만 추가  
{: .notice}

```
[root@ns2 mail]# vi access
[root@ns2 mail]# makemap hash access.db < access
[root@ns2 mail]# strings access.db
RELAY
connect:localhost.localdomain
RELAY
co.ki
RELAY
connect:localhost
RELAY
connect:127.0.0.1
[root@ns2 mail]#
```

6 데몬(서비스) 구동, 방화벽 포트 개방  
CentOS ~6: service 데몬명 start  
{: .notice}
Ns2 가 네임 서버  -  zone 파일 설정   
Ns1 이 메일 서버  - 전반적인 설정은 여기에 했어야했다.  
{: .notice}

---
### Thunderbird 설치
---
```
[root@mail mail]# wget ftp://192.168.0.50/rpms/uw-imap/*
[root@mail mail]# ls -lh *.rpm
-rw-r--r-- 1 root root 678K  7월 25  2015 libc-client2007-2007e-14.el5.i386.rpm
-rw-r--r-- 1 root root  78K  7월 25  2015 uw-imap-2007e-14.el5.i386.rpm
-rw-r--r-- 1 root root  60K  7월 25  2015 uw-imap-utils-2007e-14.el5.i386.rpm
[root@mail mail]# rpm -Uvh libc-client2007-2007e-14.el5.i386.rpm
경고: libc-client2007-2007e-14.el5.i386.rpm: Header V3 DSA signature: NOKEY, key ID 217521f6
준비 중...               ########################################### [100%]
   1:libc-client2007        ########################################### [100%]
[root@mail mail]# rpm -Uvh uw-imap-2007e-14.el5.i386.rpm
경고: uw-imap-2007e-14.el5.i386.rpm: Header V3 DSA signature: NOKEY, key ID 217521f6
준비 중...               ########################################### [100%]
   1:uw-imap                ########################################### [100%]
[root@mail mail]# rpm -Uvh uw-imap-utils-2007e-14.el5.i386.rpm
경고: uw-imap-utils-2007e-14.el5.i386.rpm: Header V3 DSA signature: NOKEY, key ID 217521f6
준비 중...               ########################################### [100%]
   1:uw-imap-utils          ########################################### [100%]
[root@mail mail]#
```
```
[root@mail mail]# rpm -qc uw-imap
```
```
Imap 과 ipop3 disable no 로 수정 
[root@mail mail]# vi /etc/xinetd.d/ipop3
[root@mail mail]# vi /etc/xinetd.d/imap
[root@mail mail]#
[root@mail mail]# service xinetd restart
xinetd 를 정지 중:                                         [  OK  ]
xinetd (을)를 시작 중:                                     [  OK  ]
[root@mail mail]#
[root@mail mail]# iptables -I INPUT -p tcp --dport 110 -j ACCEPT
[root@mail mail]# iptables -I INPUT -p tcp --dport 143 -j ACCEPT
[root@mail mail]#
```

이제 썬더 버드 설치한 거를 보자  
pop3는 메일을 지워지면서 가져온다   
Imap은 access한다 client 가 메일을 보러 들어간다 서버에 메일이 유지된다.   
{: .notice}
