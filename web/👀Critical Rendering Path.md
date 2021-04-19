# 👀Critical Rendering Path

> *성능을 최적하려면 HTML, CSS 및 자바스크립트 바이트를 수신한 후 렌더링된 픽셀로 변환하기 위해 필요한 처리까지, 그 사이에 포함된 중간 단계에서 어떠한 일이 일어나는지를 파악하기만 하면 됩니다. 이러한 단계가 바로 Critical Rendering Path(주요 렌더링 경로)입니다.*
>
> 이 문서의 모든 내용은 기본적으로 구글의 개발자 문서를 바탕으로 작성합니다. 이해하기 어려운 부분들은 다른 블로그들의 친절한 설명들을 인용할 예정이며 모두 출처를 밝히고 사용하겠습니다. 
>
>  https://developers.google.com/web/fundamentals/performance/critical-rendering-path

---

먼저, Critical Rendering Path는 크게 다섯개의 단계로 구분할 수 있다.

1. HTML 마크업을 처리하고 DOM 트리를 빌드한다.
2. CSS 마크업을 처리하고 CSSOM 트리를 빌드한다.
3. DOM 및 CSSOM을 결합하여 렌더링 트리를 형성한다.
4. 렌더링 트리에서 레이아웃을 실행하여 각 노드의 기하학적 형태를 계산한다.
5. 개별 노드를 화면에 페인트 한다.

주요 렌더링 경로를 최적화하는 작업은 위의 1~5단계를 수행할 때 필요한 총 시간을 최소화시키는 프로세스이다.

---

## 1. HTML 마크업을 처리하고 DOM 트리를 빌드한다.

>  기본적으로 HTML 마크업을 처리한 결과인 DOM과, CSS 마크업을 처리한 결과인 CSSOM은 서로 독립적인 데이터 구조라는 사실을 명심해야 한다.

http 혹은 https 통신을 통해 우리는 바이트 형태의 HTML 파일을 가져오게 된다. 이후 이 바이트를 문자로, 문자를 토큰으로, 토큰을 노드로, 노드를 객체 모델로 전환하는 작업을 수행해야 한다, (**바이트 → 문자 → 토큰 → 노드 → 객체 모델**)

이 과정을 조금 더 쉽게 설명하기 위해 구글 개발자 문서의 코드와 그림을 사용한다.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet">
  </head>
  <body>
    <p>Hello <span>web performance</span> students!</p>
    <div><img src="awesome-photo.jpg"></div>
  </body>
</html> 
```

![DOM](../assets/img/DOM.png)

1. 변환: 브라우저가 HTML의 원시 바이트를 디스크나 네트워크에서 읽어와서, 해당 파일에 대해 지정된 인코딩(ex. UTF-8)에 따라 개별 문자로 변환한다.

2. 토큰화

   >![token](../assets/img/token.png)
   >
   >토큰화 과정을 잘 보여주는 그림이다.
>
   >초기 상태는 "자료" 이다. `<` 문자를 만나면 상태는 "태그열림" 으로 변한다. 이후 a부터 z까지의 문자를 만나면 "태그이름" 상태로 변하게 되고 이 상태는 `>` 문자를 만날 때까지 유지한다. 
   >
   >`>` 문자에 도달하면 현재 토큰이 발행되고 상태는 다시 "자료" 로 돌아간다. 이후 다시 문자들을 소비하면서 문자 토큰이 생성되고 발행될 것이다. 이 과정은 종료 태그의 `<` 문자를 만날 때까지 진행된다.
   >
   >`<` 문자에 도달하면 다시 "태그열림" 상태가 된다. `/` 문자는 종료 태그 토큰을 생성하고 "태그이름" 상태로 변경된다. 이 상태는 `>` 문자를 만날 때까지 유지된다. 
   >
   >`>` 문자를 만나게 되면 다시 새로운 토큰이 발행되고 "자료" 상태가 된다. 
   >
   >이후 과정은 반복이다.

3. 렉싱: 방출된 토큰은 해당 속성 및 규칙을 정의하는 '객체'로 변환된다.
   
4. DOM 생성: 마지막으로, HTML마크업이 여러 태그 간의 관계를 정의하기 때문에 생성된 객체는 트리 데이터 구조 내에 연결된다. 이 트리 데이터 구조에는 원래 마크업에 정의된 상위-하위 관계도 포함된다. 즉, `HTML` 객체는 `body` 객체의 상위이고, `body`는 `paragraph `객체의 상위인 식이다.




**이 전체 프로세스의 최종 출력이 해당 페이지의 DOM이며, 브라우저는 이후 모든 페이지 처리에 이 DOM을 사용한다.**

---

## 2. CSS 마크업을 처리하고 CSSOM 트리를 빌드한다.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet">
  </head>
  <body>
    <p>Hello <span>web performance</span> students!</p>
    <div><img src="awesome-photo.jpg"></div>
  </body>
</html> 
```

위에서 사용한 HTML 파일을 다시본다. 코드의 흐름은 위에서 아래이다. html 태그를 거치고, head 태그를 거쳐서, meta에 대한 내용을 확인하고, 그 이후에 외부 CSS 스타일시트인 style.css를 참조하는 코드를 만나게 된다. 이때, 브라우저는 페이지를 렌더링하는데 이 리소스가 필요할 것이라 판단하게 된다. 때문에 HTML 파일에 대한 DOM 구성을 잠시 멈추고 CSSOM을 구성하기 시작한다. 

바로 이 순간을 `Render-Blocking CSS`라고 부른다. 이 `Render-Blocking CSS`는 렌더링하는데 필요한 시간을 길게 만든다. 때문에 이 부분에 대한 최적화가 필요하고 이 문서의 마지막 부분쯤에서 다룰 예정이다.

HTML과 마찬가지로, 수신된 CSS 규칙을 브라우저가 이해하고 처리할 수 있는 형식으로 변환해야 한다. 따라서 HTML 대신 CSS에 대해 HTML에서 했던 프로세스를 반복한다.

```css
body { font-size: 16px }
p { font-weight: bold }
span { color: red }
p span { display: none }
img { float: right } 
```

만일 `style.css`의 내용이 위와 같다면, 아래와 같은 CSS Object Model(CSSOM)이 만들어진다.

![CSSOM](../assets/img/CSSOM.png)

   CSSOM이 트리 구조를 가지는 이유는 무엇일까? 페이지에 있는 객체의 최종 스타일을 계산할 때 브라우저는 해당 노드에 적용 가능한 가장 일반적인 규칙( ex. body 요소의 하위에 존재하는 모든 요소에 body 요소의 스타일을 적용)으로 시작한 후 더욱 구체적인 규칙을 적용하는 방식으로, 즉 '하향식'으로 규칙을 적용하는 방식으로 계산된 스타일을 재귀적으로 세분화한다.

위의 코드와 그림을 통해 어떤 식으로 스타일이 적용되는지 쉽게 이해할 수 있다.

---



   https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction?hl=ko



https://boxfoxs.tistory.com/408

https://www.notion.so/Critical-Rendering-Path-1-f497d63b474e4c5aab03c92a83e3e652

https://d2.naver.com/helloworld/59361

https://seolhun.github.io/contents/web-%EC%9B%B9%EC%82%AC%EC%9D%B4%ED%8A%B8%EC%9D%98-%EC%86%8D%EB%8F%84%EB%A5%BC-%EA%B0%9C%EC%84%A0%ED%95%A0-%EC%88%98-%EC%9E%88%EB%8A%94-%EB%B0%A9%EB%B2%95-9-%EA%B0%80%EC%A7%80-%EC%A0%95%EB%A6%AC-part-2

위에 이거 되게 중요!!

https://swimfm.tistory.com/entry/PageSpeed-Insights%EC%9D%98-%EB%A0%8C%EB%8D%94%EB%A7%81-%EC%B0%A8%EB%8B%A8-%EB%A6%AC%EC%86%8C%EC%8A%A4%EB%A5%BC-%EC%A0%9C%EA%B1%B0%ED%95%B4%EB%B3%B4%EC%9E%90

이것도 중요