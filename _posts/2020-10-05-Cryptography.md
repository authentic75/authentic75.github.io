---
title: "Cryptography: 암호란 무엇일까?"
last_modified_at: 2020-09-21T00:26:02-05:00
categories:
  - Cryptography
tags:
  - 암호학
  - 파이썬
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

필자는 '화이트 해커를 위한 암호와 해킹(1판)' 이라는 책을 참고하여 공부한 내용을 정리하고자 한다. 
책이 1판과 2판이 있길래 1판부터 공부하고 2판을 사야겠다고 생각하고 구매했는데 내용이 이어지는 1판 2판이 아니라 2판이 개정판이었던 것이다!!!
어쩐지 2판만 품절이라더니 조사 좀 하고 구매할 걸 그랬다. 허허허.
{: .notice}

---
### 암호와 코드 기초 용어
---


* **암호란?**
* 평문(Plain Text): 누구나 읽을 수 있고 알아볼 수 있는 문장
* 암호문(Cipher Text): 푸는 방법을 모르는 사람에게 전혀 알 수 없는 형태로 변환된 문장
* 암호키(Cipher Key): 암호문을 평문으로 해독하기 위해 사용된 방법, Key 라고도 부른다
* 암호화(Encryption): 평문을 암호문으로 변환하는 과정
* 복호화(Decryption): 암호문을 평문으로 변환하는 과정
* 해시값(Hash Value): 해시함수를 통해 변환한 값으로 복호화가 되지 않는 것
{: .notice--info}

* **코드란?**
* 대중에게 변환 규칙을 공개하여 어느 누구나 해독할 수 있도록 만들어 놓은 기호 또는 표시 방법을 말한다
* 모스 부호, ASCII, 유니코드 등
* 디코딩, 인코딩 이라는 용어를 사용한다
{: .notice}

---
### 암호 도구를 구현해보자
---

이제 기본 용어를 익혔으니 바로 파이썬을 통해 암호 도구를 구현해보자. 구현할 기능을 다음과 같다.
{: .notice}

* 암호화 기능: 알파벳으로 이루어진 문장을 입력 받으면 모든 문자를 코드북을 이용하여 암호화한다. 단 코드북에 존재하지 않는 문자는 그대로 출력한다.
* 복호화 기능: 암호화한 문장을 입력 받으면 코드북을 이용해 복호화 한다. 단 코드북에 존재하지 않을 시 그대로 출력한다.
* 이와 같은 방식은 매우 취약하다. 노출되는 문자를 기반으로 어느정도 내용을 유추할 수 있기 때문이다.
{: .notice}

```python
def makeCodebook(): #사전형 Data로 decbook을 정의한다. 멤버는 암호문자:평문문자
    decbook = {'5':'a', '2':'b', '#':'d',  '8':'e',  '1':'f', '3':'g', \
          '4':'h',  '6':'i', '0':'l', '9':'m', '*':'n', \
          '%':'o', '=':'p', '(':'r', ')':'s', ';':'t', '?':'u', \
           '@':'v',  ':':'y',  '7':' '}
    encbook = {}
    for k in decbook: #decbook와는 반대로 평문문자:암호문 문자 로 저장한다.
        val = decbook[k]
        encbook[val] = k
   
    return encbook, decbook

def encrypt(msg, encbook):
    for c in msg: #for문을 통해 한 문자씩 불러와 코드북을 참고하여 문자열 내 모든 해당 문자를 치환한다.
        if c in encbook:
            msg = msg.replace(c, encbook[c])
    return msg

def decrypt(msg, decbook):
    for c in msg: #for문을 통해 한 문자씩 불러와 코드북을 참고하여 문자열 내 모든 해당 문자를 치환한다.
        if c in decbook:
            msg = msg.replace(c, decbook[c])
    return msg

if __name__ == '__main__': #__main__ 부터 실행하도록 다음과 같이 명시해줄 수 있다.
#파이썬의  경우 C나 C++과 다르게 파일마다 __main__이 존재 할 수 있다.
    plaintext = 'A flower cannot blossom without sunshine, and man cannot live without love.'
    encbook, decbook = makeCodebook() #encbook과 decbook를 생성한다.
    ciphertext = encrypt(plaintext, encbook) #encrypt 함수를 이용해 평문을 암호문으로 변환한다.
    print(ciphertext)
    
    deciphertext = decrypt(ciphertext, decbook) #decrypt 함수를 이용해 암호문을 평문으로 변환한다.
    print(deciphertext)
```

```
A710%w8(7c5**%;720%))%97w6;4%?;7)?*)46*8,75*#795*7c5**%;706@87w6;4%?;70%@8.
A flower cannot blossom without sunshine, and man cannot live without love.
```

---
#### 파일 입출력을 이용해보자
---

```python
if __name__ == '__main__': #__main__ 부터 실행하도록 다음과 같이 명시해줄 수 있다.
#파이썬의  경우 C나 C++과 다르게 파일마다 __main__이 존재 할 수 있다.
    h = open('plain.txt', 'rt')
    plaintext = h.read()
    h.close()
    
    encbook, decbook = makeCodebook() #encbook과 decbook를 생성한다.
    ciphertext = encrypt(plaintext, encbook) #encrypt 함수를 이용해 평문을 암호문으로 변환한다.
    
    h = open('encryption.txt', 'wt+')
    h.write(ciphertext)
    h.close()
```

```
I hope that a study of very long sentences will arm you with 
strategies that are almost as diverse as the sentences themselves, 
such as: starting each clause with the same word, tilting with dependent
clauses toward a revelation at the end, padding with parentheticals,
showing great latitude toward standard punctuation, rabbit-trailing away from
the initial subject, encapsulating an entire life, and lastly, as this sentence is, 
celebrating the list.
```

```
I74%=87;45;757);?#:7%17@8(:70%*37)8*;8*c8)7w60075(97:%?7w6;47);(5;8368)7;45;
75(87509%);75)7#6@8()875)7;487)8*;8*c8)7;489)80@8),7)?c475):7);5(;6*3785c47c05?)
87w6;47;487)5987w%(#,7;60;6*37w6;47#8=8*#8*;7c05?)8)7;%w5(#757(8@805;6%*75;7;487
8*#,7=5##6*37w6;47=5(8*;48;6c50),7)4%w6*373(85;705;6;?#87;%w5(#7);5*#5(#7=?*c;?5
;6%*,7(5226;-;(5606*375w5:71(%97;4876*6;6507)?2j8c;,78*c5=)?05;6*375*78*;6(870618,
75*#705);0:,75)7;46)7)8*;8*c876),7c8082(5;6*37;48706);.
```


