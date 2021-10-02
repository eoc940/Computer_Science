## Views

### View의 목적
- 어떤 경우에 모든 사용자가 전체 논리적 모델(데이터베이스에 저장된 모든 실제 테이블)을 보는 건 문제가 될 수 있다
- 필요한 데이터만 특정 사용자들에게 유출할 필요가 있을 때 사용
  - employ 테이블에서 아이디, 이름, 부서 조회 가능하게 하지만 salary 정보를 숨기고 싶을 때
- 질의문 작성을 쉽게 만들어 준다
  - group by나 aggregation function 등을 미리 정의
- 데이터 종속성 제거
  - 응용프로그램은 뷰를 통해 접근함으로써 테이블 스키마 변화에 신경 쓸 필요가 없다

### View의 정의
![image](https://user-images.githubusercontent.com/67304980/135713563-6282ff8e-7037-4882-a1a8-c5880fbdc2ca.png)

### View Expansion
![image](https://user-images.githubusercontent.com/67304980/135713664-50b562a3-0217-4e67-85d4-24f062c6f662.png)

### Updatable View
![image](https://user-images.githubusercontent.com/67304980/135713708-366f1d81-336d-4f39-ba25-1909948c3225.png)
![image](https://user-images.githubusercontent.com/67304980/135713713-b49742a8-b95f-420f-9e15-25d315f1d766.png)

### Materialized View
![image](https://user-images.githubusercontent.com/67304980/135713724-7cc36b6d-1a2d-42be-b12e-42f6272730bf.png)

### Drop View
![image](https://user-images.githubusercontent.com/67304980/135713731-33ec8691-8432-4cc9-addb-49a63008d2bf.png)



