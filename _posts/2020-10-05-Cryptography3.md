---
title: "Cryptography: 대칭키 암호"
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

---
### 대칭키 암호
---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/내부보안/Crypto3.JPG" alt="">
  <figcaption></figcaption>
</figure>

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/내부보안/Crypto4.JPG" alt="">
  <figcaption></figcaption>
</figure>

---
### 메세지 암호화 구현하기
---

```
#설치경로, 관리자권한, pip 업데이트 확인할 것!
pip install pycryptodome
```

```python
from Crypto.Cipher import DES3
from Crypto.Hash import SHA256 as SHA

class myDES():
    def __init__(self, keytext, ivtext):
        hash = SHA.new()
        hash.update(keytext.encode('utf-8'))
        key = hash.digest()
        self.key = key[:24]
        
        hash.update(ivtext.encode('utf-8'))
        iv = hash.digest()
        self.iv = iv[:8]
        
    def enc(self, plaintext):
        des3 = DES3.new(self.key, DES3.MODE_CBC, self.iv)
        encmsg = des3.encrypt(plaintext.encode())
        return encmsg
        
    def dec(self, ciphertext):
        des3 = DES3.new(self.key, DES3.MODE_CBC, self.iv)
        decmsg = des3.decrypt(ciphertext)
        return decmsg
        
def main():
    keytext = 'mint'
    ivtext = '1234'
    msg = 'python35python35'
    
    myCipher = myDES(keytext, ivtext)
    ciphered = myCipher.enc(msg)
    deciphered = myCipher.dec(ciphered)
    
    print('ORIGINAL: \t%s' %msg)
    print('CIPHERED: \t%s' %ciphered)
    print('DECIPHERED: \t%s' %deciphered)
    
if __name__== '__main__':
    main()
```

```
ORIGINAL:       python35python35
CIPHERED:       b'\x1a\x8d8b\xa1\x9e\xcd~\xe1\x91\x0c"z\xbdm\x8d'
DECIPHERED:     b'python35python35'
```