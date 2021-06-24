# ✨자바스크립트의 undefined와 null

---

### undefined

> 변수가 선언이 되고 undefined로 초기화되었지만, 아직 할당이 되지 않은 상대.
>
> primitive value used when a variable has not been assigned a value.
>
> ```javascript
> let a;
> console.log(a);    // undefined
> ```

### null

> null은 NULL의 심볼이며, 의도를 갖고 변수에 null을 할당하여 값이 없다는 것을 나타낸다.
>
> null의 type은 object인데 이는 명백한 자바스크립트 자체의 오류이다
>
> primitive value that represents the **intentional** absence of any object value.
>
> ```javascript
> let a = null;
> console.log(a);    // null
> console.log(typeof a);    // object    
> ```

```javascript
undefined === null    // false
undefined == null     // true
```



### undefined와 null 모두 "값이 없음"을 의미한다. 그렇다면 언제 undefined를 쓰고 언제 null을 써야 할까?

undefined는 쓰면 안된다. 위의 설명에서도 알 수 있듯이 이 둘의 차이는 결국 할당의 유무이다. 할당의 주체는 개발자이다. 

개발자가 직접 undefined를 할당할 수도 있기는 하지만, 그렇게 하지 않는 이유는 undefined는 자바스크립트 엔진이 코드를 실행할 때, 없음을 표현하기 위해 사용하는 데이터 타입으로 보장하기 위해서이다.

그렇다면, 자바스크립트 엔진은 값이 없음을 보일 때 undefined만을 사용할까?

또, 그것은 아니다. null을 사용하는 경우도 꽤 많다. 특히, `querySelector`와 같이 dom을 조작하는 메서드에서 해당 dom이 존재하지 않는다면 null을 반환하곤 한다.

(프로토타입 체인에서 null을 상속받는 경우, regex matching에서도 null을 반환한다.)

---

### 결론

undefined를 직접 할당해서는 안된다. undefined는 자바스크립트 엔진이 반환하는 값으로 보장하기 위해서이다.

하지만, 그렇다고 해서 null이 개발자가 직접 할당하는 것을 보장하는 것은 아니다. 자바스크립트 엔진은 null을 반환하기도 한다.

---

## 참고자료

https://medium.com/crocusenergy/js-undefined-null-%EC%96%B4%EB%96%A8-%EB%95%8C-%EC%93%B8%EA%B9%8C-8782dc3c35b6

https://velog.io/@josworks27/null-undefined-%EC%B0%A8%EC%9D%B4

https://vvkchandra.medium.com/essential-javascript-mastering-null-vs-undefined-66f62c65d16b
