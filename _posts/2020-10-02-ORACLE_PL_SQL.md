---
title: "ORACLE: PL/SQL 절차형 쿼리"
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
### PL/SQL 기본구조
---

PL/SQL은 집합적 언어와 절차적 언어의 특징을 모두 가지고 있다. 일반 프로그래밍 언어처럼 변수에 값을 할당하고 예외처리도 할 수 있으며 함수나 프로시저를 만들수도 있다.
{: .notice}

블록: PL/SQL의 기본 단위를 블록이라한다. 이름부, 선언부, 실행부, 예외처리부로 나뉜다.
{: .notice--info}

```sql
이름부
IS(AS)
선언부
BEGIN
실행부
EXCEPTION
예외처리부
END;
```

블록은 이름이 있는 블록과 익명 블록으로 또 나눌 수 있다.
{: .notice--info}


익명 블록을 사용하여 변수에 100을 할당해 보자
{: .notice}
```sql
SQL> DECLARE
       vi_num  NUMBER;
	 BEGIN
	   vi_num := 100;
	   
	   DBMS_OUTPUT.PUT_LINE(vi_num);
	 END;
	 / 
```

SERVEROUTPUT ON을 사용하여 출력해보자
{: .notice}
```sql
SQL> SET SEVEROUTPUT ON
SQL> DECLARE
       vi_num  NUMBER;
	 BEGIN
	   vi_num := 100;
	   
	   DBMS_OUTPUT.PUT_LINE(vi_num);
	 END;
	 / 
100
```

TIMING ON 을 사용하면 명령이 다 수행된 후 시간이 나타난다.
{: .notice}
```sql
SQL> SET SEVEROUTPUT ON
SQL> SET TIMING ON
SQL> DECLARE
       vi_num  NUMBER;
	 BEGIN
	   vi_num := 100;
	   
	   DBMS_OUTPUT.PUT_LINE(vi_num);
	 END;
	 / 
100
```
```
PL/SQL 처리가 정상적으로 완료되었습니다.
경과: 00:00:00.06
```
---
### PL/SQL 구성요소
---




















