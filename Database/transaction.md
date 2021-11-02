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
  - 트랜잭션이 DB에 모두 반영되거나, 혹은 전혀 반영되지 않아야 한다(All or Nothing)
  - 연산이 실패하면 이미 실행된 연산들로 바뀌었던 부분들이 다시 원 상태로 바뀐다(Rollback)
  ![image](https://user-images.githubusercontent.com/67304980/135714307-fa8d92b1-76f1-4ae8-a671-f30d89fd5963.png)

- 일관성(Consistency)
  - 트랜잭션의 작업 처리 결과는 항상 일관성 있어야 한다(계좌이체 이후 성호와 은경의 잔액 합은 10000)
  - 무결성 제약 조건으로 정의된 일관성 조건이 있다면 transaction의 구성하는 연산 중 조건을 어기는게 있다면 트랜잭션은 실패
- 독립성, 격리성(Isolation)
  - 둘 이상의 트랜잭션이 동시에 병행 실행되고 있을 때, 어떤 트랜잭션도 다른 트랜잭션 연산에 끼어들 수 없다
  - 즉 하나의 특정 트랜잭션이 완료될 때까지 다른 트랜잭션이 특정 트랜잭션 결과를 참조할 수 
  - 다른 트랜잭션들이 트랜잭션 안의 중간 연산을 볼 수 있다는 것도 의미한다
    - 성호 -> 은경 5000원 이체 중에 관리자가 성호/은경 계좌의 잔액 합을 조회해 보면 항상 10000원이 보장된다
- 지속성(Durability)
  - 트랜잭션이 성공적으로 완료되었으면, 결과는 영구적으로 반영되어야 한다
  - 시스템에 장애가 발생했더라도 성공적으로 수행된 트랜잭션 결과는 데이터베이스에 반영되어 있음을 보장한다
  - 전형적으로 트랜잭션은 업데이트에 대한 로그로 적고 로그가 저장된 후에 트랜잭션이 Commit으로 간주
  - 장애 후 로그 데이터를 가지고 데이터베이스를 재구성(Recovery)

#### Commit
하나의 트랜잭션이 성공적으로 끝났고, DB가 일관성있는 상태일 때 이를 알려주기 위해 사용하는 연산

#### Rollback
하나의 트랜잭션 처리가 비정상적으로 종료되어 트랜잭션 원자성이 깨진 경우

transaction이 정상적으로 종료되지 않았을 때, last consistent state (Transaction의 시작 상태)로 Rollback 할 수 있음

상황이 주어지면 DB 측면에서 어떻게 해결할 수 있을지 대답할 수 있어야 한다

### Transaction Mode
![image](https://user-images.githubusercontent.com/67304980/135714440-78b2606d-7428-44c8-8b8c-0d088e89faaf.png)


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

#### UNDO, REDO, CHECKPOINT 추가설명

트랜잭션이 수행되는 동안 시스템에 알 수 없는 오류 또는 물리적으로 문제가 발생했을 때, 트랜잭션의 수행을 되돌려 놓아야 한다. rollback은 트랜잭션 내의 질의를 수행하면서 문제가 발생했을 경우에 수행되는 것이지만, 시스템의 오류 또는 물리적인 문제의 경우 **시스템 상의 문제이므로 트랜잭션이 다시 수행되어야 한다**. 이를 **시스템 회복**이라 한다. 회복은 데이터 신뢰성을 보장하며, 트랜잭션의 원자성과 영속성을 보장한다

- checkpoint 이후의 log를 보면서 완료되지 않은 트랜잭션에 대해서 수행
- redo : commit 된것을 다시 실행
- undo :  트랜잭션은 시작 됐지만 commit되지 않은 것을 연산 취소

시스템 오류 또는 물리적인 문제를 실패(failure)라고 한다. 실패가 발생하면 시스템의 회복을 위해 undo 또는 redo를 수행한다.

실패가 발생하면 시스테의 회복을 하기 위해 undo 또는 redo를 수행한다
- undo(원 상태로 돌리다)
  - 트랜잭션을 이전 상태로 되돌리는 것을 의미
  - 작업 롤백, 읽기 일관성, 복구
  - 사용자가 했던 작업을 반대로 진행함

- redo(다시하다)
  - 이전 상태로 되돌아간 후, 실패가 발생하기 전까지의 과정을 그대로 따라가는 것을 의미한다
  - redo를 하기 위해서는 정상적으로 실행 되기까지의 과정을 기록해야 하는데, 이를 log라고 한다
  - 복구의 역할, 오라클 서버에 무슨 작업을 하든지 모두 redo에 기록이 된다(undo 포함)
  - 사용자가 했던 작업을 그대로 다시 한다

- checkpoint
  - checkpoint로 설정한 지점 이전까지는 트랜잭션이 성공적으로 수행이 돼서 dist에 확실히 저장된 상태를 의미한다
  - ckeckpoint를 설정하는 이유는 시스템을 회복하기 위해 돌아갈 때 너무 많이 돌아가지 않도록 하기 위함이다
    - 만약 실패가 발생하여 회복을 할 때 checkpoint가 설정되어 있다면, redo는 checkpoint 시점부터 log를 따라가면서 트랜잭션을 수행하지만, undo는 log를 따라가지 않고 트랜잭션을 재수행 합니다.

![image](https://user-images.githubusercontent.com/67304980/139828388-ca171ab8-f8a5-4a31-9031-5daf713cd9d3.png)

- 트랜잭션 T1
  - check point 이전에 실행이 완료되었으므로 failure 되더라도 이미 disk에 저장이 된 상태이므로 회복을 하지 않습니다.
- 트랜잭션 T2
  - check point 이전에 실행된 내용은 disk에 반영이 되었으므로 check point 이후의 내용을 log를 따라가는 redo를 수행합니다.
- 트랜잭션 T3
  - failure 시점에 실행중이였으므로 check point 시점으로 undo한 후, 트랜잭션을 재실행합니다.
- 트랜잭션 T4
  - check point 이후에 실행됐고 failure 시점 전에 실행이 끝났으므로 redo를 수행합니다.
- 트랜잭션 T5
  - check point 이후에 실행됐고 failure 시점에 실행중이였으므로 시작지점까지 undo한 후, 트랜잭션을 재실행합니다.

#### Q. redo와 undo를 어떻게 구분할 수 있을까?

![image](https://user-images.githubusercontent.com/67304980/139829239-0f649d77-ce3d-4146-90a1-31f01c11a91e.png)

회복을 위해 시스템이 restart되면, DB에서 restart file을 읽는다. restart file은 log의 check point record를 참조하고 있으며, check point record에서 undo할 대상을 확인한다

위의 예제를 통해 어떻게 undo와 redo 목록을 정하는지 살펴보자
1. 먼저 checkpoint 시점에서 commit된 트랜잭션은 T2와 T3이다. 따라서 T2와 T3는 undo할 트랜잭션이 된다
2. 이어서 T4가 시작되므로 T4도 undo 리스트에 추가된다
3. 그러다가 undo 리스트에 있던 T2가 종료되므로 T2를 undo 리스트에서 제거하고 redo 리스트에 추가한다
4 다음으로 T5가 시작되므로 T5를 undo 리스트에 추가한다
5 마지막으로 T4는 종료되므로 redo 리스트에 추가된다

이러한 과정을 통해 failure 시점에서 undo list와 redo list에 작성된 트랜잭션에 대해 undo와 redo를 수행한다

트랜잭션 내에서 에러가 발생했을 때는 rollback하고, 시스템 오류와 같은 트랜잭션 외부 요인으로 인해 에러가 발생했을 경우 회복을 한다는 점이 rollback과 recovery의 차이점이다

#### [나의 정리]
> 트랜잭션은 데이터베이스의 상태를 변화시키기 위한 작업 단위이다. 이 기준은 사람이 정하는 논리적인 단위라고 볼 수 있다. 예를 들면 송금의 예시가 있다. A가 B에게 1000원을 송금한다면 A계좌에서 1000원 차감, B계좌에서 1000원 증가 이렇게 두 가지의 작업이 "송금"이라는 논리적인 단위인 것이다. 두 가지 작업 중 하나만 수행하면 논리성에 어긋날 뿐 아니라 데이터베이스 일관성에도 문제가 생기게 된다. 따라서 이 작업들을 묶어 트랜잭션이라고 한다. 개념을 이해했으면 트랜잭션의 4가지 특징을 이해하는 것이 어렵지 않다. 먼저 원자성은 all or nothing의 개념으로, 트랜잭션에 포함된 작업은 전부 수행되거나 전부 수행되지 말아야 한다. 위의 계좌 송금에서 하나의 작업만 수행되서는 안되는 이유가 이것이다. 둘째로 일관성이다. 송금 작업이 이루어졌을 때 A와 B의 계좌 현금 총합은 트랜잭션 이전과 이후가 일치해야 한다. 셋째로 격리성이다. 여러 트랜잭션들이 데이터베이스 상태를 변환하는 작업을 할 때 하나의 트랜잭션에서 작업중인 row나 테이블에 다른 트랜잭션이 동시에 처리할 수 없다는 것이다. 마지막으로 지속성은 변환된 데이터베이스 상태는 영구적으로 반영되어야 한다는 것이다. 데이터베이스의 상태가 영구 반영되지 않는다면 트랜잭션으로 작업을 하는 의미가 없어지기 때문이다. 다음으로는 오류 발생시 DBMS는 어떻게 대응하는지에 대한 것이다. 트랜잭션 수행 도중 트랜잭션 오류가 난다면 rollback되어 트랜잭션 시작 전 상태로 되돌린다. 하지만 트랜잭션 외적인 시스템 오류와 같은 것이 발생하면 체크포인트의 로그를 보며 undo와 redo 작업을 한다. redo는 failure 시점 이전 commit이 완료된 경우로 체크포인트로부터 해당 트랜잭션을 다시 실행한다. undo의 경우 트랜잭션 실행 중 failure가 발생한 것으로 트랜잭션 이전 상태로 돌리는 작업으로 사용자의 작업을 반대로 수행하는 방식이다. 






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

#### [나의 정리]
> 트랜잭션은 가능한 꼭 필요한 최소의 코드에만 적용하는 것이 좋다. 데이터베이스 커넥션의 개수가 제한적이기 때문에 한 트랜잭션이 커넥션을 길게 소유한다면 사용 가능한 커넥션의 수가 줄어들게 된다. 그러면 프로그램에서 커넥션을 가지기 위해 대기해야 하는 상황까지 발생할 수 있다. 또한 트랜잭션을 사용하서 교착상태가 발생할 수 있다. 두 개 이상의 트랜잭션이 행이나 테이블의 락을 획득한 후 다른 트랜잭션이 소유하고 있는 락을 얻기 위해 대기할 경우 발생할 수 있다. 교착상태의 빈도를 낮추는 방법이 몇 가지가 있다. 트랜잭션을 자주 커밋하여 락을 점유하고 있는 시간을 단축한다. 또 정해진 순서로 테이블에 접근하는 것이다. 트랜잭션1이 A->B, 트랜잭션2가 B->A (A,B는 테이블) 순서로 접근한다면 각각의 락을 획득하고 서로의 락을 기다리는 상황이 발생하므로 여러 트랜잭션의 테이블 접근 순서를 일치시켜서 문제를 해결할 수 있다. 또 동시성을 어느 정도 포기하더라도 테이블 단위의 락을 걸어 트랜잭션 처리를 직렬화시키면 교착상태를 회피할 수 있다.



## <다른 정리>

## 트랜잭션

### 데이터 무결성
- 데이터 무결성을 유지하기 위해 해결해야 할 문제들
  - Atomic operation
  - Concurrency control(병행 제어)
  - 장애 후 Recovery(회복)

- Consistent database state
  - 데이터 무결성이 유지되어 데이터베이스 안의 데이터 간에 모순점이 없는 상태
  - 일시적으로 inconsistency가 발생할 수 있으나 결국은 일관된 값으로 유지되어야 한다
    - 예) 은행 계좌 이체

- 트랜잭션
  - 데이터 무결성을 지킴으로써 데이터베이스를 일관된 상태(consistent state)을 유지하기 위한 핵심 개념

### Transaction
- 트랜잭션은 다음의 data processing의 집합을 포함한 atomic operation(unit of work)
  - 하나 이상의 read operation
  - 하나 이상의 write operation
  - 트랜잭션 안에서의 데이터 계산 operation

### ACID
- Atomicity
  - 트랜잭션 안의 모든 operation이 실행되거나 모두 실패

- Consistency
  - 트랜잭션의 실행이 database의 무결성 유지

- Isolation
  - 동시에 실행되는 트랜잭션은 서로 독립적이다(또는 순차적으로 실행)

- Durability
  - 성공적으로 수행된 Transaction은 영원히 반영되어야 함을 의미

![image](https://user-images.githubusercontent.com/67304980/137310292-584c80ab-6f1d-4d51-be52-74632b8abedc.png)


## 트랜잭션 스케줄

- 데이터베이스의 consistent state를 유지하기 위해 동시에 실행되는 트랜잭션들의 operation의 순서를 정하는 걸 말한다
- two update transactions
  - account_no 123456의 현재 balance : 1000
  - T1 : update account set balance = balance + 50 where account_no = 123456;
  - T2 : update account set balance = balance - 100 where account_no = 123456;
  - 두 트랜잭션은 balance를 읽고 값을 update하는 연산으로 이루어져 있다


![image](https://user-images.githubusercontent.com/67304980/137310523-14bf7665-3fae-4ccc-94c5-6fef63d5792c.png)


### Serial or Serializable schedules

- 정확한 결과를 만드는 두 스케줄은 serial schem
- two update transactions
  - account_no 123456의 현재 balance : 1000
  - T1 : update account set balance = balance + 50 where account_no = 123456;
  - T2 : update account set balance = balance - 100 where account_no = 123456;
  - 두 트랜잭션은 balance를 읽고 값을 update하는 연산으로 이루어져 있다
- 정확한 결과를 만드는 두 schedule은 serial schedule
  - 한 트랜잭션의 모든 operation이 끝난 후 다른 트랜잭션이 시작
  - Serial schedule은 항상 consistent 결과 생성
  - 순차적 실행으로 throughput이 좋지 않음
- Serializable schedules
  - 스케줄이 serial schedule과 equivalent할 때 스케줄을 serializable이라 한다
  - Serializable을 계산하기 위한 두 가지 개념
    - conflict serializability
    - view serializability


### Conflict Serializability

- Conflict Operations : 두 operation이 다음 조건을 만족할 때 conflicting이라 할 수 있다
  - 두 개의 다른 트랜잭션에 속해 있고
  - 같은 데이터에 대한 operation이며
  - 적어도 하나의 operation이 write인 경우
- 예제
  - Conflict : S1,S2,S3
  - non-conflict : S4,S5

![image](https://user-images.githubusercontent.com/67304980/139834975-439b3b08-8878-48d2-a04c-77d51a6d85e6.png)

- Conflict serializable
  - non-conflict operation들의 순서 바꿈(swap)으로 serial schedule로 변환이 되는 스케줄

![image](https://user-images.githubusercontent.com/67304980/139835169-c9e18c1e-3784-4f28-94ad-83023417727d.png)


### View Serializability

- View Equivalent
  - 다음 조건들을 만족할 때 스케줄 S1과 S2가 view equivalent하다고 한다
    - 1. 스케줄 안의 어떤 트랜잭션이 같은 초기값을 읽어야 한다
    - 2. 만약 S1에서 T1이 T2가 write한 값을 읽는다면, S2에서도 T1이 T2가 write한 값을 읽어야 한다
    - 3. 마지막 write는 같은 트랜잭션에서 이루어져야 한다

- View Serializable
  - serial 스케줄과 view equivalent한 스케줄
  - conflict serializable인 스케줄을 포함

![image](https://user-images.githubusercontent.com/67304980/137312021-ffb38209-af25-4969-9933-2f4a2b0fba7b.png)

### Precedence graph

- 스케줄의 conflict serializability을 테스트할 때 사용
- 각 트랜잭션이 graph의 노드
- 만약 트랜잭션간에 conflict이 있을 때 directed edge로 표시
  - T2가 write하기 전에 T1이 read할 때
  - T2가 read하기 전에 T1이 write할 때
  - T2가 write하기 전에 T1이 write할 때
- 그래프가 cycle이 없다면 conflict serializable 스케줄

![image](https://user-images.githubusercontent.com/67304980/139835829-9cda5346-d877-4315-a700-f3a6cf825cf1.png)


![image](https://user-images.githubusercontent.com/67304980/137312287-43b546bd-b8be-4e72-b0d5-f3477b7aad7f.png)


### Recoverability

복구 가능한 스케줄(Recoverable Schedule)
- 스케줄은 serializable해야 하고 복구 가능해야 한다
- T2가 T1에 의해 바뀐 데이터를 사용할 경우 T1이 commit 되기 전에 T2는 commit하면 안된다
  - 만약 T1이 abort되면 T2도 abort되어야 한다
- Cascading schedule
  - T1 -> T2 -> T3 -> T4 데이터가 이렇게 사용될 때 만약 T1이 실패하면 모든 트랜잭션 실패 - cascading rollback
  - cascading schedule은 복구 불가능한 스케줄을 만든다
  - cascadeless 스케줄 : 다른 transaction들은 commit 후에 변경된 데이터 read
- cascadeless 스케줄은 recoverable한 스케줄이다



## Recovery

### Crash Recovery(회복)
- Recovery는 다음의 트랜잭션 특성과 관계가 있다
  - Atomicity
    - 트랜잭션의 operation들이 실행 중에 장애 발생하면 실행된 operation들은 rollback이 필요
  - Durability
    - 장애 후에도 commit된 트랜잭션의 데이터는 영원히 저장

![image](https://user-images.githubusercontent.com/67304980/137312973-248d5b6b-7720-4c5f-9cf4-5f8e44ced59d.png)

### Logging & Recovery
- Recovery Algorithm은 장애 후 데이터베이스의 atomicity와 durability을 제공하는 기술
- Log는 데이터베이스의 변경 내용을 저장하고 있는 파일

![image](https://user-images.githubusercontent.com/67304980/137313138-34067041-a58a-4279-b28d-5c0cefaf1c5a.png)



### UNDO 

#### UNDO logging
- 모든 액션은 undo log record 생성(old 값을 포함)
- Disk 업데이트하기 전에 log record를 disk에 적는다(WAL)
- Commit 로그가 flush되기 전 트랜잭션의 모든 체인지는 Disk에 반영

#### UNDO 예제

![image](https://user-images.githubusercontent.com/67304980/137313635-6dcbc2d9-ec6f-48bf-a40e-03073c5aa8b5.png)

![image](https://user-images.githubusercontent.com/67304980/137313671-d2185d90-6fd6-4619-b82d-53497fc576de.png)

![image](https://user-images.githubusercontent.com/67304980/137313710-5ba774be-b75c-4e66-8b85-707332cbbf76.png)

![image](https://user-images.githubusercontent.com/67304980/137313762-6e152148-769c-42f1-8de7-addbc5c3749b.png)

![image](https://user-images.githubusercontent.com/67304980/137313803-e1d8c6bd-861b-415c-a7d0-802f8010f439.png)

![image](https://user-images.githubusercontent.com/67304980/137313842-f1329f61-5a82-4951-b61b-2cd08d88f048.png)


### REDO 

#### REDO logging
- 모든 액션은 redo log record 생성(new 값을 포함)
- Disk 업데이트하기 전에 log record를 disk에 적는다(WAL)
- 모든 log를 commit할 때 flush한다
- 체인지가 disk에 반영된 후에 END log record를 write


#### REDO 예제

![image](https://user-images.githubusercontent.com/67304980/137314289-cb0557eb-d987-4d8d-b739-b45dda38814e.png)

![image](https://user-images.githubusercontent.com/67304980/137314317-598fef7c-8819-4800-aa6e-10a496494a07.png)

![image](https://user-images.githubusercontent.com/67304980/137314366-98a1f895-a618-4366-84dc-82b7876b9a30.png)

### Checkpoint
- DBMS는 주기적으로 체크포인트를 생성하여 시스템 충돌 시 복구에 걸리는 시간을 최소화 -> Checkpoint log 작성
- Fuzzy checkpoint
  - checkpoint하는 동안 긴 시간을 다른 operation을 block하는 걸 막기 위해 checkpoint하는 동안 update는 허용하는 checkpoint 기술

![image](https://user-images.githubusercontent.com/67304980/137314617-e7f961b6-b224-4b81-97b2-5df167b6ab7f.png)

![image](https://user-images.githubusercontent.com/67304980/137314662-121b7ced-8952-4c8b-b02d-6f0b2417bbdf.png)

## 병행 제어

### Lock Protocols

#### Locking protocol in DBMS
- 동시에 실행되는 트랜잭션 간의 serializable 스케줄을 만들어 주기 위한 구현된 메커니즘
- Lock을 획득하기 전에 데이터에 읽거나 쓰는 동작을 할 수 없게 하여 동시성 문제 해결
- Lock-manager in DBMS
  - 모든 lock에 대한 request를 처리
  - lock에 대한 정보를 hash table에 저장하여 관리
- Lock의 종류
  - Shared lock(S)
    - read-only lock
    - read 트랜잭션간에 share 가능
    - write을 위한 exclusive lock인 경우 block
- Lock은 transaction이 commit될 때 release된다

#### Deadlock
- 여러 개의 트랜잭션들이 서로 각각의 lock을 잡고 상대 트랜잭션이 잡은 lock을 잡음으로써 서로 무한정 기다리게 되는 상황
- DBMS의 lock manager는 deadlock detect하여 deadlock이 되는 transaction을 rollback한다

![image](https://user-images.githubusercontent.com/67304980/139837105-d21e2278-6c27-4bbb-a61d-6fd9351d195a.png)

#### Two phase locking protocol
- Serializable 스케줄을 만들기 위해 Lock을 acquire/release 하는 순서를 정하는 protocol
- 트랜잭션에 필요한 모든 lock을 release하기 전에 모두 잡아야 한다
  - Growth phase : lock을 acquire하는 단계
  - Shrinking phase : lock을 release하는 단계

![image](https://user-images.githubusercontent.com/67304980/139837300-daa01e50-2968-41cb-a168-ea95c6657ded.png)


### MVCC

- Locking에서 오는 성능 문제 개선
- 많은 DBMS에서 현재 사용하는 동시성 제어 기법
- 데이터 업데이트가 있을 때 이전의 데이터를 덮어 씌우는게 아니라 새로운 버전의 데이터를 버전 space에 저장하고 link 생성
- 데이터를 조회할 때 트랜잭션의 시작 시점에 맞는 데이터 버전을 반환
  - Version filtering w/ timestamp = Commit Sequence Number(CSN)
  - Read operation은 lock이 필요 없음
  - lock protocol을 사용하는 DBMS보다 좋은 성능을 보여줌
  - 버전에 대한 garbage collection이 필요

![image](https://user-images.githubusercontent.com/67304980/137314967-5b85db7d-f917-4e6e-8fb5-4ef2dc169da4.png)


