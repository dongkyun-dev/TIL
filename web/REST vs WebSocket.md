# 💥REST vs WebSocket

---

> https://www.educba.com/websocket-vs-rest/
>
> 이 사이트에 대한 번역을 중심으로 글을 써나갑니다. 오역 혹은 제 생각이 중간중간 들어가 있을 수 있기 때문에 보다 더 정확한 정보를 얻기 위해서는 원문을 읽는 것을 추천합니다.

웹소켓은 TCP 연결을 통한 point-to-point 연결을 허용하는 통신 프로토콜이다. 웹소켓 프로토콜은 일반적인 소켓의 확장판이며, 서버와 양방향 통신을 할 때 굉장히 유용하다.(여기서의 양방향 통신은 단순히 하나의 서버와 하나의 브라우저만의 연결을 의미하는 것이 아닌, 여러 개의 브라우저와 서버의 연결 상태를 의미하는 것 같다. 즉, 다른 브라우저에서 서버에 어떠한 데이터를 보낸다면 그 데이터가 바로 내 브라우저에서도 반영이 돼야 한다.)

<br/>

## WebSocket

- 웹소켓 프로토콜은 HTTP에서는 제공할 수 없는 양방향 통신(full-deplex communication)을 제공합니다.
- 웹소켓 프로토콜은 기존 웹의 보안 시스템과 충돌하지 않습니다.
- 모든 웹소켓 handshake 통신은 브라우저에 의해 면밀히 조사될 수 있습니다.
- 웹소켓은 단일 TCP connection이르모 연결 제한 문제를 제거합니다.

## Rest

- REST 방식은 가장 보편적인 방식입니다.
- stateless한 특징을 가지고 있습니다. 이 특징 덕분에 서버에서는 클라이언트의 state를 가질 수도, 가질 필요도 없습니다. 이는 자원의 사용을 줄입니다.
- 클라이언트에서 HTTP 통신을 통해 get, post, put, delete(CRUD) 등의 명령을 서버에 보내면 서버는 해당 명령어의 수행 결과만을 반환하는 단방향 통신을 제공합니다.
- Rest 방식은 API를 디자인하는 가장 보편적인 방식입니다.
- REST 구조에서는 클라이언트와 서버가 서로에게 알리지 않고 각자의 명령들을 수행할 수 있습니다. (클라이언트가 A라는 명령어를 실행. 클라이언트 사이드에서는 당연히 A가 실행되었음을 인지하지만, 서버는 클라이언트에서 A가 실행되었는지 모름.) 이러한 특징은 굉장히 큰 장점입니다. 클라이언트는 서버에 영향을 주지 않고, 언제든지 자신의 값들을 수정할 수 있습니다. 



## REST와 WebSocket의 가장 큰 차이!

## #1. HTTP

WebSocket 

> 가장 처음에 서버와 연결을 수행 할 때만 HTTP 통신을 사용합니다.(처음에 연결하고 연결된 상태를 계속해서 유지하는 거지.)

REST

> HTTP 통신을 계속해서 사용합니다.

<br/>

## #2. Scenario

WebSocket

>Real time chat Application

REST

>Lots of GET request

<br/>

## 3.Dependency

WebSocket

>Rely on IP address and Port Number

REST

>Based on HTTP protocol and uses HTTP methods to rely data

<br/>

## 4. Cost

WebSocket

>Cost of communication is lower

Rest

>Cost of communication is comparatively higher than WebSocket.

여기서 들었던 의문. Rest 방식에서는 딱 필요할 때만 서버에 데이터를 보내기 때문에 연결 상태를 유지할 필요가 없지만, WebScoket은 계속해서 연결을 유지해야하니까 상대적으로 더 많은 비용이 드는 것이 아닌가? 

> 아니다. 바로 HandShake 부분 때문. WebSocket은 처음 연결할 때 단 한번의 HandShake만을 수행한다. 하지만, Rest는 특정 데이터를 보낼때 마다 HandShake를 수행해야 한다. 이 HandShake 과정은 꽤 많은 비용이 든다. 수많은 헤더들과 쿠키 데이터들을 보내야하기 때문에 HTTP HandShake에서는 several kilobytes의 비용이 발생한다.
>
> https://stackoverflow.com/questions/14703627/websockets-protocol-vs-http (굉장히 자세하고 좋은 설명이 되어 있다.)



