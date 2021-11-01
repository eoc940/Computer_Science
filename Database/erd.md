## 데이터베이스 설계

- 사용자의 다양한 요구 사항을 고려하여 데이터베이스를 생성하는 과정
- 이미 구축된 데이터베이스는 구조를 변경하기 어려우므로 체계적인 설계 과정을 통해 데이터베이스가 올바르게 구축되어야 함
- 대표적인 설계 방법
  - E-R 모델과 Relation 변환 규칙을 이용한 설계
  - 정규화를 이용한 설계

### 데이터베이스 설계 단계

1. 요구사항 분석
  > 실제 세계에서 어떤 요구사항의 시스템을 구출할 것인지 데이터베이스의 용도 파악 \
  > 요구 사항에 대해 어떤 데이터들이 필요한지, 어떤 기능들이 필요한지 분석 \
  > 결과물 : 요구 사항 명세서

2. 개념적 설계(Conceptual Model)
  > 요구 사항 분석 결과물을 개념적 데이터 모델을 이용해 개념적 구조로 표현 \
  > 요구 사항 명세서를 E-R 다이어그램으로 표현

3. 논리적 설계
  > 관계 모델(Relational Model)을 통해 개념적 모델을 논리적으로 표현 \
  > 결과물 : 릴레이션 스키마

4. 물리적 설계
  > DBMS로 구현이 가능한 물리적인 구조를 설계 \
  > 저장 레코드 타입 및 인덱스 타입 등 설계 \
  > 결과물 : 물리적 스키마


#### 1. 요구 사항 분석

- 데이터베이스를 사용할 주요 사용자의 범위 결정
- 사용자가 수행하는 업무를 분석
- 수집된 요구 사항에 대한 분석 결과를 요구 사항 명세서로

![image](https://user-images.githubusercontent.com/67304980/139661878-21b330d2-fd4e-48f2-ac3e-f47e8860f034.png)





#### 2. 개념적 설계

- DBMS에 독립적인 개념적 스키마를 설계하는 과정
- 요구 사항 분석 결과를 기반으로 중요한 개체를 추출하고 개체 간의 관계를 결정하여 E-R 다이어그램으로 표현
- 작업 과정
  - Step 1 : 개체 추출, 각 개체의 주요 속성과 키 속성 선별
  - Step 2 : 개체 간의 관계 설정
  - Step 3 : E-R 다이어그램 작성

- Step 1 : 개체 추출, 각 개체의 주요 속성과 키 속성 선별
  - 개체 : 저장할 만한 가치가 있는 중요한 데이터를 가진 사람이나 사물 등
  - 예) 병원 데이터베이스 개발에 필요한 개체
    - 병원 운영에 필요한 사람 : 환자, 의사, 간호사 등
    - 병원 운영에 필요한 사물 : 병실, 수술실, 의료 장비 등
  - 개체, 속성 추출 방법
    - 요구 사항 문장에서 업무와 관련이 깊은 의미 있는 명사를 찾는다
    - 찾아낸 명사를 개체와 속성으로 분류

![image](https://user-images.githubusercontent.com/67304980/139662267-b94c1eef-6dd4-4fb6-8864-e86a6a442453.png)


- Step 2 : 개체 간의 관계 설정
  - 관계 : 개체 간의 의미 있는 연관성
  - 관계 추출 방법
    - 요구 사항 문장에서 개체 간의 연관성을 의미 있게 표현한 동사를 찾는다
    - 찾아낸 관계에 대해 매핑 Cardinality와 참여 특성 결정
      - 매핑 Cardinality : 1:1, 1:n, m:n
      - 참여 특성 : total participation / partial participation




- Step 3 : E-R 다이어그램 작성
  - 하단에서 살펴보자


## E-R 다이어그램

### ER모델(Entity Relation Model)
- 요구 사항에서 얻어 낸 정보 등을 개체(Entity), 속성(Attribute), 관계(Relation)으로 기술하는 데이터 모델
- Peter Chen이 2012년 제안

![image](https://user-images.githubusercontent.com/67304980/139662609-cb6760b9-3561-4cd0-9a7a-d6cfe5496704.png)


### 개체(Entity)
- 저장할 만한 가치가 있는 중요한 데이터를 가진 사람이나 사물 등
- 개체들의 집합을 Entity Type이라 하고 개체 이름을 포함하는 네모로 표현
- Weak Entity
  - 포함된 속성만으로 고유하게 식별할 수 없는 개체
  - Weak Entity는 다른 소유하는 Entity에 의존하는 Entity
  - Order item은 Order 항목에 종속적이고 order 없이는 존재할 수 없음

![image](https://user-images.githubusercontent.com/67304980/139662793-66f76648-c8ab-48f7-aede-ee402f5c1b9e.png)



### 속성(Attribute)
- 개체, 관계 그리고 Attribute의 속성 또는 특성을 나타냄
- Attribute는 이름을 포함한 원으로 표현된다
- Key Attribute
  - 특정 Entity를 식별할 수 있는 고유한 값을 가진 Attribute
  - 이름에 underscore
- Multivalued Attribute
  - 하나의 Attribute가 여러 개의 값을 가질 수 있는 Attribute
  - 두 개의 원으로 표현

![image](https://user-images.githubusercontent.com/67304980/139662979-fa84c10f-1ad0-4d85-9d0c-ebc6203e1c86.png)

- Derived Attribute
  - 다른 Attribute들로부터 계산되어져 나온 Attribute
  - 점선으로 된 원으로 표현

- Composite Attribute(복합 속성)
  - 독립적인 Attribute들이 모여서 생성된 하나의 Attribute
  - Attribute가 Attribute를 가지는 것으로 표현

![image](https://user-images.githubusercontent.com/67304980/139663139-7ad3fba2-09c1-4bf1-8a3e-bf1c622b8a83.png)


### 관계(Relationship)
- Entity간의 어떤 상호작용을 하는지 표현하는 notation
- Relationship 이름을 포함한 다이아몬드(마름모)로 표현
- Relationship도 Attribute(속성)을 가질 수 있다
- Weak relationship
  - Weak entity와 parent-child 관계에서의 상호작용을 표현

![image](https://user-images.githubusercontent.com/67304980/139663284-8c1a5f37-c20c-4464-ab26-269b7a52b336.png)




### Cardinality(Degree of Relationship)
- 관계를 맺는 두 Entity에 대해 한 개체가 얼마나 많은 다른 개체와 관련된 수인지 나타내는 표현
- one to one(1:1)
- one to many(1:N)
- many to one(N:1)
- many to many(M:N)

![image](https://user-images.githubusercontent.com/67304980/139663408-1e546e6a-b028-4c92-ba21-da90c44cd52d.png)

- Entity set이 Relationship에 전체로 참여하는 부분적으로 참여하는지 표현하는 notation
- Total participation
  - 모든 entity가 relationship에 참여
  - Double line으로 표현
- Partial participation
  - Entity set에서 일부분만 relationship에 참여할 때
  - Single line으로 표현

![image](https://user-images.githubusercontent.com/67304980/139663578-6f6c0a9b-03f9-408c-be7a-7674d112c269.png)


## 릴레이션 스키마 변환


### 논리적 설계
- 관계 모델(Relational model)을 통해 개념적 모델을 논리적으로 표현
- 개념적 스키마 -> 논리적 스키마
- ER 다이어그램과 관계 데이터 모델의 차이점
  - ER 모델은 개체와 관계를 구분하지만 관계 데이터 모델은 모두 릴레이션으로 표현
  - ER 모델은 Multivalued Attribute, Composite Attribute를 표현하지만 관계 데이터 모델을 허용하지 않음
  - ER 모델의 Cardinality, participation constraint도 관계 데이터 모델에서는 그에 맞는 표현으로 변경이 필요




### 릴레이션 스키마 변환 규칙
- 규칙 1 : 모든 개체는 릴레이션으로 변환한다
  - Entity name -> Relation name
  - Entity attribute -> Relation attribute
  - Entity key attribute -> Relation primary key
  - Compose attribute(복합속성)이 있는 경우는 Compose attribute의 attribute를 Relation의 속성으로 변경

![image](https://user-images.githubusercontent.com/67304980/139664225-41702bcd-41ac-47a4-a6c1-ddc40e516212.png)

- 규칙 2 : 다대다 관계는 릴레이션으로 변환한다
  - Relationship name -> Relation name
  - Relationship attribute -> Relation attribute
  - 관계에 참여하는 기본 키를 관계 릴레이션에 포함시키고 외래키로 지정한다(같은 이름이 이미 존재하면 이름 변경)

![image](https://user-images.githubusercontent.com/67304980/139664349-86425853-3445-4388-8c24-3e19a94901dd.png)

- 규칙 3 : 일대다 관계는 외래키로 표현한다
  - 1:n 관계에서 1측 Entity 릴레이션의 기본키를 n측 Entity 릴레이션에 포함시켜 외래키로 지정
  - Relationship 속성들도 n측 릴레이션에 포함시킨다

![image](https://user-images.githubusercontent.com/67304980/139664504-f0dfeaca-8005-440b-bfa0-233c80161007.png)

  - Weak Entity - Weak Relationship이 참여하는 일대다 관계는 parent entity의 기본키인 외래키 attribute를 포함해서 기본키를 지정한다

![image](https://user-images.githubusercontent.com/67304980/139664587-cf743e61-941c-4079-81fb-9c7fc90d2cf0.png)

- 규칙 4 : 일대일 관계는 외래키로 표현한다
  - 일대일 관계도 일대다와 마찬가지로 릴레이션이 아닌 외래키로 표현한다
  - 데이터 중복을 피하기 위해 참여 특성에 따라 다르게 처리한다
  - 규칙 4-1 : 일반적인 일대일 관계는 외래키를 서로 주고받는다
  - 규칙 4-2 : 일대일 관계에 필수적으로 참여하는 개체가 있는 경우, 필수 참여 개체의 릴레이션만 외래키를 받는다
  - 규칙 4-3 : 모든 개체가 일대일 관계에 필수적으로 참여하면 릴레이션 하나로 합친다

- 규칙 5 : Multivalued Attribute(다중 값 속성)은 릴레이션으로 변환한다
  - 다중 값 속성과 그 속성을 가지고 있던 개체의 기본키를 외래키로 포함시킨다
  - 다중 값 속성으로 만들어진 새로운 릴레이션의 기본키는 다중값 속성과 외래키를 조합하여 지정

![image](https://user-images.githubusercontent.com/67304980/139664906-7b3beee9-1fc6-4c98-bfc8-46005a0bf01c.png)



## 릴레이션 스키마 변환 예제

### 한빛 마트 E-R 다이어그램 예제
![image](https://user-images.githubusercontent.com/67304980/136700191-4e8c4b32-1ef0-49b5-8156-f93bd78fabc1.png)
![image](https://user-images.githubusercontent.com/67304980/136700199-32f9e0e4-4481-4d4e-8f25-498b5cde155d.png)
![image](https://user-images.githubusercontent.com/67304980/136700213-f02f6e50-96a8-4a55-be7b-b910fe4fff5a.png)
![image](https://user-images.githubusercontent.com/67304980/136700226-8017c441-e7a8-4c03-83d6-a98924483a19.png)







