# 컴포넌트 반복

[TOC]



## 자바스크립트 배열의 map() 함수

✔ **map()** : 파라미터로 전달된 함수를 사용해서 배열 내 각 요소를 원하는 규칙에 따라 변환한 후 그 결과로 새로운 배열 생성



### 01. 문법

```react
arr.map(callback, [thisArg])
```

- `callback` : 새로운 배열의 요소를 생성하는 함수
  - `currentValue` : 현재 처리하고 있는 요소
  - `index` : 현재 처리하고 있는 요소의 index 값
  - `array` : 현재 처리하고 있는 원본 배열
- `thisArg` (선택) : callback 함수 내부에서 사용할 this 레퍼런스



### 02. 예제

- map 함수를 사용하여 배열  [1, 2, 3, 4, 5]의 각 요소를 제곱해서 새로운 배열 생성

```react
var numbers = [1, 2, 3, 4, 5];

var processed = numbers.map(function(num) {
	return num*num;
});

console.log(processed);
```

```react
const numbers = [1, 2, 3, 4, 5];
const result = numbers.map(num => num*num);
console.log(result);
```





## 데이터 배열을 컴포넌트 배열로 변환

```react
// IterationSample.js

const IterationSample = () => {
    const names=['눈사람', '얼음', '눈', '바람'];
    const nameList=names.map(name=><li>{name}</li>);
    
    return (
        <ul>{nameList}</ul>
    );
};

export default IterationSample;
```

```react
// App.js

import { Component } from "react";
import IterationSample from "./IterationSample.js";

class App extends Component {
  render() {
    return (
        <IterationSample />
    );
  }
}

export default App;
```

> Warning: Each child in a list should have a unique "key" prop.
>
> => "key" prop이 없다는 경고 메시지를 표시



## key

- 컴포넌트 배열을 렌더링했을 때 어떤 원소에 변동이 있었는지 알아내기 위해 사용
- key가 없을 때는 Virtual DOM을 비교하는 과정에서 리스트를 순차적으로 비교하면서 변화를 감지
- key가 있다면, 이를 이용해 어떤 변화가 일어났는지 빠르게 알 수 있음



### key 설정

✔ key 값은 언제나 **유일**해야 함. => 데이터가 가진 고윳값을 key 값으로 설정

```react
// IterationSample.js

const IterationSample = () => {
    const names=['눈사람', '얼음', '눈', '바람'];
    const nameList=names.map((index, name) => <li key={index}>{name}</li>);
    
    return (
        <ul>{nameList}</ul>
    );
};

export default IterationSample;
```



## 실습

```
1. 초기 상태 설정하기
2. 데이터 추가 기능 구현하기
3. 데이터 제거 기능 구현하기
```



### 데이터 추가

```react
// IterationSample.js

import { useState } from "react";

const IterationSample = () => {
    const [names, setNames] = useState([
        { id: 1, text: '눈사람' },
        { id: 2, text: '얼음' },
        { id: 3, text: '눈' },
        { id: 4, text: '바람' }
    ]);

    const [inputText, setInputText] = useState('');
    const [nextId, setNextId] = useState(5); // 새로운 항목을 추가할 때 사용할 id

    const onChange = e => setInputText(e.target.value);
    const onClick = () => {
        const nextNames = names.concat({
            id: nextId,
            text: inputText
        });

        setNextId(nextId+1); // nextId 값에 1을 더해준다
        setNames(nextNames); // names 값을 업데이트
        setInputText('');    // inputText를 비운다
    };

    const namesList = names.map(name => <li key={name.id}>{name.text}</li>);

    return (
        <>
            <input value={inputText} onChange={onChange}/>
            <button onClick={onClick}>추가</button>
            <ul>{namesList}</ul>
        </>
    );
};

export default IterationSample;
```

- `push` : 기존 배열 자체를 변경
- `concat` : 새로운 배열을 만들어 줌



✔ 불변성 유지 : 리액트에서 상태를 업데이트할 때는 **기존 상태를 그대로 두면서 새로운 값을 상태로 설정**해야 함 





### 데이터 제거

- 각 항목을 더블클릭했을 때 해당 항목이 화면에서 사라지는 기능 => **filter**

```react
// IterationSample.js

import { useState } from "react";

const IterationSample = () => {
    const [names, setNames] = useState([
        { id: 1, text: '눈사람' },
        { id: 2, text: '얼음' },
        { id: 3, text: '눈' },
        { id: 4, text: '바람' }
    ]);

    const [inputText, setInputText] = useState('');
    const [nextId, setNextId] = useState(5); // 새로운 항목을 추가할 때 사용할 id

    const onChange = e => setInputText(e.target.value);
    const onClick = () => {
        const nextNames = names.concat({
            id: nextId,
            text: inputText
        });

        setNextId(nextId+1); // nextId 값에 1을 더해준다
        setNames(nextNames); // names 값을 업데이트
        setInputText('');    // inputText를 비운다
    };

    // 데이터 삭제
    const onRemove = id => {
        const nextNames = names.filter(name => name.id !== id);
        setNames(nextNames);
    }

    const namesList = names.map(name => <li key={name.id} onDoubleClick={() => onRemove(name.id)}>
        {name.text}
    </li>);

    return (
        <>
            <input value={inputText} onChange={onChange}/>
            <button onClick={onClick}>추가</button>
            <ul>{namesList}</ul>
        </>
    );
};
```



‼ 상태 안에서 배열을 변형할 때는 배열에 직접 접근하여 수정하는 것이 아니라 concat, filter 등의 배열 내장 함수를 사용하여 새로운 배열을 만든 후 이를 새로운 상태로 설정해 주어야 함 ‼