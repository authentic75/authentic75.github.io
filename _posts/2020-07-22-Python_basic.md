---
title: "Python: 파이썬 기초 연산"
last_modified_at: 2020-07-22T20:20:02-05:00
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
#파일명: 0722-1.py
a = "Hello World!"
b = 5
c = 3

print(a)
print(b + c)
```

```
# 파일명: 0722-2.py
"""
a = 5
b = 7
print("더하기")
print(a + b)
print("\n")

#2진수, 8진수, 16진수

a = 0b111111
b = 0o10
c = 0xff
print("2진수, 8진수, 16진수")
print(a)
print(b)
print(c)
print("\n")

#양수, 음수

a = 123
b = -178
c = 0
print("양수, 음수")
print(a)
print(b)
print(c)
print("\n")

#다양한 연산
a = 20
b = 6

print(a + b)
print(a - b)
print(a * b)
print("\n")

#나누기, 몫, 나머지
print(a / b)
print(a // b)
print(a % b)
print("\n")

#제곱
print(2 * 3)
print(2 ** 2)
print(2 ** 3)
print(2 ** 4)
print("\n")

#데이터 타입
a = "abc"
type(2)
type("abc")
type("123")
print(12 + 12)
print("12" + "12")
print("\n")
"""

# """을 사용한 주석안에 """를 이용한 문법이 있으면 에러난다
#따옴표
#print("abc")
#print("""abc""")
#print("\n")

#print('abc')
#print('''abc''')
#print("\n")
#print(""abc"") 혹은 print(''abc'') 은 오류


#문자열 개행
#a = "you\nneed\npyhon"
#b = """you
#need
#python
#"""

#print(a)
#print("\n")
#print(b)
#print("\n")




#쌍따옴표 안에는 홀따옴표를 써도 그대로 출력된다
#print("Python's favorite food is perl!\n")

#데이터 타입의 구분
#a = "Python's favorite food is perl!"
#s = 1
#favorite = 3

#print('Python', s, favorite)

#문자열에 작은 따옴표, 큰따옴표 포함 시키기
#a = '"Python is very easy." he says.'
#print(a)

#b = "\"Python is very easy.\" he says."
#print(b)
#print("\n")

#문제1 ""을 사용하여 다음을 출력하라
"""
제 이름은 '홍길동' 입니다.
이 문자열속에 '을 넣으려면, \'를 표기하면 됩니다.
이 문자열속에 "을 넣으려면, \"를 표기하면 됩니다.
제 잠수시간은 1' 25"입니다.
"""

#a = "제 이름은 \'홍길동\' 입니다."
#b = "이 문자열속에 \'을 넣으려면, \\\'를 표기하면 됩니다."
#c = "이 문자열속에 \"을 넣으려면, \\\"를 표기하면 됩니다."
#d = "제 잠수시간은 1\' 25\"입니다."

#print(a)
#print(b)
#print(c)
#print(d)
#print("\n")

#문자열 연산하기
#head = "Python"
#tail = "is fun"

#head + tail
#print(head+ " "+ tail)

#문자를 그냥 나열해도 된다
#print("2" "3" "4")
#문자와 숫자의 연산은 불가능하다

#문자열의 곱하기는 가능하다
#print(head*2)
#print("*"*50)

#문자열 길이 구하기
#a = "Life is too short"
#print(len(a))

#문자열의 인덱싱
a = "Life is too short, You need Python"
print(len(a))
print(a[13])
print(a[-1])
print(a[-10])
print("\n")

#문자열의 슬라이싱
#0<a=<4 라는 의미
print(a[0:4])
print(a[19:])
print(a[:])
```

