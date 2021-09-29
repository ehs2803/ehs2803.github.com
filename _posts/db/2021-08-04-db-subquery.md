---
title:  "[DB/SQLD] 서브쿼리"
excerpt: "서브쿼리"
date: 2021-08-04 09:00:00
categories:
  - 데이터베이스
  - SQLD 
tags:
  - 데이터베이스
  - 서브쿼리
---

# Main query와 Subquery

Subquery는 select문 내에 다시 select문을 사용하는 SQL문이다.
subquery형태에는 from구에 select문을 사용하는 인라인뷰, select문에 subquery를 사용하는 스칼라 서브쿼리 등이 있다.
where구에 select문을 사용하면 서브쿼리라고 한다.

from구에 select문을 사용하면 가상의 테이블을 만드는 효과가 있다.

<br>
<br>

# 단일 행 서브쿼리와 다중 행 서브쿼리

서브쿼리는 반환하는 행 수가 한 개인 것과 여러 개인 것에 따라서 단일 행 서브쿼리와 멀티 행 서브쿼리로 분류된다.

**서브쿼리 종류(반환 행)**

|서브쿼리 종류|설명|
|---|------|
|단일 행 서브쿼리(Single row subquery)|서브쿼리를 실행하면 그 결과는 반드시 한 행만 조회되며, 비교연산자 =,<,<=,>,>=,<>를 사용한다.|
|다중 행 서브쿼리(multi row subquery)|서브쿼리를 실행하면 그 결과는 여러 개의 행이 조회되며, 다중 행 비교 연산자인 in, any, all, exists를 사용|

<br>
<br>

# 다중 행 Subquery

**다중 행 비교 연산자**
|다중 행 연산|설명|
|---|------|
|IN|메인쿼리의 비교조건이 서브쿼리의 결과 중 하나만 동일하면 참.|
|ALL|메인쿼리와 서브쿼리의 결과가 모두 동일하면 참. < ALL 는 최솟값을 반환하고, > ALL 은 최댓값을 반환한다.|
|ANY|메인쿼리의 비교조건이 서브쿼리의 결과중 하나이상 동일하면 참. < ANY는 하나라도 크게 되면 참이되고 > ANY는 하나라도 작게 되면 참이 된다.|
|EXISTS|메인쿼리와 서브쿼리의 결과가 하나라도 존재하면 참이 된다.|

<br>
<br>

# 스칼라 subquery

스칼라 서브쿼리는 반드시 한 행과 한 칼럼만 반환하는 서브쿼리이다.
만약 여러행이 반환되면 오류가 발생한다.

```sql
SELECT ENAME AS "이름"
       SAL AS "급여",
       (SELECT AVG(SAL)
        FROM EMP
        ) AS "평균급여"
FROM EMP
WHERE EMPNO=1000;
```
<br>
<br>

# 연관(Correlated) subquery

연관 서브쿼리는 서브쿼리 내에서 메인쿼리 내의 칼럼을 사용하는 것을 의미한다.

```sql
...
FROM EMP a
WHERE a.DEPTNO = 
        (SELECT DEPTNO FROM DEPT b
          WHERE b.DEPTNO=a.DEPTNO);
```
