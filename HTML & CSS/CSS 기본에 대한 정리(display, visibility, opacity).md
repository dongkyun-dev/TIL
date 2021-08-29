# CSS 기본에 대한 정리(display, visibility, opacity)

---

### display 프로퍼티

| 프로퍼티값 키워드 | 설명                                          |
| ----------------- | --------------------------------------------- |
| block             | block 특성을 가지는 요소로 지정               |
| inline            | inline 특성을 가지는 요소로 지정              |
| inline-block      | inline-block 특성을 가지는 요소로 지정        |
| none              | 해당 요소를 화면에 표시 X (공간조차 사라진다) |

---

### block 레벨 요소

- 항상 새로운 라인에서 시작한다
- 화면 크기 전체의 가로폭을 차지한다 (width: 100%)
- width, height, margin, padding 프로퍼티 지정이 가능하다
- block 레벨 요소 내에 inline 레벨 요소를 포함할 수 있다

- block 레벨 요소 예
  - div
  - h1 ~ h6
  - p
  - ol
  - ul
  - li
  - hr
  - table
  - form

---

### inline 레벨 요소

- 새로운 라인에서 시작하지 않으며 문장의 중간에 들어갈 수 있다. 즉, 줄을 바꾸지 않고 다른 요소와 함께 한 행에 위치한다
- `content`의 너비만큼 가로폭을 차지한다
- `width`, `height`, `margin-top`, `margin-bottom` 프로퍼티를 지정할 수 없다. 상, 하 여백은 `line-height`로 지정한다
- inline 레벨 요소 뒤에 공백(엔터, 스페이스 등)디 있는 경우, 정의하지 않은 space(4px)가 자동 지정된다
- inline 레벨 요소 내에 block 레벨 요소를 포함할 수 없다. inline 레벨 요소는 일반적으로 block 레벨 요소에 포함되어 사용된다
- inline 레벨 요소 예
  - span
  - a
  - strong
  - img
  - br
  - input
  - select
  - textarea
  - button

---

### inline-block 레벨 요소

block과 inline 레벨 요소의 특징을 모두 갖는다. inline 레벨 요소와 같이 한 줄에 표현되면서 **width, height, margin 프로퍼티를 모두 지정할 수 있다.**

- 기본적으로 inline 레벨 요소와 흡사하게 줄을 바꾸지 않고 다른 요소와 함께 한 행에 위치시킬 수 있다
- block 레벨 요소처럼 width, height, margin, padding 프로퍼티를 모두 정의할 수 있다. 상, 하 여백을 margin과 line-height 두 가지 프로퍼티 모두를 통해 제어할 수 있다
- content의 너비만큼 가로폭을 차지한다
- inline-block 레벨 요소 뒤에 공백이 있는 경우, 정의하지 않은 space(4px)가 자동 지정된다. 

```css
.inline-block {
      display: inline-block;
      vertical-align: middle; /* inline-block 요소 수직 정렬 */
      border: 3px solid #73AD21;
      font-size: 16px;
}
```

