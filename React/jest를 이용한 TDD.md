

테스트의 종류

- Unit Tests
- Integration Tests
- End to End Tests

보통 딱 하나의 컴포넌트를 대상으로 테스팅하게 되면 Unit Tests, 여러 개의 컴포넌트 간의 상호작용에 대해 테스팅하게 되면 Integration Tests라고 한다.

```javascript
  "@testing-library/jest-dom": "^5.11.4",
  "@testing-library/react": "^11.1.0",
  "@testing-library/user-event": "^12.1.10",
```

리액트 테스팅을 위해 필요한 라이브러리

```javascript
  "scripts": {
    "test": "react-scripts test",
  },
```

테스팅 시작 명령어

test 혹은 it 명령어를 통해 테스트 구문을 작성할 수 있다.

---

### 테스트 블록

- Render a component we to test
- Find elements we want to interact with
- Interact with those elements
- Assert that the results are as expected

```react
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders learn react link", () => {
  render(<App />);  // Render a component we do test
  const linkElement = screen.getByText(/learn react/i); // Find elements we want to interact with
  
  // Interact with those elements (this tutorial doesn't have this part.)

  expect(linkElement).toBeInTheDocument();  // Assert that the results are as expected
});
```

learn react라는 텍스트가 렌더링 되는지 확인하는 테스트 코드

---

### `AllBy*`와 `By*`

`findAllBy*`는 *에 해당하는 값들을 모두 가져오게 된다.

`findBy*`은 *에 해당하는 값을 딱 하나 가져오게 된다.

이와 비슷하게  `getAllBy*`와 `getBy*`, `queryAllBy*`와 `queryBy*`또한 같은 차이점을 가진다.

|          | getBy  | findBy | queryBy | getAllBy | findAllBy | queryAllBy |
| -------- | ------ | ------ | ------- | -------- | --------- | ---------- |
| No Match | error  | error  | null    | error    | error     | array      |
| 1 Match  | return | return | return  | array    | array     | array      |
| 1+ Match | error  | error  | error   | array    | array     | array      |
| Await    | no     | yes    | no      | no       | yes       | no         |

---

### Priority

`AllBy*` 혹은 `By*` 뒤에 붙는 요소들이 여러가지가 존재하는데 이들의 우선순위는 다음과 같다.

![priority](../assets/img/priority.jpg)

---

### 실제 유닛 테스팅

![header](../assets/img/header.jpg)

헤더에 대한 테스트를 진행한다고 가정한다. 그렇다면 헤더의 폴더 구조는 다음과 비슷하게 될 것이다.

```react
// Header.js

export default function Header({ title }) {
	return <h1>{title}</h1>
}
```

```react
// Header.test.js

import { render, screen } from '@testing-library/react';
import Header from '../Header';

it('should render same text passed into title prop', async () => {
	render(<Header title="My Header" />);
	const headingElement = screen.getByText(/my header/i);
	expect(headingElement).toBeInTheDocument();
});    // success

it('should render same text passed into title prop', async () => {
	render(<Header title="My Header" />);
	const headingElement = screen.getByRole("heading");
	expect(headingElement).toBeInTheDocument();
});    // success
```

`h1` 태그가 존재하기 때문에 두번째 테스트 코드 또한 통과한다.

테스트 코드 상에서는 단순히 h, p가 아닌 heading, paragraph라는 것을 잘 기억해둘 필요가 있겠다.

 만약 `Header.js`가 다음과 같이 변경되었다면 어떨까

```react
// Header.js

export default function Header({ title }) {
	return (
        <>
        	<h1>{title}</h1>
        	<h3>Cats</h3>
        </>
    )
}
```

이 경우 위의 테스트 코드는 에러가 난다. 왜냐하면 `getByRole`의 경우 1개를 초과하는 매칭이 일어나면 에러가 나게 되어있기 때문이다. 이럴 때 특정 요소의 전부에 대한 테스트가 아닌 특정 요소의 특정한 케이스에만 테스트를 하고 싶은 경우가 존재할 것이다. 예를 들면 text 값이 "My Header"인 heading만 찾는 예제처럼 말이다. 해당 경우 2번째 인자로 특정한 값을 지정해 주면 그것만 가져올 수 있게 된다.

```react
// Header.test.js

import { render, screen } from '@testing-library/react';
import Header from '../Header';

it('should render same text passed into title prop', async () => {
	render(<Header title="My Header" />);
	const headingElement = screen.getByText(/my header/i);
	expect(headingElement).toBeInTheDocument();
});    // success

it('should render same text passed into title prop', async () => {
	render(<Header title="My Header" />);
	const headingElement = screen.getByRole("heading", { name: "My Header" });
	expect(headingElement).toBeInTheDocument();
});    // success
```

특정한 요소를 고를 때 단순히 텍스트만 가능한 것은 아니다. 굉장히 많은 경우들이 존재하지만, title을 사용하는 예제 하나를 살펴본다.

```react
// Header.js

export default function Header({ title }) {
	return (
        <>
        	<h1>{title}</h1>
        	<h3 title="Header">Cats</h3>
        </>
    )
}
```

여기에서 title이 Header인 값을 테스트하기 위해서는 어떻게해야 할까.

```react
it('should render some text passed into title prop', async () => {
	render(<Header title="My Header" />);
	const headingElement = screen.getByTitle("Header");
	expect(headingElement).toBeInTheDocument();
})
```

다음과 같은 코드로 테스팅이 가능하다.

testId를 통해서도 이런 테스팅이 가능하다. 해당 부분의 코드도 확인해본다.

```react
// Header.js

export default function Header({ title }) {
	return (
        <>
        	<h1 className="header" data-testid="header-1">{title}</h1>
        	<h3 title="Header">Cats</h3>
        </>
    )
}
```

```react
it('should render some text passed into title prop', async () => {
	render(<Header title="My Header" />);
	const headingElement = screen.getByTest("header-1");
	expect(headingElement).toBeInTheDocument();
})
```

---

### findBy*

```react
it('should render same text passed into title prop', async () => {
	render(<Header title="My Header" />);
	const headingElement = await screen.findByText(/my header/i);
	expect(headingElement).toBeInTheDocument();
});
```

`findBy*` 메서드는 await과 함께 써야 한다는 특징을 가진다.

---

queryBy*

```react
it('should render same text passed into title prop', async () => {
	render(<Header title="My Header" />);
	const headingElement = screen.getByText(/dog/i);
	expect(headingElement).toBeInTheDocument();
});
```

해당 코드는 당연히 에러를 테스팅 실패를 뱉게 된다. 왜냐하면 그 어떤 곳에서도 dog라는 단어를 찾을 수 없기 때문이다. 그렇다면 에러가 나는 시점은 어디일까? 바로 3번째 줄이다.

```react
it('should render same text passed into title prop', async () => {
	render(<Header title="My Header" />);
	const headingElement = screen.getByText(/dog/i);
	expect(headingElement).not.toBeInTheDocument();
});
```

3번째 줄에서 테스팅이 실패한다는 것을 이 테스팅 코드를 통해 정확하게 파악할 수 있다. 얼핏 봤을 때 해당 코드는 성공해야할 것 같지만 실패하게 된다. 그 이유는 4번째 행이 실행되기도 전에 3번째 행에서 실패하고 그곳에서 테스팅이 끝나기 때문이다.

때문에 실패하더라도 테스팅을 더 진행시켜서 원래의 개발자 의도와 맞게 실행시키기 위해서 `queryBy*`를 사용하게 된다.

```react
it('should render same text passed into title prop', async () => {
	render(<Header title="My Header" />);
	const headingElement = screen.queryByText(/dog/i);
	expect(headingElement).not.toBeInTheDocument();
});    // success
```

---

### Assertions

```react
export default function TodoFooter({ numberOfIncompleteTasks }) {
    return (
    	<div className="todo-footer">
        	<p>{numberOfIncompleteTasks} {numberOfIncompleteTasks === 1 ? "task" : "tasks"} left</p>
            <Link to="/followers">Followers</Link>
        </div>
    )
}
```

```react
import { render, screen } from "@testing-library/react";
import TodoFooter from "../TodoFooter";

it("should render the correct amount of incomplete tasks", () => {
  render(<TodoFooter numberOfIncompleteTasks={5} />);
  const paragraphElement = screen.getByText(/5 tasks left/i);
  expect(paragraphElement).toBeInTheDocument();
});
```

해당 테스트 코드는 전혀 문제가 없어 보이지만 실제로는 실패하게 된다.

그 이유는 에러 메시지에서 정확하게 파악할 수 있다.

`Invariant failed: You should not use <Link> outside a <Router>`

결국 테스팅을 위해 작성된 코드에서는 `<BrowserRouter>`에 의해 감싸지지 않기 때문이다. 

때문에 우리가 임의로 mock 함수를 만들고 이를 라우터로 감싸주어야 에러가 나지 않는다.

```react
import { render, screen } from "@testing-library/react";
import TodoFooter from "../TodoFooter";
import { BrowserRouter as Router } from "react-router-dom";

const MockTodoFooter = ({ numberOfIncompleteTasks }) => {
  return (
    <Router>
      <TodoFooter numberOfIncompleteTasks={numberOfIncompleteTasks} />
    </Router>
  );
};

it("should render the correct amount of incomplete tasks", () => {
  render(<MockTodoFooter numberOfIncompleteTasks={5} />);
  const paragraphElement = screen.getByText(/5 tasks left/i);
  expect(paragraphElement).toBeInTheDocument();
});
```

위의 테스트코드를 이렇게 수정하면 기존의 에러가 해결된다.

또한, 해당 코드의 경우

`<p>{numberOfIncompleteTasks} {numberOfIncompleteTasks === 1 ? "task" : "tasks"} left</p>`

다음과 같은 조건부 연산자가 사용된다. 결국 이 말은 테스트가 2개가 필요하다는 이야기이다.

때문에 우리는 다음과 같은 2개의 테스트 코드를 작성해서 테스트 해볼 수 있을 것이다.

```react
import { render, screen } from "@testing-library/react";
import TodoFooter from "../TodoFooter";
import { BrowserRouter as Router } from "react-router-dom";

const MockTodoFooter = ({ numberOfIncompleteTasks }) => {
  return (
    <Router>
      <TodoFooter numberOfIncompleteTasks={numberOfIncompleteTasks} />
    </Router>
  );
};

it("should render the correct amount of incomplete tasks", () => {
  render(<MockTodoFooter numberOfIncompleteTasks={5} />);
  const paragraphElement = screen.getByText(/5 tasks left/i);
  expect(paragraphElement).toBeInTheDocument();
});

it("should render the task which amount is only one", () => {
  render(<MockTodoFooter numberOfIncompleteTasks={1} />);
  const paragraphElement = screen.getByText(/1 task left/i);
  expect(paragraphElement).toBeInTheDocument();
});
```

---

### toBeVisible

`expect(*Element)`. 이 뒤에 붙을 수 있는 메서드들은 굉장히 많다. 하나하나 설명하기에는 정말 방대한 양이기에 그때 그때 호버해서 어떠한 특징을 갖는지 파악하고 익숙해질 필요가 있다.

이 문단에서는 여러 메서드들 중에서도 `visible`에 대해서 알아본다. `visible`은 사용자에게 해당 element가 보여지는지 아닌지에 대해 테스팅해준다.

```react
export default function TodoFooter({ numberOfIncompleteTasks }) {
    return (
    	<div className="todo-footer">
        	<p>{numberOfIncompleteTasks} {numberOfIncompleteTasks === 1 ? "task" : "tasks"} left</p>
            <Link to="/followers">Followers</Link>
        </div>
    )
}
```

```react
it("should render the task which amount is only one", () => {
  render(<MockTodoFooter numberOfIncompleteTasks={1} />);
  const paragraphElement = screen.getByText(/1 task left/i);
  expect(paragraphElement).toBeVisible;
});
```

다음과 같이 `toBeVisible`을 사용해주면 된다. 해당 경우는 사용자에게 보이는 요소이기 때문에 테스트가 통과하게 된다.

하지만,  만약 `<p style={{ opacity: 0 }}>{numberOfIncompleteTasks} {numberOfIncompleteTasks === 1 ? "task" : "tasks"} left</p>` 

이런 식으로 opacity 값을 주어서 투명하게 만든다면 해당 테스트는 실패하게 된다.

---

### toContainHTML

요소에 특정한 HTML 태그가 포함되는지 테스트하는 메서드이다.

```react
export default function TodoFooter({ numberOfIncompleteTasks }) {
    return (
    	<div className="todo-footer">
        	<p>{numberOfIncompleteTasks} {numberOfIncompleteTasks === 1 ? "task" : "tasks"} left</p>
            <Link to="/followers">Followers</Link>
        </div>
    )
}
```

```react
it("exercise toContainHTML", () => {
  render(<MockTodoFooter numberOfIncompleteTasks={1} />);
  const paragraphElement = screen.getByText(/1 task left/i);
  expect(paragraphElement).toContainHTML("p");
});
```

1 task left 라는 텍스트의 HTML element는 p tag이다. 때문에 해당 테스트는 성공한다.

하지만, 만약에 다음과 같이 테스트 코드를 작성했다고 가정해보자.

```react
it("exercise toContainHTML", () => {
  render(<MockTodoFooter numberOfIncompleteTasks={1} />);
  const paragraphElement = screen.getByText(/1 task left/i);
  expect(paragraphElement).toContainHTML("h3");
});
```

해당 텍스트에 h3 태그는 존재하지 않기 때문에 해당 테스트는 실패한다.

---

### toHaveTextContent

지금까지는 계속해서 `getByText`를 통해 엘리먼트를 가져와서 내용을 검사하는 경우가 없었지만, 만약 다른 메서드를 통해서 엘리먼트를 가져오게 된다면 내용을 검사해야 하는 경우가 생길 수 있다.

이때 사용할 수 있는 메서드가 `toHaveTextContent`이다.

```react
export default function TodoFooter({ numberOfIncompleteTasks }) {
    return (
    	<div className="todo-footer">
        	<p data-testid="para">{numberOfIncompleteTasks} {numberOfIncompleteTasks === 1 ? "task" : "tasks"} left</p>
            <Link to="/followers">Followers</Link>
        </div>
    )
}
```

```react
it("exercise toHaveTextContent", () => {
  render(<MockTodoFooter numberOfIncompleteTasks={1} />);
  const paragraphElement = screen.getByTestId("para");
  expect(paragraphElement).toHaveTextContent("1 task left");
});
```

몰론 `toBe`를 활용하여 다음과 같이 테스트 코드를 작성할 수도 있다.

```react
it("exercise toHaveTextContent", () => {
  render(<MockTodoFooter numberOfIncompleteTasks={1} />);
  const paragraphElement = screen.getByTestId("para");
  expect(paragraphElement.textContent).toBe("1 task left");
});
```

---

### describe를 통해 여러 테스트 코드를 그룹핑하기

![describe1](../assets/img/describe1.jpg)

![describe2](../assets/img/describe2.jpg)

두 개의 모양 중 하나로 하게 되는 경우가 많다.

기존에는 다음과 같은 모양으로 테스트 코드를 작성해왔다.

```react
it("", () => {

});

it("", () => {

});

it("", () => {

});
```

하지만 앞으로는 describe를 활용하여 다음과 같이 그룹핑 한다.

```react
describe("", () => {
	it("", () => {

	});

	it("", () => {

	});

	it("", () => {

	});
});
```

describe 안에 여러 개의 describe를 넣어서 두번째 사진처럼 그룹핑 하는 방법 또한 존재한다.

```react
describe("", () => {
	describe("", () => {
		it("", () => {

		});

		it("", () => {

		});
	});
	
	describe("", () => {
		it("", () => {

		});
	});
});
```

---

