# 👕airbnb 스타일 가이드에서 필요한 것만 요약하자

---

객체나 배열을 만들 때 생성자 함수를 쓰기 보다는 literal syntax를 써라

```javascript
// bad
const item = new Object();
const arr = new Array();

// good
const item = {}
const arr = []
```

---

```javascript
// bad
const obj = {
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  lukeSkywalker,
  episodeThree: 3,
  mayTheFourth: 4,
  anakinSkywalker,
};

// good
const obj = {
  lukeSkywalker,
  anakinSkywalker,
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  episodeThree: 3,
  mayTheFourth: 4,
};
```

축약 구문을 쓴 것과 쓰지 않은 것이 섞여 있다면, 쓴 것들을 위로 몰아서 관리하라.

---

```javascript
// bad
const bad = {
  'foo': 3,
  'bar': 4,
  'data-blah': 5,
};

// good
const good = {
  foo: 3,
  bar: 4,
  'data-blah': 5,
};
```

invalid한 identifier에만 single quote를 사용해라. 그래야 가독성이 높아지고, 많은 JS 엔진들에 의해 최적화될 수 있다.

valid한 식별자란 `[a-z], $, _` 로 시작하고, `[a-z], [0-9], $, _`와 같은 character들을 포함하고 있는 것이다. 때문에 -(하이푼)이 쓰인 식별자는 invalid하고 이들만 single quote 처리해주어야 한다.

---

```javascript
// bad
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 }); // { a: 1, b: 2, c: 3 }

// good
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 }; // { a: 1, b: 2, c: 3 }
```

코딩의 기술 책에서는 위의 방법을 소개해주었는데 아래에 방식이 더 깔끔하고 좋은 코드이다. 항상 스프레드 연산자를 잘 활용해야 한다.

```javascript
const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
```

배열이야 배열 메서드로 배열을 잘라내면 되지만, 객체는 그러한 메서드가 없다. 때문에 이런 식의 객체 구조 분해 할당을 잘 이용하면 보다 효과적으로 객체를 자를 수도 있다.

---

```javascript
const someStack = [];

// bad (direct assignment)
someStack[someStack.length] = 'abc';

// good (push)
someStack.push('abc')
```

어떤 책에서 direct assignment가 더 성능이 좋다고 해서 그렇게 써왔는데, 에어비엔비에서는 push를 쓸 것을 권장한다. 그래서 이 두 개의 코드를 벤치마크 돌려봤더니

![benchmark(push_direct_and_assignment)](../assets/img/benchmark(push_direct_and_assignment).jpg)

거의 성능 차이가 없다. 오히려 push가 조금 더 빠른 걸로 나온다.

찾아보니까 예전에는 direct assignment가 더 좋은 성능을 냈던게 맞는 것 같다. 하지만, 자바스크립트 엔진이 발전하면서 이에 대한 최적화가 이루어졌고 때문에 push와 direct assignment의 성능 차이가 발생하지 않는 것 같다.

두 개의 성능 차이가 없다면 당연히 훨씬 가독성이 좋은 push를 쓰는 것이 더 좋은 선택지일 것이다.

---

```javascript
const foo = document.querySelectorAll('.foo');

// good
const nodes = Array.from(foo);

// best
const nodes = [...foo];
```

이터러블 객체를 배열로 바꿀 때는 스프레드 연산을 사용하는 것이 가장 좋다.

---

```javascript
const arrLike = { 0: 'foo', 1: 'bar', 2: 'baz', length: 3 };

// bad
const arr = Array.prototype.slice.call(arrLike);

// good
const arr = Array.from(arrLike);
```

유사 배열을 배열로 바꿀 때는 `Array.from()`을 사용하는 것이 가장 좋다.

---

```javascript
// bad
const arr = [
  [0, 1], [2, 3], [4, 5],
];

const objectInArray = [{
  id: 1,
}, {
  id: 2,
}];

const numberInArray = [
  1, 2,
];

// good
const arr = [[0, 1], [2, 3], [4, 5]];

const objectInArray = [
  {
    id: 1,
  },
  {
    id: 2,
  },
];

const numberInArray = [
  1,
  2,
];
```

배열을 표현할 때 line을 깨지 않도록 주의하자.

---

```javascript
// bad
const name = "Capt. Janeway";

// bad - template literals should contain interpolation or newlines
const name = `Capt. Janeway`;

// good
const name = 'Capt. Janeway';
```

문자열을 표현할 때는 single quote를 사용하자.

---

문자열이 너무 길다고 해서 여러 줄로 나누어서 할당하는 것은 좋지 않다. 

---

```javascript
// bad
function foo(name, options, arguments) {
  // ...
}

// good
function foo(name, options, args) {
  // ...
}
```

파라미터의 이름으로 arguments를 써서는 안된다. 모든 함수는 arguments 객체를 이미 지니기 때문이다. 

---

```javascript
// bad
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments);
  return args.join('');
}

// good
function concatenateAll(...args) {
  return args.join('');
}
```

arguments 객체가 필요하다면 미리 스프레드 연산자로 받는 것이 좋다. 왜냐하면, arguments는 일반 배열이 아닌 유사 배열이기 때문이다.

---

```javascript
// bad
function f1(a) {
  a = 1;
  // ...
}

function f2(a) {
  if (!a) { a = 1; }
  // ...
}

// good
function f3(a) {
  const b = a || 1;
  // ...
}

function f4(a = 1) {
  // ...
}
```

`arguments` 객체는 기본적으로 재할당이 가능하다. 그렇다고 해서 재할당을 하는 것이 좋다는 이야기는 아니다. `arguments` 객체를 재할당하게 되면 특히 V8엔진에서 최적화 이슈가 발생한다.

---

8단원 Arrow function에서부터 다시 시작