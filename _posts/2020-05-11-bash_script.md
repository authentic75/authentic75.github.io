---
title: "LINUX: bash와 script"
last_modified_at: 2020-05-11T16:20:02-05:00
categories:
  - LINUX
tags:
  - bash
  - LINUX
  - shell
  - script
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
author_profile: true #작성자 프로필 출력여부 #false
read_time: false # read_time을 출력할지 여부 1min read 같은것!
---
### bash의 기본
---
**bash의 특징**  
{: .notice}
```console
[root@ns1 ~]# alias lls=”ls -lh”   #명령어에 별칭을 붙여줄 수 있다. 명령어 단축기능
[root@ns1 ~]# history # ! , !!, !-3  히스토리 기능
[root@ns1 ~]# jobs #job control 기능
[root@ns1 ~]# fg % 1 #job control 기능
```
그 외,  
* 명령문 처리 기능  
* 자동완성 기능 (Tab)  
* 프롬프트 제어 기능  
* 연산 기능  
* 명령 편집 기능  
* 환경 변수 기능
{: .notice}
---
### 환경 변수
---