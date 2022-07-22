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
```sql
```


#### 그룹화 함수
```SQL
```

#### LISTAGG 함수
```SQL
```

#### PIVOT, UNIPIVOT 함수
```SQL
```

#### 잊기 전에 한번 더!
```SQL
```
