# CPU Scheduling

> Ready 상태의 프로세스들 중 누구에게 CPU를 줄 것이냐!!!
>
> 최신 운영체제에서는 실질적으로 프로세스가 아닌 커널 수준 쓰레드를 스케줄한다. 하지만, 보다 편한 설명과 이해를 위해 스케줄링의 대상을 프로세스로 가정하고 설명하겠다. 실제로, 프로세스 스케줄링과 스레드 스케줄링 이라는 두 단어는 상호 교환적으로 사용된다. 
>
> Ready Queue는 반드시 선입선출(FIFO) 방식의 큐가 아니어도 되는 것에 유의해야 한다. (이름은 큐이지만 보통 링크드 리스트로 구성한다.) Ready Queue 속에 있는 모든 프로세스는 CPU에서 실행될 기회를 기다리며 대기한다. 큐에 있는 레코드들은 일반적으로 프로세스들의 PCB이다.

---

### 🔴CPU Scheduling

- CPU Scheduling is the basis of multiprogrammed operating systems.
- By switching the CPU among processes, the OS can make the computer more productive.
- In a single-processor system, only one process can run at a time.
- Any others must wait until the CPU is free and can be rescheduled.
  - The objective of multiprogramming is to have some process running at all times, to maximize CPU utilization.
  - A process is executed until it must wait, typically for the completion of some I/O request.
  - 하지만, 이런 식으로 I/O가 끝날 때까지 기다린다면 CPU의 활용시간을 낭비하는 것이다.
- 때문에, 멀티프로그래밍에서 우리는 이 기다리는 시간을 효율적으로 사용하기 위한 노력을 해야 한다.
  - Several processes are kept in memory at one time.
  - When one process has to wait, the operating systems takes the CPU away from that process and gives the CPU to another process and this pattern continues.

**A 프로세스에서 B 프로세스로 CPU를 옮기는 일을 디스패쳐가 수행한다!**

---

### 🔴Dispatcher

> 유저 프로그램과 유저 프로그램이 수행되는 중간에 개입해서 CPU를 A 프로세스에서 B 프로세스로 넘겨주는 역할을 한다. 이때 가장 중요한 것이 디스패쳐가 수행되기 위해서는 CPU를 차지해야 한다는 것! (지금까지 잘못 알고 있었다. 나는 프로세스가 바뀔 때 CPU가 논다고 생각했다. 하지만, CPU가 놀든 본래의 용도(명령어 처리)가 아닌 다른 일을 하든 이 시간은 오버헤드이다.) 다시 말해 CPU에 대한 점유권, CPU에 대한 control이 유저 프로그램에서 디스패쳐로, 디스패쳐에서 다시 다른 유저 프로그램으로 넘어가야 한다. 디스패쳐가 수행된다는 것은 커널로 execution control이 진입하는 것이다. 그리고 다시 다른 유저 프로그램으로 execution control이 넘어가는 것을 의미한다. 유저 모드에서 커널 모드로 바꾸려면 Interrupt가 필요하고 이 때문에 Interrupt 하는 순간 OS(정확히 말하면 디스패쳐)가 CPU를 사용하는 것이다.

### System Call

> 운영체제의 커널이 제공하는 서비스를 응용 프로그램에 요청하기 위한 Interface. 즉, OS는 커널 모드에서 수행되는 라이브러리 함수들의 집합체이다. 커널 모드에서 수행되는 라이브러리 함수는 System Call 함수 외에 한가지가 더 있다. 바로 Interrupt(Interrupt Service Routine) . 왜냐? Interrupt가 일어나면 mode change가 일어나고 하드웨어적인 정의에 의해 해당 Interrupt Service Routine이 불려진다. 결국, 커널 함수하고 하는 것은 System Call을 구현하는 함수들과 Interrupt Service Routine들로 구성되어있다.

---

### CPU and I/O Burst Cycles

- Process execution consists of a cycle of CPU execution and I/O wait.
- CPU execution 상태를 CPU Burst, I/O 처리가 끝나기를 기다리는 상태를 I/O Burst 라고 한다.

Process execution begins with a

​												**CPU** **Burst**   That is followed by an

​																										**I/O** **Burst** which is followed by another

​																																									**CPU** **Burst**

​																																															and so on.....

결국 마지막 CPU Burst는 또 다른 I/O Burst가 뒤따르는 대신, 실행을 종료하기 위한 시스템 요청과 함께 끝난다.

---

### Preemptive and Non-Preemptive Scheduling

> CPU Scheduler는 CPU를 받을 프로세스를 정하고, 디스패쳐가 그 프로세스에 직접적으로 CPU를 전달한다.

<br/>

CPU Scheduling은 아래 4개의 상황에서 발생한다.

1. When a process switches from the `running state` to the `waiting state` (I/O 요청이나 프로세스가 종료되기를 기다리기 위해 `wait()`을 호출할 때)
2. When a process switches from the `running state` to the `ready state` (인터럽트가 걸릴 때)
3. When a process switches from the `waiting state` to the `ready state` (I/O가 끝났을 때)
4. When a process terminates

1, 4번의 경우 이후에 Ready Queue에 있는 프로세스들 중 하나를 선택하면 된다. 하지만, 2, 3번의 경우에는 조금 다르다. 

2번의 경우 원래의 프로세스가 `ready state`로 가게 된다. 그렇다면 원래 처리하던 프로세스를 다시 선택할 지 아니면 새로운 프로세스를 선택할 지 골라야 한다.

3번의 경우 I/O 처리를 위해 `waiting state`로 갔던 프로세스가 `ready state`로 온다면 이 프로세스를 먼저 처리할 것인지 아니면 새로운 프로세스를 선택할 지 골라야 한다.

1, 4번의 상황에서 발생하는 Scheduling을 `Non-Preemptive` 혹은 `Cooperative` 하다고 부른다.

2, 3번의 상황에서 발생하는 Scheduling을 `Preemptive` 하다고 부른다.

`nonpreemptive`가 더 좋고, `preemptive`가 나쁘다 이런 건 존재하지 않는다. 상황에 따라 유리한 선택지가 달라질 수 있다.

---

### Scheduling Criteria

- CPU Utilization (개념적으로 0-100%이지만, 실제 시스템에서는 40(for a lightly loaded system)-90(for a heavily used system)%이다.)
- Throughput
- Turnaround Time
- Waiting Time
- Response Time

---

