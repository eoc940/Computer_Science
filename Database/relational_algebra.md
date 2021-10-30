## 관계 대수(relational algebra)

- 관계형 데이터 모델링에는 두 가지 언어가 존재한다
  - 관계해석 : 원하는 데이터만 명시하고 "어떻게 질의를 해석하는가"에 대해 언급이 없는 선언적 언어이다
  - 관계대수 : "어떻게 질의를 해석하는가"에 대해 언급하는 절차적 

### 관계 데이터 연산

모든 DBMS는 데이터 처리를 위해 하나 이상의 데이터 언어를 제공

#### Formal query language
- 수학기호(notation)을 사용하여 데이터 처리를 기술한 언어
- 새로운 언어의 개념과 유용성을 검증하는 기준
- 관계 대수

#### Commercial language
- 수학적인 원리를 기반으로 사용하기 쉽게 만들어진 단어
- 관계 대수로 만들어진 모든 질의가 표현 가능 - Relationally complete
- SQL

### 관계 대수 연산자
- 피연산자로 하나 또는 두 개의 릴레이션
- 각 연산자의 연산 결과는 새로운 릴레이션-연산의 합성(compose) & 체이닝(chaining) 가능

#### 연산자 종류
- select
- project
- union
- difference
- intersection
- cartesian product
- natural join
- theta join
- outer join

#### select
![image](https://user-images.githubusercontent.com/67304980/135411312-66a638ea-57d7-4d5f-9208-7e817c9bd1ff.png)

A=B 조건과 D>5 조건을 만족하는 튜플들을 출력

#### project
![image](https://user-images.githubusercontent.com/67304980/135411458-28bf79d3-0d1c-480e-a702-7a44af005f42.png)

r릴레이션에 속한 A,D 속성만 출력

#### union(합집합)
![image](https://user-images.githubusercontent.com/67304980/135411674-267a6919-0d7b-41f0-8d04-e3c56a1ea351.png)

#### difference(차집합)
![image](https://user-images.githubusercontent.com/67304980/135411824-92b2d75a-1668-4ad3-8507-8da6c3e1b303.png)

#### intersection(교집합)
![image](https://user-images.githubusercontent.com/67304980/135411962-f466959d-4922-4f69-8cdf-6a1277ac8ff8.png)

#### cartesian product(카티션 프로덕트)
![image](https://user-images.githubusercontent.com/67304980/135412095-73d251b2-95ef-427f-a200-c7149452f114.png)

속성을 이어붙이고 각 튜플을 서로 곱해서 결국 새로운 릴레이션의 튜플 수는 각 릴레이션의 튜플 개수의 곱과 같다

#### natural join(자연 조인)
![image](https://user-images.githubusercontent.com/67304980/135412276-7ac33f84-49a3-43ce-99e3-0f765b2b2862.png)

두 릴레이션의 같은 속성에서 같은 값을 갖는 튜플들끼리 곱셈 연산을 진행한다

#### theta join(세타 조인)
![image](https://user-images.githubusercontent.com/67304980/135412692-b93e8b76-d0fd-4ae3-8de6-f43f8c8c7d6d.png)

r릴레이션의 A속성의 값이 a인 튜플과 r릴레이션의 B속성과 s릴레이션의 B속성이 같은 튜플간의 곱셈 연산을 진행한다

#### outer join(외부 조인)
![image](https://user-images.githubusercontent.com/67304980/135412994-f2e50701-7e8b-4ddc-a047-842c8a66c851.png)

![image](https://user-images.githubusercontent.com/67304980/135413024-c1824d06-1779-43dc-9913-5bca2c1caee6.png)


## SQL
- 위와 같이 다양한 연산들이 있는 관계대수도 한계가 명확히 존재한다
  - 정렬이 안된다
  - 집단 함수를 지원하지 않는다
  - 산술 연산(+,-,*,/)을 못한다
  - 데이터베이스를 수정할 수 없다
  - 결과에 중복된 튜플들들 명시하고 싶을 때 명시하지 못한다

- 이러한 한계를 극복하기 위해 확장을 시키고, 확장을 시킨 후 튜플기반의 관계해석과 결합한 것이 SQL이다
  - SQL = 관계대수 + 튜플기반의 관계해석

관계대수의 확장된 기능을 살펴보자

### 1. 집단함수
- 주로 AVG, MIN, MAX, COUNT가 있다

![image](https://user-images.githubusercontent.com/67304980/139528687-864aa05f-5131-4711-84e9-adc7791cf849.png)


### 2. 그룹화
- 그룹화를 원하는 목록을 그룹으로 묶어준다

![image](https://user-images.githubusercontent.com/67304980/139528713-0c63791e-ea43-457a-b698-4251538a3ac1.png)


### 3. 외부조인
- 조인의 문제점은 상호 연산이 없는 데이터들이 배제당한다는 것이다
- 그래서 배제당하는 데이터들도 표현해주기 위해 외부조인이 등장했다

![image](https://user-images.githubusercontent.com/67304980/139528774-ca3c9c6c-2256-4f4d-9d44-a4b27ad8aca9.png)


이렇게 관계대수의 확장된 기능을 확인했다. 이제 SQL을 살펴볼텐데, SQL은 관계대수의 영향을 받으면서도 관계해석의 영향도 받는다

### 관계해석
- 정의
  - 원하는 데이터만 명시하고 질의를 어떻게 수행할 것인가는 명시하지 않는 선언적인 언어
- 특징
  - 비절차 언어
  - 튜플 관계 해석과 도메인 관계 해석이 있다
  - 기본적으로 관계해석과 관계대수는 관계 데이터베이스를 처리하는 기능과 능력면에서 동등
  - 연산들의 절차를 사용하여 데이터를 가져온다
  - 기본적인 연산지로 union, intersection, difference를 사용한다
  - 전체관계를 조작하는데 사용되는 연산들의 집합
  - SQL문과 같은 질의어를 사용한다


## 조인
- 한 데이터베이스 내의 여러 테이블의 레코드를 조합하여 하나의 열로 표현한 것
- 다른 말로 여러 테이블에 있는 정보 중 사용자가 필요한 정보만 가져와 가상의 테이블처럼 만들어서 결과를 보여주는 것으로 n개의 테이블을 조합하여 하나의 열로 표현한 것이다

### 1. Inner Join
- 키 값이 있는 테이블의 컬럼 값을 비교해서 맞는 값을 가져오는 것이다
- 서로 관련된 내용을 검색해 가져오는 방법이다
- 교집합이라고 생각하면 된다
- 디폴트 조인이다

![image](https://user-images.githubusercontent.com/67304980/139529563-39bd0354-4128-4b88-bd15-8e9e03bc1ca0.png)

![image](https://user-images.githubusercontent.com/67304980/139529577-d3fdd300-4317-44c6-ae44-ad52015e6ea0.png)


### 2. Outer Join
- Outer Join은 여러 테이블 중 한 테이블에만 데이터가 있고 다른 쪽에는 없는 경우, 데이터가 있는 테이블의 내용을 전부 출력하는 방법이다

![image](https://user-images.githubusercontent.com/67304980/139529613-709d7909-f8b3-4dd5-a52a-4fd26af82cf4.png)

- Outer Join에는 LEFT, RIGHT, FULL OUTER JOIN이 있는데 말 그대로 왼쪽의 모든 데이터를 오른쪽 테이블 데이터에 매칭하고 매칭되는 데이터가 없는 경우 NULL로 표시하고, 반대로 오른쪽의 데이터를 왼쪽 데이터를 매칭, 양쪽 모두 조건이 일치하지 않은 것까지 모두 결합해서 출력하는 방식이다

![image](https://user-images.githubusercontent.com/67304980/139529683-0df06a1d-6911-4aca-ad59-8cc599df0566.png)



### 3. Cross Join
- Cross Join은 두 테이블의 모든 조합을 받아온다
- 곱집합이라고 생각하면 된다

![image](https://user-images.githubusercontent.com/67304980/139529696-9473653a-73e2-4970-8f8f-f387f6629257.png)

![image](https://user-images.githubusercontent.com/67304980/139529715-0c9ef21d-16e6-4c93-babf-92a75548f6bf.png)


### 4. Self Join
- INNER와 OUTER는 보통 여러 테이블을 조인하는데 자신의 테이블로도 조인이 가능하다. 이를 SELF JOIN이라고 한다
- SELF JOIN은 동일한 테이블을 사용하기 위해 각각 다른 이름으로 지정해서 SQL을 작성한다
- 동일 테이블이라 조인 조건 및 조회하는 컬럼명이 겹치게 되어, 각각 다른 이름으로 별칭을 붙여서 구별을 할 수 있게 한다

![image](https://user-images.githubusercontent.com/67304980/139529775-45c58724-08e5-43e2-b465-4bb81fae0935.png)












