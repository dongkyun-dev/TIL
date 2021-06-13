



# 👏Call, Apply, Bind

> this를 공부하면서, this는 함수를 어떻게 선언했느냐가 아니라 어떻게 호출했느냐에 따라 달라진다고 이야기 했었다. 하지만 이 call, apply, bind를 사용하면 어떻게 호출했는지와는 관계없이 this를 지정할 수 있다.

```javascript
const bruce = {name: "Bruce"}
const madeline = {name: "Madeline"}

function greet(){
	return `Hello, I'm ${this.name}!`
}

function update(birthYear, occupation){
	this.birthYear = birthYear
	this.occupation = occupation
}
```

### 1. Call

call 함수의 구조는 `함수.call(this로 사용할 객체, 매개변수들)`이다. 위의 함수를 활용한 예제를 본다.

```javascript
greet()              // "Hello, I'm !" - this는 어디에도 묶이지 않는다.
greet.call(bruce)    // "Hello, I'm Bruce!" - this는 bruce이다.
greet.call(madeline) // "Hello, I'm Madeline!" - this는 madeline이다.
```

call 함수의 파라미터로 객체 하나만을 넣게 되면, 바로 그 객체에 this를 묶게 된다. 

중요한 것은 greet 함수 선언부에는 파라미터에 대한 표시가 없다는 것이다. call 함수의 호출 대상이 되는 함수(greet 함수)에는 따로 this로 사용할 객체에 대한 파라미터 이름을 적지 않는다. 

다음 예제는 call 함수의 파라미터가 여러개인 경우이다.

```javascript
update.call(bruce, 1949, 'singer')
update.call(madeline, 1942, 'actor')
```

 아래 두번의 call 함수 호출 이후에 bruce 객체는 `{name: "Bruce", birthYear: 1949, occupation: "singer"}`, madeline 객체는 `{name: "Madeline", birthYear: 1942, occupation: "actor"}`가 된다.

여기서도 call 함수의 호출부에는 ~~파라미터가~~ arguments가 3개이지만, update 함수 선언부에는 파라미터가 2개뿐인 것을 확인할 수 있다. 

항상 call 함수의 호출부에 들어가는 ~~파라미터들~~ arguments들 중 맨 처음은 this로 사용할 값이어야하고, 이에 대한 내용을 call 함수의 대상이 되는 함수(update 함수) 선언부에서 명시하지 않는다.

---

### 2. Apply

apply는 함수 매개변수를 처리하는 방법을 제외하고는 call 함수와 완전히 같다. call은 일반적인 함수와 마찬가지로 매개변수를 직접 받지만, apply는 매개변수를 배열로 받는다.

```javascript
update.apply(bruce, [1949, 'singer'])
update.apply(madeline, [1942, 'actor'])
```

이 코드는 저 위의 call 함수를 호출하는 두 줄과 동일한 결과를 만든다.

때문에 매개변수가 일반 요소일 경우 call 함수를, 배열일 경우 apply 함수를 사용하는 것이 편하다.

물론, 스프레드 연산을 이용하면 call 함수도 배열을 쉽게 처리할 수 있다.

```javascript
const newBruce = [1940, 'artist']

update.call(bruce, ...newBruce)
update.apply(bruce, newBruce)

//두 개가 똑같은 결과를 만든다.
```

---

### 3. Bind

bind 함수를 사용하면 함수의 this 값을 영구히 바꾼다. bind된 함수를 한 번 호출한 이후에는 call, apply, bind를 호출하더라도 this가 변하지 않는다.

```javascript
const updateBruce = update.bind(bruce)   // 객체를 넣고 바인드 시킨다. 이제부터 updateBruce의 this는 항상 bruce 객체이다.

updateBruce(1904, "actor")               // bruce 객체는 {name: "Bruce", birthYear: 1904, occupation: "actor"}가 된다.

updateBruce.call(madeline, 1274, "king") // 원래라면 madeline 객체의 값이 수정될 것이다. 하지만, updateBruce의 this는 bruce 객체이고 이는 바뀌지 않는다. 때문에 bruce 객체가 {name: "Bruce", birthYear: 1274, occupation: "king"}으로 변하게 된다.

```

bind는 함수의 동작을 영구적으로 바꾸기 때문에 찾기 어려운 버그의 원인이 될 수 있다. 때문에 bind를 쓰고자 한다면 반드시 함수의 this가 어디에 묶이는지 정확히 파악하고 사용해야만 한다.

---

## 참고문헌

러닝자바스크립트(한빛미디어)