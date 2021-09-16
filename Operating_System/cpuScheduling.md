### CPU 스케줄링

> CPU가 하나의 프로세스 작업이 끝나면 다음 프로세스 작업을 수행해야 한다. 이때 어떤 프로세스를 다음에 처리할
> 지 선택하는 알고리즘을 CPU Scheduling 알고리즘이라고 한다. 따라서 상황에 맞게 CPU를 어떤 프로세스에 배정
> 하여 효율적으로 처리하는지가 관건이라고 할 수 있다

#### Preemptive VS Non-Preemptive
1. Preemptive(선점)
- 프로세스가 CPU를 점유하고 있는 동안 I/O나 인터럽트가 발생하지 않았음에도 다른 프로세스가 해당 CPU를 강제로 점유할 수 있다
- 즉, 프로세스가 정상적으로 수행중인 동안 다른 프로세스가 CPU를 강제로 점유하여 실행할 수 있다

2. Non-Preemptive(비선점)
- 한 프로세스가 CPU를 점유했다면 I/O나 인터럽트 발생 또는 프로세스가 종료될 때까지 다른 프로세스가 CPU를 점유하지 못하는 것이다

#### 선점형 스케줄링
1. SRT(Shortest Remaining Time) 스케줄링
- 짧은 시간 순서대로 프로세스를 수행한다
- 현재 CPU에서 실행 중인 프로세스의 남은 CPU 버스트 시간보다 더 짧은 CPU 버스트 시간을 가지는 프로세스가 도착하면 CPU가 선점된다
- SJP에 선점 정책을 도입한 것이라고 생각하면 되는데 예제를 통해 이해해보자
![image](https://user-images.githubusercontent.com/67304980/133598430-c5fcd685-2425-4c42-91eb-941e4541d90b.png)

위의 결과가 SJF 스케줄링을 적용했을 때다. P1 프로세스 실행중, P2가 준비완료 큐에 더 작은 실행시간을 가지고 진입했음에도 불구하고 묵묵히 자기 일을 수행한다. SJF의 문제점은 무기한적으로 연기 현상 발생이 가능한데 이는 시간이 지날수록
우선순위를 높여주는 '에이징 기법'을 통해 해결 가능하다

![image](https://user-images.githubusercontent.com/67304980/133598719-b981b8db-d467-4afd-8207-330003148b98.png)

다음은 SRT(SRTF) 스케줄링을 같은 프로세스에 적용했을 때의 그림이다. P1 실행중 P2가 들어오자 실제로 P1을 멈추고 P2를
실행하는 것이 특징이다. 마냥 좋을 것 같지만 심각한 문제들이 몇 가지 있다. 대표적으로 잦은 프로세스 잔여 실행시간 계산, 잦은 선점으로 인한 문맥 교환 오버헤드 증가 등이 있다. 따라서 현실적으로 구현 및 사용이 어렵다



2. Round Robin 스케줄링
- 시분할 시스템의 성질을 활용한 방법
- 일정 시간을 정하여 하나의 프로세스가 이 시간동안 수행하고 다시 대기 상태로 돌아간다
- 그리고 다음 프로세스 역시 같은 시간동안 수행한 후, 대기한다. 이러한 작업을 모든 프로세스가 돌아가면서 진행하며, 마지막 프로세스가 끝나면 다시 처음 프로세스로 돌아와서 작업을 반복한다
- 일정 시간을 Time Quantum(Time Slice)라고 부른다. 일반적으로 10 - 100msec 사이의 범위를 갖는다
- 한 프로세스가 종료되기 전에 time quantum이 끝나면 다른 프로세스에게 CPU를 넘겨주기 때문에 선점형 스케줄링의 대표적인 예시다
- 공평하게 들어온대로 처리하면 waiting time이 너무 커지니까 컴퓨터는 time sharing 방식을 이용하는 것이다. 이것을 줄여서 RR 알고리즘이라고 한다
- 현재 지금 우리의 운영체제는 Priority + RR 형태로 스케줄링 되고 있다

문제를 풀며 이해해보자

그전에 Time Quantum에 대해 조금 더 알아보자. Time Quantum이 작을수록 많은 context switching이 발생한다. Time Quantum이 극단적으로 무한대에 근접한다면 그 프로세스의 크기만큼 CPU가 처리하다가 해당 프로세스가 끝나야 다른 프로세스가 처리되므로 결국 FCFS와 같은 알고리즘이 된다. 따라서 적당한 Time Quantum의 크기를 정하는 것이 중요하다. CPU burst의 80% 정도를 포함할 수 있도록 Time Quantum을 설정하는 것을 권장한다.

![image](https://user-images.githubusercontent.com/67304980/133600786-8ae7d46e-d675-4a2f-b59e-fc4e7751b287.png)

Q. Time Quantum이 1이라고 할 때 waiting time, turnaround time, response time, completion itme을 구해라(동시에 도착했지만 순서는 P1,P2,P3로 가정)

처음 대기 큐에는 p1,p2,p3가 들어있다. 가장 먼저 들어온 p1이 1초 동안 수행된다. 따라서 p1은 2초가 남게 된다. 그 다음 p1을 그 다음 대기큐로 넣고 다음 대기 중인 p2가 1초 동안 수행된다. 이런 방식으로 수행하게 되면 아래 그림과 같다

![image](https://user-images.githubusercontent.com/67304980/133601690-fb7e382a-0425-4930-b333-97a56879c05b.png)

이 정보를 기반으로 문제에서 요구하는 시간들을 계산해보자

![image](https://user-images.githubusercontent.com/67304980/133601812-0d4d294f-97c0-4cbc-b238-d4b81db7f5cc.png)

먼저 completion time은 끝날 때까지의 시간을 말한다. 뒤에서부터 프로세스들을 확인하면 되므로 어렵지 않다

그 다음 turnaround time은 요청한 다음에 끝날 때까지의 시간이므로 completion time - arrival time하면 된다. 여기서는 도착 시간이 0으로 다 같으므로 completion time과 turnaround time이 같다

즉 평균 turnaround time은 (7+9+10)/3 = 26/3 = 8.67이 된다



![image](https://user-images.githubusercontent.com/67304980/133601897-0e3f4f8f-0d28-402a-a5cc-71fe1c49eca9.png)

waiting time은 다른 프로세스가 수행되고 있어서 내가 기다려야 하는 시간이다. 즉 총 걸리는 작업 수행 시간에서 내 프로세스가 CPU에서 수행중인 시간을 빼면 남들이 CPU에서 수행되는 시간, 즉 내가 기다리는 시간이므로 turnaround time - burst time 하면 된다. 즉 평균 waiting time은 (4+6+6)/3 = 16/3 = 5.33이 된다

response time은 프로세스가 처음으로 응답하기까지 시간이다. 즉 처음 프로세스가 응답하는 시간에서 프로세스가 도착한 시간을 빼 주면 프로세스가 시작부터 응답하기까지 걸리는 시간인 response time이 된다. 근데 여기서는 도착한 시간이 모두 0이므로 response time은 처음 프로세스가 응답하는 시간만 생각하면 된다. 즉 0,1,2다

![image](https://user-images.githubusercontent.com/67304980/133602797-b2922370-bd62-43ac-aea7-41e8185617e2.png)
![image](https://user-images.githubusercontent.com/67304980/133602812-21c129bf-8643-44fc-b3d4-fce82f49b547.png)

Q. Time quantum이 3일 때 평균 turnaround time, 평균 waiting time, 평균 response time을 구하라

가장 먼저 도착한 프로세스가 먼저 실행된다. 따라서 p4가 1초에 들어와서 3초동안 수행이 된다. p4의 주어진 시간이 다되기 전 즉 4초 안에 p2,p3가 들어온다. 즉 p1이 수행 중일때 ready queue에 p3,p2,p4(수행 후 p4)가 된다. 

그 다음에는 p3가 3초간 수행된다. 그 도중 6초에 p1이 들어오고 ready queue에는 p2, p4, p1이 있게 된다.
이 방식이 진행되는 과정을 표로 나타내면

![image](https://user-images.githubusercontent.com/67304980/133603417-6fceda19-eba3-48b9-959e-1d76e78de672.png)

![image](https://user-images.githubusercontent.com/67304980/133604272-341916d1-60f5-4e4f-b373-4c8f62c03aed.png)



3. Multi-level Queue 스케줄링
- 프로세스를 그룹으로 나누어, 각 그룹에 따라 Ready Queue(준비 큐)를 여러 개 두며, 각 큐마다 다른 규칙을 지정할 수도 있다(ex. 우선순위, CPU 시간 등)
- 즉, 준비 큐를 여러 개로 분할해 관리하는 스케줄링 방법이다
- 프로세스들이 CPU를 기다리기 위해 한 줄로 서는 게 아니라 여러 줄로 선다
![image](https://user-images.githubusercontent.com/67304980/130614952-00b5b395-af65-456b-8277-78402de85fc8.png)

- 운영체제는 프로세스들을 분류할 때 보통 사용자와 상호작용하는 앞단의 프로세스들은 중요하다고 판단하고 백그라운드에 돌아가는 프로세스(일괄처리형 batch processes)들은 상대적으로 덜 중요하다고 판단하여 분류하는 것이 일반적이다
- 이렇게 분류를 해서 서로 다른 스케줄링 기법을 적용하는 것이 다단계 큐 알고리즘이다
- 뒤에서 조용히 돌아가는 프로그램보다 당장 내가 하고 있는 작업에 반응이 느릴 때 답답하다. 이러한 이유로 앞단의 프로세스들은 백그라운드 프로세스보다 더 높은 우선순위를 갖게 되고, 빠른 응답 시간을 주기 위해서 당연히 다른 알고리즘이 적용되어야 더 효율적으로 성능을 끌어낼 수 


4. Multi-level feedback Queue 스케줄링
- 기본 개념은 Multi-level Queue와 동일하나, 프로세스가 하나의 큐에서 다른 큐로 이동 가능하다는 점이 다르다
![image](https://user-images.githubusercontent.com/67304980/130615043-5ae717a7-7a4c-4fda-bbc1-e057d05aaf4c.png)
- 위 그림에서 모든 프로세스는 가장 위의 큐에서 CPU의 점유를 대기한다. 이 상태로 진행하다가 이 큐에서 기다리는 시간이 너무 오래 걸린다면 아래의 큐로 프로세스를 옮긴다. 이와 같은 방식으로 대기 시간을 조정할 수 있다
- 만약, 우선순위 순으로 큐를 사용하는 상황에서 우선순위가 낮은 아래의 큐에 있는 프로세스에서 starvation 상태가 발생하면 이를 우선순위가 높은 위의 큐로 옮길 수도 있다
- 대부분의 상용 운영체제는 여러 개의 큐를 사용하고 각 큐마다 다른 스케줄링 방식을 사용한다. 프로세스의 성격에 맞는 스케줄링 방식을 사용하여 최대한 효율을 높일 수 있는 방법을 선택한다

#### 비선점형 스케줄링
1. FCFS(First Come First Served)
- 준비 큐에 먼저 도착한 프로세스가 먼저 CPU를 점유하는 방식이다
- CPU를 할당받으면 CPU 버스트가 완료될 때까지 CPU를 반환하지 않으며, 할당되었던 CPU가 반환될 때만 스케줄링이 이루어진다
- Simple & Fair
- 단점 : Convoy Effect 발생 : 소요 시간이 긴 프로세스가 짧은 프로세스보다 먼저 도착해서 뒤에 프로세스들이 오래 기다려야 하는 현상
![image](https://user-images.githubusercontent.com/67304980/130628613-545c67b4-9fde-41a4-8405-114025ad1dac.png)
  > 평균 대기 시간을 구해보자. p1은 0msec에 시작된다. p2는 24msec을 대기하고 시작된다. p3는 27msec을 대기하고 시작된다. 0,24,27의 평균은 17이다. 만약 p2,p3,p1순서로 시작되었다면 대기시간은 0,3,6이며 평균은 3이다. 따라서 대기시간을 척도로 본다면 FCFS방식은 좋진 않다. 만약 첫번째 프로세스가 실행시간이 길고 뒤이은 프로세스가 실행순서가 짧다면 뒤의 프로세스들은 첫번째 프로세스를 따라다니는 형세가 되는데 이를 호위효과라고 한다. FCFS는 Nonpreemtive scheduling이다. 

2. SJF(Shortest-Job-First)
- 다른 프로세스가 먼저 도착했더라도 CPU 버스트가 짧은 프로세스에게 CPU를 먼저 할당하는 방식이다
- 선점, 비선점 모두 가능

- Shortest-Job_First(1)
  - Provably optimal -> 대기시간 줄이는 측면에서는 가장 좋은 방법이다
  - Not realistic; prediction may be needed -> 비현실적(실제로는 프로세스가 cpu시간을 얼마나 사용할지는 해봐야 안다). 프로세스 시간은 예측된 후에 측정이 가능하다.
![image](https://user-images.githubusercontent.com/67304980/130629233-330bc161-460d-4338-9fd0-74c2070e27fc.png)
   > 실행시간이 짧은 순서대로 정렬하면 p4 - p1 - p3 - p2 순이다. 그러면 각 프로세스의 대기시간의 합은 0+3+9+16 = 28이다. 이걸 4로 나누면 평균적으로 7msec을 기다린 것이다. 만약 FCFS 방식으로 했다면 평균 10.25msec이 나온다.

- Shortest-Job_First(2)
  - Preemptive or Nonpreemtive -> Shortest-Remaining- Time - First(최소잔여시간 우선)
![image](https://user-images.githubusercontent.com/67304980/130629389-533f0b6e-99da-4068-bc5a-4f8ccac39174.png)
  > Nonpreemptive로 할 경우 시간0에서 p1밖에 없으므로 p1을 먼저 실행한다. 그러면 시간8이 되는데 이때 p2,p3,p4다 들어와있다. 이때 가장 실행시간 짧은 순으로 p2 - p4 - p3순으로 실행한다. 평균 대기시간은 {0+(8-1)+(17-2)+(12-3)}/4는 (0+7+15+9)/4 = 7.75가 나온다. Preemptive로 할 경우 시간0에서 p1밖에 없으므로 p1을 먼저 실행한다. 시간1에서 p2가 들어오는데 실행시간이 p2가 짧으므로 p2로 스위칭된다. 시간2에서 p3가 들어오고 실행시간을 비교하면 p2가 제일 짧으므로 그대로 진행된다. 시간3에서 p4가 들어오고 실행시간을 비교하여 p2가 그대로 진행된다. p2가 종료되면 p1,p3,p4인데 p4가 먼저 돌고 그 다음 p1, 마지막으로 p3가 돈다. 평균 대기시간은 {9+(1-1)+(17-2)+(5-3)}/4 = 26/4 = 6.5가 나온다.

3. Priority
- 우선순위가 높은 프로세스가 먼저 선택되는 스케줄링 알고리즘이다
- 우선순위는 정수값으로 나타내며, 작은 값이 우선순위가 높다(Unix, Linux 기준)
- 선점, 비선점 모두 가능하다
![image](https://user-images.githubusercontent.com/67304980/130629690-b6387082-4a46-449e-94bd-4091ef035ce5.png)
  > 우선순위가 높은 순으로 진행한다. p2-p5-p1-p3-p4 순으로 진행한다. 평균 대기시간을 계산해보면(0+1+6+16+18)/5 = 8.2가 나온다.
- Priority(우선순위)
  - 내부요소 : 시간제한, 메모리 요구량, i/o와 cpu시간 등
  - 외부요소 : 돈 많이 낸 쪽(보통 유료 컴퓨터의 경우), 정치적 요소(학생의 과제 업무보다 입시 업무의 우선순위 높임) 
- Preemptive or Nonpreemptive
- Problem
  - 기아 : 만약 계속 기다리는 프로세스가 있는데 계속 큐에 프로세스가 들어온다. 만약 들어오는 프로세스들이 다 우선순위가 높다면 영영 해당 프로세스는 처리될 수 없다.
  - 해결책 : aging이다. 레디 큐에서 실행 안되고 오래 머문다면 해당 프로세스의 우선순위를 점진적으로 높여주는 것.
 
























