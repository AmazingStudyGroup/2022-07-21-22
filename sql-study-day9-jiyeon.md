### 07-1 하나의 열에 출력 결과를 담는 다중행 함수 
여러 행을 바탕으로 하나의 결과 값을 도출해내기 위해 사용
```SQL
-- SAL 열 값을 모두 합한 결과 값이 하나의 행으로 출력됨
SELECT SUM(SAL)
  FROM EMP;
```

> 다중행 함수를 사용한 SELECT절에는 기본적으로 여러 행이 결과로 나올 수 잇는 열을 함께 사용할 수 없다.
```SQL
-- 실행되지 못하고 오류발생
SELECT ENAME, SUM(SAL)
  FROM EMP;
```
#### 다중행 함수의 종류     
|함수|설명|
|---|---|
|SUM|지정한 데이터의 합 반환|
|COUNT|지정한 데이터의 개수 반환|
|MAX|지정한 데이터 중 최댓값 반환|
|MIN|지정한 데이터 중 최솟값 반환|
|AVG|지정한 데이터의 평균값 반환|

#### 합계를 구하는 SUM 함수
```SQL
-- 추가 수당 합계 구하기
SELECT SUM(COMM)
  FROM EMP;
```
- 앞서 여러 예제에서 살펴보았듯이, COMM 열에는 NULL이 존재한다. 하지만 SUM 함수는 NULL 데이터는 제외하고 합계를 구한다.    

```SQL
-- DISTINCT : 같은 결과값을 가진 데이터는 합계에서 한 번만 사용
-- ALL : 아무것도 쓰지 않고 사용하는 것과 결과값이 같다.
SELECT SUM(DISTINCT SAL),
       SUM(ALL SAL),
       SUM(SAL)
  FROM EMP;
```
| SAL |
|-----|
|300|
|100|
|250|
|470|
|210|
|300|
- SELECT SUM(DISTINCT SAL) FROM EMP; : 1330
- SELECT SUM(SAL) FROM EMP; : 1630

#### 데이터 개수를 구해주는 COUNT함수
```SQL
-- EMP 테이블의 데이터 개수 출력
SELECT COUNT(*)
  FROM EMP;

-- 부서 번호가 30번인 직원 수 구하기(이처럼 WHERE 절의 조건식과 함께 사용하면 매우 유용하다)
SELECT COUNT(*)
  FROM EMP
 WHERE DEPTNO = 30;

-- DISTINCT 와 ALL 사용 (SUM와 유사)
SELECT COUNT(DISTINCT SAL),
       COUNT(ALL SAL),
       COUNT(SAL)
  FROM EMP;

-- NULL데이터는 반환개수에서 제외(아래 두 개의 출력결과 같음)
SELECT COUNT(COMM)
  FROM EMP;

SELECT COUNT(COMM)
  FROM EMP
 WHERE COMM IS NOT NULL;
```

#### 최댓값과 최솟값을 구하는 MAX, MIN 함수
```SQL
-- 부서번호가 10번인 사원들의 최대급여 출력하기
SELECT MAX(SAL)
  FROM EMP
 WHERE DEPTNO = 10;
```
MAX/MIN 은 앞서 살펴본 COUNT, SUM 함수 처럼 DISTINCT나 ALL을 지정해서 사용할 수 있지만,      
최댓값/최솟값은 데이터 중복 제거와 무관하게 같은 결과같을 반환하기 때문에 실제로는 지정하지 않는다.

- 날짜 데이터에 MAX/MIN 함수 사용하기
```SQL
-- 부서 번호가 20인 사원의 입사일 중 제일 오래된 입사일 출력하기
SELECT MIN(HIREDATE)
  FROM EMP
 WHERE DEPTNO = 20;
```
#### 평균 값을 구하는 AVG 함수
> 숫자 또는 숫자로 암시적 형변환이 가능한 데이터만 사용할 수 있다.
```SQL
-- 부서 번호가 30인 사원들의 평균 급여 출력하기
SELECT AVG(SAL)
  FROM EMP
 WHERE DEPTNO = 30;
```
### 07-2 결과 값을 원하는 열로 묶어 출력하는 GROUP BY절
```SQL
-- 각 부서별 평균 급여 출력하기(하드코딩 ver)
SELECT ABG(SAL) FROM EMP WHERE DEPTNO = 10;
SELECT ABG(SAL) FROM EMP WHERE DEPTNO = 20;
SELECT ABG(SAL) FROM EMP WHERE DEPTNO = 30;
...

-- 집합 연산자를 사용해서 출력하기
SELECT AVG(SAL), '10' AS DEPTNO FROM EMP WHERE DEPTNO = 10
UNION ALL
SELECT AVG(SAL), '20' AS DEPTNO FROM EMP WHERE DEPTNO = 20
UNION ALL
SELECT AVG(SAL), '30' AS DEPTNO FROM EMP WHERE DEPTNO = 30
...
```
#### GROUP BY절의 기본 사용법
- GROUP BY 절에 명시하는 열은 여러 개 지정할 수 있다.     
- 먼저 지정한 열로 대그룹을 나누고, 그 다음 지정한 열로 소그룹을 나눈다.         
- GROUP BY 절에는 별칭이 인식되지 않는다!      
```SQL
-- 부서별 평균 급여 출력하기
SELECT AVG(SAL), DEPTNO
  FROM EMP
GROUP BY DEPTNO;

-- 부서 번호 및 직책별 평균 급여로 정렬하기
SELECT DEPTNO, JOB, AVG(SAL)
  FROM EMP
GROUP BY DEPTNO, JOB
ORDER BY DEPTNO, JOB;
```
- GROUP BY절 사용시 유의점    
다중행 함수를 사용하지 않은 일반 열은 GROUP BY 절에 명시하지 않으면    
SELECT절에서 사용할 수 없다.
```sql
-- GROUP BY 절에는 없는 열을 SELECT 절에 포함했을 경우 : 오류발생
SELECT ENAME, DEPTNO, AVG(SAL)
  FROM EMP
GROUP BY DEPTNO;
```

### 07-3 GROUP BY 절에 조건을 줄 때 사용하는 HAVING절
HAVING절은 SELECT문에 GROUP BY절이 존재할 때만, GROUP BY절 바로 다음에 작성할 수 있다.     
GROUP BY 절을 통해 그룹화된 결과 값의 범위를 제한하는 데 사용한다.
```sql
-- 각 부서의 직책별 평균 급여를 구하되, 그 평균 급여가 2000이상인 그룹만 출력
SELECT DEPTNO, JOB, AVG(SAL)
  FROM EMP
GROUP BY DEPTNO, JOB
  HAVING AVG(SAL) >= 2000
ORDER BY DEPTNO, JOB;
```
- HAVING vs WHERE     
WHERE절은 출력 대상 행을 제한하고, HAVING절은 그룹화된 대상을 출력에서 제한한다.    

#### WHERE절과 HAVING절의 차이점
```sql
-- HAVING절만 사용
SELECT DEPTNO, JOB, AVG(SAL)
  FROM EMP
GROUP BY DEPTNO, JOB
  HAVING AVG(SAL) >=2000
ORDER BY DEPTNO, JOB;
```
|DEPTNO|JOB|AVG(SAL)|
|----|----|-----|
|10|MANAGER|2450|
|10|PRESIDENT|5000|
|20|ANALYST|3000|
|20|MANAGER|2975|
|30|MANAGER|2850|
```sql
-- WHERE절과 HAVING절 모두 사용(WHERE절이 GROUP BY절보다 먼저 실행)
SELECT DEPTNO, JOB, AVG(SAL)
  FROM EMP
 WHERE SAL <= 3000
GROUP BY DEPTNO, JOB
  HAVING AVG(SAL) >=2000
ORDER BY DEPTNO, JOB;
```
|DEPTNO|JOB|AVG(SAL)|
|----|----|-----|
|10|MANAGER|2450|
|10|ANALYST|3000|
|20|MANAGER|2975|
|30|MANAGER|2850|

1) WHERE절을 실행한 후에 나온 결과 데이터가     
2) GROUP BY절을 통해 그룹화가 진행되고,    
3) HAVING절에서 그룹을 제한한다.     

```sql
-- HAVING절을 사용하여 EMP 테이블의 부서별 직책의 평균 급여가 500 이상인
-- 사원들의 부서번호, 직책, 부서별 직책의 평균 급여가 출력되도록 작성해라
SELECT DEPTNO, JOB, AVG(SAL)
  FROM EMP
GROUP BY DEPTNO, JOB
  HAVING AVG(SAL) >= 500
ORDER BY DEPTNO, JOB;
```

















