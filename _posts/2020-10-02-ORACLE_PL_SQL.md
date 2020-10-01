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

```
변수명 데이터타입 := 초깃값;
```
```
상수명 CONSTANT 데이터타입 := 상수값;
```
위와 같이 변수와 상수를 선언할 수 있고 연산자들은 아래와 같다.
{: .notice}
```
연산자                설명
------------------ ---------------
**                 제곱연산자
+,-                양수, 음수 식별 연산자
*,/                곱셈, 나눗셈
+,-,||             덧셈, 뺄셈, 문자열 연결 연산자
=,<,>,<=,>=,<>,!=, 비교 연산자
~=,^=,IS NULL,     비교 연산자
LIKE, BETWEEN, IN  비교 연산자
NOT                논리 연산자
AND                논리 연산자
OR                 논리 연산자
```
AND, OR, NOT 논리연산자도 사용할 수 있다.
{: .notice}

```sql
DECLARE
--한줄 주석
 a INTEGER := 2**2*3**2;
BEGIN
 DBMS_OUTPUT.PUT_LINE('a = ' || TO_CHAR(a));
END;
```
```
a = 36
```

---
### DML 문
---

```sql
DECLARE
 vs_emp_name VARCHAR2(80);
 vs_dep_name VARCHAR2(80);
BEGIN
 SELECT a.emp_name, b.department_name
  INTO vs_emp_name, vs_dep_name
  FROM employees a,
	   departments b
  WHERE a.department_id = b.department_id
   AND a.employee_id = 100;
   
 DBMS_OUTPUT.PUT_LINE(vs_emp_name|| ' - ' || vs_dep_name);
END;
```

```
Steven King - 기획부
```

위 DML문을 아래와 같이 쓸수도 있다.
{: .notice}

```sql
DECLARE
 vs_emp_name employees.emp_name%TYPE;
 vs_dep_name departments.department_name%TYPE;
BEGIN
 SELECT a.emp_name, b.department_name
  INTO vs_emp_name, vs_dep_name
  FROM employees a,
	   departments b
  WHERE a.department_id = b.department_id
   AND a.employee_id = 100;
   
 DBMS_OUTPUT.PUT_LINE(vs_emp_name|| ' - ' || vs_dep_name);
END;
```





