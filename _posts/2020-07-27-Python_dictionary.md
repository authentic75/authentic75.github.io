---
title: "Python: 딕셔너리"
last_modified_at: 2020-07-27T20:20:02-05:00
categories:
  - Python
tags:
  - Python
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
```python
##파일명: 0727-1.py


#목록형 자료형과 딕셔너리형 자료
#목록형  변수 = [item, item1, item2...]
#사전형  변수 = {key1:value1, key2:value2}
#              item1          item2
#a1 = {'hong', '홍길동', 23}
#a2 = {'id':'hong', 'name':'홍길동'}

#print(a1[2])
#print(a2['name'])
#print(a2['id'])
#key가 아닌 value로 접근은 안된다

#lis = ['pey', '0119993323', '1118']
#dic = {'name':'pey', 'phone':'0118883323', 'birth':'1118'}

#print(lis[0])
#print(lis[1])
#print(dic['name'],dic['phone'])

######추가해보자########
#a = [1,2,3]
#b = {1:'a'}

#a는 수정만 되고 추가가 안된다
#a.append(4)
#print(a)

#a[2]=5
#print(a)

#b는 추가가 가능하다. 추가와 수정의 명령어가 같다
#b = {1:'a'}
#b[3] = 'c'
#print(b)
#b[3] = 'b'
#print(b)

######빈공간에 추가#######################
#a = []
#a.append('a')
#a.append('b')
#a.append('c')
#a.append('d')
#print(a)

#b = {}
#b[1] = 'a'
#b[2] = 'x'
#b[3] = 'c'
#b[4] = 'b'
#print(b)

#b[4] = [1,3,5]
#b['name'] = 'pey'
#print(b)
########삭제###########################
#del b[4]
#print(b)
#del b['name']
#print(b)
########리스트와 사전형자료 비교 ###########

#students1 = [
#		['hong', '홍길동', 23, '경기'],
#		['hwang', '황진이', 25, '서울'],
#		['dog', '멍멍이', 5, '제주'],
#		['cat', '야옹이', 3, '울룽']
#	    ]

#students1에서 황진이의 주소를 출력하라면 어떻게 할것인가
#머리속으로 구조를(인덱스 번호를) 다 알아야한다는 단점이 있다. 
#print(students1[1][3])


# PK : { name, age, addr } 형태로도 가능 
#students2 = {
#		'hong': {'name':'홍길동', 'age':23, 'addr':'경기'},
#		'hwang': {'name':'황진이', 'age':25, 'addr':'서울'},
#		'dog': {'name':'멍멍이', 'age':5, 'addr':'제주'},
#		'cat': {'name':'야옹이', 'age':3, 'addr':'울룽'}
#        }
#print(students2['hwang']['addr'])

#고양이의 나이는?
#인덱스를 세봐야한다
#print(students1[3][2])
#인덱스를 몰라도 'cat'과 'age'라는 키로 value를 얻을 수 있다
#print(students2['cat']['age'])

############사전형 자료에서 지원하는 함수###############
#키 값들이 뭐가 있는지 모를때는 다음과 같은 명령어로 키값만 알수 있다.
#print(students2.keys())
#혹은 value들을 출력해보고 싶다면 
#print(students2.values())


#a  = {1:'a',2:'b',3:'c'}
#print(a.keys())
#print(a.values())
#print(a.items())

#위와 같은 것들은 객체로 리턴 해주는 명령들이고
#리스트로 출력하려면 아래와 같이 써야한다.
#print(list(a.keys()))
#############사전형 자료 지우기##############
#a.clear()   #메모리 주소를 유지한채 내용을 지운다
#a = {}      #메모리를 다시 할당 받는다
########################################
#students2 = {
#		'hong': {'name':'홍길동', 'age':23, 'addr':'경기'},
#		'hwang': {'name':'황진이', 'age':25, 'addr':'서울'},
#		'dog': {'name':'멍멍이', 'age':5, 'addr':'제주'},
#		'cat': {'name':'야옹이', 'age':3, 'addr':'울룽'}
#        }

#print('hong' in  students2)
#print('name' in  students2) #False
#print('email' in  students2)
## 'a' in A.keys() 와 'a' in A 와 의미가 같다.
#####################################
#answer1 = "yes"
#answer2 = "y"
#print(answer1=="yes" or answer1 =="ye")
#####################################

#############BOOL형 자료###############
#a = True
#b = False
#x = 10

#print(x > 5)

#c = x > 5
#print(c)

#type(a)
#type(b)

#print(1 == 1)
#print(1 != 1)
#############IF문을 활용하자##############

#0이 아닌 다른 값들은 전부 참이다.
#data = 100
#data = [1,2,3]
#비어있어야 false다 
#data = [] 

#if data:
#    print("TRUE")
#else:
#    print("FALSE")
######################################
#산술연산 , 관계연산, 논리연산
#산술 + - * /
#관계 == != > >= < <=
#논리 not and or
######################################
#변수명 규칙
#알파벳, 숫자, 특수문자(_)만으로 구성
#변수명 처음글자가 숫자일수는 없다
######################################
###########배열의 복사###################
#둘은 같은 메모리 위치를 가지게된다
#a = [1,2,3]
#b = a

#print(a)
#print(b)

#a를 수정했는데 b도 함께 수정되었다.
#a.append(4)
#print(a)
#print(b)

#a와 b는 같다 메모리 주소도 같다 
#하지만 a와 c는 내용은 같지만 메모리 위치가 다르다 
#a = [1,2,3]
#b = a
#c = a[:]

#print(a)
#print(b)
#print(c)

#print(a==b)
#print(a==c)
#print( a is b )
#print( a is c )
#print(id(a))
#print(id(b))
#print(id(c))
#############다양한 연산################
#a = 10 
#b = 10
#c = 10

#a = b = c = 10
#print(a)
#print(b)
#print(c)

#맨 끝쪽에는 연산도 가능하다 
#a = b = c = 10 + 20
#print(a)
#print(b)
#print(c)
#############변수에 값 대입################
#a = 10
#b = 20
#c = 30

#1:1 매치만 된다면 아래와 같이 대입도 가능하다 
#a, b, c = 11, 22, 33
#a, b, c = (11, 22, 33)
#(a, b, c) = 11, 22, 33
#(a, b, c) = (11, 22, 33)
#print(a)
#print(b)
#print(c)
##############변수값의 switch################

#a = 10
#b = 20

#a,b = b,a
#age >=20 and age <30, 20 <= age <30
####################if문###################

#money = True

#if money:
#    print("택시를 타고 가라")
#else:
#    print("걸어 가라")
    
###########입력을 받아서 if문을 실행하자##########

#사용자 입력을 받는데, 문자열로 받는다
#money = input()

#정수로 입력받는다

#money = int(input("소지금액: "))
#print(money)
#print(type(money))

#if money > 10000000000:
    #print("-" * 18)
    #print("{:^18}".format("결과")
    #print("-" * 18)
#    print("전용기를 타고 가라")
#elif money >= 3500:
    #print("-" * 18)
    #print("{:^18}".format("결과")
    #print("-" * 18)
#    print("택시를 타고 가라")
#else:
    #print("-" * 18)
    #print("{:^18}".format("결과")
    #print("-" * 18)
#    print("걸어 가라")
##########################################

#파이썬에서는 문장이나 조건이 끝날 때 : 이 오고 
#그 다음엔 들여쓰기가 꼭 함께 온다
##########################################
#연산자 우선순위
# 괄호
# * /
# + -
# == != > >= < <=
# not
# and 
# or 

#3 > 2 or not True and 10 !=5 or (4+1) > 5
# (4+1)
# 3>2   10!=5   5 > 5
# Not True
# not True and 10 !=5
# 3 > 2 or not True and 10 !=5 or (4+1) > 5
###############여러 조건 조건문################
#money = int(input("소지금액: "))
#card = input("카드유무[y/n]: ")

#if money >= 3500 or card == "y":
#    print("택시를 타고 가라")
#else:
#    print("걸어서 가라")
############not ~ in ~ 조건문###############

#프로그램2
#코드1
#if 조건 1:
#    코드2
#elif 조건 2:
#    코드3
#elif 조건 3:
#    코드4
#else:
#    코드5
##########################################
age1 = 15
age2 = 25

#if age1 > = 20:
#    print("성년")
#else:
#    print("미성년")

#인터프리터에서는 이렇게만 쓰면 컴퓨터 입장에서는 어디가 끝인지 모르니까 엔터를 한번 더 쳐준다

if age1 >= 20: message = "성년"
else: message = "미성년"
print(message) 

#이거를 더 쉽게 만들어 보자
message = "성년" if age2 >= 20 else "미성년"
print(message) 
```