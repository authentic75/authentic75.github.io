---
title: "LINUX: Squirrelmail"
last_modified_at: 2020-05-27T20:20:02-05:00
categories:
  - LINUX
tags:
  - DNS
  - 웹메일
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
### 메일 전송 과정
---

* 다음과 같은 조건으로 메일을 송수신 한다고 가정하자
* 발신자: Kjy@kjy.co.ki
	* Ns1/smtp/mail(pop3)/www/www
* 수신자: User1@naver.com
	* Ns1/smtp/mail(pop3)/www/www
{: .notice}

* kjy@kjy.co.ki에서 메일을 보낼때
	* smtp 에 메일을 보내겠다고 의뢰 그러면 naver 안에 mail(pop3)로 메일을 전달
* 네이버에서 답장을 보낼 때
	* smtp를 통해 kjy의 mail(pop3)로 온다
{: .notice}

* smtp 는 전송만을 맡는다
* Kjy.co.ki와 naver가 서로의 access에 RELAY 설정이 되어있어야 받아들인다  
* 메일 송신 시, 가장 먼저 nslookup -type=mx naver.com 레코드를 조회한다  
	* 결과로 naver에서 mx1.naver.com 의 주소 125.209.238.100 다음으로 mx2 mx3 의 주소를 얻는다  
* 네이버 사이트에서도 이 주소를 확인 할 수 있는데 설정에서 pop3/smtp를 살펴보면 smtp 서버 주소(smtp.naver.com)가 공개되어있다
* 마찬가지로 pop 서버주소는 pop.naver.com이 이다
* 우리는 최종적으로 mail(pop3) 에서 메일을 조회하고 다운받는다  
{: .notice--info}

* Outlook 같은 프로그램 사용할 경우  
	* 우리가 메일을 직접 목적지에 보내는게 아니라 smtp 프로토콜을 통해서 메일을 전송한다
	* 메일을 보내면 DNS mx 조회를 통해서 smtp가 상대편 메일 교환기를 찾아준다
	* 반대도 마찬가지로 smtp 서버를 통해서 mx 레코드에 등록되어있는 곳으로 메일을 보낸다
	* 그리고 spool에 메일을 쌓아두고 MUA를 통해 메일을 다운받아와서 본다   
* 네이버 같은 웹메일을 이용할 경우 
	* 우리가 메일을 보내면 HTTP(httpd)가 SMTP를 통해서 메일 교환기 쪽으로 메일을 보냅니다
	* 이 상황에서는 포트번호 80번이 열려 있어야한다
	* 이때 pop3는 사용안하고 IMAP을 사용한다
	* Pop3는 세션이 끊기는 순간 메일이 지워져서 내 메일을 볼 수가 없다 (웹메일은 받은메일함에 기본적으로 메일이 저장되어 있어야한다)
	* 메일 확인도 최종적으로 HTTP를 통해서 본다  
{: .notice--info}

---
### 웹서버 구성
---

DNS 주소를 192.168.0.150
보조 DNS 192.168.0.100 설정
{: .notice}

그리고 CentOS5, 8 을 켜서 각각 시간을 세팅해준다
{: .notice}
```
CentOS5  # date -s time.bora.net
CentOS8  # date -s “2020-05-27 13:52:00”
```

웹서버를 설치하여 구성할 건데  웹서버와  메일(IMAP)서버는 어쩔 수 없이 같은 CentOS5에 구현할 것이다.  
이때 통신은 127.0.0.1 로 해볼 것. 그리고 클라이언트(Centos8)로 웹서버에 접근하여 메일을 보내볼 것이다.  
그리고 잘되면 CentOS8 에다가도 웹서버를 구현해볼 것이다.  
{: .notice}

* CentOS 8 에 네임서버가 구축되어있다는 가정하에 진행한다  
* zone 파일에서 www 의 주소(웹서버 주소)를 CentOS5 주소로 설정해줍니다
* 그리고 웹브라우저에서 www.kjy.co.ki 로 접속해보자
	* 안되는 경우 7계층 에러인지 확인하기 위해서 Ping test를 해본다
	* Ping을 www.kjy.co.ki 로 보내면 잘 된다
	* 그렇다면 telnet으로 CentOS5에서 80번 포트로 윈도우에 접속해보자 안된다
	* 결론은 방화벽의 문제다
{: .notice}
```
[root@mail ~]# iptables -nL | grep 80
[root@mail ~]# iptables -I INPUT -p tcp --dport 80 -j ACCEPT
[root@mail ~]# service httpd start
```
* 다시 telent으로 80포트 테스트를 해본다
* 마찬가지고 telnet 으로 80 포트 mail.kjy.co.ki 테스트를 해본다
* 윈도우로 확인하면 메세지를 반환해주지 않으니 CentOS8에서 CentOS5 로 확인해보자 잘된다
* 그렇다면 웹브라우저에 들어가서 mail.kjy.co.ki 로 접속해보자 잘들어가진다
{: .notice}

ns1 에서 간단하게 홈페이지를 구성해두자
{: .notice}

```
[root@mail html]# cat index.html
<html>
<head>


</head>
<title>강정윤의 홈페이지</title>
<body>
MINT의 홈페이지 입니다.<br>
방문해주셔서 감사합니다.<br>
</body>
</html>
```

---
### 다람쥐 메일 설치 및 설정
---
다람쥐 메일을 설치하고 패키지를 확인해보자
{: .notice--info}
```
[root@mail html]# yum install squirrelmail -y -q
[root@mail html]# rpm -qa | grep squirrel
squirrelmail-1.4.8-21.el5.centos
[root@mail html]# rpm -qc squirrelmail
/etc/httpd/conf.d/squirrelmail.conf
/etc/squirrelmail/config.php
/etc/squirrelmail/config_local.php
/etc/squirrelmail/default_pref
/etc/squirrelmail/sqspell_config.php
```

* http://www.kjy.co.ki/index.html 는 192.168.0.114:/var/www/html/index.html 를 가리킨다
* http://www.kjy.co.ki/webmail/ 를 index.php 으로 가상디렉토리를 설정해줄 것이다 
{: .notice}
```
[root@mail html]# ls -lh /etc/httpd/conf/httpd.conf
-rw-r--r-- 1 root root 33K  9월 17  2014 /etc/httpd/conf/httpd.conf
```
* httpd.conf에 alias를 이용하여 디렉토리 경로의 별칭을 만들어준다
* 첫 줄에 alias	/webmail/	/usr/share/squirrelmail/ 추가한다. (별칭을 만든다는 뜻이다) 
	* /var/www/html/webmail 을 / usr/share/squirrelmail/ 로 연결해줌
* www.kjy.co.ki/webmail 로 접속시도해보고 안되면 캐시를 지우고 시도해보자
{: .notice--info}

이제 언어를 바꾸고 IMAP을 지정해줄 것이다.
{: .notice}
```
[root@mail html]# ls -lh /usr/share/squirrelmail/config/conf.pl
-rwxr-xr-x 1 root root 146K  1월  9  2013 /usr/share/squirrelmail/config/conf.pl
[root@mail html]#
```

* /usr/share/squirrelmail/config/conf.pl 을 실행하면 콘솔창에서 환경 설정 화면에 들어갈 수 있다.
* 우리가 메일을 작성해서 보내면 웹 메일이 mqueue 에 알아서 메일을 넣어주기 때문에 보내는 서버, 받는 서버, 포맷 설정만 해주면 된다
{: .notice--info}

1. 우선 2 > 1 > kjy.co.ki 순으로 입력해서 도메인 세팅
2. 3 > 1  입력해서 sendmail 을 사용할 것이라고 설정
3. IMAP Server 설정을 위해  A > 4 > mail.kjy.co.ki 
	* 썬더버드에서 pop으로  웹에서는 IMAP 을 사용해야한다
	* uwi 로 IMAP을 설치 했었고 disable no 로 해놨었다.(썬더버드 게시물 참고)
4. Return > R > 10 > 1 > ko_KR
5. 2 > UTF-8  설정해준다
{: .notice--info} 

* 원래는 http (7L) tcp(80 4L) 로 웹에 접속하지만 https는  http(7L), ssl(4L), tcp 443(4L) 
지금은 표준화 되면서 ssl -> TLS 라고 부른다
{: .notice--warning} 

그상태로 다람쥐 메일에서 아이디 로그인 입력하면 로그인이 된다.
{: .notice} 


