---
title:  "[DB/SQLD] DCL, TCL"
excerpt: "DCL, TCL"
date: 2021-08-02 16:34:00
categories:
  - 데이터베이스
  - SQLD 
tags:
  - 데이터베이스
  - DCL
  - TCL
---

# DCL(Data Control Language)

### 1. GRANT
grant문은 데이터베이스 사용자에게 권한을 부여한다.
데이터베이스 사용을 위해서는 권한이 필요하며 연결, 입력, 수정, 삭제, 조회를 할 수 있다.

```sql
GRANT privileges ON object TO usr;
```
privileges는 권한을 의미하며 object는 테이블명이다.
user는 oracle 데이터베이스 사용자를 지정하면 된다.

**privileges(권한) 종류**
select
insert 
update
delete
references : 지정된 테이블을 참조하는 제약조건을 생성하는 권한을 부여.
alter
index : 지정된 테이블에 인덱스를 생성할 수 있는 권한을 부여.
all

<br>

**with grant option**
* with grant option : 특정 사용자에게 권한을 부여할 수 있는 권한을 부여한다. 권한을 A사용자가 B에 부여하고 B가 다시 C를 부여한 후에 권한을 취소(revoke)하면 모든 권한이 회수된다.

* with admin option : 테이블에 대한 모든 권한을 부여한다. 권한을 A 사용자가 B에 부여하고 B가 다시 C에게 부여한 후에 권한 취소하면 B사용자 권한만 취소된다.

### 2. REVOKE
revoke문은 데이터베이스 사용자에게 부여된 권한을 회수한다.

```sql
REVOKE privileges ON object FROM usr;
```

<br>
<br>

# TCL(Transaction Control Language)

### 1. COMMIT
- commit은 insert, update, delete문으로 변경한 데이터를 데이터베이스에 반영.
- 변경 이전 데이터는 잃어버린다. 즉 A값을 B로 변경하고 commit을 하면 A 값은 잃어버리고 B 값을 반영한다.
- 다른 모든 데이터베이스 사용자는 변경된 데이터를 볼 수 있다.
- commit이 완료되면 데이터베이스 변경으로 인한 lock이 해제(unlock)된다.
- commit이 완료되면 다른 모든 데이터베이스 사용자는 변경된 데이터를 조작할 수 있다.
- commit을 실행하면 하나의 트랜잭션 과정을 종료한다.
- oracle 데이터베이스는 암시적 트랜잭션 관리를 한다. 즉 oracle 데이터베이스로 트랜잭션을 시작하고 트랜잭션의 종료는 oracle 데이터베이스 사용자가 commit 혹은 rollback으로 처리해야 한다.

<br>

**auto commit**
- SQL*PLUS 프로그램을 정상적으로 종료하는 경우 자동 commit 된다.
- DDL 및 DCL을 사용하는 경우 자동 commit 된다.
- "set autocommit on;"을 SQL*PLUS에서 실행하면 자동 commit 된다.

<br>

### 2. ROLLBACK
rollback을 실행하면 데이터에 대한 변경 사용을 모두 취소하고 트랜잭션을 종료한다.
insert, update, delete문의 작업을 모두 취소한다. 단, 이전에 commit한 곳까지만 복구한다.
rollback을 실행하면 lock이 해제되고 다른 사용자도 데이터베이스 행을 조작할 수 있다.

<br>

### 3. SAVEPOINT(저장점)

- savepoint는 트랜잭션을 작게 분할해 관리하는 것으로 savepoint를 사용하면 지정된 위치 이후의 트랜잭션만 rollback 할 수 있다.
- savepoint의 지정은 savepoint <SAVEPOINT명>을 실행.
- 지정된 savepoint까지만 데이터 변경을 취소하고 싶은 경우 "ROLLBACK TO <SAVEPOINT명>"을 실행한다.
- "rollback"을 실행하면 savepoint와 관계없이 데이터의 모든 변경사항을 저장하지 않는다.
