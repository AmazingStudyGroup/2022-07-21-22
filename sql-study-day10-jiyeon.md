### 07-4 그룹화와 관련된 여러 함수
> 조금 어려운 내용이니까 간단히 훑고, 후에 필요할 때 다시 찾아보기!
#### ROLLUP, CUBE, GROUPING SETS 함수
GROUP BY절에 지정할 수 있는 특수함수들
```sql
-- 기존 GROUP BY절만 사용한 그룹
-- DEPTNO 로 먼저 큰 그룹을 만들고, JOB 으로 소그룹을 나눙 각 그룹에 해당하는 데이터 출력
SELECT DEPTNO, JOB, COOUNT(*), MAX(SAL), SUM(SAL), AVG(SAL)
  FROM EMP
GROUP BY DEPTNO, JOB
ORDER BY DEPTNO, JOB;

-- ROLL UP함수를 적용한 그룹화
-- 각 부서의 직책별 사원수, 최고급여, 급여 합, 평균 급혀 출력 후 각 부서별 결과 출력
-- 마지막에 테이블 전체 데이터를 대상으로 하여 출력
SELECT DEPTNO, JOB, COUNT(*), SUM(SAL), AVG(SAL)
  FROM EMP
GROUP BY ROLLUP(DEPTNO, JOB);
```
> ROLL UP함수에는 그룹함수 지정 불가
```sql
-- CUBE함수를 적용한 그룹화
SELECT DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL)
  FROM EMP
GROUP BY CUBE(DEPTNO, JOB)
ORDER BY DEPTNO, JOB;
```
```
ROLLUP(A, B, C) 
1. A 그룹별 B 그룹별 C 그룹에 해당하는 결과 출력
2. A 그룹별 B 그룹에 해당하는 결과 출력
3. A 그룹에 해당하는 결과 출력
4. 전체 데이터 결과 출력

CUBE(A, B, C)
1. A 그룹별 B 그룹별 C 그룹에 해당하는 결과 출력
2. A 그룹별 B 그룹의 결과 출력 
3. B 그룹별 C 그룹의 결과 출력
4. A 그룹별 C 그룹의 결과 출력
5. A 그룹 결과
6. B 그룹 결과
7. C 그룹 결과
8. 전체 데이터 결과
```
- GROUPING SETS 함수    
계층적으로 그룹화하여 데이터를 집계하는 ROLLUP과 CUBE함수와 달리, GROUPING SETS 함수는 부서별 인원수, 직책별 인원수의 결과 값을 하나의 결과로 출력할 수 있다. 
```sql
SELECT DEPTNO, JOB, COUNT(*)
  FROM EMP
GROUP BY GROUPING SETS(DEPTNO, JOB)
ORDER BY DEPTNO, JOB;
```
> 그룹화를 위해 지정한 열이 계층적으로 분류되지 않고 각각 따로 그룹화한 후 연산을 수행함
|DEPTNO|JOB|COUNT(*)|
|---:|---|----:|
|10||3|
|20||5|
|30||6|
||ANALYST|2|
||CLERK|4|
||MANAGER|3|
||PRESIDENT|1|
||SALESMAN|4|


#### 그룹화 함수
그룹화 함수는 데이터 자체의 가공이나 특별한 연산 기능을 수행하지는 않음     
그룹화 데이터의 식별이 쉽고 가독성을 높이기 위한 목적으로 사용한다.    

- GROUPING 함수
```sql
-- DEPTNO, JOB GROUPING
SELECT DEPTNO, JOB COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL),
       GROUPING(DEPTNO),
       GROUPING(JOB)
  FORM EMP
GROUP BY CUBE(DEPTNO, JOB)
ORDER BY DEPTNO, JOB;
```
> 0 : GROUPING 함수에 지정한 열이 그룹화되었음      
> 1 : 그룹화되지 않은 데이터      

- GROUPING_ID 함수    
  - GROUPING 함수와 마찬가지로, 특정 열의 그룹화 여부를 출력해주는 함수. 하지만 GROUPING함수와 달리 GROUPING_ID 함수는 한번에 여러 열을 지정할 수 있다.     
  - GROUPING_ID 함수를 사용한 결과는 그룹화 비트벡터(grouping bit vector) 값으로 나타낸다.     

> GROUPING_ID(a,b)와 같이 열을 두 개 지정한다면 출력 결과는 다음과 같다.
|그룹화 된 열|그룹화 비트 벡터|최종 결과|
|:---:|:---:|:---:|
|a,b|0 0|0|
|a  |0 1|1|
|b  |1 0|2|
|없음|1 1|3|

#### LISTAGG 함수
그룹에 속해 있는 데이터를 가로로 나열할 때 사용    
```sql
-- 부서별 사원 이름을 나란히 출력
SELECT DEPTNO, 
       LISTAGG(ENAME, ',')
       WITHIN GROUP(ORDER BY SAL DESC) AS ENAMES
  FROM EMP
GROUP BY DEPTNO;
```
|DEPTNO|ENAMES|최종 결과|
|:---:|:---:|:---:|
|a,b|0 0|0|
|a  |0 1|1|
|b  |1 0|2|
|없음|1 1|3|

#### LISTAGG 함수(오라클 11g 버전 부터)
그룹에 속해있는 데이터를 가로로 나열할 때 사용
```SQL
-- 부서별 사원 이름을 나란히 나열하여 출력하기
SELECT DEPTNO,
       LISTAGG(ENAME, ',')
       WITHIN GROUP(ORDER BY SAL DESC) AS ENAMES
  FROM EMP
GROUP BY DEPTNO;
```
|DEPTNO|ENAMES|
|---:|:---:|
|10|KING, CLARK, MILLER|
|20|FORD, SCOTT, JONES, ADAMS, SMITH|
|30|BLAKE, ALLEN, TURNER, MARTIN, WARD, JAMES|

#### PIVOT, UNPIVOT 함수 (오라클 11g 버전 부터)
- PIVOT : 기존 테이블 행을 열로 바꾼다.    
- UNPIVOT : 기존 테이블 열을 행으로 바꾼다
```SQL
-- PIVOT함수를 사용하여 직책별, 부서별 최고 급여를 2차원 표 형태로 출력하기
SELECT *
  FROM(SELECT DEPTNO, JOB, SAL
       FROM EMP)
 PIVOT(MAX(SAL)
       FOR DEPTNO IN (10, 20, 30)
       )
ORDER BY JOB;
```
|JOB|10|20|30|
|--|---:|---|:---:|
|ANALYST||3000||
|CLERK|1300|1100|950|
|MANAGER|2450|2975|2850|
|PRESIDENT|5000|||
|SALESMAN|||1600|
```SQL
SELECT *
  FROM(SELECT JOB, DEPTNO, SAL
        FROM EMP)
 PIVOT(MAX(SAL)
      FOR JOB IN ('CLERK' AS CLERK,
                  'SALESMAN' AS SALESMAN,
                  'PRESIDENT' AS PRESIDENT,
                  'MANAGER' AS MANAGER,
                  'ANALYST' AS ANALYST)
      )
ORDER BY DEPTNO;
```
|DEPTNO|CLERK|SALESMAN|PRESIDENT|MANAGER|ANALYST|
|--:|---:|---:|---:|--:|--:|
|10|1300||5000|2450||
|20||||2975|3000|
|30|950|1600||2850||     

- UNPIVOT 함수는 다른 형태로 제작한 SELECT문의 결과 열을 행 형태로 변환할 때 사용한다.     


#### 잊기 전에 한번 더!
```SQL
-- Q1
SELECT DEPTNO, TRUNC(AVG(SAL)) AS AVG_SAL, 
       MAX(SAL) AS MIN_SAL, COUNT(*) AS CNT
  FROM EMP
GROUP BY DEPTNO;

-- Q2
SELECT JOB, COUNT(*)
  FROM EMP
 GROUP BY JOB
 HAVING COUNT(*) >= 3;

-- Q3
SELECT HIRE_YEAR, DEPTNO, COUNT(*) AS CNT
  FROM EMP
 GROUP BY HIRE_YEAR, DEPTNO;


-- Q4
SELECT NVL2(COMM, 'O', 'X') AS EXIST_COMM, COUNT(*)
  FROM EMP
 GROUP BY NVL2(COMM, 'O', 'X'), COUNT(*);

-- Q5

```
