---
title: "Python: 튜플과 리스트"
last_modified_at: 2020-07-24T20:20:02-05:00
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

```
# 파일명: 0724-1.py

#a = "20200724Rainy"
#print(a.find("R"))
#print(a.index("R"))

#index로 값을 찾게되면 처음에 찾은 인덱스만 리턴하는데 뒤에 옵션을 하나 더 줄 수 있다
#index(찾을문자열, 시작인덱스)
#b = "123a 456789a xyza 123"
#print("b.index(\"a\")=",b.index("a"))
#print("b.index(\"a\",0)=",b.index("a",0))
#print("b.index(\"a\",2)=",b.index("a",2))
#print("b.index(\"a\",3)=",b.index("a",3))
#print("b.index(\"a\",4)=",b.index("a",4))
#print("b.index(\"a\",11)=",b.index("a",11))

#join을 사용하여 문자 껴넣기 
#print(",".join("abcd"))

#[Yes/No]  Yes/No  Y  YES  Ye  yes
#print("YEs".lower())
#print("YES".lower())
#print("yeS".lower())

#strip 함수 
#a= "aabbcc홍aabbcc길동aabbcc"
#print(a.strip("a"))
#print(a.strip("ab"))
#print(a.strip("abc"))

#print(a.rstrip("a"))
#print(a.lstrip("a"))

#문자열 변수, replace("old", "new")
#print(a.replace("aabbcc", "xyz"))
#print(a.replace("aabbcc", ""))

#Split 함수 
#a = "Life is too short"
#print(a.split())
#b = "user1:x:500:500::/home/user:/bin/bash"
#print(b.split(":"))

#List 리스트명 = [요소1,요소2,요소3] 
#순차적 접근 자료라고도 부른다
#odd = [1,3,5,7,9]
#print(odd)
#print(odd[0])
#print(odd[2]+odd[1])
#print(odd[-3])
#print(type(odd))

#a = [1,2,3,['a','b','c']]
#print(a[0],a[1],a[2])
#print(a[3])
#print(a[-1][0])

#아래와 같이 입력해도 출력은 공백이 사라지고 쭈욱 나열됨
#students = [
#		['hong', '홍길동', 23, '경기'],
#		['hwang', '황진이', 25,  '서울'],
#		['dog', '멍멍이', 3, '울릉'],
#		['cat', '야옹이', 5, '제주']
#	   ]
#print(students)
#print(students[2][2])

#students = ["hong","홍길동","A",82,96,78,90,88]
#print(students[3:])

#a = [1,2,3]
#b = [4,5,6]
#c = "abc"
#d = "xyz"

#같은 자료형 끼리 더하기가 가능하다 문자열과 리스트와 비슷한 특징을 가진다
#print(a+b)
#print(c+d)
#정수와 연산할수는 없지만 곱하기는 가능하다
#print(c*3)
#print(a*3)
#쉽게 초기화할수도 있다
#print([0]*10)

#문자열 처럼 리스트도 길이를 가진다
#a = "abcde"
#b = [1,2,3,4,5]
#print(len(a))
#print(len(b))

#print(a[2]+"hi")
#print(b[2]+"hi") // 이건 오류다!

#문자열 자체는 불변이라 원소를 바꿀수 없다. 바꾸려면 전체를 바꿔야한다
#하지만 리스트의 경우 원소를 바꿀 수 있다. 그런데 원래 자리에 값이 존재해야만 교체 가능
#a = [1,2,3]
#a[2] = 4
#print(a)


#del 을 사용하여 삭제 가능하다, del 변수 del 함수 도 가능하다 
#del a[1]
#print(a)
#print(len(a))
#del a      //a가 삭제됨
#print(a)


#a = [1,2,3,4,5,6,7]
#print(a)
#i = a.index(4)
#del a[i:]
#print(a)


#목록형 자료와 함께 쓰이는 함수 
#append
#a = [1,2,3]
#print(a)
#a.append(5)
#print(a)
#a.append(4)
#print(a)


#Insert 
#a = [1,2,3]
#a.insert(3,6)       #원래 3번 자리에 있던 숫자는 뒤로 밀려나고 6이 들어간다
#print(a)
#a.insert(1,7)       # 1번자리에 있던 숫자는 뒤로 밀려나고 7이 들어간다
#print(a)


#sort 함수
#a = [5,4,3,2,1,6,7,8]
#x = [1,2,3]
#y = [4,5,6]
#a.sort()
#print(a)
#print(x+y)
#a.reverse()
#print(a)

#a = ['a','b','c',1,2,3]
#a.index('c')
#a.index(2)

# a.index(4) # 에러남
# a.find(4) #에러남


#del a[2]
#print(a)

#a.remove(2)
#print(a)

#del과 pop의 차이
#a = ['a','b','c',1,2,3]
#del a[-1]
#print(a)
#print(a.pop())
#print(a)
#print(a.pop(1))
#print(a)

#원소 개수 세기
#a = [1,2,3,4,1,2,3,1,2]
#print(a.count(4))
#print(a.count(2))
#print(a.count(1))


#append 는 한개의 값만 더한다, extend는 여러값을 추가한다
#a = [1,2,3]
#b = [4,5,6]
#print(a+b)
#print(a)
#print(b)
#a.extend(b)
#print(a)

#반드시 리스트형 원소로 더해야한다 그냥 정수 입력하면 에러 
#a.extend([9,10])
#print(a)
#a.extend([11])
#print(a)

#튜플과 리스트 ! 거의 같다 차이점은 튜플은 문자열과 같아서 수정
이 안된다 append, remove 불가능 
a = [10,20,30,40]
b = (10,20,30,40)
print(type(a))
print(type(b))

print(a[2])
print(b[2])

print(a.index(30))
print(b.index(30))

#튜플과 연산 두개가 헷갈릴수 있음. 따라서 튜플을 나타내기 위해 값이 몇개 없어도 콤마를 써준다
print(type(()))
print(type((10)))
print(type((10,)))
print(type((10,20)))
print(type((10+20)))
```