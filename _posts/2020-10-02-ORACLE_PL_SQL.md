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

---
### PL/SQL 제어문
---

PL/SQL도 조건문, 반복문 같은 순차적 제어문이 있다.
{: .notice}

---
#### IF 문
---

```sql
DECLARE
 vn_num1 NUMBER := 1;
 vn_num2 NUMBER := 2;
BEGIN
 IF vn_num1 > vn_num2 THEN
  DBMS_OUTPUT.PUT_LINE(vn_num1 || '이 큰 수');
 ELSEIF vn_num2 > vn_num1
  DBMS_OUTPUT.PUT_LINE(vn_num2 || '이 큰 수');
 ELSE
  DBMS_OUTPUT.PUT_LINE(vn_num1|| '와' || vn_num2 || '이 같은 수');
 END IF;
END;
```
```sql
2이 큰수
```

```sql
DECLARE
 vn_salary NUMBERER := 0;
 vn_department_it NUMBER := 0;
 vn_commission NUMBER := 0;
BEGIN
 vn_department_id := ROUND(DBMS_RANDOM.VALUE (10, 120), -1);
 
  SELECT salary, commission_pct
  INTO vn_salary, vn_commission
  FROM employees
  WHERE department_id = vn_department_id
  AND ROWNUM = 1;
 
DBMS_OUTPUT.PUT_LINE(vn_salary);

 IF vn_commission>0 THEN
  IF vn_commission> 0.15 THEN
   DBMS_OUTPUT.PUT_LINE(vn_salary * vn_commission);
  END IF;
 ELSE
  DBMS_OUTPUT.PUT_LINE(vn_salary);
 END IF;
END;
```

10~120 까지 10의 배수를 랜덤으로 불러와서(부서 번호) 직원 한명의 정보를 가져온다. 그리고 연봉을 출력하고 커미션이 0 보다 크고 0.15보다 작으면 연봉을 한번더 출력,
만약 0.15보다 커미션이 크면 연봉과 커미션을 곱한 값을 출력한다. 
{: .notice}

---
#### CASE 문
---

```sql
DECLARE
 vn_salary NUMBER := 0;
 vn_department_id NUMBER := 0;
BEGIN
 vn_department_id := ROUND(DBMS_RANDOM.VALUE (10,120), -1);
 
  SELECT salary
  INTO vn_salary
  FROM employees
  WHERE department_id = vn_department_id
  AND ROWNUM = 1;
 
 DBMS_OUTPUT.PUT_LINE(vn_salary);
 
 CASE WHEN vn_salary BETWEEN 1 AND 3000 THEN
           DBMS_OUTPUT.PUT_LINE('낮음');
      WHEN vn_salary BETWEEN 3001 AND 6000 THEN
           DBMS_OUTPUT.PUT_LINE('중간');
      WHEN vn_salary BETWEEN 6001 AND 10000 THEN
           DBMS_OUTPUT.PUT_LINE('높음');
      ELSE
           DBMS_OUTPUT.PUT_LINE('최상위');		   
 END CASE;
END;
```

연봉이 출력되고 연봉 액수에 따라서 낮음 ~ 최상위 까지 출력된다.
{: .notice}

---
#### LOOP 문
---

```sql
DECLARE
 vn_base_num NUMBER := 3;
 vn_cnt      NUMBER := 1;
BEGIN
 LOOP
  DBMS_OUTPUT.PUT_LINE(vn_base_num || '*' || vn_cnt || '= ' || vn_base_num * vn_cnt);
  vn_cnt := vn_cnt + 1;
  EXIT WHEN vn_cnt > 9;
 END LOOP;
END; 
```

구구단 3단이 출력된다.
{: .notice}

---
#### WHILE 문
---

```sql
DECLARE
 vn_base_num NUMBER := 3;
 vn_cnt      NUMBER := 1;
BEGIN
 WHILE vn_cnt <=9
 LOOP
  DBMS_OUTPUT.PUT_LINE(vn_base_num || '*' || vn_cnt || '= ' || vn_base_num * vn_cnt);
  vn_cnt := vn_cnt + 1;
 END LOOP;
END; 
```

구구단 3단이 출력된다.
{: .notice}

---
#### FOR 문
---

```sql
DECLARE
 vn_base_num NUMBER := 3;
BEGIN
 FOR i IN 1...9
 LOOP
  DBMS_OUTPUT.PUT_LINE(vn_base_num || '*' || i || '= ' || vn_base_num * i);
 END LOOP;
END; 
```

구구단 3단이 출력된다.
{: .notice}

```sql
DECLARE
 vn_base_num NUMBER := 3;
BEGIN
 FOR i IN REVERSE 1...9
 LOOP
  DBMS_OUTPUT.PUT_LINE(vn_base_num || '*' || i || '= ' || vn_base_num * i);
 END LOOP;
END; 
```

구구단 3단이 거꾸로 출력된다.
{: .notice}

---
#### CONTINUE 문
---
```sql
DECLARE
 vn_base_num NUMBER := 3;
BEGIN
 FOR i IN 1...9
 LOOP
  CONTINUE WHEN i=5;
  DBMS_OUTPUT.PUT_LINE(vn_base_num || '*' || i || '= ' || vn_base_num * i);
 END LOOP;
END; 
```

CONTINUE문으로 인해 i가 5인 경우는 넘어가게된다.
{: .notice}

---
#### GOTO 문
---

```sql
DECLARE
 vn_base_num NUMBER := 3;
BEGIN
 <<third>>
 FOR i IN 1...9
 LOOP
  DBMS_OUTPUT.PUT_LINE(vn_base_num || '*' || i || '= ' || vn_base_num * i);
  IF i = 3 THEN
   GOTO fourth;
  END IF;
 END LOOP;
 
 <<fourth>>
 vn_base_num := 4;
 FOR i IN 1...9
 LOOP
  DBMS_OUTPUT.PUT_LINE(vn_base_num || '*' || i || '= ' || vn_base_num * i);
 END LOOP; 
END; 
```

i가 3일때 까지만 구구단을 수행하고 4단으로 넘어간다.
{: .notice}

---
#### NULL 문
---

```sql
DECLARE
 vn_num1 NUMBER := 1;
 vn_num2 NUMBER := 2;
BEGIN
 IF vn_num1 > vn_num2 THEN
  DBMS_OUTPUT.PUT_LINE(vn_num1 || '이 큰 수');
 ELSEIF vn_num2 > vn_num1
  DBMS_OUTPUT.PUT_LINE(vn_num2 || '이 큰 수');
 ELSE NULL;
 END IF;
END;
```

ELSE NULL을 이용하여 아무것도 수행하지 않는다.
{: .notice}

---
### 그 외,
---

* PRAGMA: 전처리 과정
	* PRAGMA AUTONOMOUS_TRANSACTION: 주 트랜잭션이나 다른 트랜잭션에 영향을 받지 않고 독립적으로 현재 블록 내부에서의 변경사항을 COMMIT이나 ROLLBACK 한다
	* PRAGMA EXCEPTION_INIT(예외명, 예외번호): 컴파일러에 이 예외를 사용한다는 것을 알리는 역할을 한다
	* PRAGMA RESTRICT_REFERECES(서브 프로그램명, 옵션): 패키지에 속한 서브 프로그램 옵션값에 따라 특정 동작을 제한한다
	* PRAGMA SERIALLY_RESUABLE: 패키지 메모리 관리를 쉽게 할 목적으로 사용된다
{: .notice}

PL/SQL 프로그램 상에서 특정 부분에 이름을 부여할 수 있는데 이를 라벨이라 하고 <<라벨명>> 형태로 사용한다.
라벨을 붙이면 GO TO문으로 특정 라벨로 이동할 수 있고 반목문에서 EXIT으로 해당 블록을 빠져나올 수 있도록 할 수도있다. 
{: .notice}


