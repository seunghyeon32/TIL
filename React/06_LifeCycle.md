# 컴포넌트의 라이프사이클 메서드

[TOC]

‼ 라이프사이클 메서드는 클래스형 컴포넌트에서만 사용할 수 있음 ‼ 



- **Will** : 어떤 작업을 작동하기 "전"에 실행되는 메서드

- **Did** : 어떤 작업을 작동한 "후"에 실행되는 메서드

---



## 라이프사이클 메서드 이해



### 01. 마운트 (mount)

- DOM이 생성되고 웹 브라우저상에 나타나는 것

``` 
1. constructor
 : 컴포넌트를 새로 만들 때마다 호출되는 클래스 생성자 메서드
2. getDerivedStateFromProps
 : props에 있는 값을 state에 넣을 때 사용하는 메서드
3. render
 : UI를 렌더링하는 메서드
4. componentDidMount
 : 컴포넌트가 웹 브라우저상에 나타난 후 호출하는 메서드
```



### 02. 업데이트

1. props가 바뀔 때
2. state가 바뀔 때 (setState)
3. 부모 컴포넌트가 리렌더링될 때
4. this.forceUpdate로 강제로 렌더링을 트리거 할 때



---

1. 업데이트를 발생시키는 요인
 ex. props 변경, state 변경, 부모 컴포넌트 리렌더링

2. getDerivedStateFromProps
     - props의 변화에 따라 state값에도 변화를 주고 싶을 때 사용
     - 마운트 과정, 업데이트가 시작하기 전에 호출
3. shouldComponentUpdate
   - 컴포넌트가 리렌더링을 해야 할지 말아야할지를 결정하는 메서드
   - true : 다음 라이프사이클 메서드 계속 실행
   - false : 작업 중지
   - `this.forceUpdate()` 함수 호출 시, 바로 render 함수 호출
4. render
   - 컴포넌트를 리렌더링
5. getSnapshotBeforeUpdate
  - 컴포넌트 변화를  DOM에 반영하기 바로 직전에 호출하는 메서드
6. componentDidUpdate
  - 컴포넌트의 업데이트 작업이 끝난 후 호출

---



### 03. 언마운트

- 컴포넌트를 DOM에서 제거하는 것
- `componentWillUnmount` : 컴포넌트가 웹 브라우저상에서 사라지기 전에 호출하는 메서드



---





## 라이프사이클 메서드

### 01. render()

- 컴포넌트의 모양새 정의
- this.props / this.state에 접근 가능
- 이벤트 설정이 아닌 곳에서  setState를 사용하면 안되며, DOM에 접근해서도 안됨



### 02. constructor 

```
constructor(props) {
	...
}
```

- 컴포넌트의 생성자 메서드
- 초기 state 설정 가능



### 03. getDerivedStateFromProps

- props로 받아 온 값을 state에 동기화시키는 용도
- 컴포넌트가 마운트될 때와 업데이트될 때 호출

```react
static getDerivedStateFromProps(nextProps, prevState) {
	if(nextProps.value !== prevState.value) {
        return {value: nextProps.value};
    }
    return null;     // state를 변경할 필요가 없다면 null 반환
}
```





### 04. componentDidMount

```react
componentDidMount() { ... }
```

- 컴포넌트를 만들고, 첫 렌더링을 다 마친 후 실행

- 비동기 작업 처리



### 05. shouldComponentUpdate

```react
shouldComponentUpdate(nextProps, nextState) {...}
```

- props 또는 state를 변경했을 때, **리렌더링을 시작할지 여부를 지정**하는 메서드
- 반드시 true (default) / false 값을 반환해야 함

- false 값을 반환한다면 업데이트 과정 중지
- props와 state는 this.props, this.state로 접근하고, 새로 설정된 props 또는 state는 nextProps와 nextState로 접근 가능
- 리렌더링 방지 => "false" 반환



### 06. getSnapshotBeforeUpdate

- render에서 만들어진 결과물이 브라우저에 **실제로 반영되기 직전**에 호출

- 반환 값 : `componentDidUpdate`에서 세 번쟤 파라미터인 snapshot 값으로 전달받을 수 있음
- 업데이트하기 직전의 값을 참고 할 일이 있을 때 활용

```react
getSnapshotBeforeUpdate(prevProps, prevState) {
    if(prevState.array !== this.state.array) {
        const { scrollTop, scrollHeight } = this.list
        return { scrollTop, scrollHeight };
    }
}
```



### 07. componentDidUpdate

```
componentDidUpdate(prevProps, prevState, snapshot) { ... }
```

- 리렌더링을 완료한 후 실행
- 업데이트가 끝난 직후이므로, DOM 관련 처리를 해도 무방
- prevProps 또는 prevState를 사용하여 컴포넌트가 이전에 가졌던 데이터에 접근 가능
- getSnapshotBeforeUpdate에서 반환한 값이 있다면 여기서 snapshot 값을 전달받을 수 있음



### 08. componentWillUnmount

```
componentWillUnmount() {...}
```

- 컴포넌트를 DOM에서 제거할 때 실행
- componentDidMount에서 등록한 이벤트, 타이머, 직접 생성한 DOM이 있다면 여기서 제거 작업



### 09. componentDidCatch

- 컴포넌트 렌더링 도중에 에러가 발생했을 떄 애플리케이션이 먹통이 되지 않고 오류 UI를 보여 줄 수 있게 해 줌

```react
componentDidCatch(error, info) {
	this.setState({
        error: true
    });
    console.log({ error, info });
}
```

- error : 파라미터에 어떤 에러가 발생했는지 알려줌
- info : 어디에 있는 코드에서 오류가 발생했는지에 대한 정보를 줌
- 컴포넌트 자신에게 발생하는 에러를 잡아낼 수 없고 자신의 this.props.children으로 전달되는 컴포넌트에서 발생하는 에러만 잡아낼 수 있음



---



## 라이프사이클 메서드 실습

```
1. LifeCycleSample 컴포넌트 생성
2. App에 렌더링
3. 버튼 누르고 콘솔 창 관찰
```

```react
// LifeCycleSample.js

import { Component } from "react";

class LifeCycleSample extends Component {
    state = {
        number: 0,
        color: null
    }

    myRef = null; // ref를 설정할 부분

    constructor(props) {
        super(props);
        console.log('constructor');
    }

    // 부모에게서 받은 color 값을 state에 동기화
    static getDerivedStateFromProps(nextProps, prevState) {
        console.log('getDerivedStateFromProps');
        if(nextProps.color !== prevState.color) {
            return {color: nextProps.color};
        }
        return null;     // state를 변경할 필요가 없다면 null 반환
    }

    // 첫 렌더링 완료 후, 실행
    componentDidMount() {
        console.log('componentDidMount');
    }

    // 리렌더링 시작 여부 지정
    shouldComponentUpdate(nextProps, nextState) {
        console.log('shouldComponentUpdate', nextProps, nextState);
        // 숫자의 마지막 자리가 4면 리렌더링 취소
        return nextState.number % 10 !== 4;     // true or false로 반환
    }

    componentWillUnmount() {
        console.log('componentWillUnmount');
    }

    handleClick = () => {
        this.setState({
            number: this.state.number+1
        });
    }

    // render에서 만들어진 결과물이 브라우저에 실제로 반영되기 직전에 호출
    // DOM에 변화가 일어나기 직전의 색상 속성을 snapshot 값으로 반환
    getSnapshotBeforeUpdate(prevProps, prevState) {
        console.log('getSnapshotBeforeUpdate');
        if (prevProps.color !== this.props.color) {
            return this.myRef.style.color;
        }
        return null;
    }

    // 리렌더링을 완료한 후 실행
    // getSnapshotBeforeUpdate에서 반환값이 있을 때, 
    // snapshot값으로 전달받음(조회)
    componentDidUpdate(prevProps, prevState, snapshot) {
        console.log('componentDidUpdate', prevProps, prevState);
        if(snapshot) {
            console.log('업데이트되기 직전 색상: ', snapshot)
        }
    }

    

    render() {
        console.log('render');

        const style={
            color: this.props.color
        };

        return (
            <div>
                <h1 style={style} ref={(ref)=> this.myRef=ref}>
                    {this.state.number}
                </h1>
                <p>color: {this.state.color}</p>
                <button onClick={this.handleClick}>더하기</button>
            </div>
        )
    }
}

export default LifeCycleSample;
```

> 각 라이프사이클 메서드를 실행할 때마다 콘솔 디버거에 기록하고, 부모 컴포넌트에서 props로 색상을 받아 버튼을 누르면  state.number 값을 1씩 더함

```react
// App.js

import { Component } from "react";
import LifeCycleSample from "./LifeCycleSample.js";

function getRandomColor() {
  return '#' + Math.floor(Math.random() * 16777215).toString(16);
}

class App extends Component {

  state = {
    color: '#000000'
  }

  handleClick = () => {
    this.setState({
      color: getRandomColor()
    });
  }

  render() {
    return (
      <div>
        <button onClick={this.handleClick}>랜덤 색상</button>
        <LifeCycleSample color={this.state.color}/>
      </div>
    );
  }
}

export default App;
```

> getRandomColor : state의 color값을 랜덤 색상으로 설정
>
> 16777215를 hex로 표현하면 ffffff가 되므로 해당 코드는 000000 ~ ffffff 값을 반환함.

- 버튼을 렌더링하고, 누를 때마다 handleClick 메서드가 호출되게 이벤트 설정
- 불러온 LifeCycleSample 컴포넌트에 color 값을 props로 설정

---

### 에러 잡아내기

- render 함수에서의 에러는 주로 존재하지 않는 함수를 사용하려고 하거나, 존재하지 않는 객체의 값을 조회하려고 할 때 발생

```react
// ErrorBoundary.js

import { Component } from "react";

class ErrorBoundary extends Component {
    state = {
        error: false
    }

    componentDidCatch(error, info) {
        this.setState({
            error: true
        });
        console.log({ error, info });
    }

    render() {
        if (this.state.error) return <div>에러가 발생했습니다.</div>;
        return this.props.children;
    }
}
```

> 에러가 발생하면 componentDidCatch 메서드가 호출
>
> => this.state.error 값을  true로 업데이트
>
> render() => this.state.error가 true라면 에러가 발생했음을 알려주는 문구 표시

```react
// App.js

import { Component } from "react";
import LifeCycleSample from "./LifeCycleSample.js";
import ErrorBoundary from "./ErrorBoundary.js"

function getRandomColor() {
  return '#' + Math.floor(Math.random() * 16777215).toString(16);
}

class App extends Component {

  state = {
    color: '#000000'
  }

  handleClick = () => {
    this.setState({
      color: getRandomColor()
    });
  }

  render() {
    return (
      <div>
        <button onClick={this.handleClick}>랜덤 색상</button>
        <ErrorBoundary>
          <LifeCycleSample color={this.state.color}/>
        </ErrorBoundary>
      </div>
    );
  }
}

export default App;
```

