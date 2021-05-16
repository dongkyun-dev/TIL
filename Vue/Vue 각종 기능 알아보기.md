# Vue 각종 기능 알아보기

> script 안의 코드로 인해 해당 값이 바뀔 가능성이 존재하는 경우 html 태그 부분에서 바인드(:)를 한다고 생각하면 편할 것 같다.

---

### ref로 돔 조작하기

```html
<template>
  <h1>{{ title }}</h1>
  <input type="text" ref="name">
  <button @click="handleClick">Click me</button>
</template>

<script>
export default {
    name: "App",
    data() {
        return {
            title: 'My first Vue App'
        }
    },
    methods: {
        handleClick() {
            // 이런 식으로 this.$refs를 사용해서 DOM 조작을 할 수 있다.
            // input DOM의 ref를 name으로 지정했기 때문에 this.$refs.name
            // 으로 접근하는 것이다.
            this.$refs.name.classList.add('active') 
            this.$refs.name.focus() 
        }
    }
};
</script>

<style>
</style>
```

---

### 직접 구현한 modal

```html
<template>
  <div class="backdrop">
    <div class="modal">
      <p>Modal Content</p>
    </div>
  </div>
</template>

<style>
.modal {
  width: 400px;
  padding: 20px;
  margin: 100px auto; /* auto로 좌우 중앙 정렬 */
  background: white;
  border-radius: 10px;
}

.backdrop {
  top: 0;
  position: fixed;
  background: rgba(0, 0, 0, 0.5);
  width: 100%;
  height: 100%;
}
</style>
```

css 구성만 참고하면 나중에 직접 모달창을 구현할 때 쉽게 구현할 수 있을 듯.

---

### CSS 상속

만약 어떠한 vue 파일의 style 부분을 이렇게 구성했다고 가정하자.

```css
<style>
h1 {
    color: #03cfb4;
    border:none;
    padding: 0;
}
</style>
```

이렇게 되면 **global CSS**로 적용되어, 프로젝트 전체의 h1 태그에 해당 스타일이 적용된다. 

때문에 이러한 global CSS 대신 다른 방법을 사용해야 하는데 크게 2가지의 방법이 존재한다. 

```css
<style scoped>
h1 {
    color: #03cfb4;
    border:none;
    padding: 0;
}
</style>
```

1. scoped를 적용시킨다. - 이렇게 하면 해당 컴포넌트의 h1 태그에만 css가 적용된다.

```css
<style>
.modal h1 {
    color: #03cfb4;
    border:none;
    padding: 0;
}
</style>
```

2. 선택자를 명시한다. - 이렇게 하면 modal 클래스의 h1 태그에만 스타일이 적용된다.

대부분의 경우 해당 컴포넌트에서만 적용되기를 원하는 CSS 요소를 작성하지만, 가끔은 전역에 CSS를 적용시키고 싶을 때도 생긴다. 이때는 어떤 식으로 처리하는 것이 좋을까?

이럴 경우 `global.css` 파일을 하나 만드는 것이 좋다. 해당 파일에 전역으로 적용시키고자 하는 CSS 속성을 적고 `src/main.js`에 `import './assets/global.css'` 이런 식으로 `global.css`를 불러오는 식으로 사용하면 된다.

---

### Props

```html
// App.vue 

<template>
  <Modal header="Sign up for the Giveaway" />
</template>

<script>
import Modal from "./components/Modal.vue";

export default {
  name: "App",
  components: { Modal },
  data() {
    return {
      title: "My first Vue App",
    };
  },
};
</script>

<style>
</style>
```

```html
// Modal.vue 

<template>
  <h1>{{ header }}</h1>
</template>

<script>
export default {
  props: ["header"],
};
</script>

<style>
</style>
```

App.vue에서 props로 header 값을 넘겨주면, Modal.vue에서 header 값을 사용할 수 있게 된다.

하지만, 보통 이런 식으로 직접 값을 넣기 보다는 data 안의 값을 바인드해서 props로 보내는 경우가 많다. 이때는 바인드를 위한 콜론(:) 사용이 필요하다.

```html
// App.vue 

<template>
  <Modal :header="header" :text="text" />
</template>

<script>
import Modal from "./components/Modal.vue";

export default {
  name: "App",
  components: { Modal },
  data() {
    return {
      title: "My first Vue App",
      header: "Sign up for the Giveaway",
      text: "Grab your ninja~~"
    };
  },
};
</script>

<style>
</style>
```

```html
// Modal.vue 

<template>
  <h1>{{ header }}</h1>
  <p>{{ text }}</p>
</template>

<script>
export default {
  props: ["header", "text"],
};
</script>

<style>
</style>
```

props를 보내고 해당 데이터가 내가 원하는 데이터일 경우 class를 적용시키는 방법도 존재한다.

```html
// App.vue 

<template>
  <Modal :header="header" :text="text" theme="sale"/>
</template>

<script>
import Modal from "./components/Modal.vue";

export default {
  name: "App",
  components: { Modal },
  data() {
    return {
      title: "My first Vue App",
      header: "Sign up for the Giveaway",
      text: "Grab your ninja~~"
    };
  },
};
</script>

<style>
</style>
```

```html
// Modal.vue 

<template>
  <div :class="{sale: theme === 'sale'}">
    <h1>{{ header }}</h1>
    <p>{{ text }}</p>
  </div>
</template>

<script>
export default {
  props: ["header", "text", "theme"],
};
</script>

<style>
    .sale {
        padding: 20px;
    }
</style>
```

props로 온 theme의 값이 'sale' 이라면 sale이라는 class를 적용시키는 예제이다.

---

### Emitting Custom Event

부모에서 자식에게 데이터를 전달하는 법은 바로 위의 props에서 배웠다. 그렇다면, 자식에서 부모로 데이터를 전달하는 것도 가능할까? 안타깝게도 이는 불가능하다. 대신, 이벤트는 전달할 수 있는데 이를 Emit이라고 한다.

```html
// App.vue 

<template>
  <div v-if="showModal">
    <Modal :header="header" :text="text" theme="sale" @close="toggleModal" />
  </div>
  <button @click="toggleModal">open modal</button>
</template>

<script>
import Modal from "./components/Modal.vue";

export default {
  name: "App",
  components: { Modal },
  data() {
    return {
      title: "My first Vue App",
      header: "Sign up for the Giveaway",
      text: "Grab your ninja~~",
      showModal: false,
    };
  },
  methods: {
    toggleModal() {
      this.showModal = !this.showModal;
    },
  },
};
</script>

<style>
</style>
```

```html
// Modal.vue 

<template>
  <div :class="{ sale: theme === 'sale' }" @click="closeModal">
    <h1>{{ header }}</h1>
    <p>{{ text }}</p>
  </div>
</template>

<script>
export default {
  props: ["header", "text", "theme"],
  methods: {
    closeModal() {
      this.$emit("close");
    },
  },
};
</script>

<style>
.sale {
  padding: 20px;
}
</style>
```

먼저 Modal.vue를 본다. click 이벤트가 발생했을 때 closeModal 함수가 실행된다.

closeModal 함수 내부에는 `this.$emit()`이 있는데 이것이 emit 호출이다. 해당 호출의 이름으로 `close`를 사용한 것을 확인할 수 있다.

이제 다시 App.vue로 간다. App.vue의 <Modal/> 맨 마지막에 `@close="실행될 함수 이름"` 이 있는 것을 볼 수 있다. 즉, `@emit으로 보낸 이름="실행될 함수"`의 구조로 사용하는 것이다.

이런 식으로 코드를 구성하면 우리는 Modal.vue에서 일어난 클릭 이벤트를 상위의 App.vue로 전달할 수 있게 된다.

---

### 다양한 클릭 이벤트

```javascript
@click.right=""    // 오른쪽 클릭에만 반응
@click.shift=""    // shift를 누른 상태에서 클릭할 때만 반응
@click.self=""     // 내부의 자식 요소들이 존재하는 경우 클릭된 요소가 해당 엘리먼트와 일치하는 경우에만 바인딩된 함수를 호출한다. 다시 말해 이벤트 버블링 등의 간접적인 클릭 요소가 발생하여 함수 호출이 한 번이 아닌 다수가 일어나는 문제를 방지할 수 있다.
@click.once=""    // 클릭이 오직 한 번만 되어야 하는 경우
```

---

