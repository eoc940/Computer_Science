## Prologue

Client의 상태 정보를 Server가 저장하느냐 마느냐로 Stateful / Stateless 서비스를 구분하던 추상적인 개념 이해에서 벗어나,
더 깊은 수준의 Stateful / Stateless 서비스에 대한 이해를 해보자

1장부터 3장을 통해 Stateful / Stateless 서비스의 차이점을 이해하고, 이를 바탕으로 최근 웹서비스들이 Stateless 서비스
구조를 선호하는 이유도 알아보도록 하자

마지막 4장은 Stateless 서비스와 관련되 키워드로 HTTP, REST에 대해 알아보자

## 1. Stateful Service

Stateful 구조는 Server와 Client간 세션의 State(상태)에 기반하여 Client에 response를 보낸다.

이를 위해 세션 '상태'를 포함한 Client와의 세션 정보를 server에 저장하게 된다

대표적인 Stateful 구조를 따르는 프로토콜로 TCP가 있다. TCP의 3-way handshaking 과정을 생각해보자. Server와 Client는
3-way handshaking 과정에서 SYN과 SYNACK를 주고받으며 양단간 세션 상태를 established한 상태로 만든다. 세션 상태가
established되면 client와 server는 데이터를 주고 받을 수 있다. 이렇게 TCP는 세션 상태에 따라 Server의 응답이 달라지는
Stateful 프로토콜이다

TCP의 데이터 전송과정도 확인해 보자. server는 client로부터 송신된 패킷헤더를 파싱하여 앞으로 수신해야할 데이터의
크기(window), sequence 번호 등을 저장한다. 그리고 해당 크기의 데이터가 모두 수신 될 때까지 client와의 세션을 유지한다.
이 과정에서 server는 앞으로 수신할 데이터의 크기, sequence 번호 등과 같이 client와의 세션 정보를 저장하게 된다

![image](https://user-images.githubusercontent.com/67304980/133034042-476373d5-1c90-4534-a63f-6305a3e02844.png)

따라서 TCP는 client와의 세션 정보를 server에 저장하고, 세션 상태에 기반한 server의 응답이 달라진다는 점에서 Stateful하다고
말할 수 있다

Stateful Service는 이러한 Stateful한 특성 즉 1. 세션 정보를 server에 저장, 2. 세션 상태(State)에 따른 응답 \
을 만족하도록 설계된 서비스 구조이다

아래 그림과 같은 Stateful Service 구조를 생각해 보자
ClientA의 요청이 로드밸런서를 통해 임의의 서버, 그림의 경우 Server#1에 전달되면 Server#1에는 ClientA의 세션 정보가 저장된다.
그러나 로드밸런서에 연결된 다른 server, 그림의 경우 Server#2에는 해당 ClientA에 대한 정보가 없으므로 Server#2는 ClientA와의
세션 인증을 처음부터 다시 해야할 필요가 있으며, 이러한 redundant한 작업을 없애기 위해 ClientA의 요청을 Server#1을 향해
유지되도록 관리가 필요하다

![image](https://user-images.githubusercontent.com/67304980/133034457-67424fa4-6dca-4f1d-bd0d-12d8f7730626.png)

## 2. Stateless Service

Stateless 구조는 server의 응답이 client와의 세션 상태와 독립적이다. Stateless 구조에서 server는 단순히 요청이 오면 
응답을 보내는 역할만 수행하며, 세션 관리는 client에게 책임이 있다

따라서 이러한 Stateless 구조는 client와의 세션 정보를 기억할 필요가 없으므로, 이러한 정보를 서버에 저장하지 않는다.
대신 필요에 따라 외부 DB에 저장하여 관리할 수 있다.

대표적인 Stateless 프로토콜로는 UDP와 HTTP 등이 있다. UDP를 예로 들자. 알다시피 UDP는 TCP와 달리 handshaking 과정을
통해 연결 세션을 인증하는 절차를 수행하지 않는다. 세션 상태에 관계 없이 단순히 데이터그램을 source에서 destination쪽으로
전송한다

client가 송신하려 했던 모든 데이터가 server쪽에 수신 되었는지 확인하지 않는다. 따라서 server쪽은 client와의 세션 정보를
전혀 저장하지 않는다

![image](https://user-images.githubusercontent.com/67304980/133034874-62706e20-e844-45cf-aa49-7727da612777.png)

따라서 UDP는 client와의 세션 상태에 관계없이 요청에 대한 응답만을 수행한다. 또한 Client와의 세션 정보를 server가
저장하지 않는다는 점에서 Stateless 하다고 말할 수 있다

Stateless Service는 이러한 Stateless한 특성, 1. 세션 정보를 server에 저장하지 않음, 2. 세션 State(상태)에 무관한 응답 \
을 만족하도록 설계된 서비스 구조이다

아래와 같은 Stateless 서비스 구조를 생각해보자. 이번에도 ClientA의 요청이 로드밸런서를 통해 Server#1으로 전달 되었다고
생각해 보자. Stateful 서비스 구조에서는 server에 직접 ClientA와의 세션 정보를 저장했지만, Stateless 서비스 구조에서는
이 정보를 server에 저장하지 않고 외부 DB에 저장한다

따라서 ClientA의 요청이 Server#2로 전달 되더라도, Server#2는 DB를 통해 ClientA 정보에 접근 가능하다. 이러한 Stateless
구조에서는 ClientA의 요청을 Server#1에만 유지되도록 관리할 필요가 없다

![image](https://user-images.githubusercontent.com/67304980/133036702-4edbd581-26d2-4546-91aa-fa02c82d45db.png)

## 3. Why Stateless Service?

위에서 알 수 있듯이, Stateless 구조가 Stateful 구조 대비 갖는 몇가지 장점들로 인해 최근의 웹서비스 구조는 모두 
Stateless 구조 기반을 따르고 있다.

그렇다면 Stateless Service가 어떤 점에서 유리할까? 여러지 이유가 있겠지만 가장 큰 이점은 **Scaling이 자유롭다**는 것이다

트래픽의 급증으로 서버가 scale out된 상황을 생각해보자

Stateful 서비스의 경우, client의 세션 정보가 새로 scale out된 서버에 저장되어 있지 않다. 따라서 세션 정보를 옮겨주는
등의 부수적인 관리가 요구된다

![image](https://user-images.githubusercontent.com/67304980/133037668-7a21fef4-ec29-41dc-9877-95d8d0c80aee.png)

Stateless 서비스의 경우, 어차피 server는 client 세션 관리를 하지 않으므로 걱정할 필요가 없다

![image](https://user-images.githubusercontent.com/67304980/133037744-5d0b2dd4-96f3-4f10-bbe7-7cf6ba41a64b.png)

scaling은 최근 웹 서비스들이 요구하는 중요한 기능이다. serverless 구조의 서비스 구현에서는 필수적이다. 따라서 최근 웹
서비스 트렌드 동향에 발맞춰, scaling이 자유로운 Stateless 서비스 구조가 각광받고 있다

## 4. Stateless Service 및 HTTP, REST

stateless service를 검색하면 나오는 연관 검색어들이 HTTP, REST이다. 각 용어들의 대분류를 해보자

Stateless와 Representational State Transfer(REST)는 구조(Architecture)의 이름이다. 반면 HTTP는 프로토콜이다

2장에서 말했듯, HTTP는 Stateless한 프로토콜이다. 그리고 REST는 이러한 HTTP 프로토콜 상에 구현된 Resource Oriented
Architecture(ROA) 설계 구조이다.

따라서 HTTP와 REST모두 Stateless한 성격을 가진 녀석들이며, HTTP는 Stateless한 성격을 가진 프로토콜, REST는 Stateless한
성격을 가진 설계 구조이다.






