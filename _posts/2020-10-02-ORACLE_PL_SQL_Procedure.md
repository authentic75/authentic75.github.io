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

---
#### 프로시저 형태
---

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

---
#### 프로시저 생성
---

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

```sql
EXEC my_new_job_proc ('SM_JOB1', 'Sample JOB1', 1000, 5000);
```

---
#### 프로시저 응용
---

그렇지만 위의 경우 PRIMARY KEY가 겹치는 레코드를 삽입할 경우 오류가 발생한다. 따라서 이를 검사하는 문구를 추가해보자.
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
 vn_cnt NUMBER := 0;
BEGIN
 SELECT COUNT(*)
 INTO vn_cnt
 FROM JOBS
 WHERE job_id = p_job_id;
 
 IF vn_cnt = 0 THEN
  INSERT INTO JOBS(job_id, job_title, min_salary, max_salary, create_date, update_date)
  VALUES (p_job_id, p_job_title, p_min_sal, p_max_sal, SYSDATE, SYSDATE);
 ELSE
  UPDATE JOBS
   SET job_title = p_job_title
       min_salary = p_min_sal
	   max_salary = p_max_sal
	   update_date = SYSDATE
   WHERE job_id = p_job_id
 END IF;
 COMMIT;
END;
```

```sql
EXEC my_new_job_proc (p_job_id=> 'SM_JOB1', 
                      p_job_title=> 'Sample JOB1', 
					  p_min_sal => 1000,
					  p_max_sal => 5000);
					  --매개변수명과 값을 연결해줄 수 도 있다.
```

---
#### 매개변수 디폴트 값 설정
---

```sql
CREATE OR REPLACE PROCEDURE my_new_job_proc 
 (
  p_job_id IN JOBS.JOB_ID%TYPE,
  p_job_title IN JOBS.JOB_TITLE%TYPE,
  p_min_sal IN JOBS.MIN_SALARY%TYPE:= 10,
  p_max_sal IN JOBS.MAX_SALARY%TYPE:= 100
  )
...
```

```sql
EXEC my_new_job_proc ('SM_JOB1', 'Sample JOB1');
```

---
#### OUT, IN OUT 매개변수
---

프로시저는 함수와는 다르게 값을 리턴하지 않는다 하였지만 방법이 있긴하다. 실행이 끝나면 전달한 변수를 참조해 값을 가져올 수 있다.
{: .notice--info}

```sql
CREATE OR REPLACE PROCEDURE my_new_job_proc 
 (
  p_job_id IN JOBS.JOB_ID%TYPE,
  p_job_title IN JOBS.JOB_TITLE%TYPE,
  p_min_sal IN JOBS.MIN_SALARY%TYPE,
  p_max_sal IN JOBS.MAX_SALARY%TYPE
  p_upd_date OUT JOBS.UPDATE_DATE%TYPE -- OUT 매개변수
  )
IS
 vn_cnt NUMBEr := 0;
 vn_cur_date JOBS.UPDATE_DATE%TYPE:=SYSDATE; -- date를 저장할 새로운 변수
 
BEGIN
 SELECT COUNT(*)
 INTO vn_cnt
 FROM JOBS
 WHERE job_id = p_job_id;
 
 IF vn_cnt = 0 THEN
  INSERT INTO JOBS(job_id, job_title, min_salary, max_salary, create_date, update_date)
  VALUES (p_job_id, p_job_title, p_min_sal, p_max_sal, SYSDATE, SYSDATE);
 ELSE
  UPDATE JOBS
   SET job_title = p_job_title
       min_salary = p_min_sal
	   max_salary = p_max_sal
	   update_date = vn_cur_date -- 새로운 변수로 날짜 업데이트
   WHERE job_id = p_job_id
 END IF;
 p_upd_date := vn_cur_date -- OUT 매개변수에 할당
 
 COMMIT;
END;
```

```sql
EXEC my_new_job_proc ('SM_JOB1', 'Sample JOB1', 2000, 6000, vd_cur_date);
```

익명 블록에서 프로시저를 실행하려면 EXEC을 빼야한다.
{: .notice--info}

```sql
DECLARE
 vd_cur_date JOBS.UPDATE_DATE%TYPE;
BEGIN
  my_new_job_proc ('SM_JOB1', 'Sample JOB1', 2000, 6000, vd_cur_date);
  
  DBMS_OUTPUT.PUT_LINE(vd_cur_date);
END;
```

매개 변수에 OUT 대신 IN OUT을 입력하면 입력과 출력을 다할수 있는 매개변수가 된다.
{: .notice--info}

---
#### RETURN 문
---

프로시저에서 RETURN문은 프로시저를 빠져나가는 동작을 수행한다.
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
 vn_cnt NUMBEr := 0;
 vn_cur_date JOBS.UPDATE_DATE%TYPE:=SYSDATE;
 
BEGIN
 IF p_min_sal < 1000 THEN
  DBMS_OUTPUT.PUT_LINE('최소 급여값은 1000이상이어야 합니다.');
  RETURN;
 END IF;
 
 SELECT COUNT(*)
 INTO vn_cnt
 FROM JOBS
 WHERE job_id = p_job_id;
 
 COMMIT;
END;
```