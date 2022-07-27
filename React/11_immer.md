# immer를 사용하여 더 쉽게 불변성 유지하기

[TOC]



- 객체의 구조가 엄청나게 깊어지면 불변성을 유지하며 업데이트하는 것이 매우 힘듦.
- 전개 연산자 사용 : 기존에 가지고 있던 다른 값은 유지하면서 원하는 값을 새로 지정하기 위해
  - 번거롭고, 가독성이 좋지 않음
- **immer** 라이브러리를 사용하면, 구조가 복잡한 객체를 매우 쉽고 짧은 코드로 불변성을 유지하면서 업데이트 가능



## 01. immer를 설치하고 사용법 알아보기

```bash
$ yarn create react-app immer-tutorial
$ cd immer-tutorial
$ yarn add immer
```



### immer를 사용하지 않고 불변성 유지

```react
// App.js

import { useState, useRef, useCallback } from 'react';

const App = () => {
  const nextId = useRef(1);
  const [form, setForm] = useState({ name: '', username: ''});
  const [data, setData] = useState({
    array: [],
    uselessValue: null
  });

  // input 수정을 위한 함수
  const onChange = useCallback(
      e => {
        const { name, value } = e.target;
        setForm({
          ...form,
          [name]: [value]
        });
      }, [form],
  );

  // form 등록을 위한 함수
  const onSubmit = useCallback(
    e => {
      e.preventDefault();
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username
      };

      // array에 새 항목 등록
      setData({
        ...data,
        array: data.array.concat(info)
      });

      // form 초기화
      setForm({
        name: '',
        username: ''
      });

      nextId.current += 1;
    }, [data, form.name, form.username]
  );

  // 항목을 삭제하는 함수
  const onRemove = useCallback (
    id => {
      setData({
        ...data,
        array: data.array.filter(info => info.id !== id)
      });
    }, [data]
  );

  return (
    <div>
      <form onSubmit={onSubmit}>
        <input
          name="username"
          placeholder="아이디"
          value={form.username}
          onChange={onChange}
        />
        <input
          name="name"
          placeholder="이름"
          value={form.name}
          onChange={onChange}
        />
        <button type="submit">등록</button>
      </form>

      <div>
        <ul>
          {data.array.map(info => (
            <li key={info.id} onClick={()=>onRemove(info.id)}>
              {info.username} ({info.name})
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
};

export default App;
```

> 폼에서 아이디/이름을 입력하면 리스트에 추가되고, 리스트 항목을 클릭하면 삭제되는 컴포넌트
>
> => 전개 연산자와 배열 내장 함수를 사용하여 불변성 유지



### immer 사용법

```react
import produce from 'immer';

const nextState = produce(originalState, draft => {
    // 바꾸고 싶은 값 바꾸기
    draft.somewhere.deep.inside = 5;
})
```

> produce 함수의 두 가지 파라미터
>
> 1. 수정하고 싶은 상태
> 2. 상태를 어떻게 정의할지 정의하는 함수
>
>  두 번째 파라미터로 전달되는 함수 내부에서 원하는 값을 변경하면, produce 함수가 불변성 유지를 대신해 주면서 새로운 상태를 생성

**핵심 : 불변성에 신경 쓰지 않는 것처럼 코드를 작성하되 불변성 관리를 하는 것**



```react
// 예시 코드

import produce from 'immer';

const originalState = [
    {
        id: 1,
        todo: '전개 연산자와 배열 내장 함수로 불변성 유지하기',
        checked: true
    },
    {
        id: 2,
        todo: 'immer로 불변성 유지하기',
        checked: false
    }
];

const nextState = produce(originalState, draft => {
    // id가 2인 항목의 checked 값을 true로 설정
    const todo = draft.find(t => t.id === 2); // id로 항목 찾기
    todo.checked = true;
    	// 혹은 draft[1].checked = true;
    
    // 배열에 새로운 데이터 추가
    draft.push({
        id: 3,
        todo: 'immer 적용하기',
        checked: false
    });
    
    // id = 1인 항목 제거하기
    draft.splice(draft.findIndex(t => t.id === 1 ), 1);
});
```



```javascript
array.splice(start[, deleteCount[, item1[, item2[, ...]]]])
```

> - start: 배열의 변경을 시작할 인덱스 
>   - 음수를 지정한 경우: 배열의 끝에서부터 요소 카운팅
>   - 배열의 길이보다 큰 수를 지정한 경우: 실제 시작 인덱스는 배열의 길이로 설정
>   - 절대값이 배열의 길이보다 큰 경우: 0으로 세팅
> - deleteCount: 배열에서 제거할 요소의 수
>   - 생략 
>   - 값이 array.length - start보다 큰 경우: start부터의 모든 요소를 제거.
>   - 0 이하의 수를 지정: 어떤 요소도 제거되지 않는다
> - item1, item2, ... : 배열에 추가할 요소.
>
>   - 지정하지 않는 경우: splice()는 요소 제거만 수행
> - 반환값: 제거한 요소를 담은 배열
>   - 아무 값도 제거하지 않았으면 빈 배열을 반환한다

 

### App 컴포넌트에 immer 적용하기

```react
// App.js

import { useState, useRef, useCallback } from 'react';
import produce from 'immer';

const App = () => {
  const nextId = useRef(1);
  const [form, setForm] = useState({ name: '', username: ''});
  const [data, setData] = useState({
    array: [],
    uselessValue: null
  });

  // input 수정을 위한 함수
  const onChange = useCallback(
      e => {
        const { name, value } = e.target;
        setForm(
          produce(form, draft => {
            draft[name] = value;
          })
        );
      }, [form],
  );

  // form 등록을 위한 함수
  const onSubmit = useCallback(
    e => {
      e.preventDefault();
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username
      };

      // array에 새 항목 등록
      setData(
        produce(data, draft => {
          draft.array.push(info);
        })
      );

      // form 초기화
      setForm({
        name: '',
        username: ''
      });

      nextId.current += 1;
    }, [data, form.name, form.username]
  );

  // 항목을 삭제하는 함수
  const onRemove = useCallback (
    id => {
      setData(
        produce(data, draft => {
          data.array.slice(draft.array.findIndex( info => info.id === id ), 1);
        })
      );
    }, [data]
  );

  return (
    <div>
      <form onSubmit={onSubmit}>
        <input
          name="username"
          placeholder="아이디"
          value={form.username}
          onChange={onChange}
        />
        <input
          name="name"
          placeholder="이름"
          value={form.name}
          onChange={onChange}
        />
        <button type="submit">등록</button>
      </form>

      <div>
        <ul>
          {data.array.map(info => (
            <li key={info.id} onClick={()=>onRemove(info.id)}>
              {info.username} ({info.name})
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
};

export default App;
```

> immer는 코드가 복잡할 때만 사용해도 충분
>
> 무조건적으로 코드가 간결해지는 것은 아님



### useState의 함수형 업데이트와 immer 함께 쓰기

```react
const update = produce(draft => {
    draft.value = 2;
});

const originalState = {
    value: 1,
    foo: 'bar',
};

const nextState = update(originalState);
console.log(nextState);  // { value: 2, foo: 'bar' }
```

> immer에서 제공하는 produce 함수를 호출할 때, 첫 번째 파라미터가 함수 형태라면 업데이트 함수 반환

```react
// App.js

import { useState, useRef, useCallback } from 'react';
import produce from 'immer';

const App = () => {
  const nextId = useRef(1);
  const [form, setForm] = useState({ name: '', username: ''});
  const [data, setData] = useState({
    array: [],
    uselessValue: null
  });

  // input 수정을 위한 함수
  const onChange = useCallback(
      e => {
        const { name, value } = e.target;
        setForm(
          produce(draft => {
            draft[name] = value;
          })
        );
      }, [],
  );

  // form 등록을 위한 함수
  const onSubmit = useCallback(
    e => {
      e.preventDefault();
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username
      };

      // array에 새 항목 등록
      setData(
        produce(draft => {
          draft.array.push(info);
        })
      );

      // form 초기화
      setForm({
        name: '',
        username: ''
      });

      nextId.current += 1;
    }, [form.name, form.username]
  );

  // 항목을 삭제하는 함수
  const onRemove = useCallback (
    id => {
      setData(
        produce(draft => {
          draft.array.slice(draft.array.findIndex( info => info.id === id ), 1);
        })
      );
    }, 
    []
  );

  return (
    <div>
      <form onSubmit={onSubmit}>
        <input
          name="username"
          placeholder="아이디"
          value={form.username}
          onChange={onChange}
        />
        <input
          name="name"
          placeholder="이름"
          value={form.name}
          onChange={onChange}
        />
        <button type="submit">등록</button>
      </form>

      <div>
        <ul>
          {data.array.map(info => (
            <li key={info.id} onClick={()=>onRemove(info.id)}>
              {info.username} ({info.name})
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
};

export default App;
```

