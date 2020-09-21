---
title: "Python: 정규 표현식 활용3"
last_modified_at: 2020-08-18T20:20:02-05:00
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
# 파일명: 0818-1.py
import re

def find_tag(tag, text):
	pattern = re.compile(f'<{tag}(\s+\w+="\w+")*>.*?</{tag}>', re.DOTALL)
	return pattern.search(text)
def find_tags(tag, text):
	pattern = re.compile(f'<{tag}(\s+\w+="\w+")*>(?P<text>.*?)</{tag}>', re.DOTALL)
	return pattern.finditer(text)

file = open("html\\index.html", "r")
read = file.read()
tags = ["ul", "ol"]
rows = []
for tag in tags:
	tag_text = find_tag(tag, read).group(0)
	li_list = find_tags("li", tag_text)
	row = []
	for li in li_list:
		row.append(li.group("text"))
	rows.append(row)
print(rows)
```
```
# 파일명: 0818-2.py
import re

def find_tag(tag, text):
	pattern = re.compile(f'<{tag}(\s+\w+="\w+")*>.*?</{tag}>', re.DOTALL)
	return pattern.search(text)
def find_tags(tag, text):
	pattern = re.compile(f'<{tag}(\s+\w+="\w+")*>(?P<text>.*?)</{tag}>', re.DOTALL)
	return pattern.finditer(text)

file = open("html\\table1.html", "r")
read = file.read()

tbody_text = find_tag("tbody", read).group(0)
tr_list = find_tags("tr", tbody_text)
rows = []
for tr in tr_list:
	tr_text = tr.group(0)
	td_list = find_tags("td", tr_text)
	row = []
	for td in td_list:
		row.append(td.group("text"))
	rows.append(row)
print(rows)
```
```
# 파일명: 0818-3.py

from bs4 import BeautifulSoup
import requests

#file = open("html\\index.html", "r")
#read = file.read()

response = requests.get("http://192.168.0.100")
content = response.content

rows = []
html = BeautifulSoup(content, 'html.parser')
for tag in ["ul", "ol"]:
	row = []
	li_list = html.find(tag).find_all("li")
	for li in li_list:
		row.append(li.text)
	rows.append(row)

print(rows)
```
```
# 파일명: 0818-4.py

from bs4 import BeautifulSoup

file = open("html\\table1.html", "r")
read = file.read()

html = BeautifulSoup(read, "html.parser")
tr_list = html.find("tbody").find_all("tr")
rows = []
for tr in tr_list:
	td_list = tr.find_all("td")
	row = []
	for td in td_list:
		row.append(td.text)
	rows.append(row)

print(rows)

```