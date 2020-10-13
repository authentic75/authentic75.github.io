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
    if os.name == 'nt':       #윈도우인 경우
        sock_protocol = IPPROTO_IP
    else:
        sock_protocol = IPPROTO_ICMP 
    sniffer = socket(AF_INET, SOCK_RAW, sock_protocol)
    sniffer.bind((host, 0))
    sniffer.setsockopt(IPPROTO_IP, IP_HDRINCL, 1)
    if os.name == 'nt':    #윈도우인 경우 promiscuous 모드로 변경하여 모든 패킷 수신함
        sniffer.ioctl(SIO_RCVALL, RCVALL_ON)
    packet = sniffer.recvfrom(65565)      #소켓으로 패킷이 들어올때까지 대기함 65565바이트는 버퍼크기를 의미
    print(packet)
    if os.name == 'nt':
        sniffer.ioctl(SIO_RCVALL, RCVALL_OFF) #윈도우인 경우 이전에 설정했던
                                                       #promiscuous모드를 해제함
def main():
    host = gethostbyname(gethostname())    #Ipv4(현재호스트 이름). 해당 컴퓨터 IP주소 담김
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
def recvData(sock):
    data = ''
    try:
        data = sock.recvfrom(65565)
    except timeout:
        data = ''
    return data[0]    #첫번째 멤버인 바이트 코드에 IP헤더가 포함됨
    
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
    try:
        while True:
            data = recvData(sniffer)
            print('SNIFFED [%d] %s' %(count, data[:20]))  #처음 20바이트가 IP헤더 부분
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
from socket import *
import os
import struct
def parse_ipheader(data):
    ipheader = struct.unpack('!BBHHHBBH4s4s', data[:20])
    return ipheader
def getDatagramSize(ipheader):
    return ipheader[2]
def getProtocol(ipheader):
    protocols = {1:'ICMP', 6:'TCP', 17:'UDP'}
    proto = ipheader[6]
    if proto in protocols:
        return protocols[proto]
    else:
        return 'OTHERS'
def getIP(ipheader):
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