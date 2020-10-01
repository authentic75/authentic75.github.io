---
title: "ORACLE: 서브쿼리"
last_modified_at: 2020-10-01T00:26:02-05:00
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
### 서브 쿼리
---

서브쿼리 부터는 다시 타이핑하는 걸로... ㅋㅋㅋ 필기량이 너무나도 많다!!  
서브쿼리는 SQL 문장 안에서 보조로 사용되는 또 다른 SELECT문 의미한다.  
{: .notice}

* 서브쿼리도 구분이 가능하다!
	* 메인쿼리와 연관이 없는 쿼리
	* 연관이 있는 서브 쿼리
* 형태에 따라서도 구분이 가능하다!
	* 일반 서브 쿼리 (SELECT 절)
	* 인라인 뷰 (FROM 절)
	* 중첩 쿼리 (WHERE 절)
{: .notice--info}

---
### 연관이 없는 쿼리
---

메인 테이블과 조인 조건이 걸리지 않는 서브 쿼리를 가리킨다.
{: .notice}

```sql
SELECT count(*)
FROM employees
WHERE salary >= (SELECT AVG(salary)
FROM employees);
```
```sql
COUNT(*)
---------
	51
```

```sql
SELECT employee_id, emp_name, job_id
FROM employees
WHERE (employee_id, job_id) IN ( SELECT employee_id, job_id
                                 FROM job_history);
```
```sql
EMPLOYEE_ID EMP_NAME 		JOB_ID
----------- --------------- ----------
200         Jennifer Whalen	AD_ASST
176         Jonathon Taylor	SA_REP
```

SELECT문 이외에도 아래와 같이 사용할 수 있다.
{: .notice}

```sql
UPDATE employees
SET salary = ( SELECT AVG(salary)
               FROM employees);
```
```sql
DELETE employees
WHERE salary >= ( SELECT AVG(salary)
                  FROM employees);
```

---
### 연관성이 있는 서브 쿼리
---

조인 조건이 걸린 서브쿼리를 이용하여 보자.
{: .notice}

```sql
SELECT a.department_id, a.department_name
FROM departments a
WHERE EXISTS( SELECT 1
              FROM job_history b
              WHERE a.department_id = b.department_id );
```
```sql
DEPARTMENT_ID DEPARTMENT_NAME
------------- ---------------
20            마케팅
50            배송부
50            IT
...
```

```sql
SELECT a.employee_id,
( SELECT b.emp_name
  FROM employees b
  WHERE a.employee_id = b.employee_id) AS emp_name,
  a.department_id,
( SELECT b.department_name
  FROM departments b
  WHERE a.department_id = b.department_id) AS dep_name
FROM job_history a;
```

```sql
SELECT a.department_id, a.department_name
FROM departments a
WHERE EXISTS ( SELECT 1
               FROM employees b
               WHERE a.department_id = b.department_id
               AND b.salary > ( SELECT AVG(salary)
                                FROM employees )
			  );
```

마찬가지로 UPDATE, DELETE, MERGE 문에서도 사용가능하다. 아래의 경우를 살펴보자.
{: .notice}

```sql
UPDATE employees a
SET a.salary = ( SELECT salary
                 FROM ( SELECT b.department_id, AVG(c.salary) as sal
                        FROM departments b,
                             employees c
                        WHERE b.parent_id = 90
                        AND b.department_id = c.department_id
                        GROUP BY b.department_id) d
                 WHERE a.department_id = d.department_id)
WHERE a.department_id IN ( SELECT department_id
                           FROM departments
                           WHERE parent_id = 90 );
```

상위 부서가 90번인 정보들을 부서별로 그룹화하고 부서별 평균치 연봉을 계산하고(SET 서브쿼리)  
상위 부서 번호가 90인 부서에 한해서(WHERE 서브쿼리) 모든 사원의 급여를 평균 급여로 SET 한다.(메인쿼리)  
{: .notice}

이를 MERGE문으로 고치면 훨씬 간단해진다. 오라클의 UPDATE문에서는 WHERE 테이블=테이블; 같은 조인을 사용할수가 없기 때문이다.
{: .notice}

```sql
MERGE INTO employees a
USING ( SELECT b.department_id, AVG(c.salary) as sal 
        FROM departments b,
             employees c
        WHERE b.parent_id = 90
        AND b.department_id = c.department_id
        GROUP BY b.department_id ) d
     ON( a.department_id = d.department_id )
WHEN MATCHED THEN
     UPDATE SET a.salary = d.sal;
```

---
### 인라인 뷰
---

FROM에서 사용하는 서브쿼리를 인라인 뷰라고 한다. 면접에서 인라인 뷰이야기가 나온적이 있는데 순간 기억이 안나서 당황했던적이 있다. 이제 확실히 기억하고 있다. 
{: .notice}


```sql
SELECT a.employee_id, a.emp_name, b.department_id, b.department_name
FROM   employees a,
       departments b,
	   ( SELECT AVG(c.salary) AS avg_salary
	     FROM departments b,
		      employees c
         WHERE b.parent_id = 90
		 AND b.department_id = c.department_id) d
WHERE a.department_id = b.department_id
AND a.salary > d.avg_salary;
```

복잡한 쿼리는 다음에 혼자 연습을 더 해봐야겠다.
{: .notice}






