---
title: "Python: 정규 표현식 활용2"
last_modified_at: 2020-08-17T20:20:02-05:00
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
#0817-1.py
import re


#Grouping의 목적1 #############################
# t1 = "hong 010-1234-5678"
# t2 = "hwang 891207-2233555"

# pattern1 = re.compile("\w+\s+\d{6}[-]\d{7}")
# print(pattern1.match(t1))
# print(pattern1.match(t2))
# print(pattern1.match(t2).group())

# pattern2 = re.compile("(\w+)\s+(\d{6}[-]\d{7})")
# print(pattern2.match(t1))
# print(pattern2.match(t2))
# print(pattern2.match(t2).group(0))
# print(pattern2.match(t2).group(1))
# print(pattern2.match(t2).group(2))

# pattern3 = re.compile("(\w+)\s+((\d{6})[-](\d{7}))")
# print(pattern3.match(t1))
# print(pattern3.match(t2))
# print(pattern3.match(t2).group(1))
# print(pattern3.match(t2).group(2))
# print(pattern3.match(t2).group(3))
# print(pattern3.match(t2).group(4))

# print("---------- data.txt -----------")
# file = open("data.txt", "r")
# read = file.read()
# print(read)

# print("------ ssn pattern match ------")
# obj_list= pattern3.finditer(read)
# for obj in obj_list:
    # name = obj.group(1)
    # ssn = obj.group(3)
    # print(name, ssn)
#목적2 반복되는 문자나 단어 찾기#####################
# pattern = re.compile("ca+t")
# print(pattern.match("caaat"))
# print(pattern.match("caaaaaat"))
# print(pattern.match("caaaaaaaaaat"))
# print("----------------------------------------------------")
# pattern1 = re.compile("(ca)+t")
# print(pattern1.match("cat"))
# print(pattern1.match("cacacat"))
# print(pattern1.match("caaaaaaaaaat"))
#############################################

# pattern = re.compile("\w+\s+\w+")
# print(pattern.match("Paris in the the spring")) #띄어쓰기된 첫번째 패턴만 출력

#pattern1 = re.compile(r"(\w+)\s\1+")
#print(pattern1.search("Paris in the the spring")) # 꼭 search로 찾아야함..

# 문제 1. #####################################
#반복되는 단어를 찾아라
#
# text1 = "python is very easy, you need python"
# text2 = "Paris in the the spring"

# pattern1 = re.compile(r"\bpython\b")
# print(pattern1.findall(text1))

# pattern1 = re.compile(r"\bthe\b")
# print(pattern1.findall(text2))
#
#정해진 단어 말고 한문장에 같은 단어가 있을때는? (뒤에 배우는 것으로 다시해보자)

#Grouping된 문자열에 이름 붙이기 #####################
# t1 = "hong 010-1234-5678"
# t2 = "hwang 891207-2233555"
# pattern = re.compile(r"(?P<name>\w+)\s+(?P<ssn>\d{6}[-]\d{7})")
# print(pattern.match(t2).group("name"))
# print(pattern.match(t2).group("ssn"))
#긍정형 전방 탐색 (?) ##############################
#:을 구분자로서는 들어가도 결과에서는 빼고 싶을 때 사용한다.
#(?=빼고싶은 문자)
# pattern = re.compile(".+(?=:)")
# print(pattern.match("http://www.naver.com"))
# print(pattern.match("ftp://ftp.naver.com"))
#
#목적3 OR 연산 ####################################
#pattern = re.compile("file(ABC|XYZ|123)")

################################################
#[.]이 아닌 그냥 . 은 문자라는 것을 잊지말자(\n 제외)
#  .+([.].+)? -> 확장자 형태 
# 책에서는 .*[.].*$ 으로 나타냄
# file1 = "foo.bar"
# file2 = "autoexec.bat"
# file3 = "sendmail.cf"
# file4 = "window.bin"
# file5 = "python"

# pattern = re.compile(r".*[.].*$")
# print(pattern.match(file1))
# print(pattern.match(file2))
# print(pattern.match(file3))
# print(pattern.match(file4))
# print(pattern.match(file5))
#부정형 전방 탐색 (^) ############################
# file1 = "foo.bar"
# file2 = "autoexec.bat"
# file3 = "sendmail.cf"
# file4 = "window.bin"
# file5 = "python"

#아래와 같이 하면 의도하지 않은 결과가 나온다.
#pattern = re.compile(r".*[.][^b].*$") #b로 시작하는 확장자 모두 제외
#pattern = re.compile(r".*[.]([^b]..|.[^a].|..[^t])$") #window.bin, foo.bar 만 찾음
#pattern = re.compile(r".*[.]([^b].?.?|.[^a]?.?|..?[^t]?)$") #원하는 결과가 나온다 bat만 제외
#무슨 뜻이냐면 [b^].?.?은 한글자는 필수로 있고 그 뒤로 두글자는 있을수도 없을 수도 있다.
#.[a^]?.?은 최소 1글자 있고(맨앞 . ) a 부터는 있을수도 없을 수도 있다.
#..?[^t]? 맨앞 한글자 뒤로 ... 마찬가지로 있을수도 있고 없을 수도 있다.

#(?!bat$) ->bat으로 끝나는 것 제외 exe 를 추가하면 (?!bat$|exe$) 으로 추가 할수 있다.
# pattern = re.compile(r".*[.](?!bat$).*$") 

# print(pattern.match(file1))
# print(pattern.match(file2))
# print(pattern.match(file3))
# print(pattern.match(file4))
# print(pattern.match(file5))
#
#문자열 바꾸기 ##################################

# pattern = re.compile("(blue|white|red)")
# text = "blue socks and red shoes"
# print(pattern.findall(text))

#pattern.sub(치환, 원래 문자열)
# print(pattern.sub("colour", text))
# print(pattern.sub("colour", text, count = 1)) #치환할 개수 1개 (앞에서 부터)
# print(pattern.sub("colour", text, count = 2)) #치환할 개수 2개
#################################################
#file = open("data.txt", "r")
#read = file.read()
#print(read)

#pattern = re.compile(r"(?P<name>\w+)\s+(?P<phone>\d+[-]\d+[-]\d+)")
#print(pattern.findall(read),"\n")

#print(pattern.sub("???", read),"\n\n") #pattern으로 찾은 부분 전체를 ???으로 치환한다
#print(pattern.sub("\g<0>", read),"\n\n")
#print(pattern.sub("\g<name>", read),"\n\n") #찾은 부분에서 원하는 부분만 출력할 수 있다. 이름만
#print(pattern.sub("\g<phone>", read),"\n\n") # 번호만 출력
#
#################################################
# file = open("data.txt", "r")
# read = file.read()

# pattern = re.compile(r"(?P<name>\w+)\s+(?P<ssn>(?P<birth>\d{6})[-]\d{7})")
#print(pattern.sub("???",read),"\n\n")
#print(pattern.sub("\g<name>", read),"\n\n")
#print(pattern.sub("\g<ssn>", read),"\n\n")
# print(pattern.sub("\g<name> \g<birth>-*******", read),"\n\n")

#################################################

# file = open("demo1.html", "r", encoding = "utf-8")
# read = file.read()

# pattern = re.compile(r"<title>(.*)</title>")
# print(pattern.search(read).group(1))
################################################
# file = open("demo1.html", "r", encoding = "utf-8")
# read = file.read()

# pattern = re.compile("<ul>.*</ul>", re.DOTALL)
# print(pattern.search(read).group())
#?는 non greedy 문자다 #############################

# file = open("demo1.html", "r", encoding = "utf-8")
# read = file.read()

# pattern = re.compile("<ul>.*?</ul>", re.DOTALL)
# lists = pattern.finditer(read)

# for list in lists:
    # print(list.group(0))
#################################################

file = open("demo1.html", "r", encoding = "utf-8")
read = file.read()

pattern = re.compile("<ul>(.*?)</ul>", re.DOTALL)
pattern2 = re.compile("<li>(.*?)</li>", re.DOTALL)
ul_list = pattern.finditer(read)


final_list=[]

for ul in ul_list:
    li_text_list=[]
    ul_text = ul.group(1)
    li_list = pattern2.finditer(ul_text)
    for li in li_list:
        li_text = li.group(1)
        li_text_list.append(li_text)
        print(li_text)
    final_list.append(li_text_list)
    print("---------------------------------")

print(final_list)

```