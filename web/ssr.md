# 🔴CSR vs SSR

> 항상 사람들의 이야기를 듣다보면 SSR 개념을 강조하면서 Next.js를 배울 것을 추천한다. 그러다보니 SSR이 도대체 무슨 개념이길래 저렇게 추천하는거지? 라는 생각이 들어 정리해본다.

### ![img](../assets/img/ssr_and_csr_3.png)

## CSR(Client-Side-Rendering)

- CSR은 최초 로딩 시에 HTML, JS, CSS를 비롯한 static 파일들 전부를 받아온다.
- 최초에 모든 것을 불러와야하기 때문에 처음에는 느리지만, 그 이후 요청들에 대해서는 이미 다 불러온 상태이기 때문에 굉장히 빠른 속도를 가진다. 또한, 렌더링이 클라이언트 사이드에서 일어난다.
- 서버에 요청하는 횟수가 훨씬 적기 때문에 서버 부담이 덜하다.(처음에 웬만한 것들은 전부 가져오기 때문에 서버에 요청을 보낼 일이 상대적으로 적다.)
- SPA(말 그래도 단 하나의 HTML 파일을 기반으로 자바스크립트를 이용해 동적으로 화면을 이리저리 바꾸는 방식의 웹 어플리케이션)가 사용하는 렌더링 방식이 CSR이다. 그렇다고 SPA와 CSR이 같은 개념인 것은 아니다. 

<hr/>

## SSR(Server-Side-Rendering)

- 전통적인 웹 페이지 방식이 SSR 방식을 사용한 것이다.
- View를 서버에서 렌더링 하여 가져오기 때문에 첫 로딩이 굉장히 짧다.
- 매번 페이지를 요청 할 때마다 새로고침 되기 때문에 사용자 UX가 떨어진다.
- 서버에 매번 요청을 해야 하기 때문에 트래픽, 서버 부하가 커진다.

---

# ❓그렇다면 왜!? SSR을 써야 하는가????



###   SEO(검색 엔진 최적화) 문제 때문

> 서버사이드에서 렌더링한 파일들을 개발자도구로 검사해서 봤을 경우에 글자들이 그대로 긁힌다. 하지만, SPA에서 만든 홈페이지들을 보면 이 모든게 bundle.js에 들어있고 이를 읽어와서 사용하는 방식이다. 아무래도 한 번 더 절차를 거치기 때문에 검색 엔진에 잘 노출되지 않는다.

<br/>

### 보안 문제 때문

> SSR에서는 사용자에 대한 정보를 세션 즉 서버 상에서 관리할 수 있다.CSR의 경우 쿠키나 로컬스토리지에 사용자에 대한 정보를 저장해야 하는데 이는 XSS 공격에 취약하다.([XSS 공격에 대한 설명](https://4rgos.tistory.com/1))

<br/>

### SSR을 사용하면 프론트엔드와 백엔드 영역을 REST API를 통해 느슨하게 연결할 수 있다.

> 기존에 CSR 페이지는 프론트엔드에서 개발하고 SSR 페이지는 백엔드에서 개발을 했다면, SSR 환경을 구축하면 페이지의 소유권이 온전히 프론트엔드에 존재하므로 페이지가 변경될 때마다 불필요한 통신을 할 필요가 없어진다.



---

## @참고문헌

https://mygumi.tistory.com/81



https://d2.naver.com/helloworld/7804182 (설명이 굉장히 자세하고 구체적이다. 꼭 읽어보자.)



https://goodgid.github.io/Server-Side-Rendering-and-Client-Side-Rendering/