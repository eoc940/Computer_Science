## UDP

- User Datagram Protocol의 약자이다
- 데이터를 데이터그램 단위로 처리하는 프로토콜
  - 데이터그램 : 독립적인 관계를 지니는 패킷
- 비연결형 프로토콜로 사전에 연결 설정 없이 데이터를 전달한다
- 사전에 연결 설정을 하지 않은 데이터그램 방식을 통해 데이터를 전달하기 때문에 하나의 메시지에서 분할된 각각의 패킷은
서로 다른 경로로 전송될 수 있다
- 송신측에서 전송한 패킷의 순서와 수신측에 도착한 패킷의 순서가 다를 수 있다. 그러나 서로 다른 경로로 패킷을 처리함에도
불구하고 순서를 부여하거나 재조립하지 않는다
- 흐름 제어, 혼잡 제어, 오류 제어를 하지 않으므로 손상된 세그먼트에 대한 재전송을 하지 않는다
- 이로 인해 속도가 빠르며 네트워크 부하가 적다는 장점이 있지만, 신뢰성 있는 데이터 전송을 보장하지 못한다
- UDP는 RTP(Real Time Protocol), Multicast, DNS 등에서 사용된다

DNS 같은 경우 누군가 DNS 서비스를 요청할 때마다 TCP처럼 Session을 맺고 통신한다면 속도도 느리고, 서버 리소스도 엄청나게
소모될 것이다

그런가 하면 NMS(Network Management Server)가 5분에 한번씩 장비 상태를 점검하기 위해 정보를 읽어오는데 수백, 수천대의
장비와 Session을 맺어야 한다면 이것도 마찬가지로 문제가 생긴다

그렇게 때문에 UDP를 사용한다

재전송을 하면 안되는 서비스는 대표적으로 RTP이다. 전화를 하고 있다고 가정해보자

여, 보, 세, 요 라는 4개의 데이터를 전송했는데 '세'를 못받았다고 다시 보내달라고 하면 '여보요세'가 될 것이다

이럴 때는 그냥 '여보X요'로 전달하는게 나은 상황이다

또한 Multicast 서비스가 UDP를 사용한다

1:N으로 통신하는 방식에서 한 사람이 데이터를 받지 못했다고 재전송을 요청한다고 가정해보자. 제대로 받은 사람들도 해당
데이터를 다시 받아서 처리해야 한다는 문제점이 발생할 수 있기 때문에 UDP를 사용한다


### UDP는 왜 사용할까?
- UDP의 결정적인 장점은 데이터의 신속성이다. 데이터의 처리가 TCP보다 빠르다
- 주로 실시간 방송과 온라인 게임에서 사용된다. 네트워크 환경이 안 좋을때 끊기는 현상을 생각하면 된다

### DNS(Domain Name Service)에서 UDP를 사용하는 이유
- Request의 양이 작음 -> UDP Request에 담길 수 있다
- 3 way handshaking으로 연결을 유지할 필요가 없다(오버헤드 발생)
- Request에 대한 손실은 Application Layer에서 제어가 가능하다
- DNS : port 53번
- 그러나 TCP를 사용할 때가 있다. 크기가 512(UDP 제한)이 넘을 때, TCP를 사용해야 한다

### UDP Header

![image](https://user-images.githubusercontent.com/67304980/132818610-08a2cce0-e6ae-4909-ae98-dc4ddf9293ad.png)

- Source port : 시작 포트
- Destination port : 도착지 포트
- Length : 길이
- Checksum : 오류 검출 -> 중복 검사의 한 형태로, 오류 정정을 통해 공간이나 시간 속에서 송신된 자료의 무결성을 보호하는
단순한 방법이다

- 이렇게 간단하므로, TCP보다 용량이 가볍고 송신 속도가 빠르게 작동됨
- 그러나 확인 응답을 못하므로, TCP보다 신뢰도가 떨어짐
- UDP는 비연결성, TCP는 연결성으로 정의할 수 있음

### DNS와 UDP 통신 프로토콜을 사용

DNS는 데이터를 교환하는 경우이다

이때, TCP를 사용하게 되면, 데이터를 송신할 때까지 세션 확립을 위한 처리를 하고, 송신한 데이터가 수신되었는지 점검하는
과정이 필요하므로, Protocol Overhead가 UDP에 비해서 크다

DNS는 Application layer protocol이다

모든 Application layer protocol은 TCP, UDP 중 하나의 Transport layer protocol을 사용해야 한다

(TCP는 reliable, UDP는 not reliable이다) / DNS는 reliable해야할 것 같은데 왜 UDP를 사용할까?

#### 사용하는 이유
1. TCP가 3-way handshake를 사용하는 반면, UDP는 connection을 유지할 필요가 없다
2. DNS request는 UDP segment에 꼭 들어갈 정도로 작다. DNS query는 single UDP request와 server로부터의 single UDP reply로
구성되어 있다
3. UDP는 not reliable이나, reliability는 application layer에 추가될 수 있다(Timeout 추가나, resend 작업을 통해)

DNS는 UDP를 53번 port에서 사용한다

**그러나 TCP를 사용하는 경우가 있다**

Zone tranfer를 사용해야 하는 경우에는 TCP를 사용해야 한다

(Zone Transfer : DNS 서버 간의 요청을 주고받을 때 사용하는 transfer)

만약에 데이터가 512 bytes를 넘거나, 응답을 못받은 경우 TCP로 한다

















