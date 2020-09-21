---
title: "Python: 모듈의 활용"
last_modified_at: 2020-08-07T20:20:02-05:00
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
# 0810-1.py
##########모듈에 대하여###############
#import mod2
#print(mod2.PI)

#mod2.average([80,92,81])
#mod2.SSN("801205-1133555")
########모듈 호출 및 사용 방법################
# from mod2 import PI
# print(PI)

# from mod2 import PI,average
# print(PI)
# print(average([78,91,99,92]))

# from mod2 import *
# a = SSN("940826-2233555")
# print(a.age)
# print(a.birthday)

#################################

# import mod2 as m
# m.average([89,88,78])

# from mod2 import average as avg
# avg([89,88,78])

# from mod2 import average as avg, PI as p
# avg([89,88,78])
# print(p)
#######직접 호출 , 간접 호출###########
#print(__name__)
#a = SSN("801205-1133555")
#print(type(a)) 
#코드를 직접 실행하는 경우 <class '__main__.SSN'>
#import 해서 실행하면 mod2.SSN ~~~
#################################
##mod2에서 직접 사용할때
# if __name__ == "__main__":
    # scores = [89,79,91,93,79]
    # print("점수목록: {}".format(scores))
    # avg = average(scores)
    # print("평균 점수: {:.2f}".format(avg))

# import mod2
#간접(import)로 사용할때    
# if __name__ == "mod2":
    # scores = [89,79,91,93,79]
    # print("점수목록: {}".format(scores))
    # avg = average(scores)
    # print("평균 점수: {:.2f}".format(avg))
#########모듈의 위치를 옮겨보자 #########################################
#source 디렉토리에 없는데 사용할 수 있었던 모듈들은 어디있는가.
#../../..python38/scripts  파이썬 모듈이 저장되는 위치가 있다.
# import datetime
# import time
# import csv

######################프로그램 종료시 사라짐##############################
#import sys
#print(sys.path) #path가 list 안에 등록되어있다.
#C:\Users\authe\OneDrive\바탕 화면\Source\mymod
#sys.path.append(r"C:\Users\authe\OneDrive\바탕 화면\Source\mymod")
#path에는 역슬래쉬가 있는데 이것을 해석하기 때문에 에러가난다 이것을 해석하지 못하게 하는 방법이 3가지인데
#한가지는 이스케이프 문자를 쓰는것, 다른하나는 역슬래쉬 \\쓰는대신 /슬래쉬를 쓰는것, 또 다른 하나는 sting 앞에 r 붙이는것

################윈도우에서 변수 확인하는법##################################
#C:\Users\authe\OneDrive\바탕 화면\Source>set A="Hong Gil Dong"
#C:\Users\authe\OneDrive\바탕 화면\Source>echo %A%
#"Hong Gil Dong"
#C:\Users\authe\OneDrive\바탕 화면\Source>
#
###############파이썬 패스에 추가해보자(창을 닫았다 끄면 초기화됨)##################
# C:\Users\authe\OneDrive\바탕 화면\Source>echo %PYTHONPATH%
# %PYTHONPATH%

# C:\Users\authe\OneDrive\바탕 화면\Source>echo %PYTHONPATH%
# %PYTHONPATH%

# C:\Users\authe\OneDrive\바탕 화면\Source>set PYTHONPATH=C:\Users\authe\OneDrive\바탕 화면\Source\mymod

# C:\Users\authe\OneDrive\바탕 화면\Source>echo %PYTHONPATH%
# C:\Users\authe\OneDrive\바탕 화면\Source\mymod

# C:\Users\authe\OneDrive\바탕 화면\Source>

#import sys
#print(sys.path)
####################영구적으로 추가 등록(환경변수 지정)########################
#PYTHONPATH 에 C:\Users\authe\OneDrive\바탕 화면\Source\mymod 를 추가한다
###################################################################

#######################p222 예외처리#################################
# try:
    # 코드1
    # 코드2
# except:
    # 코드3
# 코드4

# 진행: 코드1 -> 코드2 ->코드4
     # 코드1(에러) ->코드3 ->코드4
     # 코드1 -> 코드2(에러) ->코드3 ->코드4
###################################################################
# except: #모든에러
# except [에러종류]: #특정에러
# except [에러종류] as 변수: #특정에러에 대해, 에러 메세지를 변수에 저장한 후 진행
######################예외처리 예시들 ###################################
#
# try, except, finally
# try:
# 코드1
# except:
# 코드2
# finally: #에러 여부 상관없이 반드시 실행되는 코드
# 코드3

# 코드1(정상) -> 코드3
# 코드1(에러) -> 코드2 -> 코드3

# try, except, else, finally
# try:
# 코드1
# except:
# 코드2
# else: #에러가 없을 시에만 추가 실행
# 코드3
# finally: #에러 발생 여부 상관없이 실행
# 코드4

# 코드1(정상)->코드3->코드4
# 코드1(에러)->코드2->코드4
#예시1###############################################################
# try:
    # a = [1,2]
    # print(a[3])
    # 4 / 1
    # int('a')
# except IndexError:
    # print("인덱싱할 수 없습니다.")
# except ZeroDivisionError:
    # print("0으로 나눌수 없습니다.")
# except:
    # print("예상외의 에러발생입니다.")
#예시2################################################################
# try:
    # a = [1,2]
    # print(a[1])
    # 4 / 1
    # int('a')
# except IndexError as message:
    # print("인덱싱할 수 없습니다.")
    # print(message)
# except ZeroDivisionError as message:
    # print("0으로 나눌수 없습니다.")
    # print(message)
# except:
    # pass #아무것도 하고 싶지 않을때 
#문제1.################################################################

#나의 풀이법
# import csv

# for i in range(1,6,2):
    # filename = r"csv\class_{}.csv".format(i)
    # file = open(filename, "r")
    # rows = csv.reader(file)
    # print("")
    # for row in rows:
        # print(row)

# import csv

# for i in range(1,6):
    # try:
        # filename = r"csv\class_{}.csv".format(i)
        # file = open(filename, "r")
        # rows = csv.reader(file)
        # print("")
        # for row in rows:
            # print(row)
    # except:
        # pass

# import csv

# for i in range(1,6):
    # try:
        # filename = r"csv\class_{}.csv".format(i)
        # file = open(filename, "r")
    # except FileNotFoundError:
        # print("|없는 파일: {}|".format(filename))
    # except:
        # print("예상외의 에러발생")
    # else:
        # rows = csv.reader(file)
        # for row in rows:
            # print(row)
        # file.close()
    # finally:
        # print("---------------------------------------")

#문제2.############################################################
import mod2

while True:
    try:
        ssn = input("주민 등록 번호: ")
        a = mod2.SSN(ssn)
    except ValueError:
        print("주민등록번호를 다시 입력해주세요.\n")
    else:
        g = a.gender()
        b = a.birthday()
        n = a.age()

        print("입력하신 주민등록번호의 정보입니다.")
        print("성별: {}".format(g))
        print("생년월일: {}".format(b))
        print("만나이: {}".format(n))
        break;
```