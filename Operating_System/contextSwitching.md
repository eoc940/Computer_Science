### 프로세스의 개념 및 상태

#### 프로세스
- 실행중인 프로그램
- 디스크에 저장된 수동적 파일(실행 파일)이 메모리에 적재될 때, 비로소 프로세스가 된다

![image](https://user-images.githubusercontent.com/67304980/138383211-5f264af2-e6f8-4e1d-96a4-a7917ea024a8.png)

#### 메모리 상에서의 프로세스

![image](https://user-images.githubusercontent.com/67304980/138383269-9d41b779-a45c-41ed-83a5-f15e21e8d975.png)

#### 프로세스 상태 별 설명

![image](https://user-images.githubusercontent.com/67304980/138383398-b20dd774-8dac-400a-ace2-58c48d2d7c81.png)

![image](https://user-images.githubusercontent.com/67304980/138383416-d608e404-ff18-4610-81cc-0b9a97532fa0.png)


### Process Management
>CPU가 프로세스가 여러개일 때, CPU 스케줄링을 통해 관리하는 것을 말함
>이때 CPU는 각 프로세스들이 누군지 알아야 관리가 가능함
>프로세스들의 특징을 갖고 있는 것이 바로 Process Metadata이다

![image](https://user-images.githubusercontent.com/67304980/138383007-1710169f-e42c-4957-a454-85e3427dcdd6.png)


- Process Metadata
  - Process ID
  - Process State
  - Process Priority
  - CPU Registers
  - Owner
  - CPU Usage
  - Memory Usage
>이 메타데이터는 프로세스가 생성되면 PCB(Process Control Block)이라는 곳에 저장된다

### PCB(Process Control Block)
- 프로세스 메타데이터들을 저장해 놓는 곳, 한 PCB 안에는 한 프로세스의 정보가 담긴다
![pcb](https://user-images.githubusercontent.com/67304980/129594566-3696e69e-1221-4828-a21b-79186041a961.JPG)
#### 정리해보면
```
프로그램 실행 -> 프로세스 생성 -> 프로세스 주소 공간에 (코드, 데이터, 스택) 생성 -> 디 프로세스의 메타데이터들이 PCB에 저장
```
### PCB가 필요한 이유
> CPU에서는 프로세스 상태에 따라 교체작업이 이루어진다(Interrupt가 발생해서 할당받은 프로세스가 waiting 상태가 되고 다른 프로세스를 running으로 바꿔 올릴 때)
> 이때 앞으로 다시 수행할 대기 중인 프로세스에 관한 저장 값을 PCB에 저장해두는 것이다

### PCB관리 방법
> Linked List 방식으로 관리한다
> PCB List Head에 PCB들이 생성될 때마다 붙게 된다. 주소값으로 연결이 이루어져 있는 연결리스트이기 때문에 삽입 삭제가 용이하다
> 즉, 프로세스가 생성되면 해당 PCB가 생성되고 프로세스 완료시 제거된다

이렇게 수행 중인 프로세스를 변경할 때, CPU의 레지스터 정보가 변경되는 것을 Context Switching이라 한다



### Context Switching
- CPU는 한번에 하나의 프로세스만 처리할 수 있다
- 여러 프로세스를 처리해야 하는 상황에서 현재 진행중인 Task(프로세스, 스레드)의 상태를 PCB(Process Control Block)에 저장하고
다음에 진행할 Task의 상태값을 읽어 적용(레지스터에 적재)하는 과정을 말한다(다른 프로세스에게 CPU를 할당해 작업을 수행하는 과정이다)
- 과정
  - Task의 대부분 정보는 Register에 저장되고 PCB로 관리된다
  - 현재 실행하고 있는 Task의 PCB 정보를 저장한다
  - 다음 실행할 Task의 PCB 정보를 읽어 Register에 적재하고 CPU가 이전에 진행했던 과정을 연속적으로 수행할 수 있다
- Context Switching은 많은 비용이 소모된다(
  - Cache 초기화
  - Memory mapping 초기화
  - 커널은 항상 실행되어야 한다
- Context Switching의 비용은 프로세스가 스레드보다 더 많이 든다
- 이유 : 스레드는 Stack 영역을 제외한 모든 메모리를 공유하기 때문에 Context Switching 발생시 Stack 영역만
변경을 진행하면 되기 때문이다.

![image](https://user-images.githubusercontent.com/67304980/138390240-e2cd85e4-ddfa-4647-a781-a1277aebc624.png)

- 위 그림에서 context switching이 시작되는 부분은 reload state from PCB1이다
- 예전에 배웠던 인터럽트는 운영체제가 CPU를 현재 작업에서 빼앗아 커널 루틴을 실행할 수 있게 한다
- 종료 후 본래 작업중이던 프로세스를 재개한다

- 프로세스를 재개하려면
  - 어떤 값을 저장해야 할까? : **실행 중이던 프로세스의 문맥(Context)**
  - 어느 곳에 저장해야 할까? : **PCB**
  - 이 작업을 어떻게 칭해야 할까? : **컨텍스트 스위치**

- 문맥(Context) : CPU 레지스터의 값, 프로세스 상태, 메모리 관리 정보 등을 포함
- 컨텍스트 스위치가 진행될 동안의 시스템
  - 그 동안 시스템은 아무런 유용한 일을 하지 못함
    - 컨텍스트 스위치 소요 시간은 순수한 오버헤드

