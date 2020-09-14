---
title: "Python: 함수(def)"
last_modified_at: 2020-07-30T20:20:02-05:00
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
#0730-1.py

#문제1.########################################
#입력된 과목수 만큼 반복하면서 변수 scores 생성
#subject = int(input("평균을 구할 과목수:"))
#scores = []
#for i in range(1,subject+1):
#    score = int(input("과목 {}:".format(i)))
#    scores.append(score)
#print(scores)
#
#sum = 0
#for score in scores:
#    sum +=score 
#avg = sum/len(scores)
#print("평균점수: {}".format(avg))
#
#문제2.########################################
#그냥 엔터를 누를때 까지 반복하면서 변수 scores 생성
#num = 1
#scores=[]
#while True:
#    score = input("과목 {}:".format(num))
#    if score:
#        scores.append(int(score))
#        num+=1
#    else:
#        break
#print(scores)
#
#sum = 0
#for score in scores:
#    sum +=score 
#avg  =sum/len(scores)
#print("평균점수: {}".format(avg))
##############################################

####################함수#######################
#
#def add(a,b):
#    return a+b
#print(add(10,4))
#
###############인자가 없는 함수###################
#def apple1():
#    print("사과를 1개 사다주세요.")
#def apple2():
#    print("사과를 2개 사다주세요.")
#print(apple1())
##############인자를 받는 함수####################
#def apple(count):
#    print("사과를 {}개 사다주세요.".format(count))
#print(10)
#
#def buy(goods, count):
#    print("{}을/를 {}개 사다주세요.".format(goods, count))
#print(buy("콜라",5))
#
##############반환이 있는 함수####################
#
#def average1(scores):
#    sum = 0;
#    for score in scores:
#        sum+= score 
#    avg = sum/len(scores)
#    return avg
#def average2(scores):
#    sum = 0
#    for score in scores:
#        sum+=score
#    avg = sum/len(scores)
#    print(avg)
#    
#print(average1([80,92,93,88,90]))
#average2([80,92,93,88,90])
#############################################
#
#매개변수 위치를 바꿔쓰거나 매개변수 수를 안맞게 넣으면 문제가 발생 
#def buy(goods, count):
#    print("{}을/를 {}개 사다주세요.".format(goods, count))
#print(buy("콜라",5))
#
#예로 들어서 ~
#connect(IP, Port, User, Pass, DB)
#connect("192.168.0.164",3306,"root","P@ssw0rd", "shopping_db")
#
########파라매터 개수가 정해지지 않은 경우##############
#
#def test(args):
#    print(args)
#test(10)
#
#인자 개수 제한 x 
#def test(*args):
#    print(args)
#
#인자 최소 2개 필요 
#def test(arg1, *args):
#    print(arg1,args)
#
#def test(arg1,arg2,*args):
#    print(arg1,arg2,args)
#
###############################################
#예시
#def average(name, *scores):
#    print(name, scores)
#
###############################################
#
#입력된 과목수 만큼 반복하면서 변수 scores 생성
#
#def input_scores(nums):
#    scores=[]
#    for num in range(1, nums+1):
#        score = int(input("과목{}: ".format(num)))
#        scores.append(score)
#    return scores
#
#def input_scores2(): 
#    num = 1
#    scores=[]
#    while True:
#        score = input("과목 {}:".format(num))
#        if score:
#            scores.append(int(score))
#            num+=1
#        else:
#            break
#    return scores
#
#def average(scores):
#    sum = 0
#    for score in scores:
#        sum +=score 
#    avg = sum/len(scores)
#    print("평균점수: {}".format(avg))

#nums = int(input("평균을 구할 과목수:"))
#scores = input_scores(nums)
#scores = input_scores2()
#print(scores)
#
##############################################
#딕셔너리 형태로도 만들수 있다
#def test2(**args):
#    print(args)
#test2(a = 1, b = 2, c = 3)
#
##############################################
#return이 두개일 경우 , 첫번째 리턴후 함수가 그대로 끝난다.
#def add_and_mul(a,b):
#    return a + b
#    return a * b
#
##############################################   
#students = [['홍길동',89,68,72,93,90],
#            ['황진이',90,92,88,85,78],
#            ['누렁이',48,62,55,75,38],
#            ['야옹이',72,62,78,85,83]]

#def average(scores):
#    sum = 0
#    for score in scores:
#        sum+=score
#        avg = sum/len(scores)
#    return avg
#       
#def print_grade(name,avg):            
#    if avg > 60: print("{} 평균: {:.2f}이며 합격입니다.".format(name, avg))
#    else: print("{} 평균: {:.2f}이며 불합격입니다.".format(name, avg))
#                  
#for student in students:
#    name = student[0]
#    scores = student[1:]
#    avg = average(scores)
#    print_grade(name,avg)
##############################################

```