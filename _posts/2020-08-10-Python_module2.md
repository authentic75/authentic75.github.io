---
title: "Python: 모듈 활용하기"
last_modified_at: 2020-08-13T20:20:02-05:00
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
#0813-1.py
#리눅스를 켜보자  ~
#리눅스에서 환경변수를 보는 명령어가 뭘까여 ~?
#로컬 변수 확인: set  환경변수: env 
############# OS ######################################
#import os
#print(os.environ) #딕셔너리 형태로 나열됨
# print("")
# print(os.environ['PYTHONPATH']) #파이썬 경로 나타남
# print("")
# print(os.environ['HOMEPATH'])
# print("")
# print(os.environ['PATH'])
#######################################################
#디렉토리 목록 보기
#print(os.system("dir"))
#print(os.system("ls -lh")) 이 명령어는 리눅스에서만 가능

#디렉토리 이동 및 확인
#os.chdir("..")
#print(os.getcwd())
########################################################많이 쓰는 명령어 기억해두자
#os 명령어의 결과를 코드에 사용할 수 없다.
#중요한 명령어인데
#print(os.listdir())
#a= os.listdir()  
#for item in a:
#    print(item)
########################################################

#os.chdir(r"C:\Users\authe\OneDrive\바탕 화면\test")
#os.getcwd()
#os.system("dir")
#a = os.popen(dir)
#for x in a:
#    print(x)
    
#os.system("md abc")
#os.mkdir("xyz")
# os.system("move file1.txt file10.txt")
# print(os.system("dir"))
#################### 파 일 복 사 ##########################
#import shuil
#shutil.copy("src.txt", "dst.txt")
#################### 원하는 파일만 읽어오기 ###################
# import glob
# glob.glob(r"C:\Users\authe\OneDrive\바탕 화면\test*")
####################### T E M P #########################
# import tempfile
# f = tempfile.TemporaryFile() #임시파일 생성
# f.close() #임시파일이 삭제됨
######################### T I M E #######################
#import time
# print(time.time())
# print(int(time.time())/86400/365)

# print(time.localtime(time.time()))
# print(time.localtime(time.time()).tm_year)
# print(time.localtime(time.time()).tm_mon)


# import datetime
# print(datetime.date.today())
# print(datetime.date.today().year)

#time.sleep(5)

#print(time.asctime(time.localtime(time.time())))
#print(datetime.date.today().strftime("%Y-%m-%d %A"))
#
#########################################################
# import calendar
# for year in range(2000,2020):
    # for month in range(1,13):
        # last = calendar.monthday(year, month)[1] + 1
        # for day in range(1, last):
#########################################################
# Random 
#import random
#random.random() # 0~1 사이
#random.randomint(1, 10) #1~10 사이

#이 코드를 고쳐서 같은 숫자가 나오면 다른 숫자가 나오도록 한다.
# numbers = []
# for x in range(0,6):
    # numbers.append(random.randint(1,46))


# numbers = []
# for x in range(0,6):
    # n = random.randint(1,46)
    # while n in numbers:
        # n = random.randint(1,46)
    # numbers.append(n)
# numbers.sort()
# print("추천: {}".format(numbers))
#문제 1 #################################################
# numbers = []
# for x in range(0,6):
    # while True:
        # n = random.randint(1,46)
        # if n not in numbers:
            # numbers.append(n)
            # break
        # else:
            # continue   
# numbers.sort()
# print("추천: {}".format(numbers))
#문제 2 ##################################################
#조건을 판단한 다음에 주민 등록 번호 양식이 맞다면 참

# file = open("data.txt", "r")
# read = file.read()

# lines = read.split("\n")


# for line in lines:
    # words = line.split()
    # for word in words:
        # if (len(word) == 14 and word[0:6].isdigit()
            # and word[6]=="-" and word[7:].isdigit()):
            # print(word, "참")
        # else:
            # print(word, "거짓")

# import re

# file = open("data.txt", "r")
# read = file.read()

# lines = read.split("\n")
# ssn = re.compile('[0-9]{6}-[0-9]{7}')

# for line in lines:
    # words = line.split()
    # for word in words:
        # m = ssn.match(word)
        # if m:
            # print(word[:7], "*******", end = ' ')
        # else:
            # print(word, end = ' ')
    # print(" ")

import re

file = open("data.txt", "r")
read = file.read()

pattern = re.compile("([0-9]{6})-[0-9]{7}")
print(pattern.sub("\g<1>-*******", read))
```