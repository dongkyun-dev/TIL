# vue with typescript

---

### 참고할만한 코드

```html
<template>
  <div>
    <router-link to="/">Index</router-link> |
    <router-link to="/create">Create</router-link>
  </div>
  <router-view :todos="todosApp" @add-todo="createTodo" />
</template>

<script lang="ts">
import { defineComponent } from "vue";

interface TodosApp {
  title: string;
  year: number;
}

export default defineComponent({
  name: "APP",
  data() {
    return {
      todosApp: [
        { title: "first", year: 2000 },
        { title: "second", year: 3000 },
      ] as TodosApp[],
    };
  },
  methods: {
    createTodo(userInput: string) {
      console.log(userInput);
      const todo: TodosApp = {
        title: userInput,
        year: 1500,
      };
      this.todosApp.push(todo);
    },
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```

---

### props에서 type 적용하기

```typescript
// /src/types/Job.ts

interface Job {
    title: string,
    location: string,
    salary: number,
    id: string | number,
}

export default Job
```

```html
// src/App.vue

<template>
  <div class="app">
    <JobList :jobs="jobs" />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import JobList from "./components/JobList.vue";
import Job from "./types/Job";

export default defineComponent({
  name: "App",
  components: { JobList },
  setup() {
    const jobs = ref<Job[]>([
      { title: "farm worker", location: "seoul", salary: 30000, id: 1 },
      { title: "farm worker", location: "seoul", salary: 30000, id: 1 },
      { title: "farm worker", location: "seoul", salary: 30000, id: 1 },
      { title: "farm worker", location: "seoul", salary: 30000, id: 1 },
      { title: "farm worker", location: "seoul", salary: 30000, id: 1 },
    ]);

    return { jobs };
  },
});
</script>

<style scoped>
</style>

```

```html
// /src/components/JobList.vue

<template>
  <div class="job-list">
    <ul>
      <li v-for="job in jobs" :key="job.id">
          <h2>{{ job.title }} in {{ job.location }}</h2>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";
import Job from "../types/Job";

export default defineComponent({
  props: {
    jobs: {
      required: true,
      type: Array as PropType<Job[]>,
    },
  },
  setup() {},
});
</script>

<style>
</style>
```

props에서는 타입을 이런 식으로 설정하게 된다. `PropType` 메서드를 이용해서 props 내부의 값에 대한 타입을 지정해준다는 것을 까먹어서는 안되겠다.

---

### Function

만약에 특정한 원소를 기준으로 정렬을 해야 하는 상황이 생기게 된다면, 특정한 원소를 가지고 있어야 한다. 그렇다면 이 특정한 원소의 후보군에 대한 타입은 어떻게 지정할 수 있을까.

```html
<button>order by title</button>
<button>order by salary</button>
<button>order by location</button>
```

바로, 또 다시 우리만의 타입을 만들어서 활용하면 된다. 정렬의 기준이 되는 특정한 원소의 후보군은 현재 title, salary, location이다.

```typescript
// /src/types/OrderTerm.ts

type OrderTerm = 'location' | 'title' | 'salary'

export default OrderTerm
```

위의 예제와 엮어서 새로운 예제를 작성한다면 다음과 같을 수 있다. (실제로 정렬되는 기능은 넣기 전이다.)

```html
// /src/App.vue

<template>
  <div class="app">
    <button @click="handleClick('title')">order by title</button>
    <button @click="handleClick('salary')">order by salary</button>
    <button @click="handleClick('location')">order by location</button>

    <JobList :jobs="jobs" :order="order" />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import JobList from "./components/JobList.vue";
import Job from "./types/Job";
import OrderTerm from "./types/OrderTerm";

export default defineComponent({
  name: "App",
  components: { JobList },
  setup() {
    const jobs = ref<Job[]>([
      { title: "farm worker", location: "seoul", salary: 30000, id: 1 },
      { title: "farm worker", location: "seoul", salary: 30000, id: 1 },
      { title: "farm worker", location: "seoul", salary: 30000, id: 1 },
      { title: "farm worker", location: "seoul", salary: 30000, id: 1 },
      { title: "farm worker", location: "seoul", salary: 30000, id: 1 },
    ]);

    const order = ref<OrderTerm>("title");

    const handleClick = (term: OrderTerm) => {
      order.value = term;
    };

    return { jobs, handleClick, order };
  },
});
</script>

<style scoped>
</style>

```

```html
// /src/components/JobList.vue

<template>
  <div class="job-list">
    <ul>
      <li v-for="job in jobs" :key="job.id">
        <h2>{{ job.title }} in {{ job.location }}</h2>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";
import Job from "../types/Job";
import OrderTerm from "../types/OrderTerm";

export default defineComponent({
  props: {
    jobs: {
      required: true,
      type: Array as PropType<Job[]>,
    },
    order: {
      required: true,
      type: String as PropType<OrderTerm>,
    },
  },
  setup() {},
});
</script>

<style>
</style>
```

---



