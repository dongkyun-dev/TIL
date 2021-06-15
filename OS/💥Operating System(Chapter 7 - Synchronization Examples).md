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
>
> 내용의 대부분이 https://jeonyeohun.github.io/2020-05-21/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-Readers-Wirters-%EB%AC%B8%EC%A0%9C 이 블로그의 내용을 참조했다. 이 부분뿐만 아니라 운영체제 전반적으로 좋은 글들이 많다. 이곳의 글들을 읽어보는 것이 나중에 면접을 대비하는데 큰 도움이 될 수 있겠다.

---

## 1. 생산자-소비자 문제 (유한 버퍼 문제)

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

## 2.Readers-Writers 문제

하나의 데이터베이스가 다수의 병행 프로세스 간에 공유된다며고 가정한다. 독자는 데이터베이스를 read 하는 요청, 저자는 데이터베이스를 write 하는 요청이라고 한다면 우리는 다음과 같은 충돌 상황에 대한 대처가 필요하다.

1. Write 하고 있는 중에 Read가 되는 경우
2. Write 하고 있는 중에 또 다른 Write가 요청된 경우

결국 문제를 야기하는 부분은 write이다. 이 문제들을 해결하기 위해서는 다음과 같은 대처가 필요하다.

1. Write가 수행되고 있는 동안에는 Read가 접근하지 못하도록 막는다, Read가 수행되고 있는 동안에는 Write가 접근하지 못하도록 막는다.
2. 한번에 하나의 Write만 접근을 허용한다.

이를 위해 세마포를 활용한 자료구조를 활용한다.

```c
semaphore rw_mutex = 1;
semaphore mutex = 1;
int read_count = 0;
```

mutex 세마포는 read_count를 갱신할 때 상호 배제를 보장하기 위해 사용한다. (n개의 Reader들이 접근했지만 이들이 동시에 접근해버려서 read_count가 n이 아닌 1만 증가하는 경우를 막기 위함.)

read_count는 현재 몇 개의 프로세스들이 객체를 읽고 있는지 알려준다.

rw_mutex 세마포는 writer들을 위한 상호 배제 세마포이다. 이것은 또한 임계구역으로 진입하는 첫번째 reader와, 임계구역을 빠져나오는 마지막 reader에 의해서도 사용된다. 그러나 다른 reader들이 임계구역 안에 있는 동안 임계구역을 드나드는 reader들은 이것을 사용하지 않는다. 해당 값을 1로 초기화하는 이유는 lock을 획들할 수 있는 프로세스의 개수가 최대 한 개이기 때문이다.

###  Writer의 구조

```c
do {
	wait(rw_mutex);    // 쓰기를 위한 lock 획득
	
	// 쓰기 작업 수행
	
	signal(rw_mutex);    // lock 반납
} while(true)
```

1. Writer는 일단 rw_mutex가 사용가능한 상태인지 확인한다. 만약 Reader가 데이터를 읽고 있다면, rw_mutex의 값이 0이기 때문에 Writer는 대기한다.
2. lock을 획득하게 되면 임계구역으로 진입해서 쓰기 작업을 수행한다. rw_mutex는 binary semaphore이기 때문에 만약 Writer가 lock을 획득했다면, Reader는 임계구역에 접근할 수 없게 된다.
3. 쓰기 작업을 마치면 signal을 실행하면서 lock을 방출하고 작업을 종료한다.

### Reader의 구조

```c
do {
	wait(mutex);    // read_count의 중가 연산이 다른 프로세스의 영향을 받지 않게 하기 위해 lock 획득
	read_count++;   // 증가. mutex 세마포어 덕분에 한번에 하나의 증가만 일어난다.
	if(read_count == 1) {   // read_count가 1이라면 제일 처음 읽기를 시도하는 프로세스
		wait(rw_mutex);    // Writer가 작업 중인지 확인하고 작업중이면 대기상태로 넣기
	}
	signal(mutex);    // lock 반환

	// 읽기 작업 수행
	
	wait(mutex);    // read_count의 값을 줄이기 위해 lock 획득
	read_count--;   
	if(read_count == 0) {    // 만약 read_count의 값이 0이라면, 현재 읽기 작업을 수행 중인 프로세스가 없다.
		signal(rw_mutex);    // 대기 중인 Writer에 signal을 보낸다.
	}
	signal(mutex);    // lock 반환
} while(true)

```

임계구역을 기준으로 앞 뒤로 나누어서 확인한다.

### 읽기 작업 진입 전

```c
wait(mutex);    // read_count의 중가 연산이 다른 프로세스의 영향을 받지 않게 하기 위해 lock 획득
read_count++;   // 증가. mutex 세마포어 덕분에 한번에 하나의 증가만 일어난다.
if(read_count == 1) {   // read_count가 1이라면 제일 처음 읽기를 시도하는 프로세스
	wait(rw_mutex);    // Writer가 작업 중인지 확인하고 작업중이면 대기상태로 넣기
}
signal(mutex);    // lock 반환

// 읽기 작업 수행
```

1. 다수의 Reader가 임계구역 진입을 시도할 수 있다. 이때 `read_count`의 값을 증가시키는 것이 atomic하게 수행되어야 하는데 `++`연산은 atomic한 연산이 아니다.
2. 따라서 `mutex`로 lock을 획득해서 `read_count`를 한번에 한 프로세스에서만 증가시킬 수 있게 한다.
3. `read_count`를 증가시킨 시점에서 `read_count`의 값이 1이라면, 해당 프로세스가 임계구역에 진입하는 최초의 Reader 임을 의미한다. lock을 획득해서 read_count 값을 올리는 이유가 바로 여기에 있다. 만약 lock이 없었다면 여러 Reader가 `read_count`의 값을 증가시켜서 최초의 프로세스가 누구였는지 판단할 수 없게 된다.
4. 진입을 시도하는 프로세스가 최초의 Reader라면 Writer가 작업 중일 가능성이 있기 때문에 rw_mutex를 확인해서 Writer가 작업 중이라면 Reader를 대기 큐에 넣는다.
5. 만약 이 프로세스가 읽기를 처음 시작하는 프로세스가 아니라면 이미 임계구역 내에서 읽기 작업을 수행중인 프로세스가 있다는 것이기 때문에 `rw_mutex`를 검사할 필요없이 곧바로 임계구역으로 진입한다.
6. 임계구역으로 진입하면서 다른 Reader 의 접근을 허용하기 위해 `signal(mutex)`를 실행한다.

### 읽기 작업 후

```c
// 읽기 작업 수행

wait(mutex);    // read_count의 값을 줄이기 위해 lock 획득
read_count--;   
if(read_count == 0) {    // 만약 read_count의 값이 0이라면, 현재 읽기 작업을 수행 중인 프로세스가 없다.
	signal(rw_mutex);    // 대기 중인 Writer에 signal을 보낸다.
}
signal(mutex);    // lock 반환
```

1. 읽기 작업이 끝나면, Reader는 임계구역을 빠져나오면서 `read_count `를 하나 감소시켜야 한다.
2. 이때 역시 `read_count `의 감소연산을 atomic하게 만들기 위해 mutex 세마포어를 통해 다른 Reader의 접근을 제한한다.
3. `read_count`의 값을 하나 줄였을 때 결과 값이 0이 된다면 방금 임계구역을 빠져나온 프로세스가 실행중이던 마지막 Reader가 된다.
4. 따라서 `rw_mutex`의 값을 증가시켜서 Writer가 lock을 획득할 수 있게한다.
5. 마지막 Reader가 아니라면 아직 임계구역에서 작업중인 Reader가 있다는 것을 의미하고 Writer 의 접근이 제한되어야 하기 때문에 다른 mutex만 signal 해주고 종료된다

Reader-writer 락은 다음과 같은 상황에서 가장 유용하다.

- 공유 데이터를 읽기만 하는 프로세스와 쓰기만 하는 스레드를 식별하기 쉬운 응용
- Writer보다 reader의 개수가 많은 응용. 일반적으로 reader-writer 락을 설정하는 데에 드는 오버헤드가 세마포나 상호 배제 락을 설정할 때보다 크다. 이 오버헤드는 동시에 여러 reader거 읽게 하여 병행성을 높임으로써 상쇄할 수 있다.

결국, Readers-Writers 문제에서의 핵심은 Reader는 문제를 일으키지 않는다는 것이다. 그렇다면 모두가 Reader가 된다면 그 프로그래밍은 얼마나 효율적일까라는 생각을 해볼 수 있다. => 함수형 프로그래밍

---







 https://jeonyeohun.github.io/2020-05-21/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EC%9C%A0%ED%95%9C%EB%B2%84%ED%8D%BC%EB%AC%B8%EC%A0%9C

https://ko.wikipedia.org/wiki/%EC%83%9D%EC%82%B0%EC%9E%90-%EC%86%8C%EB%B9%84%EC%9E%90_%EB%AC%B8%EC%A0%9C

https://m.blog.naver.com/hirit808/221785171412