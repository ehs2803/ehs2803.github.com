---
title:  "[DB/SQLD] 계층형 조회"
excerpt: "connect by"
date: 2021-08-03 10:50:00
categories:
  - 데이터베이스
  - SQLD 
tags:
  - 데이터베이스
  - 계층형조회
---

# 계층형조회(Connect by)

계층형조회는 oracle 데이터베이스에서 지원하는 것으로 계층형으로 데이터를 조회할 수 있다.

예를 들어 부장에서 차장, 차장에서 과장, 과장에서 대리, 대리에서 사원 순으로 트리 형태의 구조를 위에서 아래로 탐색하면서 조회하는 것이다. 물론 역방향 조회도 가능하다.


```sql
SELECT MAX(LEVEL)
FROM Limbest.EMP
START WITH MGR IS NULL
CONNECT BY PRIOR ENPNO = MGR;
```
Connect by는 트리 형태의 구조로 질의를 수행하는 것으로 start with구는 시작 조건을 의미하고 connect by prior는 조인 조건이다.
root는 노드로부터 하위 노드의 질의를 실행한다.
계층형 조회에서 MAX(LEVEL)을 사용하여 최대 계층 수를 구할 수 있다. 즉, 계층형 구조에서 마지막 leaf node의 계층값을 구한다.

**connect by 키워드**

|키워드|설명|
|----|------|
|LEVEL|검색 항목의 깊이를 의미. 즉 계층구조에서 가장 상위 레벨이 1이 된다.|
|CONNECT_BY_ROOT|계층구조에서 가장 최상위 값을 표시한다.|
|CONNECT_BY_ISLEAF|계층구조에서 가장 최하위를 표시한다.|
|SYS_CONNECT_BY_PATH|계층구조의 전체 전개 경로를 표시한다.|
|NOCYCLE|순환구조가 발생지점까지만 전개된다.|
|CONNECT_BY_ISCYCLE|순환구조 발생 지점을 표시한다.|


