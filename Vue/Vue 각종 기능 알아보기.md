

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

### signup form

```html
<template>
  <form @submit.prevent="handleSubmit">
    <label>Email:</label>
    <input type="email" required v-model="email" />

    <label>Password:</label>
    <input type="password" required v-model="password" />
    <div v-if="passwordError" class="error">{{ passwordError }}</div>

    <label>Role:</label>
    <select v-model="role">
      <option value="developer">Web Developer</option>
      <option value="designer">Web Designer</option>
    </select>

    <div class="terms">
      <input type="checkbox" v-model="terms" required />
      <label>Accept terms and com=ndition</label>
    </div>

    <div>
      <input type="checkbox" value="shaun" v-model="names" />
      <label>Shaun</label>
    </div>
    <div>
      <input type="checkbox" value="yoshin" v-model="names" />
      <label>Yoshin</label>
    </div>
    <div>
      <input type="checkbox" value="mario" v-model="names" />
      <label>Mario</label>
    </div>

    <div class="submit">
      <button>Create an Account</button>
    </div>
  </form>

  <p>Email: {{ email }}</p>
  <p>Password: {{ password }}</p>
  <p>Role: {{ role }}</p>
  <p>Terms accepted: {{ terms }}</p>
  <p>Names: {{ names }}</p>
</template>

<script>
export default {
  data() {
    return {
      email: "mario",
      password: "",
      role: "designer",
      terms: false,
      names: [],
      passwordError: "",
    };
  },
  methods: {
    handleSubmit() {
      // validate password
      this.passwordError =
        this.password.length > 5
          ? ""
          : "Password must be at least 6 chars long.";
      if (!this.passwordError) {
        // axios로 백엔드에 보내야지 데이터를
      }
    },
  },
};
</script>

<style>
form {
  max-width: 420px;
  margin: 30px auto;
  background: white;
  text-align: left;
  padding: 40px;
  border-radius: 10px;
}
label {
  color: #aaa;
  display: inline-block;
  margin: 25px 0 15px;
  font-size: 0.6em;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: bold;
}
input,
select {
  display: block;
  padding: 10px 6px;
  width: 100%;
  box-sizing: border-box;
  border: none;
  border-bottom: 1px solid #ddd;
  color: #555;
}
input[type="checkbox"] {
  display: inline-block;
  width: 16px;
  margin: 0 10px 0 0;
  position: relative;
  top: 2px;
}
button {
  background: #0b6dff;
  border: 0;
  padding: 10px 20px;
  margin-top: 20px;
  color: white;
  border-radius: 20px;
}
.submit {
  text-align: center;
}
.error {
  color: #ff0062;
  margin-top: 10px;
  font-size: 0.8em;
  font-weight: bold;
}
</style>
```

되게 깔끔하고 이쁜 signup form이다. 이거를 바탕으로 원하는 방향으로 조금씩 수정하면 더 좋은 결과물을 얻을 수 있을 것 같다.

---

### Router

```html
<template>
  <div id="nav">
    <router-link to="/">Home</router-link>
    <router-link :to="{ name: 'About' }">About</router-link>
  </div>
  <router-view />
</template>

<script>
export default {};
</script>

<style>
</style>
```

첫번째 라우터 링크처럼 일반 경로로 사용할 수도 있지만, 바인드를 시키면 컴포넌트의 이름을 활용해서 라우터를 사용할 수도 있다.

---

### Dynamic Routing

```html
/jobs/1
/jobs/2
/jobs/3
```

만약, 이런 식으로 뒤에 id 값이 붙는 경우의 라우트는 어떻게 하는 지에 대해 궁금할 수 있다.

이런 경우 다음과 같이 라우터를 구현해주면 된다.

```javascript
// /src/router/index.js

const routes = [
	{
		path: '/jobs',
		name: 'Jobs',
		component: Jobs
	},
	{
		path: '/jobs/:id',
		name: 'JobsDetails',
		component: JobDetails,
         props: true,
	}
]
```

```html
// /src/views/jobs/JobDetails.vue

<template>
  <h1>Job Detail Page</h1>
  <p>The job is {{ $route.params.id }}</p>
</template>

<script>
export default {
	props: ['id'], 
};
</script>

<style>
</style>
```

```html
// /src/views/jobs/Jobs.vue

<template>
  <h1>Jobs</h1>
  <div v-for="job in jobs" :key="job.id">
    <router-link :to="{ name: 'JobDetails', params: { id: job.id } }">
      <h2>{{ job.title }}</h2>
    </router-link>
  </div>
</template>

<script>
export default {
  data() {
    return {
      jobs: [
        { title: "UX designer", id: 1, details: "lorem" },
        { title: "Web designer", id: 2, details: "lorem" },
        { title: "Vue designer", id: 3, details: "lorem" },
      ],
    };
  },
};
</script>

<style>
</style>
```

우리가 라우터를 설정할 때 `props: true`값을 주었다. 이를 통해 해당 라우터는 props가 있다는 것을 미리 알고 있고 때문에 실제 해당 페이지에서 `props: ['id']`로 손쉽게 url 파라미터 값을 가져올 수 있다.

---

### Router redirect

```javascript
const routes = [
	{
		path: '/all-jobs',
         redirect: '/jobs'
	},
]
```

이런 식으로 라우터를 설정해두면 `/all-jobs`주소 요청을 바로 `/jobs`주소 요청으로 바꿔준다.

---

### 존재하지 않는 페이지로의 접근(404에러)

```javascript
const routes = [
	{
	    path: '/:catchAll(.*)',
        name: 'NotFound',
        component: NotFound 
	},
]
```

```html
// /src/views/NotFound.vue

<template>
	<h2>404</h2>
	<h3>Page not found</h3>
</template>
```

---

### 앞으로가기, 뒤로가기, 홈으로가기 버튼

```html
<template>
  <button @click="redirect">Redirect</button>
  <button @click="back">Go back</button>
  <button @click="forward">Go forward</button>
</template>

<script>
export default {
  methods: {
    redirect() {
      this.$router.push({name: 'Home'})
    },
    back() {
      this.$router.go(-1)
    },
    forward() {
      this.$router.go(1)
    }
  }
}
</script>

<style>

</style>
```

이런 식으로 앞으로 가기, 뒤로 가기, 홈으로 가기 버튼을 구현해볼 수도 있다.

---

### fetch data

```html
<template></template>

<script>
export default {
  data() {
    return {
      jobs: [],
    };
  },
  mounted() {
    fetch("http://localhost:3000/jobs")
      .then((res) => res.json())
      .then((data) => this.jobs = data)
      .catch((err) => {
        console.log(err);
      });
  },
};
</script>

<style>
</style>
```

React Hook에서의 useEffect가 mounted라고 보면 될 것 같다. 때문에 mounted 내부에서 fetch(or axios)를 수행한다.

---

### Conditionally showing data

React에서 useEffect를 활용할 때도 그랬지만, useEffect와 mounted는 DOM tree가 완성된 이후에 실행된다. 즉, useEffect 혹은 mounted 속에 api 통신이 있는 경우 해당 데이터가 없는 상태에서 DOM tree를 만들고 렌더하다보니 에러가 날 수 있다. 떄문에 우리는 Conditionally showing 기법을 사용할 필요가 있다.

```html
<template>
  <h1>Jobs</h1>
  <div v-if="jobs.length">
  	<div v-for="job in jobs" :key="job.id">
    	<router-link :to="{ name: 'JobDetails', params: { id: job.id } }">
      	<h2>{{ job.title }}</h2>
    	</router-link>
  	</div>
  </div>
  <div v-else>
  	<p>Loading...</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      jobs: [],
    };
  },
  mounted() {
  	fetch('http://localhost:3000/jobs')
  		.then(res => res.json())
  		.then(data => this.jobs = data)
  		.catch(err => console.log(err))
  }
};
</script>

<style>
</style>
```

배열일 때는 `.length` 속성을 활용해 데이터를 받아오는 작업이 끝났는지 확인하지만, 객체나 일반 데이터인 경우 그냥 해당 이름만 넣으면 될 것이다.

핵심은 Vue의 mounted === React의 useEffect 이다!
