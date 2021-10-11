# Parameters와 Arguments

---

가장 먼저 이들의 정의를 확인한다.

A parameter is a variable in a function definition. When a method is called, the arguments are the data you pass into the function's parameters.

딱 한 줄이다.

파라미터는 함수 선언 시에 만드는 variable, arguments는 실제 함수를 호출할 때 인자로 넘겨주는 값을 의미한다.

---

### argument

함수 객체의 arguments 프로퍼티 값은 arguments 객체이다. 함수 호출 시 전달된 인수들의 정보를 담고 있는 이터러블한 유사 배열  객체이며, 함수 내부에서 지역 변수처럼 사용된다.

함수가 호출되면 함수 몸체 내에서 암묵적으로 매개변수가 선언되고 undefined로 초기화된 이후 인수가 할당된다. (var과 작동 방식이 유사하다.)

undefined로 초기화된 이후 인수가 할당됐기 때문에 재할당이 되었다는 사실과 추가적인 재할당이 가능하다는 사실을 알 수 있다.

```javascript
function foo(num) {
	num = 20;
	console.log(num);    // 20
}

foo(100);
```

또한, argument 객체는 유사 배열이기 때문에 배열 메서드를 사용하기 위해서는 배열로 바꾸는 작업이 필요했다. 하지만, ES6에서 Rest 파라미터가 도입되면서 이러한 변환 작업이 더욱 간편해졌다.

```javascript
function sum(...args) {
	return args.reduce((pre, curr) => pre + curr, 0);
}

console.log(sum(1, 2));    // 3
console.log(sum(1, 2, 3));    // 6
```

