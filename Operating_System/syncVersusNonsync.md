### 동기와 비동기
- 해야할 일이 빨래, 설거지, 청소 3가지가 있다고 가정하가
  - 이 일들을 동기적으로 처리한다면 빨래를 하고 설거지를 한 뒤 청소를 한다
  - 비동기적으로 일을 처리한다면 빨래하는 업체에게 빨래를 시킨다. 설거지 대행 업체에 설거지 대행 업체에 설거지를
  시키고, 청소 대행 업체에 청소를 시킨다. 셋 중 어떤 것이 먼저 완료될지는 알 수 없다. 일을 모두 마친 업체는
  나에게 알려줄 것이니 나는 다른 작업을 할 수 있다
    - 이때는 백그라운드 스레드에서 해당 작업을 처리하는 경우의 비동기를 의미한다

일반적으로 동기와 비동기의 차이는 메서드를 실행시킴과 동시에 반환값이 기대되는 경우를 동기라고 표현하고 그렇지
않은 경우에 대해서 비동기라고 표현한다. "동시에"라는 말은 실행되었을 때 값이 반환되기 전까지는 blocking되어 있다는
것을 의미한다. 비동기의 경우, blocking되지 않고 이벤트 큐에 넣거나 백그라운드 스레드에게 해당 task를 위임하고 
바로 다음 코드를 실행하기 때문에 기대되는 값이 바로 반환되지 않는다.

#### 개념적 설명
- 동기(Synchronous : 동시에 일어나는)
  - 동시에 일어난다는 뜻이다. 요청과 그 결과가 동시에 일어난다는 약속
  - 바로 요청을 하면 시간이 얼마가 걸리던지 요청한 자리에서 결과가 주어져야 한다
  - 요청과 결과각 한 자리에서 동시에 일어난다
  - A노드와 B노드 사이의 작업 처리 단위(transaction)를 동시에 맞춘다

- 비동기(Asynchronous : 동시에 일어나지 않는)
  - 동시에 일어나지 않는다를 의미한다. 요청과 결과각 동시에 일어나지 않을 것이라는 약속이다
  - 요청한 그 자리에서 결과가 주어지지 않는다
  - 노드 사이의 작업 처리 단위를 동시에 맞추지 않아도 된다

- 동기 방식은 설계가 매우 간단하고 직관적이지만 결과가 주어질 때까지 아무것도 못하고 대기해야 한다는 단점이 있다
- 비동기 방식은 동기보다 복잡하지만, 결과가 주어지는데 시간이 걸리더라도 그 시간동안 다른 작업을 할 수 있으므로
자원을 효율적으로 사용할 수 있다는 장점이 있다

### blocking, non-blocking and Async

#### blocking
![image](https://user-images.githubusercontent.com/67304980/131362489-b8e6aa99-78df-404e-aa22-466cb8d545ad.png)
- I/O 작업은 user level에서 수행할 수 없고 kernel level로 들어가야 한다. 따라서 user level에서 kernel level로
system call을 보내서 I/O 작업을 수행한다. 이때 application단의 스레드에 block이 걸린다(I/O 작업이 끝나기 전까지
반환되지 않기 때문) 그리고 kernel level에서 해당 I/O 작업이 끝나고 데이터를 반환하면 그때서야 application단의
스레드에 걸렸던 block이 풀린다. 즉 application단에서는 아무런 동작도 안하는 것처럼 보인다
- 예를 들어 C언어의 scanf()가 있다. 프로그램은 사용자가 입력하기 전까지 대기한 상태로 scanf는 return하지 않는다
- wait queue에 들어간다
- 단점 : cpu낭비가 심하다

#### non-blocking
![image](https://user-images.githubusercontent.com/67304980/131363024-743a776a-653c-4779-8b62-2a5d0a45d5d9.png)
- blocking I/O 방식과 다르게 non-blocking I/O 방식은 kernel level에서 I/O 작업을 수행하는 동안 user단의 작업을
중단시키지 않는다. 즉 user가 system call을 보내면 kernel에서는 일단 바로 결과를 반환한다. 여기서 반환하는 값은
그 순간 가져올 수 있는 데이터에 해당한다. 이후로 계속적으로 가져올 수 있는 데이터를 축적한다
- wait queue에 들어가지 않는다
- 단점 : 가져올 수 있는 데이터를 축적하는 과정에서 user level에서 원하는 사이즈가 되었는지 계속해서 확인해줘야
한다(polling). 이 과정에서 수많은 클라이언트의 요청이 동시 다발적으로 일어나면 cpu에 부담이 될 수 있다

#### Synchronous
![image](https://user-images.githubusercontent.com/67304980/131363531-b1d84dfc-3c76-4f79-9d66-a50f9120cc45.png)
- Synchronous는 system call이 끝날때까지 기다리고 결과물을 가져온다

#### Asynchronous
![image](https://user-images.githubusercontent.com/67304980/131363634-2fd54473-92e0-4444-97d4-f8be28c1150f.png)
- Asynchronous에서는 system call이 완료되지 않아도 기다리지 않는다. 나중에 완료가 되면 그때 결과물을 가져온다

> blocking / non-blocking은 호출되는 함수가 바로 리턴하는지 아닌지가 포인트
> synchronous / asynchronous는 호출되는 함수의 작업 완료 여부를 누가 신경쓰냐가 포인트

#### blocking VS synchronous
- synchronous : 시스템의 반환을 기다리는 동안 waiting queue에 머무는 것이 필수가 아님. 호출하는 함수가 호출되는
함수의 작업 완료 후 리턴을 기다리거나, 또는 호출되는 함수로부터 바로 리턴 받더라도 작업 완료 여부를 호출하는 
함수 스스로 계속 확인하고 신경쓰면 synchronous
- blocking : 시스템의 반환을 기다리는 동안 waiting queue에 머무는 것이 필수, 호출된 함수가 자신의 작업을 모두
마칠 때까지 호출한 함수에게 제어권을 넘겨주지 않고 대기하게 만들면 blocking
- 둘다 시스템의 반환을 기다린다는 측면에서 같은 개념

#### non-blocking VS Asynchronous
- Asynchronous : system call이 반환될 때 실행된 결과와 함께 반환될 경우, 호출되는 함수에게 callback을 전달해서,
호출되는 함수의 작업이 완료되면 호출되는 함수가 전달받은 callback을 실행하고, 호출하는 함수는 작업 완료 여부를
신경쓰지 않으면 Asynchronous
- non-blocking : system call이 반환될 때 실행된 결과와 함께 반환되지 않는 경우, 호출된 함수가 바로 리턴해서
호출한 함수에게 제어권을 넘겨주고, 호출한 함수가 다른 일을 할 수 있는 기회를 줄 수 있으면 non-blocking

#### non-blocking VS synchronous
- 위 설명에 따르면 non-blocking VS synchronous는 호출되는 함수는 바로 리턴하고 호출하는 함수는 작업 완료 여부를
신경쓴다. 즉, 함수를 호출하고 바로 반환 받아서 다른 작업을 할 수 있지만(non-blocking) 호출 후 수행되는 작업이
완료되는 것은 아니며, 호출하는 함수가 호출된 함수 쪽에 작업 완료 여부를 계속 묻는다

#### blocking VS Asynchronous
- 위 설명에 딸면 호출되는 함수가 바로 리턴하지 않고, 호출하는 함수는 작업 완료 여부를 신경쓰지 않는다








