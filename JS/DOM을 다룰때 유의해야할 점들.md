# DOM을 다룰때 유의해야할 점들

---

### document

브라우저 환경의 모든 자바스크립트 코드는 script 태그에 의해 분리되어 있어도 하나의 전역 객체 window를 공유한다. 따라서 모든 자바스크립트 코드는 전역 객체 window의 document 프로퍼티에 바인딩 되어 있는 하나의 document 객체를 바라본다. 즉, HTML 문서당 document 객체는 유일하다.

---

### getElementById

HTML 문서 내에는 중복된 id 값을 갖는 요소가 여러 개 존재할 가능성이 있다. (물론 그렇게 하면 안된다.) 

이러한 경우 `getElementById` 메서드는 인수로 전달된 id 값을 갖는 첫 번째 요소 노드만 반환한다. 즉, `getElementById` 메서드는 언제나 단 하나의 요소 노드를 반환한다.

만약 인수로 전달된 id 값을 갖는 HTML 요소가 존재하지 않는 경우 `getElementById` 메서드는 null을 반환한다.

---

### 어떠한 선택자를 사용해야 할까?

CSS 선택자 문법을 사용하는 `querySelector`, `querySelectorAll` 메서드는 `getElementById`, `getElementBy***` 메서드보다 다소 느린 것으로 알려져 있다. 하지만 CSS 선택자 문법을 사용하여 좀 더 구체적인 조건으로 요소 노드를 취득할 수 있고 일관된 방식으로 요소 노드를 취득할 수 있다는 장점이 있다.

따라서 id 어트리뷰트가 있는 요소 노드를 취득하는 경우에는 `geElementById` 메서드를 사용하고 그 외의 경우에는 `querySelector`, `querySelectorAll` 메서드를 사용하는 것을 권장한다.

---

### textContent vs innerText

textContent는 완전히 textNode를 위한 것이기 때문에 가져올 때도 text로 가져오고 넣을 때도 text로 넣게 된다.

`document.getElementById('foo').textContent = 'Hi! <span>there</span>'`

와 같이 HTML 마크업을 넣는다 하더라도 파싱되지 않고 문자열 그대로 들어간다.

참고로 `textContent` 프로퍼티와 유사한 동작을 하는 `innerText` 프로퍼티가 있다. `innerText` 프로퍼티는 다음과 같은 이유로 사용하지 않는 것이 좋다.

- `innerText` 프로퍼티는 CSS에 순종적이다. 예를 들어, `innerText` 프로퍼티는 CSS에 의해 비표시로 지정된 요소 노드의 텍스트를 반환하지 않는다.
- `innerText` 프로퍼티는 CSS를 고려해야 하므로 `textContent` 프로퍼티보다 느리다.

---

### innerHTML vs insertAdjacentHTML

먼저, innerHTML의 단점을 파악해본다.

- innerHTML 프로퍼티에 할당한 HTML 마크업 문자열은 렌더링 엔진에 의해 파싱되어 요소 노드의 자식으로 DOM에 반영된다. 이때 사용자로부터 입력받은 데이터를 그대로 innerHTML 프로퍼티에 할당하는 것은 크로스 사이트 스크립팅 공격(XSS) 에 취약하므로 위험하다.

- ```javascript
  <ul id="fruits">
  	<li class="apple">Apple</li>
  </ul>
  
  const $fruits = document.getElementById('fruits');
  $fruits.innerHTML += '<li class="banana">Banana</li>';
  ```

  해당 코드를 보면 li.banana 요소 노드만 생성하여 #fruits 요소의 자식 요소로 추가할 것이라고 예측할 수 있다. 하지만, innerHTML은 이러한 우리의 상식과는 조금 다르게 동작한다. 실질적으로 #fruits 요소의 모든 자식 노드(li.apple)를 제거하고 새롭게 요소 노드 li.apple, li.banana를 생성하여 #fruits 요소의 자식 요소로 추가한다.

  기존 것을 삭제하고 다시 만든다는 것은 당연히 엄청난 비효율이다.

- 새로운 요소를 삽입할 때 삽입될 위치를 지정할 수 없다.



이와 달리 insertAdjacentHTML은 다음과 같은 특징을 가진다.

![insertAdjection](../assets/img/insertAdjection.svg)

- insertAdjacentHTML 메서드는 기존 요소에는 영향을 주지 않고 새롭게 삽입될 요소만을 파싱하여 자식 요소로 추가하므로 기존의 자식 노드를 모두 제거하고 다시 처음부터 새롭게 자식 노드를 생성하여 자식 요소로 추가하는 innerHTML 프로퍼티보다 효율적이고 빠르다.
- 새로운 요소를 삽입할 때 위치를 지정할 수 있다.
- 하지만, insertAdjacent 메서드 또한 HTML 마크업 문자열을 파싱하므로 XSS 공격에 취약하다는 점은 동일하다.

---

## 참고문헌

모던 자바스크립트 딥다이브



