---
title: "LINUX: DNS의 위임체계"
last_modified_at: 2020-05-21T20:20:02-05:00
categories:
  - LINUX
tags:
  - DNS
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
### DNS 위임체계를 생성해보자
---

* 다음과 같은 상황에서 위임이 이루어 진다
	* mint.com 을 만들건데  
	* jeju.mint.com 제주도 지역과 관련된 것은 jeju 지사에 위임을 해주도록 할 것이다 
	* admin.jeju.mint.com 제주 지역내 관리 업무는 관리 부서에 위임을 줄 것 이다
	* prod.jeju.mint.com 제주 지역내 제품 관리 업무는 제품 관리 부서에 위임을 줄 것이다
{: .notice}

---
#### come 도메인 생성
---

com 도메인을 우선 생성해보자
{: .notice}

```console
#/etc/named.conf

Zone “com” IN {
Type master;
File “come.zone”;
}; 
```
```console
#named.conf에서 설정한 zone 파일을 생성해줍니다.
[root@ns2 ~]# cd /var/named/
[root@ns2 ~]# Touch com.zone 추가 
[root@ns2 ~]# vi com.zone 
```
```console
$TTL 1D
$ORIGIN com.
@     IN  SOA  ns1  root (
2020052100 ; Serial
3H ; Refresh
15M ; Retry
1W; Expire
1D ) ; Minimum TTL

IN NS  ns1
ns1          IN  A   192.168.0.150
mint     IN  NS   ns1.mint.com.
ns1.mint  IN  A    192.168.0.151

apple     IN  NS      ns1.mint.com.
ns1.apple  IN  A       192.168.0.151

instagram     IN  NS  ns1.mint.com.
ns1.instagram  IN  A   192.168.0.151
```

---
#### abc 도메인 생성
---

* abc.com.  ns2.abc.com.  192.168.0.170
* Busan.abc.com. ns1.busan.abc.com.  192.168.0.171 로 가정하자
{: .notice}

```console
# vi /etc/named.conf
# 마지막 행으로 가서 해당 영역을 설정해보자 

ZONE “abc.com” IN {
      Type master;
      File “abc.com.zone”;
};
```

```console
cd /var/named/
touch abc.com.zone 
vi abc.com.zone
```

```console
$TTL  10
$ORIGIN abc.com.
@  IN SOA  ns1   root  (
            2020052100
               3H
              15M
               1W
               1D )
        IN NS   ns1
Ns1    IN A      192.168.0.170
Busan IN NS ns1.busan
Ns1.busan IN A 192.168.0.171
```

```console
만들고나서 restart 필수
[root@ns2 ~]# service named restart
```
---
#### busan.abc.com 도메인 생성
---
```
# /etc/named.conf
ZONE “busan.abc.com” IN{
    Type master;
    File “busan.abc.com.zone”;
};
```

```
$ TTL 1D
$ ORIGIN
@       IN SOA  ns1     root (
                2020052110
                3H
                15M
                1W
                1D )
        IN NS   ns1 
ns  IN A   192.168.0.161

admin IN NS ns1.admin
ns1.admin IN A 192.168.0.161

sales IN NS ns1.sales
ns1.sales IN A 192.168.0.161
```

---
#### 조를 편성하여 구성해보자
---

* 나의 팀은 instagram.com을 구성하기로 하였다.
* instagram.com은 seoul에게 위임을 하고 
* seoul은 admin에게 위임을 한다
* 내가 맡은 부분은 admin 이다
{: .notice}

```
#admin.seoul.instagram.com을 name.conf에서 설정해준다
ZONE "admin.seoul.instagram.com" IN {
        type master;
        file "admin.seoul.instagram.com.zone";
};
```

```
#설정한 zone파일을 /var/named/ 밑에 생성해줍니다.
$TTL 1D
$ORIGIN admin.seoul.instagram.com.
@       IN SOA  ns1     root (
                2020052110
                3H
                15M
                1W
                1D )
        IN NS   ns1
ns1     IN A    192.168.0.164
admin   IN A 192.168.0.164

www     IN A 1.1.1.1

ftp     IN A 2.2.2.2
```

---
### 간단한 Crawling 코드를 작성해보자
---

```
[root@ns2 named]# cat > dict.txt
www
ftp
db
file
mail
mx
mx1
mx2
ns1
ns2
```
```
[root@ns2 named]# cat dnsgather.py
import os

file = open("dict.txt", "r")
read = file.read().strip()
words = read.split("\n")

for word in words:
        command = "dig {}.naver.com +short > result".format(word)
        result = os.system(command)
        f = open("result", "r")
        r = f.read()
        if r:
                print("도메인네임: {}.naver.com".format(word))
                print(r.strip())
                print("-----------------------")
```
```
[root@ns2 named]# python3 dnsgather.py
도메인네임: www.naver.com
www.naver.com.nheos.com.
210.89.160.88
125.209.222.142
-----------------------
도메인네임: file.naver.com
182.162.192.161
-----------------------
도메인네임: mail.naver.com
mail.naver.com.nheos.com.
125.209.230.135
125.209.218.225
-----------------------
도메인네임: mx1.naver.com
125.209.238.100
-----------------------
도메인네임: mx2.naver.com
125.209.238.137
-----------------------
도메인네임: ns1.naver.com
125.209.248.6
-----------------------
도메인네임: ns2.naver.com
125.209.249.6
-----------------------
```

이처럼 쉽게 정보를 가져올 수 있다.
{: .notice}

---
### 영역 전송을 사용하여 정보를 받아보자
---

* 보안 대책
* 서버는 zone 파일을 받고 싶어하는 사람에게 가리지 않고 보내준다 이를 영역 전송이라고 부른다 (보안취약)
* allow-transfer { CentOS5_IP(slave) ); slave를 특정하여 운영해줘야 안전하다
* Master/Slave 구조가 아니라면 allow-transfer {none}; 으로 설정하여 영역 전송을 방지한다
* Centos ~6 까지는 slave 파일들이 txt 라서 cat으로도 조회가 가능하다
{: .notice}

다른 사람 컴퓨터로 부터 zone 파일을 받아 올 수도 있다.
{: .notice}

```
include "/etc/named.rfc1912.zones";
include "/etc/named.root.hints";

ZONE "kjy.co.ki" IN {
        type slave;
        file "slaves/kjy.co.ki.bak.zone";
        masters { 192.168.0.164;};
};  // 수정을 하자 

ZONE "jyj.co.ki" IN {
        type slave;
        file "slaves/jyj.co.ki.bak.zone";
        master { 192.168.0.113; };
}; //추가
```

같은 방법으로 danawa.com으로 부터 영역파일을 받아오자
{: .notice}

```
ZONE "danawa.com" IN {
        type slave;
        file "slaves/danawa.com.bak.zone";
        masters {121.189.13.231; 119.205.194.130;};
};
```

```
[root@ns1 named]# tail -n 10 slaves/danawa.com.bak.zone
www9-news               A       119.205.194.109
www9-pc                 A       119.205.194.109
www9-plan               A       119.205.194.109
www9-prod               A       119.205.194.109
www9-search             A       119.205.194.109
www9-shop               A       119.205.194.109
www9-static             A       119.205.194.109
www9-used               A       119.205.194.109
www9-web                A       119.205.194.109
young                   A       125.209.214.79
[root@ns1 named]#
```

---
### 역방향 조회
---

도메인 네임 -> IP 주소 정방향 조회  
IP 주소 -> 도메인 네임 역방향 조회  
{: .notice}

168.126.63.1 - 역방향 PRT -> kns.kornet.net  
1.63.126.168.in-addr.arpa. 역방향 조회시 사용하는 주소  
164.0.168.192.in-addr.arpa. 168.x.x.x ISP  
Domain name InterNIC -> APNIC -> KRNIC kr , co.kr , or.kr  
{: .notice}

```console
[root@ns1 named]# dig -x 168.126.63.1

; <<>> DiG 9.3.6-P1-RedHat-9.3.6-20.P1.el5_8.6 <<>> -x 168.126.63.1
;; global options:  printcmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 24623
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 2

;; QUESTION SECTION:
;1.63.126.168.in-addr.arpa.     IN      PTR

;; ANSWER SECTION:
1.63.126.168.in-addr.ARPA. 43186 IN     PTR     kns.kornet.net.

;; AUTHORITY SECTION:
63.126.168.in-addr.ARPA. 43185  IN      NS      rev2.kornet.net.
63.126.168.in-addr.ARPA. 43185  IN      NS      rev1.kornet.net.

;; ADDITIONAL SECTION:
rev1.kornet.net.        43186   IN      A       211.216.50.170
rev2.kornet.net.        43186   IN      A       211.216.50.180

;; Query time: 2 msec
;; SERVER: 192.168.0.150#53(192.168.0.150)
;; WHEN: Thu May 21 17:18:00 2020
;; MSG SIZE  rcvd: 166
```

우리도 한번 구현해보자 ~
{: .notice}

```console
#ns 1 named.conf 
//192.168.0.114 Reverce NS Configuration
//
ZONE "114.0.168.192.in-addr.arpa" IN {
        type master;
        file "192.168.0.114.rev";
};
```
```console
#zone 파일에 PTR 레코드를 추가해준다
$TTL    1D
$ORIGIN 114.0.168.192.in-addr.arpa.
@       IN SOA  ns1.kjy.co.ki.  root.kjy.co.ki. (
                2020052100
                3H
                15M
                1W
                1D      )
        IN NS   ns1.kjy.co.ki.

        IN PTR ns1.kjy.co.ki.
        IN PTR www.kjy.co.ki.
```

조회해보자
{: .notice}

```console
[root@ns1 named]# dig @127.0.0.1 -x 192.168.0.114

; <<>> DiG 9.3.6-P1-RedHat-9.3.6-20.P1.el5_8.6 <<>> @127.0.0.1 -x 192.168.0.114
; (1 server found)
;; global options:  printcmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 24221
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 1, ADDITIONAL: 1

;; QUESTION SECTION:
;114.0.168.192.in-addr.arpa.    IN      PTR

;; ANSWER SECTION:
114.0.168.192.in-addr.arpa. 86400 IN    PTR     ns1.kjy.co.ki.
114.0.168.192.in-addr.arpa. 86400 IN    PTR     www.kjy.co.ki.

;; AUTHORITY SECTION:
114.0.168.192.in-addr.arpa. 86400 IN    NS      ns1.kjy.co.ki.

;; ADDITIONAL SECTION:
ns1.kjy.co.ki.          86400   IN      A       192.168.0.114

;; Query time: 0 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
;; WHEN: Thu May 21 17:29:10 2020
;; MSG SIZE  rcvd: 119
```