# ⚪️ CSS 애니메이션

---



GIF

> 최대 256 색상을 지원하며 비 손실 압축 포맷으로 구현이 간편함. 압축률이 낮아 이미지 용량이 크고, 초당 20프레임이 넘을 경우 브라우저 성능 저하 발생. 웹에서의 사용을 지양해야 한다. 아이콘 혹은 로딩 애니메이션 정도에서 사용하기 좋다.

MP4

> 손실 압축 포맷으로 용량이 적고 스트리밍 재생이 가능하다. 알파 채널이 지원되지 않아 배경색을 포함한 콘텐츠를 제작해야 한다.

CSS & JS

> 인터렉션 UI 애니메이션에 적합하고, 부드러운 애니메이션 제공. 복잡하고 현란한 애미네이션 제작 어려움. CSS를 활용하여 애니메이션을 만드는 방법은 GPU 사용을 가속시켜 CPU 성능 개선에 도움을 줄 수 있다.

LOTTIE

> SVG 벡터 기반으로 선명하고 현란한 애니메이션 제작이 가능. After Effects 도구 사용으로 진입 장벽이 높음. 웹, 안드로이드, IOS 모두에서 사용이 가능하다.

|                 |      GIF       |         MP4         |         LOTTIE         |      CSS & JS       |
| :-------------: | :------------: | :-----------------: | :--------------------: | :-----------------: |
|      용량       |      많음      |        보통         |          적음          |        적음         |
|      품질       |      나쁨      |        보통         |       좋음(SVG)        |        좋음         |
|   구현 난이도   |      쉬움      |       어려움        |         어려움         |        쉬움         |
| 알파 채널(투명) |      지원      |        불가         |          지원          |        지원         |
|  브라우저 성능  |      나쁨      |        좋음         |          보통          |      좋음(CSS)      |
|    적합한 UI    | 아이콘 블릿 등 | 대용량, 긴 재생시간 | 복잡한 벡터 애니메이션 | 인터렉션 UI, 시퀀스 |

---

## Transition

> CSS 속성을 Transform(변형)할 때 애니에미션 속도 조절을 위해 transition(화면 이동)을 사용

### 원리

1. 적용될 대상은 초기/종료 상태의 스타일과 Transition 정의가 필요
2. 애니메이션 발생을 위한 가상 선택자(:hover 등) 또는 js 트리거가 필요

```html
<style>
  .box {
    /* 초기 상태 */
    display: block;
    width: 100px;
    height: 100px;
    margin: 20px;
    line-height: 100px;
    text-align: center;
    color: #fff;
    background-color: red;
  }
  
  .tr {
    /* transition 적용 */
    transition: 0.5s ease-out;
  }
  
  .tr:hover {
    /* 종료 상태 */
    width: 500px;
  }
</style>


<a href="#" class="box tr">transition</a>
```



### 속성

|            속성            |            설명            | 기본값 |
| :------------------------: | :------------------------: | :----: |
|    transition-property     |     CSS 속성을 지정함      |  all   |
|    transition-duration     |      실행 시간(필수)       |   0s   |
| transition-timing-function |      가속, 감속 설정       |  ease  |
|      transition-delay      |       실행 지연 시간       |   0s   |
|         transition         | 속기형 선언(한 줄 작성 시) |        |



```css
transition-property: none | all | property;

/* 속성 1개 이상 적용 가능 */
transition-property: background-color, width;
```

- none: 모든 속성에 적용하지 않음
- all (기본값): 모든 속성에 적용함
- property: 개별 속성을 적용함. 여러 개의 속성을 적용하는 경우 쉼표(,)로 구분



```css
transition-duration: time;

/* 초 단위(s), 밀리 초 단위(ms)로 적용 가능*/
transition-duration: .5s, 500ms;
```

- time: 기본값은 0s이므로 필수 입력해야 트랜지션을 실행함
- 시간 단위는 초(s) 또는 1/1000초(ms)를 사용



```css
transition-timing-function: ease | linear | ease-in | ease-out | ease-in-out | step-start, step-end, steps(n, start | end) | cubic-bezier(n, n, n, n);
```

- ease(기본값): 느리게 시작한 후 빠르게 가속되다가 다시 느려짐
- linear: 등속도 움직임으로 속도의 변화가 없음
- ease-in: 가속. 애니메이션이 느리게 시작한 후 빨라짐
- ease-out: 감속. 애니메이션이 빠르게 시작된 후 느려짐
- ease-in-out: ease와 유사하지만, 변화 정도가 ease처럼 급격하지 않음
- cubic-bezier(n, n, n, n): 3차 배지어 곡선은 정교한 제어 시 사용  [참고](https://easings.net/)
- steps(n, start | end): 시퀀스 이미지 제어 시 사용. steps(전체 프레임 수)

감속과 관련된 ease, ease-out, ease-in-out 이 부드러운 Animation을 만들기 적합하다.



```css
transition-delay: time;

transition-delay: .5s, 500ms;
```

- time: 기본값은 0s이다
- 시간 단위는 초(s) 또는 1/1000초(ms)를 사용



```css
transition: property duration timing-function delay;
```

- 구분은 띄어쓰기를 통해 한다. 쉼표(,)를 사용하지 않는다



### 예시

```css
/* 개별 트랜지션 선언 */
transition-property: width;
transition-duration: .5s;
transition-timing-function: ease;
transition-delay: 1s;

/* 트랜지션 속기형 (property duration timing-fn delay) */
transition: width .5s ease 1s;
```

```css
/* 트랜지션 속성 개별 선언 */
transtion-property: background-color, height, line-height;
/* 홀수 인덱스 프로퍼티에 적용할 값, 짝수 인덱스 프로퍼티에 적용할 값 두 개만 제시하는 것도 가능하다. */
transition-duration: .5s, 1s ;
transition-timing-function: linear, ease, ease;
transition-delay: .5s, 0s, .5s;

/* 속기형 */
/* 기본값인 ease, 0s는 명시하지 않아도 괜찮다 */
transition: background-color .5s linear .5s, height 1s, line-height .5s .5s;
```

---

## Animation

> 애니메이션은 CSS 스타일과 키프레임들로 구성되며, Transition보다 복잡하고 다양한 능력을 가지고 있기 때문에 좀 더 정밀한 효과를 구현 가능

### 원리

1. 적용될 대상은 @keyframes 규칙이 필요
2. transition과 다르게 트리거 없이도 실행 가능

```css
@keyframes ani {
  25% {
    top: 0;
    left: 200px;
  }
  50% {
    top: 200px;
    left: 200px;
  }
  75% {
    top: 200px;
    left: 0;
  }
  100% {
    top: 0;
    left: 0;
  }
}

animation: ani 4s infinite;
```



### 속성 

|           속성            |          설명           | 기본값  |
| :-----------------------: | :---------------------: | :-----: |
|      animation-name       |     @keyframes 이름     |         |
|    animation-duration     |     실행 시간(필수)     |   0s    |
| animation-timing-function |     가속, 감속 설정     |  ease   |
|      animation-delay      |     실행 지연 시간      |   0s    |
| animation-iteration-count |  애니메이션 재생 횟수   |    1    |
|    animation-direction    |  애니메이션 재생 방향   | normal  |
|    animation-fill-mode    | 애니메이션 종료 후 상태 |  none   |
|   animation-play-state    |  애니메이션 재생/정지   | running |
|         animation         |       속기형 선언       |         |



```css
animation-name: name;

/* 키프레임 이름 = 애니메이션 이름 */
@keyframes name {
  /* from(0%), to(100%) 또는 %로 작성 */
  from {
    width: 100px;
  }
  to {
    width: 300px;
  }
  25%, 75% {
    background-color: red;
  }
}

/* 유효하지 않은 애니메이션 이름 */
animation-name: 1name;  // 숫자로 시작하는 이름
animation-name: @name;  // 특수 문자로 시작하는 이름
```



```css
animation-duration: time | initial | inherit;

/* 초 단위(s), 밀리 초 단위(ms)로 적용 가능 */
animation-duration: .5s, 500ms;
```

- time: 기본값은 0s이므로 필수 입력해야 애니메이션이 실행됨
- 시간 단위는 초(s) 또는 1/1000초(ms)를 사용



```css
animation-timing-function: ease | linear | ease-in | ease-out | ease-in-out | step-start, step-end, steps(n, start | end) | cubic-bezier(n, n, n, n)
```

- transition-timing-function과 동일



```css
animation-delay: time;
```

- transition-delay와 동일



```css
animation-iteration-count: number | infinite;

/* 5회, 무한 반복 */
animation-iteration-count: 5, infinite;
```

- number: 기본값은 1. 설정한 횟수만큼 애니메이션 반복 재생 (소수값 설정 가능. 0.5 설정 시 50%에 해당하는 프레임까지 재생)
- infinite: 애니메이션을 무한으로 반복



```css
animation-direction: normal | reverse | alternate | alternate-reverse;
```

- normal(기본값): 순방향 재생. 끝나면 처음 프레임부터 다시 시작
- reverse: 역방향 재생. 끝나면 마지막 프레임부터 다시 시작
- alternate: 순방향과 역방향으로 번갈아 재생
- alternate-reverse: 역방향과 순방향으로 번갈아 재생



```css
animation-fill-mode: none | forwards | backwards | both;
```

- none(기본값): 애니메이션 100% 도달 후 시작 키프레임 이동하고 속성 초기화
- **forwards: 애니메이션 100% 도달 후 종료 키프레임 유지**
- backwards: none과 동일하나 시작 키프레임 설정을 미리 적용(delay 적용 시)
- both: forwards, backwards 둘다 적용



```css
animation-play-state: running | paused;
```

- running(기본값): 애니메이션 재생
- paused: 애니메이션 정지



```css
animation: name | duration | timing-function | delay | iteration-count | direction | fill-mode | play-state;
```

- 개별 속성을 한줄로 축약해서 사용가능



```css
@keyframes name_color {
  0% {
    background-color: red;
  }
  100% {
    background-color: blue;
  }
}

@keyframes name_width {
  from {
    width: 100px;
  }
  to {
    width: 300px;
  }
}

/* 권장 순서: name duration timing-fn delay iteration-count derection fill-mode play-state */
animation: name_color 1s linear 1s, name_width 1s ease-out infinite;
```

---

### 참고문헌

https://www.boostcourse.org/web344













