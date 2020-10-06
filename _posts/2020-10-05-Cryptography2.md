---
title: "Cryptography: 전치 암호"
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
### 전치 암호
---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/내부보안/Crypto1.JPG" alt="">
  <figcaption></figcaption>
</figure>

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/내부보안/Crypto2.JPG" alt="">
  <figcaption></figcaption>
</figure>


```python
ENC = 0
DEC = 1

def parseKey(key):#enc_table과 dec_table을 생성한다
    tmp = []
    key = key.upper() #키값을 입력받아 대문자로 변환
    
    for i, k in enumerate(key): #키값에서 문자를 차례대로 i와 k에 받아서
        tmp.append((i, k)) #tmp에 튜플로 추가한다. (0,B), (1,R), (2,A)...
        
    tmp = sorted(tmp, key=lambda x:x[1]) #[(2, 'A'), (0, 'B'), (3, 'I'), (4, 'N'), (1, 'R')]
    enc_table = {}
    dec_table = {}
    
    for i, r in enumerate(tmp): #enc_table dec_table
        enc_table[r[0]] = i #{2: 0, 0: 1, 3: 2, 4: 3, 1: 4} 20341을 인덱스에 저장하면서 키값은 연속된 i 저장
        dec_table[i] = r[0] #{0: 2, 1: 0, 2: 3, 3: 4, 4: 1} 20341을 키값으로 저장하면서 연속된 i 값을 인덱스로
    return enc_table, dec_table

def transposition(msg, key, mode):
    msgsize = len(msg) # 블록을 생성하기 위해 평문길이와 키 길이를 구한다
    keysize = len(key) 
    ret = ''
    
    filler = ''
    if msgsize%keysize != 0:
        filler = '0'*(keysize - msgsize%keysize) #평문길이를 key길이의 배수로 만드는 과정
        
    msg = msg.upper()
    msg += filler
    
    enc_table, dec_table = parseKey(key) #enc_table, dec_table 초기화
    
    if mode == ENC:  #암호화할지 복호화할 지 선택
        table = enc_table
    else:
        table = dec_table
    
    if mode == ENC:         #암호화 과정
        buf = ['']*keysize
        for i, c in enumerate(msg):
            col = i%keysize  #몇번째 블록에 들어갈지 정한다. (모듈로 연산후 나머지)
            index = table[col] 
            buf[index] += c #buf의 정해진 index에 한문자씩 더한다.
            
        for text in buf:
            ret += text
    else:
        blocksize = int(msgsize/keysize)
        buf = ['']*keysize
        pos = 0
        for i in range(keysize):
            text = msg[pos:pos+blocksize] #msg를 블록사이즈 만큼 자른다
            index = table[i] #테이블이 1번 인덱스의 키값 저장
            buf[index] += text #해당 키값이 가르키는 위치에 블록을 저장
            pos += blocksize #다음블록으로 넘어감
            
        for i in range(blocksize):
            for j in range(keysize):
                if buf[j][i] != '0':
                    ret += buf[j][i]  #buf에 저장된 값을 차례대로 불러온다
    return ret
    
def main():
    key = 'BRAIN'
    msg = 'Step by step goes a long way'
    print('Original:\t%s' %msg.upper())
    
    ciphertext = transposition(msg, key, ENC)
    print('Ciphered:\t%s' %ciphertext)
    
    deciphertext = transposition(ciphertext, key, DEC)
    print('Deciphered:\t%s' %deciphertext)
    
    print('\n')
    
    msg = 'STEPBYSTEPGOESALONGWAY'
    print('Original:\t%s' %msg.upper())
    
    ciphertext = transposition(msg, key, ENC)
    print('Ciphered:\t%s' %ciphertext)
    
    deciphertext = transposition(ciphertext, key, DEC)
    print('Deciphered:\t%s' %deciphertext)
    
if __name__ == '__main__':
    main()
```
```
Original:       STEP BY STEP GOES A LONG WAY
Ciphered:       E   NYSBEELWPSGAG0 TO  0TYPSOA
Deciphered:     STEP BY STEP GOES A LONG WAY


Original:       STEPBYSTEPGOESALONGWAY
Ciphered:       ETEN0SYGLAPESG0BPAW0TSOOY
Deciphered:     STEPBYSTEPGOESALONGWAY
```