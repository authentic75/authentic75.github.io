---
title: "Cryptography: 패스워드 크래킹"
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
### 시스템 패스워드 크래킹의 유형
---

* 수동적 온라인 공격: 시스템에 직접 연결하거나 통신하지 않고 패스워드 크래킹 (유선 스니핑, 중간자 공격)
* 능동적 온라인 공격: 시스템에 직접 연결, 접속하여 패스워드를 추측하여 입력 (트로이 목마, 스파이웨어, 키로거 활용)
* 오프라인 공격: 공격자가 시스템이 관리하는 패스워드 파일에 접근하여 공격 (무차별 대입 공격, 사전 공격, 음절 공격, 레인보우 테이블 공격)
{: .notice}

```python
def findPass(passhash, dictfile):  
    salt = passhash[3:5]    #passhash의 3,4번째 문자가 salt
    with open(dictfile, 'r') as dfile:        #with ~ as를 사용하면 자동으로 close 됨
        for word in dfile.readlines():
            word = word.strip('\n')
            cryptwd = ccrypt.crypt(word, salt) #패스워드 해시를 계산
            if cryptwd == passhash[3:]: #3번째부터 패스워드 해시			
                return word
    return ''  #일치하는 단어가 없으면 빈 문자열을 리턴
	
def main():
    dictfile = 'dictionary.txt'
    with open('passwords.txt', 'r') as passFile:
        for line in passFile.readlines():   
            data = line.split(':')       
            user = data[0].strip()      #data[0]은 사용자 아이디
            passwd = data[1].strip()  #data[1]은 패스워드 해시값
            word = findPass(passwd, dictfile) #word가 빈 문자열이 아니면 패스워드를 찾은 것.
            if word:
                print('Found Password: ID [%s] Password [%s]' %(user, word))
            else:
                print('Password Not Found!')
if __name__ == '__main__':
    main()
```

```
#dictionary.txt
apple
python
password
12345
secret
welcome
admin
pineapple
other
login
pokemon
iloveyou
```

```
#passwords.txt
root : $1$a3gSE1RAj6522:14923: 0 : 99999 : 7 :      :      :
samsjang:$1$zfti6chVSQLlQ:14945: 0: 99999: 7:        :      :
```

```
Found Password: ID [root] Password [iloveyou]
Found Password: ID [samsjang] Password [python]
```