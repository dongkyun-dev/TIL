## Execution Context

자바스크립트의 핵심 개념들인 Hoisting, Scope, Closure와 같은 개념들을 이해하기 위해서는 Execution Context와 Execution Stack에 대한 이해가 필요하다. 

자바스크립트 코드를 실행한다는 말은, 내부적으로 실행 컨텍스트가 동작하기 시작한다는 말과 같다.

실행 컨텍스트에는 3가지 종류가 존재한다.

- Global Execution Context(전역 실행 컨텍스트)

  > 가장 기본이 되는 실행 컨텍스트이다. 어떠한 함수에도 포함되지 않는 코드들이 전역 실행 컨텍스트에 포함된다. 전역 실행 컨텍스트는 2가지의 일을 한다. 첫째, 전역 객체(= window object)를 만든다. 둘째, 전역 객체를 `this`의 값으로 설정한다. 하나의 프로그램에서 전역 실행 컨텍스트는 단 한 개만이 존재할 수 있다.

- Functional Execution Context(함수형 실행 컨텍스트)

  > 함수가 호출 될 때마다, 각 함수에 대한 함수 실행 컨텍스트가 만들어진다. 각 함수는 자신만의 실행 컨텍스트를 가진다. 하지만, 이 실행 컨텍스트가 실제로 만들어지기 위해서는 해당 함수가 호출되어야 한다. 하나의 프로그램에서 함수 실행 컨텍스트는 여러개가 존재할 수 있다.

- Eval Function Execution Context

  > 사용하지 않는다. Eval function에 대해 궁금하다면 [이곳](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/eval)의 내용을 읽어보면 된다.

---

## Execution Stack(Call Stack)

실행 스택(다른 프로그래밍 언어들에서는 "call stack"이라 부른다.)은 LIFO(Last in, First out) 형태의 스택 구조이다. 코드가 실행되면서 만들어지는 모든 실행 컨텍스트들은 이 실행 스택에 쌓인다. 

자바스크립트 엔진이 스크립트 코드를 처음 만나게 되면, 전역 실행 컨텍스트를 만들고 이를 실행 스택에 푸시(push)한다. 이후에 자바스크립트 엔진이 함수 호출을 발견하게 되면, 새로운 함수 실행 컨텍스트를 만들고 이를 실행 스택에 푸시한다.

해당 함수의 실행이 끝나면, 자바스크립트 엔진은 이 함수에 대한 함수 실행 컨텍스트를 팝(pop)한다.

ES5까지는 이렇게 자동으로 생성되는 전역공간을 제외하면, 실행 컨텍스트를 구성하는 방법은 함수를 실행하는 것 뿐이었다.(Eval 실행 컨텍스트는 사실상 쓰이지 않기 떄문에)

하지만, ES6가 등장하게 되면서, 블록({})에 의해서도 실행 컨텍스트가 만들어지게 되었다.  

조금 더 쉬운 이해를 위해 코드로 보자.

```javascript
let a = 'Hello World!';
function first() {
  console.log('Inside first function');
  second();
  console.log('Again inside first function');
}
function second() {
  console.log('Inside second function');
}
first();
console.log('Inside Global Execution Context');
```

![Execution_Context](../assets/img/Execution_Context.png)

- 코드의 가장 처음에서 전역 실행 컨텍스트가 만들어지고 푸시된다.
- 자바스크립트 엔진이 `first()`를 보는 순간 first 함수에 대한 함수 실행 컨텍스트가 만들어지고 푸시된다.
- 이후 first 함수가 실행된다.
- first 함수 실행 도중, `second()`를 만난다. 자바스크립트 엔진은 바로 second 함수에 대한 함수 실행 컨텍스트를 만들고 푸시한다.
- 이후 second 함수가 실행된다.
- second 함수의 실행이 끝나고 second 함수에 대한 실행 컨텍스트는 팝된다.
- first 함수의 실행이 끝나고 first 함수에 대한 실행 컨텍스트는 팝된다.
- 해당 프로그램에 대한 실행이 끝나고 전역 실행 컨텍스트가 팝된다.

---

## 그렇다면 실행 컨텍스트는 어떻게 만들어지는걸까?

자바스크립트 엔진이 실행 컨텍스트를 만드는 과정은 크게 두 개의 phase로 구성된다. 첫번째는 **Creation Phase** 두번째는 **Execution Phase**이다.



### The Creation Phase

실행 컨텍스트는 실질적으로 creation phase동안 만들어진다. 

하나의 실행 컨텍스트가 가지는 구조는 다음 사진과 같다. (함수의 이름은 first라고 가정한다.)

![Execution_Context_Detail](../assets/img/Execution_Context_Detail.jpg)

- VariableEnvironment - 현재 컨텍스트 내의 식별자들에 대한 정보 + 외부 환경 정보. LexicalEnvironment의 스냅샷으로, 변경사항은 반영되지 않는다는 특징이 있다.
- LexicalEnvironment - 처음에는 VariableEnvironment와 같지만 변경 사항이 실시간으로 반영된다.
- ThisBinding - this 식별자가 바라봐야 할 대상 객체.

---

### VariableEnvironment와 LexicalEnvironment

실행 컨텍스트를 생성할 때 VariableEnvironment에 정보를 먼저 담은 다음, 이를 그대로 복사해서 LexicalEnvironment를 만들고, 이후에는 LexicalEnvironment를 주로 활용하게 된다.

때문에 초기화 과정 중에는 사실상 VariableEnvironment와 LexicalEnvironment가 완전히 동일하다.

이후 코드가 진행됨에 따라 LexicalEnvironment의 내부는 계속해서 변화하지만, VariableEnvironment는 초기의 값을 계속해서 유지한다.

간단히 말해서 VariableEnvironment는 최초의 LexicalEnvironment에 대한 스냅샷이다.

---

### VariableEnvironment와 LexicalEnvironment의 내부요소

- #### environmentRecord와 호이스팅

  environmentRecord에는 현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장된다.

  컨텍스트를 구성하는 함수에 지정된 매개변수 식별자, 선언한 함수가 있을 경우 그 함수 자체, var로 선언된 변수의 식별자 등이 식별자에 해당한다.

  컨텍스트 내부 전체를 처음부터 끝까지 쭉 훑어나가면서 이러한 식별자들을 순서대로 수집한다.

  변수 정보를 수집하는 과정을 모두 마쳤더라도, 아직 실행 컨텍스트가 관여할 코드들은 실행되기 전의 상태이다. 코드가 실행되기 전임에도 불구하고 자바스크립트 엔진은 이미 해당 환경에 속한 코드의 변수명들을 모두 알고 있게 되는 셈이다. (실행은 Creation Phase가 끝나고, Execution Phase가 되었을 때 시작한다.)

  그렇다면 엔진의 실제 동작 방식 대신에 '자바스크립트 엔진은 식별자들을 최상단으로 끌어올려놓은 다음 실제 코드를 실행한다.' 라고 생각해도 코드를 해석하는 데는 문제될 것이 전혀 없다.

  여기에서 '호이스팅'의 개념이 등장한다. 호이스팅을 한 줄로 정의하자면, '모든 선언을 최상단으로 끌어올린다.' 라고 말할 수 있다. 

  실제로 자바스크립트 엔진이 선언들을 끌어올리지는 않지만, 개념을 쉽게 이해하기 위해서 끌어올렸다고 가정하는 것이다.

  어떻게 변수를 수집해 environmentRecord에 저장하는지 설명하기 위해 하나의 예를 들겠다.

  

  ```javascript
  // 1. 원본 코드
  
  function a(x) {		  // 수집 대상 1(매개변수)
  	console.log(x)    // (1)
  	var x			 // 수집 대상 2(변수 선언)
  	console.log(x)	  // (2)
  	var x = 2         // 수집 대상 3(변수 선언)
  	console.log(x)    // (3)
  }
  
  a(1)
  ```

  ```javascript
  // 2. 매개변수를 변수 선언/할당과 같다고 간주해서 변환한 상태
  
  function a() {
  	var x = 1        // 수집 대상 1(매개변수 선언)
  	console.log(x)   // (1)
  	var x            // 수집 대상 2(변수 선언)
  	console.log(x)   // (2)
  	var x = 2        // 수집 대상 3(변수 선언)
  	console.log(x)   // (3)
  }
  
  a()
  ```

  ```javascript
  // 3. 호이스팅을 마친 상태                                          
  
  function a() {												
  	var x          // 수집 대상 1의 변수 선언 부분				  
  	var x          // 수집 대상 2의 변수 선언 부분
  	var x          // 수집 대상 3의 변수 선언 부분
  	
  	x = 1          // 수집 대상 1의 할당 부분
  	console.log(x) // (1)
  	console.log(x) // (2)
  	x = 2          // 수집 대상 3의 할당 부분
  	console.log(x) // (3)
  }
  
  a()
  ```

  이제 호이스팅이 끝났으니 Creation Phase가 끝났다. (원래는 Creation Phase에서 스코프 체인 수집 및 this 할당도 이루어져야 하지만 일단 이 과정들이 다 끝났다고 가정한다.) 이제 Execution Phase가 실행된다.

  - 첫번째 `var x`가 선언된다. x에 undefined가 할당된다.
  - 두번째, 세번째 `var x`는 이미 선언된 변수 x가 있으므로 무시한다.
  - x에 1을 할당한다.
  - x 값을 두 번 출력한다. (1), (2) 모두 1이 출력된다.
  - x에 2를 할당한다.
  - x 값을 한 번 출력한다. (3)은 2가 된다.
  - 실행이 끝났다. 해당 실행 컨텍스트는 실행 스택에서 팝된다.

  호이스팅 개념을 완벽하게 이해한다면 이렇게 값을 예측하는 일은 그리 어려운 일이 아니다.

  

  +++++책에 있는 다른 예제(var에 undefined가 할당되어 있는 상태에서 출력되는 예제, 함수 선언문 예제, 함수 표현식 예제, let const에 대한 예제)들 추가. let과 const 예제도 만들어서 추가. let과 const 도 사실은 호이스팅이 된다는 것을 보여줘야한다.

  

  그렇다면 왜 let과 const는 호이스팅이 됨에도 불구하고 선언되기 전에 출력하려하면 레퍼런스 에러가 나는걸까?

  이유:

  **Note —** As you might have noticed that the `let` and `const` defined variables do not have any value associated with them during the creation phase, but `var` defined variables are set to `undefined `.

  This is because, during the creation phase, the code is scanned for variable and function declarations, while the function declaration is stored in its entirety in the environment, the variables are initially set to `undefined `(in case of `var`) or remain uninitialized (in case of `let `and `const`).

  This is the reason why you can access `var `defined variables before they are declared (though `undefined`) but get a reference error when accessing `let` and `const` variables before they are declared.

  This is, what we call hoisting.

  **Note —** During the execution phase, if the JavaScript engine couldn’t find the value of `let` variable at the actual place it was declared in the source code, then it will assign it the value of `undefined`.

  

  그러면 let x; 했을 때 어떻게 x에 undefined가 들어있는건가??

  이거는 Execution Phase에서 실행 흐름이 해당 코드에 왔을 때 undefined로 할당되는 것이다. 

  

  결국 var은 Creation Phase에서 존재도 확인되고 undefined로 초기화도 된다.(함수 스코프인것과 연관이 있을듯.) 반면, let과 const는 Creation Phase에서 존재는 확인되지만, 값이 할당되지 않는다. 때문에 Execution Phase에서 값이 할당되기 전에 실행된다면 레퍼런스 에러가 난다. (이거는 예제 적을 때 호이스팅된 결과 코드에서 이미 에러가 날게 분명히 보일듯!!!)

  

  environmentRecord에 이렇게 현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장된다는 것을 확인했다. 그렇다면  VariableEnvironment의 environmentRecord와 LexicalEnvironment의 environmentRecord 중 어느 곳에 식별자 정보들이 저장되는걸까?

  ES6부터는 함수 선언과 let, const에 대한 내용들은 LexicalEnvironment의 environmentRecord에 저장된다.

  var에 관련된 내용들은 VariableEnvironment의 environmentRecord에 저장된다.

  

그 다음에 스코프 체인에 대한 이야기 추가

디스 바인딩 내용 추가

---

https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0

https://poiemaweb.com/js-execution-context

https://medium.com/korbit-engineering/let%EA%B3%BC-const%EB%8A%94-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85-%EB%90%A0%EA%B9%8C-72fcf2fac365

https://stackoverflow.com/questions/31219420/are-variables-declared-with-let-or-const-hoisted

이 스택오버플로에 연동되어있는 ES2016가서 내용을 더 확인해야 한다.