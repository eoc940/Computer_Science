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
![image](https://user-images.githubusercontent.com/67304980/135711508-f3ca5641-6917-4128-8c1c-973aaf889b4f.png)


#### constraint clause
![image](https://user-images.githubusercontent.com/67304980/135711511-6cb04ae1-ea9b-4143-912f-9b7b99345851.png)

#### ALTER TABLE
![image](https://user-images.githubusercontent.com/67304980/135711550-7921f2f1-5b4b-4cd3-98b2-756aac063ce9.png)


#### DROP TABLE
![image](https://user-images.githubusercontent.com/67304980/135711559-cea5f0f6-ec37-4d81-8814-8f4d98e03aa4.png)

### SELECT
![image](https://user-images.githubusercontent.com/67304980/135711916-0c8b8ea9-e057-41e9-be78-96c5cf61c8bf.png)
![image](https://user-images.githubusercontent.com/67304980/135711924-07eadc52-4d9d-47e4-a272-fb503f956fd9.png)
![image](https://user-images.githubusercontent.com/67304980/135711927-cee435fe-2299-47a4-a7fa-074dcc660e4b.png)
![image](https://user-images.githubusercontent.com/67304980/135711932-c7a619ca-9e6d-495b-b591-663e477cdca1.png)
![image](https://user-images.githubusercontent.com/67304980/135711938-442d4d7f-7a71-47f0-89b3-231e9a950388.png)
![image](https://user-images.githubusercontent.com/67304980/135711941-3317dc48-0562-4118-b1e3-ddb6588fdd1b.png)
![image](https://user-images.githubusercontent.com/67304980/135711948-a3d1118e-a209-4d99-84a5-c23cd82981f8.png)
![image](https://user-images.githubusercontent.com/67304980/135711956-d149c838-a2f7-42b6-9e20-0ae10f8cc18f.png)
![image](https://user-images.githubusercontent.com/67304980/135711961-1a7cacb4-fe3f-4d14-9cf1-6fda2b3f9fca.png)
![image](https://user-images.githubusercontent.com/67304980/135711964-197bf514-ef3d-48b8-a72d-f8d6fa04f34f.png)
![image](https://user-images.githubusercontent.com/67304980/135711968-0674ac39-271a-43c4-af2f-a4c0a24c8cdd.png)
![image](https://user-images.githubusercontent.com/67304980/135711973-f4bd740c-7f6a-4473-9951-7ad2e7819ca1.png)
![image](https://user-images.githubusercontent.com/67304980/135711982-31c54342-3b9c-49ef-8d2a-1bb9c60b2567.png)
![image](https://user-images.githubusercontent.com/67304980/135711992-2fc4fa1e-b66d-4130-b814-797bc609c22e.png)
![image](https://user-images.githubusercontent.com/67304980/135711997-f90a7508-37ae-440b-8fab-4cd331b7d4ba.png)
![image](https://user-images.githubusercontent.com/67304980/135712004-81a016c8-a930-42de-ad13-b55bcaefd1ca.png)

### INSERT, DELETE, UPDATE
![image](https://user-images.githubusercontent.com/67304980/135712022-87da0e43-0f14-49fe-b272-ca7d00b43c9d.png)
![image](https://user-images.githubusercontent.com/67304980/135712031-52e26f4d-aa30-43b6-aeb2-540abe23fea8.png)
![image](https://user-images.githubusercontent.com/67304980/135712039-ebfd85f9-eb03-43b7-8fe4-ecf29bb1d349.png)
![image](https://user-images.githubusercontent.com/67304980/135712048-8bd367e3-1564-49f2-9c6d-dab736fce7a7.png)




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



























