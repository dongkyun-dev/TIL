

# 🤞querySelector

---

> https://www.youtube.com/watch?v=uDEQEeIWLaU   
>
> 제로초님의 강의를 정리했습니다.

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

```html
// 여러 개의 버튼 태그가 있는데 querySelector를 쓰는 경우

<button>버튼1</button>
<button>버튼2</button>
<button>버튼3</button>

<script>
	// 가장 먼저 나오는 button tag(버튼1)가 선택된다.
	const $$buttons = document.querySelector('button')
</script>
```

```html
// id가 order인 span 태그를 가져오고 싶다면?

<div><span id="order">1</span>번째 참가자</div>

<script>
    // id는 유니크하기 때문에 span#word로 할 필요가 없다.
	const $span = document.querySelector('#order')
    
    // 자손 선택자(띄어쓰기)를 이용해서 선택할 수도 있다. div의 자손인 span 태그를 찾는다.
    const $span = document.querySelector('div span')
    
    // 이 경우에는 바로 span이 div의 바로 한단계 아래이다. 때문에 div의 자식인 span 태그를 찾아도 된다.
    const $span = document.querySelector('div>span')
</script>
```

```html
// class가 btn인 button 태그를 가져오고 싶다면?

<button class="btn">버튼!</button>

<script>
	document.querySelector('.btn')
</script>
```



