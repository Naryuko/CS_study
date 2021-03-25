
# Problem 1
프로세스 P1, P2, P3 의 CPU burst time 은 각각 4, 2, 8 msec 이며, 세 프로세스는 각기 다른 시간에 ready queue 에 도착했다.
즉 P1 은 0 msec에, P2 는 1 msec에, P3 는 5 msec 에 도착했다. 
CPU scheduling 에 대한 아래 물음에 답하라
(※ 각 경우마다 Gantt chart를 그리고 수식도 적어라. 단위도 기록해야 한다).

### a) FCFS 스케쥴링을 사용하면 평균 대기시간 average waiting time 은 얼마인가?

| Process | arrive time | start time | average waiting time |
|---|---|---|---|
| P1 | 0 | 0 | 0 |
| P2 | 1 | 4 | 3 |
| P3 | 5 | 5 | 0 |

따라서 평균 대기시간은 1 ms


### b) 선점형 preemptive SJF 스케쥴링을 사용할 때 평균반환시간 average turnaround time은 얼마인가?
SJF = Shortest Job First, 선점형 - 일을 하다 cpu 뺏기기 가능

| Process | CPU burst time | arrive time | start time | average waiting time | 비고 |
|---|---|---|---|---|---|
| P1 | 4 | 0 | 0 | 0 | - |
| P2 | 2 | 1 | 1 | 0 | P1 종료 |
| P1 | 3(남음) | 1 | 3 | 2 | 재시작 |
| P3 | 8 | 5 | 6 | 1 | P1보다 일이 길어 순위 밀림 |

따라서 P1 2, P2 0, P3 1 ms 대기하므로 평균 대기시간은 1 ms

### c) Time quantum 이 무한대 (∞) 인 Round-Robin scheduling을 적용하면 평균반환시간은 얼마인가?

time quantum이 무한이면 프로세스 종료까지 cpu를 뺏기지 않으므로 FCFS와 같다. 1 ms

### d) 비선점형 우선순위 non-preemptive priority 스케쥴링을 사용하면 처리율 throughput 은 얼마인가? 단, 프로세스 P1, P2, P3 의 우선순위는 각각 3, 2, 1 이며, 숫자가 작을수록 우선순위가 높다.

non-preemptive이므로 작업중 cpu를 뺏기지 않는다. 

| Process | CPU burst time | arrive time | start time | average waiting time | 비고 |
|---|---|---|---|---|---|
| P1 | 4 | 0 | 0 | 0 | 우선순위가 가장 낮지만 도착했을 때 다른 프로세스가 없으므로 가장 먼저 실행 |
| P2 | 2 | 1 | 4 | 3 | 4초에 대기 큐에 존재하는건 P2뿐이므로 P2가 실행 |
| P3 | 8 | 5 | 6 | 1 | - |

따라서 P1 0, P2  3, P3 1 ms 대기하므로 평균 대기시간은 1.333 ms



# Problem 2
### a) 프로세스 동기화 process synchronization 란 무엇을 의미하는가? 
서로 다른 프로세스들이 같은 데이터에 접근해 서로 동시에 연산을 수행하는 경우 연산 결과가 정확히 반영되지 않고 마지막으로 연산을 수행한 프로세스에 의해서만 결정될 수 있다. 이러한 race condition을 방지하는 것.

### b) 세마포의 내부 구조를 그림으로 나타내고 간략히 설명하라.

P 연산: 자원을 할당함. 프로세스가 자원 요구시 value -= 1 후 value < 0이라면 block 후 대기 queue에 넣는다, value>=0이라면 프로세스 실행. value가 음수라면 value 절댓값이 block되어있는 프로세스 수이다.

S 연산: 자원을 반납함. Value += 1 후 value >= 0 이라면 block queue에서 프로세스를 하나 꺼내 wake한다.

###  c) 어떤 세마포에 다섯 개의 프로세스가 블록 block되어있다고 가정하자. 이때 세마포 내부의 정수 값은 얼마인가?

-5



# Problem 3
### a) 프로세서 processor 의 이중모드 dual mode 란 무엇을 의미하는가? 

Mode bit를 말하는 것으로 생각한다. 사용자 프로그램에 의해 OS나 자원에 피해가 가지 않도록 보호하는 역할. Mode bit = 1이라면 사용자 프로세스에게 cpu 할당, os가 cpu 제어권을 가질 때에는 mode bit = 0

### b) 이중모드를 사용한 입출력장치 i/o devices 보호 방법에 대해 설명하여라.

사용자 프로세스가 i/o가 필요함 -> 시스템 콜(소프트웨어 인터럽트, 트랩)를 통해 os에게 i/o작업 필요를 알림 -> mode bit 0으로 바꾸고 os가 cpu 획득 -> os가 시스템 콜 처리 루틴을 통해 적절한 코드 실행 -> i/o 실행(이 때 문맥 교환을 해 다른 프로세스에게 cpu를 줄 수 있다. 이러면 당연히 mode bit = 1) -> i/o 완료시 i/o장치가 cpu에 인터럽트 -> i/o를 요청한 프로세스는 시스템 콜 다음 코드부터 계속 실행됨


# Problem 4
### a) 프로세스 상태 변화를 보여주는 상태천이도 process state transition diagram 를 그려라. 
Ready -> running -> wait/block/sleep (+ suspended)

### b) ready 와 waiting 상태는 서로 어떻게 다른지 구분하여 설명하라. 
ready는 cpu를 주면 바로 작업이 가능한 상태이며 cpu를 기다리고 있는 상태이다.

waiting은 cpu를 당장 주어도 작업을 수행하지 못하는 상태로, i/o작업 요청중 등의 상태이다.

### c) 프로세스 상태가 running 상태에서 ready 상태로 변하는 때는 어떤 경우인가?
타이머 인터럽트에 의해. 해당 프로세스에게 주어진 시간이 만료되어 타이머가 cpu에 인터럽트를 걸면 cpu는 해당 프로세스에게 자원 할당을 중지하고 ready 큐에 있는 다음 프로세스에게 사용권을 넘긴다.

# Problem 5
### a) 비특권 명령 non-privileged instruction 의 예를 세 가지 소개하라. 
### b) 사용자 프로세스가 시스템 호출 system call 을 하면 CPU 실행 모드는 사용자 모드에서 시스템 모드로 변환된다. 왜 그렇게 변환되어야만 하는지 이유를 설명하라. 

### c) 시스템 호출 시 메모리의 사용자 user 영역에서 커널 kernel 영역으로 자료 전달은 어떻게 일어나는가?
커널은 사용자 프로세스의 메모리 페이지에 마음대로 접근할 수 있다.


6. 다음 용어의 의미를 간략히 설명하라.
### a) convoy effect 
프로세스 스케쥴링 방법 중 FCFS을 사용할 때 cpu burst time이 긴 프로세스가 먼저 와서 다른 많은 프로세스들의 대기 시간이 길어지는 현상

### b) time-sharing system 
OS에서 여러 프로세스 작업을 수행해야 할 때 각 프로세스별 일정 시간동안만 cpu를 할당하고 해당 시간이 지나면 cpu를 뺏어 다음 프로세스에게 준다

### c) process control block
os의 data 메모리에 들어 있는 프로세스들을 관리하기 위한 자료구조. Process id, 진행 상태, program counter 등이 포함되어있다.


### d) dispatcher 
현재 프로세스의 pcb를 저장하고 다음 프로세스의 pcb를 메모리에 올리는 문맥 교환 담당 커널 코드

### e) critical section 
코드 중 공유 데이터에 접근하는 부분

### f) software interrupt
사용자 프로세스가 커널 코드의 함수를 사용해야 할 때 (i/o 등) os에게 cpu를 넘겨 작업을 위탁하기 위해 cpu에게 거는 인터럽트.
