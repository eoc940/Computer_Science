## DBMS 쿼리 프로세싱

### Query optimization on DBMS

- Parser
  - SQL 문장을 분석해서 syntax 체크 및 catalog를 이용해서 semantic check 등을 하여 annotated AST 생성
- Optimizer
  - AST를 분석하여 execution plan을 생성
  - AST에 따라 가능한 execution plan 리스트를 만들고 그 중에서 최저 비용을 가지는 execution plan을 선택
  - Rule-Based Optimizer
    - 미리 정해 놓은 rule에 따라 logical access 경로를 비교하며 최적의 plan을 생성
  - Cost-Based Optimizer
    - Plan의 각 실행 operator의 cost를 미리 정해놓고 예상 비용을 계산하여 최적의 plan을 선택

![image](https://user-images.githubusercontent.com/67304980/137851689-9d24d9bb-5b8c-4e06-8b8a-3d994c5bfce3.png)

- 좋은 DBMS는 query를 가장 효율적인 execution plan으로 변환하여 실행한다
  - better response time
  - better throughput
- Query optimization은 DBMS 안에서도 가장 어려운 문제(NP-Complete)
- 어떤 optimizer도 "optimal" plan만 만들어 낸다고 보장할 수 없다
  - real plan cost를 추측하여 cost estimation
  - 가능한 plans 수를 제한하기 위한 heuristics 방법을 사용
- Cost estimation
  - Resource utilization (CPU, Disk IO, network)
  - Cost of algorithms and access methods
  - Size of intermediate results
  - Data statistics(sker, order, placement)

### Relational algebra equivalence
- 두 relational algebra expression이 같은 결과의 튜플 셋을 만들어 내는 경우 equivalent하다고 한다

![image](https://user-images.githubusercontent.com/67304980/137852694-73402e0e-e3a4-4734-984c-e936df8adb8b.png)

### Equivalence Rules

![image](https://user-images.githubusercontent.com/67304980/137852763-6c30883c-388c-4283-821d-5fc222fa948e.png)

![image](https://user-images.githubusercontent.com/67304980/137852787-bfb7d884-dbe2-4a1f-9d5b-de4374ac5459.png)

![image](https://user-images.githubusercontent.com/67304980/137852810-5fe47a84-48bd-4c5f-a1c6-54c1b30dda44.png)



## Selection Operators

### Selection Strategies
- Linear search - Full table scan
  - 데이터가 저장된 모든 disk block의 access 필요
- Binary search - (B+Tree Index)
  - Exact matches
  - Multiple matches
  - Range queries
  - Complex queries
- Index를 이용한 search는 index access를 위한 disk block access와 실제 data block access가 필요
  - 일반적인 query에서 전체 full table scan에 비해 access 횟수가 훨씬 적다

### Join Strategies
- Join의 경우 세 가지 형태로 execution plan이 만들어진다
- Nested loop Join
  - 조인을 순차적으로 수행하면서 데이터에 random access
  - Inner table에 조인을 위한 index 필요
  - cost = Outer table cardinality * access count of Inner table block
- Merge Join (Sort Merge Join)
  - 데이터에 랜덤하게 액세스하지 않고 스캔을 하면서 수행
  - 양쪽 테이블 처리 범위를 각자 접근해 정렬한 결과를 차례대로 스캔
  - 인덱스가 존재하지 않고 조인 연산자가 "="가 아닌 경우 좋은 성능을 보임
- Hash Join
  - 내부적으로 hash table을 만들어서 수행
  - 두 테이블 중에 작은 테이블에 대해 hash table 생성
  - 큰 테이블을 스캔하면서 hash table를 조회하며 join하는 방식
  - hash table 생성 cost가 추가되기 때문에 한 join대상 테이블이 작을 때 유리

## Optimizer-1

### Heuristic-based optimization
- Optimizer of INGRES of Stonebraker and Oracle(1990년초 이전버전)
- Logical operator를 physical plan으로 바꾸는 static rules를 이용한다
  - 가장 제한적인 selection을 먼저 수행한다
  - Join전에 모든 selection을 수행한다
  - Predicate/Limit/Projection은 push down
  - Cardinality를 기준으로 join ordering

### Heuristic + Cost based optimization
- Static rule로 initial optimization을 수행한다
- 좋은 join ordering을 결정하기 위해 dynamic programming을 수행한다
  - 첫번째 cost-based query optimizer
  - divide-and-conquer 방식을 이용한 Bottom-up planning
- System R, 초기의 IBM DB2, 대부분의 open source databases들이 이 방식을 이용
- System R optimizer
  - query를 blocks들로 나누고 각 block을 위한 logical operator들을 생성
  - 각 logical operator을 위한 physical operator의 모든 가능한 join path와 access path의 조합을 set으로 생성
  - 반복적으로 cost를 계산하면서 가장 cost가 작은 plan의 left-deep tree을 만든다

![image](https://user-images.githubusercontent.com/67304980/137854469-0fc0cc58-6741-4d33-a416-b4db791baaa6.png)


### Example of Heuristic + Cost based optimization

![image](https://user-images.githubusercontent.com/67304980/137854515-9dcb84d5-0117-421e-b371-542c98d7a709.png)

![image](https://user-images.githubusercontent.com/67304980/137854540-d8d29273-8838-460d-b859-91a5b6524046.png)

![image](https://user-images.githubusercontent.com/67304980/137854578-0af934f6-9a1d-4ddf-8eb2-1a04d6362c20.png)

![image](https://user-images.githubusercontent.com/67304980/137854706-667cb404-2747-4758-acab-159cceb72ac0.png)

![image](https://user-images.githubusercontent.com/67304980/137854752-225d2ee8-ba87-49c7-97f1-f268040aefaf.png)

![image](https://user-images.githubusercontent.com/67304980/137854788-5883b4d1-81b5-4594-bf25-1c9bced0b899.png)


## Optimizer-2

### Top-down VS Bottom-up
- Top-down Optimization
  - 최종 logical plan을 가지고 plan tree를 내려가며 alternative plan과 비교하며 cost를 계산하여 optimal plan을 계산
  - Volcano, Cascades
- Bottom-up Optimization
  - 아무것도 없는 상태에서 시작해서 plan을 시작부터 만들어 가면서 원하는 최종 plan을 만드는 방식
  - System R, Starburst

### Volcano Optimizer
- Relational algebra의 equivalence rule을 기본으로 하는 cost-based optimizer이다
  - 새로운 operator과 equivalence rule를 추가하기 쉽다
  - branch-and-bound 방법을 사용하여 top-down 방식으로 optimal plan을 고른다 -> 일단 logical plan에 대한 가능한 physical plan을 고르고 그 다음 alternative plan들을 찾아 비교
  - MEMO table을 이용하여 equivalent operator를 저장
- Cascade optimizer : Object-oriented implementation of Vocano
  - SQL server에서 사용 

![image](https://user-images.githubusercontent.com/67304980/137855499-066ffa69-cbfc-462f-b1f3-440bde6b1d24.png)

![image](https://user-images.githubusercontent.com/67304980/137855533-1db9ee8d-b00b-4d33-946f-19997431dd10.png)

![image](https://user-images.githubusercontent.com/67304980/137855582-72f7b1f0-d628-4d6a-a8df-cd9a974e55c4.png)

![image](https://user-images.githubusercontent.com/67304980/137855619-748b303a-80af-4b97-ac35-0d78a6564f66.png)

### 결론
- Optimizer에 의한 query optimization은 매우 어려운 문제
- "optimal"한 plan을 찾아내는 건 NP-complete 문제로 DBMS는 heuristic과 지정된 cost값을 이용
- DBMS들은 optimizer의 역할을 보완하기 위한 기능 제공
  - SQL Hint
  - Plan Stability(저장된 old 버전의 plan 사용)


