---
title: "Cryptography: 스니핑과 스캐너"
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
### 스니핑을 구현해보자
---

네트워크 소켓은 클라이언트나 서버 프로그램을 구현하기 위한 가장 핵심적인 모듈이다.  
네트워크 통신에 있어서 시작점과 종착점으로 소켓을 통해서 서로 데이터를 교환한다.  
네트워크를 통한 컴퓨터 사이의 통신은 거의 IP 기반이므로 네트워크 소켓은 대부분 인터넷 소켓이다.  
{: .notice}

* **인터넷 소켓의 종류**
* TCP 소켓
* UDP 소켓
* Raw 소켓: 라우터나 네트워크 장비에서 활용되는 네트워크 소켓
{: .notice}

---
#### 단순 패킷 수신
---

```python
from socket import *
import os
def sniffing(host):   
    if os.name == 'nt':
        sock_protocol = IPPROTO_IP 						#사용할 프로토콜 지정(윈도우는 IPPROTO_IP)
    else:
        sock_protocol = IPPROTO_ICMP 
    sniffer = socket(AF_INET, SOCK_RAW, sock_protocol)  #Raw소켓 생성
    sniffer.bind((host, 0)) 							#호스트와 바인드
    sniffer.setsockopt(IPPROTO_IP, IP_HDRINCL, 1)
    if os.name == 'nt':    								#윈도우인 경우 promiscuous 모드로 변경하여 모든 패킷 수신함
        sniffer.ioctl(SIO_RCVALL, RCVALL_ON)			#promiscuous모드를 켠다
    packet = sniffer.recvfrom(65565)      				#소켓으로 패킷이 들어올때까지 대기함 (65565바이트는 버퍼크기를 의미)
    print(packet)
    if os.name == 'nt':
        sniffer.ioctl(SIO_RCVALL, RCVALL_OFF) 			#윈도우인 경우 이전에 설정했던
                                                        #promiscuous모드를 해제함
def main():
    host = gethostbyname(gethostname())    				#Ipv4(현재호스트 이름). 해당 컴퓨터 IP주소
    print('START SNIFFING at [%s]' %host)
    sniffing(host)
if __name__ == '__main__':
    main()
```
```
START SNIFFINT at [192.168.3.1]
(b'E\x00\x00<\x06E\x00\x00\x80\x01\x00\x00\xc0\xa8\x03\
x01\xc0\xa8\x03\x01\x08\x00I\xe0\x00\x01\x03
{abcdefghijklmnopqrstuvwabcdefghi', ('192.168.3.1', 0))
```
```
START SNIFFING at [192.168.3.1]
(b'E\x00\x008\x89\xec\x00\x00\x01\x11\x00\x00\xc0\xa8\x03\
x01\xe0\x00\x00\xfb\x14\xe9\x14\xe9\x00$\x1c}\x00\x00\x00\
x00\x00\x01\x00\x00\x00\x00\x00\x00\x04wpad\x05local\
x00\x00\x01\x00\x01', ('192.168.3.1', 0))
```
---
#### 헤더 수신
---

ip 헤더만 뽑아보자
{: .notice}

```python
from socket import *
import os

def recvData(sock): 	#패킷을 수신하는 부분
    data = ''
    try:
        data = sock.recvfrom(65565)
    except timeout:
        data = ''
    return data[0]	    #첫번째 멤버인 바이트 코드에 IP헤더가 포함됨
    
def sniffing(host):
    if os.name == 'nt':
        sock_protocol = IPPROTO_IP
    else:
        sock_protocol = IPPROTO_ICMP
    sniffer = socket(AF_INET, SOCK_RAW, sock_protocol)
    sniffer.bind((host, 0))
    sniffer.setsockopt(IPPROTO_IP, IP_HDRINCL, 1)
    if os.name == 'nt':
        sniffer.ioctl(SIO_RCVALL, RCVALL_ON)
        
    count = 1
    try:												  #Ctrl+C키를 누를때 까지 계속해서 패킷 수신
        while True:
            data = recvData(sniffer)
            print('SNIFFED [%d] %s' %(count, data[:20]))  #처음 20바이트가 IP헤더 부분
            count += 1
    except KeyboardInterrupt:
        if os.name == 'nt':
            sniffer.ioctl(SIO_RCVALL, RCVALL_OFF)		  #promiscuous모드 OFF
                                                 
def main():
    host = gethostbyname(gethostname())
    print('START SNIFFINT at [%s]' %host)
    sniffing(host)
if __name__ == '__main__':
    main()
```

```
START SNIFFINT at [192.168.3.1]
SNIFFED [1] b'E\x00\x00\xbf\xf9\xba\x00\x00\x01\x11\x00\x00\xc0\xa8\x03\x01\xef\xff\xff\xfa'
SNIFFED [2] b'E\x00\x00\xbf\xf9\xba\x00\x00\x01\x11\x0b\xd0\xc0\xa8\x03\x01\xef\xff\xff\xfa'
SNIFFED [3] b'E\x00\x00\xbf\xf9\xbb\x00\x00\x01\x11\x00\x00\xc0\xa8\x03\x01\xef\xff\xff\xfa'
SNIFFED [4] b'E\x00\x00\xbf\xf9\xbb\x00\x00\x01\x11\x0b\xcf\xc0\xa8\x03\x01\xef\xff\xff\xfa'
SNIFFED [5] b'E\x00\x008\x8a\x04\x00\x00\x01\x11\x00\x00\xc0\xa8\x03\x01\xe0\x00\x00\xfb'
SNIFFED [6] b'E\x00\x008\x8a\x04\x00\x00\x01\x11\x8b\x0c\xc0\xa8\x03\x01\xe0\x00\x00\xfb'
SNIFFED [7] b'E\x00\x00N\xf8=\x00\x00\x80\x11\x00\x00\xc0\xa8\x03\x01\xc0\xa8\x03\xff'
SNIFFED [8] b'E\x00\x00N\xf8=\x00\x00\x80\x11\xba\x10\xc0\xa8\x03\x01\xc0\xa8\x03\xff'
```

---
#### 헤더로 부터 정보를 얻어보자
---

```python
ipheader = struct.unpack('!BBHHHBBH4s4s', data[:20])
```

위의 의미는 !(네트워크 바이트 순서), B(unsigned char 1바이트), H(unsigned short 2바이트), 4s(char[4] 4바이트)라는 뜻으로
1, 1, 2, 2, 2, 1, 1, 2, 4, 4 바이트로 구분하여 파이썬 튜플을 만듭니다. 그리고 data[:20]의 값을 튜플에 넣습니다.
{: .notice--info}

* ipheader[0] Version+Header Length
* ipheader[1] Service Type
* ipheader[2] Entire Packet Length
* ipheader[3] Datagram ID
* ipheader[4] Flag+Fragment Offset
* ipheader[5] Time to Live
* ipheader[6] Protocol
* ipheader[7] Header Checksum
* ipheader[8] Source IP Address
* ipheader[9] Destination IP Address
{: .notice--warning}

```python
from socket import *
import os
import struct

def parse_ipheader(data):
    ipheader = struct.unpack('!BBHHHBBH4s4s', data[:20])
    return ipheader
	
def getDatagramSize(ipheader):  #데이터 그램 크기 추출
    return ipheader[2]
	
def getProtocol(ipheader):		#프로토콜 추출
    protocols = {1:'ICMP', 6:'TCP', 17:'UDP'}
    proto = ipheader[6]
    if proto in protocols:
        return protocols[proto]
    else:
        return 'OTHERS'
		
def getIP(ipheader):			#IP 주소 추출
    src_ip = inet_ntoa(ipheader[8])  #바이트 문자열->IP주소 형식
    dst_ip = inet_ntoa(ipheader[9])
    return (src_ip, dst_ip)
	
def recvData(sock):
    data = ''
    try:
        data = sock.recvfrom(65565)
    except timeout:
        data = ''
    return data[0]
    
def sniffing(host):
    if os.name == 'nt':
        sock_protocol = IPPROTO_IP
    else:
        sock_protocol = IPPROTO_ICMP
    sniffer = socket(AF_INET, SOCK_RAW, sock_protocol)
    print('AF_INET',AF_INET)
    sniffer.bind(('192.168.0.4', 0)) #여기에 주소를 지정할 수 있따.
    sniffer.setsockopt(IPPROTO_IP, IP_HDRINCL, 1)
    if os.name == 'nt':
        sniffer.ioctl(SIO_RCVALL, RCVALL_ON)
    count = 1
	
    try:
        while True:
            data = recvData(sniffer)
            ipheader = parse_ipheader(data[:20])
            datagramSize = getDatagramSize(ipheader)
            protocol = getProtocol(ipheader)
            src_ip, dst_ip = getIP(ipheader)
            print('\nSNIFFED [%d] ++++++++++' %count)
            print('Datagram SIZE:\t%s' %str(datagramSize))
            print('Protocol:\t%s' %protocol)
            print('Source IP:\t%s' %src_ip)
            print('Destination IP:\t%s' %dst_ip)
            count += 1
			
    except KeyboardInterrupt:
        if os.name == 'nt':
            sniffer.ioctl(SIO_RCVALL, RCVALL_OFF)
                                                 
def main():
    host = gethostbyname(gethostname())
    print('START SNIFFINT at [%s]' %host)
    sniffing(host)
if __name__ == '__main__':
    main()
```

```
START SNIFFINT at [192.168.3.1]

SNIFFED [1] ++++++++++
Datagram SIZE:  68
Protocol:       TCP
Source IP:      45.113.130.88
Destination IP: 192.168.0.4

SNIFFED [2] ++++++++++
Datagram SIZE:  68
Protocol:       TCP
Source IP:      45.113.130.88
Destination IP: 192.168.0.4

SNIFFED [3] ++++++++++
Datagram SIZE:  1500
Protocol:       TCP
Source IP:      45.113.130.88
Destination IP: 192.168.0.4
```

---
### ICMP 스니퍼
---

```python
from socket import *
import os
import struct

def parse_ipheader(data):
    ipheader = struct.unpack('!BBHHHBBH4s4s', data[:20])
    return ipheader
    
def getProtocol(ipheader):
    protocols = {1:'ICMP', 6:'TCP', 17:'UDP'}
    proto = ipheader[6]
    if proto in protocols:
        return protocols[proto]
    else:
        return 'OTHERS'
        
def getIP(ipheader):
    src_ip = inet_ntoa(ipheader[8])
    dst_ip = inet_ntoa(ipheader[9])
    return (src_ip, dst_ip)
    
def getIPHedaerLen(ipheader):
    ipheaderlen = ipheader[0] & 0x0F
    ipheaderlen *= 4
    return ipheaderlen
    
def getTypeCode(icmp):
    icmpheader = struct.unpack('!BB', icmp[:2])
    icmp_type = icmpheader[0]
    icmp_code = icmpheader[1]
    return (icmp_type, icmp_code)
    
def recvData(sock):
    data = ''
    try:
        data = sock.recvfrom(65565)
    except timeout:
        data = ''
    return data[0]
    
def sniffing(host):
    if os.name == 'nt':
        sock_protocol = IPPROTO_IP
    else:
        sock_protocol = IPPROTO_ICMP
    sniffer = socket(AF_INET, SOCK_RAW, sock_protocol)
    sniffer.bind(('192.168.0.17', 0)) #IP 주소 입력 해주자
    sniffer.setsockopt(IPPROTO_IP, IP_HDRINCL, 1)
    if os.name == 'nt':
        sniffer.ioctl(SIO_RCVALL, RCVALL_ON)
    count = 1
    try:
        while True:
            data = recvData(sniffer)
            ipheader = parse_ipheader(data[:20])
            ipheaderlen = getIPHedaerLen(ipheader)
            protocol = getProtocol(ipheader)
            src_ip, dst_ip = getIP(ipheader)
            if protocol == 'ICMP': 								  #추가된 부분
                offset = ipheaderlen
                icmp_type, icmp_code = getTypeCode(data[offset:]) #IP헤더 바로 다음부터 ICMP헤더 시작
                print('%s -> %s: ICMP: Type[%d], Code[%d]'\
                      %(src_ip, dst_ip, icmp_type, icmp_code))
    except KeyboardInterrupt:
        if os.name == 'nt':
            sniffer.ioctl(SIO_RCVALL, RCVALL_OFF)
                                
def main():
    host = gethostbyname(gethostname())
    print('START SNIFFINT at for ICMP [%s]' %'192.168.0.17')
    sniffing(host)
if __name__ == '__main__':
    main()
```

```
C:\Users\authe\OneDrive\바탕 화면\Source>ping google.com

Ping google.com [172.217.31.174] 32바이트 데이터 사용:
172.217.31.174의 응답: 바이트=32 시간=32ms TTL=114
172.217.31.174의 응답: 바이트=32 시간=32ms TTL=114
172.217.31.174의 응답: 바이트=32 시간=32ms TTL=114
172.217.31.174의 응답: 바이트=32 시간=32ms TTL=114

172.217.31.174에 대한 Ping 통계:
    패킷: 보냄 = 4, 받음 = 4, 손실 = 0 (0% 손실),
왕복 시간(밀리초):
    최소 = 32ms, 최대 = 32ms, 평균 = 32ms
```

```
START SNIFFINT at for ICMP [192.168.0.17]
192.168.0.17 -> 172.217.31.174: ICMP: Type[8], Code[0]
192.168.0.17 -> 168.126.63.2: ICMP: Type[3], Code[3]
172.217.31.174 -> 192.168.0.17: ICMP: Type[0], Code[0]
192.168.0.17 -> 172.217.31.174: ICMP: Type[8], Code[0]
172.217.31.174 -> 192.168.0.17: ICMP: Type[0], Code[0]
192.168.0.17 -> 172.217.31.174: ICMP: Type[8], Code[0]
172.217.31.174 -> 192.168.0.17: ICMP: Type[0], Code[0]
192.168.0.17 -> 172.217.31.174: ICMP: Type[8], Code[0]
172.217.31.174 -> 192.168.0.17: ICMP: Type[0], Code[0]
```

---
### 호스트 스캐너
---

```
cd C:\Users\authe\AppData\Local\Programs\Python\Python38\Scripts
pip install netaddr
```

```python
from socket import *
from netaddr import IPNetwork, IPAddress

def sendMsg(subnet, msg):
    sock = socket(AF_INET, SOCK_DGRAM)   #UDP 소켓 생성
    for ip in IPNetwork(subnet):   
        try:
            print('SENDING MESSAGE to [%s]' %ip)
            sock.sendto(msg.encode('utf-8'), ('%s' %ip, 9000)) #서브넷의 모든 IP에 대해 9000번포트로 메시지 전송
                                                               #유니코드 메시지는 오류 발생하므로 UTF-8로 인코딩
        except Exception as e:
            print(e)

def main():
    #host = gethostbyname(gethostname())
    subnet = '192.168.0.17/24'
    msg = 'KNOCK!KNOCK!'
    sendMsg(subnet, msg)
```

```
**공유기의 경우 원하는 결과가 안나올 수 도 있음
SENDING MESSAGE to [192.168.0.0]
SENDING MESSAGE to [192.168.0.1]
SENDING MESSAGE to [192.168.0.2]
SENDING MESSAGE to [192.168.0.3]
SENDING MESSAGE to [192.168.0.4]
SENDING MESSAGE to [192.168.0.5]
...
```

---
#### ping을 이용한 호스트 스캐너
---

왜인지 모르겠지만 글씨가 깨지고 에러가 난다. 다음에 해보자.
{: .notice}

```python
import os
from socket import *
from netaddr import IPNetwork, IPAddress
from threading import Thread

def sendPing(ip):
    try:
        ret = os.system('ping -n 1 %s'%ip)
    except Exception as e:
        print(e)
    
def main():
    #host = gethostbyname(gethostname())
    #subnet = host + '/24'
    subnet = '192.168.0.17/24'
    for ip in IPNetwork(subnet):
        t = Thread(target=sendPing, args=(ip,))  #서브넷 ip 개수만큼 스레드를 구동하여 처리속도를 높임
        t.start()
    
if __name__ == '__main__':
    main()
```

```python
from socket import *
import os
import struct

def parse_ipheader(data):
    ipheader = struct.unpack('!BBHHHBBH4s4s', data[:20])
    return ipheader
    
def getProtocol(ipheader):
    protocols = {1:'ICMP', 6:'TCP', 17:'UDP'}
    proto = ipheader[6]
    if proto in protocols:
        return protocols[proto]
    else:
        return 'OTHERS'
        
def getIP(ipheader):
    src_ip = inet_ntoa(ipheader[8])
    dst_ip = inet_ntoa(ipheader[9])
    return (src_ip, dst_ip)
    
def getIPHedaerLen(ipheader):
    ipheaderlen = ipheader[0] & 0x0F
    ipheaderlen *= 4
    return ipheaderlen
    
def getTypeCode(icmp):
    icmpheader = struct.unpack('!BB', icmp[:2])
    icmp_type = icmpheader[0]
    icmp_code = icmpheader[1]
    return (icmp_type, icmp_code)
    
def recvData(sock):
    data = ''
    try:
        data = sock.recvfrom(65565)
    except timeout:
        data = ''
    return data[0]
    
def sniffing(host):
    if os.name == 'nt':
        sock_protocol = IPPROTO_IP
    else:
        sock_protocol = IPPROTO_ICMP
    sniffer = socket(AF_INET, SOCK_RAW, sock_protocol)
    sniffer.bind(('192.168.0.17', 0)) #IP 주소 입력 해주자
    sniffer.setsockopt(IPPROTO_IP, IP_HDRINCL, 1)
    if os.name == 'nt':
        sniffer.ioctl(SIO_RCVALL, RCVALL_ON)
    count = 1
    try:
        while True:
            data = recvData(sniffer)
            ipheader = parse_ipheader(data[:20])
            ipheaderlen = getIPHedaerLen(ipheader)
            protocol = getProtocol(ipheader)
            src_ip, dst_ip = getIP(ipheader)
            if protocol == 'ICMP':
                offset = ipheaderlen
                icmp_type, icmp_code = getTypeCode(data[offset:]) #IP헤더 바로 다음부터 ICMP헤더 시작
                if icmp_type ==0:
					print('HOST ALIVE: %s' %src_ip)
					
    except KeyboardInterrupt:
        if os.name == 'nt':
            sniffer.ioctl(SIO_RCVALL, RCVALL_OFF)
                                
def main():
    host = gethostbyname(gethostname())
    print('START SNIFFINT at for ICMP [%s]' %'192.168.0.17')
    sniffing(host)
if __name__ == '__main__':
    main()
```

---
### Scapy 모듈을 사용해보자
---

```
C:\Users\authe>cd C:\Users\authe\AppData\Local\Programs\Python\Python38\Scripts
C:\Users\authe\AppData\Local\Programs\Python\Python38\Scripts>cd scapy-master
C:\Users\authe\AppData\Local\Programs\Python\Python38\Scripts\scapy-master>cd scapy-master
C:\Users\authe\AppData\Local\Programs\Python\Python38\Scripts\scapy-master\scapy-master>python setup.py build
C:\Users\authe\AppData\Local\Programs\Python\Python38\Scripts\scapy-master\scapy-master>python setup.py install
```

```python
from scapy.all import *

def showpacket(packet):
    print(packet.show())
    
def main(filter):
    sniff(filter=filter, prn=showpacket, count=1)
    
if __name__ == '__main__':
    filter = 'ip'
    main(filter)
```
```
###[ Ethernet ]###
  dst       = 70:5d:cc:39:0c:7c
  src       = 7c:d3:0a:81:cb:1a
  type      = IPv4
###[ IP ]###
     version   = 4
     ihl       = 5
     tos       = 0xb8
     len       = 138
     id        = 37413
     flags     = DF
     frag      = 0
     ttl       = 128
     proto     = tcp
     chksum    = 0x0
     src       = 192.168.0.69
     dst       = 113.29.138.230
     \options   \
###[ TCP ]###
        sport     = 22338
        dport     = https
        seq       = 2258849478
        ack       = 1873317550
        dataofs   = 5
        reserved  = 0
        flags     = PA
        window    = 1024
        chksum    = 0x8b3e
        urgptr    = 0
        options   = []
###[ Raw ]###
           load      = '^\x00\x00\x00\xca\x0c\xc1\xe2\xaf\xe9\xcd\x1f\x1c{\xcf+\xfa\x13\xaa\xf5\xc4\xba\xed72!\x10\x05\xebfZ\xdb\x88\\}\x12,\xa5\x1c\xac~\xd8oT\x12\x8e!\xab\x94\x1b\x12\xe3\x9d\x06\x9e\xf2%\xf4\x00\x16+\xe3jL-\xf1M->\x0c\x19H\x9b\x8aA\x97\xd1\xe6\xce\x83\xbf\xf8%s"\x1f\xdf\x9bYc\xc9\x95\x85\xfbx\xff\x9b\x05'
```

```python
from scapy.all import *
import logging
logging.getLogger("scapy.runtime").setLevel(logging.ERROR)

protocols = {1:'ICMP', 6:'TCP', 17:'UDP'}

def showpacket(packet):
    src_ip = packet[0][1].src
    dst_ip = packet[0][1].dst
    proto = packet[0][1].proto
    if proto in protocols:
        print('PROTOCOL: %s: %s -> %s' %(protocols[proto], src_ip, dst_ip))

def main(filter): #랜카드가 여러개라 iface로 사용할 인터페이스 지정
    sniff(filter=filter, prn=showpacket, count=1, iface="Killer E2500 Gigabit Ethernet Controller")
    
if __name__ == '__main__':
    filter = 'ip'
    main(filter)
```

```
PROTOCOL: TCP: 192.168.0.69 -> 210.89.160.53
```

---
#### 메세지 가로채기
---

```python
from scapy.all import*  
  
def showPacket(packet):  
    data = '%s' %(paket[TCP].payload)  
    if 'user' in data.lower() or 'pass' in data.lower():  
        print ('+++[%s]: %s' %(packet[IP].dst, data))  
  
def sniffing(filter):  
    sniff(filter = filter, prn = showPacket, iface="Killer E2500 Gigabit Ethernet Controller", count = 0, store = 0)  
  
if __name__ == '__main__':  
    filter = 'tcp port 25 or tcp port 110 or tcp port 143 or tcp port 80'  
    sniffing(filter)  
```

---
### 스니핑 방어 대책
---

1. 패킷 스니퍼가 설치될 수 없도록 네트워크 미디어에 대한 물리적 접근 제한
2. 중요 정보에 대해 암호화 사용
3. ARP 스푸핑 방지를 위해 정적 IP와 ARP 테이블을 사용
4. ARP 캐시에 게이트웨이 영구 MAC 주소 설정
5. SSH나 SCP, SSL등과 같은 암호화 세션 사용
6. 브로드캐스트 기능 OFF
{: .notice}