# ⚛️React-Modal

> https://www.npmjs.com/package/react-modal

---

react에서 모달창을 만들기 위한 패키지.

`$ npm install react-modal` 을 통해 설치가 가능하다.



```javascript
import React, { useState } from 'react'
import './App.css'
import Modal from 'react-modal'

// 이 부분을 달지 않아도 렌더링은 된다. 하지만, 콘솔창에 에러가 뜨게 된다. 때문에 넣어두는 것이 좋다.
Modal.setAppElement('#root')

function App () {
  const [modalIsOpen, setModalIsOpen] = useState(false)
  return (
    <div className='App'>
      <button onClick={() => setModalIsOpen(true)}>Open modal</button>
      <Modal
        // modalIsOpen의 값이 true가 되면 모달창이 열린다.
        isOpen={modalIsOpen}
          
        //onRequestClose 속성을 다음과 같이 적용하게 되면 모달의 외부를 클릭하거나, esc를 누름으로써 모달창을 닫을 수 있다. shouldCloseOnOverlayClick={false} 속성을 추가로 넣게 되면 외부를 클릭했을 때는 모달창이 닫히지 않지만, esc를 눌렀을 때는 원래처럼 닫힌다. 
        onRequestClose={() => setModalIsOpen(false)}
        
        // style을 바꿀 수 있다는 점이 가장 큰 장점이지 않을까 싶다. http://reactcommunity.org/react-modal/styles/ 이 문서의 내용을 통해 내가 원하는 모양의 모달을 쉽게 만들 수 있다.
        style={{
          overlay: {
            backgroundColor: 'grey'
          },
          content: {
            color: 'orange'
          }
        }}
        // shouldCloseOnOverlayClick={false}
      >
        <h2>Modal title</h2>
        <div>Modal Body</div>
        <div>
          <button onClick={() => setModalIsOpen(false)}>Close</button>
        </div>
      </Modal>
    </div>
  )
}

export default App
```

