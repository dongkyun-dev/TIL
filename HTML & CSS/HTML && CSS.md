`<a href="https://www.naver,com"></a>`

> 공백은 X, 쌍따옴표를 쓴다.

시맨틱 태그(기능은 div와 같지만 의미론적 요소를 가지는 태그)

- header: 문서 전체나 섹션의 헤더
- nav: 내비게이션
- aside: 사이드에 위치한 공간, 메인 콘텐츠와 관련성이 적은 것
- section: 문서의 일반적인 구분, 컨텐츠의 그룹을 표현
- article: 문서, 페이지, 사이트 안에서 독립적으로 구분되는 영역(section 내부에 여러개의 article)
- footer: 문서 전체나 섹션의 마지막 부분

Non semantic 요소에는 div와 span등이 있으며 h1, table등의 태그들도 시맨틱 태그로 볼 수 있다.

form은 크게 action과 method로 구분된다.

CSS 적용 우선순위

> !important > 인라인 > id 선택자 > class 선택자 > 요소 선택자

CSS는 상속을 통해 부모 요소의 속성을 자식에게 상속할 수도 있다.

- 상속 되는 것 예시 - Text 관련 요소(font, color, text-align), opacity, visibility
- 상속 되지 않는 것 예시 - Box model 관련 요소, position 관련 요소

em - 부모 태그의 폰트 사이즈를 기준으로 상대적인 사이즈를 가짐

rem - 최상위 요소(html)의 폰트 사이즈를 기준으로 배수 단위를 가짐

```css
/*상하좌우 모두 10px*/
.margin-1{
	margin: 10px;
}

/*상하 10px, 좌우 20px*/
.margin-2{
	margin: 10px 20px;
}

/*상 10px, 좌우 20px, 하 30px*/
.margin-3{
	margin: 10px 20px 30px;
}

/*시계방향(상우하좌)*/
.margin-4{
	margin: 10px 20px 30px 40px;
}
```

기본적으로 모든 요소의 box-sizing은 content-box! 즉, Padding을 제외한 순수 contents 영역만을 box로 지정

다만, 우리가 일반적으로 영역을 볼 때는 border까지의 너비를 100px로 보는 것을 원한다.

**이 경우 box-sizing을 border-box로 설정한다.**

---

## display

- block
  - 줄 바꿈이 일어나는 요소
  - 화면 크기 전체의 가로 폭을 차지한다.
  - 블록 레벨 요소 안에 인라인 레벨 요소가 들어갈 수 있다.
  - ex) div, ul, ol, li, p, hr, form
- inline
  - 줄 바꿈이 일어나지 않는 행의 일부 요소
  - content 너비만큼 가로 폭을 차지한다.
  - width, height, margin-top, margin-bottom을 지정할 수 없다.
  - ex) span, a, img, input, label, b, em, i, strong
- inline-block
  - block과 inline 레벨 요소의 특징을 모두 갖는다.
  - inline 처럼 한 줄에 표시 가능하며, block처럼 width, height, margin 속성을 모두 지정할 수도 있다.
- none
  - 해당 요소를 화면에 표시하지 않는다.
  - 이와 비슷한  `visibility: hidden`은 해당 요소가 공간은 차지하나 화면에 표시만 하지 않는다.

---

## 정렬

- 좌측에 붙이기 - margin-right: auto; text-align: left;
- 오른쪽에 붙이기 - margin-left: auto; text-align-right;
- 중앙 정렬 - (margin-right: auto; margin-left: auto;), text-align: center;

---

## CSS position

- static: 디폴트 값 보통 좌측 상단
- relative: static 위치를 기준으로 이동
- absolute: static이 아닌 가장 가까이 있는 부모를 기준으로 이동
- fixed: 부모 요소와 관계없이 고정 위치 스크롤시에도 항상 같은 곳에 위치

https://electronic-moongchi.tistory.com/26

---

## Float

> Float된 이미지 좌, 우측 주변으로 텍스트를 둘러싸는 레이아웃을 위해 도입

Float을 적용하게 되면 위로 살짝 뜨게 되다 보니 밑에 블록 요소가 위로 올라오게 됨.

이를 막기 위해서

```css
.float된 요소의 선택자{
	content: "";
	display: block;
	clear: both;
}
```

이러한 코드가 필요하다.

---

## Flex

가장 먼저 부모 요소에 display: flex 혹은 inline-flex를 적용하는 것부터 시작한다.

- 배치 방향 설정: flex-direction
- 메인축 방향 정렬: justify-content
- 교차축 방향 정렬: align-items, align-self

### flex-direction

- row(default)
- row-reverse
- column
- column-reverse

### justify-content

- flex-start
- flex-end
- center
- space-between
- space-around
- space-evenly

### align-items

- flex-start
- flex-end
- center

### align-self

- auto
- flex-start
- flex-end
- center

---

- 브라우저 `<html>`의 root 글꼴 크기는 16px

 ```css
.mx-auto /*x축 정렬*/

.mx-auto{
	margin-right: auto !important;
	margin-left: auto !important;
}

.py-0 /*y축의 패딩을 0*/

.py-0{
	padding-top: 0 !important;
	padding-bottom: 0 !important;
}
 ```



- t - top
- b - bottom
- s - left
- e - right
- x - left, right
- y - top, bottom

---

## Grid system

- bootstrap grid system은 flexbox로 제작되었다.
- container, rows, column으로 컨텐츠를 배치하고 정렬한다.
- 12개의 column을 나눠가지고, 6개의 grid breakpoints가 존재한다.

