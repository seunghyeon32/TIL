# Event Handling

[TOC]



---

✔ **이벤트(event)** : 사용자가 웹 브라우저에서 DOM 요소들과 상호 작용하는 것



## 리액트의 이벤트 시스템



### 이벤트 사용 시, 주의 사항

1. 이벤트 이름은 **camelCase**로 작석.

   - ex. `onKeyUp`, `onClick`

2. **이벤트에 실행할 자바스크립트 코드를 전달하는 것이 아니라, 함수 형태의 값을 전달**

3. DOM 요소에만 이벤트를 설정 가능

   - `div`, `button`, `input`, `form`, `span` 등의 DOM 요소에 이벤트 설정 가능

   - 우리가 직접 만든 컴포넌트에는 이벤트를 자체적으로 설정할 수 없음

     ```react
     // 1 ❌
     <MyComponent onClick={doSomething}/>
     
     
     // 2 ⭕
     <div onClick={this.props.onClick}>
     	{/* ... */}
     </div>
     ```

     > 1. MyComponent를 클릭할 때, doSomething 함수를 실행하는 것이 아니라, 이름이 onClick인 props를 MyComponent 에게 전달
     > 2. 전달받은 props를 컴포넌트 내부의 DOM이벤트로 설정



### 이벤트 종류

- Clipboard
- Composition
- Keyboard
- Focus
- Form
- Mouse
- Selection
- Touch
- UI
- Wheel
- Media
- Image
- Animation
- Transition



---



## 이벤트 핸들링 실습

```
1. 컴포넌트 생성 및 불러오기
2. onChange 이벤트 핸들링하기
3. 임의 메서드 만들기
4. input 여러 개 다루기
5. onKeyPress 이벤트 핸들링하기
```



### 01. 컴포넌트 생성 및 불러오기

1. 컴포넌트 생성

```react
// EventPractice.js

import { Component } from 'react';

class EventPractice extends Component {
    render() {
        return (
        	<div>
            	<h1>이벤트 연습</h1>
            </div>
        );
    }
}

export default EventPractice;
```

2. App.js에서 EventPractice 렌더링



### 02. onChange 이벤트 핸들링

#### 02-1. onChange 이벤트 설정

```react
// EventPractice.js

import { Component } from 'react';

class EventPractice extends Component {
    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input
                    type="text" 
                    name="message" 
                    placeholder="입력하세요" 
                    onChange={
                        (e) => {
                            console.log(e);
                        }
                    }
                />
            </div>
        );
    }
}

export default EventPractice;
```

> input에 입력시 이벤트 객체가 console 창에 나타남
>
> 여기서, 콘솔에 기록되는 객체는 SyntheticEvent로 웹 브라우저의 네이티브 이벤트를 감싸는 객체.
>
> 네이티브 이벤트와 인터페이스가 같으므로 순수 JS에서 HTML 이벤트를 다룰 때와 동일하게 사용.



➕ SyntheticEvent는 네이티브 이벤트와 달리 이벤트가 끝나고 나면 이벤트가 초기화되므로 정보를 참조할 수 없음.

​	> ex. 0.5초 뒤에 e 객체를 참조하면 e 객체 내부의 모든 값이 비워지게 됨.



➕ 만일, 비동기적으로 이벤트 객체를 참조할 일이 있다면 `e.persist()` 함수를 호출해야 함.

```react
// EventPractice.js 의 onChange

onChange={
    (e) => {
        console.log(e.target.value);
    }
}
```

> 값이 바뀔때마다 콘솔에 기록



#### 02-2. state에 imput 값 담기

1. `constructor`에서 `state` 초깃값을 설정
2. 이벤트 핸들링 함수 내부에서 `this.setState` 메서드를 호출하여 state를 업데이트
3. input의 value 값을 state에 있는 값으로 설정

```react
// EventPractice.js

import { Component } from 'react';

class EventPractice extends Component {
    
    state = {
        message: ''
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input
                    type="text" 
                    name="message" 
                    placeholder="입력하세요" 
                    value={this.state.message}
                    onChange={
                        (e) => {
                            this.setState({
                                message: e.target.value
                            })
                        }
                    }
                />
            </div>
        );
    }
}

export default EventPractice;
```



#### 02-3. 버튼을 누를 때 comment 값을 공백으로 설정

- 입력한 값이 state에 잘 들어갔는지 확인

```react
// EventPractice.js

import { Component } from 'react';

class EventPractice extends Component {
    
    state = {
        message: ''
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input
                    type="text" 
                    name="message" 
                    placeholder="입력하세요" 
                    value={this.state.message}
                    onChange={
                        (e) => {
                            this.setState({
                                message: e.target.value
                            })
                        }
                    }
                />

                <button onClick={                    // 버튼을 누르면
                    () => {
                        alert(this.state.message);   // state의 message에 저장된 값이 alert
                        this.setState({
                            message: ''              // state의 message를 초기화
                        });
                    }
                }>확인</button>
            </div>
        );
    }
}

export default EventPractice;
```



### 03. 임의 메서드 만들기

- **이벤트에 실행할 자바스크립트의 코드를 전달하는 것이 아니라, 함수 형태의 값을 전달**
  - 이벤트를 처리할 때 렌더링을 하는 동시에 함수를 만들어 전달
- **함수를 미리 준비하여 전달하는 방법**



#### 03-1. 기본 방식

```react
// EventPractice.js

import { Component } from 'react';

class EventPractice extends Component {
    
    state = {
        message: ''
    }

    constructor(props) {
        super(props);
        this.handleChange = this.handleChange.bind(this);
        this.handleClick = this.handleClick.bind(this);
    }

    handleChange(e) {
        this.setState({
            message: e.target.value
        });
    }

    handleClick() {
        alert(this.state.message);
        this.setState({
            message: ''
        });
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input
                    type="text" 
                    name="message" 
                    placeholder="입력하세요" 
                    value={this.state.message}
                    onChange={this.handleChange}
                />

                <button onClick={this.handleClick}>확인</button>
            </div>
        );
    }
}

export default EventPractice;
```

> 함수가 호출될 때, this는 호출부에 따라 결정되므로, 클래스의 임의 메서드가 특정 HTML요소의 이벤트로 등록되는 과정에서 메서드와 this 의 관계가 끊어짐.
>
> 이 때문에, 임의 메서드가 이벤트로 등록되어도 this가 컴포넌트 자신으로 제대로 가리키기 위해서 **메서드를 this와 바인딩하는 작업 필요**
>
> > 바인딩하지 않으면, this는 undefined를 가리킨.



#### 03-2. Property Initializer Syntax를 사용한 메서드 작성

- 바벨의 transform-class-properties

```react
// EventPractice.js

import { Component } from 'react';

class EventPractice extends Component {
    
    state = {
        message: ''
    }

    handleChange = (e) => {
        this.setState({
            message: e.target.value
        });
    }

    handleClick = () =>  {
        alert(this.state.message);
        this.setState({
            message: ''
        });
    }


    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input
                    type="text" 
                    name="message" 
                    placeholder="입력하세요" 
                    value={this.state.message}
                    onChange={this.handleChange}
                />

                <button onClick={this.handleClick}>확인</button>
            </div>
        );
    }
}

export default EventPractice;
```



### 04. input 여러 개 다루기

- input이 여러개 인 경우, **`event` 객체를 활용 !**

```react
// EventPractice.js

import { Component } from 'react';

class EventPractice extends Component {
    
    state = {
        username: '',
        message: ''
    }

    handleChange = (e) => {
        this.setState({
            [e.target.name]: e.target.value
            // e.target.name: 해당 input의 name
        });
    }

    handleClick = () =>  {
        alert(this.state.username + ': ' + this.state.message);
        this.setState({
            username: '',
            message: ''
        });
    }


    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input
                    type="text" 
                    name="username" 
                    placeholder="사용자명" 
                    value={this.state.username}
                    onChange={this.handleChange}
                />

                <input
                    type="text" 
                    name="message" 
                    placeholder="입력하세요" 
                    value={this.state.message}
                    onChange={this.handleChange}
                />

                <button onClick={this.handleClick}>확인</button>
            </div>
        );
    }
}

export default EventPractice;
```

```react
handleChange = (e) => {
    this.setState({
        [e.target.name]: e.target.value
        // e.target.name: 해당 input의 name
    });
}
```

> 객체 안에서 key를 [ ] 로 감싸면 그 안에 넣은 레퍼런스가 가리키는 실제 값이 key로 사용됨.
>
> ex.
>
> ```react
> const name = '승현';
> const obj = {
> 	[name]: 'value'
> }
> 
> ----------------------
> // obj 결과
> {
> 	'승현': 'value'
> }
> ```



## 05. onKeyPress 이벤트 핸들링

- 키를 눌렀을 때 발생하는 KeyPress 이벤트 처리
- input에서 Enter를 눌렀을 때 handleClick 메서드 호출

```react
// EventPractice.js

import { Component } from 'react';

class EventPractice extends Component {
    
    state = {
        message: ''
    }

    handleChange = (e) => {
        this.setState({
            [e.target.name]: e.target.value
            // e.target.name: 해당 input의 name
        });
    }

    handleClick = () =>  {
        alert(this.state.message);
        this.setState({
            message: ''
        });
    }

    handleKeyPress = (e) => {
        if(e.key === 'Enter') {
            this.handleClick();
        }
    }


    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input
                    type="text" 
                    name="message" 
                    placeholder="입력하세요" 
                    value={this.state.message}
                    onChange={this.handleChange}
                    onKeyPress={this.handleKeyPress}
                />
                <button onClick={this.handleClick}>확인</button>
            </div>
        );
    }
}

export default EventPractice;
```



---



## 함수 컴포넌트로 구현해보기

```react
// EventPractice.js

import { useState } from "react";

const EventPractice = () => {
    const [username, setUsername] = useState('');
    const [message, setMessage] = useState('');
    const onChangeUsername = e => setUsername(e.target.value);
    const onChangeMessage = e => setMessage(e.target.value);
    const onClick = () => {
        alert(username + ': ' + message);
        setMessage('');
        setUsername('');
    };
    const onKeyPress = (e) => {
        if(e.key === 'Enter') {
            onClick();
        }
    };

    return(
        <div>
            <h1>이벤트 연습</h1>
            <input
                type="text"
                name="username"
                placeholder="사용자명"
                value={username}
                onChange={onChangeUsername}
            />

            <input
                type="text"
                name="message"
                placeholder="메시지를 입력하세요"
                value={message}
                onChange={onChangeMessage}
                onKeyPress={onKeyPress}
            />

            <button onClick={onClick}>확인</button>
        </div>
    );
}

export default EventPractice;
```



```react
// EventPractice.js

import { useState } from "react";

const EventPractice = () => {
    const [form, setForm] = useState({
        username: '',
        message: ''
    });
    const { username, message } = form;

    const onChange = e => {
        const nextForm = {
            ...form,          // 기존 form의 내용을 복사한 뒤
            [e.target.name]: e.target.value     // 원하는 값 덮어씌우기
        };
        setForm(nextForm);
    };

    const onClick = () => {
        alert(username + ': ' + message);
        setForm({
            username: '',
            message: ''
        });
    };
    const onKeyPress = (e) => {
        if(e.key === 'Enter') {
            onClick();
        }
    };

    return(
        <div>
            <h1>이벤트 연습</h1>
            <input
                type="text"
                name="username"
                placeholder="사용자명"
                value={username}
                onChange={onChange}
            />

            <input
                type="text"
                name="message"
                placeholder="메시지를 입력하세요"
                value={message}
                onChange={onChange}
                onKeyPress={onKeyPress}
            />

            <button onClick={onClick}>확인</button>
        </div>
    );
}

export default EventPractice;
```

> `e.target.name` 값을 활용하려면, useState 를 쓸 때 인풋 값들이 들어있는 form 객체를 사용하면 됨.
