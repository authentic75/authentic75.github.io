---
title: "Python: While문"
last_modified_at: 2020-07-29T20:20:02-05:00
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
#0729-1.py

########################################################
#최고평균점수는 00 이면 누구입니다.
#students = [['홍길동',89,68,72,93,90],
#            ['황진이',90,92,88,85,78],
#            ['누렁이',48,62,55,75,38],
#            ['야옹이',72,62,78,85,83]]
#
#max = 0;
#who = ''; #초기화 할 필요는 없다. 비교할일이 없으니까.
#for student in students:
#    name = student[0]
#    scores = student[1:]
#    sum = 0
#    for score in scores:
#        sum+=score
#    avg = sum/len(scores)
#    if avg > 60: print("{} 평균: {:.2f}이며 합격입니다.".format(name,avg))
#    else: print("{} 평균: {:.2f}이며 불합격입니다.".format(name,avg))
#    if avg > max:
#        max = avg
#        who = name
#print("최고평균점수는 {:.2f}점 이며, {}입니다".format(max,who))
########################################################

##########################매개변수#########################
#
#p143 구구단을 짜볼것이다.
#
#for n in range(2, 10):
#    for m in range(1, 10):
#        print(n*m, end=" ")
#    print(end = "\n")
#
#####################리스트 내포 사용하기#####################
#a = [1,2,3,4]
#result = []
#result = [3,6,9,12] 결과를 가지고 새 목록을 만들어 주고 싶다.
#append 또는 insert로 값을 넣을 수 있다.
#for n in a:
#    result.append(n*3)
#print(result)
###########################또는###########################
#a = [1,2,3,4]
#result = [n*4 for n in a]
#print(result)
#########################################################
#A = [1,2,3,4,5,6]
#result = []
#또는 result = [a*3 for a in A if a%2 ==0]
#for a in A:
#    if a%2 ==0:
#        result.append(a*3)
#print(result)
#########################################################

#######################WHILE문############################
#몇번이 반복될지 모르는 상황, 횟수가 정해져 있지 않을때 사용
#
#코드1
#while 조건:
#   코드2
#   코드3
#코드4
#
#진행: 코드1 -> 조건(참) -> 코드2 -> 코드3
#           -> 조건(참) -> 코드2 -> 코드3
#           -> 조건(참) -> 코드2 -> 코드3
#           .....반복.....
#           -> 조건(거짓) 
#########################################################
#
#for n in range(2,20,2):
#    print(n)
#
#n = 2    
#while n < 20:
#    print(n)
#    n+=2
#문제1.####################################################
#
#
#for treeHit in range(1,11,1):
#    print("나무를 {}번 찍었습니다.".format(treeHit))
#print("나무가 넘어갑니다")
#
#########################################################
#while 문으로 만들어보자
#
#treeHit = 0
#while treeHit < 10:
#    treeHit+=1
#    print("나무를 {}번 찍었습니다.".format(treeHit))
#print("나무가 넘어갑니다")
#
#########################################################
#
#아래와 같이 프롬프트를 만들어 보이는대로 여러줄로 출력할 수 있음.
#prompt = """1. Add
#2. Del
#3. List
#4. Quit
#Enter Number: """
#work = int(input(prompt))
#while work in [1,2,3]:
#    print()
#    work = int(input(prompt))
#문제2.#####################################################
#a 추가 d 삭제 l 리스트 q 종료 
#prompt="원하는 작업(a/d/1/q) 코드: "
#code = input(prompt)
#while code != "q":
#    code = input(prompt)
#print("프로그램을 종료합니다.")
#
#문제3.#######################################################
#prompt="원하는 작업(a/d/1/q) 코드: "
#code = input(prompt)
#while code != "q":
#    if code !="q": print("작업 '{}'을 실행합니다.\n".format(code))
#    code = input(prompt)
#print("프로그램을 종료합니다.")
#############################################################
#여러가지방법이 있다.
#prompt="원하는 작업(a/d/1/q) 코드: "
#code = ""
#while True:
#    code = input(prompt)
#    if code !="q": print("작업 '{}'을 실행합니다.\n".format(code))
#    else: break
#print("프로그램을 종료합니다.")
#
##############################################################
#while True:
#    code = input(prompt)
#    if code =="q": 
#       print("프로그램을 종료합니다.")
#       break
#    print("작업 '{}'을 실행합니다.\n".format(code))
#        
#
##############################################################
#
#커피 자판기 프로그램
#잔량(coffee)이 0이 될때까지 판매를 하고
#0이 되면 판매를 중지한다.
#
#coffee = 10
#money = 300

#while money:
#    print("돈을 받았으니 커피를 줍니다.")
#    coffee -= 1
#    print("남은 커피의 양은 {}개입니다.".format(coffee))
#    if coffee == 0:
#        print("커피가 다 떨어졌습니다. 판매를 중지합니다.")
#        break
###############################################################
#
coffee = 10
money = 300

while True:
    money = int(input("돈을 넣어주세요[잔량: {}]: ".format(coffee)))
    if money == 300:
        print("돈을 받았으니 커피를 줍니다.")
        coffee -= 1
    
    elif money > 300:
        print("거스름돈 {}원을 주고, 커피를 줍니다.".format(money-300))
        coffee -= 1
    
    elif money < 300:
        print("돈을 돌려주고, 커피를 주지 않습니다.")
        continue
        
    if coffee == 0:
        print("커피가 다 떨어졌습니다. 판매를 중지합니다.")
        break

```