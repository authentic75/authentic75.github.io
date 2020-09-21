---
title: "Python: 객체 지향"
last_modified_at: 2020-08-06T20:20:02-05:00
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
#0806-1.py
#파이썬은 객체지향 언어다.
#P183

# x = 5 
# def test(x):
    # x+=1
    # print(x)

# result = 0
# def add(num):
    # global result
    # result +=num
    # print(result)

# print(result)
# add(4)
# add(3)
#####################################
# result1 = 0
# def add1(num):
    # global result1
    # result1 +=num
    # print(result1)
    
# result2 = 0
# def add2(num):
    # global result2
    # result2 +=num
    # print(result2)
    
# add1(4)
# add1(3)

# add2(10)
# add2(10)
# add2(10)

# print(result2)
#클래스를 만들어보자########################

# class Calc:
    # result = 0
    # def add(self, num):
        # self.result +=num
        # print(self.result)
# a = Calc()
# b = Calc()

# print("a.result",a.result)
# print("b.result",b.result)

# a.add(4)
# a.add(3)

# b.add(10)
# b.add(20)

# print("a.result",a.result)
# print("b.result",b.result)

# print(type(a))
# print(type(b))
#자료형도 결국엔 타입이다!#################

# a = int()
# print(type(a))
# print(a)

# b = str()
# print(type(b))
# print(b)

######################################

# class Demo:
    # a = 1
    # b = 2

# class Test:
    # x = 10
    # y = 20

# m = Demo()
# n = Test()

# print(type(m))
# print(type(n))

#같은 부모로 부터 태어난 자식들 ##############
#객체는 부모 인스턴스는 자식이라고 보면 된다
# class Demo:
    # a = 1
    # b = 2
    
# class Test:
     # x = 10
     # y = 20    
# a = Demo()
# b = Demo()

# a.a+=1
# b.a+=3

#print(a.a)
#print(b.a)


# a = Test()
# b = Demo()
# c = str()

# print(dir(a))
# print(dir(b))
# print(dir(c))

#클래스를 이제 만들어보자 ###################

# class FourCal:
    # def setdate(self, first, second)
        # self.first = first
        # self.second = second
 
# a = FourCal()
# a.setdate(4, 2)
#모듈을 사용하여 보자###########################
#모듈파일에 FourCal()을 추가하고 나서 사용해보자
# import mod1
# a = mod1.FourCal()
# print(type(a))
#함수명만 사용하여 부를수 있게 해보자###############
#from mod1 import FourCal
#b = FourCal()
#print(dir(b)[-5:]) #b는 2개의 속성을 부모로 부터 물려받았다. setdata와 add 데이터만 생김

#b.setdata(4,2)     #set Data를 통해 fist와 second 속성이 생김
#b.add()            #first와 second 속성이 생겨서 add를 부를수 있다. 그런데 a.add()를 set없이 부르면 에러가 난다.
#print(dir(b)[-5:])
##########################################
#이러한 상황을 방지하려면? 
#default 값을 미리 넣어준다.
#
#class FourCal:
#    first = 0
#    second = 0
#    def setdata(self, first, second):
#        self.first = first
#        self.second = second
#    def add(self):
#        result = self.first + self.second
#        return result
#########################################
#mod1.py에 FourCal 을 완성해보자.
#
# from mod1 import FourCal 
# a = FourCal()
# a.add()
# a.sub()

# print("a:")
# a.setdata(4,2)
# print(a.add())
# print(a.sub())
# print(a.mul())
# print(a.div())
       
# print("\nb:")
# b = FourCal()
# b.setdata(5,1)
# print(b.add())
# print(b.sub())
# print(b.mul())
# print(b.div())

#a와 b는 메모리의 다른 곳에 저장되었음을 알 수 있다.
# print("\na id:",id(a))
# print("b id:",id(b))
#문제1. ##################################
# import csv

# rfile = open("shopping_db.csv", "r", encoding = "utf-8")
# rows = list(csv.reader(rfile))
# wfile = open("sales.csv","w", newline="")
# csvfile = csv.writer(wfile)
# csvfile.writerow(['월','매출액'])
# sales=[]
# for month in range(1, 7):
    # sum = 0
    # for row in rows:
        # mm = int(row[3][5:7])
        # price = int(row[5])
        # if month == mm:
            # sum +=price
    # print(month, sum)
    # csvfile.writerow([month, sum])
#문제2. ##################################
import csv

rfile = open("shopping_db.csv", "r", encoding = "utf-8")
rows = list(csv.reader(rfile))
wfile = open("sales2.csv","w", newline="")
csvfile = csv.writer(wfile)
csvfile.writerow(['월','매출액'])
sales=[]
for month in range(1, 7):
    sum = 0
    for row in rows:
        mm = int(row[3][5:7])
        price = int(row[5])
        if month == mm:
            sum +=price
    sales.append([month]+[sum])
csvfile.writerows(sales)
#######################################
#0807-1.py

#문제1. ###########################################################
#주민등록번호와 관련된 함수: gender, birthday, age
#뒷자리의 두번째 자리가  1256(1900년대) 3478(2000년대) 90(1800년대)
#  13579 'M' , 24680 'F'

#datetime.date.today()
#datetime.date.today().year
#datetime.date.today().month
#today = datetime.date.today()
#int(today.strftime("%Y%m%f"))

#import datetime

# def gender(ssn):
    # if ssn[7] in ['1','3','5','7','9']:
        # return 'M'
    # elif ssn[7] in ['2','4','6','8','0']:
        # return 'F'

# def birthday(ssn):
    # if ssn[7] in ['1','2','5','6']:
        # year = "19"+ssn[0:2]
    # elif ssn[7] in ['3','4','7','8']:
        # year = "20"+ssn[0:2]
    # else:
        # year = "18"+ssn[0:2]   
    # month = ssn[2:4]
    # day = ssn[4:6]
    # return [year, month, day]

    
# def age(ssn):
    # today = int(datetime.date.today().strftime("%Y%m%d"))
    # if ssn[7] in ['1','2','5','6']:
        # birth = int("19"+ ssn[0:6])
    # elif ssn[7] in ['3','4','7','8']:
        # birth = int("20"+ ssn[0:6])
    # elif ssn[7] in ['9','0']:
        # birth = int("18"+ ssn[0:6])
    # else:
        # year = 0
    # age = (today - birth)//10000
    # return age

    
# ssn = input("주민등록번호:")
# print("성별:",gender(ssn))
# print("생일:",birthday(ssn))
# print("만 나이:",age(ssn))
#################################################################
#문제2. 이러한 프로그램에는 문제가있다 ssn이 잘 못 입력 되었을 경우를 대비하기위해서
#각각의 함수안에서 일일이 인자값으로 들어오는 ssn을 검사해야하는데, 이럴경우
#데이터 중심인 Class를 이용한다면 에러가 날 일 이 없다. 한번 구현해보자.
#
#type('a')==type(str())을 이용하자

#data[0:6].isdigit() and data[6] == "-" and data[7:14].isdigit()
#not data[0:6].isdigit() or data[6]!="-" or not data[7:14].isdigit()

#정규표현식
#import re
#regex = re.compile(r'\d\d\d\d\d\d-\d\d\d\d\d\d\d')
import datetime

class SSN:
    def __init__(self, data):
        if not isinstance(data, str):
            raise ValueError("주민등록번호 양식이 아닙니다: {}".format(data))
        elif len(data) != 14:
            raise ValueError("14자리 길이의 문자열이 아닙니다: {}".format(data))
        elif not(data[0:6].isdigit() and data[6] == "-" and data[7:14].isdigit()):
            raise ValueError("주민등록번호 양식이 아닙니다: {}".format(data))
        self.data = data 

    def gender(self):
        code = int(self.data[7])
        if code in [1, 3, 5, 7, 9]:
            g = 'M'
        else:
            g = 'F'
        return g
        
    def birthday(self):   
        code = int(self.data[7])
        if code in [1, 2, 5, 6]:
            year = 1900+int(self.data[0:2])
        elif code in [3, 4, 7, 8]:
            year = 2000+int(self.data[0:2])
        else:
            year = 1800+int(self.data[0:2])   
        month = int(self.data[2:4])
        day = int(self.data[4:6])
        return datetime.date(year,month,day)

    def age(self):
        tday = int(datetime.date.today().strftime("%Y%m%d"))
        bday = int(self.birthday().strftime("%Y%m%d"))
        age = (tday-bday)//10000
        return age

a = SSN("930715-1133555")
b = SSN("930807-2233555")
c = SSN("930809-2233555")

print(a.gender())
print(b.gender())
print(c.gender())

print(a.birthday())
print(b.birthday())
print(c.birthday())

print(a.age())
print(b.age())
print(c.age())

```







































