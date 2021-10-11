# 🍙typeof와 instanceof의 차이

---

### typeof

typeof 연산자는 7가지 문자열 중 한 개를 반환하게 된다

string, number, boolean, undefined, symbol, object, function

자바스크립트의 데이터 타입 7개 중에서 null이 빠진 대신 객체에 속하는 function이 따로 분리되었다고 생각하면 된다.

```javascript
// 헷갈릴만한 것들

typeof NaN    // number
typeof null    // object
```

typeof연산자로 null값을 연산하면 object를 반환한다. 이는 자바스크립트 고유의 버그이다. 하지만, 기존 코드에 영향을 줄 수 있기 때문에 아직까지 수정되지 못하고 있다.

또 하나 주의해야할 것은 선언하지 않은 식별자를 typeof 연산자로 연산하면 `ReferenceError`가 아닌 `undefined`를 반환한다는 것이다.

````javascript
typeof undeclared    // undefined
````

---

### instanceof

`객체 instanceof 생성자 함수`

우변의 생성자 함수의 프로토타입에 바인딩된 객체가 좌번의 객체의 프로토타입 체인 상에 존재하면 true로 평가되고, 그렇지 않은 경우에는 false로 평가된다.

```javascript
function Person(name) {
	this.name = name;
}

const me = new Person('Lee');

console.log(me instanceof Person);    // true
console.log(me instanceof Object);    // true
```

프로토타입을 교체해본다.

```javascript
function Person(name) {
	this.name = name;
}

const me = new Person('Lee');

const parent = {};

Object.setPrototypeOf(me, parent);

console.log(me instanceof Person);    // false
console.log(me instanceof Object);    // true
```

`Person.prototype`이 `me` 객체의 프로토타입 체인 상에 존재하지 않기 때문에 첫번째 콘솔은 `false`를 뱉는다.

`me`의 프로토타입으로 들어간 `parent` 객체가 `Person` 생성자 함수의 프로토타입을 가지게 된다면 다시 true가 출력된다. 

```javascript
function Person(name) {
	this.name = name;
}

const me = new Person('Lee');

const parent = {};

Object.setPrototypeOf(me, parent);

Person.prototype = parent;

console.log(me instanceof Person);    // true
console.log(me instanceof Object);    // true
```

이처럼 instanceof 연산자는 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수를 찾는 것이 아니라 **생성자 함수의 prototype에 바인딩된 객체가 프로토타입 체인 상에 존재하는지 확인한다.**

