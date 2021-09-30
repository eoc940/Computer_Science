## 관계 대수(relational algebra)

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






