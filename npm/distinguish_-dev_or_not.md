# NPM(Node Package Manager)

>프로젝트에 필요한 라이브러리를 다운로드 또는 관리 할 수 있도록 해주는 프로그램이다. **npm**을 사용하게 되면 `package.json` 파일을 프로젝트 폴더 아래에 생성하여 관리한다. `package.json`이 없다면 `npm init`을 통해 키본 세팅을 할 수 있다.

<br/>

#### `npm install <module-name> --save`

- `--save` 옵션을 추가함으로써 `package.json`의 `dependency `항목에 모듈을 추가한다.
- npm5부터는 `--save` 옵션을 사용하지 않아도 `dependency`에 자동으로 모듈 추가가 된다.

<br/>

#### `npm install <module-name> --save-dev`

- `-dev` 옵션을 추가함으로써 `package.json`의 `devDependencies` 항목에 모듈을 추가한다.

<br/>

### 그렇다면 이 둘을 어떻게 명시적으로 구분할 것이냐? &#128557;

<br/>

- dependencies: Packages required by your application in production.
- devDependencies: Packages that are only needed for local development and testing.

<br/>

npm 공식문서에서는 이 둘을 이렇게 구분하고 있다. 로컬 환경 혹은 테스팅을 위해 사용하는 패키지`(ex.EsLint)`는 `devDependencies`에 그렇지 않은 것들은 모두 `dependencies`에 넣는 것이다. 

<br/>

하지만, 이 둘을 가르는 더 핵심은 바로 다음 문장에 있다.

<br/>

*The real answer is <u>it depends</u>. The choice of where to put each module depends not only on the module itself, but on your application and even on the ways it's developed and deployed.*

<br/>

결국! 제일 핵심은 때에 따라 다르다는 것이다. 그때 그때 상황에 맞추어 자신의 관점하에 이 둘을 구분하는 것이다. 때문에 이 둘을 구분할 때 사용할 <u>**자신의 관점**</u>을 확실하게 만들어나가는 과정이 중요하다. 나 또한 이런 관점과 기준을 만들기 위해 노력하고, 이 기준 하에 둘을 구별해 나갈 생각이다.

---

참고문헌))

[https://withblue.ink/2020/06/07/is-this-a-dependency-or-devdependency.html](https://withblue.ink/2020/06/07/is-this-a-dependency-or-devdependency.html)

>되게 좋은 내용, 재미있는 내용이 있다. 나중에 또 읽어봐도 정말 좋은 글이다.





