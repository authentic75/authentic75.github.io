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

mod를 실행하는 함수를 작성해보자
{: .notice--info}

```sql
CREATE OR REPLACE FUNCTION my_mod(num1 NUMBER, num2 NUMBER)
 RETURN NUMBER
IS
 vn_remainder NUMBER := 0;
 vn_quotient  NUMBER := 0;
BEGIN
 vn_quotient := FLOOR(num1/num2);
 vn_remainder := num1 - (num2*vn_quotient);
 
 RETURN vn_remainder;
END;
```

작성한 함수를 호출해보자
{: .notice--info}

```sql
SELECT my_mod(14, 3)
 FROM DUAL;
```

---
### 프로시저
---

함수는 특정 연산을 수행한 뒤 결과 값을 반환하지만 프로시저는 특정한 로직을 처리하기만 하고 결과 값을 반환하지는 않는 서브 프로그램입니다.
다르게 말하면 완성된 테이블에서 데이터를 추출해 원하는대로 조작을 한 후 결과를 다른데 다시 저장하거나 갱신할 때 사용합니다.
{: .notice--info}


```sql
CREATE OR REPLACE PROCEDURE 프로시저 이름 
 (
  매개변수1[IN | OUT | IN OUT] 데이터타입[:= 디폴트 값], 
  매개변수2[IN | OUT | IN OUT] 데이터타입[:= 디폴트 값], 
  ...
  )
IS [AS]
 변수, 상수 등 선언
BEGIN
 실행부

[EXCEPTION
 예외처리부]
END [프로시저 이름];
```

예시로 jobs 테이블에 신규로 JOB을 넣는 프로시저를 만들어보자.
{: .notice--info}

```sql
CREATE OR REPLACE PROCEDURE my_new_job_proc 
 (
  p_job_id IN JOBS.JOB_ID%TYPE,
  p_job_title IN JOBS.JOB_TITLE%TYPE,
  p_min_sal IN JOBS.MIN_SALARY%TYPE,
  p_max_sal IN JOBS.MAX_SALARY%TYPE
  )
IS

BEGIN
 INSERT INTO JOBS(job_id, job_title, min_salary, max_salary, create_date, update_date)
 VALUES (p_job_id, p_job_title, p_min_sal, p_max_sal, SYSDATE, SYSDATE);
 
 COMMIT;
END;
```

생성한 프로시저를 실행해보자.
{: .notice--info}
























