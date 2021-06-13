# 💥Operating System(Chapter 7 - Synchronization Examples)

> 이 챕터에서는 고전적인 동기화 문제들에 대해서 알아본다.
>
> 고전적인 동기화 문제는 크게 3가지가 존재한다.
>
> 1. 생산자-소비자 문제(유한 버퍼 문제)
> 2. Readers-Writers 문제
> 3. 식사하는 철학자들 문제
>
> 7장에서는 고전적인 동기화 문제들 뿐만 아니라 커널, 자바, POSIX에서의 동기화에 대한 내용들도 존재하기는 하나 내가 이해할 수 있는 내용들은 아닌 것 같다. 나중에 언젠가 해당 내용에 대해 이해해야할 필요가 있어질 때 읽으면 되겠다.
>
> 7장의 맨 마지막에는 함수형 프로그래밍에 대한 이야기가 나온다. 이 부분은 중요하기 때문에 정리하고 넘어갈 생각이다.

---

### 1. 생산자-소비자 문제 (유한 버퍼 문제)

생산자-소비자 문제는 유한 버퍼 문제로도 불린다. 설명의 편의를 위해 앞으로는 생산자-소비자 문제로만 언급하겠다.

![Producer_Consumer](../assets/img/Producer_Consumer.png)

이 그림에서도 볼 수 있듯이 생산자-소비자 문제의 핵심은 버퍼의 존재 때문이다.

생산자는 기본적으로 자신이 생성한 데이터를 소비자에게 바로 보내지 않고 버퍼(Buffer)에 저장한다. 그 이유는 생산자의 생산 속도와 소비자의 소비 속도가 동일하지 않기 때문이다. 생산 속도가 너무 빨라 소비자가 1개를 소비하는 시간 동안 10개가 생산되어져서 온다면 9개를 버려야하는 불상사가 생긴다. 때문에 우리는 9개를 잠시 저장해둘 공간이 필요하고 이 공간을 버퍼라 한다.

하지만, 생산자와 소비자 사이의 버퍼는 메모리 상에 존재하기 때문에 크기가 유한할 수 밖에 없다. 무한히 저장할 수도, 또 무한히 꺼내올 수도 없다. 버퍼가 가득 차면 생산자는 더이상 데이터를 저장할 수 없고, 버퍼가 비어있다면 소비자는 데이터를 소비할 수 없게 된다.

때문에 버퍼가 가득차 있다면 생성자는 생성을 잠시 멈춰야 하고, 버퍼가 비어있다면 소비자는 소비를 잠시 멈춰야 한다.

이를 위해 우리는 세마포를 활용한 다음과 같은 자료구조를 활용한다. (동기화 문제에 대한 해결책을 제시할 때 전통적으로 세마포를 사용해 왔기 때문에 우리의 해결안에서도 세마포를 사용한다. 그러나 이 해결책을 실제 구현할 때에는 이진 세마포 대신에 `mutex lock`이 사용될 수도 있다. 즉, 고전적인 동기화 문제들을 해결하는데에 있어서 반드시 세마포를 써야 하는 것은 아니다.)

```c
int n;    // 버퍼의 크기
semaphore mutex = 1;    // 상호배제를 위한 임계구역 진입여부 표시 (0 혹은 1)
semaphore empty = n;    // 비어있는 버퍼의 자리 개수 (0~n개)
semaphore full = 0;     // 차있는 버퍼의 자리 개수 (0~n개)
```

<br/>

(signal, wait 함수의 모양은 chapter 6 정리본에서 확인하면 된다.)

앞으로 "lock을 획득했다." = "mutex값을 1로 바꾼다." ,   "lock을 해제한다." = "mutex값을 0으로 바꾼다." 와 같은 의미라고 생각하면 된다.

#### 생산자 프로세스의 구조

```c
do {
	// 자원 생성
	
	wait(empty);    // 빈자리가 생길 때까지 대기한다.
	wait(mutex);    // 임계구역 진입이 획득될 때까지 대기한다.
	
	// 버퍼에 자원 넣기
	
	signal(mutex);    // 임계구역 진입 lock을 푼다.
	signal(full);     // 버퍼에 새로 들어간 자원이 있다는 것을 알린다.
} while(true);
```

생산자의 역할은 자원을 버퍼에 넣는 것이다. 이런 맥락에서 위 코드를 해석하면 생성자가 하는 일의 과정은 다음과 같다.

1. 버퍼에 넣을 자원 정하기
2. 버퍼에 빈자리가 있는지 확인하고 빈자리가 없다면 대기
3. 빈자리가 생기면 lock 획득 후 임계구역 진입
4. 임계구역 내에서 버퍼에 자원 넣기
5. 임계구역 탈출
6. 임계구역을 빠져나오면서 lock 해제 및 signal(full) 실행

#### 소비자 프로세스의 구조

```c
do {
	wait(full);    // 자원 대기
	wait(mutex);   // lock 획득
	
	// 버퍼에서 자원 하나 가져오기. 해당 버퍼는 비워진다.
	
	signal(mutex);    // lock 해제
	signal(empty);    // 자원을 하나 가져갔기 때문에 버퍼에 빈자리가 생겼다는 것을 알린다.
	
	// 가져온 자원을 사용한다.
} while(true);
```

Consumer의 역할은 버퍼에 남아있는 자원이 있다면, 해당 자원을 버퍼에서 꺼내 가져오는 것이다.

이 맥락에서 위 코드를 읽어보면 Consumer의 작업은 다음과 같은 과정으로 이루어진다.

1. wait(full)을 통해 자원이 있는지 확인
2. 자원이 있다면 계속 진행, 자원이 없다면 대기
3. 자원이 있으면 lock을 획득하고 임계구역으로 진입해서 자원을 하나 소비
4. 임계구역 탈출
5. 임계구역을 빠져나오면서 lock 해제 및 signal(empty) 실행

### lock 을 함께 사용하는 이유

위 자료구조에서 세마포어가 총 세 개가 사용된다. 비어있는 버퍼의 갯수를 나타내는 `empty`, 차 있는 버퍼의 갯수를 나타내는 `full` 그리고 프로세스간 상호 배제를 위해 사용하는 `mutex`이다. 세마포어 변수의 이름이 `mutex` 로 지어져서 헷갈릴 수도 있지만, 이 변수는 binary semaphore이지 mutex lock은 아니다. 근데 왜 굳이 lock 역할을 하는 세마포어가 또 필요할끼?

문제는 full 과 empty의 signal 과 wait 연산에 있다. 두 연산은 그 자체로는 전혀 atomic 한 연산이 아니다. 내부적으로 값도 증가시켜주어야 하고, 대기를 시킬 때는 리스트에 PCB를 연결해주는 것 까지 해야한다. 따라서 signal 에 의해 동시에 여러 프로세스들이 임계구역 진입에 대한 조건을 만족하게 되면, 저마다 대기 큐에서 빠져나오려고 할 텐데, 이 때 binary semaphore를 사용해서 단 하나의 프로세스에게만 진입을 허용한다면, 동시에 여러 프로세스가 임계구역으로 진입하는 문제를 막을 수 있을 것이다.

#### 모니터를 이용한 방법

```c
monitor ProducerConsumer
{
    int itemCount = 0;
    condition full;
    condition empty;

    procedure add(item)
    {
        if (itemCount == BUFFER_SIZE)
        {
            wait(full);
        }

        putItemIntoBuffer(item);
        itemCount = itemCount + 1;

        if (itemCount == 1)
        {
            notify(empty);
        }
    }

    procedure remove()
    {
        if (itemCount == 0)
        {
            wait(empty);
        }

        item = removeItemFromBuffer();
        itemCount = itemCount - 1;

        if (itemCount == BUFFER_SIZE - 1)
        {
            notify(full);
        }


        return item;
    }
}

procedure producer()
{
    while (true)
    {
        item = produceItem();
        ProducerConsumer.add(item);
    }
}

procedure consumer()
{
    while (true)
    {
        item = ProducerConsumer.remove();
        consumeItem(item);
    }
}
```



---



 https://jeonyeohun.github.io/2020-05-21/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EC%9C%A0%ED%95%9C%EB%B2%84%ED%8D%BC%EB%AC%B8%EC%A0%9C

https://ko.wikipedia.org/wiki/%EC%83%9D%EC%82%B0%EC%9E%90-%EC%86%8C%EB%B9%84%EC%9E%90_%EB%AC%B8%EC%A0%9C

https://m.blog.naver.com/hirit808/221785171412