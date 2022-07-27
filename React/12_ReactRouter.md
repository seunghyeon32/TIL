# React Router

[TOC]

## 01. 라우팅 (Routing)

- 사용자가 요청한 URL에 따라 알맞은 페이지를 보여주는 것



✔ **리액트에서 라우트 시스템 구축**

1. **리액트 라우터 (react router)**

   : 리액트의 라우팅 관련 라이브러리들 중 가장 오래됐고, 많이 사용됨. 컴포넌트 기반으로 라우팅 시스템 설정

2. **Next.js**

   : 리액트 프로젝트의 프레임워크. Create React App과 같이 프로젝트 설정 기능, 라우팅 시스템, 최적화 등 다양한 기능을 제공함. 

   라우팅 시스템은 파일 경로 기반으로 작동

3. react-location, rakkas 등 존재



---



## 02. SPA (Single Page Application)

- SPA : 하나의 페이지로 이루어진 애플리케이션
- html은 한번만 받아와서 웹 애플리케이션을 실행시킨 후, 이후에는 필요한 데이터만 받아와서 화면에 업데이트 하는 것



✔ **MPA (Multi Page Application)**

- 사용자가 다른 페이지로 이동할 때마다 새로운 html을 받아오고, 페이지를 로딩할 때마다 서버에서 CSS, JS, 이미지 파일 등의 리소스를 전달받아 브라우저 화면에 보여줌.
- 사용자 인터렉션이 별로 없는 정적인 페이지에 적합

- 사용자 인터렉션이 많고, 다양한 정보를 제공하는 경우
  - 새로운 페이지를 보여줘야할 때마다 서버 측에서 모든 준비를 한다면 서버 자원이 낭비되고, 트래픽도 증가
- 렌더링을 사용자의 브라우저가 담당, 사용자와의 인터렉션에서 필요한 부분만 자바스크립트를 사용해 업데이트, 서버 API 호출을 통해 필요한 데이터만 새로 불러와 사용



---



## 03. 리액트의 라우터 적용 및 기본 사용법

### 03-1. 프로젝트 생성 및 라이브러리 설치

```bash
$ yarn create react-app router-tutorial
$ cd router-tutorial
$ yarn add react-router-dom
```



### 03-2. 프로젝트에 라우터 적용

```react
// src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import { BrowserRouter } from 'react-router-dom';

ReactDOM.render(
  <BrowserRouter>
    <App/>
  </BrowserRouter>,
  document.getElementById('root')
);
```

> 웹 애플리케이션에 HTML5의 History API를 사용하여 
>
> **페이지를 새로 불러오지 않고 주소를 변경**하고 
>
> **현재 주소의 경로에 관련된 정보**를 리액트 컴포넌트에서 사용할 수 있도록 해 줌.



### 03-3. 페이지 컴포넌트 만들기

- `src/pages/` => `src/routes/` 의 경로 혹은 `src/` 사용 가능

```react
// pages/Home.js

const Home = () => {
    return (
        <div>
            <h1>Home</h1>
            <p>가장 먼저 보여지는 페이지입니다.</p>
        </div>
    );
};

export default Home;
```

```react
// pages/About.js

const About = () => {
    return (
        <div>
            <h1>소개</h1>
            <p>리액트 라우터를 사용해 보는 프로젝트입니다.</p>
        </div>
    );
};

export default About;
```



### 03-4. Route 컴포넌트로 특정 경로에 원하는 컴포넌트 보여주기

✔ **Route 컴포넌트 사용 규칙**

```react
<Route path="주소 규칙" element={보여줄 컴포넌트 JSX}/>
```

- Route 컴포넌트는 Routes 컴포넌트 내부에서 사용되어야 함



```react
// App.js

import { Route, Routes } from "react-router-dom";
import About from "./pages/About";
import Home from "./pages/Home";

const App = () => {
  return (
    <Routes>
      <Route path="/" element={<Home/>}/>
      <Route path="/about" element={<About/>}/>
    </Routes>
  );
};

export default App;
```



### 03-5. Link 컴포넌트를 사용하여 다른 페이지로 이동하는 링크 보여주기

- a 태그 ❌❌❌
  - a 태그를 클릭하여 페이지를 이동할 때, 브라우저에서는 페이지를 새로 불러오게 되기 때문
- Link 컴포넌트 역시 a 태그를 사용하긴 하지만, 페이지를 새로 불러오는 것을 막고 History API를 통해 브라우저 주소의 경로만 바꾸는 기능이 내장

```react
<Link to="경로">링크 이름</Link>
```

```react
// pages/Home.js

import { Link } from 'react-router-dom';

const Home = () => {
    return (
        <div>
            <h1>Home</h1>
            <p>가장 먼저 보여지는 페이지입니다.</p>
            <Link to="/about">소개</Link>
        </div>
    );
};

export default Home;
```



---



## 04. URL 파라미터와 쿼리스트링

- URL 파라미터 
  - 주소의 경로에 유동적인 값을 넣는 형태
  - 주로 ID 또는 이름을 사용하여 특정 데이터를 조회할 때 사용
  - ex) `/profile/velopert`
- 쿼리스트링 
  - 주소의 뒷부분에 `?` 문자열 이후에 `key=value` 로정의하며 `&`로 구분하는 형태
  - 키워드 검색, 페이지네이션, 정렬 방식 등 데이터 조회에 필요한 옵션을 전달할 때 사용
  - ex) `/articles?page=1&keyword=react`



### 04-1. URL 파라미터

```react
// pages/Profile.js

import { useParams } from "react-router-dom";

const data = {
    velopert: {
        name: '김민준',
        description: '리액트를 좋아하는 개발자'
    },
    gildong: {
        name: '홍길동',
        description: '고전 소설 홍길동전의 주인공'
    },
};

const Profile = () => {
    const params = useParams();
    const profile = data[params.username];

    return (
        <div>
            <h1>사용자 프로필</h1>
            {profile ? (
                <div>
                    <h2>{profile.name}</h2>
                    <p>{profile.description}</p>
                </div>
            ) : (
                <p>존재하지 않는 프로필 입니다.</p>
            )}
        </div>
    );
};

export default Profile;
```

> useParams : URL 파라미터를 객체 형태로 조회
>
> URL 파라미터의 이름은 라우트 설정을 할 때 Route 컴포넌트의 path props를 통해 설정 (App.js)

```react
// App.js

import { Route, Routes } from "react-router-dom";
import About from "./pages/About";
import Home from "./pages/Home";
import Profile from "./pages/Profile";

const App = () => {
  return (
    <Routes>
      <Route path="/" element={<Home/>}/>
      <Route path="/about" element={<About/>}/>
      <Route path="/profiles/:username" element={<Profile/>}/>
    </Routes>
  );
};

export default App;
```

```react
// pages/Home.js

import { Link } from 'react-router-dom';

const Home = () => {
    return (
        <div>
            <h1>Home</h1>
            <p>가장 먼저 보여지는 페이지입니다.</p>
            <ul>
                <li>
                    <Link to="/about">소개</Link>
                </li>
                <li>
                    <Link to="/profiles/velopert">velopert의 프로필</Link>
                </li>
                <li>
                    <Link to="/profiles/gildong">gildong의 프로필</Link>
                </li>
                <li>
                    <Link to="/profiles/void">존재하지 않는 프로필</Link>
                </li>
            </ul>
        </div>
    );
};

export default Home;
```

> Profile 페이지로 이동할 수 있도록 Home 페이지에 Link 생성



### 04-2. 쿼리스트링

- 별도로 설정해야 하는 것이 없음

```react
// pages/About.js

import { useLocation } from "react-router-dom";

const About = () => {
    const location = useLocation();

    return (
        <div>
            <h1>소개</h1>
            <p>리액트 라우터를 사용해 보는 프로젝트입니다.</p>
            <p>쿼리 스트링: {location.search}</p>
        </div>
    );
};

export default About;
```

> useLocation : 현재 사용자가 보고 있는 페이지의 정보를 가짐
>
> - pathname : 현재 주소의 경로 (쿼리스트링 제외)
> - search : 맨 앞의 ? 문자를 포함한 쿼리스트링 값
> - hash : 주소의 # 문자열 뒤의 값 
>   - 주로 History API가 지원되지 않는 구형 브라우저에서 클라이언트 라우팅을 사용할 때 쓰는 해시 라우터에 사용
> - state : 페이지로 이동할 때 임의로 넣을 수 있는 상태 값
> - key : location 객체의 고유값, 초기에는 default 이며 페이지가 변경될 때마다 고유값이 생성됨



#### useSearchParams 를사용해 쿼리스트링 파싱하여 사용하기

```react
// pages/About.js

import { useSearchParams } from "react-router-dom";

const About = () => {
    const [searchParams, setSearchParams] = useSearchParams();
    const detail = searchParams.get('detail');
    const mode = searchParams.get('mode');

    const onToggleDetail = () => {
        setSearchParams({ mode, detail: detail === 'true' ? false : true });
    };

    const onIncreaseMode = () => {
        const nextMode = mode === null ? 1 : parseInt(mode) + 1;
        setSearchParams({ mode: nextMode, detail });
    };

    return (
        <div>
            <h1>소개</h1>
            <p>리액트 라우터를 사용해 보는 프로젝트입니다.</p>
            <p>detail: {detail}</p>
            <p>mode: {mode}</p>
            <button onClick={onToggleDetail}>Toggle Detail</button>
            <button onClick={onIncreaseMode}>mode + 1</button>
        </div>
    );
};

export default About;
```

> `useSearchParams` : 배열타입의 값을 반환
>
> 1. 쿼리파라미터를 조회하거나 수정하는 메서드들이 담긴 객체 반환
>    - get 메서드를 통해 특정 쿼리파라미터를 조회할 수 있고, set 메서드를 통해 특정 쿼리파라미터 업데이트 가능
>    - 조회 시, 존재하지 않는 경우 null로 조회
> 2. 객체 형태로 업데이트 할 수 있는 함수



✔ **쿼리파라미터를 사용할 때 주의할 점**

: 조회할 때, 값은 **무조건 문자열 타입 !!!!** => 문자열로 만들거나 parse !



---



## 05. 중첩된 라우트

```react
// pages/Articles.js

import { Link } from 'react-router-dom';

const Articles = () => {
    return (
        <ul>
            <li>
                <Link to="/articles/1">게시글 1</Link>
            </li>
            <li>
                <Link to="/articles/2">게시글 2</Link>
            </li>
            <li>
                <Link to="/articles/3">게시글 3</Link>
            </li>
        </ul>
    )
}

export default Articles;
```

```react
// pages/Article.js

import { useParams } from "react-router-dom";

const Article = () => {
    const { id } = useParams();

    return (
        <div>
            <h2>게시글 {id}</h2>
        </div>
    );
};

export default Article;
```

```react
// App.js

import { Route, Routes } from "react-router-dom";
import About from "./pages/About";
import Home from "./pages/Home";
import Profile from "./pages/Profile";
import Article from "./pages/Article";
import Articles from "./pages/Articles";

const App = () => {
  return (
    <Routes>
      <Route path="/" element={<Home/>}/>
      <Route path="/about" element={<About/>}/>
      <Route path="/profiles/:username" element={<Profile/>}/>
      <Route path="/articles" element={<Articles/>}/>
      <Route path="/articles/:id" element={<Article/>}/>
    </Routes>
  );
};

export default App;
```

```react
// pages/Home.js

import { Link } from 'react-router-dom';

const Home = () => {
    return (
        <div>
            <h1>Home</h1>
            <p>가장 먼저 보여지는 페이지입니다.</p>
            <ul>
                <li>
                    <Link to="/about">소개</Link>
                </li>
                <li>
                    <Link to="/profiles/velopert">velopert의 프로필</Link>
                </li>
                <li>
                    <Link to="/profiles/gildong">gildong의 프로필</Link>
                </li>
                <li>
                    <Link to="/profiles/void">존재하지 않는 프로필</Link>
                </li>
                <li>
                    <Link to="/articles">게시글 목록</Link>
                </li>
            </ul>
        </div>
    );
};

export default Home;
```



### 중첩된 라우트 형태로 구현

```react
// App.js

import { Route, Routes } from "react-router-dom";
import About from "./pages/About";
import Home from "./pages/Home";
import Profile from "./pages/Profile";
import Article from "./pages/Article";
import Articles from "./pages/Articles";

const App = () => {
  return (
    <Routes>
      <Route path="/" element={<Home/>}/>
      <Route path="/about" element={<About/>}/>
      <Route path="/profiles/:username" element={<Profile/>}/>
      <Route path="/articles" element={<Articles/>}>
        <Route path=":id" element={<Article/>}/>
      </Route>
    </Routes>
  );
};

export default App;
```



✔ **Outlet 컴포넌트**

: Route의 children으로 들어가는 JSX 엘리먼트를 보여주는 역할

```react
<Route path=":id" element={<Article/>}/>
```

```react
// pages/Articles.js

import { Link, Outlet } from 'react-router-dom';

const Articles = () => {
    return (
        <div>
            <Outlet/ >
            <ul>
                <li>
                    <Link to="/articles/1">게시글 1</Link>
                </li>
                <li>
                    <Link to="/articles/2">게시글 2</Link>
                </li>
                <li>
                    <Link to="/articles/3">게시글 3</Link>
                </li>
            </ul>
        </div>
    )
}

export default Articles;
```





### 05-1. 공통 레이아웃 컴포넌트

- 페이지끼리 공통적으로 보여줘야하는 레이아웃이 있을 경우, 용이

```react
// Layout.js

import { Outlet } from "react-router-dom";

const Layout = () => {
    return (
        <div>
            <header style={{ background: 'lightgray', padding: 16, fontSize: 24 }}>
                Header
            </header>
            <main>
                <Outlet/>
            </main>
        </div>
    );
};

export default Layout;
```

```react
// App.js

import { Route, Routes } from "react-router-dom";
import Layout from "./Layout";
import About from "./pages/About";
import Home from "./pages/Home";
import Profile from "./pages/Profile";
import Article from "./pages/Article";
import Articles from "./pages/Articles";

const App = () => {
  return (
    <Routes>
      <Route element={<Layout/>}>
        <Route path="/" element={<Home/>}/>
        <Route path="/about" element={<About/>}/>
        <Route path="/profiles/:username" element={<Profile/>}/>
        </Route>
        
      <Route path="/articles" element={<Articles/>}>
        <Route path=":id" element={<Article/>}/>
      </Route>
    </Routes>
  );
};

export default App;
```



### 05-2. index props

- Route 컴포넌트에는 index라는 props가 존재. 이는 `path='/'` 와 동일한 의미

```react
// App.js

import { Route, Routes } from "react-router-dom";
import Layout from "./Layout";
import About from "./pages/About";
import Home from "./pages/Home";
import Profile from "./pages/Profile";
import Article from "./pages/Article";
import Articles from "./pages/Articles";

const App = () => {
  return (
    <Routes>
      <Route element={<Layout/>}>
        <Route index element={<Home/>}/>
        <Route path="/about" element={<About/>}/>
        <Route path="/profiles/:username" element={<Profile/>}/>
        </Route>

      <Route path="/articles" element={<Articles/>}>
        <Route path=":id" element={<Article/>}/>
      </Route>
    </Routes>
  );
};

export default App;
```

> index prop을 사용하면 상위 라우트의 경로와 일치하지만, 
>
> 그 이후에 경로가 주어지지 않았을 때 보여지는 라우트 설정 가능
>
> path="/" 와 동일한 역할



---



## 06. 리액트 라우터 부가 기능

### 06-1. useNavigate

- Link 컴포넌트를 사용하지 않고 다른 페이지로 이동해야하는 상황에 사용하는 Hook

```react
// Layout.js

import { Outlet, useNavigate } from "react-router-dom";

const Layout = () => {
    const navigate = useNavigate();

    const goBack = () => {
        // 이전 페이지로 이동
        navigate(-1);
        // -1: 뒤로 한번, -2: 뒤로 두번
        // 1: 앞으로 한번 (뒤로 가기를 한 상태에서)
    }

    const goArticles = () => {
        // articles 경로로 이동
        navigate('/articles');
    }

    return (
        <div>
            <header style={{ background: 'lightgray', padding: 16, fontSize: 24 }}>
                <button onClick={goBack}>뒤로가기</button>
                <button onClick={goArticles}>게시글 목록</button>
            </header>
            <main>
                <Outlet/>
            </main>
        </div>
    );
};

export default Layout;
```



✔ **replace** : 페이지를 이동할 때 현재 페이지를 기록에 남기지 ❌ 

```react
// Layout.js - goArticles

const goArticles = () => {
    // articles 경로로 이동
    navigate('/articles', { replace: true });
}
```



### 06-2. NavLink

- 링크에서 사용하는 경로가 현재 라우트의 경로와 일치하는 경우, 특정 스타일 또는 CSS 클래스를 적용하는 컴포넌트
- style 과 className은 { isActive: boolean }을 파라미터로 전달받는 함수 타입의 값을 전달

```react
<NavLink
    style={({isActive}) => isActive ? activeStyle : undefined}
/>

<NavLink
    className={({isActive}) => isActive ? 'active' : undefined}
/>

```

```react
// pages/Articles.js

import { NavLink, Outlet } from 'react-router-dom';

const Articles = () => {
    const activeStyle = {
        color: 'green',
        fontSize: 21,
    };

    return (
        <div>
            <Outlet/ >
            <ul>
                <li>
                    <NavLink to="/articles/1"
                        style={({isActive}) => (isActive ? activeStyle : undefined )}>
                        게시글 1
                    </NavLink>
                </li>
                <li>
                    <NavLink to="/articles/2"
                        style={({isActive}) => (isActive ? activeStyle : undefined )}>
                        게시글 2
                    </NavLink>
                </li>
                <li>
                    <NavLink to="/articles/3"
                        style={({isActive}) => (isActive ? activeStyle : undefined )}>
                        게시글 3
                    </NavLink>
                </li>
            </ul>
        </div>
    )
}

export default Articles;
```

```react
// pages/Articles.js

import { NavLink, Outlet } from 'react-router-dom';

const Articles = () => {
    return (
        <div>
            <Outlet/ >
            <ul>
                <ArticleItem id={1}/>
                <ArticleItem id={2}/>
                <ArticleItem id={3}/>
            </ul>
        </div>
    )
}



const ArticleItem = ({ id }) => {
    const activeStyle = {
        color: 'green',
        fontSize: 21,
    };
    return (
        <li>
            <NavLink
                to={`/articles/${id}`}
                style={({isActive}) => ( isActive ? activeStyle : undefined )}>
                게시글 {id}
            </NavLink>
        </li>
    )
}

export default Articles;
```



### 06-3. NotFound 페이지 만들기

- 사전에 정의되지 않는 경로로 사용자가 진입했을 때 보여주는 페이지

```react
// pages/NotFound.js

const NotFound = () => {
    return (
        <div 
            style={{
                display: 'flex',
                alignItems: 'center',
                justifyCenter: 'center',
                fontSize: 64,
                position: 'absolute',
                width: '100%',
                height: '100%',
            }}>
                04
        </div>
    );
};

export default NotFound;
```

```react
// App.js

import { Route, Routes } from "react-router-dom";
import Layout from "./Layout";
import About from "./pages/About";
import Home from "./pages/Home";
import Profile from "./pages/Profile";
import Article from "./pages/Article";
import Articles from "./pages/Articles";
import NotFound from "./pages/NotFound";

const App = () => {
  return (
    <Routes>
      <Route element={<Layout/>}>
        <Route index element={<Home/>}/>
        <Route path="/about" element={<About/>}/>
        <Route path="/profiles/:username" element={<Profile/>}/>
        </Route>

      <Route path="/articles" element={<Articles/>}>
        <Route path=":id" element={<Article/>}/>
      </Route>
      <Route path="*" element={<NotFound/>}/>
    </Routes>
  );
};

export default App;
```



### 06-4. Navigate 컴포넌트

- 컴포넌트를 화면에 보여주는 순간 다른 페이지로 이동하고 싶을 때 사용

  **" 페이지를 리다이렉트하고 싶을 때"**

```react
// pages/Login.js

const Login = () => {
    return <div>로그인 페이지</div>;
};

export default Login;
```

```react
// pages/MyPage.js

import { Navigate } from "react-router-dom";

const MyPage = () => {
    const isLoggedIn = false;

    if (!isLoggedIn) {
        return <Navigate to="/login" replace={true} />;
    }

    return <div>마이 페이지</div>;
};

export default MyPage;
```

> isLoggedIn이 false (로그인이 되지 않은 경우) 일 때, /login 경로로 이동
>
> replace props로 인해 페이지를 이동할 때, 현재 페이지를 기록에 남기지 않기 때문에 이동 후 뒤로가기 눌렀을 때 두 페이지 전의 페이지로 이동

```react
// App.js

import { Route, Routes } from "react-router-dom";
import Layout from "./Layout";
import About from "./pages/About";
import Article from "./pages/Article";
import Articles from "./pages/Articles";
import Home from "./pages/Home";
import Login from "./pages/Login";
import MyPage from "./pages/MyPage";
import NotFound from "./pages/NotFound";
import Profile from "./pages/Profile";

const App = () => {
  return (
    <Routes>
      <Route element={<Layout/>}>
        <Route index element={<Home/>}/>
        <Route path="/about" element={<About/>}/>
        <Route path="/profiles/:username" element={<Profile/>}/>
        </Route>

      <Route path="/articles" element={<Articles/>}>
        <Route path=":id" element={<Article/>}/>
      </Route>
      
      <Route path="/login" element={<Login/>}/>
      <Route path="/mypage" element={<MyPage/>}/>
      <Route path="*" element={<NotFound/>}/>
    </Routes>
  );
};

export default App;
```

