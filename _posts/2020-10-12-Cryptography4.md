---
title: "Cryptography: 공개키 암호"
last_modified_at: 2020-10-05T00:26:02-05:00
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
### 공개키 암호
---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/내부보안/Crypto5.JPG" alt="">
  <figcaption></figcaption>
</figure>

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/내부보안/Crypto6.JPG" alt="">
  <figcaption></figcaption>
</figure>

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/내부보안/Crypto7.JPG" alt="">
  <figcaption></figcaption>
</figure>

---
### RSA 공개키 암호 구현
---

```python
from Crypto.PublicKey import RSA

def createPEM(): #개인키 생성
    private_key = RSA.generate(1024)
    f = open('mykey.pem', 'wb+')
    f.write(private_key.exportKey('PEM'))
    f.close()
    
def readPEM(): #mykey.pem 파일에 저장된 개인키를 읽어 리턴
    h = open('mykey.pem', 'r')
    key = RSA.importKey(h.read())
    h.close()
    return key

def rsa_enc(msg): #rsa_enc(msg)는 RSA 공개키로 메세지를 암호화 하는 함수입니다.
    private_key = readPEM()
    public_key = private_key.publickey()
    encdata = public_key.encrypt(msg, 32)
    return encdata

def rsa_dec(msg): #rsa_dec(msg)는 RSA 개인키로 메시지를 복호화 하는 함수입니다.
    private_key = readPEM()
    decdata = private_key.decrypt(msg)
    return decdata
    
if __name__ == '__main__':
    createPEM()
    msg = 'mint loves python'
    ciphered = rsa_enc(msg.encode('utf-8'))
    print(ciphered)
    deciphered = rsa_dec(ciphered)
    print(deciphered)
```

---
### RSA 공개키 서명 구현하기
---

```python
from Crypto.PublicKey import RSA
from Crypto.Hash import SHA256 as SHA

def readPEM():
    h = open('mykey.pem', 'r')
    key = RSA.importKey(h.read())
    h.close()
    return key

	#사용자의 개인키로 서명하는 측
def rsa_sign(msg):
    private_key = readPEM()
    public_key = private_key.publickey()
    hash = SHA.new(msg).digest()
    signature = private_key.sign(hash, '')
    return public_key, signature
	
	#사용자의 공개키로 서명을 확인하는 측
def rsa_verify(msg, public_key, signature):
    hash = SHA.new(msg).digest()
    if public_key.verify(hash, signature):
        print('VERIFIED')
    else:
        print('DENIED')
        
if __name__ == '__main__':
    msg = 'My name is mint'
    public_key, signature = rsa_sign(msg.encode('utf-8'))
    rsa_verify(msg.encode('utf-8'), public_key, signature)
```
