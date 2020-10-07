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
### 메세지 암호화 구현하기 (DES3)
---

```
#설치경로, 관리자권한, pip 업데이트 확인할 것!
pip install pycryptodome
```

```python
from Crypto.Cipher import DES3 #3DES를 import 해준다.
from Crypto.Hash import SHA256 as SHA #SHA256을 통해 3DES의 암호키와 초기화 벡터를 만든다.

class myDES(): #클래스를 생성해준다
    def __init__(self, keytext, ivtext): #클래스 생성자 
    #키를 생성하기위한 텍스트로 keytext와 초기화 벡터를 생성하기위한 텍스트로 ivtext를 받는다.
    #keytext가 16바이트 길이라면 3DES가 지원하는 키 크기 이므로 바로 암호키로 활용할수도 있다.
    #하지만 키길이가 무엇이든간에 자동으로 3DES가 지원하는 길이로 변환된다면 더 편할 것이다.
    #이때, 사용하는것이 SHA 다.
        hash = SHA.new() #SHA 객체를 만들어 hash에 할당한다.
        hash.update(keytext.encode('utf-8')) #keytext를 받아 해시값을 갱신한다. 파이썬은 유니코드를 이용하기 때문에 utf-8로 변환해준다.
        key = hash.digest() #hash 값을 추출하여 key 값에 할당해준다.
        self.key = key[:24] #SHA256이 생성하는 키 크기는 32바이트(256비트) 크기다.
        #하지만 Crypto에서 지원하는 3DES 키크기는 16바이트 아니면 24바이트 크기 이므로 슬라이싱 해준다.
        
        hash.update(ivtext.encode('utf-8')) #마찬가지로 초기화 벡터에 사용할 키를 만들어준다.
        iv = hash.digest() #hash 값을 추출하여 iv 값에 할당해준다.
        self.iv = iv[:8] # 이 소스코드는 CBC 방식으로 암호화를 해줄 것이다. DES3 에서 지원하는 암호화블록 크기는 64비트 이므로 iv 크기도 64비트가 되어야한다.
        
    def enc(self, plaintext):
        des3 = DES3.new(self.key, DES3.MODE_CBC, self.iv) # 암호키, 운영모드, 초기화벡터를 받아서 DES3 객체를 생성한다. 
        encmsg = des3.encrypt(plaintext.encode()) #des3 객체를 이용하여 plaintext를 암호화 한다.
        return encmsg
        
    def dec(self, ciphertext):
        des3 = DES3.new(self.key, DES3.MODE_CBC, self.iv) # 암호키, 운영모드, 초기화벡터를 받아서 DES3 객체를 생성한다.
        decmsg = des3.decrypt(ciphertext)  #des3 객체를 이용하여 ciphertext를 복호화 한다.
        return decmsg
		
def make8String(msg):   #암호화할 문자열 바이트가 8의 배수가 아니어도 길이를 맞춰준다.
    msglen = len(msg)
    filler = ''
    if msglen%8 != 0:
        filler = '0'*(8 - msglen%8)
    msg+=filler
    return msg
	
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
ORIGINAL:       Step by step goes a long way
CIPHERED:       b'\xfa\x06\x84*K\xbf\xd7\xc8u\x9a\x9d\x87\xee\x01\x97\x90N\xfc\x83sB\x94\xd2s\xb7s\x9c\xde:\xb2`d'
DECIPHERED:     b'Step by step goes a long way0000' #나중에 추가된 0의 길이를 따로 저장해두고 잘라내도 되겠다.
```

---
### 메세지 암호화 구현하기 (AES)
---

```python
from Crypto.Cipher import AES #AES를 import 해준다.
from Crypto.Hash import SHA256 as SHA #SHA256을 통해 3DES의 암호키와 초기화 벡터를 만든다.

class myAES():
    def __init__(self, keytext, ivtext):
        hash = SHA.new()
        hash.update(keytext.encode('utf-8'))
        key = hash.digest()
        self.key = key[:16]
        
        hash.update(ivtext.encode('utf-8'))
        iv = hash.digest()
        self.iv = iv[:16]
        
    def makeEnabled(slef, plaintext):
        fillersize = 0
        textsize = len(plaintext)
        if textsize%16 !=0:
            fillersize = 16-textsize%16
            
        filler = '0'*fillersize
        header = '%d' %(fillersize)
        gap = 16-len(header)
        header += '#'*gap
        
        return header+plaintext+filler

    def enc(self, plaintext):
        plaintext = self.makeEnabled(plaintext)
        aes = AES.new(self.key, AES.MODE_CBC, self.iv)
        encmsg = aes.encrypt(plaintext.encode())
        return encmsg
       
    def dec(self, ciphertext):
        aes = AES.new(self.key, AES.MODE_CBC, self.iv)
        decmsg = aes.decrypt(ciphertext)
        
        header = decmsg[:16].decode()
        fillersize = int(header.split('#')[0])
        if fillersize !=0:
            decmsg = decmsg[16:-fillersize]
        else:
            decmsg = decmsg[16:]
        return decmsg

def main():
    keytext = 'mint'
    ivtext = '1234'
    msg = 'Step by step goes a long way'
    
    myCipher = myAES(keytext, ivtext)
    ciphered = myCipher.enc(msg)
    deciphered = myCipher.dec(ciphered)
    
    print('ORIGINAL: \t%s' %msg)
    print('CIPHERED: \t%s' %ciphered)
    print('DECIPHERED: \t%s' %deciphered)
    
if __name__== '__main__':
    main()
```

```
ORIGINAL:       Step by step goes a long way
CIPHERED:       b"\x99o\xae\xa2\xb6\x88~w\x1f\x87A\xaf\xb3hX\x1c\xd8\x0fEI\x00z\xdbv\x17\xbeshR\xc1\x85\xb9\xfb\xd4\x12\xd6\xb48'9\xc82\x9e\xc6Kg\xe4\xca"
DECIPHERED:     b'Step by step goes a long way'
```

---
### 메세지 암호화 구현하기 (스트림 암호 ARC4)
---

```python
from Crypto.Cipher import ARC4 #ARC4를 import 해준다. ARC4란 Alleged RC4를 의미한다.
from Crypto.Hash import SHA256 as SHA #SHA256을 통해 3DES의 암호키와 초기화 벡터를 만든다.

#스트림 암호는 보통 8비트 단위로 암호화를 수행하므로 암호 블록 크기는 1바이트다.
#운영 모드의 경우 ECB모드만 사용가능하여 초기화 벡터는 필요없다는 것이 특징이다.
#즉, 암호화 키만 정의되면 1문자 이상의 임의의 메세지에 대해 암호화, 복호화가 가능하다.

class myARC4():
    def __init__(self, keytext):
        self.key = keytext.encode()
        
    def enc(self, plaintext):
        arc4 = ARC4.new(self.key)
        encmsg = arc4.encrypt(plaintext.encode())
        return encmsg
        
    def dec(self, ciphertext):
        arc4 = ARC4.new(self.key)
        decmsg = arc4.decrypt(ciphertext)
        return decmsg
    
def main():
    keytext = 'mintmint'
    msg = 'Step by step goes a long way'
    
    myCipher = myARC4(keytext)
    ciphered = myCipher.enc(msg)
    deciphered = myCipher.dec(ciphered)
    
    print('ORIGINAL: \t%s' %msg)
    print('CIPHERED: \t%s' %ciphered)
    print('DECIPHERED: \t%s' %deciphered)
      
if __name__== '__main__':
    main()
```

```
ORIGINAL:       Step by step goes a long way
CIPHERED:       b'\xbeX\xda\xe1=\t\x9aXP\\\xca\xed\xae\xe3\xc6\x0c\x14h\xba\x98\x9fu\x9c$i\xa9\xdb\xc4'
DECIPHERED:     b'Step by step goes a long way'
```

---
### 데이터 무결성 검증 수행
---

```python
from Crypto.Hash import SHA256 as SHA
SIZE = 1024*256 #256kb 단위로 나누어서 해쉬 값을 구하는 것이 좋다. (컴퓨터 메모리가 크다면 상관없을 지라도...)

def getFileHash(filename):
    hash = SHA.new()
    h = open(filename, 'rb')
    content = h.read(SIZE)
    while content:
        hash.update(content) #sha를 통해서 파일 내용을 불러와 hash 값을 계산한다.
        hashval = hash.digest() #hash 값을 추출
        content = h.read(SIZE)
    h.close()
    return hashval
  
def hashCheck(file1, file2):
    hashval1 = getFileHash(file1)
    hashval2 = getFileHash(file2)
    if hashval1 == hashval2:	#두개 파일의 해쉬값을 불러와서 비교해본다. 
        print('Two Files are Same')
    else:
        print('Two Files are Different')
        
def main():
    file1 = 'file1.txt'
    file2 = 'file2.txt'
    hashCheck(file1, file2)
    
if __name__ == '__main__':
    main()
```

```
Two Files are Same
```
```
Two Files are Different
```
