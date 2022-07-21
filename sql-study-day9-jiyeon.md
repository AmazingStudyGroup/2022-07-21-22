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
```
```SQL
```
```SQL
```
```SQL
```























