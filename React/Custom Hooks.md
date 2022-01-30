https://www.youtube.com/watch?v=J-g9ZJha8FE&t=489s

components === user interface

hooks === business logic

커스텀 훅을 사용하는 이유는 UI에서 비즈니스 로직을 분리하기 위함.





### useFetch

```react
import { useEffect, useState } from "react";

export default function useFetch(url) {
  // 어떠한 타입의 데이터가 올지 모르는 상황이기 때문에 null을 초기값으로 설정하는 것이 좋다.
  // 하지만 렌더 부분에서 data를 배열이라 생각하고 map을 돌면서 렌더하는 경우에는 null에는 map이라는 메서드가 존재하지 않아 오류가 
  // 발생하게 된다. 이러한 경우에는 null이 아닌 빈배열([])을 초기인자로 넣어줄 필요가 있다.
  const [data, setData] = useState(null);
  
  useEffect(() => {
    fetch(url)
    	.then(res => {
      	return res.json();
    	})
    	.then(data => {
      	setData(data);
    	});
  }, [url]);
  
  // 데이터를 받아오는 특성상 set 함수는 사용할 일이 없음
  return data;
}
```

```react
// 실제 사용
const words = useFetch("http://www.naver.com");
```
---

### 로딩 인디케이터와 에러처리를 추가한 useFectch

```react
import { useState, useEffect } from "react";

export default function useFetch(url) {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(false);
  
  useEffect(() => {
    const fetchData = async () => {
      setLoading(true);
      setError(false);
      try {
        const res = await fetch(`${url}`);
        const data = await res.json();
        setData(data);
      } 
      catch(error) {
      	setError(true);  
      }
      finally {
        setLoading(false); 
      }
    };
    fetchData();
  }, [url]);
  
  return { data, loading, error };
}
```

```react
// 실제 사용
import useFetch from "./useFetch";

export default function App() {
  const { data, loading, error } = useFetch("www.naver.com");
  
  if (loading) return <h1>LOADING...</h1>
  if (error) return <p>There is Error.</p>
  return (
  	<>
      // data가 null이 아니라면! data.title을 출력하라
    	<p>{data?.title}</p>
    </>
  )
}
```


---

### useLocalStorage

```react
import { useState, useEffect } from "react";

function getSavedValue(key, initialValue) {
	const savedValue = JSON.parse(localstorage.getItem(key));
  if (savedValue) {
    return savedValue;
  }
  
  if (initialValue instanceof Function) {
    return initialValue();
  }
  
  return initialValue;
}

export default function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    return getSavedValue(key, initialValue);
  });
  
  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [value]);
   
  return [value, setValue];
}
```

```react
// 실제 사용

import useLocalStorage from "./useLocalStorage";

export default function App() {
  const [name, setName] = useLocalStorage("name", "");
  
  return (
		<input
      type="text"
      value={name}
      onChange={e => setName(e.target.value)}
  	/>
  )
}
```

---

### useUpdateLogger

```react
import { useEffect } from "react";

export default function useUpdateLogger(value) {
  useEffect(() => {
    console.log(value);
  }, [value])
}
```

---

### useInput

```react
import { useState } from "react";

export default function useInput(initialValue) {
  const [value, setValue] = useState(initialValue);
  
  const reset = () => {
    setValue(initialValue);
  };
  
  const bind = {
    value,
    onChange(e) {
      setValue(e.target.value);
    }
  };
  
  return { value, bind, reset };
}
```

```react
// 실제 사용
import { useState } from "react";
import useInput from "./useInput";

export default function App() {
  const [firstName, bindFirstName, resetFirstName] = useInput("");
  const [lastName, bindLastName, resetLastName] = useInput("");
  
  const submitHandler = e => {
  	e.preventDefault();
    alert(`Hello ${firstName} ${lastName}`);
    resetFirstName();
    resetLastName();
  };
  
  return (
  	<>
    	<form>
    		<div>
      		<label>First Name</label>
          <input
            { ...bindFirstName }
            type="text"
          />
      	</div>
      	<div>
      		<label>Last Name</label>
          <input 
          	{ ...bindLastName }
            type="text"
          />
      	</div>
	      <button>Submit</button>
    	</form>
    </>
  )
}
```

---

### useToggle

```react
import { useState } from "react";

export default function useToggle(defaultValue) {
  const [value, setValue] = useState(defaultValue);
  
  // set 함수의 인자에는 자동으로 기존 value가 들어온다.
  function toggleValue(value) {
    setValue(currentValue =>
    	typeof value === "boolean" ? value : !currentValue        
  	)
  }
  
  return [value, toggleValue];
}
```

```react
// 실제 사용
import useToggle from "./useToggle"

export default function App() {
  const [value, toggleValue] = useToggle(false);
  
  return (
  	<div>
    	<button onClick={toggleValue}>Toggle</button>
      <button onClick={() => toggleValue(true)}>Make True</button>
      <button onClick={() => toggleValue(false)}>Make False</button>
    </div>
  )
}
```

---

