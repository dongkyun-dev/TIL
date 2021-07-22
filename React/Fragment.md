# Fragment

> https://reactjs-kr.firebaseapp.com/docs/fragments.html

---

React의 일반적인 패턴은 컴포넌트가 여러개의 요소를 반환하는 것입니다. Fragments를 사용하면 DOM에 별도 노드를 추가하지 않고 자식 목록을 그룹화할 수 있습니다.

리액트 공식문서에서 설명하는 Fragment이다.

보통 리액트에서 여러 요소들을 그룹핑할 때 사용한다.

`<React.Fragment></React.Fragment>` 로 감싸기도하고, 내부의 내용을 생략하고 `<></>` 이런 식의 사용 또한 가능하다.

---

### 제일 중요한 것은!!

Fragment를 사용하면 DOM에 별도 노드를 추가하지 않고 자식 목록을 그룹화 한다는 점이다. 보통의 경우 이러한 동작이 문제를 일으키지 않지만, `justify-content`와 같이 자식 노드들 조작하는 CSS 요소들을 사용할 때는 문제를 일으킬 수 있다. 

실제로 자식 노드가 된 것이 아니기 때문에 `justify-content`가 작동하지 않는 것이다.

때문에 명백히 부모와 자식 간의 관계가 필요한 경우에는 Fragment가 아닌 div와 같은 명시적인 태그를 사용해야 한다.