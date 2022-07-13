# JSX

[TOC]

✔ **JSX** : 자바스크립트의 확장 문법

- 브라우저에서 실행되기 전에 코드가 번들링되는 과정에서 바벨을 사용하여 일반 자바스크립트 형태의 코드로 변환됨



- `ReactDOM.render`

  - 컴포넌트를 페이지에 렌더링하는 역할

    ```react
    import ReactDOM from 'react-dom';
    ```

- `React.StrictMode`

  - 리액트 프로젝트에서 리액트의 레거시 기능들을 사용하지 못하게 하는 기능



## JSX 문법

### 01. 감싸인 요소

```react
// src/App.js

function App() {
  return (
    <div>
      <h1>리액트 안녕!</h1>
      <h2>잘 작동하니?</h2>
    </div>
  );
}

export default App;
```

> div 태그가 없는 경우 Parsing error 발생 !
>
> Why? Virtual DOM에서 컴포넌트 변화를 감지해 낼 때 효율적으로 비교할 수 있도록 컴포넌트 내부는 하나의 DOM트리 구조로 이루어져야한다는 규칙때문 !

➕ 여기서 div 태그 대신 `import { Fragment } from 'react';` 하여 `<div>` 태그를 `<Fragment> </Fragment>` 혹은 `<> </>`로 대체 가능





### 02. 자바스크립트 문법

```react
// src/App.js

import { Fragment } from 'react';

function App() {
  const name = '리액트';
  return (
    <>
      <h1>{name}리액트 안녕!</h1>
      <h2>잘 작동하니?</h2>
    </>
  );
}

export default App;
```

- `const` : 변경이 불가능한 상수 선언

- `let` : 동적인 값을 담을 수 있는 변수

- `var` : scope가 함수 단위

  ```react
  // src/App.js
  
  function myFunction() {
      var a = "hello";
      if(true) {
          var a = "bye";
          console.log(a); // bye
      }
      console.log(a);  // bye
  }
  ```

  > scope가 함수 범위이므로 if문 바깥에서 a를 정의하고, if문 내부에서 새로 선언했음에도 if 문 밖에서 a를 조회하면 변경된 값이 나타남
  >
  > > let 과 const는 블록 단위
  > >
  > > 같은 블록 내부에서 중복 선언이 불가능



### 03. 조건문 연산자 (if문 대신)

```react
// src/App.js

function App() {
  const name = '리액트';
  return (
    <div>
      {name === '리액트' ? (
        <h1>리액트입니다.</h1>
      ) : (
        <h2>리액트가 아닙니다.</h2>
      )}
    </div>
  );
}

export default App;
```

- ` { 조건 ? (참인경우) : (거짓인경우) }`



### 04. 조건부 렌더링 - AND 연산자(&&)

```react
// src/App.js

function App() {
  const name = '뤼액트';
  return (
    <div>
      {name === '리액트' ? (
        <h1>리액트입니다.</h1>
      ) : (
        null
      )}
    </div>
  );
}

export default App;
```

> 조건이 거짓인 경우, null이라면 아무것도 렌더링하지 않는다.

```react
// src/App.js

function App() {
  const name = '뤼액트';
  return (
    <div>
      {name === '리액트' && <h1>리액트입니다.</h1>}
    </div>
  );
}

export default App;
```

> 위의 코드와 동일하게 동작



☠ **false**를 렌더링할 때는 null과 마찬가지로 아무것도 나타나지 않는다. 하지만, **falsy** 한 값인 **"0"** 은 예외적으로 화면에 나타난다.

```react
const number = 0;
return number && <h1>내용</h1>
```



### 05. undefined를 렌더링하지 않기

- undefined를 렌더링하면 `App(..): Nothing was retuned from render. ...` 오류 발생 !

- OR(||) 연산자를 사용하여 문제 해결

  ```react
  // src/App.js
  
  import './App.css';
  
  function App() {
      const name = undefined;
      return name || '값이 undefined 입니다.';
  }
  
  export default App;
  ```

- JSX 내부에서 undefined를 렌더링하는 것은 OK

  ```react
  // src/App.js
  
  import './App.css';
  
  function App() {
      const name = undefined;
      return <div>{name}</div>;
  }
  
  export default App;
  ```

  

### 06. 인라인 스타일링

- 리액트에서 DOM요소에 스타일을 적용할 때는 문자열 형태로 넣는 것이 아니라 **객체 형태**로 넣어주어야 함 
- 스타일 이름은 `-` 문자를 없애고 카멜 표기법(camelCase)으로 작성해야 함.

```react
// src/App.js

function App() {
  const name = '리액트';
  const style = {
    backgroundColor: 'black',
    color: 'aqua',
    fontSize: '48px',
    fontWeight: 'bold',
    padding: 16
  };
  return <div style={style}>{name}</div>
}

export default App;
```

```react
// src/App.js

function App() {
  const name = '리액트';
  return <div style={{
    backgroundColor: 'black',
    color: 'aqua',
    fontSize: '48px',
    fontWeight: 'bold',
    padding: 16
  }}>{name}</div>
}

export default App;
```



### 07. class 대신 className

```css
// src/App.css

.react {
  background: aqua;
  color: black;
  font-size: 48px;
  font-weight: bold;
  padding: 16px;
}
```

```react
// src/App.js

import './App.css';

function App() {
  const name = '리액트';
  return <div className="react">{name}</div>
}

export default App;
```



### 08. 꼭 닫아야 하는 태그

```react
// src/App.js

import './App.css';
import { Fragment } from 'react';

function App() {
  const name = '리액트';
  return (
    <>
      <div className="react">{name}</div>
      <input>
    </>
  )
}

export default App;
```

> Parsing Error

```react
// src/App.js

import './App.css';
import { Fragment } from 'react';

function App() {
  const name = '리액트';
  return (
    <>
      <div className="react">{name}</div>
      <input></input>
    </>
  )
}

export default App;
```

> 꼭 닫아줘야함 ! 
>
> or `<input/>` : 선언과 동시에 닫는 태그



### 09.  주석

- `{/* ..... */}` : 여러 줄 주석

- `//` : 한 줄 주석 >> 시작 태그를 여러줄로 작성할 때에는 그 내부에서만 사용 가능

---

