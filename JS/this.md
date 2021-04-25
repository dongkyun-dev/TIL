# 🤣this

기본적으로 this를 완벽하게 이해하기 위해서는 실행 컨텍스트를 알아야한다.

가장 처음의 this는 Window 객체를 가진다. (브라우저 콘솔(F12)을 켜고, this를 쳐보면 확인할 수 있다.)

```javascript
this; // Window{}
```

함수 안에 넣어서 동작 시켜도 동일한 결과를 얻을 수 있다.

```javascript
function a() {console.log(this);}

a(); // Window{}
```

동일한 코드임에도 불구하고 strict모드로 확인한다면 undefined가 나온다.

```javascript
function a() {'use strict'; console.log(this);}

a(); // undefined
```

**this가 이 정도의 동작만을 수행한다면 어렵지 않은 개념일 것이다. 하지만, 일반적으로 this는 객체의 프로퍼티인 함수에서 의미를 가진다.  메서드를 호출하면 this는 호출한 메서드를 소유하는 객체가 된다.**

```javascript
const o = {
	name: 'dongkyun',
	speak() {return `My name is ${this.name}!`},
}

o.speak()  // "My name is dongkyun!"
```

`o.speak()`을 호출하게 되면 this는 o에 묶인다. 때문에 `this.name`을 통해 'dongkyun'이라는 값을 얻어낼 수 있다.

여기서 제일 중요한 것은, this는 함수를 어떻게 선언했느냐가 아니라 어떻게 호출했느냐에 따라 달라진다는 것을 이해해야 한다는 점이다. 즉, this가 o에 묶인 이유는 speak가 o의 프로퍼티여서가 아니라, o에서 speak 함수를 호출했기 때문이다. 때문에 같은 함수를 다른 변수에 할당하면 this가 달라진다. 

조금 더 이해하기 쉽게 말하자면, 호출할 때, 호출하는 함수가 객체의 메서드인지 아니면 그냥 함수인지가 중요하다. 위의 예제는 객체의 메서드를 호출한 것이고, 아래의 예제는 그냥 함수를 할당받은 것이다.

```javascript
const speak = o.speak
speak === o.speak    //true 두 변수는 같은 함수를 가리킨다는 것을 알 수 있다.
speak()    //"My name is undefined!" this가 달라졌음을 알 수 있다.
```

명시적으로 이 this를 바꾸는 방법도 존재한다. 바로 bind, call, apply를 사용하는 것이다. 이에 대한 내용은 분리해서 작성할 예정이다.

this는 특히 생성자에서 많이 쓰인다.

```javascript
function Person(name, age){
	this.name = name
	this.age = age
}

Person.prototype.sayHi = function(){
	console.log(this.name, this.age)
}
```

만약 이런 생성자 함수가 있을 때, new없이 호출하게 되면 어떠한 결과를 발생시킬까?

```javascript
Person('dongkyun', 25)
console.log(window.name, window.age)    // dongkyun 25
```

this가 특정한 객체에 묶이지 않고 실행된다. 때문에 this는 가장 처음값인 window 객체가 되고, 생성자 함수 호출을 통해 바뀌는 값들도 window 객체에 적용돼버린다.

때문에 `Person('dongkyun', 25)`가 아닌 `const person = new Person('dongkyun', 25)`으로 실행해야, this가 새롭게 생성되는 person 인스턴스에 묶이게 된다.

 <br/>

하지만, this가 악명이 높은 이유는 다른 곳에 있다. 바로 중첩된 함수 안에서 this를 사용할 때 나타나는 동작방식이다.

```javascript
const o = {
	name: 'Julie',
	greetBackwards: function(){
		function getReversedName(){
			let nameBackwards = ''
			for(let i=this.name.length-1;i>=0;i--){
				nameBackwards += this.name[i]
			}
			return nameBackwards
		}
		return `${getReverseName()} si eman ym ,olleH`
	},
}

o.greetBackwards()
```

이 코드는 제대로 동작하지 않는다. `o.greetBackwards()`를 호출하는 시점에서 자바스크립트는 this를 의도한 대로 o에 연결한다. 하지만, greetBackwards 안에서 getReversedName을 호출하면 this는 o가 아닌 다른 것에 묶이게 된다. (스트릭트 모드라면 undefined가 될 것이고, 스트릭트 모드가 아니라면 전역 객체인 Window에 묶일 것이다.)

이 문제를 해결하는 방법은 2가지가 존재한다.

첫번째 방법은 this를 함수 안에서 한번 할당해놓고 사용하는 것이다.

```javascript
const o = {
	name: 'Julie',
	greetBackwards: function(){
		const self = this 
		function getReversedName(){
			let nameBackwards = ''
			for(let i=self.name.length-1;i>=0;i--){
				nameBackwards += self.name[i]
			}
			return nameBackwards
		}
		return `${getReverseName()} si eman ym ,olleH`
	},
}

o.greetBackwards()
```

보통 할당하는데 사용하는 변수의 이름으로는 `self`나 `that`을 많이 사용한다.

두번쨰 방법은 화살표 함수를 사용하는 것이다.

```javascript
const o = {
	name: 'Julie',
	greetBackwards: function(){ 
		const getReverseName = () => {
			let nameBackwards = ''
			for(let i=this.name.length-1;i>=0;i--){
				nameBackwards += this.name[i]
			}
			return nameBackwards
		}
		return `${getReverseName()} si eman ym ,olleH`
	},
}

o.greetBackwards()
```

(화살표함수가 되는 이유는 화살표함수는 함수가 아닌 하나의 value이기 때문이랑 관련이 있지 않을까 싶은데... 확실한건 아니기 때문에 더 찾아봐야겠다.)

---

## 참고문헌

https://www.zerocho.com/category/JavaScript/post/5b0645cc7e3e36001bf676eb

러닝 자바스크립트(한빛미디어)

