# Styled Components 연습

---

`$yarn add styled-components`

---

### props 사용하기

```react
// Button.style.js
import styled from 'styled-components';

export const Button = styled.button`
	width: 200px;
	height: 50px;
	background-color: ${(props) => props.backgroundColor || 'blue'};
`;
```

```react
// app.js

import {Button} from './Components/Button.style';    // js 확장자는 생략 가능

export defautl function App() {
	return {
		<div className="App">
			<Button backgroundColor="red">Click this button</Button>
			<Button backgroundColor="blue">Click this button</Button>
		</div>
	}
}
```

styled component에서는 이렇게 css 코드 사이에 자바스크립트 코드가 사용이 가능하다. 문법 또한 백틱 사이에 `${}` 안에 자바스크립트 코드를 작성한다는 점이 동일해서 헷갈릴 일도 별로 없겠다. 항상 default 값을 넣어주는 것이 좋은 선택이겠다.

---

### event 발생

```react
// Button.style.js
import styled from 'styled-components';

export const Button = styled.button`
	width: 200px;
	height: 50px;
	background-color: ${(props) => props.backgroundColor};
	
	&:hover {
		background-color: colar;
	}
	
	&:active {
		background-color: black;
	}
`;
```

&: 연산자를 통해 이벤트를 지정할 수 있다. &: 연산자는 결국 this라고 생각하면 이해하기가 편하다.

hover 이벤트가 발생하면 해당 버튼의 배경이 colar 색깔로 변할 것이고, click(active) 이벤트가 발생하면 해당 버튼의 배경이 검정색으로 변할 것이다.

---

### 중첩 선택자

```react
// app.js

import {Button, ButtonLabel} from './Components/Button.style';

export defautl function App() {
	return {
		<div className="App">
			<Button backgroundColor="red">
        		<ButtonLabel>Click this button</ButtonLabel>
        	</Button>
		</div>
	};
};
```

```react
// Button.style.js
import styled from 'styled-components';

export const Button = styled.button`
	width: 200px;
	height: 50px;
	background-color: ${(props) => props.backgroundColor};
	
	&:hover {
		& label {
			color: green;
		}
	}
`;

export const ButtonLabel = styled.label`
	font-size: 25px;
	color: white;
`;
```

&: 연산자는 이벤트를 지정하는 거였다면 & 연산자는 중첩 선택자이다.

hover 이벤트가 발생하면 그 내부의 label 태그의 text color가 초록색으로 변하는 것이다.

---

### 내가 직접 만든 컴포넌트에 style 적용하기

```react
// 내가 직접 만든 button 컴포넌트 Button.js

import React from 'react';

export default function Button({ className, buttonLabel }) {
	return <button className={className}>{ buttonLabel }</button>;
}
```

```react
import styled from 'styled-components';
import Button from './Button';

export const StyledButton = styled(Button)`
	width: 200px;
	height: 50px;
	background-color: ${(props) => props.backgroundColor};
`;
```

```react
import { StyledButton} from './Components/Button.style';

export default function App() {
	return(
		<>
			<StyledButton buttonLabel="Click here" background-color="red"></StyledButton>
		</>
	)
}
```

---

### 전역 CSS 만들기

```react
import { createGlobalStyle } from 'styled-components';

export const GlobalStyles = createGlobalStyle`
	body {
		background-color: pink;
		margin: 0px;
		padding: 0px;
	}
`;
```

```react
import { StyledButton} from './Components/Button.style';
import { GlobalStyles } from './GlobalStyles.style';

export default function App() {
	return(
		<GlobalStyles>
			<StyledButton buttonLabel="Click here" background-color="red"></StyledButton>
		</GlobalStyles>
	)
}
```

---

### 미디어 쿼리

```react
const Box = styled.div`
	background: ${props => props.color || 'blue'};
	padding: 1rem;
	display: flex;
	/* 기본적으로 가로 크기 1024px에 가운데 정렬을 하고 가로 크기가 작아짐에 따라 크기를 줄이고 768px 미만이 되면 꽉 채운다. */
	width: 1024px;
	margin: 0 auto;
	@media (max-width: 1024px) {
		width: 768px;
	}
	@media (max-width: 768px) {
		width: 100%;
	}
`;
```

---

https://styled-components.com/docs/basics

더 자세한 설명과 예시들은 공식 문서에 잘 정리되어 있기 때문에 유의해서 볼 필요가 있겠다.

