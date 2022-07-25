# ref: DOM에 이름달기

💡 리액트 컴포넌트 안에서 id를 사용하지 않는 이유

: HTML에서 DOM의 id는 유일해야 하는데, 중복 id를 가진 DOM이 여러개 생기기 때문이다. 

ref는 전역적으로 작동하지 않고 컴포넌트 내부에서만 작동하기 때문에 이런 문제가 발생하지 않는다. 만일, id를 사용해야 하는 경우 컴포넌트를 만들 때마다 id 뒷부분에 추가 텍스트를 붙여 중복 id가 발생하는 것을 방지 (ex. button01, button02 ...)



## ref를 사용하는 경우

- 특정 DOM에 작업을 해야 할 때 = **DOM을 꼭 직접적으로 건드려야 할 때**

```
1. ValidationSample 컴포넌트 만들기
2. input에 ref 달기
3. 버튼 누를 때마다 input에 포커스 주기
```



---

```css
// src/ValidationSample.css

.success {
    background-color: lightgreen;
}

.failure {
    background-color: lightcoral;
}
```

```react
// src/ValidationSample.js

import { Component } from "react";
import './ValidationSample.css';

class ValidationSample extends Component {
    state = {
        password: '',
        clicked: false,
        validated: false
    }

    handleChange = (e) => {
        this.setState({
            password: e.target.value
        });
    }

    handleButtonClick = () => {
        this.setState({
            clicked: true,
            validated: this.state.password === '0000'
        })
    }

    render() {
        return (
            <div>
                <input
                    type="password"
                    value={this.state.password}
                    onChange={this.handleChange}
                    className={this.state.clicked ? (this.state.validated ? 'success' : 'failure') : ''}
                />
                <button onClick={this.handleButtonClick}>검증하기</button>
            </div>
        );
    }
}

export default ValidationSample;
```

> `handleChange`: input 에서 inChange 이벤트가 발생하면 handleChange를 호출하여 state의 password 값을 업데이트
>
> `handleButtonClick`: button에서 onClick 이벤트가 발생하면 handleButtonClick을 호출하여 clicked의 값을 참으로 설정하고 validated 값을 검증 결과로 설정
>
> + input의 className값은 버튼을 누르기 전에는 빈 문자열을 전달, 버튼을 누른 후에는 검증의 결과에 따라 success 또는 failure값을 설정
>
>   => 해당 값에 따라 input 박스의 배경색 변경

```react
// App.js

import ValidationSample from "./ValidationSample.js";

const App = () => {
  return (
    <ValidationSample/>
  );
};

export default App;
```

> App.js에 ValidationSample 컴포넌트 렌더링





🚨 DOM을 꼭 사용해야 하는 상황

- state를 사용하여 우리에게 필요한 구현을 기능하지만, state만으로 해결할 수 없는 상황이 존재 
  - 특정 input에 포커스 주기
  - 스크롤 박스 조작하기
  - Canvas 요소에 그림 그리기 등
- 어쩔 수 없이 DOM에 직접적으로 접근해야 하는 상황에 ref를 사용



---



## ref 사용

### 01. 콜백 함수를 통해 ref 설정

1. ref 를 달고자 하는 요소에 ref 라는 콜백 함수를 props로 전달함

2. 콜백 함수는 ref 값을 파라미터로 전달 받음

3. 함수 내부에서 파라미터로 받은 ref 를 컴포넌트의 멤버 변수로 설정함

```react
ex. <input ref={(ref) => {this.input=ref}}/>
```

> this.input은 input 요소의 DOM을 가리킨



### 02. createRef를 통한 ref 설정

```react
import { Component } from 'react';

class RefSample extends Component {
    input = React.createRef();
    
    handleFocus = () => {
        this.input.current.focus();
    }
    
    render() {
        return (
        	<div>
            	<input ref={this.input}/>
            </div>
        );
    }
}

export default RefSample;
```

1. 컴포넌트 내부에서 멤버 변수로 `React.createRef()` 를 담아줌
2. 해당 멤버 변수를 ref 를 달고자 하는 요소에 ref props 로 넣어주면 ref 설정 완료 !



- ref를 설정해 준 DOM에 접근하려면 => `this.input.current` 를 조회하면 됨

  🌟 `.current`



### 03. 적용

- 01에서 button을 한번 눌렀을 때, 포커스가 다시 input쪽으로 자동으로 넘어가기

```react
// src/ValidationSample.js

...
<input
    ref={(ref) => this.input=ref}
...
```

```react
// ValidationSample.js - handleButtonClick 메서드

handleButtonClick = () => {
    this.setState({
        clicked: true,
        validated: this.state.password === '0000'
    });
    this.input.focus();
}
```

> button을 눌렀을 때, 포커스가 input으로 이동하여 커서가 나타남



---



## 컴포넌트에 ref 달기

- DOM에 ref를 다는 방법과 동일

```react
<MyComponent
    ref={(ref) => {this.myComponent=ref}}
/>
```

- MyComponent 내부의 메서드 및 멤버 변수에도 접근 가능
  - 내부의 ref에도 접근 가능
  - ex. myComponent.handleClick / myComponent.input 등



```
1. ScrollBox 컴포넌트 만들기
2. 컴포넌트에 ref 달기
3. ref를 이용하여 컴포넌트 내부 메서드 호출하기
```

```react
// src/ScrollBox.js

import { Component } from 'react';

class ScrollBox extends Component {
    render() {
        const style = {
            border: '1px solid black',
            height: '300px',
            width: '300px',
            overflow: 'auto',
            position: 'relative'
        };

        const innerStyle = {
            width: '100%',
            height: '650px',
            background: 'linear-gradient(white, black)'
        };

        return (
            <div
                style={style}
                ref={(ref) => {this.box=ref}}>
                <div style={innerStyle}/>
            </div>
        );
    }
}

export default ScrollBox;
```

```react
// App.js

import ScrollBox from "./ScrollBox.js";

const App = () => {
  return (
    <ScrollBox/>
  );
};

export default App;
```



- **스크롤바를 맨 아래쪽으로 내리는 메서드**

```react
// ScrollBox.js

import { Component } from 'react';

class ScrollBox extends Component {

    scrollToBottom = () => {
        const { scrollHeight, clientHeight } = this.box;
        this.box.scrollTop = scrollHeight - clientHeight;
    }

...
```

> scrollTop : 세로 스크롤바 위치 (0-350)
>
> scrollHeight : 스크롤이 있는 박스 안의 div 높이 (650)
>
> clientHeight : 스크롤이 있는 박스의 높이 (300)

```react
// App.js

import { Component } from "react";
import ScrollBox from "./ScrollBox.js";

class App extends Component {
  render() {
    return (
      <div>
        <ScrollBox ref={(ref) => this.scrollBox=ref}/>
        <button onClick={() => this.scrollBox.scrollToBottom()}>맨 밑으로</button>
      </div>
    );
  }
}

export default App;
```

✔ `onClick={this.scrollBox.scrollToBottom}`의 형식으로 작성해도 틀린 것은 아니자만, 컴포넌트가 처음 렌더링될 때, `this.scrollBox`의 값이 undefined 이므로 값을 읽어오는 과정에서 오류가 발생함. 

➰ 화살표 함수 문법을 사용해 새로운 함수를 만들고 그 내부에서 메서드를 실행하면, 버튼을 누를 때 (이미 한 번 렌더링을 해서 this.scrollBox를 설정한 시점) `this.scrollBox.scrollToBottom` 값을 읽어 와서 실행하므로 오류가 발생하지 않음 !



