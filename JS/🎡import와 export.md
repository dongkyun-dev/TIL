

# 🎡import와 export

> 항상 헷갈려서 정리해둔다.

---

```javascript
// util.js

function getPower(decimalPlaces) {
	return 10 ** decimalPlaces;
}

function capitalize(word) {
	return word[0].toUpperCase() + word.slice(1);
}

const PI = 3.14;
const E = 2.71828;

export { getPower, capitalize, PI, E };
```

---

기본적으로 다음과 같이 `import` 해서 사용할 수 있다.

```javascript
import { getPower, PI } from './util';
```

---

만약, 해당 파일에서 `export`하는 전부가 필요하다면 다음과 같이도 사용할 수 있다.

```javascript
import * as utils from './util';

console.log(utils.PI)    // 3.14
utils.getPower(1);       // 10
```

util.js에서 `export`하는 모든 것들을 객체로 받아오는데 이 이름을 utils로 하겠다는 뜻이다.

---

밑에서 몰아서 `export`를 할 수도 있지만 각 선언 앞에 `export`를 붙일 수도 있다.

```javascript
// util.js

export function getPower(decimalPlaces) {
	return 10 ** decimalPlaces;
}

export function capitalize(word) {
	return word[0].toUpperCase() + word.slice(1);
}

export const PI = 3.14;
export const E = 2.71828;
```

`export default`를 붙여서 사용할 수도 있다.

```javascript
// extension.js

export default function extension() {
	console.log('foo');
}
```

---

이 둘의 차이는 import할 때 중괄호를 쓰냐, 안 쓰냐의 차이에 있다.

```javascript
import { capitalize, PI } from './util';
import extension from './extension';
```

또한, 중요한 차이가 있는데 `export default`는 파일 당 딱 한 번만 선언이 가능하다는 것이다. 여러 개의 `export default`를 사용해봐야 `import`할 때는 맨 처음 `export default` 한 것만을 가져올 수 있다. 때문에 컴포넌트 혹은 클래스와 같이 한 파일에서 하나의 `export`만 사용하는 경우에만 `default`를 붙이고 이외에는 반드시 일반 `export`를 쓸 필요가 있다.

---

## 참고자료

자바스크립트 코딩의 기술(길벗)

