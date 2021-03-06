## 로드 밸런싱

둘 이상의 CPU or 저장장치와 같은 컴퓨터 자원들에게 작업을 나누는 것

![image](https://user-images.githubusercontent.com/67304980/132987365-04e796ff-71a8-4ff4-8a0f-f763ee1ff26f.png)

요즘 시대에는 웹사이트에 접속하는 인원이 급격히 늘어나게 되었다

따라서 이 사람들에 대해 모든 트래픽을 감당하기에 1대의 서버로는 부족하다. 대응 방안으로 하드웨어의 성능을 올리거나(Scale-up)
여러대의 서버가 나눠서 일하도록 만드는 것(Scale-out)이 있다. 하드웨어 향상 비용이 더욱 비싸기도 하고, 서버가 여러대면
무중단 서비스를 제공하는 환경 구성이 용이하므로 Scale-out이 효과적이다. 이때 여러 서버에게 균등하게 트래픽을 분산시켜주는
것이 바로 로드 밸런싱이다

로드 밸런싱은 분산식 웹 서비스로 여러 서버에 부하(Load)를 나누어주는 역할을 한다. Load Balancer를 클라이언트와 서버 사이에
두고, 부하가 일어나지 않도록 여러 서버에 분산시켜주는 방식이다. 서비스를 운영하는 사이트의 규모에 따라 웹 서버를 추가로
증설하면서 로드 밸런서로 관리해주면 웹 서버의 부하를 해결할 수 있다

Scale-up : Server가 더 빠르게 동작하기 위해 하드웨어의 성능을 올리는 방법

Scale-out : 하나의 Server보다는 여러 대의 Server가 나눠서 일을 하는 방법

### 로드 밸런서가 서버를 선택하는 방식
- 라운드 로빈(Round Robin) : CPU 스케줄링의 라운드 로빈 방식 활용
  - DNS(Domain name system) Round Robin 방식의 문제점
    - 1. 서버의 수만큼 공인 IP주소가 필요하다. 부하 분산을 위해 서버의 대수를 늘리기 위해서는 그만큼의 공인 IP가 필요하다
    - 2. 균등하게 분산되지 않는다. 모바일 사이트 등에서 문제가 될 수 있는데, 스마트폰의 접속은 캐리어 게이트웨이라고 하는 프록시 서버를 경유한다. 프록시 서버에서는 이름변환 결과가 일정 시간 동안 캐싱되므로 같은 프록시 서버를 경유하는 접속은 항상 같은 서버로 접속된다. 또한 PC용 웹 브라우저도 DNS 질의 결과를 캐싱하기 때문에 균등하게 부하분산되지 않는다. DNS 레코드의 TTL값을 짧게 설정함으로써 어느 정도 해소가 되지만, TTL에 따라 캐시를 해제하는 것은 아니므로 반드시 주의가 필요하다
    - 3. 서버가 다운되어도 확인 불가능하다. DNS 서버는 웹 서버의 부하나 접속 수 등의 상황에 따라 질의결과를 제어할 수 없다. 웹 서버의 부하가 높아서 응답이 느려지거나 접속 수가 꽉 차서 접속을 처리할 수 없는 상황인지를 전혀 감지할 수가 없기 때문에 어떤 원인으로 다운되더라도 이를 검출하지 못하고 유저들에게 제공한다. 이 때문에 유저들은 간혹 다운된 서버로 연결되기도 한다. DNS 라운드 로빈은 어디까지나 부하분산을 위한 방법이지 다중화 방법은 아니므로 다른 SW와 조합해서 관리할 필요가 있다
    - Weighted round robin(WRR) : 각각의 웹 서버에 가중치를 가미해서 분산 비율을 변경한다. 물론 가중치가 큰 서버일수록 빈번하게 선택되므로 처리능력이 높은 서버는 가중치를 높게 설정하는 것이 좋다
    - Least Connection : 접속 클라이언트 수가 가장 적은 서버를 선택한다. 로드밸런서에서 실시간으로 connection 수를 관리하거나 각 서버에서 주기적으로 알려주는 것이 필요하다
  
- Least Connections : 연결 개수가 가장 적은 서버 선택(트래픽으로 인해 세션이 길어지는 경우 권장)
- Source : 사용자 IP를 해싱하여 분배(특정 사용자가 항상 같은 서버로 연결되는 것 보장)

### 로드 밸런서 장애 대비

서버를 분배하는 로드 밸런서에게 문제가 생길 수 있기 때문에 로드 밸런서를 이중화하여 대비한다(Active 상태, Passive 상태)

![image](https://user-images.githubusercontent.com/67304980/132987620-5f367e8f-3493-49cd-901c-db16f3e50a0d.png)

- 두 로드 밸런서들은 서로를 Health Check한다
- 메인 로드 밸런서가 동작하지 않으면 가상 IP(VIP, Virtual IP)는 여분의 Load Balancer로 변경된다
- 여분의 로드 밸런서로 운영하게 된다



