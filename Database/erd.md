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

![image](https://user-images.githubusercontent.com/67304980/136699795-9bb19edd-9db6-40ae-b591-d497c108e9b0.png)

#### 2. 개념적 설계

![image](https://user-images.githubusercontent.com/67304980/136699808-f4eb05b2-779a-4b9b-91ac-53ba83b5d6b9.png)
![image](https://user-images.githubusercontent.com/67304980/136699820-bc906dfe-a3b6-4fc9-bdf5-9e6d50bc432d.png)
![image](https://user-images.githubusercontent.com/67304980/136699831-dd84e565-1df5-4f26-9d35-f7df65a10f20.png)
![image](https://user-images.githubusercontent.com/67304980/136699835-26cae881-acf9-42ab-abf5-8be5e781db60.png)
![image](https://user-images.githubusercontent.com/67304980/136699848-43394f76-425c-463a-8e26-faef6c9f63e0.png)


## E-R 다이어그램

### ER모델(Entity Relation Model)
![image](https://user-images.githubusercontent.com/67304980/136699911-ad4d4d89-a97f-4c60-9287-312e37be1f08.png)

### 개체(Entity)
![image](https://user-images.githubusercontent.com/67304980/136699937-6e7bdcac-e787-4f9b-8c26-3876df6865e8.png)

### 속성(Attribute)
![image](https://user-images.githubusercontent.com/67304980/136699961-6ca481e2-5dc3-40a4-a67e-2c242eb97c99.png)
![image](https://user-images.githubusercontent.com/67304980/136699971-3d26822e-fcee-434d-91d1-8a66bfc8fc61.png)

### 관계(Relationship)
![image](https://user-images.githubusercontent.com/67304980/136699981-4b50ae95-a98d-4fcc-9a1d-53b6a4513f87.png)

### Cardinality(Degree of Relationship)
![image](https://user-images.githubusercontent.com/67304980/136700003-ee032f04-e2ac-4793-b4a6-4ef5740c0fc4.png)
![image](https://user-images.githubusercontent.com/67304980/136700020-abb4d2cb-bd0d-43ef-ba3c-41dc08a97e65.png)


## 릴레이션 스키마 변환

### 논리적 설계
![image](https://user-images.githubusercontent.com/67304980/136700052-687f329f-fd17-45d9-8ed1-e7cf72d6197d.png)

### 릴레이션 스키마 변환 규칙
![image](https://user-images.githubusercontent.com/67304980/136700064-0c14501c-8d47-458e-95ff-9466b48c5004.png)
![image](https://user-images.githubusercontent.com/67304980/136700080-98cf996d-3061-4a07-a3bc-6bf3a370201a.png)
![image](https://user-images.githubusercontent.com/67304980/136700090-f8b0a5d3-0d51-48d9-9d33-5ab86a0c7c7a.png)
![image](https://user-images.githubusercontent.com/67304980/136700108-00227bcf-7527-4fd0-b769-99b9c94bd84e.png)
![image](https://user-images.githubusercontent.com/67304980/136700117-c15ba6be-b7b8-41d2-92c9-85183f271c46.png)
![image](https://user-images.githubusercontent.com/67304980/136700128-b819aa46-a96d-429b-9c73-76cce6ed1cab.png)
![image](https://user-images.githubusercontent.com/67304980/136700134-fd9703d9-dfa7-479f-969d-ffde8f8d93e1.png)
![image](https://user-images.githubusercontent.com/67304980/136700140-137ebd50-bf22-41e3-90f5-7026e1ec4902.png)
![image](https://user-images.githubusercontent.com/67304980/136700156-4393db63-4daf-488d-bda0-28755dd3c1d8.png)
![image](https://user-images.githubusercontent.com/67304980/136700171-42f00eb9-2d24-4a88-9ec3-8da9601bd526.png)


## 릴레이션 스키마 변환 예제

### 한빛 마트 E-R 다이어그램 예제
![image](https://user-images.githubusercontent.com/67304980/136700191-4e8c4b32-1ef0-49b5-8156-f93bd78fabc1.png)
![image](https://user-images.githubusercontent.com/67304980/136700199-32f9e0e4-4481-4d4e-8f25-498b5cde155d.png)
![image](https://user-images.githubusercontent.com/67304980/136700213-f02f6e50-96a8-4a55-be7b-b910fe4fff5a.png)
![image](https://user-images.githubusercontent.com/67304980/136700226-8017c441-e7a8-4c03-83d6-a98924483a19.png)







