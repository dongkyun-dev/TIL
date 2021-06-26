# 🧧localStorage, sessionStorage로 간단한 상태 유지하기

> 자바스크립트 코딩의 기술(길벗)을 보고 작성하는 요약글입니다.

---

사용자의 데이터를 가져오는 가장 간단한 방법은 단연 로그인 절차를 만드는 것이다.

문제는 많은 사용자들이 로그인이 필요한 사이트를 외면한다는 사실이다. 때문에 로그인을 하지 않고도 사용자의 간단한 정보들을 저장할 곳이 필요한데 이때 localStorage, sessionStorage를 활용하면 된다. localStorage는 데이터가 영구적으로 저장되고, sessionStorage는 데이터가 영구적으로 저장되지는 않는다. (더 자세한 내용은 맨 마지막에 추가적으로 확인할 것이다.)

이 둘의 차이는 저장한 데이터의 영구성에 있다. 사용되는 메서드들은 모두 같기 때문에 localStorage만을 기준으로 메서드들을 설명하겠다. (앞에 localStorage를 sessionStorage로만 바꾸면 된다.

---

### 데이터 저장

```javascript
function save(item) {
	localStorage.setItem('item', item);
}
```

이와 같이 key-value 쌍으로 저장하게 된다.

---

### 데이터 가져오기

```javascript
function getSavedBreed() {
	return localStorage.getItem('breed');
}
```

key 값을 통해 value를 가져온다.

---

### 특정 데이터 삭제

```javascript
function removeBreed() {
	return localStorage.removeItem('breed');
}
```

---

localStorage의 유일한 단점은 데이터가 반드시 문자열이어야 한다는 점이다. localStorage에 배열이나 객체는 저장할 수 없다. 다행히 이 단점을 쉽게 해결할 수 있다. 저장할 때는 `JSON.stringify()`를 이용해 데이터를 문자열로 변환하고, 다시 가져올 때는 `JSON.parse()`를 이용해 자바스크립트 객체로 변환하면 된다.

```javascript
function saveArray(arr) {
	const make_array_to_string = JSON.stringify([...arr]);
	localStorage.setItem('arr', make_array_to_string);
}
```

```javascript
function getArray() {
	return JSON.parse(localStorage.getItem('arr'));
}
```

---

### localStorage 전체를 삭제

```javascript
function clearPreferences() {
	localStorage.clear();
}
```

---

### localStorage와 sessionStorage의 차이

이 둘은 저장하는 데이터의 영구성에 차이를 가진다.

localStorage에 저장한 데이터는 개발자나 사용자가 직접 지우지 않는 이상 사라지지 않는다. 새로고침을 눌러도 사라지지 않고 브라우저를 아예 껏다가 켜도 사라지지 않는다. 

sessionStorage는 반면 새로고침을 눌렀을 때는 사라지지 않지만, 브라우저를 껏다가 다시 키면 사라진다.

때문에 저장하는 데이터가 어느 정도의 영구성이 필요한지를 판단하고 그에 맞는 곳에 적절히 저장해서 사용하면 되겠다.

---

## 참고문헌

자바스크립트 코딩의 기술 (길벗) 