---
title: "Python: 파이썬 유용한 기능"
last_modified_at: 2020-10-22T20:20:02-05:00
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
---
### 변수 값 swap
---
```
>>> a, b = 1, 10
>>> print(a,b)
1 10
>>> a,b = b, a
>>> print(a,b)
10 1
```

---
### 리스트로 문자열 만들기
---
```
>>> a = ['abc', 'def', 'ghi']
>>> print(''.join(a))
abcdefghi
```
```
#숫자인 경우
>>> a = [1,2,2,3]
>>> print(''.join(map(str,a)))
1223
```

---
### 가장 빈번하게 등장한 요소 찾기
---
```
>>> from collections import Counter
>>> cnt = Counter(a)
>>> print(cnt.most_common(3))
[(1, 5), (2, 2), (3, 2)]
>>> print(cnt.most_common(1))
[(1, 5)]
>>>
```
```
test = [1,2,3,4,2,2,3,1,4,4,4] 
print(max(set(test), key=test.count))
#4
```

---
### 단어의 순서 상관 없이 비교하기
---

```
>>> from collections import Counter
>>> str1 = 'bake'
>>> str2 = 'kabe'
>>> Counter(str1)
Counter({'b': 1, 'a': 1, 'k': 1, 'e': 1})
>>> Counter(str1) == Counter(str2)
True
>>>
```

---
### 문자열, 리스트 거꾸로 출력하기
---

```
>>> a = 'abcdefghijklmnopqrstuvwxyz'
>>> print(a[::-1])
zyxwvutsrqponmlkjihgfedcba
```
```
>>> num = 123456789
>>> print(int(str(num)[::-1]))
987654321
>>>
```
```
>>> for char in reversed(a):
...     print(char)
...
z
y
x
w
v
u
t
<<생략>>
```
```
>>> a = [1,3,3,5,7]
>>> a[::-1]
[7, 5, 3, 3, 1]
>>>
```
---
### 2차원행렬의 전치행렬 구하기(행과 열 바꾸기)
---
```
>>> array2d = [[1,2], [3,4], [5,6]]
>>> transposed = list(zip(*array2d))
>>> transposed
[(1, 3, 5), (2, 4, 6)]
```
---
### 연쇄 비교
---
```
>>> a = 10
>>> 5 < a < 50
True
>>> 10 == a <= 10
True
```
---
### 연쇄 함수 호출
---
```
>>> def product(a, b):
...     return a * b

>>> def add(a, b):
...     return a + b

>>> i = True
>>> (product if i else add)(5, 7)
35
>>> i = False
>>> (product if i else add)(5, 7)
12
```

---
### 리스트의 단순 복사, 얕은 복사
---

```
# 단순복사
>>> a = [1, 2, [3, 4]]
>>> b = a
>>> b
[1, 2, [3, 4]]
>>> b[0] = 10
>>> b 
[10, 2, [3, 4]]
>>> a
[10, 2, [3, 4]] #b의 내용을 바꾸는 순간 a도 바뀐다
```
```
# 얕은 복사 - 1
>>> a = [1, 2, [3, 4]]
>>> b = a[:]
>>> b[0] = 10
>>> b
[10, 2, [3, 4]]
>>> a
[1, 2, [3, 4]] #a의 내용 그대로
```
```
# 얕은 복사 - 2
>>> a = [1, 2, [3, 4]]
>>> b = a.copy()
>>> b[0] = 10
>>> b
[10, 2, [3, 4]]
>>> a
[1, 2, [3, 4]] #a의 내용 그대로
```

---
### 딕셔너리 요소 얻기
---
```
>>> dic = {'a': 1, 'b':2}
>>> dic.get('a')
1
>>> dic.get('d')
>>> dic.get('c', 3) #없는 값 대신 반환할 값 지정
3
```
---
### 값으로 딕셔너리 정렬하기
---
```
# 값으로 정렬하기 - 1 
>>> dic = {'a': 4, 'b':3, 'c':2, 'd':1}
>>> print(sorted(dic))
['a', 'b', 'c', 'd']
>>> print(sorted(dic.items()))
[('a', 4), ('b', 3), ('c', 2), ('d', 1)]
>>> print(sorted(dic.items(), key=lambda x:x[1]))
[('d', 1), ('c', 2), ('b', 3), ('a', 4)]
```
```
# 값으로 정렬하기 - 2
>>> from operator import itemgetter
>>> sorted(dic.items(), key=itemgetter(1))
[('d', 1), ('c', 2), ('b', 3), ('a', 4)]
```
```
# 값으로 정렬하기 - 3
>>> sorted(dic, key=dic.get)
['d', 'c', 'b', 'a']
```

---
### For와 Else (For문이 다 돌아간 경우 실행)
---
```
>>> for ele in [1,2,3,4]:
...     if ele > 4:
...         break
...
...
... else:
...     print('5 이상인 숫자 없음')
```

---
### 리스트를 컴마로 구분한 문자열로 만들기
---

```
>>> li = [1,2,3,4]
>>> ','.join(str(i) for i in li)
'1,2,3,4'
>>> li = [1,'two',3,'four']
>>> ','.join(str(i) for i in li)
'1,two,3,four'
```

---
### 딕셔너리 합치기
---
```
>>> dic1 = {'a':1}
>>> dic2 = {'b':2}
>>> {**dic1, **dic2}
{'a': 1, 'b': 2}
dic1 = {'a':1}
dic2 = {'a':2}
>>> {***dic1, **dic2}
{'a': 2}
```

---
### 리스트의 가장 작은, 가장 큰 값의 index 얻기
---
```
# 가장 작은 값의 index
>>> li = [10,20,30,40]
>>> def min_index(li):
...     return min(range(len(li)), key=li.__getitem__)
...
>>> min_index(li)
0
# 가장 큰 값의 index
>>> def max_index(li):
...     return max(range(len(li)), key=li.__getitem__)
...
>>> max_index(li)
3
```
---
### 리스트에서 중복 값 제거하기
---
```
>>> li = [1,2,3,1,2,3,4]
>>> list(set(li))
[1, 2, 3, 4]
```
```
>>> from collections import OrderedDict
>>> list(OrderedDict.fromkeys(li).keys())
[1, 2, 3, 4]
```
---
### 삼항 조건 연산자
---
```
con = True
if con:
	x = 1
else:
	x = 0
```
```
x = 1 if con else 0
```
```
x = "Success!" if (y == 2) else "Failed!"
```
---
### enumerate()
---
```
a = ['a','b','c']
for idx, data in enumerate(a): print(idx, data)
// 0 a
// 1 b
// 2 c
```
---
### zip()
---
```
a = [1,2,3]
b = [4,5,6]
>>> for i,j in zip(a,b): print(i,j)
// 1 4
// 2 5
// 3 6 #한쪽 길이가 짧은 경우 짧은 쪽을 기준으로 끊긴다.
```
---
### map()
---
```
def upper(s): 
return s.upper() 

mylist = list(map(upper, ['sentence', 'fragment'])) 
print(mylist) 
# ['SENTENCE', 'FRAGMENT'] 

list_of_ints = list(map(int, "1234567")))
print(list_of_ints) 
# [1, 2, 3, 4, 5, 6, 7]
```
```
>>> a = [1,2,3,4]
>>> b = [17,12,11,10]
>>> list(map(lambda x,y: x+y, a,b))
[18, 14, 14, 14]
```
---
### filter()
---
```
>>> foo = [2, 18, 9, 22, 17, 24, 8, 12, 27]
>>> list( filter(lambda x: x % 3 == 0, foo) )
[18, 9, 24, 12, 27]
```
---
### 여러 값을 반환하는 함수
---
```
def test():
   a = 1
   b = 2
   return a, b

v1, v2 = test()
```
---
### 언더스코어 사용하기
---
```
// ex1
a, b, _ = (1,2,3)
// ex2
for _ in range(5):
```
---
### dir()로 객체가 가진 변수와 메소드 보는 법
---
```
>>> dir(datetime)
['__add__', '__class__', '__delattr__', '__dir__', ... ,"tzinfo", ...]
```
---
### 문자열 맨 앞글자 대문자로 바꾸기
---
```
mystring = "10 awesome python tricks" 
print(mystring.title()) 
'10 Awesome Python Tricks'
```
---
### 구분자로 나누어서 리스트로 만들기
---
```
mystring = "The quick brown fox" 
mylist = mystring.split(' ') 
print(mylist) 
# ['The', 'quick', 'brown', 'fox']
```
---
### format 함수
---
```
>>> print("{:*<20}".format("Mint"))
Mint****************
>>> print("{:*^20}".format("Mint"))
********Mint********
>>> print("{:*>20}".format("Mint"))
****************Mint
```
---
### 문자열 구성 파악하기 
---
```
isalnum( ): 문자열이 알파벳 또는 숫자로만 구성되어 있는지 확인
isalpha( ): 문자열이 알파벳으로만 구성되어 있는지 확인
isidentifier( ): 문자열이 식별자로 사용할 수 있는 것인지 확인
isdecimal( ): 문자열이 정수 형태인지 확인
isdigit( ): 문자열이 숫자로 인식될 수 있는 것인지 확인
isspace( ): 문자열이 공백으로만 구성되어 있는지 확인
islower( ): 문자열이 소문자로만 구성되어 있는지 확인
isupper( ): 문자열이 대문자로만 구성되어 있는지 확인
```
---
### find(), rfind()
---
```
output_a = '안녕안녕하세요'.find('안녕') # 왼쪽에서부터 찾음
print(output_a)

output_b = '안녕안녕하세요'.rfind('안녕') # 오른쪽에서부터 찾음
print(output_b)

0
2
```
---
### chr(), ord()
---
```
print chr(65)
#A

print chr(100)
#d

print chr(126)
#~
```
```
print ord('A')
#65

print ord('d')
#100

print ord('~')
#126
```
---
### 람다 함수
---
```
>>> g = lambda x: x**2
>>> print(g(8))
64
>>>
>>> f = lambda x, y: x + y
>>> print(f(4, 4))
8
>>>
```



`[출처] [Python] 알고 가면 써먹을 만한 파이썬 꿀팁 모음|작성자 IML`
`https://medium.com/@chrisjune_13837/`
`출처: https://shared.tistory.com/167 [지식의공유]`
`https://hun931018.tistory.com/53`
`출처: https://offbyone.tistory.com/73 [쉬고 싶은 개발자]`