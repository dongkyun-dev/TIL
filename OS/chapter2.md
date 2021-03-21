# 프로세스

> *초기의 컴퓨터 시스템은 한 번에 하나의 프로그램만을 실행하도록 허용했다. 반면, 오늘날의 컴퓨터 시스템들은 메모리에 다수의 프로그램들을 적재해놓고 병행 실행하는 것을 허용한다. 이러한 발전은 다양한 프로그램들을 보다 견고하게 제어하고 보다 구획화할 것을 필요로 했다. 이러한 필요성이 프로세스의 개념을 낳았으며, **프로세스란 실행 중인 프로그램을 말한다.(정확히 말하면 실행되어 메인 메모리에 적재된 프로그램)*** - 공룡책 p,117
>
> 프로그램은 수동적인 존재(passive entity), 프로세스는 능동적인 존재(active entity) 라고 할 수 있다.
>
> 프로그램의 실행 파일이 메모리에 적재(load)되는 순간, 프로그램은 프로세스가 된다. 프로그램의 실행 파일은 메모리에 적재하는 방법은 아이콘을 더블 클릭하는 방식과 명령어 라인상에서 `prog.exe` 또는 `a.out` 과 같이 파일 이름을 입력하는 방식이다.

---

### 프로세스의 메모리 배치

- 텍스트 섹션 - 실행 코드
- 데이터 섹션 - 전역 변수
- 힙 섹션 - 프로그램 실행 중에 동적으로 할당되는 메모리
- 스택 섹션 - 함수를 호출할 때 임시 데이터 저장장소(ex. 함수 매개변수, 복귀 주소 및 지역 변수)

함수가 호출 될 때마다 함수 매개변수, 지역 변수 및 복귀 주소를 포함하는 **활성화 레코드(activation record)**가 스택에 푸시 된다. 함수에서 제어가 되돌아오면 스택에서 활성화 레코드가 팝 된다. 마찬가지로, 메모리가 동적으로 할당됨에 따라 힙이 커지고 메모리가 시스템에 반환되면 축소된다. 스택 및 힙 영역이 서로의 방향으로 커지더라도 운영체제는 이 둘이 서로 겹치지 않게 만들어야 한다.

---

### 활성화 레코드

![activation_record](../assets/img/activation-record.png)



하나의 활성화 레코드는 이런 모양을 가지고 있다.

called procedure는 해당 활성화 레코드를, calling procedure는 함수를 호출한 프로세스를 말한다고 생각하면 이해하기가 좋을 것 같다.

- **Return Value** : It is used by calling procedure to return a value to calling procedure.
- **Actual Parameter** : It is used by calling procedures to supply parameters to the called procedures.
- **Control Link:** It points to activation record of the caller.
- **Access Link:** It is used to refer to non-local data held in other activation records.
- **Saved Machine Status:** It holds the information about status of machine before the procedure is called.
- **Local Data:** It holds the data that is local to the execution of the procedure.
- **Temporaries:** It stores the value that arises in the evaluation of an expression.

출처)https://www.javatpoint.com/activation-record

> 이 부분은 조금 더 공부해보고 내용을 추가할 필요가 있을 것 같다. 일단은 대략적으로만 이해하고 넘어간다.

---

### 🔴스택 영역 vs 힙 영역

> 만약에 면접에 가서 ""스택과 힙의 차이가 무엇인가요?" 라는 질문이 나오면
>
> 스택은 정적이며 컴파일 타임에 할당되는 반면, 

#### 스택이란?

- 함수에 의해 만들어지는 임시 변수(temporary variables)들이 저장되는 컴퓨터 메모리의 특별한 영역이다.

- 스택 속에서 변수들은 **런타임 동안** 선언(declare), 저장(store), 초기화(initialize) 된다.(이 이유 때문에 stackoverflow(스택의 영역이 흘러넘쳐서 힙 영역을 침범하는 에러) 에러가 runtime 에러인 것이다.)

  > 다른거 다 까먹더라도 스택 영역에 변수가 저장되는 일은 런타임에 일어난다. 이거 하나는 꼭 기억해야 한다.(런타임에 메서드콜이 일어나고 그 이후에 저장.)

- 스택은 임시 저장 메모리(temporary storage memory)이다. 

- When the computing task is complete, the memory of the variable will be automatically erased.

- The stack section mostly contains methods, local variable, and reference variables.

#### 힙이란? 

- 동적 할당(Dynamic memory allocation)된 변수들이 힙 영역에 저장된다.

- **The heap is not managed automatically for you and is not as tightly managed by the CPU.**

  > 이 말은 C/C++에서만 해당하는 말이다. C/C++에서는 Garbage Collector가 존재하지 않는다. 때문에 동적으로 할당(malloc)된 변수들을 프로그래머가 직접 해제(free)해 주어야 한다.
  >
  > 반면, 자바와 자바스크립트 같은 언어에서는(물론, 자바는 컴파일 언어, 자바스크립트는 스크립트 언어라는 점에서 결이 다르다.) Garbage Collector가 존재한다. 때문에 프로그래머가 직접 해제하지 않아도 가비지 컬렉터가 해제해야하는 대상을 알아서 찾아내고 해제한다.
  >
  > Garbage Collector => 동적으로 할당한 메모리 영역 중 사용하지 않는 영역을 탐지하여 해제하는 기능
  >
  > https://www.youtube.com/watch?v=vZRmCbl871I     => 가비지 컬렉터의 동작 방식은 이 유튜브 영상에서 정말 자세하게 설명하고 있다. 재미있고 좋은 영상이라고 생각한다.
  >
  > 

#### Key differences between Stack and Heap

| Parameter                   | Stack                                                | Heap                                     |
| --------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Basic                       | Memory is allocated in a contiguous(연속적인) block. | Memory is allocated in any random order. |
| Allocation and Deallocation | Automatic by complier instructions.                  | Manual by the programmer.                |
| Cost                        | Less                                                 | More                                     |
| Implementation              | Easy                                                 | Hard                                     |
| Access Time                 | Faster                                               | Slower                                   |
| Main Issue                  | Shortage of Memory                                   | Memory fragmentation                     |
| Locality of reference       | Excellent                                            | Adequate                                 |
| Flexibility                 | Fixed-size                                           | Resizing is possible                     |
| Data type structure         | Linear                                               | Hierarchical                             |



참고문헌)

https://www.guru99.com/stack-vs-heap.html

https://stackoverflow.com/questions/44359953/are-global-variables-in-c-stored-on-the-stack-heap-or-neither-of-them

>첫번째로 적어놓은 guru99 저 사이트 내용 때문에 애를 많이 먹었다. 저 사이트에서 "전역 변수들은 모두 힙 영역에 저장된다."라고 설명하고 있다. 하지만, 아래 스택오버플로우 질문을 보게 되면 전역 변수는 힙 영역이 아닌 (스택 영역, 힙 영역과는 별개의) 데이터라는 영역에 저장됨을 알려준다. 정확히 어떤 곳에 저장되는지 바로 아래에서 설명하겠다.(근데 또 읽어보니까 내가 잘못 해석한건가? 라는 생각도 든다.....)

---

힙영역에 저장되는 변수들은 모두 전역에서 접근이 가능한가??????

c언어 저장 모양 적어야해









### 🔴스크립트 언어 vs 컴파일 언어

> 파이썬과 같은 스크립트 언어를 사용하고 있는 상황에서 이 차이를 확실히 짚고 넘어갈 필요성이 있겠다라는 생각이 들었다. 단순히 파이썬은 느린 언어야! 라고 말하는 프로그래머보다는 어떠한 이유 때문에 느린 언어야! 라고 말할 수 있는 프로그래머가 돼야 한다.

