# 💥Operating System(Chapter 1 - Introduction)

---

> 본 내용은 WILEY 출판사에서 발간한 운영체제 10판(번역본)을 바탕으로 작성하였습니다.

운영체제란 기본적으로 컴퓨터 하드웨어를 관리하는 소프트웨어이다. 컴퓨터 사용자(User)와 컴퓨터 하드웨어(Hardware) 사이에 위치하며 중재자 역할을 수행한다.

![CSCI 6730 / 4730 Operating Systems Key Questions in System Design](data:image/png;base64,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)

이 사진은 이러한 운영체제의 역할을 잘 보여준다. 운영체제는 그 자체로 유용한 기능을 수행하는 소프트웨어가 아니다. 운영체제는 단순히 다른 프로그램이 유용한 작업을 할 수 있는 환경을 제공하는 역할을 한다.

---

Modern computer system consists of one or more CPUs and a number of device controllers(ex. Disk Controller, USB Controller). Those are connected by common bus and they shared memory.

일반적으로 운영체제에는 각 장치 컨트롤러마다 장치 드라이버가 있다. 이 장치 드라이버는 장치 컨트롤러의 작동을 잘 알고 있고 나머지 운영체제에 장치에 대한 일관된 인터페이스를 제공한다.

---

### 운영체제의 목표는 크게 3가지로 분류할 수 있다.

1. Convenience - It is an interface between User & Hardware.
2. Efficiency -  Allocation of Resources.
3. Both - Management of Memory, Security, etc.

---

###  중요한 개념인 Bootstrap program, Interrupt, system call에 대한 간단한 설명

- #### Bootstrap Program

  > - The initial program that runs when a computer is powered up or rebooted.
  > - It is stored in the ROM(Read-Only-Memory).
  > - It must know how to load the OS and start executing that system.
  > - It must locate and load into Memory the OS kernel.

- #### Interrupt(주로 Hardware에서 발생, 굉장히 중요한 개념이기 때문에 아래에 설명을 추가하겠습니다.)

  > - The occurrence of an event is usually signaled by an Interrupt from Hardware or Software.
  >
  > - Hardware may trigger an interrupt at anytime by sending a signal to the CPU, usually by the way of the system bus.
  >
  > - Execution Progress 1 
  >
  >   When the CPU is interrupted, it stops what it is doing and immediately transfers execution to a fixed location.(The fixed location usually contains the starting address where the Service Routine of the interrupt is located.)
  >
  > - Execution Progress 2
  >
  >   The Interrupt Service Routine executes. On completion, the CPU resumes the interrupted computation.

- #### System Call(Software에서 발생)

  > - Software may trigger an interrupt by executing a special operation called System Call.

---

## 🔴Interrupts

> CPU가 프로그램을 실행하고 있을 때, 입출력 하드웨어 장치의 요청이나 예외상황이 발생하여 처리가 필요한 경우 CPU에게 알려서 처리할 수 있도록 하는 것

<br/>

하드웨어는 어느 순간이든 시스템 버스를 통해 CPU에 신호를 보내 인터럽트를 발생시킬 수 있다.

CPU가 인터럽트 되면

1. CPU는 하던 일을 중단하고 즉시 고정된 위치로 실행을 옮긴다.(고정된 위치란 일반적으로 인터럽트를 위한 서비스 루틴이 위치한 시작 주소이다.)
2. 인터럽트를 위한 서비스 루틴이 실행된다.
3. 인터럽트 서비스 루틴의 실행이 완료되면 CPU는 인터럽트 되었던 연산을 재개한다.

여기서 중요한 것은 인터럽트 되었던 연산을 재개하기 위해서는 해당 연산이 남아있어야 한다는 것이다. 하지만, 현재 CPU는 인터럽트를 위한 서비스 루틴을 실행한 직후이고 모든 값들이 인터럽트를 위한 서비스 루틴에 맞추어져 있는 상태이다.

때문에! 반드시 1번 과정 이전에 현재의 상태를 저장해두어야 한다. 상태를 저장해두어야 인터럽트를 위한 서비스 루틴을 수행한 후, 저장되어 있던 복귀 주소를 프로그램 카운터에 적재하고, 인터럽트에 의해 중단되었던 연산이 인터럽트가 발생하지 않았던 것처럼 다시 시작할 수 있다.(어떻게 어디에 저장하는지는 이후 챕터에서 나온다.)



![Exception and interrupt handling :: Operating systems 2018](💥Operating System(Chapter 1 - Introduction).assets/exception-and-interrupt-handling-v1.png)

이 사진 한 장이 인터럽트의 과정을 잘 설명하고 있다. 하지만, 조금 더 구체적으로  인터럽트는 단순히 외부에서만 발생하지 않는다.

- 외부 인터럽트

> 하드웨어가 발생시키는 인터럽트
>
> CPU가 아닌 다른 하드웨어 장치가 CPU에 요청하는 경우
>
> 아마 외부 인터럽트가 일반 인터럽트의 대부분을 차지하지 않을까?

- 내부 인터럽트

  >CPU 내부에서 실행하면서 인터럽트에 걸리는 경우
  >
  >Divide by Zero, Overflow, Underflow

- 소프트웨어 인터럽트

  > 소프트웨어가 발생시키는 인터럽트
  >
  > 예외상황, system call

  <br/>

  `+` 인터럽트 기법에는 우선순위 레벨도 존재한다고 한다. 우선순위가 높은 인터럽트를 먼저 처리할 수도, 우선순위가 낮은 인터럽트를 늦게 처리할 수도 있다.

  <br/>

  <br/>

  **요약하면, 인터럽트는 최신 운영체제에서 비동기 이벤트를 처리하기 위해 사용된다. 장치 컨트롤러 및 하드웨어 오류로 인해 인터럽트가 발생한다. 가장 긴급한 작업을 먼저 수행하기 위해 최신 컴퓨터는 인터럽트 우선순위 시스템을 사용한다. 인터럽트는 시간에 민감한 처리에 빈번하게 사용되므로 시스템 성능을 좋게 하려면 효율적인 인터럽트 처리가 필요하다.**

---

## 🔴DMA(Direct Memory Access)

> 기본으로 알아야 하는 내용
>
> 1. OS 코드는 많은 부분들이 I/O를 manage 하는데 할당된다. 왜냐하면,  I/O의 reliability(신뢰성)와 performance(성능)이 중요하고, I/O 디바이스의 종류가 굉장히 다양하기 때문
> 2. Each device controller is in charge of a specific type of device. (Device controller maintains load storage buffer and set of special purpose registers.)
> 3. Typically OS has a device driver for each device controller.

<br/>

위에 언급한 interrupt를 통한 I/O는 소량의 데이터일 때는 괜찮지만, 대량의 데이터 이동 시에는 굉장히 높은 오버헤드를 발생시킬 수 있다.(당연하지 한 바이트마다 인터럽트가 발생할 것이고 인터럽트 자체가 pure overhead이다.)

이 문제를 해결하기 위해 DMA가 사용된다.  

DMA는 블록마다 딱 1번의 인터럽트가 발생한다.(디바이스 드라이버의 동작이 완료 되었을 때)

인터럽트를 거는 횟수가 월등히 적어지고 디바이스와 메모리가 직접적으로 데이터를 transfer하기 때문에 CPU는 그동안 다른 일을 처리할 수 있다.

![DMA](/assets/img/DMA.png)

---

![심심해서 하는 블로그 :: [컴퓨터 구조] 캐시 메모리(Cache)와 매핑 방법](💥Operating System(Chapter 1 - Introduction).assets/27460D4E57FA4D7C24)

블록이란 메모리에서 캐쉬로 이동하는 데이터의 단위이다.

---

### 조금 더 심화된 의문. OS는 어떤 디바이스들이 연결되어 있는지 어떻게 알고 있는 것일까?

기본적으로 I/O  디바이스는 I/O 포트에 의해 머신에 연결된다. 디바이스는 자신만의 디바이스 컨트롤러를 갖는다. 디바이스 컨트롤러는 I/O machine과 OS, 그 중간에 위치한 하드웨어이다.

OS가 I/O 디바이스 조작을 위해 CPU에 Instructions를 보낸다. 그러면 이 CPU는 이 I/O 디바이스들에 시그널을 보낸다.(디바이스 컨트롤러를 통해) 그렇다면 OS는 어떤 디바이스들이 연결되어 있는지, 디바이스 working을 위해 어떤 instructions를 보내야 하는지 어떻게 알고 있는 것일까.

=> manufacture가 디바이스를 만들 때 `디바이스 프로그램` 또한 같이 배포하게 된다. 이 때문에 OS에는 디바이스에 대한 정보들이 전혀 존재하지 않는다. 따라서, OS가 디바이스와 상호작용할 수 있는 유일한 수단은 이것의 `디바이스 프로그램`을 통해서 이다. 많은 초심자들이 일단 드라이버가 설치만 되면 OS는 디바이스의 모든 정보를 이해한다고 생각한다. 하지만, 이것은 사실이 아니고, 드라이버는 OS의 한 부분이 절대 될 수 없다. 결국 드라이버 프로그램이 instructions을 보내고 디바이스로부터 데이터를 받으면 드라이버 프로그램이 그 정보를 OS에 전달하는 식의 플로우이다. OS와 드라이버 간의 소통은 굉장히 간단하게 이루어지는데 둘다 메모리에서 load 되는 것들이기 때문이다. 드라이버는 메모리 공간에서 오랫동안 존재하는 **daemon program**이라 생각할 수 있다. 이러한 이유들 때문에 드라이버 프로그램이 crash 되자마자 I/O 디바이스의 동작이 멈추는 것이다.

**위의 내용은 굉장히 심화된 내용이면 나 또한 부분부분 이해하지 못한 내용들이 많다. 언젠가 실력이 올라서 이 내용을 이해할 수 있는 순간이 오지 않을까라는 생각에 일단 적어둔다.**

---

##  Storage Structure

<br/>

![storage_structure](/assets/img/storage_structure.png)

- 범용 컴퓨터는 프로그램 대부분을 메인 메모리(randim-access-memory 또는 RAM 이라 부른다)라 불리는 재기록 가능한 메모리에서 가져온다. 메인 메모리는 dynamic random-access memory(DRAM)이라 불리는 반도체 기술로 구현한다.
- 캐쉬는 CPU 속에 들어가 있다.
- 보통 비휘발성 저장장치 전부를 `DISK`로 표현한다.(이건 정확하지 않을 수 있다. 근데 보통 그런 식으로 다들 그림을 그리는 것 같다.)

---

