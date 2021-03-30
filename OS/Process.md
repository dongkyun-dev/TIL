# Process

> 지난 주는 의도치 않게 스택 영역과 힙 영역에 집중해서 글을 썼다. 이번 주부터 제대로 프로세스 전체에 대한 글을 작성해보려 한다.

---

### Process와 Thread의 정의!

Process 

> A process can be thought of as a program in execution.(실행 중인 프로그램을 프로세스라 한다.)

Thread

> A thread is the unit of execution within a process. A process can have anywhere from just one thread to many threads. (스레드는 프로세스 속에 있으며, 프로세스는 1개 이상의 스레드를 가진다.)

---

### PCB

> 각각의 process는 OS 안에서 Process Control Block 이라고 불린다.(Task Control Block으로 불리기도 함.)

![PCB](../assets/img/PCB.jpg)

- 1개의 프로세스는 1개의 PCB를 가진다.
- 이 PCB에는 PID와 Process Scheduling과 같은 정보들이 존재한다.
- 우리가 말하는 OS는 Multiprocessing이 되는 Time-Sharing OS. 때문에 당연히 한 OS 안에 프로세스가 여러개 있을 것이고, PCB 또한 여러개 있을 것이다. 이 wild하게 존재하는 여러 PCB들을 array로 만들면 **Process Table**  
- 하지만, 단순히 array 방식으로 만들면 OS가 제공할 수 있는 프로세스의 개수가 제한된다는 단점이 있다. 
- 이를 극복하기 위해서 링크드 리스트 방식을 사용하는 것이다.  (아마도 prev 포인터와 next 포인터 모두를 가지는 doubley-linked-list 방식으로 구현되지 않을까 싶다.)

---

### Process State

![Process-State-Diagram](../assets/img/Process-State-Diagram.png)

new - The process is being created.

running - Instructions are being executed.

waiting - The process is waiting for some event to occur.

Ready - The process is waiting to be assigned to a processor. (new와 running 사이)

Terminated - The process has finished execution.

<br/>

- Ready 상태에 있는 프로세스들을 묶어서 큐로 구성하고 그 큐를 **Ready Queue**라 한다. 

  > Ready Queue: Ready 상태의 프로세스들을 관리하기 위한 큐 형태의 자료구조. 일반적으로 PCB들을 링크드 리스트로 연결하여 구현
  >
  > 

- Ready 상태에서 CPU를 받게 되면 Running 상태가 된다. 

<br/>

Q. Waiting 상태의 프로세스가 여러개 있을 수 있을까??

A. 그렇다. 하지만 waiting의 이유는 각 프로세스마다 다를 수 있다. OS는 waiting하는 이유에 따라 별도의 큐를 사용한다. 결국, OS는 프로세스가 생성되면 하나의 PCB를 할당하고 그 프로세스를 일련의 조건에 따라 다른 상태로 전이 시킨다. (상태 전이를 위해 ReadyQueue, WaitingQueue 같은 것들이 필요)

---

### 🔴Process Scheduling

> 프로세스의 상태를 나누는 이유는 스케쥴링 때문이다.

- The objective of multiprogramming is to have some process running at all times, to maximize CPU utilization.(멀티프로그래밍의 목적은 CPU의 효율을 최대로 높이는 것이다. 이를 위해서는 특정한 프로세스가 항상 실행 중이어야 한다.)
- The objective of time sharing is to switch the CPU among processes so frequently that users can interact with each program while it is running.(실질적으로 이 여러 프로세스들이 **actual same time** 에 동시에 실행되는 것은 아니다! 워낙 자주, 빠르게 CPU가 옮겨다니기 때문에 사람의 감각으로는 그 차이를 파악하지 못하는 것일 뿐이다.)
- To meet these objectives, the process scheduler selects an available process(possibly from a set of several available processors) for program execution on the CPU.
  - For a single-processor system, there will never be more than one running system.
  - If there are more processes, the rest will have to wait until the CPU is free and can be rescheduled.

<br/>

Q. Running state의 프로세스가 CPU를 빼앗기는 경우는 I/O request가 왔을 때 뿐인가?

A. 아니다. Running state의 프로세스가 CPU를 빼앗기게 되는 경우는 크게 2가지가 존재한다.

첫번째로는 I/O request가 올 때, 두번째로는 Running state의 프로세스보다 더 높은 우선순위의 프로세스가 오는 경우이다.

하지만, 이 두가지 경우에는 큰 차이가 존재한다. I/O request가 오는 경우에는 해당 I/O request가 끝날 때까지 기다려야만 한다. 때문에 현재 Running state의 프로세스는 Waiting queue에 들어가게 된다. 반면, 더 높은 우선순위의 프로세스가 오는 경우에는 단순히 CPU를 뺏기기만 하는거지 기다릴 이유가 없다. 때문에 바로 Ready queue에 들어간다.

---

### 🔴Context Switch

- 위에서 언급했듯이, 프로세스를 수행하는 도중 더 높은 우선순위를 가지는 프로세스가 오게 되면, 원래 하던 프로세스에 interrupt를 걸고 더 높은 우선순위의 프로세스를 처리한다.
- Such operations happen frequently on general-purpose system.
- When an interrupt occurs, the system need to save the current **context** of the process currently running on the CPU so that it can restore that context when its(새롭게 들어온 우선순위가 높은 프로세스) processing is done. (interrupt가 걸려서 CPU가 이동하기 전에, 지금까지 이 프로세스에서 했던 작업들을 저장해두고 가야 다음에 돌아왔을 때 이어서 처리할 수 있다.)
- The **Context** is represented in the PCB of the process. (이 context를 저장해놓을 공간이 필요하기 때문에!! 프로세스마다 하나의 PCB를 할당하는 것이다.) 
- Switching the CPU to another process requires performing a state save of the current process and a state restore of a different process. 이 두 동작을 합쳐서 우리는 `Context Switch` 라 부른다. 
- It's speed varies from machine to machine, depending on the memory speed, the number of registers that must be copied, and the existence of special instructions.(such as a single instruction to load or store all registers.)

<br/>

Q. Context Switching은 오버헤드가 없는 동작일까?

A.아니다. Context Switching은 pure overhead를 갖는다.(1번당 5 microseconds on today's hardware) 왜냐하면, Switching을 하는 동안에는 CPU가 다른 일을 할 수 없다. 

---

### Process Creation

- A process may create several new processes, via a create-process system call `fork()`, during the course of execution.
- The creating process is called a **parent process** , and the new processes are called the **children of that process**.
- Each of these new processes may in turn(차례로) create other processes, forming a tree of processes.

  ![fork_system_call](../assets/img/fork_system_call.png)
- When a process creates a new process, two possibilities exist in terms of execution
  	1. The parent continues to execution concurrently with its children.
   	2. The parent waits until some or all of its children have terminated.
- There are also two possibilities in terms of the address space of the new process.
  1. The child process is a duplicate of the parent process.(It has the same program and data as the parent.)
  2. The child process has a new program loaded into it.

---

### Process Termination

- A process terminates when it finishes executing its final statement and asks the operating system to delete it by using the `exit()` system call.
- At that point, the process may return a status value(typically an integer) to its parent process. (via the `wait()` system call) 
- Process가 종료 되면, all the resources of the process - including physical and virtual memory, open files and I/O buffers - are deallocated by the OS.
- **Termination can occur in other circumstances as well**
  - A process can cause the termination of another process via an appropriate system call.
  - Usually, such a system call can be invoked only by the parent of the process that is to be terminated. (즉, 부모 프로세스가 종료될 때 시스템콜을 통해 자식 프로세스를 종료시킬 수 있다.)
  - Otherwise, users could arbitrarily kill each other's jobs.
- **A parent may terminate the execution of one of its children for a variety of reasons, such as these**
  - The child has exceeded its usage of some of the resources that it has been allocated. (이걸 알기 위해서 the parent must have a mechanism to inspect the state of its children.)
  - The task assigned to the child is no longer required.
  - The parent is exiting, and the operating system does not allow a child to continue if its parent terminates. (parent가 종료되면 이 parent의 child를 OS가 모두 종료시키는 경우도 있다.)

---

### fork() and exec() system call

- fork()

  > The fork() system call is used to create a separate, duplicate process.

- exec()

  > When an exec() system call is invoked, the program specified in the parameter to exec() will replace the entire process - including all threads.
  >
  > Replace process by another process and then another process has a same process id with older one.(왜냐하면, 새로 프로세스를 만든 것이 아니라 replace한 것이기 때문. 결국 pid는 둘 다 같지만 그 안에 내용이 다르게 된다.)

  ```c
  int main(){
  	① fork();
  	② fork();
  	③ fork();
  	④ print("Hello");
  }
  ```

  이 코드를 실행하면 가장 먼저 ④가 실행되면서 Hello가 출력된다. 그 다음 ①이 실행되는데 ④에 의해 만들어진 process의 child process가 1개 생긴다.(현재 전체 프로세스 2개) 그 다음 ②가 실행되는데 현재 process가 2개이고 각각에 child process가 붙는다.(현재 프로세스 4개) 그 다음 ③이 실행되면 현재 process는 4개이고 각각의 process에 child가 붙어서 process는 총 8개가 된다. fork()를 통해 새로운 process를 만든 것이기 때문에 pid는 모두 다르다.
  
  <br/>
  
  **결국! fork()와 exec()을 통해 자식 프로세스를 만드는 이유는 여러 작업들을 동시(물론 프로세서가 굉장히 빨리 움직여서 '동시' 처럼 보이는 것이지 실제로 완전한 동시는 아니다.)에 처리하기 위함에 있다. 하지만, 위의 내용들을 읽다보면 부모 프로세스의 내용들이 똑같이 자식 프로세스에 복사되는 일련의 과정들이 굉장히 비효율적이라는 생각이 들 수 밖에 없다. 때문에 "공통적으로 필요한 부분들은 공동으로 소유하면 어떨까?" 라는 아이디어가 나왔고, 이 아이디어를 바탕으로 만들어진 개념이 쓰레드(thread)이다.**

---

### 🔴Thread

> 1 개의 프로세스에 여러 쓰레드가 존재 가능하다. 이 여러 쓰레드들은 1개의 프로세스 안에서 많은 것들을 공유하면서 CPU를 나눠 사용한다.
>
> 프로세스를 만드는 것은 오버헤드가 큰 반면, 쓰레드는 프로세스에 비해 훨씬 **simple && light** 하다.

- A thread is a basis unit of CPU utilization.

- It comprises Thread ID, Program COunter, Register Set and Stack.

- It shares with other threads belonging to the same process its **code section, data section, and other operating systam resources, such as open files and signals.**

- A traditional/heavyweight process has a single thread of control. If a process has multiple threads of control, it can perform more than one task at a time.

  ![ThreadDiagram](../assets/img/ThreadDiagram.jpg)

- code, data, files와 같이 공통적으로 사용하는 부분들은 공유하면서 각각의 쓰레드가 독립된 레지스터와 스택을 갖는다.

<br/>

### Thread의 장점

- Responsiveness

  > Multithreading an interactive application may allow a program to continue running even if part of it is blocked or is performing a lengthy operation, thereby increasing responsiveness to the user.

- Resource Sharing

- Economy

- Utilization of Multiprocessor Architectures

<br/>

근데 그렇다고 항상 멀티 쓰레딩이 멀티 프로세싱보다 좋은 것은 아니다. 상황에 따라 다르게 된다.

https://www.crocus.co.kr/1510   //이 글의 내용이 좋다.

https://www.joinc.co.kr/w/Site/system_programing/Book_LSP/ch05_Process // 교수님께서 알려주신 사이트. 내용이 되게 자세하고 좋은 것 같다. 천천히 정독하고 여기 내용에 살을 붙이는 식으로 활용해야겠다.

---





