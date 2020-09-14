---
title: "Python: 파이썬 문자열, 배열, 형식"
last_modified_at: 2020-07-23T20:20:02-05:00
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
# 파일명: 0723-1.py

#a = "20010331rainy"
#print("a : ", a )

#date = a[:8]
#weather = a[8:]
#print( "date : ", date)
#print( "weather : ", weather)
#print("\n")
#--------------------------------------
#문제1 주민등록번호에서 년, 월, 일 출력 

#ssn = "891207-1133555"
#year = "19"+ssn[:2]
#month = ssn[2:4]
#day = ssn[4:6]

#print("생년월일")
#print("생년:", year)
#print("생월:", month)
#print("생일:", day)
#--------------------------------------
#문자열 일부를 수정할 수 없다
#문자열은 불변의 속성을 가진다 
#a = "Pithon"
#print(a)
#a[1] = "y"             에러

#a= a[0] + "y" + a[2:]
#print(a)
#하지만 현실은 다시 그냥 치는게 빠르다
#------------포맷 스트링--------------------

#t11 = "현재 온도는 11도 입니다."
#t20 = "현재 온도는 20도 입니다."
#일일이 만들기 힘들다 

#정수형
#t = "현재 온도는 %d도 입니다."
#print(t%11)
#print(t%20)
#print("I eat %d apples"%4)

#num = 4
#print("I eat %d apples"%num)

#문자열
#print("I eat %s apples"%"three")

#한문장에 여러 포맷 스트링 
#num = 10
#day = "three"
#a = "I ate %d apples, so I was sick for %s days"
#print(a%(num, day))

#실수 
#error = 98.5
#a = "Error is %d%%."
#b = "Error is %f%%."
#print(a%error)
#print(b%error)

#----------------자릿수-----------------

#자릿수 확보 (왼쪽 정렬)
#a = "Error is %5d%%."
#print(a%error)
#b = "Error is %15f%%."
#print(b%error)

#오른쪽 정렬 (음수)
#a = "Error is %-5d%%."
#print(a%error)
#b = "Error is %-15f%%."
#print(b%error)
#----------------소수의 자리수-------------

#Default로 6자리 까지 출력
#a = "---%f---"

#n1 = 78.1234
#n2 = 85.13579
#n3 = 93.123456789

#print("%f")
#print(a%10)
#print(a%n1)
#print(a%n2)
#print(a%n3)

#b = "---%.2f---"
#c = "---%10.2f---"

#print("%.2f")
#print(b%10)
#print(b%n1)
#print(b%n2)
#print(b%n3)

#print("%10.2f")
#print(c%10)
#print(c%n1)
#print(c%n2)
#print(c%n3)

#---------새로운 방식의 문자열(텍스트 포맷팅)--------

#새로운 방식이다. 데이터 타입 상관 없다, 여러값이 들어갈 수도있다
#함수형이라 괄호는 무조건 넣는다
#a = "I eat {} apples"
#num = 3
#print(a.format(10))
#print(a.format(10, 20, 40))
#print(a.format("three"))
#print(a.format(10.5))
#print(a.format(num))

#문자 안에 여러개의 괄호가 있을떄
#b = "I eat {} appels, so I was sick for {} days"
#print(b.format(10, "three"))
#print(b.format("three", 10))


#인덱스를 지정해서 출력할 수도 있다
#print("I eat {0} appels, so I was sick for {1} days".format(10,20,30))
#print("%d %d %d %d %d"%(10,10,20,20,10))
#print("{} {} {} {} {}".format(10,20,30,40,50))
#print("{0} {0} {1} {2} {0}".format(10,20,30,40,50))

#매개 변수를 사용할 수도 있다
#g = "I ate {number} apples, so I was sick for {day} days"
#print(g.format(number = 4, day = "two"))

#그렇다면 이 새로운 방식은 어떻게 자릿수와 위치를 조정할까
#print("---{0}---".format(123,456))
#print("---{1}---".format(123,456))

#숫자는 우측정렬 문자는 좌측 정렬, 콜론을 기준으로 앞은 인덱스 뒤는 자릿수 
#print("---{0:10}---".format(123,456))
#print("---{0:10}---".format("hong"))

#정렬 위치 지정은?
#print("---{0:<10}---".format("hong"))
#print("---{0:>10}---".format("hong"))
#print("---{0:^10}---".format("hong"))

#print("---{:f}---".format(12.345678))
#print("---{:.3f}---".format(12.345678))
#print("---{:10.3f}---".format(12.345678))

#print("---{:}---".format(12345678))
#print("---{:,}---".format(12345678))

#빈공간을 특정 문자로 채우고 싶다
#print("---{0:0>10}---".format("12345"))

#괄호를 함께 출력해보자 \\ , %%, {{ 

#print("---{{{:d}}}---".format(12))

#----------------f열 포맷팅--------------------
#number = 10
#day = "three"
#print(f"I ate {number} apples, so I was sick for {day} day.")

#----------------함수(메소드)-------------------
a = "hobby"
b = "Life is too short, You need python"

#함수명(데이터) -> 함수 , 데이터.함수명() -> 메소드 : 클래스가 필요하다

#count 함수: 원소가 몇개 존재하는지 
#print(a.count("h"))
#print(a.count("b"))
#print(b.count("t"))

#find, index: 찾고자하는 원소의 인덱스를 구해준다
#print(a.find("h"))
#print(a.index("b"))
#print(b.find("t"))

#응용
#i = b.find(",")
#print(b[:i])

#없는 문자를 찾으면 어떻게 되는가 
#find는 -1 을 출력 index는 오류 메세지 출력
#print(a.find("x"))
#print(a.index("x"))
```

```
# 파일명: 0723-2.py
#문제 !  다음과 같은 변수를 가지고 3가지 방법으로 총 9 문자열을 출력해라
#제 이름은 '홍길동' 입니다.
#제 평균점수는 89.1점입니다.
#제 잠수시간은 1' 25"입니다.

name = "홍길동"
avg = 89.13579
min = 1
sec = 25

#쌍따옴표안에는 이스케이프 문자안써도 ' 출력됨 출력할 문자열이 "을 포함하면 이스케이프문자 다써야함
print("제 이름은 \'%s\' 입니다."%name)
print("제 평균점수는 %.1f점 입니다."%avg)
print("제 잠수시간은 %d\' %d\"입니다.\n"%(min,sec))

print("제 이름은 \'{}\' 입니다.".format(name))
print("제 평균점수는 {:.1f}점 입니다.".format(avg))
print("제 잠수시간은 {}\' {}\"입니다.\n".format(min, sec))

#Ctrl + D 를 누르면 커서가 있는 줄을 그대로 밑에 복사해줌 
print(f"제 이름은 \'{name}\' 입니다.")
print(f"제 평균점수는 {avg:.1f}점 입니다.")
print(f"제 잠수시간은 {min}\' {sec}\"입니다.\n")
```



























