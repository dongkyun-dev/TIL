



---

### querySelector

querySelector는 HTML 태그를 가져와야할 때 사용한다.

```javascript
document.querySelector('선택자')

// ex1
const $input = document.querySelector('input')

// ex2
const $button = document.querySelector('button')
```

제로초님이 변수명 앞에 $를 붙이는 이유는, 해당 변수가 태그를 선택한 코드임을 명시적으로 구분하기 위해서라고 한다. (여러 개의 태그를 선택하는 경우에는 $$를 사용한다고 한다.)

```html
// 여러 개의 버튼 태그를 선택해야하는 경우

<button>버튼1</button>
<button>버튼2</button>
<button>버튼3</button>

<script>
	const $$buttons = document.querySelectorAll('button')
</script>
```

https://www.youtube.com/watch?v=uDEQEeIWLaU   3qns39ch