---
title: "Python: 정규 표현식"
last_modified_at: 2020-08-14T20:20:02-05:00
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
#0814-1.py
import re
#정규표현식############################################
#file?  file뒤에 한글자만
#a.c    a와 c 사이에 어떠한 문자가 와도된다 a\tc abc a*b  단, \n만 제외
#a[.]c  a.c .을 점으로 본다
#cat+   1문자이상  cat catt cattttt
#cat*   0문자이상 ca cat catttt
#ca?t   a가 있거나 없거나 
#
# ^ 는 처음 $는 끝
#
#[0-9A-Za-z], [0-z_],   \w
#[^0-9A-Za-z], [^0-z_], \W
#[0-9]   \d
#[^0-9]  \D
#[\t\n]  \s
#[^\t\n] \S
#
# $로 검색 구간 정해줄 수 있다. 
#
#
#변수.match()     #match 되는 1개만 찾아준다 (첫부분이 패턴과 다르면 찾지 x), 위치는 알려줌
#변수.search()    #1개만 찾아준다   (중간부터 찾을 수 있다), 위치 알려줌
#변수.findall()   #결과를 목록화 하여 반환 (여러 값 찾아준다 하지만 위치는 알 수 없다)
#변수.finditer()  # 반복가능 for문 사용 가능하다
#
#단계
#1. import re
#2. Pattern 생성   re.compile("패턴")
#3. 문자열 검색      변수.match() 변수.search()
#
#
####################################################
#file[123] file1 file2 file3  (대괄호는 문자 class 라 부르고 괄호안의 문자중에 하나만 붙는다)
#import re
#pat = re.compile("file[456]")
# print(pat.match("file4"))
# print(pat.match("file5"))
# print(pat.match("file6"))

#print(pat.match("file567")) #뒤에 6 7을 뺀 file5를 찾아서 리턴한다.
#match 는 무조건 처음부터 일치해야 찾을 수 있다. 중간에 있는 것도 찾고 싶으면 search를 사용해야한다.
# print(pat.search("asdffile567asdf"))

# pattern1 = re.compile("file[A-Z]")
# pattern2 = re.compile("file[5-9]")

# print(pattern1.match("filea"))
# print(pattern1.match("fileF"))
# print(pattern2.match("file5"))
# print(pattern2.match("file9"))

# pattern3 = re.compile("file[a-zA-Z0-9]")
# print(pattern3.match("filea"))
# print(pattern3.match("fileF"))
# print(pattern3.match("file5"))
# print(pattern3.match("file9"))
#################################################
# pattern = re.compile("file[^a-z]")
# print(pattern.match("filea")) #(못찾음) a-z 를 뺀 것
# print(pattern.match("fileF")) #찾음
# print(pattern.match("file5")) #찾음
# print(pattern.match("file ")) #찾음

# pattern1 = re.compile("file[a-zA-Z0-9_]") #_는 공백 
# pattern2 = re.compile("file[file\w]") #\w는 [a-zA-Z0-9_]  \W 는 [^a-zA-Z0-9_]
# pattern3 = re.compile("file[0-z_]")# 숫자 대문자 소문다 모두 포함 -> \w
############################################################

# pattern1 = re.compile("ca*t") #a가 0개 이상 없어도 된다
# pattern2 = re.compile("ca+t") #a가 1개 이상
# pattern3 = re.compile("ca{3}t") #정확한 횟수를 검사한다
#주민등록번호 앞자리 같은경우 [0-9]{6}
# {n,m} n~m 사이 반복 예로 {2,4} 2개에서 4개 사이 반복 까지만 
# {n,} 최소 개수 (0번 이상)
# {,m} 최대 개수 (m 까지)

# url = re.compile("https?://")
# print(pattern.match("http://www.naver.com"))
# print(pattern.match("https://www.naver.com"))

#############################################################

# pattern = re.compile("https?://")
# print(pattern.match("http://www.naver.com"))
# print(pattern.match("https://www.naver.com"))

# print(type(pattern))
# print(type(pattern.match("http://www.naver.com")))

# 출력 결과.
# C:\Users\authe\OneDrive\바탕 화면\Source>0814-1.py
# <re.Match object; span=(0, 7), match='http://'>
# <re.Match object; span=(0, 8), match='https://'>
# <class 're.Pattern'>
# <class 're.Match'>

#print(dir(pattern))

#dir 결과 
# ['__class__', '__copy__', '__deepcopy__', '__delattr__', 
# '__dir__', '__doc__', '__eq__', '__format__', '__ge__', 
# '__getattribute__', '__gt__', '__hash__', '__init__', 
# '__init_subclass__', '__le__', '__lt__', '__ne__', 
# '__new__', '__reduce__', '__reduce_ex__', '__repr__', 
# '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 
# 'findall', 'finditer', 'flags', 'fullmatch', 'groupindex', 
# 'groups', 'match', 'pattern', 'scanner', 'search', 'split', 
# 'sub', 'subn']
############################################################
#
# pattern = re.compile("[a-z]+")
# print(pattern.match("asdfsafFDFD$adfasf")) 
# print(pattern.match("123123sadfsfDFD"))
# print(pattern.search("123123sadfsfDFD"))
#
###########################################################
#
#pattern = re.compile("[a-z]+")
# text = "abcd1234xyzABCij$"
# print(pattern.match(text))
# print(pattern.search(text))
# print(pattern.findall(text))
# print(pattern.finditer(text))

# a = pattern.match(text)
# print(a.group())  #찾은 문자열
# print(a.start())  #시작 위치
# print(a.end())    #끝 위치
# print(a.span())   #시작과 끝

# pattern = re.compile("[a-z]+")
# text = "abcd1234xyzABCij$"
# print(pattern.finditer(text))

# for obj in pattern.finditer(text):
    # print(obj)

# <re.Match object; span=(0, 4), match='abcd'>
# <re.Match object; span=(8, 11), match='xyz'>
# <re.Match object; span=(14, 16), match='ij'>
#문제 1 #####################################################
#
#
# urls = """http://www.naver.com
# https://www.google.com
# ftp://ftp.kaist.ac.kr
# smtp://mail.naver.com
# smb://smb.window.com
# http://www.ktword.co.kr"""
#
#pattern = re.compile("https?://[a-z.]+")
# pattern = re.compile("https?://[0-z_.]+")
# result=pattern.findall(urls)
# print(result)
#
############################################################

# pattern = re.compile("https?://[0-z_.]+")
# pattern = re.compile("https?://[0-z_.]+", re.MULTILINE) #각줄의 첫 문자 검사
# pattern = re.compile("https?://[0-z_.]+", re.DOTALL) #개행문자 포함

# pattern = re.compile("https?://[0-z_.]+", re.DOTALL)
# pattern = re.compile("https?://[0-z_.]+", re.S) #DOTALL 과 일치한데 여러 줄을에 걸쳐서 찾는다.

#############################################################
# tag1 = '<td class="number">'
# tag2 = '<td class="number"\nid="num">'

# pattern1 = re.compile("<td.*>")
# print(pattern1.match(tag1)) #match='<td class="number">'>
# print(pattern1.match(tag1).group()) #<td class="number">
#pattern1.match(tag2).group() #오류


# pattern1 = re.compile("<td.*>", re.DOTALL) #개행문자 포함
# print(pattern1.match(tag1)) #match='<td class="number">'>
# print(pattern1.match(tag2)) #match='<td class="number"\nid="num">'>

############################################################
# pattern1 = re.compile("a.b")
# pattern2 = re.compile("a.b", re.DOTALL)

# print(pattern1.match("a1b"))
# print(pattern1.match("a b"))
# print(pattern1.match("a     b")) #none

# print(pattern2.match("a1b"))
# print(pattern2.match("a b"))
# print(pattern2.match("a     b")) #none

# print(pattern2.match("a\nb"))
# print(pattern2.match("a\tb"))
# print(pattern2.match("a\n\nb")) #none

#############################################################
# data = """python one
# life is too short
# python two
# you need python
# python three"""


# pattern1 = re.compile("python")
# pattern2 = re.compile("^python")

# print(pattern1.match(data))
# print(pattern2.match(data))


# print(pattern1.findall(data)) #중간에 있는 python도 검색
# print(pattern2.findall(data)) #python으로 시작하는 부분은 맨앞 하나 밖에 없다

# pattern=re.compile("^python\s\w+",re.MULTILINE) #시작은 python으로 그러나 멀티라인으로 찾는다.
# print(pattern.search(data))
# print(pattern.findall(data))

# charref  = re.compile(r"""
# 0b[01]+        #2진수
# | 0o[0-7]+        #8진수
# | 0x[0-9a-f]+     #16진수
# | [0-9]+          #10진수
# """,re.VERBOSE)
############################################################
# text = "abc\sectionxyz"
# pattern = re.compile("\\\\section")
# print(pattern.search(text))

#이런경우 복잡해지니까 r 을 붙여서 raw string으로 전달 한다.
#값전달 보다는 compile 같은 경우에는 해석이 우선이다.

# pattern = re.compile("file[AB]")
# pattern.match("file1")
# pattern.match("fileA")
# pattern = re.compile("file(10|20)")
# pattern.match("file10")
# pattern.match("file20")
# pattern = re.compile("file(AB|CD)")
# pattern.match("fileA")
# pattern.match("fileCD")
############################################################
#pattern = re.compile("(white|black|blue)")
#print(pattern.findall("jlgkerfwhite123black134 123 abc"))
#['white', 'black']
#pattern = re.compile("(white black blue)") #and 와 같다 

# ^를 쓰는경우 대부분 re.MULTILINE을 함께 쓴다.
# MULTILINE이 아니면 그냥 match 쓰면 되니깐
#
############################################################

#t1 = "python is very easy, you need python"
#t2 = "python is very easy\nyou need python\npython abc\nxyz python"

#pat1 = re.compile("^python")
#pat2 = re.compile("\Apython") #파이썬으로 시작하는 ~ 위와 같은 결과를 보인다.

# print(pat1.search(t1))  
# print(pat1.findall(t1))

# print(pat2.search(t1))   
# print(pat2.findall(t1))

#print(pat1.findall(t2), re.MULTILINE)   #MULTILINE이 있어도 ^와 \A와 쓰면 상쇄된다.
#print(pat2.findall(t2), re.MULTILINE)

#마찬가지로 $가 붙어도 어짜피 맨뒤 만 출력하니까 MULTILINE 의미가 없음 상쇄됨
############################################################

#\b 를 사용해보자
#보통 시스템에서는 단어를 구분할때 " " "." "," 으로 구분한다.

# pattern = re.compile("class")
# pattern.search("abc class xyz")
# pattern.search("abc/class.xyz")
# pattern.search("declassfied")

#pattern1 = re.compile("\\bclass\\b")
# pattern1 = re.compile(r"\bclass\b")
# pattern2 = re.compile(" class ") 
# pattern3 = re.compile(r"\bclass\B") #앞은 단어처럼 처리(공백, 특수문자) 뒤는 단어가 x 

# print(pattern1.search("abc class xyz"))
# print(pattern1.search("abc/class.xyz"))
# print(pattern1.search("declassfied")) #none

# print(pattern2.search("abc class xyz"))
# print(pattern2.search("abc/class.xyz"))#none
# print(pattern2.search("declassfied"))#none
###########################################################
#그룹핑 ~
#
# pat1 = re.compile("[ABC]+")
# pat2 = re.compile("ABC+")
# pat3 = re.compile("(ABC)+")

# print(pat1.match("ABABABBACBDABCABC"))  #match 니까 앞에서부터 일치해야 찾는다
# print(pat2.match("ABCCCCC"))
# print(pat3.match("ABCABCABC"))
#
######################################################
import re 
file = open("data.txt", "r")
read = file.read()
#pattern = re.compile("[0-9]{6}[-][0-9]{7}")  #주민등록번호
#pattern = re.compile(r"\w+\s\d+[-]\d+[-]\d+") #id와 전화번호
#pattern = re.compile(r"(\w+)\s(\d+[-]\d+[-]\d+)") #id와 전화번호를 group으로 구분한다
#pattern = re.compile(r"(\w+)\s(\d{6}[-]\d{7})")
pattern = re.compile(r"(\w+)\s((\d{6})[-](\d{7}))") #그룹을 4개로 0은 전체 1은 id 2는 주민등록번호 3은 앞자리 4는 뒷자리

object_list= pattern.finditer(read)

for object in object_list:
    #print(object.group(0)) # 0은 id와 전화번호, 1은 id, 2는 전화번호로 구분되어 저장됨
    name = object.group(1)
    birth = object.group(3)  # id와 주민등록번호인경우도 마찬가지
    #print(object.group(3))
    #print(object.group(4))
    print("{:5} {}-*******".format(name, birth))
#######################################################
```