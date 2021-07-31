# Framer

> 왜 framer를 사용하냐!?
>
> 굉장히 직관적이고 간단하다. 원래의 HTML 태그 앞에 motion만 붙여주면 될 뿐 아니라 내부의 속성값들도 실제 CSS에서 사용하는 속성값들과 이름이 같다. 

---

`yarn add framer-motion`

> framer 설치

framer에 쓰는 CSS 프로퍼티들은 모두 리액트 문법, JSX 문법을 따른다. 때문에 카멜케이스 사용이 필수이다.

---

### 처음 렌더링시 바로 animated

```html
<motion.h2 animate={{ fontSize: 50, color: 'red', x: 100, y: -100 }}>
	Welocme
</motion.h2>
```

이 바로 animated된다는 뜻이 폰트의 크기가 50, 색깔이 빨간색인 상태로 나온다는 것이 아니라 화면을 키면 기존 속성에서 바로 해당 속성(animate로 설정해놓은 값들)으로 변화하고, 해당 변화를 우리가 시각적으로 볼 수 있다는 의미이다.

기본적인 CSS 속성들은 모두 사용이 가능한 것 같고, 처음 보는 속성만 기록한다.

- x

  > x축으로 특정 px 만큼 이동한다. 기존 CSS 속성을 적용했을 때 위치하게 되는 x축의 위치를 0으로 한다.

- y

  > y축으로 특정 px 만큼 이동한다. 기존 CSS 속성을 적용했을 때 위치하게 되는 y축의 위치를 0으로 한다.

- rotateZ

  > 회전. ex) `rotateZ: 180` 이라면 180도 회전한다.

---

### 만약에 initial 속성을 준다면?

```html
<motion.div className="title"
	initial={{ y: -250 }}
	animate={{ y: -10 }}
>
	test
</motion.div>
```

렌더링시 y축으로 -250 픽셀에서 시작하고 바로 -10 픽셀의 지점으로 떨어진다. 즉, 렌더링 되자마자 initial에서 animate로의 전환이 바로 이루어진다.

---

### 조건부 연산에 의해 렌더링되는 요소

조건부 연산에 의해 렌더링되는 요소는 화면이 켜지자마자 렌더링 되지 않는다. 그렇다는 것은 특정한 이벤트가 발생하고, 그것이 조건을 바꾸어 조건부 연산에 의해 렌더링되는 요소가 만들어졌을 때, 그때 애니메이션이 적용된다.

```html
{pizza.base && (
	<motion.div className="next"
		initial={{ x: '-100vw' }}    // 이렇게 -100vw 값을 주면 화면 밖에서부터 들어오는 느낌을 줄 수 있다.
		animate={{ x: 0 }}
	>
	<button>Go</button>
	</motion.div>
)}
```

이전까지 봤던 예제에서 initial, animate 이 두 가지의 요소만을 설정하게 되면 바로 애니메이션이 실행되었다. 하지만, 이 경우는 조건부 연산에 의해 렌더링 되는 요소이므로 바로 실행되지 않는다. 기다렸다가 `pizza.base`가 true가 되는 순간 애니메이션이 실행된다.

initial, animate와 같은 속성 내부에 숫자를 적게 되면 default로 px이 된다. 만일 px이 아닌 단위를 넣어야 한다면 싱글 쿼트, 혹은 더블 쿼트로 감싸서 문자열임을 명시해야 한다.

---

### [transition 속성](https://www.framer.com/docs/transition/)

transition 속성에는 delay, duration과 같은 값을 설정할 수 있다.

```html
<motion.div
	initial={{ opacity: 0 }}
	animate={{ opacity: 1 }}
	transition={{ delay: 1.5, duration: 5 }}
>
	It's a test.
</motion.div>
```

- 화면이 렌더링된다.
- 1.5 초의 딜레이가 지나고 애니메이션이 시작된다.
- initial 부터 animate 로의 변화, 즉 opacity 값이 0에서 1로 5초에 걸쳐서 변하게 된다.

transition 속성

- delay

- duration

- type

  > spring(통통 튀기는 느낌)과 tween(딱 떨어지는 느낌) 같은 속성이 있다. 궁금하다면 공식문서를 보는게 좋을 것 같다.
  >
  > 디폴트 값은 spring이다.
  >
  > type이 spring일 때만 stiffness를 적용할 수 있다. 
  >
  > duration 값을 주게 되면 type은 자동으로 tween이 된다. 

- stiffness

  > 요동치는 정도를 줄 수 있다. (약간 탱탱볼 같이 튕기는 느낌) `ex) transition={{ stiffness: 120 }}` 상수값을 주게 되며, 큰 수를 줄 수록 좌우로 요동치는 정도가 더 심해진다.

```html
return (
	<motion.div
		initial={{ x: '100vw'}}
		animate={{ x: 0}}
		transition={{ type: 'spring', delay: 0.5 }}
	>
		// 컴포넌트의 내용
	</motion.div>
)
```

이렇게 컴포넌트 전체에 이런 모션을 주면 화면이 전환되고 해당 페이지가 되었을 떄 

페이지가 화면 밖 오른쪽에서부터 들어오고 들어와서는 잠깐 통통 튀기게 된다.

---

### 코드 분리하기

```html
import React from 'react';
import { Link } from 'react-router-dom';
import { motion } from 'framer-motion';

const Base = ({ addBase, pizza }) => {
  const bases = ['Classic', 'Thin & Crispy', 'Thick Crust'];

  return (
    <motion.div className="base container"
      initial={{ opacity: 0, x: '100vw' }}
      animate={{ opacity: 1, x: 0 }}
      transition={{ type: 'spring', delay: 0.5 }}
    >
```

딱봐도 html 부분에 motion에 대한 CSS 속성들이 들어가면서 크기가 비대해졌음을 볼 수 있다. 때문에 이러한 코드들을 밖으로 분리할 필요가 있다. 

```react
import React from 'react';
import { Link } from 'react-router-dom';
import { motion } from 'framer-motion';

const containerVariants = {
	hidden: {
		opacity: 0,
		x: '100vw'
	},
	visible: {
		opacity: 1,
		x: 0,
		transition: {         // transition이 animate 내부의 들어갈 수 있다.
			type: 'spring',
			delay: 0.5
		}
	}
}

const Base = ({ addBase, pizza }) => {
  const bases = ['Classic', 'Thin & Crispy', 'Thick Crust'];

  return (
    <motion.div className="base container"
      variants={containerVariants}
      initial="hidden"
      animate="visible"
    >
```

훨씬 깔끔해졌다. 또한 똑같은 모션을 적용하는 경우가 생길 수도 있는데 이때에도 중복된 코드를 작성할 필요가 없어진다.

---

### 부모가 먼저 실행되고 그 이후에 자식이 실행되게 만들고 싶을 때

```react
import React from 'react';
import { motion } from 'framer-motion';

const containerVariants = {
  hidden: { 
    opacity: 0, 
    x: '100vw',
    transition: {
      staggerChildren: 0.5,
    } 
  },
  visible: { 
    opacity: 1, 
    x: 0,
    transition: { 
      type: 'spring',
      staggerChildren: 0.4,
      when: "beforeChildren",
    }
  },
};

const childVariants = {
  hidden: {
    opacity: 0,
  },
  visible: {
    opacity: 1,
  }
}

const Order = ({ pizza }) => {
  return (
    <motion.div className="container order"
      variants={containerVariants}
      initial="hidden"
      animate="visible"
    >
      <h2>Thank you for your order :)</h2>
      <motion.p variants={childVariants}>You ordered a {pizza.base} pizza with:</motion.p>
      <motion.div variants={childVariants}>
        {pizza.toppings.map(topping => <div key={topping} >{topping}</div>)}
      </motion.div>
      
    </motion.div>
  )
}

export default Order;
```

가장 주의 깊게 봐야할 부분은 다음이다.

```react
transition: { 
      type: 'spring',
      staggerChildren: 0.4,
      when: "beforeChildren",
    }
```

현재 부모(containerVariants)는 두 개의 자식(childVariants)을 가지고 있는 모양이다. 

부모가 자식보다 먼저 실행되고 싶다면 `when: "beforeChildren"` 속성을 추가한다. 만약에, 자식이 부모보다 먼저 실행되어야 한다면 `when: "afterChildren"`을 추가하면 된다.

또한, 자식이 여러 개일 경우 각 자식 사이의 딜레이 시간을 지정해 줄 수 도 있다. ` staggerChildren: 0.4`

결국 해당 코드에 의해, 부모가 가장 먼저 렌더링 될 것이고, 이후에 첫번째 자식이 렌더링되고, 0.4초가 지난 후에 두번째 자식이 순서대로 렌더링 될 것 이다.

---

### 코드를 더 줄이기

```html
import React from 'react';
import { Link } from 'react-router-dom';
import { motion } from 'framer-motion';

const containerVariants = {
  hidden: { 
    opacity: 0, 
    x: '100vw' 
  },
  visible: { 
    opacity: 1, 
    x: 0,
    transition: { type: 'spring', delay: 0.5 }
  },
};

const nextVariants = {
  hidden: { 
    x: '-100vw' 
  },
  visible: {
    x: 0,
    transition: { type: 'spring', stiffness: 120 }
  } 
}

const Base = ({ addBase, pizza }) => {
  const bases = ['Classic', 'Thin & Crispy', 'Thick Crust'];

  return (
    <motion.div className="base container"
      variants={containerVariants}
      initial="hidden"
      animate="visible"
    >
      <h3>Step 1: Choose Your Base</h3>
      <ul>
        {bases.map(base => {
          let spanClass = pizza.base === base ? 'active' : '';
          return (
            <motion.li key={base} onClick={() => addBase(base)}
              whileHover={{ scale: 1.3, originX: 0, color: '#f8e112' }}
              transition={{ type: 'spring', stiffness: 300 }}
            >
              <span className={spanClass}>{ base }</span>
            </motion.li>
          )
        })}
      </ul>

      {pizza.base && (
        <motion.div className="next"
          variants={nextVariants}
        >
          <Link to="/toppings">
            <motion.button
               whileHover={{ 
                scale: 1.1, 
                textShadow: "0px 0px 8px rgb(255,255,255)",
                boxShadow: "0px 0px 8px rgb(255,255,255)",
              }}
            >
              Next
            </motion.button>
          </Link>
        </motion.div>
      )}
    </motion.div>
  )
}

export default Base;
```

강의의 예제 코드이다.

여기에서 볼 곳은 크게 두 부분이다.

```html
 <motion.div className="base container"
      variants={containerVariants}
      initial="hidden"
      animate="visible"
    >
    
    
{pizza.base && (
        <motion.div className="next"
          variants={nextVariants}
        >
```

바로 알 수 있듯이 아래에는 initial과 animate 속성이 존재하지 않는다. 하지만, 해당 코드는 제대로 동작한다. 이는 위의 코드가 아래 코드의 부모이고 속성들은 상속되기 때문이다.

결국 아래 코드는 상속에 의해 다음 코드와 같아지게 된다.

```html
{pizza.base && (
        <motion.div className="next"
          variants={nextVariants}
          initial="hidden"
      	  animate="visible"
        >
```

---

### whileHover를 쓰는 곳에서 코드 분리하기

```react
import React from 'react';
import { Link } from 'react-router-dom';
import { motion } from 'framer-motion';

const buttonVariants = {
  visible: {                          // 당연히 이 위에 initial을 줄 수도 있다.
  x: [0, -20, 20, -20, 20, 0],
  transition: { delay: 2 }
  },
  hover: {
    scale: [1, 1.1, 1, 1.1, 1, 1.1],
    textShadow: "0px 0px 8px rgb(255,255,255)",
    boxShadow: "0px 0px 8px rgb(255,255,255)",
    transition: {
      duration: 1.5
    }
  }
}

const Home = () => {
  return (
    <motion.div className="home container"
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ delay: 1.5, duration: 1.5 }}
    >
      <h2>Welcome to Pizza Joint</h2>
      <Link to="/base">
        <motion.button
          variants={buttonVariants}
          animate="visible"
          whileHover="hover"
        >
          Create Your Pizza
        </motion.button>
      </Link>
    </motion.div>
  )
}

export default Home;
```

---

### keyFrame이 너무 긴 배열일 때 줄이기

위의 예제에서 다음과 같은 코드를 확인했다.

```react
const buttonVariants = {
  visible: {                          // 당연히 이 위에 initial을 줄 수도 있다.
  x: [0, -20, 20, -20, 20, 0],
  transition: { delay: 2 }
  },
  hover: {
    scale: [1, 1.1, 1, 1.1, 1, 1.1],
    textShadow: "0px 0px 8px rgb(255,255,255)",
    boxShadow: "0px 0px 8px rgb(255,255,255)",
    transition: {
      duration: 1.5
    }
  }
}
```

이 코드에서 볼 수 있듯이 scale의 배열이 반복적임에도 불구하고 하나하나 입력하는 것은 귀찮은 일이다.

이때 yoyo 라는 속성을 사용할 수 있다.

```react
const buttonVariants = {
	...
	hover: {
    scale: 1.1,
    textShadow: "0px 0px 8px rgb(255,255,255)",
    boxShadow: "0px 0px 8px rgb(255,255,255)",
    transition: {
      duration: 1.5,
      yoyo: 10
    }
  }
}
```

이런 식으로 코드를 작성하면 결국 scale은 [1, 1.1, 1, 1.1, 1, 1.1, 1, 1.1, 1, 1.1] 이 된다.

무한히 커졌다 작아졌다는 반복해야 한다면 yoyo에 Infinity 값을 주어도 된다. 

---

### 다음과 같은 HTML 파일에 작았다가 커지는 애니메이션을 주고 싶다면 어떻게 해야 할까?

```html
<h1>It's a test</h1>
```

motion으로 감싸고 속성값들을 적절하게 넣어주면 된다.

```html
import { motion } from 'framer-motion';

    <motion.h1 initial="hidden" animate="visible" variants={{
        hidden: {
            scale: .8,
            opacity: 0
        },
        visible: {
            scale: 1,
            opacity: 1,
            transition: {
                delay: .4
			}
		},
	}}>
		It's a test
	</h1>
</motion.div>
```

코드가 굉장히 직관적이다.

해당 코드는 다음과 같은 기능을 한다.

1. initial(hidden)에서 시작해서 animate(visible)로 점점 변화해간다.
2. 이 변화 과정은 variants 내부에 정의한다.
3. 처음 시작 (해당 코드에서는 hidden으로 네이밍했다) 은 크기가 0.8에 투명도가 0이다.
4. 마지막 (해당 코드에서는 visible로 네이밍했다)은 크기가 1에 투명도가 1이다.
5. initial -> animate까지의 변화를 0.4초에 걸쳐서 보여줘라.

---

### 카드에 Hover 이벤트가 발생했을 때 크기가 커지는 애니메이션을 주고 싶다면 어떻게 해야 할까?

다음과 같은 템플릿한 카드 코드가 주어진다고 가정한다.

```html
<ul>
	{results.map(result => {
		const { id, name, image } = result;
		return (
			<li key={id}>
				<Link href="/">
					<a>
						<img>
						<h3>test</h3>
					</a>
				</Link>
			</li>
		)
	})}
</ul>
```

이 경우 또한 motion을 import하여 사용한다.

```html
import { motion } from 'framer-motion';

<ul>
	{results.map(result => {
		const { id, name, image } = result;
		return (
    		// li의 경우 반드시 키가 있어야 한다.
			<motion.li key={id} 
                  whileHover={{
				position: 'relative',
				zIndex: 1,
				background: 'white',
				scale: 1.2,
				transition: {
					duration: .2
				}
              >
                
				<Link href="/">
					<a>
						<img>
						<h3>test</h3>
					</a>
				</Link>
			</motion.li>
		)
	})}
</ul>
```

1. Hover 이벤트가 발생하면 1.2배 사이즈가 커지는데 그 동작이 0.2초 동안에 이루어진다는 것을 바로 알 수 있다.
2. 또한, framer의 가장 큰 특징은 중괄호 두 개 사이에 해당 코드들을 작성한다는 것인데, 이는 react에서 CSS를 인라인으로 적용할 때도 사용하는 방법이다. 즉, 아마도 framer에서 사용하는 코드들이 결국 CSS 속성으로 바뀌어서 적용되는게 아닐까 싶다. 때문에 일반적인 CSS 코드들도 중괄호 두 개 사이에 넣어서 적용시킬 수도 있다.

**Hover 이벤트가 발생했을 때 보통 scale 속성을 사용해서 사이즈를 키우는 작업을 많이 하게 된다. 이때 중요한 것이 scale이 가진 본래의 속성이다. scale 속성을 사용하게 되면 기본적으로 왼쪽으로 커지게 된다. 하지만, 보통 사람들은 이러한 이벤트가 발생했을 때 오른쪽으로 커지기를 바란다. 이를 위해서는 originX를 사용해주면 된다. `whileHover={{ scale: 1.3, originX: 0, color: #fff }}` 이런 식으로.**

---

### Next.js에서의 Page transition

```html
//__app.jsx

import { motion } from 'framer-motion'l

export default function App({ Component, pageProps, router }) {
	return (
		<motion.div key={router.route} initial="pageInitial" animate="pageAnimate" variants={{
			pageInitial: {
				opacity: 0
			},
			pageAnimate: {
				opacity: 1
			},
		}}>
			<Component {...pageProps} />
		</motion.div>
	)
}
```

---

### 카드에 scale 속성을 연속적으로 주고 싶다면? => keyFrame을 사용하고 싶다면?

특정한 요소가 쭉 커졋다가 다시 작아지는 애니메이션이 필요하다면 어떻게 해야 할까. 이 경우 scale 속성을 배열로 주면 된다. 위의 카드 예제로 다시 확인하겠다.

다음 코드는 Hover 이벤트가 발생했을 때 1.2배 크기가 커지게 된다.

```html
<ul>
	{results.map(result => {
		const { id, name, image } = result;
		return (
			<motion.li key={id} whileHover={{
				position: 'relative',
				zIndex: 1,
				background: 'white',
				scale: 1.2,
				transition: {
					duration: .2
				}
			}}>
				<Link href="/">
					<a>
						<img>
						<h3>test</h3>
					</a>
				</Link>
			</motion.li>
		)
	})}
</ul>
```

하지만 scale 속성에 배열을 준다면 

```html
<motion.li key={id} whileHover={{
				position: 'relative',
				zIndex: 1,
				background: 'white',
				scale: [1, 1.4, 1.2],
				transition: {
					duration: .2
				}
			}}>
```

이런 식으로 적용한다면 0.2초 동안 Hover 이벤트가 발생한 해당 카드는 1.4배로 커졌다가 1.2배로 줄어든다. 약간 바운스? 하는 느낌의 카드를 만들어 볼 수 있다.

또 다른 속성들이 존재하는데 이번에는 rotate 속성에 대해 알아본다. rotate 속성은 말 그래도 회전을 주는 속성이다.

```html
<motion.li key={id} whileHover={{
				position: 'relative',
				zIndex: 1,
				background: 'white',
				scale: [1, 1.4, 1.2],
				rotate: [0, 10, -10, 0],
				transition: {
					duration: .2
				}
			}}>
```

이런 식으로 rotate 속성에 배열을 주면 0.2초 동안 10도, -10도, 0도로 돌아오게 된다. 즉, 흔들리는 느낌을 줄 수 있다.

---

### Page transition에서 페이지에서 나올 때의 애니메이션

기본적으로 AnimatePresence의 exit 속성을 활용하면 모션이 끝날 때의 변화를 

```html
//__app.jsx

import { motion, AnimatePresence } from 'framer-motion'l

export default function App({ Component, pageProps, router }) {
	return (
		<AnimatePresence>
            <motion.div key={router.route} initial="pageInitial" animate="pageAnimate" exit="pageExit" variants={{
                pageInitial: {
                    opacity: 0
                },
                pageAnimate: {
                    opacity: 1
                },
                pageExit: {
                	backgroundColor: 'white',
                    opacity: 0
                }
            }}>
                <Component {...pageProps} />
            </motion.div>
		</AnimatePresence>
	)
}
```

---

### 모달 만들기

```css
// index.css

/* modal */
.backdrop{
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.5);
  z-index: 1;
}

.modal {
    max-width: 400px;
    margin: 0 auto;
    padding: 40px 20px;
    background: white;
    border-radius: 10px;
    text-align: center;
}

.modal p {
    color: #fff;
    font-weight: bold;
}
```

```react
// Modal.js

import React from 'react';
import { Link } from 'react-router-dom';
import { motion, AnimatePresence } from 'framer-motion';

const backdrop = {
  visible: { opacity: 1 },
  hidden: { opacity: 0 },
}

const modal = {
    hidden: {
        y: "-100vh",
        opacity: 0
    },
    visible: {
        y: "200px",
        opacity: 1,
        transition: { delay: 0.5 }
    }
}

const Modal = ({ showModal, setShowModal }) => {
  return (
    <AnimatePresence exitBeforeEnter>
      { showModal && (
        <motion.div className="backdrop"
          variants={backdrop}
          initial="hidden"
          animate="visible"
          exit="hidden"
        >
        	<motion.div className="modal"
            	variants={modal}
                initial="hidden"
            >
            	<p>It's your Pizza</p>
            </motion.div>
        </motion.div>
      )}
    </AnimatePresence>
  )
}

export default Modal;
```

```react
// 실제 모달이 나오게 되는 Order 컴포넌트

const Order = ({ pizza, setShowModal }) => {
 
  useEffect(() => {
    setTimeout(() => setShowModal(true), 5000);
  }, [setShowModal]);
```

```react
// App.js

import React, { useState } from 'react';
import { Route, Switch, useLocation } from "react-router-dom";
import Header from './components/Header';
import Home from './components/Home';
import Base from './components/Base';
import Toppings from './components/Toppings';
import Order from './components/Order';
import Modal from './components/Modal';
import { AnimatePresence } from 'framer-motion';

function App() {
  const location = useLocation();
  const [pizza, setPizza] = useState({ base: "", toppings: [] });
  const [showModal, setShowModal] = useState(false);

  const addBase = (base) => {
    setPizza({ ...pizza, base })
  }
  
  const addTopping = (topping) => {
    let newToppings;
    if(!pizza.toppings.includes(topping)){
      newToppings = [...pizza.toppings, topping];
    } else {
      newToppings = pizza.toppings.filter(item => item !== topping);
    }
    setPizza({ ...pizza, toppings: newToppings });
  }

  return (
    <>
      <Header />
      <Modal showModal={showModal} />
      <AnimatePresence exitBeforeEnter onExitComplete={() => setShowModal(false)}>
        <Switch location={location} key={location.key}>
          <Route path="/base">
            <Base addBase={addBase} pizza={pizza} />
          </Route>
          <Route path="/toppings">
            <Toppings addTopping={addTopping} pizza={pizza} />
          </Route>
          <Route path="/order">
            <Order pizza={pizza} setShowModal={setShowModal} />
          </Route>
          <Route path="/">
            <Home />
          </Route>
        </Switch>
      </AnimatePresence>
    </>
  );
}

export default App;
```

전체 페이지에 걸쳐서 나오는 모달이기 때문에 이곳에 모달을 두는듯???? 

---

### 로더

```react
import { motion } from 'framer-motion';

const Loader = () => {
	return (
		<>
			<motion.div className="loader">
        	 </motion.div>
		</>
	)
}
```



