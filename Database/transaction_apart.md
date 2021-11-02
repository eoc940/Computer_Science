## 트랜잭션 격리(Transaction Isolation Level)

### Isolation level

트랜잭션에서 일관성 없는 데이터를 허용하도록 하는 수준

### Isolation level의 필요성

데이터베이스는 ACID 특징과 같이 트랜잭션이 독립적인 수행을 하도록 한다

따라서 Locking을 통해 트랜잭션이 DB를 다루는 동안 다른 트랜잭션이 관여하지 못하도록 막는 것이 필요하다

하지만 무조건 Locking으로 동시에 수행되는 수많은 트랜잭션들을 순서대로 처리하는 방식으로 구현하게 되면 데이터베이스의 
성능은 떨어지게 될 것이다

그렇다고 해서, 성능을 높이기 위해 Locking의 범위를 줄인다면, 잘못된 값이 처리될 문제가 발생하게 된다

따라서 최대한 효율적인 Locking 방법이 필요하다


### Isolation level 종류

**1. Read Uncommitted (레벨 0)**
  > SELECT 문장이 수행되는 동안 해당 데이터에 Shared Lock이 걸리지 않는 계층
- 트랜잭션에 처리중이거나, 아직 Commit되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용함
  - 사용자1이 A라는 데이터를 B라는 데이터로 변경하는 동안 사용자2는 아직 완료되지 않은(Uncommitted) 트랜잭션이지만 데이터B를 읽을 수 있다
- 데이터의 일관성을 유지하는 것이 거의 불가능하다
- Dirty Read, Non-Repeatable Read, Phantom Read

- 어떤 트랜잭션의 변경내용이 commit이나 rollback과 상관없이 다른 트랜잭션에서 보여진다
  - 1. A 트랜잭션에서 10번 사원의 나이를 27에서 28로 바꿈
  - 2. 아직 커밋하지 않음
  - 3. B 트랜잭션에서 10번 사원의 나이를 조회함
  - 4. 28이 조회됨. 이를 더티 리드라고 한다
  - 5. A 트랜잭션에서 문제가 발생해 rollback함
  - 6. B 트랜잭션은 10번 사원이 여전히 28살이라고 생각하고 로직을 수행함

- 이런식으로 데이터 정합성에 문제가 많으므로 RDBMS 표준에서는 격리수준으로 인정하지도 않는다


**2. Read Committed (레벨 1)**
  > SELECT 문장이 수행되는 동안 해당 데이터에 Shared Lock이 걸리는 계층
- 트랜잭션이 수행되는 동안 다른 트랜잭션이 접근할 수 없어 대기하게 된다
- Commit이 이루어진 트랜잭션만 조회 가능
  - 사용자1이 A라는 데이터를 B라는 데이터로 변경하는 동안 사용자2는 해당 데이터에 접근이 불가능함
- SQL 서버가 Default로 사용하는 Isolation level이다
- Non-Repeatable Read, Phantom Read

- 어떤 트랜잭션의 변경 내용이 commit 되어야만 다른 트랜잭션에서 조회할 수 있다
- Oracle DBMS가 기본으로 사용하고, 온라인 서비스에서 가장 많이 선택되는 격리수준이다
- 여기서는 B 트랜잭션이 10번 사원의 나이를 조회해도 27살이 조회된다(커밋되지 않았기 때문)(이는 undo 영역에 저장된 데이터이다). A 트랜잭션에서 최종 커밋하면 B 트랜잭션에서 28살 이라는 값을 받아볼 수 있다
- 정합성 문제가 해결된 것 같지만 NON-REPEATABLE-READ 부정합 문제가 있다
  - 1. B 트랜잭션에서 10번 사원 나이를 조회
  - 2. 27살이 조회됨
  - 3. A 트랜잭션에서 10번 사원의 나이를 27살에서 28살로 바꾸고 커밋
  - 4. B 트랜잭션에서 10번 사원의 나이를 다시 조회(변경되지 않은 이름이 조회됨)
  - 5. 28살이 조회됨
- 이는 하나의 트랜잭션 내에서 똑같은 SELECT를 수행했을 경우 항상 같은 결과를 반환해야 한다는 REPEATABLE READ 정합성에 어긋난다
- 작업이 금전적인 처리와 연결되어 있다면 문제가 발생할 수 있다.



**3. Repeatable Read (레벨 2)**
  > 트랜잭션이 완료될 때까지 SELECT 문장이 사용하는 모든 데이터에 Shared Lock이 걸리는 계층
- 트랜잭션이 범위 내에서 조회한 데이터 내용이 항상 동일함을 보장한다
- 다른 사용자는 트랜잭션 영역에 해당되는 데이터에 대한 수정 불가능하다
- Phantom Read

- **트랜잭션이 시작되기 전에 커밋된 내용에 대해서만 조회할 수 있는 격리수준**이다. MySQL에서 기본으로 사용하고, NON REPEATABLE READ 부정합이 발생하지 않는다
- 내용을 살펴보자
  - 1. 10번 트랜잭션이 500번 사원 조회
  - 2. 12번 트랜잭션이 500번 사원 이름 변경하고 커밋
  - 3. 10번 트랜잭션이 500번 사원 다시 조회
  - 4. undo 영역에 백업된 데이터 반환
- 모든 InnoDB의 트랜잭션은 고유한 트랜잭션 번호(순차적 증가)를 갖고 있으며 undo 영역에 백업된 모든 레코드는 변경을 발생시킨 트랜잭션의 번호가 포함되어 있다. 따라서 자신의 트랜잭션 번호보다 낮은 트랜잭션 번호에서 변경된(+커밋된) 것만 보게 된다

이 격리 수준의 두 가지 문제가 있다

1. UPDATE 부정합
```
START TRANSACTION; -- transaction id : 1
SELECT * FROM Member WHERE name='junyoung';

    START TRANSACTION; -- transaction id : 2
    SELECT * FROM Member WHERE name = 'junyoung';
    UPDATE Member SET name = 'joont' WHERE name = 'junyoung';
    COMMIT;

UPDATE Member SET name = 'zion.t' WHERE name = 'junyoung'; -- 0 row(s) affected
COMMIT;
```
이 상황에서 최종 결과는 name = joont가 된다.
REPETABLE READ이기 때문에,
2번 트랜잭션에서 name = joont로 변경하고 COMMIT을 하면 name = junyoung의 내용을 언두로그에 남겨놔야 한다.
그래야 1번 트랜잭션에서 일관되게 데이터를 보는 것을 보장해줄 수 있기 때문이다.

이 상황에서 아래 구문에서 UPDATE 문을 실행하게 되는데, UPDATE의 경우 변경을 수행할 로우에 대해 잠금이 필요하다.
하지만 현재 1번 트랜잭션이 바라보고 있는 name = junyoung 의 경우 레코드 데이터가 아닌 언두영역의 데이터이고,
언두영역에 있는 데이터에 대해서는 쓰기 잠금을 걸 수가 없다.

그러므로 위의 UPDATE 구문은 레코드에 대해 쓰기 잠금을 시도하려고 하지만 name = junyoung인 레코드는 존재하지 않으므로,
0 row(s) affected가 출력되고, 아무 변경도 일어나지 않게 된다.
그러므로 최종적으로 결과는 name = joont가 된다.

2. Phantom Read
한 트랜잭션 내에서 같은 쿼리를 두 번 실행했는데, 첫 번째 쿼리에서 없던 유령(Phantom) 레코드가 두 번째 쿼리에서 나타나는 현상을 말한다.
REPETABLE READ 이하에서만 발생하고(SERIALIZABLE은 발생하지 않음), INSERT에 대해서만 발생한다.
아래와 같은 상황에서 재현될 수 있다.
```
START TRANSACTION; -- transaction id : 1 
SELECT * FROM Member; -- 0건 조회

    START TRANSACTION; -- transaction id : 2
    INSERT INTO MEMBER VALUES(1,'joont',28);
    COMMIT;

SELECT * FROM Member; -- 여전히 0건 조회 
UPDATE Member SET name = 'zion.t' WHERE id = 1; -- 1 row(s) affected
SELECT * FROM Member; -- 1건 조회 
COMMIT;
```
REPETABLE READ에 에 의하면 원래 출력되지 않아야 하는데 UPDATE 문의 영향을 받은 후 부터 출력된다.
이 시점에 스냅샷을 적용시키는 것 같다.

참고로 DELETE에 대해서는 적용되지 않는다.
```
START TRANSACTION; -- transaction id : 1 
SELECT * FROM Member; -- 1건 조회

    START TRANSACTION; -- transaction id : 2
    DELETE FROM Member WHERE id = 1;
    COMMIT;

SELECT * FROM Member; -- 여전히 1건 조회 
UPDATE Member SET name = 'zion.t' WHERE id = 1; -- 0 row(s) affected
SELECT * FROM Member; -- 여전히 1건 조회 
COMMIT;
```



**4. Serializable (레벨 3)**
  > 트랜잭션이 완료될 때까지 SELECT 문장이 사용하는 모든 데이터에 Shared Lock이 걸리는 계층

- 완벽한 읽기 일관성 모드를 제공한다
- 다른 사용자는 트랜잭션 영역에 해당되는 데이터에 대한 수정 및 입력 불가능하다
- select 작업의 경우에도 공유 잠금을 설정하게 되고 이러면 동시에 다른 트랜잭션에서 이 레코드를 변경하지 못하게 된다
- 이러한 특징으로 동시처리 능력이 떨어지고 성능저하가 발생한다

### 선택 시 고려사항

Isolation Level에 대한 조정은, 동시성과 데이터 무결성에 연관되어 있다

동시성을 증가시키면 데이터 무결성에 문제가 발생하고, 데이터 무결성을 유지하면 동시성이 떨어지게 된다

레벨을 높게 조정할수록 발생하는 비용이 증가한다

### 낮은 단계 Isolation Level을 활용할 때 발생하는 현상들
- Dirty Read
  > 커밋되지 않은 수정중인 데이터를 다른 트랜잭션에서 읽을 수 있도록 허용할 때 발생하는 현상 \
  > 어떤 트랜잭션에서 아직 실행이 끝나지 않은 다른 트랜잭션에 의한 변경사항을 보게되는 경우

- Non-Repeatable Read
  > 한 트랜잭션에서 같은 쿼리를 두 번 수행할 때 그 사이에 다른 트랜잭션 값을 수정 또는 삭제하면서 두 쿼리의 결과가
  > 상이하게 나타나는 일관성이 깨진 현상

- Phantom Read
  > 한 트랜잭션 안에서 일정 범위의 레코드를 두 번 이상 읽었을 때, 첫번째 쿼리에서 없던 레코드가 두번째 쿼리에서 
  > 나타나는 현상 \
  > 트랜잭션 도중 새로운 레코드 삽입을 허용하기 때문에 나타나는 현상이다

![image](https://user-images.githubusercontent.com/67304980/135714988-e1a7b5f5-b96e-4ced-a015-08c6e137f9d0.png)























