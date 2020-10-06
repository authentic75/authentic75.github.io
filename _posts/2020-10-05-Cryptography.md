---
title: "Cryptography: 암호란 무엇일까?(고전암호)"
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
### 르그랑의 키드 암호
---

르그랑은 미국의 한 소설에 등장하는 인물로 암호를 해독하여 보물을 찾아냈다고 한다.  
르그랑의 키드 암호문은 고전 암호로 너무 간단하여 지금은 사용하지 않는 알고리즘이라고 한다.  
파이썬을 통해 암호 도구를 구현해보자. 구현할 기능을 다음과 같다.  
{: .notice}

* 암호화 기능: 알파벳으로 이루어진 문장을 입력 받으면 모든 문자를 코드북을 이용하여 암호화한다. 단 코드북에 존재하지 않는 문자는 그대로 출력한다.
* 복호화 기능: 암호화한 문장을 입력 받으면 코드북을 이용해 복호화 한다. 단 코드북에 존재하지 않을 시 그대로 출력한다.
* 이와 같은 방식은 매우 취약하다. 노출되는 문자를 기반으로 어느정도 내용을 유추할 수 있기 때문이다.
{: .notice--info}

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
    h = open('plain.txt', 'rt')#파일을 읽는다.
    plaintext = h.read()
    h.close()
    
    encbook, decbook = makeCodebook() #encbook과 decbook를 생성한다.
    ciphertext = encrypt(plaintext, encbook) #encrypt 함수를 이용해 평문을 암호문으로 변환한다.
    
    h = open('encryption.txt', 'wt+')#파일을 생성한 후 쓰기모드로 연다.
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

---
### 카이사르의 시저 암호
---

로마황제 율리우스 카이사르가 가족이나 친분이 있는 사람에게 편지를 쓸때 사용했던 암호문에서 유래했다고 한다.  
알파벳을 일정한 수만큼 뒤에 있는 알파벳으로 변환하는 방식으로 암호 디스크를 이용한다.  
암호디스크는 레옴 바리스타 알베르티라는 건축가가 만든 것으로 알파벳과 0~9 숫자도 포함하고 있다.  
{: .notice--info}

문자 인덱스를 i라고 하고 암호화에 사용하는 일정한 수를 k라고 한다면 카이사르의 암호문은 다음 식을 만족한다.
{: .notice}
```
Enc(i) = (i+k) mod 26
```

이를 참조하여 암호화 도구를 작성해보자
{: .notice}

```python
ENC = 0 #ENC와 DEC Mode를 나타내기 위해 전역변수로 표시
DEC = 1

def makeDisk(key): #알파벳 하나를 입력받는다.
    keytable = map(lambda x:(chr(x+65), x), range(26))
    #람다 함수에서 x는 0~25 까지의 숫자를 의미하고 chr을 통해 0~25 + 65 인 아스키 값을 계산한다 (A~Z)
    #결과로 ('A', 0), ('B', 1) ... 같은 튜플들이 리스트로 생성된다.
    key2index = {}

    for t in keytable:
        alphabet, index = t[0], t[1] #튜플의 내용이 각각 알파벳과 인덱스로 들어간다.
        key2index[alphabet] = index #그리고 튜플의 내용을 기준으로 사전형 자료를 만든다.

    if key in key2index: #매개변수로 받은 Key가 사전형 자료에 있는지 확인하고
        k = key2index[key] #인덱스를 구한다.
    else:
        return None, None #만약 입력받은 Key가 없는 경우 None을 리턴하고 종료한다.

    enc_disk = {}
    dec_disk = {}

    for i in range(26):
        enc_i = (i+k)%26 #입력받은 키값의 인덱스와 알파벳을 더한다
        enc_ascii = enc_i + 65 #암호화에 이용할 알파벳을 대문자로 바꾼다
        enc_disk[chr(i+65)] = chr(enc_ascii) #(평문 알파벳:암호화될 알파벳) 형태로 디스크 생성
        dec_disk[chr(enc_ascii)] = chr(i+65) #복호화 디스크 생성 (암호화된 알파벳: 평문 알파벳) 

    return enc_disk, dec_disk


def caesar(msg, key, mode): #메세지와 키를 입력받고 암호화할지 복호화 할지 mode를 통해 정한다
    ret = '' #결과가 저장될 빈 문자열
    
    msg = msg.upper() #내용을 대문자로 변환 
    enc_disk, dec_disk = makeDisk(key) #disk 생성
    
    if enc_disk is None: #결과가 None이면 빈문자열을 출력한다
        return ret
        
    if mode is ENC: #암호화 모드일 경우 disk에 암호화 디스크를 넣어준다
        disk = enc_disk
    if mode is DEC: #복호화 모드일 경우 disk에 복호화 디스크를 넣어준다
        disk = dec_disk
    
    for c in msg:
        if c in disk: #msg에 있는 문자를 순서대로 disk와 대조하여 변환한 결과를 ret에 저장해준다. 
            ret += disk[c]
        else: #만약 없으면 그대로 저장한다.
            ret += c
    
    return ret

def main():
    plaintext = 'You will never know untill you try.' #평문
    key = 'F' #암호문 생성에 사용할 키 값을 입력해준다.
    
    print('Original:\t%s' %plaintext.upper()) #스트링포맷을 사용하여 문자열 출력(대문자로)
    ciphertext = caesar(plaintext, key, ENC) #암호화한 결과를 저장
    print('Caesar Cipher: \t%s' %ciphertext) #암호화한 결과 출력 
    deciphertext = caesar(ciphertext, key, DEC) #복호화한 결과 저장 
    print('Deciphered: \t%s' %deciphertext)#복호화한 결과 출력
    
if __name__=='__main__':
    main()
```

```
Original:       YOU WILL NEVER KNOW UNTILL YOU TRY.
Caesar Cipher:  DTZ BNQQ SJAJW PSTB ZSYNQQ DTZ YWD.
Deciphered:     YOU WILL NEVER KNOW UNTILL YOU TRY.
```

---
#### lambda와 map 함수
---

이번 암호 도구를 작성하면서 lambda 함수를 필자는 처음 보았다. lambda 함수는 함수이름 없이 한줄로 구성하는 함수를 뜻하는데 아래와 같은 형태로 사용한다.
{: .notice}

```
lambda 인자, 인자: 식
```

```
>>> f = lambda x: x+x
>>> f(2)
4
>>> f(3)
6
```

map 함수의 경우 반복가능한 자료형을 입력받아서 반복동작 하는 함수라고 보면 되겠다.
{: .notice--info}

```
map(함수, 반복가능한 자료)
```
간단한 예를 들자면, 
{: .notice}

```
results = map(lambda x: x+x, [0,1,2,3])
print(list(results))
```
리스트의 각 멤버를 람다의 인자로 받아서 실행한다. 그럼 결과는 아래와 같이 된다.
{: .notice}

```
[0,2,4,6]
```

그럼 다시 아래 부분을 살펴보자.
{: .notice}

```
keytable = map(lambda x:(chr(x+65), x), range(26))
```

0~25의 숫자를 인자로 (아스키 코드를 계산한 후 대문자 알파벳으로 나타냄, 숫자) 와 같은 튜플로 만들어 내는 것이다.
{: .notice}

---
### 아핀 암호
---

카이사르 암호는 26개의 키를 가지고 반드시 암호문은 해독할 수 있다. 따라서 변형으로 암호키의 개수를 늘린것이 아핀 암호방법이다. 아핀 암호는 k1과 k2를 사용한다.
{: .notice}

알아둬야한 점은 k1에는 26과 서로소인 수만 올 수 있다. {1,3,5,7,9,11,15,17,19,21,23,25}  
시저 암호의 키 개수가 26개면 12배 많은 경우의 수를 가진 셈이다. 즉 312가지를 시도하면 무조건 풀 수 있다.
{: .notice--info}

```
Enc(i) = (k1*i+k2) mod 26
```

```python
ENC = 0 #ENC와 DEC Mode를 나타내기 위해 전역변수로 표시
DEC = 1

def makeDisk(k1,k2): #두개의 키를 입력받는다.
    enc_disk = {}
    dec_disk = {}
    
    for i in range(26):
        enc_i = (k1*i+k2)%26 #입력받은 두개의 키값을 이용해 아스키코드를 구한다
        enc_ascii = enc_i + 65 #암호화에 이용할 알파벳을 대문자로 바꾼다
        enc_disk[chr(i+65)] = chr(enc_ascii) #(평문 알파벳:암호화될 알파벳) 형태로 디스크 생성
        dec_disk[chr(enc_ascii)] = chr(i+65) #복호화 디스크 생성 (암호화된 알파벳: 평문 알파벳) 

    return enc_disk, dec_disk


def affine(msg, key1, key2, mode): #메세지와 키를 입력받고 암호화할지 복호화 할지 mode를 통해 정한다
    ret = '' #결과가 저장될 빈 문자열

    msg = msg.upper() #내용을 대문자로 변환 
    enc_disk, dec_disk = makeDisk(key1, key2) #disk 생성

    if mode is ENC: #암호화 모드일 경우 disk에 암호화 디스크를 넣어준다
        disk = enc_disk
    if mode is DEC: #복호화 모드일 경우 disk에 복호화 디스크를 넣어준다
        disk = dec_disk
    
    for c in msg:
        if c in disk: #msg에 있는 문자를 순서대로 disk와 대조하여 변환한 결과를 ret에 저장해준다. 
            ret += disk[c]
        else: #만약 없으면 그대로 저장한다.
            ret += c
    
    return ret


def main():
    k1, k2 = 3, 5
    msg = 'You will never know untill you try.' #평문

    print('Original:\t%s' %msg.upper()) #스트링포맷을 사용하여 문자열 출력(대문자로)
    ciphertext = affine(msg, k1, k2, ENC) #암호화한 결과를 저장
    print('Affine Cipher: \t%s' %ciphertext) #암호화한 결과 출력 
    deciphertext = affine(ciphertext, k1, k2, DEC) #복호화한 결과 저장 
    print('Deciphered: \t%s' %deciphertext)#복호화한 결과 출력
    
if __name__=='__main__':
    main()
```

```
Original:       YOU WILL NEVER KNOW UNTILL YOU TRY.
Affine Cipher:  ZVN TDMM SRQRE JSVT NSKDMM ZVN KEZ.
Deciphered:     YOU WILL NEVER KNOW UNTILL YOU TRY.
```