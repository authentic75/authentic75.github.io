---
title: "squirrelmail"
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

DNS 주소를 192.168.0.150
보조 DNS 192.168.0.100 설정
{: .notice}

그리고 CentOS5, 8 을 켜서  
각각 시간을 세팅해준다
{: .notice}
```
CentOS5  # date -s time.bora.net
CentOS8  # date -s “2020-05-27 13:52:00”
```

Kjy.co.ki 가 있다고 가정하자 이 안에는 
Ns1/smtp/mail(pop3)/www/www 이 있다고 하자
네이버에도 마찬가지로 Ns1/smtp/mail(pop3)/www/www 가 있다고 하자 
{: .notice}
발신자: Kjy@kjy.co.ki
수신자: User1@naver.com
{: .notice}
메일을 보낸다고 가정하자 우리가 smtp 에 메일을 보내겠다고 의뢰 그러면 naver 안에 mail(pop3)로 메일을 전달 한다 반대로 네이버에서 답장이 온다고 해도 smtp를 통해 kjy의 mail(pop3)로 온다.
{: .notice}
smtp 는 전송만을 맡는다. Kjy.co.ki 또는 naver가 RELAY 설정이 되어있어야 받아들인다  
메일을 보내면 nslookup -type=mx naver.com 레코드를 조회한다  
그러면 naver에서 mx1.naver.com 의 주소 125.209.238.100 다음으로 mx2 mx3 의 주소를 얻는다…  
네이버에서 별도로 pop3/smtp 를 사용하려 보면 smtp.naver.com이 smtp 서버 주소라고 나와있다  
pop.naver.com이 pop 서버이다. 우리는 최종적으로 mail(pop3) 에서 메일을 조회하고 다운받는다  
{: .notice}

Outlook 같은 사용자를 사용할 경우 (MUA)  
우리가 메일을 직접 목적지에 보내는게 아니라 smtp 프로토콜로 메일을 보내면 DNS mx 조회를 통해서 smtp가 상대편 메일 교환기를 찾아준다. 반대도 마찬가지로 smtp 서버를 통해서 mx 레코드에 등록되어있는 곳으로 메일을 보낸다. 그리고 그곳에 메일이 쌓여있고 MUA를 통해 메일을 다운받아와서 본다   
네이버 같은 곳 (웹상에서 메일을 보내는경우)  
우리가 메일을 보내면 HTTP(httpd)가 SMTP를 통해서 메일 교환기 쪽으로 메일을 보냅니다. 이 상황에서는 포트번호 80번이 열려 있어야한다. 이때 pop3는 사용안하고 IMAP을 사용한다. Pop3는 세션이 끊기는 순간 메일이 지워져서 내 메일을 볼 수가 없다. 메일 확인도 최종적으로 HTTP를 통해서 본다  
{: .notice}


P546  ?  웹메일
웹서버를 설치하여 구성할 건데  웹서버와  메일(IMAP)서버는 어쩔 수 없이 같은 CentOS5에 구현할 것이다.
이때 통신은 127.0.0.1 로 해볼 것. 그리고 클라이언트(Centos8)로 웹서버에 접근하여 메일을 보내볼 것이다.
그리고 잘되면 CentOS8 에다가도 웹서버를 구현해볼 것이다.

우선 CentOS 8 에서 zone 파일을 보자 www 의 주소 웹서버 주소를 100번대로 ~ 그런데 이미 되어있다.
웹브라우저에서 www.kjy.co.ki 로 접속해보자 안된다. 7계층 에러로 일단 보고 아래 계층을 확인해보자
Ping을 www.kjy.co.ki 로 보내면 잘 된다. 
그렇다면 telnet으로 CentOS5에서 80번 포트로 윈도우에 접속해보자 안된다.

[root@mail ~]# iptables -nL | grep 80
[root@mail ~]# iptables -I INPUT -p tcp --dport 80 -j ACCEPT
[root@mail ~]# service httpd start
그 후에 telnet 으로 80 포트 해서 mail.kjy.co.ki 잘 들어가지나 확인
윈도우로 확인하면 불친절 하니 CentOS8에서 CentOS5 로 확인해보자 잘된다.
그렇다면 웹브라우저에 들어가서 mail.kjy.co.ki 로 접속해보자 이번엔 잘들어가진다.

자 그러면 ns1 에서 간단하게 홈페이지를 구성해두자


[root@mail html]# cat index.html
<html>
<head>


</head>
<title>강정윤의 홈페이지</title>
<body>
강정윤의 홈페이지 입니다.<br>
방문해주셔서 감사합니다.<br>
</body>
</html>
[root@mail html]# yum install squirrelmail -y -q
다람쥐 메일을 설치하고 패키지를 확인해보자

[root@mail html]# rpm -qa | grep squirrel
squirrelmail-1.4.8-21.el5.centos
[root@mail html]# rpm -qc squirrelmail
/etc/httpd/conf.d/squirrelmail.conf
/etc/squirrelmail/config.php
/etc/squirrelmail/config_local.php
/etc/squirrelmail/default_pref
/etc/squirrelmail/sqspell_config.php

http://www.kjy.co.ki/index.html  ->  192.168.0.114:/var/www/html/index.html
그런데 만약 /abc 와 같은 없는 폴더에 접근하게 되면 디렉토리 내부를 보여준다. 하지만 없으면 404 컨텐츠 없음 이 뜬다.
시간 01:43:33
http://www.kjy.co.ki/webmail/  index.php
가상 디렉토리 사용할 것이다.

[root@mail html]# ls -lh /etc/httpd/conf/httpd.conf
-rw-r--r-- 1 root root 33K  9월 17  2014 /etc/httpd/conf/httpd.conf

1 alias   /webmail/       /usr/share/squirrelmail/
첫 줄에 추가한다. (별칭을 만든다는 뜻이다) 
/var/www/html/webmail 을 / usr/share/squirrelmail/ 로 연결해줌

www.kjy.co.ki/webmail  접속  안되면 캐시 지워라
 
이제 언어를 바꾸고 IMAP을 지정해줄 것이다.

[root@mail html]# ls -lh /usr/share/squirrelmail/config/conf.pl
-rwxr-xr-x 1 root root 146K  1월  9  2013 /usr/share/squirrelmail/config/conf.pl
[root@mail html]#

이 파일로 환경 구성을 할 것이다.
/usr/share/squirrelmail/config/conf.pl  치고 엔터치면 실행된다.
 

우리가 메일을 작성해서 보내면 웹 메일이 mqueue 에 알아서 메일을 넣어준다.
보내는 서버, 받는 서버, 포맷 설정만 해주면 된다

우선 2 > 1 > kjy.co.ki 순으로 입력해서 도메인 세팅
3 > 1  입력해서 sendmail 을 사용할 것이라고 설정
 
우리가 썬더메일에서 어제 받는 메일 서버와 보내는 메일 서버 주소를 뭐로 설정했었는가
Mail.kjy.co.ki 로 했었다. 그렇지만 여기서는 다르게 줘보자
우선 IMAP Server 설정부터  A > 4 > mail.kjy.co.ki 
썬더버드에서 pop으로  웹에서는 IMAP 을 사용해야한다
우리가 uwi 로 IMAP을 설치 했었고 disable no 로 해놨었다. 
 

원래는 http (7L) tcp(80 4L) 로 웹에 접속하지만 https는  http(7L), ssl(4L), tcp 443(4L) 
지금은 표준화 되면서 ssl -> TLS 라고 부른다 TLS 사용하면 465 번으로 ? 전송하지만 우리는 사용 아직 하지 않을 것이다.

Return > R > 10 > 1 > ko_KR
 
2 > UTF-8  설정해준다
그상태로 다람쥐 메일에서 
오오 아이디 로그인 입력하면 로그인이 된다.
Kjy 123

