# 👏jest를 사용한 TDD 연습

>자바스크립트는 최근 몇 년간 비약적인 발전을 통해 사용 범위를 넓혀오고 있으며, 프론트엔드 환경에서 요구하는 애플리케이션의 수준도 나날이 복잡해지고 있다. 이와 더불어 자바스크립트의 테스트 환경도 짧은 기간 동안 많은 변화를 겪었는데, 특히 [Node.js](https://nodejs.org/en/)의 등장 이후 무수히 많은 도구가 쏟아져 나오며 빠른 속도로 발전해오고 있다.
>
>프론트엔드 코드는 사용자에 따라 다양한 환경(브라우저, 기기, 운영체제 등)에서 실행되기 때문에 테스트할 때 많은 변수들을 고려해야 한다. 이 때문에 자바스크립트는 테스트를 위한 환경과 테스트 도구들을 얼마나 잘 이해하고 있는지가 중요한 요소이며, 프로젝트의 성격에 맞게 테스트의 범위나 테스트 대상을 결정하는 등의 전략을 세울 때에도 많은 노하우가 필요하다.
>
>https://ui.toast.com/fe-guide/ko_TEST
>
>해당 블로그에 왜 자바스크립트에서의 TDD가 중요한지와 여러가지 테스트 도구들에 대한 사용법들을 설명해주고 있다.
>
>그중에서도 가장 점유율이 높은 jest를 학습해보려 한다.
>
>https://www.youtube.com/channel/UCxft4RZ8lrK_BdPNz8NOP7Q
>
>유튜브 코딩앙마 님의 강의를 참고하였습니다. 정말 좋은 강의 감사합니다.

---

### 1. 프로젝트 폴더를 만들고 기본 설정을 수행한다

`$yarn init`    // `package.json` 생성

`$yarn add --dev jest`    // jest 설치

```javascript
"scripts": {
    "test": "jest"    // jest 실행을 위한 명령어 수정
},
```



---

### 2. 원하는 자바스크립트 파일을 생성한다.

`yarn test`명령어를 통해 jest를 실행하게 되면 프로젝트 내의 모든 테스트 파일들이 실행된다. 테스트 파일들이란, `***.test.js`와 같이 중간에 test가 명시되어 있는 파일 혹은 `__test__` 폴더 속에 있는 파일들을 의미한다.

```javascript
// fn.js를 파일명으로 설정하겠다.

const fn = {
  add: (num1, num2) => num1 + num2,
};

module.exports = fn;
```

```javascript
// 테스트 수행을 위한 파일
// fn.test.js

const fn = require("./fn");

test("1은 1이야", () => {
  expect(1).toBe(1);
});

test("2 더하기 3은 5야", () => {
  expect(fn.add(2, 3)).toBe(5);
});

test("3 더하기 3은 5야", () => {
  expect(fn.add(3, 3)).not.toBe(5);
});
```

![jest_basic_test](../assets/img/jest_basic_test.jpg)

다음과 같이 테스트가 잘 수행되었음을 확인할 수 있다.

test의 첫번째 인자는 해당 테스트에 대한 설명, 두번째 인자는 실제 테스트를 위한 콜백 함수가 들어간다. 다른 개발자가 봤을 때 이름만 보고 해당 테스트가 어떠한 테스트를 수행하는지 알 수 있도록 자세하게 표현해놓을 필요가 있다.

---

### 기본형과 참조형

객체를 반환하는 함수를 추가한다.

```javascript
const fn = {
  add: (num1, num2) => num1 + num2,
  makeUser: (name, age) => ({ name, age }),
};

module.exports = fn;
```

```javascript
const fn = require("./fn");

test("이름과 나이를 전달받아서 객체를 반환해줘", () => {
  expect(fn.makeUser("Mike", 30)).toBe({
    name: "Mike",
    age: 30,
  });
});

test("이름과 나이를 전달받아서 객체를 반환해줘", () => {
  expect(fn.makeUser("Mike", 30)).toEqual({
    name: "Mike",
    age: 30,
  });
});
```

이런 식으로 테스트해보면 결과는 어떨까?

![primitive_jest](../assets/img/primitive_jest.jpg)

다음과 같이 `toBe`를 사용한 테스트는 실패하고, `toEqual`을 사용한 테스트는 성공하는 것을 확인할 수 있다.

그 이유는 테스트를 통해 검사하는 값이 이번에는 기본형이 아닌 참조형 즉 객체이기 때문이다.

하지만 에러 메시지를 보게되면 `If it should pass with deep equality, replace "toBe" with "toStrictEqual"` 즉, `toEqual`도 아닌 `toStrictEqual`을 쓰라고 명시되어 있다.

---

### toEqual과 toStrictEqual

기존 `makeUser` 함수에 인자값을 추가하는데 default 값으로 `undefined`를 준다.

```javascript
const fn = {
  add: (num1, num2) => num1 + num2,
  makeUser: (name, age) => ({ name, age, gender: undefined }),
};

module.exports = fn;
```

```javascript
test("이름과 나이를 전달받아서 객체를 반환해줘", () => {
  expect(fn.makeUser("Mike", 30)).toEqual({
    name: "Mike",
    age: 30,
  });
});

test("이름과 나이를 전달받아서 객체를 반환해줘", () => {
  expect(fn.makeUser("Mike", 30)).toStrictEqual({
    name: "Mike",
    age: 30,
  });
});

test("이름과 나이를 전달받아서 객체를 반환해줘", () => {
  expect(fn.makeUser("Mike", 30)).toStrictEqual({
    name: "Mike",
    age: 30,
    gender: undefined,
  });
});
```

![toStrictEqual](../assets/img/toStrictEqual.jpg)

해당 결과는 다음과 같다. 즉, `toStrictEqual`이 조금 더 엄격한 검사를 수행하게 되는 메서드이다.

개발자의 입장에서 봤을 때 `toStrictEqual`을 사용했을 때 나오는 결과물이 실제로 기대했던 결과물일 것이다. 때문에 `toEqual` 보다는 `toStrictEqual`을 사용하는 것이 더 좋은 선택지가 될 것이다.

---

### 기본값을 체크하는 다른 메서드들

| 메서드        | 의미                     |
| ------------- | ------------------------ |
| toBeNull      | null의 유무만 확인       |
| toBeUndefined | undefined의 유무만 확인  |
| toBeDefined   | toBeUndefined의 반대     |
| toBeTruthy    | Boolean의 true인지 확인  |
| toBeFalsy     | Boolean의 false인지 확인 |

```javascript
test("null은 null입니다", () => {
	expect(null).toBeNull();
});

test("비어있지 않은 문자열은 true입니다", () => {
	expect(fn.add("hello", "world")).toBeTruthy();
})
```

