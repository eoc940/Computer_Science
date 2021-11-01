## 인덱스

### INDEX의 의미
- RDBMS에서 검색 속도를 높이기 위해 사용하는 하나의 기술이다
- INDEX는 색인이다
- 해당 Table의 컬럼을 색인화(따로 파일로 저장)하여 검색시 해당 Table의 레코드를 full scan하는 것이 아니라 색인화 되어있는
INDEX 파일을 검색하여 속도를 빠르게 한다
- INDEX 구조는 Tree 구조로 색인화한다
- RDBMS에서 사용하는 INDEX는 Balance Search Tree를 사용한다

### INDEX의 원리

INDEX를 해당 컬럼에 주게 되면 초기 Table 생성 시, **FRM, MYD, MYI** 3개의 파일이 만들어진다
- FRM : 테이블 구조가 저장되어 있는 파일
- MYD : 실제 데이터가 있는 파일
- MYI : INDEX 정보가 들어있는 파일

INDEX를 사용하지 않는 경우, MYI 파일은 비어져 있다

그러나 INDEX를 해당 컬럼에 만들게 되면 해당 컬럼을 따로 인덱싱하여 MYI 파일에 입력한다

이후에 사용자가 SELECT 쿼리로 INDEX를 사용하는 쿼리를 사용시 해당 Table을 검색하는 것이 아니라 MYI 파일의 내용을 검색한다

만약, INDEX를 사용하지 않은 SELECT 쿼리라면 해당 Table Full Scan하여 모두 검색한다

이는 책의 뒷부분에 <찾아보기>와 같은 의미로 정리해둔 단어 중에서 원하는 단어를 찾아서 페이지수를 보고 쉽게 찾을 수 있는
개념과 같다. 만약, 이 <찾아보기>가 없다면 처음부터 끝까지 모든 페이지를 보고 찾아와야할 것이다

### INDEX 자료구조

그렇다면 DBMS는 인덱스를 어떻게 관리하고 있는가?

#### B+-Tree 인덱스 알고리즘

일반적으로 사용되는 인덱스 알고리즘은 B+-Tree 알고리즘이다. B+-Tree 인덱스는 컬럼의 값을 변형하지 않고(사실 값의 앞부분만
잘라서 관리한다) 원래의 값을 이용해 인덱싱하는 알고리즘이다

#### Hash 인덱스 알고리즘

컬럼의 값으로 해시 값을 계산해서 인덱싱하는 알고리즘으로 매우 빠른 검색을 지원한다. 하지만 값을 변형해서 인덱싱하므로,
특정 문자로 시작하는 값으로 검색을 하는 전방 일치와 같이 값의 일부만으로 검색하고자 할 때는 해시 인덱스를 사용할 수 없다.
주로 메모리 기반의 데이터베이스에서 많이 사용한다

#### [왜 INDEX를 생성하는데 b-tree를 사용하는가?]

데이터에 접근하는 시간복잡도가 O(1)인 hash table이 더 효율적일 것 같은데? SELECT 지의의 조건에는 부등호(<>) 연산도 
포함된다. hash table을 사용하게 된다면 등호(=) 연산이 아닌 부등호 연산의 경우에 문제가 발생한다. 동등 연산(=)에 특화된
hash table은 데이터베이스의 자료구조로 적합하지 않다

### Primary Index VS Secondary Index

클러스터(Cluster)란 여러 개를 하나로 묶는다는 의미로 주로 사용되는데, 클러스터드 인덱스도 크게 다르지 않다. 인덱스에서
클러스터드는 비슷한 것들을 묶어서 저장하는 형태로 구현되는데, 이는 주로 비슷한 값들을 동시에 조회하는 경우가 많다는
점에서 착안된 것이다. 여기서 비슷한 값들은 물리적으로 인접한 장소에 저장되어 있는 데이터들을 말한다

클러스터드 인덱스는 테이블의 프라이머리 키에 대해서만 적용되는 내용이다. 즉, 프라이머리 키 값이 비슷한 레코드끼리 묶어서
저장하는 것을 클러스터드 인덱스라고 표현한다. 클러스터드 인덱스에서는 프라이머리 키 값에 의해 레코드의 저장위치가 결정되며
프라이머리 키 값이 변경되면 그 레코드의 물리적인 저장 위치 또한 변경되어야 한다. 그렇기 때문에 프라이머리 키를 신중하게
결정하고 클러스터드 인덱스를 사용해야 한다

클러스터드 인덱스는 테이블 당 한 개만 생성할 수 있다. 프라이머리 키에 대해서만 적용되기 때문이다. 이에 반해 non 클러스터드
인덱스는 테이블 당 여러개를 생성할 수 있다

### Composite Index

인덱스로 설정하는 필드의 속성이 중요하다. title, author 이 순서로 인덱스를 설정한다면 title을 search하는 경우, index를
생성한 효과를 볼 수 있지만, author만으로 search하는 경우, index를 생성한 것이 소용이 없어진다. 따라서 SELECT 질의를
어떻게 할 것인가가 인덱스를 어떻게 생성할 것인가에 대해 많은 영향을 끼치게 된다




### INDEX 장점
- 키 값을 기초로 하여 테이블에서 검색과 정렬 속도를 향상시킨다
- 인덱스를 사용하면 테이블 행의 고유성을 강화시킬 수 있다
- 테이블의 기본키는 자동으로 인덱스된다
- 필드 중에는 데이터 형식 때문에 인덱스 될 수 없는 필드도 있다
- 여러 필드로 이루어진(다중 필드) 인덱스를 사용하면 첫 필드 값이 같은 레코드도 구분할 수 있다

참고로 액세스에서 다중 필드 인덱스는 최대 10개의 필드를 포함할 수 있다

### INDEX 단점
- 인덱스를 만들면 .mdb 파일 크기가 늘어난다
- 사용자가 한 페이지를 동시에 수정할 수 있는 병행성이 줄어든다
- 인덱스된 필드에서 데이터를 업데이트하거나 레코드를 추가 또는 삭제할 때 성능이 떨어진다
- 인덱스가 데이터베이스 공간을 차지해 추가적인 공간이 필요해진다(DB의 10% 내외의 공간이 추가로 필요하다)
- 인덱스를 생성하는데 시간이 많이 소요될 수 있다
- 데이터 변경 작업이 자주 일어날 경우에 인덱스를 재작성해야 할 필요가 있기에 성능에 영향을 끼칠 수 있다

따라서 어느 필드를 인덱스해야 하는지 미리 시험해보고 결정하는 것이 좋다. 인덱스를 추가하면 쿼리 속도가 1초 정도 빨라지지만,
데이터 행을 추가하는 속도는 2초 정도 느려지게 되어 여러 사용자가 사용하는 경우, 레코드 잠금 문제가 발생할 수 있다.

또, 다른 필드에 대한 인덱스를 만들게 되면 성능이 별로 향상되지 않을 수도 있다. 예를 들어, 테이블에 회사 이름 필드와 성
필드가 이미 인덱스된 경우에 우편 번호를 필드로 추가해 인덱스에 포함해도 성능이 거의 향상되지 않는다. 만드는 쿼리의 종류와
관계 없이 가장 고유한 값을 갖는 필드만 인덱스해야 한다

### INDEX 목적
RDBMS에는 INDEX가 있다. 인덱스의 목적은 RDBMS의 검색 속도를 높이는데 있다

SELECT 쿼리의 WHERE절이나 JOIN 예약어를 사용했을 때만 인덱스를 사용하며,
SELECT 쿼리의 검색 속도를 빠르게 하는데 목적을 두고 있다
- DELETE, INSERT, UPDATE 쿼리에는 해당 사항이 없으며 INDEX 사용시 오히려 좀 느려진다

### 상황 분석
#### [사용하면 좋은 경우]
1. WHERE절에서 자주 사용되는 Column
2. 외래키가 사용되는 Column
3. Join에 자주 사용되는 Column

#### [사용을 피해야 하는 경우]
- Data 중복도가 높은 Column
- DML이 자주 일어나는 Column

### DML에 취약
1. INSERT
    - index split : 인덱스의 Block들이 하나에서 두개로 나누어지는 현상
    - 인덱스는 데이터가 순서대로 정렬되어야 한다. 기존 블록에 여유 공간이 없는 상황에서 그 블록이 새로운 데이터가
    입력되어야 할 경우, 오라클이 기존 블록의 내용 중 일부를 새 블록에다가 기록한 후, 기존 블록에 빈 공간을 만들어서
    새로운 데이터를 추가하게 된다
    - 성능면에서 매우 불리하다
      - Index split은 새로운 블록을 할당받고 Key를 옮기는 복잡한 작업을 수행한다. 모든 수행 과정이 Redo에 기록되고
      많은 양의 Redo를 유발한다
      - Index split이 이루어지는 동안 해당 블록에 대해 키 값이 변경되면 안되므로 DML이 블로킹된다

2. DELETE
    - 테이블에서 데이터가 Delete될 경우, 지워지고 다른 데이터가 그 공간을 사용할 수 있다
    - index에서 데이터가 delete될 경우, 데이터가 지워지지 않고 사용 안됨 표시만 해둔다
    - 즉, 테이블에 데이터가 1만건 있는 경우, 인덱스에는 2만건이 있을 수 있다는 뜻이다
    - 인덱스를 사용해도 수행 속도를 기대하기 어렵다

3. UPDATE
    - 인덱스에는 Update 개념이 없다
    - 테이블에 update가 발생할 경우, 인덱스에서는 delete가 먼저 발생한 후 새로운 작업의 insert 작업이 발생한다
    - delete와 insert 두 개의 작업이 인덱스에서 동시에 일어나 다른 DML보다 더 큰 부하를 주게 된다


#### [나의 정리]
> 인덱스는 검색 속도를 높이기 위해 사용한다. 테이블을 풀스캔하는 것이 아니라 MYI에 있는 인덱스 파일을 검색하여 스캔한다. 따라서 SELECT의 경우 속도가 향상되는 장점이 있다. WHERE 절 컬럼, 외래키 사용 컬럼, Join 컬럼을 사용하면 효과가 있다. 그런데 삽입, 수정, 삭제의 경우는 인덱스를 사용하지 않을 때보다 성능이 좋지 않다. 따라서 DML이 자주 일어나는 컬럼이나 데이터 중복도가 높은 컬럼에는 사용하지 않는 것이 좋다. 삽입, 삭제, 수정이 발생할 때 인덱스를 재작성해야 하기 때문에 처리 속도가 늦어지는 것이다. 삽입의 경우 인덱스의 블록들이 두 개로 나누어질 수 있는 index split 현상이 발생할 수 있어 기존 블록에서 새 블록을 할당받고 키를 옮기는 작업이 수행되어야 한다. 삭제의 경우 실제 테이블에서 데이터가 삭제될 때 인덱스에서는 데이터가 지워지지 않고 사용 안됨 표시만 해둔다. 즉 테이블에 있는 데이터보다 인덱스에 있는 데이터가 많아질 수 있어 수행 속도 저하가 발생할 수 있다. 수정의 경우 인덱스는 update 개념이 없다. 테이블에 update가 발생하면 인덱스에서는 삭제가 먼저 발생하고 삽입 작업이 발생한다. 삭제와 삽입 두 작업이 인덱스에서 동시에 일어나 DML보다 더 큰 부하를 준다.


## < 다른 정리 >

## 스토리지와 파일 구조

### DBMS Storage
- DBMS는 데이터를 "hard" disk에 저장
- Disk access가 DBMS의 성능에 중요한 문제
    - READ : disk -> main memory 데이터 전송
    - WRITE : memory -> disk 데이터 전송
    - higher cost than memory access
- Main memory에 모두 저장 못 하는 이유
    - 비용 문제
    - 메모리에 저장되는 데이터는 volatile(손상될 위험이 큼)
- 효과적인 memory-disk 데이터 전송을 위한 Buffer management가 필요

![image](https://user-images.githubusercontent.com/67304980/137704734-cfc5e2ec-d13e-4c0d-9ca9-37f6a9daac57.png)

### Disk space management
- DBMS에서 가장 낮은 layer에서 disk의 space를 관리
- 상위 component에서 다음과 같은 request 요청
    - allocate & de-allocate a page
    - read & write a page
- 성능을 위해 page들을 최대한 sequential하도록 allocation한다
    - seek / rotation delay를 줄이기 위해

### Buffer Management

![image](https://user-images.githubusercontent.com/67304980/137705342-8a1a71ab-b3bf-4e4a-a50d-6850a3b8dbea.png)

- Page buffering process
    - Pool에서 page를 저장할 frame을 선택
        - free frame이 있으면 선택
        - 없는 경우, replacement policy(LRU)에 의해 오래된 frame unpin후에 frame 결정
    - page를 disk에서 읽어 frame에 저장
    - pin : pin_count++, page를 사용하는 사용자가 있음을 표시하기 위해, pin_count가 0인 경우만 replacement 대상
    - 주소를 반환
- Page 사용자는 사용이 끝나면 page에 대한 unpin을 불러주어야 한다
    - 만약 업데이트 경우 dirty flag set
- 읽는 page list가 예측되면 성능을 향상 위해 prefetch 기능 제공

### Format in File

#### Record formats : Fixed Length

![image](https://user-images.githubusercontent.com/67304980/137706292-6ac12377-1a6a-4f91-ac68-52d5a098cf85.png)

- Field 타입과 size에 대한 정보가 system catalog에 저장
- 필요한 Field access할 때 pointer연산으로 빠르게 주소 반환

![image](https://user-images.githubusercontent.com/67304980/137706658-3ae39fa2-da8e-4304-b825-5c341cf57dd4.png)

![image](https://user-images.githubusercontent.com/67304980/137706771-cf072efa-dd11-4c2d-a16a-0b2818623352.png)

![image](https://user-images.githubusercontent.com/67304980/137706956-95cecc06-8634-49b1-943d-78ab717732c6.png)

## 인덱스
- 특정 기준에 맞는 rows를 빠르게 찾기 위해 사용
- Index trade-off
    - Search 성능 향상
    - 인덱스를 관리하는 비용 발생 - DML 성능 저하
    - 인덱스를 저장하기 위한 space 사용 증가
- Search Key
    - Index에서 검색을 위해 사용되는 column 또는 column의 집합
    - 한 테이블에서 다른 search key를 가지는 다수의 index 생성 가능

### 인덱스를 만들 때 고려 사항
- 인덱스가 언제 사용될 것인가?
    - 정확하게 key와 일치하는 row들을 찾을 때 사용할 것인가?
    - Range query에 사용하나? (values between, >, <= 등)
    - 테이블에 모든 row들을 조회할 때 사용할 것인가?
- 테이블이 얼마나 자주 업데이트 되는가?
    - DML은 index 관리를 위한 update가 발생
- 인덱스의 key가 super key인가 또는 primary key인가?
- 하나의 key로 여러 row들이 가능한가? Unique or not

### Tree(Ordered) Index VS Hash Index
- Tree Index는 search key의 순서로 index entry가 정렬
    - range query나 order by operation에 유리
    - B + Tree가 주로 사용
- Hash Index
    - index entry의 위치를 hash function을 이용하여 bucket의 위치를 지정하여 저장
    - search에 효율성 : O(1)
    - DML이 발생할 때 index 관리에 유리

### Index on SQL
- DBMS 다음의 경우에 index를 생성한다
    - Primary key
    - unique constraint(열의 값들이 모두 다름)
- Create index statement in SQL

![image](https://user-images.githubusercontent.com/67304980/137708464-8f3df33c-e719-43d7-82fa-e656fda03175.png)


## B+Tree 인덱스

### B+Tree
- DBMS에 index로 사용되는 tree structure
- 같은 레벨의 모든 leaf 노드에 key와 실제 data pointer 저장
- Components
    - Root Node(적어도 두 개의 children을 가질 때)
    - Non-leaf Node
    - Leaf Node
- 차수 n (Order n)는 노드 사이즈를 결정한다
    - size of Node = (n+1)pointers + n key

![image](https://user-images.githubusercontent.com/67304980/137709013-2f4a1838-498b-48d4-b4b5-6999ce5f2f49.png)

![image](https://user-images.githubusercontent.com/67304980/137709299-cc2a9f20-9791-4613-b29e-d26e0ca65287.png)

![image](https://user-images.githubusercontent.com/67304980/137709372-a934c681-ab56-44c7-bc8c-bb875c691a42.png)

![image](https://user-images.githubusercontent.com/67304980/137709475-9bfb0fdd-1397-4f92-affe-505851557a2d.png)

![image](https://user-images.githubusercontent.com/67304980/137709725-e20c5ff3-7593-4594-95f2-66315eb640f2.png)

![image](https://user-images.githubusercontent.com/67304980/137709936-bbc68bbf-c501-41f1-b73c-74062dca9e4c.png)

![image](https://user-images.githubusercontent.com/67304980/137710109-419a4451-314e-480a-942e-01419549012c.png)

![image](https://user-images.githubusercontent.com/67304980/137710140-426d46d5-2d65-4e34-b34d-ec69601dec18.png)

![image](https://user-images.githubusercontent.com/67304980/137710290-fa0fc848-cfc5-42c5-83ac-06310ec63173.png)

![image](https://user-images.githubusercontent.com/67304980/137710313-3e40b703-89bd-43db-8d3b-830af98d6cf8.png)

![image](https://user-images.githubusercontent.com/67304980/137710342-cd338b5f-e09f-46ac-9692-c2ad82e8c82e.png)

![image](https://user-images.githubusercontent.com/67304980/137710518-28d0e81a-d729-42c5-a9ae-6ff20a1fe13f.png)

![image](https://user-images.githubusercontent.com/67304980/137710555-6b1e6804-fc88-4aec-842f-dc95e615edb6.png)

![image](https://user-images.githubusercontent.com/67304980/137710603-6ebab3a1-f7cb-4851-9e95-d15a17514e57.png)


## 해쉬 인덱스

- key에 대한 hash function을 통해 데이터 위치를 찾는 index
- index entry들이 bucket이라는 단위로 저장된다
- bucket을 할당하는 방법
    - static hashing
        - bucket을 고정된 수로 할당
    - dynamic hashing
        - 데이터 양에 따라 가변적으로 bucket을 늘려나간다

![image](https://user-images.githubusercontent.com/67304980/137711262-cec34276-5879-4d05-990b-6adeb3dc2f5b.png)

### Static Hashing
- Hash function의 결과에 bucket수로 mod 연산하여 위치 결정
- Bucket 수가 미리 정해져 있기 때문에 overflow 발생 가능
    - Overflow chaining - linked list형태로 같은 hash key를 갖는 bucket을 연결한다

![image](https://user-images.githubusercontent.com/67304980/137711669-25316354-5d3e-408f-8fe4-5ab1491e8d98.png)


### Dynamic Hashing - Extensible Hashing
- Static hashing과 달리 bucket 수를 동적으로 변경하면서 hashing하는 방법
- Hashing function의 결과를 binary로 표현하여 bucket의 할당에 binary의 prefix을 사용
    - if prefix = 0, 2<sup>0</sup>=1 bucket
    - if prefix = 1, 2 bucket, prefix = 3, 2<sup>3</sup>=8
- bucket이 overflow가 발생하면 hash function의 prefix를 늘려가며 bucket의 개수를 늘린다

![image](https://user-images.githubusercontent.com/67304980/137712228-85d60f41-5cfa-417b-b916-eac83080838a.png)

![image](https://user-images.githubusercontent.com/67304980/137712825-8e55b8e1-7d9f-4b73-9c93-5f4b782b754e.png)

![image](https://user-images.githubusercontent.com/67304980/137712862-535c6c25-4416-404f-a7ed-a100e365c50c.png)


#### [나의 정리]
> 인덱스로 주로 사용하는 두 가지는 해시와 BTree이다. 먼저 해시 인덱스는 해시 함수를 통해 나온 해시 값을 이용하여 메모리 공간에 접근을 하기 때문에 O(1)의 시간 복잡도를 가진다. 하지만 이는 '단 하나의 데이터를 탐색하는 시간'에만 O(1)이다. 예를 들어 1,2,3,4,5가 저장되어 있는 해시 테이블에서 3이라는 데이터를 찾을 때에만 O(1)이라는 것이다. 우리는 등호 뿐 아니라 부등호도 사용한다. 현재 해시를 이용한 경우 모든 값이 정렬되어있지 않으므로, 해시 테이블에서는 특정 기준보다 크거나 작은 값을 찾을 수 없다. 찾을 수는 있지만 시간복잡도 O(1)을 보장하지 못한다. 또한 전방(Prefix) 일치와 같이 값의 일부만으로 검색하고자 할 때 해시 인덱스를 사용할 수 없다. 주로 메모리 기반 데이터베이스에서 많이 사용한다. BTree 인덱스의 경우 Binary Search Tree 방식을 따라 데이터 탐색, 저장, 수정, 삭제에도 O(logN)의 시간 복잡도를 보장한다. 또한 항상 정렬된 상태로 특정 값보다 크고 작은 부등호 연산에 문제가 없다.


















