# 컴포넌트

[TOC]



✔ `props` : 컴포넌트의 속성 값

✔ `state` : 컴포넌트의 상태 값



## 클래스형 컴포넌트

```react
// App.js

import "./App.css";

function App() {
  const name = "리액트";
  return <div className="react">{name}</div>;
}

export default App;
```

> 함수 컴포넌트



```react
// App.js

import "./App.css";
import { Component } from 'react';

class App extends Component {
  render() {
    const name = 'react';
    return <div className="react">{name}</div>;
  }
}

export default App;
```

> 클래스형 컴포넌트



- 클래스형 컴포넌트와 함수 컴포넌트의 차이점
  - 클래스형 컴포넌트 :
    -  state 기능 및 라이프 사이클 기능을 사용할 수 있음
    - 임의 메서드 정의 가능
    - **render 함수**가 꼭 있어야 하고, 그 안에서 보여주어야 할 JSX 를 반환해야 함.
  - 함수 컴포넌트 :
    - 클래스형 컴포넌트보다 선언하기 쉬움
    - 메모리 자원 사용이 적음
    - state 와 라이프사이클 API의 사용이 불가능함 >> 이후 Hooks 기능 도입으로 해결



---



## 첫번째 컴포넌트 생성

1. 파일 만들기
2. 코드 작성하기
3. 모듈 내보내기 및 불러오기



### 01. src 디렉터리에 MyComponent.js 파일 생성

### 02. 코드 작성

```react
const MyComponent = () => {
    return <div>나의 새롭고 멋진 컴포넌트</div>;
}

export default MyComponent;
```



- **화살표 함수 (arrow function)**

  ```js
  // 기존 함수
  setTimeout(function() {
  	console.log('hello world');
  }, 1000);
  
  
  // 화살표 함수
  setTimeout(() => {
  	console.log('hello world');
  }, 1000);
  ```

  ```js
  // 1
  
  function BlackDog() {
      this.name = '흰둥이';
      return {
          name: '검둥이',
          bark: function() {
              console.log(this.name + ':멍멍!');
          }
      }
  }
  
  const blackDog = new BlackDog();
  blackDog.bark();   // 검둥이:멍멍!
  ```

  ```js
  // 2
  
  function WhiteDog() {
      this.name = '흰둥이';
      return {
          name: '검둥이',
          bark: () => {
              console.log(this.name + ':멍멍!');
          }
      }
  }
  
  const whiteDog = new WhiteDog();
  whiteDog.bark();   // 흰둥이:멍멍!
  ```

  > function() 을 사용했을 때는 검둥이, () => 를 사용했을 때는 흰둥이가 나타남.
  >
  > 일반 함수는 자신이 종속된 객체를 this로 가리키며, 화살표 함수는 자신이 종속된 인스턴스를 가리킴.



😉 **Reactjs Code Snippets 사용하기**

- `rsc + <Enter>` : 컴포넌트 함수 생성
- `rcc + <Enter>` : 클래스형 함수 생성



### 03. 모듈 내보내기 및 불러오기

💡 **모듈 내보내기 (export)**

```js
// MyComponent.js

export default MyComponent;
```

> 다른 파일에서 이 파일을 import할 때, 위에서 선언한 MyComponent 클래스를 불러오도록 설정



💡 **모듈 불러오기 (import)**

```react
// App.js

import MyComponent from './MyComponent';

const App = () => {
	return <MyComponent/>
};

export default App;
```



---



## props

