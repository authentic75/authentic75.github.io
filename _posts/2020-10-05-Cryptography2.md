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

def parseKey(key):
    tmp = []
    key = key.upper()
    
    for i, k in enumerate(key):
        tmp.append((i, k))
        
    tmp = sorted(tmp, key=lambda x:x[1])
    
    enc_table = {}
    dec_table = {}
    
    for i, r in enumerate(tmp):
        enc_table[r[0]] = i
        dec_table[i] = r[0]
    
    return enc_table, dec_table

def transposition(msg, key, mode):
    msgsize = len(msg)
    keysize = len(key)
    ret = ''
    
    filler = ''
    if msgsize%keysize != 0:
        filler = '0'*(keysize - msgsize%keysize)
        
    msg = msg.upper()
    msg += filler
    
    enc_table, dec_table = parseKey(key)
    
    if mode == ENC:
        table = enc_table
    else:
        table = dec_table
    
    if mode == ENC:
        buf = ['']*keysize
        for i, c in enumerate(msg):
            col = i%keysize
            index = table[col]
            buf[index] += c
            
        for text in buf:
            ret += text
    else:
        blocksize = int(msgsize/keysize)
        buf = ['']*keysize
        pos = 0
        for i in range(keysize):
            text = msg[pos:pos+blocksize]
            index = table[i]
            buf[index] += text
            pos += blocksize
            
        for i in range(blocksize):
            for j in range(keysize):
                if buf[j][i] != '0':
                    ret += buf[j][i]
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