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
### 스니핑
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