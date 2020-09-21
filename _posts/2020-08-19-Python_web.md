---
title: "Python: 웹크롤링"
last_modified_at: 2020-08-19T20:20:02-05:00
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
#0819-1.py
#http://192.168.0.114
#
#
from bs4 import BeautifulSoup
import requests
#
################################################## \\index.html 파일 크롤링
# file = open("html\\table1.html", "r")
# read = file.read()

# html = BeautifulSoup(read, "html.parser")
# tr_list = html.find("tbody").find_all("tr")
# rows = []
# for tr in tr_list:
	# td_list = tr.find_all("td")
	# row = []
	# for td in td_list:
		# row.append(td.text)
	# rows.append(row)

# print(rows)
###################################################

# response = requests.get("http://192.168.0.114")
# html = requests.get("http://192.168.0.114").text
#print(response)
#print(response.headers)
#print(response.headers['Content-Type'])
#print(response.content) #바이너리로 보여준다.
# print("")
# print(html)
##################################################

# url ="http://192.168.0.100/table1.html"
# url1 = "http://192.168.0.114/table1.html"
# url2 = "http://192.168.0.114/table2.html" #한글로 만들었다
# url3 = "http://192.168.0.114/table3.html" #한글로 만들었다

# response = requests.get(url)
# content = response.content #바이너리로 보여준다.

# html = BeautifulSoup(content, "html.parser")
# tr_list = html.find("tbody").find_all("tr")
# rows = []
# for tr in tr_list:
	# td_list = tr.find_all("td")
	# row = []
	# for td in td_list:
		# row.append(td.text)
	# rows.append(row)

# print(rows)
#######################################################
# url ="http://192.168.0.100/table1.html"
# url1 = "http://192.168.0.114/table1.html"
# url2 = "http://192.168.0.114/table2.html" #한글로 만들었다
# url3 = "http://192.168.0.114/table3.html" #한글로 만들었다
#print(requests.get(url1)) # <Response [200]>
#print(requests.get(url1).status_code) #상태 코드만 정수형태로
#print(requests.get(url2))
########################################################
# response = requests.get(url2) #GET을 보내고 응답을 돌려받는다 (보통 변수에 저장하여 사용)
# print(response.headers) #헤더를 사전형 데이터로 반환해줌 header['Content-Type'] 과 같이 키를 입력
# print(response.headers['Date'])
# print(response.headers['Content-Type'])
#print(response.content) #바이너리 형태로 보여줘서 한글이 안나온다.
#print(response.text) #텍스트 그대로 보여줌 (일단은 한글도 나온다)
######################################################  euc-kr
# url ="http://192.168.0.100/table1.html"
# url1 = "http://192.168.0.114/table1.html"
# url2 = "http://192.168.0.114/table2.html" #한글로 만들었다
# url3 = "http://192.168.0.114/table3.html" #한글로 만들었다
#response = requests.get(url3)
#print(response.headers)
#print(response.headers['Content-Type']) #UTF-8 로 출력되는데 잘 못되었다
#print(response.content)
#print(response.text)    #euc-kr 이라서 꺠져서 보인다..!
#print(response.encoding)
#response.encoding = "eucKR" #인코딩을 바꿔주고 나서 보면 보인다!
#print(response.text)
######################################################

#http://192.168.0.114/professors.html

# response = requests.get(url)
# content = response.content #바이너리로 보여준다.

# html = BeautifulSoup(content, "html.parser")
# tr_list = html.find("tbody").find_all("tr")
# rows = []
# for tr in tr_list:
	# td_list = tr.find_all("td")
	# row = []
	# for td in td_list:
		# row.append(td.text)
	# rows.append(row)

# print(rows)
######################################################

# url = "http://192.168.0.114/professors.html"
# response = requests.get(url)
# text = response.text
# html = BeautifulSoup(text, 'html.parser')

# tr_list = html.find("tbody").find_all("tr")
# rows = []

# for tr in tr_list:  #<tr>...</tr> 과 같은 블록이 5개가 들어간다. 
	# td_list = tr.find_all("td")
	# row = []
	# for td in td_list:
		# row.append(td.text)
	# rows.append(row)

# print(rows)
# print(len(tr_list))

######################################################
# tr.find(tag)  <tag>...</tag> 로 구성된 1번째 블록 반환
# tr.find(tag, {"attr":"value"}) -> <tag attr="value">....</tag> 로 구성된 블록 반환
# 즉, 딕셔너리 타입으로 넣는다.

# tr.find("td")  -> <td class="number">1</td> 반환(class 값에 상관 없이 출력)
# 만약 속성도 태그도 같으면
# tr.find("td")[0]
# tr.find("td")[1] 와 같이 찾는 수밖에 없다.

# tr.find("td", {"class":"number"})  -><td class="number">1</td>
# tr.find("td", {"class":"grade"})  -><td class="grade">2</td>

#문제 나간다 !!!! #####################################################

# url = "http://192.168.0.114/professors.html"
# response = requests.get(url)
# text = response.text
# html = BeautifulSoup(text, 'html.parser')

# tr_list = html.find("tbody").find_all("tr")
# rows = []

# for tr in tr_list:  #<tr>...</tr> 과 같은 블록이 5개가 들어간다. 
    # number = tr.find("td", {"class":"number"}).text
    # professor = tr.find("td", {"class":"professor"}).text
    # lecture = tr.find("td", {"class":"lecture"}).text
    # grade = tr.find("td", {"class":"grade"}).text
    # evaluation = tr.find("td", {"class":"evaluation"}).text
    # print(number,"/",professor,"/",lecture,"/",grade,"/",evaluation)

#######################################################################
# import csv

# url = "http://192.168.0.114/professors.html"
# response = requests.get(url)
# text = response.text
# html = BeautifulSoup(text, 'html.parser')
# tr_list = html.find("tbody").find_all("tr")
# rows = []
# values = ["number","professor","lecture","grade","evaluation"]


# file = open("professors.csv", "w", newline="")
# csvfile = csv.writer(file)
# csvfile.writerow(values)

# for tr in tr_list:
    # row = []
    # for value in values:
        # text =tr.find("td",{"class":"{}".format(value)}).text
        # row.append(text)
    # csvfile.writerow(row)
    # print(row)
    
#한번에 저장하는 방법 #####################################################
# import csv

# url = "http://192.168.0.114/professors.html"
# response = requests.get(url)
# text = response.text
# html = BeautifulSoup(text, 'html.parser')
# tr_list = html.find("tbody").find_all("tr")
# rows = []
# values = ["number","professor","lecture","grade","evaluation"]

# rows=[]
# file = open("professors.csv", "w", newline="")
# csvfile = csv.writer(file)
# csvfile.writerow(values)

# for tr in tr_list:
    # row = []
    # for value in values:
        # text =tr.find("td",{"class":"{}".format(value)}).text
        # row.append(text)
    # rows.append(row)

# csvfile.writerows(rows)
# file.close()
#####################################################################

import requests
#url = "http://www.melon.com/chart/index.htm"
# url = "http://www.ktword.co.kr/"
# print(requests.get(url,headers = {"User-Agent":"Mozillar"}))  #wire shark 에서 요청을 찾아보면 mozillar 로 채워져서 보내짐


url = "http://www.melon.com/chart/day/index.htm"
print(requests.get(url,headers = {"User-Agent":"Mozillar"}))  
response = requests.get(url,headers = {"User-Agent":"Mozillar"})
text = response.text

html = BeautifulSoup(text, 'html.parser')

tr_list = html.find("tbody").find_all("tr") #tbody를 거치지 않으면 101로 뜬다
                                            #찾고자 하는 자료에 특정한 속성이 있으면 굳이 td를 안거쳐도된다
rows = []


for tr in tr_list: 
    td_list = tr.find_all("td")
    print(tr.find("span", {"class":"rank"}).text)
    row = []
    for td in td_list:
        row.append(td.text)
    rows.append(row)
    #print(row[1])
```