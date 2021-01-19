# Cookies vs Local Storage vs Session Storage

---

|                    | Cookies            | Local Storage | Session Storage |
| ------------------ | ------------------ | ------------- | --------------- |
| Capacity           | 4KB                | 10MB          | 5MB             |
| Browsers           | HTML4/HTML5        | HTML5         | HTML5           |
| Accessible from    | Any window         | Any window    | Same tab        |
| Expires            | Manually set       | Never         | On tab close    |
| Storage Location   | Browser and server | Browser only  | Browser only    |
| Sent with requests | Yes                | No            | No              |

[출처](https://www.youtube.com/watch?v=GihQAC1I39Q)

---



![View And Edit Local Storage With Chrome DevTools | Google Developers](Cookies vs Local Storage vs Session Storage.assets/localstorage.png)

로컬 스토리지(Local Storage)와 세션 스토리지(Session Storage)는 간단한 키와 값을 저장할 수 있는, HTML5에서 추가된 저장소이다.

로컬 스토리지와 세션 스토리지의 차이점은 데이터의 영구성이다. 로컬 스토리지의 데이터는 사용자가 지우지 않는 이상 계속 브라우저에 남아 있다. 하지만 세션 스토리지의 데이터는 윈도우나 브라우저 탭을 닫을 경우 제거된다.

지속적으로 필요한 데이터(자동 로그인)는 로컬 스토리지에 저장, 잠깐 동안만 필요한 정보(일회성 로그인 정보)는 세션 스토리지에 저장 한다. 이 또한 클라이언트단에서 저장되는 것이기 때문에 노출되어서는 안되는 정보(비밀번호)는 저장해서는 안된다.

하지만,로컬 스토리지와 세션 스토리지만이 브라우저에 저장소 역할을 하는 것은 아니다. 바로 쿠키가 존재한다. 

쿠키는 4KB의 용량 제한이 존재하며 매 서버 요청마다 서버로 쿠키가 같이 전송된다. 쿠키는 처음부터 서버와 클라이언트 간의 지속적인 데이터 교환을 위해 만들어졌기 때문에 서버로 계속 전송되는 것이다. 

**이게 문제가 되는 경우가 있다. 만약 4KB 용량 제한을 거의 다 채운 쿠키가 있다면, 요청을 할 때마다 기본 4KB의 데이터를 사용하게 되어버린다. 하지만, 이 4KB의 데이터 중에서는 서버에 필요하지 않은 데이터도 존재한다. 바로 이런 데이터들을 로컬 스토리지와 세션 스토리지에 저장하는 것이다. 이 두 저장소의 데이터는 서버로 자동 전송되지 않는다.**

---

출처))))

[제로초님의 사이트](https://www.zerocho.com/category/HTML&DOM/post/5918515b1ed39f00182d3048) - 프로그래밍을 잘하는 것보다 프로그래밍을 잘 가르치는 것이 더 많은 지식과 확신이 필요하기 때문에 훨씬 더 어려운 일이라 생각한다. 제로초님의 글들을 읽을 때면 얼마나 많은 지식과 확신에서 이런 좋은 글이 나오는지 가늠이 되지 않는다.   





다음글은 jwt 토큰

https://velopert.com/2350



