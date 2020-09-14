---
title: "Python: for문과 Continue문"
last_modified_at: 2020-07-28T20:20:02-05:00
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
#0728-1.py

##############list 를 이용한 for 문###################
#코드1
#for x in [10,20,30]
#   코드2(x)
#   코드3(x)
#x에 10, 20, 30 을 넣고 코드 들을 실행한다
###################################################
#print("코드1")
#for x in [10, 20, 30]:
#    print("코드2({})".format(x))
#    print("코드3({})".format(x))
#print("코드4")
# 실행 순서: 코드1-코드2(10)-코드3(10)-코드2(20)
#               -코드3(20)-코드2(30)-코드3(30)-코드4
#
#############원소 수 만큼 반복된다######################
#test_list=['one', 'two', 'three']
#for test in test_list:
#    print(test)
#
#for i in [1,2,3,4,5,6,7,8,9,0]:
#    print("안녕하세요.")
#
#################range를 활용#######################
#for i in range(10):
#    print("안녕하세요.")
#
#문제1.##############################################
#
#for i in [1,2,3,4,5,6,7,8,9,10]:
#    print("{}. 안녕하세요.".format(i))
#문제2.##############################################
#
#sum = 0
#for num in [1,2,3,4,5,6,7,8,9,10]:
#    sum += num
#print(sum)
#
#문제3.##############################################
#
#sum = 0
#scores = [82,93,88,72,91]
#for score in scores:
#    sum += score
#avg = sum / len(scores)
#print(avg)
#
####################################################
#max =0
#if s1 > max:
#   max=s1
#

#numbers = [72,88,93,89,79]
#max = 0
#for s in numbers:
#    if s > max:
#        max = s
#print(max)
###################2차원  리스트 출력####################
#               
#B = [(1,2),(3,4,),(5,6)]
#for b in B:
#    print(f"b={b}")
#
#for x,y in B:
#    print(f"x={x}, y={y}")
#
#문제4.###############################################
#
#marks = [90,25,67,45,80]
#number = 0
#for mark in marks:
#    number +=1
#    if mark >= 60:
#        print("{}번학생: 합격".format(number))
#    else:
#        print("{}번학생: 불합격".format(number))
#
#############for문과 Continue문#######################
#
#코드1
#for x in [10,20,30]:
#   코드2(x)
#   if 조건:
#       continue
#   코드3(x)
#코드4
#
#실행순서는? 코드2(10) -> 조건(거짓) -> 코드3(10)
#         코드2(20) -> 조건(참) -> continue (반복을 즉시 종료하고 다음반복으로 넘어간다)
#         코드2(30) -> 조건(거짓) -> 코드3(30)
####################################################
#예시) 합격한 학생만 출력하고 불합격인 경우 넘어가자
#marks = [90,25,67,45,80]
#number = 0
#for mark in marks:
#    number +=1
#    if mark < 60:
#        continue
#    print("{}번학생: 합격".format(number))
#
#################range(start, end)###################
#
#0에서 6까지
#print(list(range(6)))
#3에서 8까지
#print(list(range(3,8)))
#5에서 50까지 5 간격으로
#print(list(range(5,50,5)))
#     
#문제 5.##############################################
#input("1부터 몇 까지 더할까요?")
#입력된 숫자까지의 합은 45입니다.
#
#num = int(input("1부터 몇 까지 더할까요?"))
#for i in range(num):
#    num+=i
#print("입력된 숫자까지의 합은 {} 입니다.".format(num))

#for x in range(2,10):
#    print("----{}단----".format(x))
#    for y in range(1, 10):
#        print("{} x {} = {}".format(x, y, x*y))
#문제 6.##############################################    
#하나의 목록변수에 학생들의 점수를 나열했다.
#
#students = [[89,68,72,93,90],
#            [90,92,88,85,78],
#            [48,62,55,75,38],
#            [72,62,78,85,83]]
#
# 각 학생별 합계: 를 출력하라
#         
#for student in students:
#    sum = 0;
#    for score in student:
#        sum+=score
#    print("합계: {}".format(sum))
#
#문제 7.###############################################
#
#students = [[89,68,72,93,90],
#            [90,92,88,85,78],
#            [48,62,55,75,38],
#            [72,62,78,85,83]]
#          
#for student in students:
#    avg = 0;
#    for score in student:
#        avg+=score
#    avg = avg/len(student)
#    print("평균: {:.2f}".format(avg))
#문제 8.################################################

students = [['홍길동',89,68,72,93,90],
            ['황진이',90,92,88,85,78],
            ['누렁이',48,62,55,75,38],
            ['야옹이',72,62,78,85,83]]
          
#for student in students:
#    avg = 0;
#    for x in range(1,len(student)):
#        avg+=student[x]
#    avg = avg/(len(student)-1)
#    print("{}이의 평균: {:.2f}".format(student[0],avg))

    
for student in students:
    name = student[0]
    scores = student[1:]
    sum = 0
    for score in scores:
        sum+=score
    avg = sum/len(scores)
    if avg > 60: print("{} 평균: {:.2f}이며 합격입니다.".format(name,avg))
    else: print("{} 평균: {:.2f}이며 불합격입니다.".format(name,avg))
```