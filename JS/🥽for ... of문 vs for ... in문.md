# 🥽for ... of문 vs for ... in문

> 모던 자바스크립트 Deep Dive를 바탕으로 작성합니다.

---

가끔씩 이 경우에 for of를 써야 할지, for in을 써야 할지 헷갈리는 경우들이 종종 발생한다. 때문에 이번 기회에 제대로 비교하고자 정리한다.

```javascript
for (변수 선언문 of 이터러블) { ... }

for (변수 선언문 in 객체) { ... }
```

---

### for ... of 문

for ... of 문은 내부적으로 이터레이터의 `next` 메서드를 호출하여 이터러블을 순회하며 `next` 메서드가 반환한 이터레이터 `result` 객체의 `value` 프로퍼티 값을 for ... of 문의 변수에 할당한다. 그리고 이터레이터 `result` 객체의 `done` 프로퍼티 값이 `false`이면 이터러블의 순회를 계속하고 `true`이면 이터러블의 순회를 중단한다.

```javascript
for (const item of [1, 2, 3]) {
	console.log(item);
}

-----------------------------------------------------------------------
const iterable = [1, 2, 3];

// 이터러블의 Symbol.iterator 메서드를 호출하여 이터레이터를 생성한다.
const iterator = iterable[Symbol.iterator]();

for (;;) {
	// 이터레이터의 next 메서드를 호출하여 이터러블을 순회한다.
	// 이때 next 메서드는 이터레이터 result 객체를 반환한다.
	const res = iterator.next();
	
	if (res.done) break;
	
	const item = res.value;
	console.log(item);    // 1 2 3
}
```

그렇다면 이터러블인지 어떻게 확인할 수 있을까?

바로 `Symbol.iterator`의 유무를 확인하면 된다. `Symbol.iterator`는 `Symbol.iterator`를 프로퍼티 키로 사용한 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속받을 수 있다. 배열, 문자열, Map, Set은 기본적으로 `Symbol.iterator`를 상속받은 이터러블이다.

이터러블은 for ... of 문으로 순회할 수 있으며 스프레드 문법과 배열 구조분해 할당의 대상으로 사용할 수 있다.

---

그렇다면 배열과 비슷한 모양을 가진 유사 배열 객체는 이터러블일까?

먼저, 정답을 말하자면 유사 배열 객체에는 `Symbol.iterator` 메서드가 없고 이 때문에 for ... of 문으로 순회할 수 없다.

```javascript
// 유사 배열 객체는 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있고, length 프로퍼티를 갖는 객체를 말한다.
const arrayLike = {
    0: 1,
    1: 2,
    2: 3,
    length: 3
};
```

하지만, 제일 중요한 것은 이터러블과 유사 배열 객체가 완벽하게 이분법적인 개념이 아니라는 것이다. arguments, NodeList HTMLCollection과 같은 것들이 유사 배열 객체이면서 이터러블이다. 배열과 문자열 또한 유사 배열 객체이면서 이터러블이다.

하지만, 모든 유사 배열 객체가 이터러블인 것은 아니다. (위의 예제가 그러하다.) 다만, `Array.from`메서드를 통해 간단하게 배열로 바꿀 수 있다. 

```javascript
// Array.from은 유사 배열 객체 또는 이터러블을 배열로 변환한다.
const arr = Array.from(arrayLike);
```

---

### for ... in 문



---

### 결국 for ... in 문은 객체를 순환할 때 사용하고, for .. of 문은 이터러블을 순환할 때 사용한다는 것이다.