# 😺바닐라 자바스크립트로 SPA 만들기

> 프로그래머스 야옹이 과제를 수행하면서 알게 된 점들을 정리한다.

---

### api call

먼저 기본적으로 api call을 하는 함수들은 모두 `/api/api.js`에 몰아서 정의하는 것이 좋은 방법이다.

```javascript
// 공통적으로 사용될 엔드포인트를 정의해둔다.
const API_ENDPOINT = "http://localhost:8000";

// 각각의 api call 함수들을 정의한다.
export const getID = async (id) => {
	try {
		const res = await fetch(`${API_ENDPOINT}/${id}`);
		if (!res.ok) {
			throw new Error('서버의 상태가 이상합니다.');
		}
		return await res.json();
	}
	catch (e) {
		throw new Error('무언가 잘못되었습니다.');
	}
};

```

사실 선언하는 방법은 크게 어렵지 않으나 이 함수들을 import해서 사용할 때 주의할 점이 있는데, 바로 호출 시에도 `async / await`을 붙여야 한다는 것이다. `async / await` 없이 해당 함수들을 import해서 호출하게 되면 `Promise<pending>`이 반환된다. 때문에 `async / await`을 붙여야만 원래 원하던 data를 얻을 수 있다.

---

### ES6 모듈 시스템(import, export) 사용하기

가장 먼저 `index.html`은 이런 모양이 된다.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <meta http-equiv="X-UA-Compatible" content="ie=edge" />
        <title>CAT</title>
    </head>
    <body>
     <div class="app"></div>
     <script type="module" src="./src/main.js"></script>
    </body>
</html>
```

`index.html` 파일 자체는 오직 script만을 불러오고, 다른 역할은 수행하지 않는다.

자바스크립트에서 모듈을 사용하기 위해서는 반드시 `type="module"`을 명시해주어야 한다.

#### 모듈에 대한 참고 문헌: https://ui.toast.com/fe-guide/ko_DEPENDENCY-MANAGE



`src/main.js`는 다음과 같은 형태가 된다.

```javascript
import App from "./App.js";

new App(document.querySelector(".app"));
```

바닐라 자바스크립트에서는 반드시 `.js`와 같은 확장자를 붙여 주어야 한다.

리액트와 같은 라이브러리에서는 해당 확장자들을 붙이지 않아도 잘 동작하는데 그 이유는 이미 웹팩에서 해당 설정을 마치기 때문이다.

```javascript
module.exports = {
	entry: './main.js',
	output: {
		filename: 'compiled.js'
	},
	resolve: {
		extensions: ['js', 'jsx']
	}
	module: {
		rules: [
			{
				test: /\.(js|jsx)$/,
				use: {
					loader: 'babel-loader'
				}
			}
		]
	}
}
```

`resolve.extensions`에 추가가 되어 있기 때문에 적지 않아도 동작했던 것이다. 때문에 바닐라 자바스크립트로 SPA를 만들 때에는 확장자를 붙여주어야 한다.

`src/App.js`는 다음과 같은 모양일 것이다.

```javascript
import ~~~~;
import ~~~~;

export default class App {
	constructor($target) {
	
	}
}
```

이렇게 `App.js`에서 각 컴포넌트들을 import해서 사용하게 된다.

---

### status code 고려하기

200 - 요청이 성공적으로 완료

201 - 성공적으로 만듦

301, 302 - redirect

400 - 잘못된 요청

401 - 권한이 없습니다. (토큰이 없거나 만료된 경우)

403 - 해당 자원에 접근할 수 있는 권한이 없습니다.

404 - Not Found 

405 - method not allowed

---

### 🔴 추상화

class를 추상화하여 공통된 기능들을 미리 선언하는 것은 중요한 작업입니다.

기본적으로 다음과 같은 Component는 추상화시킬 수 있습니다.

```javascript
export default class Component {
	$target = null;
	$state = null;
	
	constructor($target) {
		this.$target = $target;
		this.setup();
		this.setEvent();
		this.render();
	}
	
	template() {return "";}
	
	render() {
		this.$target.innerHTML = this.template();
	}
	
	// setup 함수는 target 이외에 parameter로 받는 것들을 해당 instance에 초기화시키기
	// 위해 사용
	setup() {}
	
	// 이벤트 리스너 다는 곳
    setEvent() {}
    
    // 리액트의 useState와 같은 기능
    setState(newState) {
    	this.$state = { ...this.$state, ...newState };
    	this.render();
    }
}
```

https://junilhwang.github.io/TIL/Javascript/Design/Vanilla-JS-Component/#_1-%E1%84%87%E1%85%AE%E1%86%AF%E1%84%91%E1%85%A7%E1%86%AB%E1%84%92%E1%85%A1%E1%86%B7%E1%84%8B%E1%85%B3%E1%86%AF-%E1%84%80%E1%85%A1%E1%86%B7%E1%84%8C%E1%85%B5%E1%84%92%E1%85%A1%E1%84%80%E1%85%B5

해당 블로그의 내용을 참조하여 만든 템플릿이다. 해당 블로그에 정말 좋은 예시와 설명이 많다.

https://prgms.tistory.com/53

프로그래머스에서 제공하는 정답이다. 이곳에도 추상화에 대한 설명이 있으나 위 블로그의 코드가 더 좋다고 생각한다.

---

### display grid에서 미디어 쿼리 사용하기

다음 예제는 1000px 초과에서는 한 행에 4개의 카드가, 1000px 이하에서는 한 행에 3개의 카드가 나오는 CSS 코드이다.

```css
.albums {
	display: grid;
	grid-gap: 20px;
	grid-template-columns: 1fr 1fr 1fr 1fr;
	grid-template-areas: "content content content content";
}
```

```css
@media only screen and (max-width: 1000px) {
	.albums {
		display: grid;
		grid-gap: 20px;
		grid-template-columns: 1fr 1fr 1fr;
		grid-template-areas: "content content content";
	}
}
```

---

### class 추가하는법

```javascript
const $element = document.getElementById('element');
$element.classList.add('className');
$element.classList.add('className1 className2'); // 여러개 넣기 (컴마 사용 X)
$element.classList.remove('className1');
$element.className = 'className'; // 기존 클래스 이름은 사라지고 className만이 남음
$element.classList.toggle('className'); // 해당 클래스가 있으면 지우고 없으면 추가
```

---

### constructor

new 연산자와 함께 클래스를 호출하면 암묵적으로 빈 객체가 생성된다. 이것이 class가 생성한 인스턴스. 그 이후에 constructor 내부의 모든 코드가 실행되어 this 바인딩 되어 있는 인스턴스를 초기화 시킨다.

처음 class의 인스턴스가 만들어지는 순간 constructor 내부의 모든 것들이 실행!!

---

###  이미지에 대한 lazy load

```javascript
<img class="lazy" src="empty.png" data-src="image1.png" />
```

다음과 같은 이미지에 대한 코드가 있다고 가정한다. 뷰포트에 잡혔을 때 해당 이미지를 불러오는 것이 성능면에서 유리하기 때문에 해당 이미지가 뷰포트 내로 들어오는 순간을 파악해야하고 이를 위해 인터섹션 옵저버를 이용한다.

```javascript
lazyLoadObserver() {
	const options = { threshold: 0 };
	const callback = (entries, observer) => {
		entries.forEach(entry => {
			if (entry.isIntersecting) {
				observer.unobserve(entry.target);
				entry.target.src = entry.target.dataset.src;
			}
		});
	};
	
	const io = new IntersectionObserver(callback, options);
	const lazyImages = Array.from(document.getElementsByClassName('lazy'));
	lazyImages.forEach(image => io.observe(image));
}
```

해당 함수를 constructor 내부에 두면 

```javascript
constructor() {
	// ...
	this.lazyLoadObserver();
}
```

각각의 이미지들이 인터섹션 옵저버에  의해 관리되고 실제 뷰포트 내부에 진입하게 되었을 때 load하게 된다.

#### 참고문헌

https://github.com/hanameee/vanillaJSKitty/blob/master/studyLog.md#intersection-observer-%EC%9D%B4%EB%9E%80

https://heropy.blog/2019/10/27/intersection-observer/



---

### 무한 스크롤

```html
// html

<div class="container" id="container">
	<h1>Blog Posts</h1>
<!-- 	<div class="blog-post">
		<h2 class="title">Blog post title</h2>
		<p class="text">Lorem ipsum dolor, sit amet consectetur adipisicing elit. Provident quod debitis in repellat veritatis minus ab ex maiores itaque quis.</p>
		<div class="user-info">
			<img src="https://randomuser.me/api/portraits/women/26.jpg" alt="pic" />
			<span>Leah Taylor</span>
		</div>
	</div> -->
</div>

<div class="loading">
	<div class="ball"></div>
	<div class="ball"></div>
	<div class="ball"></div>
</div>
```

```css
// css 

@import url('https://fonts.googleapis.com/css?family=Open+Sans:300,600&display=swap');

* {
	box-sizing: border-box;
}

body {
	background-color: #fafafa;
	font-family: 'Open Sans', sans-serif;
	padding-bottom: 100px;
}

.container {
	margin: 0 auto;
	max-width: 600px;
}

.blog-post {
	background-color: #fff;
    box-shadow: 0px 1px 2px rgba(50, 50, 50, .1), 
		0px 2px 4px rgba(60, 60, 60, 0.1);
	border-radius: 4px;
	padding: 40px;
	margin: 20px 0;
}

.title {
	margin: 0;
}

.text {
	color: #555;
	margin: 20px 0;
}

.user-info {
	display: flex;
	align-items: center;
}

.user-info img {
	border-radius: 50%;
	height: 30px;
	width: 30px;
}

.user-info span {
	font-size: 13px;
	margin-left: 10px;
}

.loading {
	opacity: 0;
	display: flex;
	position: fixed;
	bottom: 50px;
	left: 50%;
	transform: translateX(-50%);
	transition: opacity .3s ease-in;
}

/* 이 부분이 문제이긴해. 그냥 투명하게만 해놓은거지 필요없는데 렌더링 되고 있다.*/
.loading.show {
	opacity: 1;
}

.ball {
	background-color: #777;
	border-radius: 50%;
	margin: 5px;
	height: 10px;
	width: 10px;
	animation: jump .5s ease-in infinite;
}

.ball:nth-of-type(2) {
	animation-delay: 0.1s;
}

.ball:nth-of-type(3) {
	animation-delay: 0.2s;
}

@keyframes jump {
	0%, 100% {
		transform: translateY(0);
	}
	
	50% {
		transform: translateY(-10px);
	}
}
```

```javascript
// js

const container = document.getElementById('container');
const loading = document.querySelector('.loading');

getPost();
getPost();
getPost();

const throttle = (callback, delay) => {
	let timerId;
	
	return event => {
		if (timerId) return;
		timerId = setTimeout(() => {
			callback(event);
			timerId = null;
		}, delay, event);
	};
};


window.addEventListener('scroll', throttle(() => {
	// scrollTop -> 스크롤의 시작 부분
	// scrollHeight ->  스크롤 전체의 길이
	// clientHeight -> 사용자 화면의 전체 높이
	const { scrollTop, scrollHeight, clientHeight } = document.documentElement;
	console.log( { scrollTop, scrollHeight, clientHeight });
	
	if(clientHeight + scrollTop >= scrollHeight - 5) {
		// show the loading animation
		showLoading();
	}
}, 100));

function showLoading() {
	loading.classList.add('show');
	
	// load more data
	setTimeout(getPost, 100)
}

async function getPost() {
	const postResponse = await fetch(`https://jsonplaceholder.typicode.com/posts/${getRandomNr()}`);
	const postData = await postResponse.json();
	
	const userResponse = await fetch('https://randomuser.me/api');
	const userData = await userResponse.json();
	
	const data = { post: postData, user: userData.results[0] };
	
	addDataToDOM(data);
}

function getRandomNr() {
	return Math.floor(Math.random() * 100) + 1;
}

function addDataToDOM(data) {
	const postElement = document.createElement('div');
	postElement.classList.add('blog-post');
	postElement.innerHTML = `
		<h2 class="title">${data.post.title}</h2>
		<p class="text">${data.post.body}</p>
		<div class="user-info">
			<img src="${data.user.picture.large}" alt="${data.user.name.first}" />
			<span>${data.user.name.first} ${data.user.name.last}</span>
		</div>
	`;
	container.appendChild(postElement);
	
	loading.classList.remove('show');
}
```

위에서 확인했던 인터섹션 옵저버를 통해서도 무한 스크롤을 구현할 수 있지만, 개인적으로 이 코드가 더 이해하기 쉬운 것 같다.

---

이벤트 버블링, 스로틀, 디바운스
