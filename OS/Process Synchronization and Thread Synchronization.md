# Process Synchronization and Thread Synchronization

> *시스템은 일반적으로 concurrently하게 또는 parallelism하게 실행되는 수백 개 또는 수천 개의 스레드로 구성된다. 스레드는 종종 사용자 데이터를 공유한다. 공유 데이터에 대한 액세스가 제어되지 않으면 경쟁 조건이 존재하여 데이터 값이 손상될 수 있다. 프로세스 동기화는 경쟁 조건을 피하고자 공유 데이터에 대한 액세스를 제어하는 도구를 사용한다.* - 공룡책 Part3 도입
>
> 이 설명 때문에 엄청나게 시간을 버렸다. "수백 수천 개의 스레드로 구성되고 스레드가 사용자 데이터를 공유하는데 이에 대한 해결이 프로세스 동기화라고??"
>
> 지금까지의 나의 지식으로 바라봤을 때 이 설명은 완전히 잘못된 설명이다. 동기화의 대상은 프로세스만 있는 것이 아니다. 스레드 또한 동기화의 대상이다.
>
> 멀티 프로세싱에서는 여러 프로세스들이 concurrently혹은 parallelism하게 shared memory에 접근하게 된다. 때문에 프로세스 동기화는 이 shared memory와 관련되어 있다. 
>
> 멀티 스레딩은 1개의 프로세스 내부에 여러 스레드들이 존재하는 것이다. 이 안에서 여러 스레드들은 해당 프로세스의 힙, 데이터, 코드 영역을 공유해서 사용한다. 때문에 스레드 동기화는 이 힙, 데이터, 코드 영역과 관련되어 있다.

---

### Race Condition

>둘 이상의 입력 또는 조작의 타이밍이나 순서 등이 결과값에 영향을 줄 수 있는 상태

프로세스 스케줄링의 역할을 소개했고 코어가 1개인 상황에서 프로세스가 여러개라면 CPU 스케줄러가 프로세스 사이에서 빠르게 오가며 각 프로세스를 실행하여 모든 프로세스를 concurrently하게 실행시킨다는 것을 설명하였다. 이는 한 프로세스가 스케줄되기 전에 일부분만 진행할 수 있다는 것을 의미한다.

여기서 문제가 되는 것은 여러 프로세스 혹은 여러 스레드가 공통적으로 참조하는 영역이 존재한다는 것이다. 공통적으로 참조하는 영역의 값을 바꿔버리면 다음에 오는 프로세스 혹은 스레드는 이게 원래 값인지 바뀐 값인지 알 수 없다. 이러한 상황을 **Race Condition**이라 한다. 

즉, Race Condition은 "타이밍에 따라 결과값이 바뀌는 상황"으로 정의할 수 있겠다. 정의는 알았지만 더 빠른 이해를 위해 그림을 통해 설명하겠다. (아래 그림은 프로세스 동기화의 예제이기 때문에 shared memory가 등장했지만, 만약 스레드 동기화의 예제였다면 shared memory가 아닌 힙, 데이터, 코드 영역이 등장했을 것이다.)

![race_condition_shared_memory](../assets/img/race_condition_shared_memory.jpg)

- P1의 실행이 앞서는 경우

  count = 5

  P1: count++   =>   count = 6

  P2: count--     =>   count = 5

- P2의 실행이 앞서는 경우

  count = 5

  P2: count--     =>   count = 4

  P1: count++   =>   count = 5

이렇게만 보면 결국 결과는 둘 다 5가 나오기 때문에 별 문제가 없어 보일 수 있다. 때문에 컴파일 후의 코드로 생각해볼 필요가 있다.

![race_condition_after_compile](../assets/img/race_condition_after_compile.jpg)

검정색 숫자의 ①~⑥의 순서로 진행되면 count: 5 -> 4 -> 6 으로 변한다.

빨간색 숫자의 ①~⑥의 순서로 진행되면 count: 5 -> 6 -> 4 으로 변한다.

이렇게 어떤 코드가 먼저 실행될지 모르는 상황에서, 타이밍에 따라 다른 결과가 나오는 이 상황을 race condition이라 한다.

그렇다면 이 race condition을 어떻게 막을 수 있을까?

바로 **critical section**을 사용하는 것이다. P1의 어셈블리 코드 3줄과 P2의 어셈블리 코드 3줄과 같은 부분이 critical section의 대상이 될 수 있는 부분이다.

이렇게 critical section을 정해두고 이 critical section이 실행되는 동안에는 다른 프로세스가 접근할 수 없도록 막음으로써 race condition 문제를 해결하는 것이다.

---

Q. 멀티프로세싱에서는 항상 프로세스 동기화가 필요할까?

A. 당연히 항상 필요한 것은 아니다. 설명에 앞서 멀티프로세싱에 대한 정의를 짚고 넘어갈 필요가 있다. 

Two or more processors or CPUs present in same computer, sharing system bus, memory and I/O is called Multiprocessing System.

즉, 멀티프로세싱은 기본적으로 두 개 이상의 CPU를 가지는 체계이다. (지금까지 나는 CPU가 하나만 있어도 이 하나의 CPU가 여러 프로세스를 옮겨다니면서 concurrently하게 처리한다면 멀티프로세싱인줄 알았는데 아니었다.)

멀티프로세싱 속에서 처리되는 여러 개의 프로세스들은 Independent 할 수도 Cooperative 할 수도 있다. Independent한 경우 이론적으로 동기화가 필요하지 않다. 하지만, 모든 프로세스들을 각각 Independent하게 동작하도록 설계하는 것은 엄청난 비효율일 것이다.

---

### Critical Section(Critical Region)

> 적어도 하나 이상의 다른 프로세스 혹은 스레드와 공유하는 데이터에 접근 혹은 갱신하는 코드 부분

위의 예제에서 확인했듯이, 각 프로세스 혹은 스레드는 임계 구역(Critical Section)이라고 부르는 코드 부분을 포함하고 있고, 그 안에서는 적어도 하나 이상의 다른 프로세스와 공유하는 데이터에 접근하고 갱신할 수 있다.

이 시스템의 중요한 특징은 한 프로세스가 자신의 임계 구역에서 수행하는 동안에는, 다른 프로세스들이 그들의 임계 구역에 들어갈 수 없다는 사실이다.

<img src="../assets/img/critical_regions.jpg" alt="critical_regions" style="zoom:80%;" />

출처: https://slidetodoc.com/introduction-to-synchronization-cs3013-operating-systems-slides-include/

그렇다면 자신의 임계 구역이 실행되는 동안, 다른 프로세스들이 그들의 임계 구역에 들어가지 못하도록 어떻게 막을 것이냐?

이 문제에 대한 해결안이 되기 위해서는 세 가지의 요구 조건을 충족해야 한다.

1. 상호 배제(mutual exclusion): 프로세스 P1이 자신의 임계구역에서 실행된다면, 다른 프로세스들은 그들 자신의 임계구역에서 실행될 수 없다. (P1이 임계 구역에 들어갔다면 처리가 끝날 때까지 다른 프로세스들의 접근을 차단해야 한다.)
2. 진행(progress): 자기의 임계구역에서 실행되는 프로세스가 없고 그들 자신의 임계구역으로 진입하려고 하는 프로세스들이 있다면, 나머지 구역에서 실행 중이지 않은 프로세스들만 다음에 누가 그 임계구역으로 진입할 수 있는지를 결정하는 데 참여할 수 있으며, 이 선택은 무한전 연기될 수 없다. (만약 P1과 P2가 동시에 각각의 임계 구역에 도착한다면, P1의 임계 구역을 먼저 수행시키고 P2의 임계 구역은 기다린다. P1이 exit된 이후에 P2의 임계 구역이 시작된다. P2의 임계 구역이 exit하고 P3의 임계 구역이 와야 하는데 P1의 다음 임계 구역이 먼저 온다면? 그렇다면 P1의 임계 구역을 블락해놓고 P3의 임계 구역이 오기를 기다린다.(반드시 순서를 지켜야한다.))
3. 한정된 대기(bounded waiting): 프로세스가 자기의 임계구역에 진입하려는 요청을 한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 그들 자신의 임계구역에 진입하도록 허용하는 횟수에 한계가 있어야 한다. ()

https://stackoverflow.com/questions/33143779/what-is-progress-and-bounded-waiting-in-critical-section

이 세 가지 요구 조건을 만족시키는 해결방안들이 가지는 일반적인 구조는 다음과 같다.

![general_structure_of_critical_section](../assets/img/general_structure_of_critical_section.png)

