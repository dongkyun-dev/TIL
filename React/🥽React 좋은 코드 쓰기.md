# 🥽React 좋은 코드 쓰기

---

### 여러 개의 state 관리하기

```javascript
// bad
const [message, setMessage] = useState('');
const [username, setUsername] = useState('');

// good
const [form, setForm] = useState({
	username: '',
	message: '',
});
```

---

### 여러 개의 onChange 관리하기

```react
function foo() {
	const [form, setForm] = useState({
		username: '',
		message: '',
	});
	const onChange = e => {
		const nextForm = {
			...form,
			[e.target.name]: e.target.value,
		};
		setForm(nextForm);
	};
	
	return (
		<input type="text" name="username" placeholder="사용자명" value={username} onChange={onChange} />
		<input type="text" name="message" placeholder="메시지 입력" value={message} onChange={onChange} />
	)
}
```

---

### useCallback, useMemo

숫자, 문자열, 객체처럼 일반 값을 재사용하려면 useMemo를 사용하고, 함수를 재사용하려면 useCallback을 사용한다.

```react
import React, { useState, useMemo, useCallback } from 'react';

const getAverage = numbers => {
	~~~
}

function Average() {
	const [list, setList] = useState([]);
	const [number, setNumber] = useState('');
	
	// 컴포넌트가 처음 렌더링될 때만 함수 생성
	const onChange = useCallback(e => {
		setNumber(e.target.value);
	}, []);
	
	// number 혹은 list가 바뀌었을 때만 함수 생성
	const onInsert = useCallback(() => {
		const nextList = list.concat(parseInt(number));
		setList(nextList);
		setNumber('');
	}, [number, list])
	
	const avg = useMemo(() => getAverage(list), [list]);
	
	return (
		<>
		</>
	)
}
```

봐서 알겟지만 state에 종속적이지 않은 함수들은 컴포넌트 안에 선언할 필요가 없다. 또한, useCallback, useMemo의 대상은 항상 state가 된다.

---

### async/await 사용하기

```react
import React, { useState } from 'react';
import axios from 'axios';

function App() {
	const [data, setData] = useState(null);
	const onClick = async () => {
		try {
			const reasponse = await axios.get(URL);
			setData(response.data);
		}
		catch (e) {
			console.log(e);
		}
	};
	return (
		<>
			<button onClick={onClick}>불러오기</button>
			{data && <textarea rows={7} value={JSON.stringify(data, null, 2)} readOnly={true} />}
        </>
	);
};
```

이렇게 JSON.stringify에 인자로 null과 2를 주면 조금 더 JSON 객체스러운 모양으로 렌더링 된다.

---

### useCallback

```react
// App.js

import React, { useState, useCallback } from "react";
import List from "./List";

export default function App() {
  const [number, setNumber] = useState(1);
  const [dark, setDark] = useState(false);

  const getItems = () => {
    return [number, number + 1, number + 2];
  };

  const theme = {
    backgroundColor: dark ? "#333" : "#FFF",
    color: dark ? "#fff" : "#333",
  };

  return (
    <div style={theme}>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(parseInt(e.target.value))}
      />
      <button onClick={() => setDark((prev) => !prev)}>Toggle Theme</button>
      <List getItems={getItems}></List>
    </div>
  );
}
```

```react
// List.js

import React, { useEffect, useState } from "react";

export default function List({ getItems }) {
  const [items, setItems] = useState([]);

  useEffect(() => {
    setItems(getItems());
    console.log("Updating Items");
  }, [getItems]);

  return items.map((item) => <div key={item}>{item}</div>);
}
```

이 코드의 가장 큰 문제는 무엇일까?

바로 자식 컴포넌트로 넘어가는 getItems 함수에 있다. 부모 컴포넌트(App.js)에서 자식 컴포넌트(List.js)로 넘어가는 getItems 함수가 재정의되면 자식 컴포넌트는 리렌더링되게 된다.

그렇다면 getItems 함수는 어느 경우에 재정의될까. 이 또한 getItems 함수가 속한 부모 컴포넌트가 리렌더링되게 되면 함수가 재정의된다.

당연히 number의 값이 바뀌는 경우 재정의되는 것이 바람직하겠지만, dark의 값이 바뀌는 경우 재정의될 필요가 전혀 없다. 떄문에 이런 함수의 재정의를 막기 위해서는 useCallback hook을 이용할 필요가 있다.

```react
const getItems = useCallback(() => {
    return [number, number + 1, number + 2];
  }, [number]);
```

이렇게 함수를 재설정하면 number의 값이 바뀔 때만 getItems 함수가 재정의된다.

즉, 리렌더링과 밀접한 관련이 있는 state 값과 연동되어 있는 함수들에 대해서는 결국 모두 이렇게 useCallback으로 관리해줄 필요가 있는게 아닐까? 라는 생각이 든다.

---

### 자식 컴포넌트의 속성값에서 객체를 정의하지 말자

```react
function SelectFruit({ selectedFruit, onChange }) {
	// ...
	return (
		<div>
			<Select
				options={[
					{ name: "apple", price: 500 },
					{ name: "banana", price: 1000 },
					{ name: "orange", price: 1500 },
				]}
				selected={selectedFruit}
				onChange={onChange}
         	 />
		</div>	
	);
};
```

컴포넌트 내부에서 객체를 정의해서 자식 컴포넌트의 속성값으로 입력하면, 자식 컴포넌트는 객체의 내용이 변경되지 않았는데도 속성값이 변경되었다고 인식한다. 이 때문에 객체를 컴포넌트 밖에 정의해야 한다.

```react
function SelectFruit({ selectedFruit, onChange }) {
	// ...
	return (
		<div>
			<Select
				options={FRUITS}
				selected={selectedFruit}
				onChange={onChange}
             />
		</div>	
	);
};

const FRUITS = [
	{ name: "apple", price: 500 },
	{ name: "banana", price: 1000 },
	{ name: "orange", price: 1500 },
];
```

하지만 다른 상태값이나 속성값을 이용해서 계산되는 값은 상수 변수로 관리 할 수 없다. 다음은 과일의 가격 조건에 따라 과일 목록을 계산하는 코드이다.

```react
function SelectFruit({ selectedFruit, onChange }) {
	const [maxPrice, setMaxPrice] = useState(1000);
	// ...
	return (
		<div>
			<Select
				options={FRUITS.filter(item => item.price <= maxPrice)}
				selected={selectedFruit}
				onChange={onChange}
			/>
		</div>	
	);
};
```

이렇게  코드를 작성하면 매번 FRUITS 객체에 filter 함수가 실행되고 자식 컴포넌트도 리렌더링되어 버린다.

근데, 사실 maxPrice가 변하지 않은 경우에는 filter 함수가 재실행될 필요도 없고, 자식 컴포넌트가 리렌더링될 필요도 없다. 이럴 때 useMemo를 사용한다.

```react
function SelectFruit({ selectedFruit, onChange }) {
	const [maxPrice, setMaxPrice] = useState(1000);
	// ...
    const fruits = useMemo(() => FRUITS.filter(item => item.price <= maxPrice), [maxPrice]);
	return (
		<div>
			<Select
				options={fruits}
				selected={selectedFruit}
				onChange={onChange}
			/>
		</div>	
	);
};
```

useMemo에 대한 예제를 하나 더 봐보자.

```react
import React, { useState, useMemo } from "react";

export default function App() {
  const [number, setNumber] = useState(0);
  const [dark, setDark] = useState(false);
  const doubleNumber = slowFunction(number);
  const themeStyles = {
    backgroundColor: dark ? "black" : "white",
    color: dark ? "white" : "black",
  };

  return (
    <>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(parseInt(e.target.value))}
      />
      <button onClick={() => setDark((prevDark) => !prevDark)}>
        Change Theme!
      </button>
      <div style={themeStyles}>{doubleNumber}</div>
    </>
  );
}

function slowFunction(num) {
  console.log("Calling slow function");
  for (let i = 0; i <= 1e8; i++) {}
  return num * 2;
}

```

이 코드의 가장 큰 문제점은 리렌더링이 발생할 때마다 엄청나게 시간이 오래 걸리는 함수가 재호출되고 doubleNumber의 값이 갱신된다는 것이다. 근데, 사실 dark의 값이 바뀔 때는 이 부분이 재계산될 필요가 전혀 없다. 때문에 dark값이 바뀐 경우에는 그냥 이전 값을 가지고만 있게 만들면 되는데 이것이 useMemo이다. 수정된 코드는 다음과 같다.

```react
const doubleNumber = useMemo(() => {
	return slowFunction(number);
}, [number]);
```

또한, useMemo를 활용할 수 있는 부분은 하나가 더 있다. 자바스크립트에서 같은 값을 가지는 두 객체는, 같은 값이라 하더라도 참조값이 다르기 때문에 비교 연산자는 false를 반환한다.

```react
  const themeStyles = {
    backgroundColor: dark ? "black" : "white",
    color: dark ? "white" : "black",
  };
  
  const themeStyles2 = {
    backgroundColor: dark ? "black" : "white",
    color: dark ? "white" : "black",
  };
  
  console.log(themeStyles == themeStyles2)    // false
  console.log(themeStyles === themeStyles2)   // false
```

이 때문에 리액트에서 리렌더링될 때, 이 두 객체가 다르다 판단하고 객체를 새롭게 다시 선언하게 된다. 객체의 사이즈가 작으면 문제가 되지 않지만 객체의 규모가 클 경우 이는 성능에 악영향을 끼친다. 때문에 이 경우에도 이전 값을 유지하게 할 필요가 있고 이때에도 useMemo를 활용할 수 있다.

```react
  const themeStyles = useMemo(() => {
  	return {
  		backgroundColor: dark ? "black" : "white",
    	color: dark ? "white" : "black",
  	}
  }, [dark]);
```

이렇게해서 dark 값이 바뀔 때에만 themeStyles 객체가 재정의되도록 만들어야 한다.

---

### useState의 비동기 동작

상태값 변경은 기본적으로 배치로 처리된다. 이를 위해 비동기로 동작한다.

```javascript
const [count, setCount] = useState(0);

function onClick() {
	setCount(count + 1);
	setCount(count + 1);
}
```

`onClick` 함수가 동작하면 `count.value` 값이 두 번 증가하여 2가 되기를 기대하게 된다. 하지만, 실제로는 1이 된다.

이는 상태값 변경 함수(`setState`)가 비동기로 동작하기 때문이다. 리액트는 효율적으로 렌더링하기 위해 여러 개의 상태값 변경 요청을 배치로 처리한다. 따라서 `onClick` 함수가 호출되어도 상태값 변경 함수는 한 번만 동작한다.

리액트가 상태값 변경 함수를 동기로 처리하면 하나의 상태값 변경 함수가 호출될 때마다 화면을 다시 그리기 때문에 성능 이슈가 생길 수 있다. 만약 동기로 처리하지만 매번 화면을 다시 그리지 않는다면 UI 데이터와 화면 간의 불일치가 발생해서 혼란스러울 수 있다.

<해결방법>

상태값 변경 함수에서는 prev를 통해 이전 state를 가져오는 것이 가능하다.

```javascript
function onClick() {
	setCount(prev => prev + 1);
	setCount(prev => prev + 1);
}
```

이런 식으로 코드를 작성하면 상태값 변경 함수를 동기로 실행시킬 수 있다.

또한, 하나 기억해두어야 할 것은 상태값 변경 함수는 비동기로 처리되지만 그 순서가 보장된다.

---

### 상태값 변경이 배치로 처리되지 않는 경우도 있다!

리액트는 내부에서 관리하는 이벤트 처리 함수에 대해서만 상태값 변경 함수를 배치로 처리한다. 즉, 리액트 외부에서 관리되는 이벤트 처리 함수의 경우에는 상태값 변경이 배치로 처리되지 않는다.

```react
function MyComponent() {
	const [count, setCount] = useState(0);
	useEffect(() => {
		function onClick() {
			setCount(prev => prev + 1);
			setCount(prev => prev + 1);
		}
		window.addEventListener("click", onClick);
		return () => window.removeEventListener("click", onClick);
	}, []);
	console.log("render called");
}
```

해당 코드는 window 객체에 이벤트 처리 함수를 등록한다. 리액트 요소에 등록되지 않은 이벤트 처리 함수는 리액트 내부에서 관리되지 않는다. 이처럼 리액트 외부에 등록된 이벤트 처리 함수에서 상태값 변경 함수를 호출하면 배치로 처리되지 않는다. 따라서 화면을 한 번 클릭하면 로그가 두 번 출력되게 된다.



