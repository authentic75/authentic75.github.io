---
title: "Python: 예외 처리"
last_modified_at: 2020-08-12T20:20:02-05:00
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
#0812-1.py
#0811에는 시험을 봐서 코딩 수업이 없었음.
#import datetime

#############에러를 직접 만들어보자###############################
# class ValueStrError(Exception):
    # def __str__(self):
        # return "입력된 값이 문자열이 아닙니다"
# class ValueLenError(Exception):
    # def __str__(self):
        # return "입력된 값이 14 자리가 아닙니다"
# class ValueTypeError(Exception):
    # pass

# class SSN:
    # def __init__(self, data):
        # if not isinstance(data, str):
            # raise ValueStrError 
        # elif len(data) != 14:
            # raise ValueLenError 
        # elif not(data[0:6].isdigit() and data[6] == "-" and data[7:14].isdigit()):
            # raise ValueTypeError 
        # self.data = data 

    # def gender(self):
        # code = int(self.data[7])
        # if code in [1, 3, 5, 7, 9]:
            # g = 'M'
        # else:
            # g = 'F'
        # return g
        
    # def birthday(self):   
        # code = int(self.data[7])
        # if code in [1, 2, 5, 6]:
            # year = 1900+int(self.data[0:2])
        # elif code in [3, 4, 7, 8]:
            # year = 2000+int(self.data[0:2])
        # else:
            # year = 1800+int(self.data[0:2])   
        # month = int(self.data[2:4])
        # day = int(self.data[4:6])
        # return datetime.date(year,month,day)

    # def age(self):
        # tday = int(datetime.date.today().strftime("%Y%m%d"))
        # bday = int(self.birthday().strftime("%Y%m%d"))
        # age = (tday-bday)//10000
        # return age

# def average(scores):
    # sum=0
    # for score in scores:
        # sum += score
    # avg = sum / len(scores)
    # return avg
# PI = 3.14
##########################################################

#ssn = input("주민등록 번호:")
# a = SSN(123123)
# print(a.gender())
# print(a.birthday())
# print(a.age())
################### 라이브러리 ##############################
#파이썬을 통해 할 수있는 코딩 수업이 많이 진행되었다. 
#파이썬을 통해 이제 프로그램을 개발 할 수 있을텐데
#이미 있는 기능들을 또 구현할 필요는 없을 것이다.
#https://www.python.org/ 에 접속하여 라이브러리를 살펴보자
#라이브러리 페이지는 아래에 
#https://docs.python.org/3.8/library/index.html
#Built-in-fuctions 에 들어가면 지원되고 있는 함수들이 listing 되어있다.
#########################################################

#abs(-5) 절대값 리턴
#chr(65) ascii 코드를 문자로 리턴 
#ord('a') 문자를 ascii 코드로 리턴
#isinstance(객체, 메소드 속성) 객체에 속해있는 속성이 맞는지 확인
#dir(객체) 객체가 가지고 있는 속성들을 나열해줌
#
#문제1. #####################################################
# friends = ['수영', '민수', '홍철', '희주']

# for i in range(0,len(friends)):
    # print("{} {}".format(i,friends[i]))
################### 반복가능한 객체 ############################
# enumerate(friends)
# list(enumerate(friends))
# print(friends)

# for item in enumerate(friends):
    # print(item)
# for a,b in enumerate(friends):
    # print(a,b)
################## eval 함수 ##############################
# print(eval('1 + 2')) #문자열을 실행하여 결과 리턴
# nums = [8, 12, 5, -3, 7, -1]

# def positive(nums):
    # result = []
    # for num in nums:
        # if num > 0:
                # result.append(num)
    # return result

# print(positive(nums))

# def test(num):
    # return num > 0

# filter(test, nums)  #함수와 함께 반복가능한 자료형을 받아서 자동으로 반복 동작)
# list(filter(test, nums))
#
################## 진수 #################################
#hex(19)  '0x13'
#bin(19)  '0b10011'
#oct(19)  '0o23'
########################################################
#id(a)  # 객체를 가리키는 고유 주소값 리턴해줌
#type(a) == type(0)
#type(a) == type(true)
#
#isinstance(a, int)
#isinstance(a, str)
#
#max( list )
#min( list )
#
#round(4.6) #반올림
#round(5.678, 2)
########################################################

#192.168.0.0
#255.255.0.0

#ip = input("IP 주소: ")
#sm = input("서브넷: ")
#print(ip.split("."))
#print(sm.split("."))

#n = bin(int("12"))[2:]
#print("{:>08}".format(num))
#
#문제2. #################################################

#ip = input("IP 주소: ")
#sm = input("서브넷: ")

# ip = "192.168.0.0"
# sm = "255.255.0.0"


# def ip_dig2bin(ip):
    # text = ""
    # for octet in ip.split("."):
        # n = bin(int(octet))[2:]
        # text +="{:>08}".format(n)+"."

    # return text[0:-1]

# print(ip_dig2bin(ip))


# ip_bin = ip_dig2bin(ip)
# sm_bin = ip_dig2bin(sm)
# last = sm_bin.index("0")
# n = len(ip_bin[last:])
# print(ip_bin[last:]+"h"*n)
# print(sm_bin)
#
########################################################
# import sys

# arg_list = sys.argv[1:])
# if len(arg_list) == 0:
    # sys.exit()  #프로그램 실행 중지

# print(arg_list)

# print(sys.path)
# sys.path.append("경로")
########################################################
# import pickle

# a = ["철수","영희","멍멍","야옹" ]
# b = {'a':'철수', 'b':'영희', 'c':'멍멍', 'd':'야옹' }

# file = open("a.bin","wb")
# pickle.dump(a, file)
# file.close()

# file = open("b.bin","wb")
# pickle.dump(b, file)
# file.close()

# file = open("a.bin", "rb")
# print(pickle.load(file))

# file = open("b.bin", "rb")
# print(pickle.load(file))
#
########################## Data Base ###########################
#파이썬으로 DB 접속을 해볼것이당...
#   Windows(DB Client)  CentOS 8(DB Server)
#L3 192.168.0.17        192.168.0.164
#L4 1024~               3306
#L7 user    winadmin
#   pass    1234
#   DB      mysql
#
#mysql -h 127.0.0.1 -P 3306 -u root -p1234 mysql
#CREATE USER 'winadmin'@'192.168.0.17' IDENTIFIED BY '1234';
#firewall-cmd --add-port=3306/tcp
#GRANT ALL PRIVILEGES ON *.* TO 'winadmin'@'192.168.0.17';
#
#import pymysql
#
# pymysql.connect(host = "192.168.0.164", port = 3306,
                # user = "winadmin", password = "1234",
                # db = "mysql")

# connect = pymysql.connect(host = "192.168.0.164",
                # port = 3306,
                # user = "winadmin",
                # password = "1234",
                # db = "shopping_db")

#cursor = connect.cursor()
#print(cursor.execute("SHOW TABLES"))  #명령어의 결과가 몇개 인지만 알려준다.
#print(cursor.fetchall())
#print(cursor.fetchone())

# cursor.execute("SELECT * FROM customer")
# print(cursor.fetchall())
#                
################################################################
# import pymysql
# connect = pymysql.connect(host = "192.168.0.164",
                # port = 3306,
                # user = "winadmin",
                # password = "1234",
                # db = "shopping_db")

# cursor = connect.cursor()

# sql = "SELECT * FROM customer"
# sql = "SELECT * FROM customer"
# cursor.execute(sql)
# rows=cursor.fetchall()
# for row in rows:
    # print(row)
###############################################################
import pymysql
connect = pymysql.connect(host = "192.168.0.164",
                port = 3306,
                user = "winadmin",
                password = "1234",
                db = "shopping_db")

cursor = connect.cursor()

while True:
    sql = input("sql> ")
    if sql == "quit":
        print("프로그램을 종료합니다")
        break;
    elif sql == "":
        continue
    try: 
        cursor.execute(sql)

    except pymysql.err.ProgrammingError as message:
        print(message)
    else:
        rows=cursor.fetchall()
        for row in rows:
            print(row)
```