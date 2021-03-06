## SQL

### SQL 분류
- DDL(Data Definition language) : 테이블을 생성하고 변경, 제거하는 기능을 제공
- DML(Data Manipulation language) : 테이블에서 새 데이터를 삽입하거나, 테이블에 저장된 데이터를 수정, 삭제하는 기능을 제공
- DCL(Data Control Language) : 보안을 위해 데이터에 대한 접근 및 사용권한을 조절하는 기능을 제공

![image](https://user-images.githubusercontent.com/67304980/135711365-59ad4e42-dcef-423e-b622-35fd83be4a6e.png)

### DDL
- 데이터베이스 구조를 정의하고 변경하는 기능 제공하는 언어
- Create : 새로운 데이터베이스 오브젝트들을 생성(schema, table, view 등)
- Alter : 존재하는 오브젝트의 정의를 변경
- Drop : 존재하는 오브젝트를 데이터베이스에서 삭제

![image](https://user-images.githubusercontent.com/67304980/135711430-d30f1041-2720-4b6c-9113-eb040fd6be0a.png)

#### 데이터 타입
![image](https://user-images.githubusercontent.com/67304980/135711445-74cbf9d0-ab0b-48e0-afb3-030a4212ab3a.png)

#### default clause

- INSERT 문에 컬럼에 대한 값이 지정되지 않았을 때 null 대신에 <value> 사용
- credit INT DEFAULT 0
- contact char(20) DEFAULT 'james'

  
#### constraint clause
- UNIQUE : 대체 키를 지정하고 유일성을 가지는 컬럼임을 지정
- PRIMARY KEY : ROW를 식별할 수 있는 기본키(unique + not null)
  
  
  
  
#### ALTER TABLE
- 새로운 컬럼 추가 : ALTER TABLE customer ADD reg_date DATE;
- 기존 컬럼 삭제 : ALTER TABLE customer drop age;
- 새로운 제약조건 추가 : ALTER TABLE customer ADD CONSTRAINT set_pro_key PRIMARY KEY(id)
- 제약조건 삭제 : ALTER TABLE customer DROP CONSTRAINT set_pri_key;
  
  
  
#### DROP TABLE
- 테이블 데이터 및 catalog 삭제
- DROP TABLE <table_name>



### 조인이란
- 두 개 이상의 테이블이나 데이터베이스를 연결하여 데이터를 검색하는 방법을 말한다
- 테이블을 연결하려면, 적어도 하나의 컬럼을 서로 공유하고 있어야 하므로 이를 이용하여 데이터 검색에 활용한다

### Join 종류
- INNER JOIN
- LEFT OUTER JOIN
- RIGHT OUTER JOIN
- FULL OUTER JOIN
- CROSS JOIN
- SELF JOIN

1. INNER JOIN

![image](https://user-images.githubusercontent.com/67304980/131811987-5c27b915-f553-478c-a5c2-3d7a936d4a44.png)

- 쉽게 말해 **교집합**이다
- 기준 테이블과 Join한 **테이블의 중복된 값**을 보여준다
- 결과값은 A 테이블과 B 테이블이 모두 가지고 있는 데이터만 검색된다

```
SELECT
A.NAME,
B.AGE
FROM EX_TABLE A
INNER JOIN JOIN_TABLE B ON A.NO_EMP = B.NO_EMP
```

2. LEFT OUTER JOIN

![image](https://user-images.githubusercontent.com/67304980/131812469-536e497d-7e50-4fc4-ab56-4554c248bfa0.png)

- **기준 테이블의 값 + 테이블과 기준테이블의 중복된 값**을 보여준다
- 왼쪽 테이블을 기준으로 Join을 하겠다고 생각하면 된다
- 결과값은 A 테이블의 모든 값과 A 테이블과 B 테이블의 중복되는 값이 검색된다

```
SELECT
A.NAME,
B.AGE
FROM EX_TABLE A
LEFT OUTER JOIN JOIN_TABLE B ON A.NO_EMP = B.NO_EMP
```

3. RIGHT OUTER JOIN

![image](https://user-images.githubusercontent.com/67304980/131813027-7c353d59-e554-4a3a-9f1f-3cf4d1363823.png)

- LEFT OUTER JOIN의 반대이다
- 오른쪽 테이블을 기준으로 Join을 하겠다고 생각하면 된다
- 결과값은 B 테이블의 모든 데이터와 A 테이블과 B 테이블에서 중복되는 값이 검색된다

```
SELECT
A.NAME,
B.AGE
FROM EX_TABLE A
RIGHT OUTER JOIN JOIN_TABLE B ON A.NO_EMP = B.NO_EMP
```

4. FULL OUTER JOIN

![image](https://user-images.githubusercontent.com/67304980/131813402-3930fe70-a48a-4a0f-8161-7ac12b342085.png)

- 쉽게 말해 합집합을 생각하면 된다
- A 테이블이 가지고 있는 데이터, B 테이블이 가지고 있는 데이터 모두 검색된다
- 사실상, 기준 테이블의 의미가 없다

```
SELECT
A.NAME,
B.AGE
FROM EX_TABLE A
FULL OUTER JOIN JOIN_TABLE B ON A.NO_EMP = B.NO_EMP
```

5. CROSS JOIN

![image](https://user-images.githubusercontent.com/67304980/131813652-8bcae87f-c9aa-4b0a-bcea-6800a8479bd2.png)

- 모든 경우의 수를 전부 표현해주는 방식이다
- 기준 테이블이 A일 경우, A의 데이터 한 ROW를 B테이블 전체와 JOIN하는 방식이다
- 결과값은 N*M이다
- 위의 경우 A 테이블에 데이터가 3개, B 테이블에 데이터가 4개가 있으므로 총 12개가 검색된다

```
--첫 번째 방식--
SELECT
A.NAME,
B.AGE
FROM EX_TABLE A
CROSS JOIN JOIN_TABLE B

--두 번째 방식--
SELECT
A.NAME,
B.AGE
FROM EX_TABLE A, JOIN_TABLE B
```

6. SELF JOIN

![image](https://user-images.githubusercontent.com/67304980/131814020-96bbbc15-37e4-498b-b5d0-09e212a79674.png)

- 자기 자신과 자기 자신을 조인한다는 의미이다
- 하나의 테이블을 여러번 복사해서 조인한다고 생각하면 된다
- 자신이 가지고 있는 컬럼을 다양하게 변형시켜 활용할 경우에 자주 사용한다

```
SELECT
A.NAME,
B.AGE
FROM EX_TABLE A, EX_TABLE B
```



























