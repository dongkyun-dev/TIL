# 💫not defined와 undefined

---

```javascript
let a;

console.log(a);    // undefined
console.log(b);    // ReferenceError: b is not defined
```

해당 코드를 보면 쉽게 a의 출력 결과는 `undefined`, b의 출력 결과는 `not defined` 에러일 것이라고 예상할 수 있다.

그렇다면, 호이스팅이 직관적으로 발생되는 경우에는 어떨까?

```javascript
console.log(a);    // ReferenceError: a is not defined

let a;
```

호이스팅이 직관적으로 발생되는 경우에도 `not defined` 에러가 발생한 것을 확인 할 수 있다. 

다시 그렇다면, 우리는 첫번째 경우에서 `not defined` 에러가 발생한 상황과, 두번째 경우에서 `not defined` 에러가 발생한 상황이 동일하다고 말할 수 있을까?

호이스팅에 대해 정확히 이해하고 있는 자바스크립트 개발자라면 동일한 상황이 아니라는 것을 금방 눈치챌 것이다.

첫번째 경우의 `not defined` 에러는 선언조차 되지 않은 상황에서 발생한 에러이다.

이에 반해 두번째 경우의 `not defined` 에러는 선언은 되었지만 초기화는 되지 않은, TDZ(Temporal Dead Zone)에 빠진 상황에서 발생하는 에러이다.

**이 두 경우를 통해 우리는 `not defined` 에러는 선언이 되지 않았거나, 선언은 되었지만 초기화되지 못한 상황에서 발생하는 에러다라는 결론을 낼 수 있다.**

---

위의 결론을 그대로 가져와서 이번에는 배열을 만들어보자.

```javascript
new Array(3);    // (3) [empty * 3]
```

이 배열은 어떠한 상태라고 할 수 있을까?

new 연산자를 통해 배열의 생성자 함수를 호출했기 때문에 분명 선언은 되었다고 볼 수 있다. 하지만, 이 상태가 초기화된 상태라고 말할 수도 있을까?

크롬이 자동으로 넣어주는 저 `empty`라는 단어 때문에 헷갈릴 수 있다. 때문에 더 근본적인(?) 모양으로 바꿔서 파악해보자.

```javascript
new Array(3);    // { length: 3 }
```

아직도 헷갈릴 수 있다. 때문에 초기화된 상태의 모양을 파악해보자.

```javascript
Array.from(new Array(3));    // { 0: undefined, 1: undefined, 1: undefined, length: 3 }
```

초기화되어 있는 형태를 보게 되면 `new Array()` 생성자로만 만든 배열은 선언은 수행되었지만 초기화되지 않았다는 것을 직관적으로 파악할 수 있다. 

결국, `new Array()` 연산자를 통해 만든 배열은 초기화되지 않은 상태이고 이 때문에 원소값을 가지고 있지 않다.

때문에 가장 위 원시값의 경우처럼 `not defined`라는 명백한 에러를 뱉지는 않지만, `new Array()` 연산자를 통해 만든 배열은 순회해봐야 초기화되지 않았기 때문에 아무런 역할도 하지 못한다.

```javascript
new Array(3).map((_, idx) => idx);    // [empty * 3]

new Array(3).forEach((_, idx) => console.log(idx));    // 아무것도 출력되지 않음
```

**때문에 `new Array()` 연산자로 만든 배열에 대한 명백한 순회가 필요한 경우에는 반드시 해당 배열을 초기화 시켜주어야 한다. 그렇다면 N의 길이를 가지는 배열을 초기화시키기 위한 방법은 어떤게 있을까.**

1. `new Array(N).fill()`

> `fill()` 함수의 인자값으로 빈값을 주게 되면 배열 전체가 `undefined`로 초기화된다.

2. `Array.from(new Array(N))`

> ```javascript
> new Array(3)    // [empty * 3]
> 
> Array.from(new Array(3))    // [undefined, undefined, undefined];
> ```

3. `Array.from({ length: N })`

> 자바스크립트에서 배열은 객체이고, `new Array(N)`이 만드는 것은 실질적으로 `{ length: N }` 이라는 객체라는 사실을 이미 확인했다. 때문에 해당 코드로도 배열을 초기화 시킬 수 있다. 내 생각에 이 방법이 가장 좋지 않을까? 싶다.

---

## ⭕실제 활용

#### 1. 이차원 배열 만들기

이차원 배열을 만드는 법은 기본적으로 배열을 만들고, 만든 배열을 순회하면서 각 원소에 배열을 만들어 넣어주면 될 것이다.

결국 배열을 만들고 바로 순회해야 하는데 단순히 `new Array()`로만 배열을 만들어서는 순회할 수 없을 것이다. 때문에 `Array.from()`을 사용한다.

```javascript
Array.from(new Array(N), () => new Array(N));

Array.from({ length: N }, () => { length : N });
```

`Array.from()` 메서드의 첫번째 인자는 배열, 두번째 인자는 콜백map이다.

결국 위의 코드는 다음과 똑같은 결과를 도출하는 코드라고 할 수 있다,

```javascript
Array.from(new Array(N)).map( _ => new Array(N));

Array.from({ length: N }).map( _ => new Array(N));
```

2. #### 증가하는 배열 만들기

증가하는 배열 또한 만들고 바로 순회해야 하기 때문에 단순히 `new Array()`로는 불가능하다.

```javascript
new Array(N).fill().map((_, idx) => idx + 1);

Array.from( { length: N }).map((_, idx) => idx + 1);
```

때문에 어떻게든 초기화를 시켜준 이후에 순회를 진행해야 한다.

3. #### range함수 만들기

```
const range = (start, end) => Array.from({ length: end - start }, (_, i) => start + i);

console.log(range(1, 5)); // prints [1,2,3,4]
```

4. #### step으로 증가하는 range 함수 만들기(step 단계씩 증가하는 배열)

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/from