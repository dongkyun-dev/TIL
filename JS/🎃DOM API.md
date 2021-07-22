# 🎃DOM API

> 모던 자바스크립트 Deep Dive 39장을 정리하는 글입니다.

---

### 문서 노드

문서 노드는 DOM 트리의 최상위에 존재하는 루트 노드로서 document 객체를 가리킨다.

브라우저 환경의 모든 자바스크립트 코드는 script 태그에 의해 분리되어 있어도 하나의 전역 객체 window를 공유한다. 따라서, 모든 자바스크립트 코드는 전역 객체 window의 document 프로퍼티에 바인딩 되어 있는 하나의 documnet 객체를 바라본다. 즉, HTML 문서당 document 객체는 유일하다.

---

### getElementById (id를 이용한 요소 노드 취득)

```javascript
<li id="apple">Apple</li>

const $elem = document.getElementById('apple');
```

id 값은 HTML 문서 내에서 유일한 값이어야 하며, class 어트리뷰트와는 달리 공백 문자로 구분하여 여러 개의 값을 가질 수는 없다. 

**단, HTML 문서 내에 중복된 id 값을 갖는 HTML 요소가 여러 개 존재하더라도 어떠한 에러도 발생하지 않는다. 즉, HTML 문서 내에서 중복된 id 값을 갖는 요소가 여러 개 존재할 가능성이 있다.**

이러한 경우 `getElementById` 메서드는 인수로 전달된 id 값을 갖는 첫 번째 요소 노드만 반환한다. 즉, `getElementById` 메서드는 언제나 단 하나의 요소 노드를 반환한다.

만약 인수로 전달된 id 값을 갖는 HTML 요소가 존재하지 않는 경우 `getElementById` 메서드는 null을 반환한다. (이게 자바스크립트 엔진이 null을 뱉는 몇가지 경우들 중 하나.)

---

### querySelector, querySelectorAll을 쓰는 것이 좋을까, getElementById, getElementBy***를 쓰는 것이 좋을까?

CSS 선택자 문법을 사용하는 querySelector, querySelectorAll 메서드는 getElementById, getElementBy*** 메서드에 비해 다소 느린 것으로 알려져 있다. 하지만 CSS 선택자 문법을 사용하여 좀 더 구체적인 조건으로 요소 노드를 취득할 수 있고 일관된 방식으로 요소 노드를 취득할 수 있다는 장점이 있다. 따라서 id 어트리뷰트가 있는 요소 노드를 취득하는 경우에는 getElementById 메서드를 사용하고 그 외의 경우에는 getElementById, getElementBy~~~ 메서드를 사용하는 것을 권장한다.

---

### 그렇다면 왜 getElementById만은 같이 사용하기를 추천하는 것일까?

이는 HTMLCollection과 NodeList의 차이점 때문이다.

먼저, 기본적으로 getElementBy*** 메서드들은 HTMLCollection을 반환한다. HTMLCollection은 유사 배열 객체이면서 이터러블 이다.

querySelector는 NodeList를 반환한다. NodeList는 유사 배열 객체이면서 이터러블 이다.

HTMLCollection과 NodeList의 중요한 특징은 노드 객체의 상태 변화를 실시간으로 반영하는 **살아 있는 객체**라는 것이다. HTMLCollection은 언제나 Live한 객체로 동작한다. 반면 NodeList는  노드 객체의 상태 변화를 실시간으로 반영하지 않고 과거의 정적 상태를 유지하는 non-live 객체로 동작하지만 경우에 따라 live 객체로 동작할 때가 있다. (childNodes 프로퍼티가 반환하는 NodeList 객체는 HTMLColleciton 객체와 같이 실시간으로 노드 객체의 상태 변경을 반영하는 live 객체로 동작한다.)

**살아 있는 객체는 왜 위험한가??? 다음의 예제를 보자.***

```html
<!DOCTYPE html>
<head>
	<style>
		.red { color: red; }
		.blue { color: blue; }
	</style>
</head>
<html>
	<body>
		<ul id="fruits">
			<li class="red">Apple</li>
			<li class="red">Banana</li>
			<li class="red">Orange</li>
		</ul>
		<script>
			const $elems = document.getElementByClassName('.red');
			console.log($elems);    // HTMLCollection(3) [li.red, li.red, li.red]
			
			for (let i = 0; i < $elems.length; i++) {
				$elems[i].className = 'blue';
			}
			
			console.log($elems);    // HTMLCollection(1) [li.red]
		</script>
	</body>
</html>
```

이 코드가 실행되면 우리는 일반적으로 3개의 li 태그의 class가 blue로 바뀌면서 색깔이 파란색으로 모두 바뀌기를 기대한다. 하지만, 실제 결과는 Apple, Orange만이 파란색으로 바뀌고 Banana는 그대로 빨간색으로 남는다. 그 이유가 바로 HTMLCollection이 살아있는 객체이기 때문이다.

1. 첫번째 반복(i === 0)

   > $elems[0]은 첫번째 li 요소다. 이 요소는 className 프로퍼티에 의해 class 값이 'red'에서 'blue'로 변경된다. 이때 첫번째 li 요소는 class 값이 'red'에서 'blue'로 변경되었으므로 getElementByClassName 메서드의 인자로 전달한 'red'와 더는 일치하지 않기 때문에 $elems에서 실시간으로 제거된다. 이처럼 HTMLCollection 객체는 실시간으로 노드 객체의 상태 변경을 반영하는 살아 있는 DOM 컬렉션 객체다.

2. 두번째 반복(i === 1)

   > 첫번째 반복에서 첫번쨰 li 요소는 $elems에서 제거되었다. 따라서 $elems[1]은 세 번째 li요소다. 이 세 번째 li 요소의 class 값도 'blue'로 변경되고 마찬가지로 HTMLCollection 객체인 $elems에서 실시간으로 제외된다.

3. 세번째 반복(i === 2)

   > 첫번째, 두번째 반복에서 첫번째 세번째 li 요소가 제거 되었다. 따라서 $elems에는 두번째 li 요소 노드만 남았다. 이때 $elems.length는 1이므로 for문의 조건식이 false로 평가되어 반복이 종료된다. 따라서 $elems에 남아 있는 두번째 li 요소의 class 값은 변경되지 않는다.

이 문제를 해결하는 방법은 for 문을 뒤에서 부터 돌던가 아니면 while문을 통해 0번째 원소에만 계속 값을 수정해주는 방법을 사용하면 된다.

하지만, 더 간단한 해결책은 결국 부작용을 발생시키는 HTMLColleciton 객체를 사용하지 않는 것이다. HTMLCollection을 사용하기 보다는 이를 일반 배열로 변환시켜서 사용하면 부작용을 없앨 수 있다. (by. Array.from, 스프레드 연산자)

**결국, HTMLCollection의 경우 항상 live 하기 때문에 변환이 필요하고 NodeList 또한 특정한 경우에는 live 해지기 때문에 일반 배열로 변환해서 사용하는 것을 추천한다. 하지만, 어쨋든 childNodes를 쓰는 경우만 아니라면 NodeList는 live해지는 경우가 없기 때문에 querySelector 사용을 통해 HTMLCollection의 반환을 피한다.**

**그렇다면 하나 의문이 들 수 있다. getElementById도 HTMLCollection을 반환하는데 왜 특별히 이 친구만 사용하는 거지??**

**Id의 특징 때문이다. 어짜피 getElementById는 유니크하다는 id의 특징 때문에 하나의 노드 혹은 null을 반환하게 된다. 때문에 live 하더라도 크게 문제를 일으키지 않는다.**

---

## 공백 텍스트 노드에 대한 내용 추가 필요

---

### textContent vs innerText

둘 다 기능은 비슷하다. 문자열을 넣는 기능을 수행한다. 하지만, `innerText`는 사용하지 않는 것이 좋다. 그 이유는 다음과 같다.

- innerText 프로퍼티는 CSS에 순종적이다. 예를 들어, innerText 프로퍼티는 CSS에 의해 비표시( visibility: hidden; )로 지정된 요소 노드의 텍스트를 반환하지 않는다.
- innerText 프로퍼티는 CSS를 고려해야 하므로 textContent 프로퍼티보다 느리다.

---

### insertAdjacentHTML vs innerHTML

HTML 마크업을 파싱하지 않는 textContent, innerText와는 달리 insertAdjacentHTML, innerHTML은 HTML 마크업을 파싱한다.

요소 노드의 innerHTML 프로퍼티에 할당한 HTML 마크업 문자열은 렌더링 엔진에 의해 파싱되어 요소 노드의 자식으로 DOM에 반영된다. 이때 사용자로부터 입력받은 데이터를 그대로 innerHTML 프로퍼티에 할당하는 것은 **크로스 사이트 스크립팅 공격(XSS)**에 취약하므로 위험하다. HTML 마크업 내에 자바스크립트 악성 코드가 포함되어 있다면 파싱 과정에서 그대로 실행될 가능성이 있기 때문이다.

innerHTML 프로퍼티로 스크립트 태그를 삽입하여 자바스크립트를 실행하도록 하는 예제를 확인해보자.

```html
<script>
	document.getElementById('foo').innerHTML
		= '<script>alert(document.cookie)</script>';
</script>
```

HTML5는 innerHTML 프로퍼티로 삽입된 script 요소 내의 자바스크립트 코드를 실행하지 않는다. 따라서 HTML5를 지원하는 브라우저에서 위 예제는 동작하지 않는다. 하지만 script 요소 없이도 크로스 사이트 스크립팅 공격은 가능하다. 다음의 간단한 크로스 사이트 스크립팅 공격은 모던 브라우저에서도 동작한다.

```html
<script>
	document.getElementById('foo').innerHTML
		= `<img src="x" onerror="alert(document.cookie)">`;
</script>
```

innerHTML 프로퍼티의 또 다른 단점은 요소 노드의 innerHTML 프로퍼티에 HTML 마크업 문자열을 할당하는 경우 요소 노드의 모든 자식 노드를 제거하고 할당한 HTML 마크업 문자열을 파싱하여 DOM을 변경한다는 것이다.

```html
<!DOCTYPE html>
<html>
	<body>
		<ul id="fruits">
			<li class="apple">Apple</li>
		</ul>
	</body>
	<script>
		const $fruits = document.getElementById('fruits');
		
		$fruits.innerHTML += '<li class="banana">Banana</li>'
	</script>
</html>
```

우리는 이 코드가 `li.banana` 요소 노드만 생성하여 `#fruits` 요소의 자식 요소로 추가되기를 바란다. 위 예제는 이와 같이 동작할 것처럼 보이지만 사실은 `#fruits` 요소의 모든 자식 노드를 제거하고 새롭게 요소 노드 `li.apple`, `li.banana`를 생성하여 `#fruits` 요소의 자식요소로 추가한다.

딱 봐도 굉장히 비효율적이다. 또한 `innerHTML`프로퍼티는 새로운 요소를 삽입할 때 삽입될 위치를 지정할 수 없다는 단점도 있다.

이러한 단점들에 비교하여 `insertAdjacentHTML`을 보자.

`insertAdjacentHTML` 메서드는 기존 요소를 제거하지 않으면서 위치를 지정해 새로운 요소를 삽입한다.

```html
<!DOCTYPE html>
<html>
	<body>
		// beforebegin
		<div id="foo">
			// afterbegin
			text
			//beforeend
		</div>
		// afterend
	</body>
	<script>
		const $foo = document.getElementById('foo');
		
		$foo.insertAdjacentHTML('beforebegin', '<p>beforebegin</p');
		$foo.insertAdjacentHTML('afterbegin', '<p>afterbegin</p');
		$foo.insertAdjacentHTML('beforeend', '<p>beforeend</p');
		$foo.insertAdjacentHTML('afterend', '<p>afterend</p');
	</script>
</html>
```

하지만, innerHTML 프로퍼티와 마찬가지로 `insertAdjacentHTML` 메서드는 HTML 마크업 문자열을 파싱하므로 크로스 사이트 스크립팅 공격에 취약하다는 점은 동일하다.

