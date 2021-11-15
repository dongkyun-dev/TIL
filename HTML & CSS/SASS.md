# SASS

---

SASS 파일을 브라우저는 해석할 수 없다. 때문에 CSS 파일로의 컴파일 작업이 필요하다. 이 과정에서 역할을 수행할 수 있는 것이 GULP이다.

`npm i gulp gulp-sass sass`

`gulp`  // 실행명령어

```javascript
// gulpfile.js

const { src, dest, watch, series } = require("gulp");
const sass = require("gulp-sass")(require("sass"));

function buildStyles() {
  return src("*.scss").pipe(sass()).pipe(dest("css"));
}

function watchTask() {
  watch(["*.scss"], buildStyles);
}

exports.default = series(buildStyles, watchTask);

```

---

### 변수 사용하기

```css
button {
  background-color: #3299ef;
}

a {
  background-color: #3299ef;
}

h1 {
  background-color: #3299ef;
}

```

```scss
$primary: #3299ef;
$secondary: #1ac888;
$error: #d32752;

button {
  background-color: $primary;
}

a {
  background-color: $primary;
}

h1 {
  background-color: $primary;
}
```

---

### 모듈화

```scss
// variables.scss

$primary: #3299ef;
$secondary: #1ac888;
$error: #d32752;
```

```scss
// index.scss

@import 'variables';

button {
  background-color: $primary;
}

a {
  background-color: $primary;
}

h1 {
  background-color: $primary;
}
```

---

### 폴더 구조

`./gulpfile.js`

`./dk/_base.scss`

`./dk/_variables.scss`

`./dk/index.scss`

이런 모양의 폴더 구조가 있는 경우 `index.scss`와 `gulpfile`은 다음과 같아진다.

```scss
// index.scss

@import 'variables';
@import 'base';
```

```javascript
// gulpfile.js

const { src, dest, watch, series } = require("gulp");
const sass = require("gulp-sass")(require("sass"));

function buildStyles() {
  return src("dk/**/*.scss").pipe(sass()).pipe(dest("css"));
}

function watchTask() {
  watch(["dk/**/*.scss"], buildStyles);
}

exports.default = series(buildStyles, watchTask);
```

`dk/**/*.scss`는 결국 dk 폴더 내부의 모든 서브 폴더들에 대한 scss 파일들을 컴파일 하겠다는 의미를 가진다.

또한, SASS에서는 import하는 파일의 순서 또한 중요하다. 현재 variables에서 정의된 변수들을 base에서 사용 중이다. 만약 base를 먼저 import하고 그 다음에 variables를 import 하게 되면 에러가 나게 된다.

---

### 미디어 쿼리

```scss
// _breakpoints.scss

$breakpoints: (
	"xs": 0,
    "sm": 480px,
    "md": 720px,
    "lg": 960px,
    "xl": 1200px,
);

@mixin xs {
    @media (min-width: map-get($breakpoints, "xs")) {
        @content;
    }
}

// ...
```

```scss
// components/_card.scss

.card {
    font-size: 15px;
    
    @xs {
        font-size: 20px;
    }
}
```

이와 같이 미리 키워드를 정의해놓고 미디어 쿼리를 사용할 수 있다.