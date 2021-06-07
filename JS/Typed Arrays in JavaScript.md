# Typed Arrays in JavaScript

---

[`Int8Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Int8Array)

> signed int
>
> -128 to 127

[`Uint8Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)

> unsigned int
>
> 0 to 255

[`Uint8ClampedArray`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8ClampedArray)

> 0 to 255
>
> 0 에서 255 사이를 강제하는 배열. 0 미만 값이 들어오면 강제로 0으로 바꾸고 255 초과값이 들어오면 강제로 255로 바꾼다.

[`Int16Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Int16Array)

> signed int
>
> -32768 to 32767

[`Uint16Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint16Array)

> unsigned int
>
> 0 to 65535

[`Int32Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Int32Array)

> signed int
>
> -2147483648 to 2147483647

[`Uint32Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint32Array)

> unsigned int
>
> 0 to 4294967295

[`Float32Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Float32Array)

> 1.2 x 10 ^ -38 to 3.4 x 10 ^ 38

[`Float64Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Float64Array)

> 5.0 x 10 ^ -324 to 1.8 x 10 ^ 308

---

ArrayBuffer와 DataView에 대한 내용은 [모질라](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)를 참고하세요!

```javascript
// ArrayBuffer의 파라미터는 바이트 값이 들어간다.
var buffer = new ArrayBuffer(8)

// 32는 비트값을 의미한다. 때문에 view는 원소를 2개만 저장할 수 있는 배열이 된다.
var view = new Int32Array(buffer)

view[0] = 1
view[1] = 2
// {'0': 1, '1': 2}

view[2] = 3
// {'0':1, '1': 2}    에러가 발생하지는 않지만, 저장이 되지 않는다.
```





