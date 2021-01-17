# MongoDB

![img](https://t1.daumcdn.net/cfile/tistory/99F1D23359DB7A6434)

>몽고디비는 document 지향 데이터베이스 시스템이다. NoSQL 데이터베이스로 분류되는 몽고디비는 JSON과 같은 동적 스키마형 document들을 선호함에 따라 전통적인 테이블 기반 관계형 데이터베이스 구조의 사용을 삼간다.

### 그렇다면 NoSQL과 관계형 데이터베이스는 어떤 차이점이 있을까?

<br/>

## NoSQL

> Non Relational Operation Database SQL의 줄임말로써 관계형 데이터베이스가 아닌 SQL이다.
>
> 일반적인 관계형 데이터베이스에서는 데이터의 중복을 제거하고 무결성을 보장하기 위해서 정규화를 하게 되는데 이러한 정규화가 과도한 JOIN으로 인해 성능 저하를 초래할 수 있다.
>
> 불필요한 JOIN 최소화, 유연성있는 서버 구조 제공, 비정형 데이터 구조로 설계비용 감소, Read/Write가 빠르면 빅데이터 처리가 가능하다는 장점을 가진다.

|    관계형 데이터베이스     |             NoSQL              |
| :------------------------: | :----------------------------: |
| 서버 한 대를 중심으로 확장 | 여러 대의 서버를 중심으로 확장 |
|           무결성           |             유연성             |
|      데이터 중복 제거      |        데이터 중복 허용        |
|          트랜잭션          |        빠른 읽기, 쓰기         |

[아마존](https://aws.amazon.com/ko/nosql/)

[참고한 블로그](https://cionman.tistory.com/44)

> 이 두 개의 글들이 관계형과 비관계형을 잘 구분해서 설명하고 있다.

---

## &#127804;기본적인 mongoose connect

```javascript
const mongoose = require('mongoose')
mongoose.connect('mongodb+srv://JangDongkyun:<password>@cluster0.nxywy.mongodb.net/<dbname>?retryWrites=true&w=majority', {
    useNewUrlParser: true,
    useUnifiedTopology: true,
    useCreateIndex: true,
    useFindAndModify: false
}).then(() => console.log('MongoDB connected....'))
  .catch(err => console.log(err))
```

> `index.js`에서의 mongoose 연결부분

<기본적으로 들어가는 옵션들에 대한 설명>

- `useNewUrlParser: true`

*The underlying MongoDB driver has deprecated their current [connection string](https://docs.mongodb.com/manual/reference/connection-string/) parser. Because this is a major change, they added the useNewUrlParser flag to allow users to fall back to the old parser if they find a bug in the new parser. You should set useNewUrlParser: true unless that prevents you from connecting. Note that if you specify useNewUrlParser: true, you must specify a port in your connection string, like mongodb://localhost:27017/dbname. The new url parser does not support connection strings that do not have a port, like mongodb://localhost/dbname.*

> useNewUrlParser 옵션에 true를 주어야 url 뒤에 붙는 포트 번호를 인식할 수 있다.

- `useUnifiedTopology: true`

*False by default. Set to true to opt in to using [the MongoDB driver's new connection management engine](https://mongoosejs.com/docs/deprecations.html#useunifiedtopology). You should set this option to true, except for the unlikely case that it prevents you from maintaining a stable connection.*

> 특별한 이유가 없다면 이 옵션은 그냥 반드시 true

- useCreateIndex: true

*False by default. Set to `true` to make Mongoose's default index build use `createIndex()` instead of `ensureIndex()` to avoid deprecation warnings from the MongoDB driver.*

>createIndex() 옵션을 사용하기 위해서는 true로 설정해야 한다. index는 항상 사용하기 때문에 이 설정도 그냥 항상 true로 설정한다고 생각해두어도 좋을 것 같다.

- useFindAndModify: false

*True by default. Set to false to make findOneAndUpdate() and findOneAndRemove() use native findOneAndUpdate() rather than findAndModify().*

> findOneAndUpdate() and findOneAndRemove()와 같은 메서드를 사용하기 위해서는 false로 설정해야 한다. 설정하지 않고 이런 메서드들을 사용하면
>
> ```javascript
> DeprecationWarning: Mongoose: `findOneAndUpdate()` and `findOneAndDelete()` without the `useFindAndModify` option set to false are deprecated. See: https://mongoosejs.com/docs/deprecations.html#findandmodify DeprecationWarning: collection.findAndModify is deprecated. Use findOneAndUpdate, findOneAndReplace or findOneAndDelete instead.
> ```
>
> 이런 에러 코드가 뜬다. 에러 코드를 이해하는 능력이 필요하기에 에러코드도 주의 깊게 보아야 한다.

<br/>

[공식문서에서의 다양한 옵션 설명](https://mongoosejs.com/docs/connections.html)

---



## &#127808;스키마 설정

```javascript
import mongoose from 'mongoose';
  const { Schema } = mongoose;

  const blogSchema = new Schema({
    title:  String, // String is shorthand for {type: String}
    author: String,
    body:   String,
    comments: [{ body: String, date: Date }],
    date: { type: Date, default: Date.now },
    hidden: Boolean,
    meta: {
      votes: Number,
      favs:  Number
    }
  });

const Blog = mongoose.model('Blog', blogSchema);
```

>[공식문서 가이드 코드](https://mongoosejs.com/docs/guide.html)



