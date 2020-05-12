---
title: "정보보안기사: 시스템보안"
last_modified_at: 2020-05-11T16:20:02-05:00
categories:
  - Security
tags:
  - 정보보안기사
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
  
{: .notice}
  
{: .notice--info}
  
{: .notice--warning}

`Table1`

| A        | B         | C          |  
| --------- | ---------- | ----------- |  
| a1 | b1| c1 |  
| a2 | b2 | c2 |  
| a3 | b3 | c3 |  

`Table2`

| A        | B         |
| --------- | ---------- |
| a1 | b1 |
| a2 | b2 |
| a3 | b3 |


---
### 운영체제
---
운영체제 5계층  
1. 프로세서 관리  
2. 메모리 관리  
3. 프로세스 관리  
4. 주변장치 관리  
5. 파일 관리  
{: .notice}

---
### 캐시메모리
---
`캐시메모리 교체 알고리즘`

| 종류        | 상세 내용          | 특징          |  
| ---------- | --------------- | ------------ |  
| Random | 교체될 Page를 임의 신청 | Over 헤드가 적다 |  
| FIFO | 캐시 내 오래있었던 page 교체 | 자주 사용되는 Page가 교체될 우려 |  
| LFU | 사용 횟수가 가장 적은 Page | 최근 적재된 page가 교체될 우려 |  
| LRU | 가장 오랫동안 사용되지 않은 Page 교체 | Time Stamping에 의한 Overhead |  
| Optimal | 가장 참조되지 않을 Page 교체 | 실현 불가능 |  
| NUR | 참조 비트와 Modify 비트로 미사용 Page 교체 | 최근 사용되지 않은 페이지 교체 |  
| SCR | 최초 참조 비트 togle (1로 set, 이후 0으로 set 그리고 0일때 교체) | 기회를 한 번 더 줌 |  



























