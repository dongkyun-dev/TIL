# ⚛️React 공식 문서 내용 정리

> React 기초를 공부하는 가장 좋은 방법은 누가 뭐라해도 공식문서를 읽는 것이다. 오랜만에 리액트 공부를 다시 하려다 보니 많이 까먹어서 다시 공식 문서를 보면서 정리, 학습한다.

---

`JSX`는 `JS`를 확장한 문법

`JSX`도 표현식이다. 컴파일이 끝나면, `JSX` 표현식이 정규 `JS` 함수로 호출이 되고 `JS` 객체로 인식하게 된다. 

`Babel`은 `JSX`를 `React.createElement()` 호출로 컴파일 한다.

`JSX`가 `Bable`을 통해 `JS`가 되고 이 `JS` 코드를 컴파일러가 실행시키는 구조인듯

`JSX` 코드가 어떤 `JS` 코드로 변환되는지는 바벨 공식 홈페이지의 `Try it out`을 통해 확인할 수 있다.

 ```javascript
/* @jsx createElement */

function Hello(props) {
  return <li className="item">{props.label}</li>;
}

function App() {
  return (
    <div>
      <h1>hello world</h1>
      <ul className="board" onClick={() => null}>
        <Hello label="Hello" />
        <Hello label="World" />
        <Hello label="React" />
      </ul>
    </div>
  );
}
 ```

```javascript
"use strict";

/* @jsx createElement */
function Hello(props) {
  return createElement("li", {
    className: "item"
  }, props.label);
}

function App() {
  return createElement("div", null, createElement("h1", null, "hello world"), createElement("ul", {
    className: "board",
    onClick: () => null
  }, createElement(Hello, {
    label: "Hello"
  }), createElement(Hello, {
    label: "World"
  }), createElement(Hello, {
    label: "React"
  })));
}
```

여러 `element`가 합쳐져서 하나의 `component`가 된다.

**React DOM은 해당 엘리먼트와 그 자식 엘리먼트를 이전의 엘리먼트와 비교하고 DOM을 원하는 상태로 만드는데 필요한 경우에만 DOM을 업데이트합니다.**

>가장 중요한 부분! 단순히 해당 엘리먼트의 변화만을 확인하는 것이 아니라 해당 엘리먼트의 자식 엘리먼트까지 모두 리액트는 확인한다.

`props`는 부모에서 자식 컴포넌트로 데이터를 보낼 때 사용하는 <u>**객체**</u>

컴포넌트의 이름은 항상 대문자로 시작해야 한다. 이는 리액트 팀에서 정한 규칙. 반드시 대문자로 적어야 하는 이유는 바벨을 통해 나오는 결과의 차이 때문

```javascript
function App() {
  return createElement("div", null, createElement("h1", null, "hello world"), createElement("ul", {
    className: "board",
    onClick: () => null
  }, createElement(Hello, {
    label: "Hello"
  }), createElement(Hello, {
    label: "World"
  }), createElement(Hello, {
    label: "React"
  })));
}
```



> 보다싶이 대문자로 시작하는 컴포넌트의 `createElement` 요소와 소문자로 시작하는 엘리먼트의 `createElement` 요소가 다름을 확인할 수 있다.(옛날에 이걸로 몇시간을 삽질해봐서 아직도 정확히 기억한다.)

`props`의 이름은 사용될 `context`가 아닌 컴포넌트 자체의 관점에서 짓는 것을 권장합니다. 

> 즉, `props`를 보내는 부모 컴포넌트에서의 기준이 아닌 `props`를 받아서 사용하는 자식 컴포넌트를 기준으로 `props` 객체 속 요소들의 네이밍을 결정하는 것이 좋다.

 UI 일부가 여러 번 사용되거나 (`Button`, `Panel`, `Avatar`), UI 일부가 자체적으로 복잡한 (`App`, `FeedStory`, `Comment`) 경우에는 별도의 컴포넌트로 만드는 게 좋습니다.

> 그냥 간단하게 말해서 최대한 잘게 잘게 컴포넌트를 쪼개는 노력이 필요하지 않을까 싶다. 최대한 코드 재사용성을 높이기 위해서!

자신의 입력값을 바꾸지 않는 함수 => 순수 함수

```javascript
function sum(a, b) {
  return a + b;
}
//=> 순수함수 O

function withdraw(account, amount) {
  account.total -= amount;
}
//=> 순수함수 X
```

onClick 함수 호출시 명시적으로 `preventDefault` 함수를 호출해야 한다.

> 원래 form에서 submit이 발생하면 페이지를 다시 불러오게 되는데, 그렇게 되면 우리가 지니고 있는 상태들을 모두 잃어버리게 되므로 ``preventDefault` ` 함수를 통해 방지한다.(by. [velopert님의 글](https://velopert.com/3634))

<논리 && 연산자로 If를 인라인으로 표현하기>

```javascript
 {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
 }
```

JavaScript에서 `true && expression`은 항상 `expression`으로 평가되고 `false && expression`은 항상 `false`로 평가됩니다.

따라서 `&&` 뒤의 엘리먼트는 조건이 `true`일때 출력이 됩니다. 조건이 `false`라면 React는 무시합니다.

false로 평가될 수 있는 표현식을 반환하면 `&&` 뒤에 있는 표현식은 건너뛰지만 false로 평가될 수 있는 표현식이 반환된다는 것에 주의해주세요. 아래 예시에서, `<div>0</div>`이 render 메서드에서 반환됩니다.

```javascript
const count = 0
{count && <h1>MEssages: {count}</h1>}
```

> 이런 경우에는 count의 0이 반환된다는걸 기억하고 있어야 한다.

<조건부 연산자로 If-Else구문 인라인으로 표현하기>

```javascript
{isLoggedIn ? 'currently' : 'not'}
```

제일 중요한 것은!!!!! 인라인으로 하던, `if, if-else`로 하던 상관없다는 것이다. 상황에 따라 더 가독성이 높은 것을 선택하면 된다. 

`map`을 통해 리스트를 사용할 때 반드시 key값 설정이 필요하다. key는 리액트가 어떤 항목을 변경, 추가, 삭제할지 식별하는 것을 돕는다. 

key는 unique해야하며, 보통 데이터의 `ID`를 키로 많이 사용한다.

`ID`가 없다면 최후의 수단으로, 인덱스를 key로 사용할 수 있다. 하지만, 이 방식은 권장하는 방식은 아니다. 성능이 저하되거나 컴포넌트의 `state`와 관련된 문제를 일으킬 수도 있기 때문이다. 

> 만약 리스트 항목에 명시적으로 key를 지정하지 않으면 React는 기본적으로 인덱스를 key로 사용한다고 한다. 이건 처음 알았네.

경험상 `map()` 함수 내부에 있는 엘리먼트에 key를 넣어 주는 게 좋습니다

리액트 공식 문서에서 `map()` 함수 사용 예제 2가지를 보여준다.

```javascript
(1)
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <ListItem key={number.toString()}
              value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

(2)
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```

> 얼핏 보기에 2번의 가독성이 더 좋은듯하고 나 또한 2번과 같은 방법만을 사용해왔다. 하지만, 공식문서의 정확한 설명은 다음과 같다.
>
> `이 방식을 사용하면 코드가 더 깔끔해 지지만, 이 방식을 남발하는 것은 좋지 않습니다. JavaScript와 마찬가지로 가독성을 위해 변수로 추출해야 할지 아니면 인라인으로 넣을지는 개발자가 직접 판단해야 합니다. `map()` 함수가 너무 중첩된다면 [컴포넌트로 추출](https://ko.reactjs.org/docs/components-and-props.html#extracting-components) 하는 것이 좋습니다.`

React의 핵심은 결국 state! 지금까지 class형 컴포넌트만을 사용했던 이유도 함수는 state를 가질 수 없었기 때문이다. 하지만, 이런 함수의 한계를 극복해준 존재가 바로 Hook. 함수형 컴포넌트도 Hook과 함께라면 state를 가질 수 있다.

속성값 => `props`를 통해 부모 컴포넌트로 부터 받아오는 값

상태값 => `useState`를 통해 내 자신 안에서 관리하는 값