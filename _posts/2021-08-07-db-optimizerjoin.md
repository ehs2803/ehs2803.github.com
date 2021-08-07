---
title:  "[DB/SQLD] 옵티마이저 조인(optimizer join)"
excerpt: "옵티마이저 조인"
date: 2021-08-07 20:46:00
categories:
  - 데이터베이스
  - SQLD 
tags:
  - 데이터베이스
  - 옵티마이저
---

# 옵티마이저 조인

#### 1. Nested Loop 조인

- 하나의 테이블에서 데이터를 먼저 찾고 그 다음 테이블을 조인하는 방식으로 실행.
- 먼저 조회되는 테이블을 외부테이블(outer table), 그다음 조회되는 테이블을 내부 테이블(innter table)이라고 한다.
- 외부테이블(선행 테이블)의 크기가 작은 것을 먼저 찾는 것이 중요하다. 그래야 데이터가 스캔되늰 범위를 줄일 수 있기 때문이다.
- Random Access가 발생하는데 random access가 많이 발생하면 성능 지연이 발생한다. 그러므로 nested loop 조인은 random access의 양을 줄여야 성능이 향상된다.
- 외부 테이블(선행테이블)의 인덱스를 먼저 검색하고 외부테이블 인덱스에 있는 ROWID를 사용해 외부 테이블을 읽는다. 그 다음에 외부테이블에서 내부테이블의 인덱스를 찾는다. 이 부분을 RANDOM ACCESS라고 한다. 그 후 내부테이블 인덱스를 사용해 내부테이블에서 데이터를 찾는다. 여기까지 실행되면 모든 데이터를 다 찾는다. 그다음은 인출(fetch)을 실행해 전송한다.

```sql
SELECT /*+ ordered use_nl(b) */ *
FROM EMP a, DEPT b
WHERE a.DEPTNO = b.DEPTNO
      AND a.DEPTNO = 10;
```
위의 예는 use_nl 힌트를 사용해 의도적으로 nested loop 조인을 실행했다.

실행계획을 보면 EMP 테이블을 FULL SCAN하고 그다음 DEPT테이블을 FULL SCAN하여 NESTED LOOP 조인을 한다.

ordered 힌트는 FROM절에 나오는 테이블 순서대로 조인을 하게 하는 것이다. ordered힌트는 혼자 사용되지 않고 use_nl, use_merge, use_hash 힌트와 함께 사용된다.

#### 2. Sort Merge 조인

- 두개의 테이블을 sort_area라는 메모리 공간에 모두 로딩하고 sort를 수행한다.
- 두개의 테이블에 대해서 sort가 완료되면 두개의 테이블을 병합(merge)한다.
- sort merge 조인은 정렬이 발생하기 때문에 데이터양이 많아지면 성능이 떨어지게 된다.
- 정렬 데이터양이 너무 많으면 정렬은 임시 영역에서 수행. 임시 영역은 디스크에 있기 때문에 성능이 급격히 떨어진다.
- use_merge 힌트는 or-dered힌트와 같이 사용해야 한다.

#### 3. Hash 조인

- hash 조인은 두개의 테이블 중에서 작은 테이블을 hash 메모리에 로딩하고 두개의 테이블의 조인키를 사용해서 해시 테이블을 생성.
- hash 조인은 해시 함수를 사용해서 주소를 계산하고 해당 주소를 사용해서 테이블을 조인하기 때문에 cpu 연산을 많이 사용.
- 특히 hash 조인 시에는 선행 테이블이 충분히 메모리에 로딩되는 크기여야 된다.

<br>
<br>

# 실행계획(Execution Plan)

```sql
SELECT *
FROM EMP, DEPT
WHERE EMP.DEPTNO = DEPT.DEPTNO
      AND EMP.DEPTNO = 10;
```
**ALL_ROWS는 비용 기반 옵티마이저를 의미**

실행 계획을 읽는 방법은 번호 순서대로 읽으면 된다.

1번 : DEPT 테이블의 SYS_C007959 인덱스를 유일하게 조회(INDEX UNIQUE SCAN)

2번 : INDEX에서 DEPT 테이블 ROWID를 사용해서 조회

3번 : EMP 테이블을 전체 스캔(FULL SCAN)

4번 : DEPT테이블과 EMP테이블을 NESTED LOOP 방식의 조인을 해서 최종 결과를 만들어 낸다.

NESTED LOOP 방식의 조인은 DEPT테이블에서 먼저 데이터를 찾고 그다음 EMP 테이블을 찾는 것을 의미. 이런 것을 RANDOM ACCESS라고 한다.
