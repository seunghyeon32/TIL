# Hooks

[TOC]



```bash
$ yarn create react-app hooks-tutorial
```



---

## 01. useState

```react
// Counter.js

import { useState } from "react";

const Counter = () => {
    const [value, setValue] = useState(0);

    return (
        <div>
            <p>
                현재 카운터 값은 <b>{value}</b>입니다.
            </p>
            <button onClick={() => setValue(value+1)}>+1</button>
            <button onClick={() => setValue(value-1)}>-1</button>
        </div>
    )
}

export default Counter;
```

> useState: 상태의 기본값 => 호출 시, 배열 반환
>
> 1. 상태 값
> 2. 상태를 설정하는 함수



```react
// App.js

import Counter from './Counter.js';

function App() {
  return (
    <div>
      <Counter/>
    </div>
  );
}

export default App;
```



### useState실습

- 하나의 useState 함수는 하나의 상태 값만 관리

```react
// Info.js

import { useState } from "react";

const Info = () => {
    const [name, setName] = useState('');
    const [nickName, setNickName] = useState('');

    const onChangeName = e => {
        setName(e.target.value);
    };

    const onChangeNickName = e => {
        setNickName(e.target.value);
    };

    return (
        <div>
            <div>
                <input value={name} onChange={onChangeName}/>
                <input value={nickName} onChange={onChangeNickName}/>
            </div>

            <div>
                <div>
                    <b>이름: </b>{name}
                </div>
                <div>
                    <b>닉네임: </b>{nickName}
                </div>
            </div>

        </div>
    )
}

export default Info;
```

```react
// App.js

import Info from './Info.js';

function App() {
  return (
    <div>
      <Info/>
    </div>
  );
}

export default App;
```



---



## 02. useEffect

✔ **useEffect** : 리액트 컴포넌트가 렌더링될 떄마다 특정 작업을 수행하도록 설정할 수 있는 Hook

=> componentDidMount + componentDidUpdate

```react
// Info.js

import { useEffect, useState } from "react";

const Info = () => {
    const [name, setName] = useState('');
    const [nickName, setNickName] = useState('');

    useEffect(()=> {
        console.log('렌더링이 완료되었습니다');
        console.log({
            name, nickName
        });
    });

    const onChangeName = e => {
        setName(e.target.value);
    };

    const onChangeNickName = e => {
        setNickName(e.target.value);
    };

    return (
        <div>
            <div>
                <input value={name} onChange={onChangeName}/>
                <input value={nickName} onChange={onChangeNickName}/>
            </div>

            <div>
                <div>
                    <b>이름: </b>{name}
                </div>
                <div>
                    <b>닉네임: </b>{nickName}
                </div>
            </div>

        </div>
    )
}

export default Info;
```



### 02-1. 마운트될 때만 실행하고 싶을 때

- 함수의 두 번째 파라미터로 비어있는 배열을 넣어줌

```react
useEffect(() => {
	console.log('마운트될 때만 실행');
}, []);
```



### 02-2. 특정 값이 업데이트 될 때만 실행

- 두 번째 파라미터로 전달되는 배열안에 검사하고 싶은 값을 넣어줌
- 배열 안에는  useState를 통해 관리하고 있는 상태, props로 전달받은 값을 넣어줘도 됨

```react
useEffect(() => {
    console.log(name);
}, [name]);
```



### 02-3. cleanup 함수

- 컴포넌트가 언마운트되기 전이나 업데이트되기 직전에 어떠한 작업을 수행하고 싶다면  useEffect 에서  cleanup 함수를 반환해줘야 함.

```react
// Info.js - useEffect

useEffect(() => {
    console.log('effect');
    console.log(name);
    return () => {
        console.log('cleanup');
        console.log(name);
    }
}, [name]);
```



- **App.js에서 Info 컴포넌트의 가시성 변경**

```react
// App.js

import { useState } from 'react';
import Info from './Info.js';

const App = () => {
  const [visible, setVisible] = useState(false);

  return (
    <div>
      <button
        onClick={()=> {
          setVisible(!visible);
        }}
      >
        {visible ? '숨기기' : '보이기'}
      </button>
      <hr/>
      {visible && <Info/>}
    </div>
  )
}

export default App;
```

- 렌더링될 때마다 cleanup 함수가 계속 나타남
- cleanup 함수가 호출될 때는 업데이트 직전의 값을 보여줌



- **언마운트될 때만 뒷정리 함수를 호출하고 싶다면  useEffect 함수의 두 번째 파라미터에 비어있는 배열을 넣으면 됨.**

```react
// Info.js - useEffect

useEffect(() => {
    console.log('effect');
    return () => {
        console.log('unmount');
    }
}, []);
```

---



## 03. useReducer

- useState 보다 더 다양한 컴포넌트 상황에 따라 다양한 상태를 다른 값으로 업데이트하고 싶은 경우 사용

- **Reducer**  : 현재 상태, 그리고 업데이트를 위해 필요한 정보를 담은 액션(action) 값을 전달받아 새로운 상태를 반환하는 함수

  - 리듀서 함수에서 새로운 상태를 만들 때는 반드시 **불변성**을 지켜야 함 !

  ```react
  function reducer(state, action) {
  	return { ... };	
  }
  ```

  ```react
  // action 값의 주요 형태
  
  {
  	type: 'INCREMENT',
      // 다른 값들이 필요하다면 추가로 들어감
  }
  ```

- 리덕스에서는 액션 객체의 type이 필수지만, useReducer에서는 선택적

- 객체가 아닌 문자열이나 숫자여도 됨



### 03-1. 카운터 구현하기

```react
// Counter.js

import { useReducer } from "react";

function reducer(state, action) {
    // action.type에 따라 다른 작업 수행
    switch (action.type) {
        case 'INCREMENT':
            return { value: state.value+1 };

        case 'DECREMENT':
            return { value: state.value-1 };

        default:
            return state;
    }
}


const Counter = () => {
    const [state, dispatch] = useReducer(reducer, { value: 0 });

    return (
        <div>
            <p>
                현재 카운터 값은 <b>{state.value}</b>입니다.
            </p>
            <button onClick={() => dispatch({type: 'INCREMENT'})}>+1</button>
            <button onClick={() => dispatch({type: 'DECREMENT'})}>-1</button>
        </div>
    )
}

export default Counter;
```

> useReducer :
>
> 1. 리듀서 함수
> 2. 해당 리듀서의 기본값
>
> - state : 현재 가리키고 있는 상태
> - dispatch : 액션을 발생시키는 함수 `dispatch(action)`
> - 함수 안에 파라미터로 액션 값을 넣어주면 리듀서 함수가 호출되는 구조
> - 컴포넌트 업데이트 로직을 컴포넌트 밖으로 뺄 수 있음

```react
// App.js

import Counter from "./Counter";

const App = () => {
  return <Counter/>
};

export default App;
```



### 03-2. 인풋 상태 관리

```react
// Info.js

import { useReducer } from "react";

function reducer(state, action) {
    return {
        ...state,
        [action.name]: action.value
    };
}


const Info = () => {
    const [state, dispatch] = useReducer(reducer, {
        name: '',
        nickName: ''
    });

    const { name, nickName } = state;

    const onChange = e => {
        dispatch(e.target);
    };

    return (
        <div>
            <div>
                <input name="name" value={name} onChange={onChange}/>
                <input name="nickName" value={nickName} onChange={onChange}/>
            </div>

            <div>
                <div>
                    <b>이름: </b>{name}
                </div>
                <div>
                    <b>닉네임: </b>{nickName}
                </div>
            </div>
        </div>
    )
}

export default Info;
```

```react
// App.js

import Info from "./Info";

const App = () => {
  return <Info/>
};

export default App;
```



---



## 04. useMemo

```react
// Average.js

import { useState } from "react";

const getAverage = numbers => {
    console.log('평균값 계산 중..');
    if(numbers.length === 0) return 0;
    const sum = numbers.reduce((a, b) => a + b);
    return sum / numbers.length;
};

const Average = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState('');

    const onChange = e => {
        setNumber(e.target.value);
    };

    const onInsert = e => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber('');
    };

    return (
        <div>
            <input value={number} onChange={onChange}/>
            <button onClick={onInsert}>등록</button>

            <ul>
                {list.map((value, index) => (
                    <li key={index}>{value}</li>
                ))}
            </ul>

            <div>
                <b>평균 값:</b> {getAverage(list)}
            </div>
        </div>
    )
}

export default Average;
```

```react
// App.js

import Average from "./Average";

const App = () => {
  return <Average/>
};

export default App;
```

>  숫자를 등록할 때뿐만 아니라 인풋 내용이 수정될 때도 getAverage 함수가 호출됨



```react
// Average.js(ver.useMemo)

import { useState, useMemo } from "react";

// 평균값 계산 함수
const getAverage = numbers => {
    console.log('평균값 계산 중..');
    if(numbers.length === 0) return 0;
    const sum = numbers.reduce((a, b) => a + b);
    return sum / numbers.length;
};

const Average = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState('');

    const onChange = e => {
        setNumber(e.target.value);
    };

    const onInsert = e => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber('');
    };

    const avg = useMemo(() => getAverage(list), [list]); 

    return (
        <div>
            <input value={number} onChange={onChange}/>
            <button onClick={onInsert}>등록</button>

            <ul>
                {list.map((value, index) => (
                    <li key={index}>{value}</li>
                ))}
            </ul>

            <div>
                <b>평균 값:</b> {getAverage(list)}
            </div>
        </div>
    )
}

export default Average;
```



---



## 05. useCallback

- 렌더링 성능을 최적화해야하는 상황에 사용

```react
// Average.js

import { useState, useMemo, useCallback } from "react";

// 평균값 계산 함수
const getAverage = numbers => {
    console.log('평균값 계산 중..');
    if(numbers.length === 0) return 0;
    const sum = numbers.reduce((a, b) => a + b);
    return sum / numbers.length;
};

const Average = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState('');

    const onChange = useCallback(e => {
        setNumber(e.target.value);
    }, []); // 컴포넌트가 처음 렌더링 될 때만 함수 생성

    const onInsert = useCallback(e => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber('');
    }, [number, list]);  // number 혹은 list가 바뀌었을 때만 함수 생성

    const avg = useMemo(() => getAverage(list), [list]); 

    return (
        <div>
            <input value={number} onChange={onChange}/>
            <button onClick={onInsert}>등록</button>

            <ul>
                {list.map((value, index) => (
                    <li key={index}>{value}</li>
                ))}
            </ul>

            <div>
                <b>평균 값:</b> {getAverage(list)}
            </div>
        </div>
    )
}

export default Average;
```

> useCallback:
>
> 1. 생성하고 싶은 함수
> 2. 배열 ( 어떤 값이 바뀌었을 때, 함수를 새로 생성해야 하는지 명시 )



---



## 06. useRef

- 함수 컴포넌트에서 ref를 쉽게 사용할 수 있도록 함
- **등록버튼을 눌렀을 때, 포커스가 인풋 쪽으로 넘어가도록**

```react
// Average.js

import { useState, useMemo, useCallback, useRef } from "react";

// 평균값 계산 함수
const getAverage = numbers => {
    console.log('평균값 계산 중..');
    if(numbers.length === 0) return 0;
    const sum = numbers.reduce((a, b) => a + b);
    return sum / numbers.length;
};

const Average = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState('');
    const inputEl = useRef(null);

    const onChange = useCallback(e => {
        setNumber(e.target.value);
    }, []); // 컴포넌트가 처음 렌더링 될 때만 함수 생성

    const onInsert = useCallback(e => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber('');
        inputEl.current.focus();
    }, [number, list]);  // number 혹은 list가 바뀌었을 때만 함수 생성

    const avg = useMemo(() => getAverage(list), [list]); 

    return (
        <div>
            <input value={number} onChange={onChange} ref={inputEl}/>
            <button onClick={onInsert}>등록</button>

            <ul>
                {list.map((value, index) => (
                    <li key={index}>{value}</li>
                ))}
            </ul>

            <div>
                <b>평균 값:</b> {getAverage(list)}
            </div>
        </div>
    )
}

export default Average;
```

> useRef를 통해 만든 객체 안의 current 값이 실제 엘리먼트를 가리킴



### 로컬 변수 사용하기

```react
import { Component } from 'react';

class MyComponent extends Component {
    id = 1
    setId = (n) => {
        this.id=n;
    }
    
    printId = () => {
        console.log(this.id);
    }
    
    render() {
        return (
        	<div>
            	MyComponent
            </div>
        )
    }
}

export default MyComponent;
```



**함수 컴포넌트로 작성**

```react
import { useRef } from 'react';

const RefSample = () => {
    const id = useRef(1);
    const SetId = (n) => {
        id.current = n;
    }
    const printId = () => {
        console.log(id.current);
    }
    return (
    	<div>
        	refsample
        </div>
    )
}

export default RefSample;
```

> ref 안의 값이 바뀌어도 컴포넌트가 렌더링되지 않음



---



## 07. 커스텀 Hooks 만들기

```react
// useInputs.js

import { useReducer } from "react";

function reducer(state, action) {
    return {
        ...state,
        [action.name]: action.value
    }
}

export default function useInputs(initialForm) {
    const [state, dispatch] = useReducer(reducer, initialForm);
    const onChange = e => {
        dispatch(e.target);
    };

    return [state, onChange];
}
```

```react
// Info.js

import useInputs from "./useInputs";

const Info = () => {
    const [state, onChange] = useInputs({
        name: '',
        nickName: ''
    });

    const { name, nickName } = state;

    return (
        <div>
            <div>
                <input name="name" value={name} onChange={onChange}/>
                <input name="nickName" value={nickName} onChange={onChange}/>
            </div>

            <div>
                <div>
                    <b>이름: </b>{name}
                </div>
                <div>
                    <b>닉네임: </b>{nickName}
                </div>
            </div>
        </div>
    )
}

export default Info;
```

