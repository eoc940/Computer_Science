## DB 트랜잭션(Transaction)

### 트랜잭션이란?

데이터베이스의 상태를 변화시키기 위해 수행하는 작업 단위

상태를 변화시킨다는 것 -> **SQL 질의어를 통해 DB에 접근하는 것**
```
SELECT
INSERT
DELETE
UPDATE
```

작업 단위 -> **많은 SQL 명령문들을 사람이 정하는 기준에 따라 정하는 것**
```
예시) 사용자 A가 사용자 B에게 10000원을 송금한다

* 이때 DB 작업
1. 사용자 A의 계좌에서 10000원 차람 : UPDATE문을 사용해 사용자 A의 잔고를 변경
2. 사용자 B의 계좌로 10000원 추가 : UPDATE문을 사용해 사용자 B의 잔고를 변경

현재 작업 단위 : 출금 UPDATE문 + 입금 UPDATE문
-> 이를 통틀어 하나의 트랜잭션이라고 한다
- 위 두 쿼리문 모두 성공적으로 완료되어야만 "하나의 작업(트랜잭션)"이 완료되는 것이다. 'Commit'
- 작업 단위에 속하는 쿼리 중 하나라도 실패하면 모든 쿼리문을 취소하고 이전 상태로 돌려놓아야한다. 'Rollback'
```

**즉, 하나의 트랜잭션 설계를 잘 만드는 것이 데이터를 다룰 때 많은 이점을 가져다 준다**

### 트랜잭션 특징
- 원자성(Atomicity)
  - 트랜잭션이 DB에 모두 반영되거나, 혹은 전혀 반영되지 않아야 한다
- 일관성(Consistency)
  - 트랜잭션의 작업 처리 결과는 항상 일관성 있어야 한다
- 독립성(Isolation)
  - 둘 이상의 트랜잭션이 동시에 병행 실행되고 있을 때, 어떤 트랜잭션도 다른 트랜잭션 연산에 끼어들 수 없다
- 지속성(Durability)
  - 트랜잭션이 성공적으로 완료되었으면, 결과는 영구적으로 반영되어야 한다

#### Commit
하나의 트랜잭션이 성공적으로 끝났고, DB가 일관성있는 상태일 때 이를 알려주기 위해 사용하는 연산

#### Rollback
하나의 트랜잭션 처리가 비정상적으로 종료되어 트랜잭션 원자성이 깨진 경우

transaction이 정상적으로 종료되지 않았을 때, last consistent state (Transaction의 시작 상태)로 Rollback 할 수 있음

상황이 주어지면 DB 측면에서 어떻게 해결할 수 있을지 대답할 수 있어야 한다

### 트랜잭션 관리를 위한 DBMS 전략
이해를 위한 2가지 개념 : DBMS의 구조 / Buffer 관리 정책

1. DBMS의 구조

크게 2가지 : Query Processor (질의 처리기), Storage System (저장 시스템)

입출력 단위 : 고정 길이의 page 단위로 disk에 읽거나 쓴다

저장 공간 : 비휘발성 저장 장치인 disk에 저장, 일부분을 Main Memory에 저장

![image](https://user-images.githubusercontent.com/67304980/132085211-4c7a065c-2181-480c-a9cb-077cd341af54.png)

2. Page Buffer Manager or Buffer Manager

DBMS의 Storage System에 속하는 모듈 중 하나로, Main Memory에 유지하는 페이지를 관리하는 모듈

Buffer 관리 정책에 따라, UNDO 복구와 REDO 복구가 요구되거나 그렇지 않게 되므로, 트랜잭션 관리에 매우 중요한 결정을 가져온다

3. UNDO

필요한 이유 : 수정된 Page들이 **Buffer 교체 알고리즘에 따라서 디스크에 출력**될 수 있다. Buffer 교체는 **트랜잭션과는
무관하게 buffer의 상태에 따라서 결정된다.** 이로 인해, 정상적으로 종료되지 않은 transaction이 변경한 page들은 원상 복구
되어야 하는데, 이 복구를 undo라고 한다
- 2개의 정책 (수정된 페이지를 디스크에 쓰는 시점으로 분류)
  - steal : 수정된 페이지를 언제든지 디스크에 쓸 수 있는 정책
    - 대부분의 DBMS가 채택하는 Buffer 관리 정책
    - UNDO logging과 복구를 필요로 한다
  - No-steal : 수정된 페이지들을 EOT(End Of Transaction)까지는 버퍼에 유지하는 정책
    - UNDO 작업이 필요하지 않지만, 매우 큰 메모리 버퍼가 필요하다
    
4. REDO

이미 commit한 transaction의 수정을 재반영하는 복구 작업

Buffer 관리 정책에 영향을 받음
- 트랜잭션이 종료되는 시점에 해당 트랜잭션이 수정한 page를 디스크에 쓸 것인가 아닌가로 기준
  - force : 수정했던 모든 페이지를 Transaction commit 시점에 disk에 반영
    - 트랜잭션이 commit 되었을 때 수정된 페이지들이 disk상에 반영되므로 redo 필요 없음
  - No-force : commit 시점에 반영하지 않는 정책
    - 트랜잭션이 disk상의 db에 반영되지 않을 수 있기에 redo 복구가 필요(대부분의 DBMS 정책)

### 트랜잭션의 상태

![image](https://user-images.githubusercontent.com/67304980/132085440-3b18479d-a65a-40ec-976f-0bf351227b77.png)

#### Active
트랜잭션의 활동 상태. 트랜잭션이 실행중이며 동작중인 상태를 말한다

#### Committed
트랜잭션 완료 상태. 트랜잭션이 정상적으로 완료된 상태를 말한다

#### Aborted
트랜잭션이 취소 상태. 트랜잭션이 취소되고 트랜잭션 실행 이전 데이터로 돌아간 상태를 말한다

#### Partially Committed와 Committed의 차이점
Commit 요청이 들어오면 상태는 Partial Committed 상태가 된다. 이후 Commit을 문제없이 수행할 수 있으면 Committed 상태로
전이되고, 만약 오류가 발생하면 Failed 상태가 된다. 즉, Partial Committed는 Commit 요청이 들어왔을때를 말하며, Committed는
Commit을 정상적으로 완료한 상태를 말한다

### 트랜잭션 사용시 주의점
트랜잭션은 꼭 필요한 최소의 코드에만 적용하는 것이 좋다. 즉 트랜잭션 범위를 최소화하라는 의미다. 일반적으로 데이터베이스
커넥션은 개수가 제한적이다. 그런데 각 단위 프로그램이 커넥션을 소유하는 시간이 길어진다면 사용 가능한 여유 커넥션의 개수는
줄어들게 된다. 그러다 어느 순간에는 각 단위 프로그램에서 커넥션을 가져가기 위해 기다려야 하는 상황이 발생할 수도 있는 것이다

### 교착상태
복수의 트랜잭션을 사용하다보면 교착상태가 일어날 수 있다. 교착상태란 두 개 이상의 트랜잭션이 특정 자원(테이블 또는 행)의
잠금(Lock)을 획득한 채 다른 트랜잭션이 소유하고 있는 잠금을 요구하면 아무리 기다려도 상황이 바뀌지 않는 상태가 되는데,
이를 교착상태라고 한다

#### 교착상태의 예(MySQL)
MySQL MVCC에 따른 특성 때문에 트랜잭션에서 갱신 연산(Insert, Update, Delete)를 실행하면 잠금을 획득한다(기본은 행에 대한 잠금)

![image](https://user-images.githubusercontent.com/67304980/132085578-9a1c9100-43d7-417d-8a4d-ddc928bb0014.png)

트랜잭션 1이 테이블 B의 첫 번째 행의 잠금을 얻고 트랜잭션 2도 테이블 A의 첫 번째 행의 잠금을 얻었다고 하자
```
Transaction 1> create table B (i1 int not null primary key) engine = innodb;
Transaction 2> create table A (i1 int not null primary key) engine = innodb;

Transaction 1> start transaction; insert into B values(1);
Transaction 2> start transaction; insert into A values(1);
```
Deadlock이 발생한다. 일반적인 DBMS는 교착상태를 독자적으로 검출해 보고한다

#### 교착 상태의 빈도를 낮추는 방법
- 트랜잭션을 자주 커밋한다
- 정해진 순서로 테이블에 접근한다. 위에서 트랜잭션 1이 테이블 B -> A 의 순으로 접근했고, 트랜잭션 2는 테이블 A -> B 의
순으로 접근했다. 트랜잭션들이 동일한 테이블 순으로 접근하게 한다
- 읽기 잠금 획득 (SELECT ~ FOR UPDATE)의 사용을 피한다
- 한 테이블의 복수 행을 복수의 연결에서 순서 없이 갱신하면 교착상태가 발생하기 쉽다. 이 경우에는 테이블 단위의 잠금을
획득해 갱신을 직렬화 하면 동시성이 떨어지지만 교착상태를 회피할 수 있다















