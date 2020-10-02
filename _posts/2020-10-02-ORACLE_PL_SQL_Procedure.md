---
title: "ORACLE: PL/SQL 함수와 프로시저"
last_modified_at: 2020-10-02T00:26:02-05:00
categories:
  - Database
tags:
  - Database
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---

---
### 사용자 정의 함수
---

이제까지 익명 블록을 사용하여 예시를 작성하였지만 실제로는 익명 블록을 사용하는 경우가 드물다고한다. 한번 실행하고 사라지기 때문에 서브 프로그램을 작성하는데 그 예가 함수이다.
서브 프로그램은 컴파일을 거쳐 데이터베이스 내에 저장되기 떄문에 재사용이 가능하다고 한다.
{: .notice}

함수를 생성하는 방법은 아래와 같다.
{: .notice--info}

```sql
CREATE OR REPLACE FUNCTION 함수이름 (매개변수1, 매개변수2, ...)
RETURN 데이터타입;
IS [AS]
 변수, 상수 등 선언
BEGIN
 실행부
 
 RETURN 반환값;
[EXCEPTION
 예외처리부]
END [함수이름];
```