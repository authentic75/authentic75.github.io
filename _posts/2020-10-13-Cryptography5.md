---
title: "Cryptography: 암호문 단독 공격, 알려진 평문 공격"
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
### 암호문 단독 공격
---

```python
def makeDisk(k): #키를 입력받아 디스크를 생성한다.
    dec_disk={}
    for i in range(26):
        alp = (i+k)%26 + 65
        dec_disk[chr(alp)] = chr(i+65)
    return dec_disk

def caesar(msg, key):
    ret = ''
    msg = msg.upper() 	 #암호문을 대문자로 변환
    disk = makeDisk(key) #디스크 생성
    for c in msg:		 #디스크와 msg 한글자씩 비교
        if c in disk:		
            ret += disk[c]  #디스크에 해당 글자가 있으면 ret에 복호화된값 입력
        else:
            ret += c		#글자가 없으면 암호문 글자 그대로 입력
    return ret

def attack(msg):
    for key in range(1, 26):	#A~Z의 경우의 수 모두 시도
        decmsg = caesar(msg, key) #복호화된 값
        print('SHIFT[%d]: %s' %(key, decmsg)) #출력

if __name__ == '__main__':
    msg = 'UGAMKZMBSMGQAVCUJMZBPZMMNQDMWVMBPZMM'
    attack(msg)
```

```
SHIFT[1]: TFZLJYLARLFPZUBTILYAOYLLMPCLVULAOYLL
SHIFT[2]: SEYKIXKZQKEOYTASHKXZNXKKLOBKUTKZNXKK
SHIFT[3]: RDXJHWJYPJDNXSZRGJWYMWJJKNAJTSJYMWJJ
SHIFT[4]: QCWIGVIXOICMWRYQFIVXLVIIJMZISRIXLVII
SHIFT[5]: PBVHFUHWNHBLVQXPEHUWKUHHILYHRQHWKUHH
SHIFT[6]: OAUGETGVMGAKUPWODGTVJTGGHKXGQPGVJTGG
SHIFT[7]: NZTFDSFULFZJTOVNCFSUISFFGJWFPOFUISFF
* SHIFT[8]: MYSECRETKEYISNUMBERTHREEFIVEONETHREE
SHIFT[9]: LXRDBQDSJDXHRMTLADQSGQDDEHUDNMDSGQDD
SHIFT[10]: KWQCAPCRICWGQLSKZCPRFPCCDGTCMLCRFPCC
SHIFT[11]: JVPBZOBQHBVFPKRJYBOQEOBBCFSBLKBQEOBB
SHIFT[12]: IUOAYNAPGAUEOJQIXANPDNAABERAKJAPDNAA
SHIFT[13]: HTNZXMZOFZTDNIPHWZMOCMZZADQZJIZOCMZZ
SHIFT[14]: GSMYWLYNEYSCMHOGVYLNBLYYZCPYIHYNBLYY
SHIFT[15]: FRLXVKXMDXRBLGNFUXKMAKXXYBOXHGXMAKXX
SHIFT[16]: EQKWUJWLCWQAKFMETWJLZJWWXANWGFWLZJWW
SHIFT[17]: DPJVTIVKBVPZJELDSVIKYIVVWZMVFEVKYIVV
SHIFT[18]: COIUSHUJAUOYIDKCRUHJXHUUVYLUEDUJXHUU
SHIFT[19]: BNHTRGTIZTNXHCJBQTGIWGTTUXKTDCTIWGTT
SHIFT[20]: AMGSQFSHYSMWGBIAPSFHVFSSTWJSCBSHVFSS
SHIFT[21]: ZLFRPERGXRLVFAHZOREGUERRSVIRBARGUERR
SHIFT[22]: YKEQODQFWQKUEZGYNQDFTDQQRUHQAZQFTDQQ
SHIFT[23]: XJDPNCPEVPJTDYFXMPCESCPPQTGPZYPESCPP
SHIFT[24]: WICOMBODUOISCXEWLOBDRBOOPSFOYXODRBOO
SHIFT[25]: VHBNLANCTNHRBWDVKNACQANNORENXWNCQANN
```

---
### 빈도수 공격
---

```python
def frequency_analysis(msg):
    fa = {}     #키는 문자, 값은 빈도수를 의미
    for c in msg:
        if c in fa:
            fa[c] += 1
        else:
            fa[c] = 1
    print(sorted(fa.items(), key=lambda x:x[1], reverse=True))

if __name__ == '__main__':
    msg = """53%%#305))6*;4826)4%=')4%);806;48#8@60'))85;1%(;;-%*8#83(88)5*#;46(;88*96
*?;8)*%(;485); 5*#2:*%(;4956*2(5*c4)8@8*;4069285);)6#8)4%%;1(%9;48081;8:8%1;48#85;4')-48
5#528806*81(%9;48;(88;4(%?34;484%;161;:188;%?;"""
    frequency_analysis(msg)
```

```
[('8', 33), (';', 27), ('4', 19), ('%', 16), (')', 15), ('5', 12), 
('*', 12), ('6', 11), ('(', 10), ('#', 8), ('1', 8), ('0', 6), 
('2', 5), ('9', 5), ('3', 4), ("'", 3), ('?', 3), (':', 3), 
('@', 2), ('-', 2), ('\n', 2), ('=', 1), (' ', 1), ('c', 1)]
```

---
### 알려진 평문 공격
---
`출처 해시넷`

```python
msg = '53%%#305))6*;4826)4%=\')4%);806*;48#8@60\'))85;1%(;:-%*8#83(88)5*#;46(;88*96*?;8)*%(;485);5*#2:*%(;4956*2(5*c4)8@8*;4069285);)6#8)4%%;1(%9;48081;8:8%1;48#85;4\')-485#528806*81(%9;48;(88;4(%?34;48)4%;161;:188;%?;'

# 알려진 평문이 있을 경우 사용 가능
# 이 암호문 중에 goodglass, mainbranch 단어가 있는 것을 알 때,

# 패턴을 비교
# 즉, 문자열을 선형으로 넘버링해서 비교(선형화 -> 선형공격)
# 실제 정규표현식 내부가 이런 식으로 구성

def make(hint):
    tmp = {}    # 순서를 저장
    res = 
    index = 0

    for c in hint:
        if c in tmp:
            res += tmp[c]
        else:
            # 없으면
            tmp[c] = str(index)
            # 단어에 대한 인덱스를 딕셔너리에 넣기

            res += str(index)
            # 리스트에 단어에 대칭하는 인덱스를 추가

            index += 1

    return res
    #return ':'.join(res)   # 리스트를 :로 구분지어 문자열로 반환

def find_pattern(msg, hint):
    pattern = make(hint)    # 선형화
    psize = len(hint)       # 선형화한 문자열의 길이
    pos = 0     # 암호문내에서 비교할 시작인덱스

    while True:
        data = msg[pos:pos+psize]
        # 시작점부터 문자열의 길이까지

        if len(data) < psize:
            # 길면 나가기
            break

        d_pattern = make(data)
        # data에 대한 패턴     

        if d_pattern == pattern:
            return data
        pos += 1

def dec(msg, key):
    pmsg =

    for c in msg:
        if c in key:
            c = key[c]

        pmsg = pmsg + c

    return pmsg

def main():
    print('---------------------------- 암호문 ------------------------------')

    print(msg)
    print()

    key = {}

    hint1 = 'goodgress'
    hint2 = 'mainbranch'

    result1 = make(hint1)
    print('1) 알려진 평문[goodgress]의 패턴 : {}'.format(result1))

    data = find_pattern(msg, hint1)

    index = 0
    for k in data:
        key[k] = hint1[index]
        index += 1

    print('2) 알려진 평문의 알파벳')
    print(' [+] {}\n'.format(key))

    result2 = make(hint2)
    print('3) 알려진 평문[mainbranch]의 패턴 : {}'.format(result2))

    data = find_pattern(msg, hint2)

    index = 0
    for k in data:
        key[k] = hint2[index]
        index += 1

    print('4) 알려진 평문의 알파벳')
    print(' [+] {}'.format(key))

    pmsg = dec(msg, key)

    print('-------------------- 찾은 알파벳으로 복호화 ----------------------')
    print(pmsg)

if __name__ == '__main__':
    main()
```

```
---------------------------- 암호문 ------------------------------
53%%#305))6*;4826)4%=')4%);806*;48#8@60'))85;1%(;:-%*8#83(88)5*#;
46(;88*96*?;8)*%(;485);5*#2:*%(;4956*2(5*c4)8@8*;4069285);)6#8)4%%;
1(%9;48081;8:8%1;48#85;4')-485#528806*81(%9;48;(88;4(%?34;48)4%;
161;:188;%?;

1) 알려진 평문[goodgress]의 패턴 : 011203455
2) 알려진 평문의 알파벳
 [+] {'3': 'g', '%': 'o', '#': 'd', '0': 'r', '5': 'e', ')': 's'}

3) 알려진 평문[mainbranch]의 패턴 : 0123451367
4) 알려진 평문의 알파벳
 [+] {'3': 'g', '%': 'o', '#': 'd', '0': 'r', '5': 'a', 
 ')': 's', '9': 'm', '6': 'i', '*': 'n', '2': 'b',
 '(': 'r', 'c': 'c', '4': 'h'}
-------------------- 찾은 알파벳으로 복호화 ------------------------------
agoodgrassin;h8bisho='shos;8rin;h8d8@ir'ss8a;1or;:-on8d8gr88sand;
hir;88nmin?;8snor;h8as;andb:nor;hmainbranchs8@8n;hrimb8as;sid8shoo;
1rom;h8r81;8:8o1;h8d8a;h's-h8adab88rin81rom;h8;r88;hro?gh;h8sho;1i1;
:188;o?;
```