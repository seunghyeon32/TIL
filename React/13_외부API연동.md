# 외부 API 연동하여 뉴스뷰어 만들기

[TOC]

## 01. 비동기 작업의 이해



### 01-1. 콜백 함수

- 파라미터 값이 주어지면 1초 뒤에 10을 더해서 반환하는 함수

```react
function increase(number, callback) {
    setTimeout(() => {
        const result = number + 10;
      if(callback) {
          callback(result);
      }
    }, 1000)
}

increase(0, result => {
    console.log(result);
});
```



### 01-2. Promise

```react
function increase(number) {
    const promise = new Promise((resolve, reject) => {
        // resolve는 성공, reject는 실패
        setTimeout(() => {
            const result = number + 10;
            if (result > 50) {
                // 50보다 높으면 에러 발생시키기
                const e = new Error('NumberTooBig');
                return reject(e);
            }
            resolve(result); // number 값에 +10 후 성공 처리
        }, 1000);
    });
    return promise;
}

increase(0)
	.then(number => {
        // Promise에서 resolve된 값은 .then을 통해 받아 올 수 있음
        console.log(number);
        return increase(number);
	})
  	.then(number => {
        console.log(number);
        return increase(number);
	})
    .then(number => {
        console.log(number);
        return increase(number);
	})
   	.catch(e => {
    console.log(e);  // 도중에 에러 발생 시, 알수 있음
})
```



### 01-3. async/await

- 함수의 앞부분에 `async` 키워드를 추가하고, 해당 함수 내부에서 Promise 앞부분에 await 키워드를 사용

```react
function increase(number) {
    const promise = new Promise((resolve, reject) => {
        // resolve는 성공, reject는 실패
        setTimeout(() => {
            const result = number + 10;
            if (result > 50) {
                // 50보다 높으면 에러 발생시키기
                const e = new Error('NumberTooBig');
                return reject(e);
            }
            resolve(result); // number 값에 +10 후 성공 처리
        }, 1000);
    });
    return promise;
}

async function runTasks() {
    try {
        let result = await increase(0);
        console.log(result);
        result = await increase(result);
        console.log(result);
        result = await increase(result);
        console.log(result);
        result = await increase(result);
        console.log(result);
        result = await increase(result);
        console.log(result);
        result = await increase(result);
        console.log(result);
	} catch(e) {
    console.log(e)
	}
}
```



## 02. axios로 데이터 호출해서 받아오기

- HTTP 요청을 Promise 기반으로 처리

```bash
$ yarn create react-app news-viewer
$ cd news-viewer
$ yarn add axios
```

```json
// .prettierrc

{
    "singleQuote": true,
    "semi": true,
    "useTabs": false,
    "tabWidth": 2,
    "trailingComma": "all",
    "printWidth": 80
}
```

```json
// jsconfig.json

{
    "compilerOptions": {
        "target": "ES6"
    }
}
```

```react
// App.js

import { useState } from "react";
import axios from 'axios';

const App = () => {
  const [data, setData] = useState(null);

  const onClick = () => {
    axios.get('https://jsonplaceholder.typicode.com/todos/1').then(response => {
      setData(response.data);
    });
  };

  return (
    <div>
      <div>
        <button onClick={onClick}>불러오기</button>
      </div>
      {data && <textarea rows={7} value={JSON.stringify(data, null, 2)} readOnly={true}/>}
    </div>
  );
};

export default App;
```

```react
// App.js

import { useState } from "react";
import axios from 'axios';

const App = () => {
  const [data, setData] = useState(null);

  const onClick = async() => {
    try {
      const response = await axios.get('https://jsonplaceholder.typicode.com/todos/1',);
      setData(response.data);
    } catch(e) {
      console.log(e);
    }
  };

  return (
    <div>
      <div>
        <button onClick={onClick}>불러오기</button>
      </div>
      {data && <textarea rows={7} value={JSON.stringify(data, null, 2)} readOnly={true}/>}
    </div>
  );
};

export default App;
```

> async / await를 적용할 때는 `async () => {}` 형식



## 03. newsapi API 키 발급받기

https://newsapi.org/register

✔ **사용할 API 주소**

1. 전체뉴스 불러오기
   - `GET` https://newsapi.org/v2/top-headlines?country=kr&apiKey=????????
2. 특정 카테고리 뉴스 불러오기
   - `GET` https://newsapi.org/v2/top-headlines?country=kr&category=business&apiKey=????????
   - 카테고리 : business, entertainment, health, science, sports, technology





## 04. 뉴스 뷰어 UI 만들기

```bash
$ yarn add styled-components
```



### 04-1. NewsItem 만들기

```json
{
    "source": {
        "id": null,
        "name": "Khan.co.kr"
    },
    "author": "유경선 기자",
    "title": "여당 고발 블랙리스트 의혹 수사 확대하는 검찰···동부지검, 통일부·과기정통부 압수수색 - 향이네 h2.khan.co.kr",
    "description": "문재인 정부의 정부부처 산하기관 블랙리스트 의혹을 수사 중인 검찰이 수사 대상을 확대하고 있...",
    "url": "https://www.khan.co.kr/article/202207271513001",
    "urlToImage": "https://img.khan.co.kr/news/2022/07/27/news-p.v1.20220727.1bc40fe94a1c4cb89b66247e16e9469b_P1.png",
    "publishedAt": "2022-07-27T06:13:00Z",
    "content": ""
},
```

> - title: 제목
> - description: 내용
> - url: 링크
> - urlToImage: 뉴스 이미지

```react
// components/NewsItem.js

import styled from "styled-components";

const NewsItemBlock = styled.div`
    display: flex;

    .thumbnail {
        margin-right: 1rem;
        img {
            display: block;
            width: 160px;
            height: 100px;
            object-fit: cover;
        }
    }
    
    .contents {
        h2 {
            margin: 0;
            a {
                color: black;
            }
        }

        p {
            margin: 0;
            line-height: 1.5;
            margin-top: 0.5rem;
            white-space: normal;
        }
    }

    & + & {
        margin-top: 3rem;
    }
`;

const NewsItem = ({article}) => {
    const { title, description, url, urlToImage } = article;
    return (
        <NewsItemBlock>
            {urlToImage && (
                <div className="thumbnail">
                    <a href={url} target="_blank" rel="noopener noreferrer">
                        <img src={urlToImage} alt="thumbnail"/>
                    </a>
                </div>
            )}
            <div className="contents">
                <h2>
                    <a href={url} target="_blank" rel="noopener noreferrer">
                        {title}
                    </a>
                </h2>
                <p>{description}</p>
            </div>
        </NewsItemBlock>
    );
};

export default NewsItem;
```



### 04-2. NewsList 만들기

```react
// components/NewsList.js

import styled from "styled-components";
import NewsItem from "./NewsItem";

const NewsListBlock = styled.div`
    box-sizing: border-box;
    padding-bottom: 3rem;
    width: 768px;
    margin: 0 auto;
    margin-top: 2rem;
    @media screen and (max-width: 768px) {
        width: 100%;
        padding-left: 1rem;
        padding-right: 1rem;
    }
`;

const sampleArticle = {
    title: '제목',
    description: '내용',
    url: 'https://google.com',
    urlToImage: 'https://via.placeholder.com/160'
};

const NewsList = () => {
    return (
        <NewsListBlock>
            <NewsItem article={sampleArticle}/>
            <NewsItem article={sampleArticle}/>
            <NewsItem article={sampleArticle}/>
            <NewsItem article={sampleArticle}/>
            <NewsItem article={sampleArticle}/>
            <NewsItem article={sampleArticle}/>
        </NewsListBlock>
    )
}

export default NewsList;
```

```react
// App.js

import NewsList from "./components/NewsList";

const App = () => {
  return <NewsList/>
};

export default App;
```





---



## 05. 데이터 연동하기

- `useEffect` 를 사용하여 컴포넌트가 처음 렌더링되는 시점에 API를 요청하면 됨

☠ useEffect 에 등록하는 함수에 async를 붙이면 안됨 !!!!! 

> useEffect의 반환값은 뒷정리 함수이기 때문이다 !
>
> async/await를 사용하고 싶다면, 함수 내부에 async가 붙은 또 다른 함수를 만들어서 사용 



- loading 상태도 관리하여 API 요청이 대기중인지 판별
  - 대기 중일 때 true / 요청이 끝나면 false



```react
// components/NewsList.js

import styled from "styled-components";
import NewsItem from "./NewsItem";
import { useState, useEffect } from "react";
import axios from 'axios';

const NewsListBlock = styled.div`
    box-sizing: border-box;
    padding-bottom: 3rem;
    width: 768px;
    margin: 0 auto;
    margin-top: 2rem;
    @media screen and (max-width: 768px) {
        width: 100%;
        padding-left: 1rem;
        padding-right: 1rem;
    }
`;


const NewsList = () => {
    const [articles, setArticles] = useState(null);
    const [loading, setLoading] = useState(false);

    useEffect(() => {
        // async를 사용하는 함수 따로 선언
        const fetchData = async() => {
            setLoading(true);
            try {
                const response = await axios.get(
                    'https://newsapi.org/v2/top-headlines?country=kr&apiKey=f83a3c447cca45f194986b5586bc6e94',
                );
                setArticles(response.data.articles);
            } catch(e) {
                console.log(e);
            }
            setLoading(false);
        };
        fetchData();
    }, []);

    // 대기 중일 때
    if(loading) {
        return <NewsListBlock>대기중 ...</NewsListBlock>;
    }

    // 아직 articles 값이 설정되지 않았을 때 (필수)
    if (!articles) {
        return null;
    }

    // articles 값이 유효할  때
    return (
        <NewsListBlock>
            {articles.map(article => (
                <NewsItem key={article.url} article={article} />
            ))}
        </NewsListBlock>
    );
};

export default NewsList;
```

> map 함수를 사용하기 전에 꼭 `!articles`를 조회하여 해당 값이 현재 null인지 아닌지 검사
>
> - 아직 데이터가 없을 때 null에는 map함수가 없기 때문에 렌더링 과정에서 오류가 발생



---



## 06. 카테고리 기능 구현하기

- business (비즈니스)
-  science (과학)
- entertainment (엔터테인먼트)
- sports (스포츠)
- health (건강)
- technology (기술)



### 06-1. 카테고리 선택 UI 만들기

```react
// components/Categories.js

import styled from "styled-components";

const categories = [
    {
        name: 'all',
        text: '전체보기'
    },
    {
        name: 'business',
        text: '비즈니스'
    },
    {
        name: 'science',
        text: '과학'
    },
    {
        name: 'entertainment',
        text: '엔터테인먼트'
    },
    {
        name: 'sports',
        text: '스포츠'
    },
    {
        name: 'health',
        text: '건강'
    },
    {
        name: 'technology',
        text: '기술'
    }
];

const CategoriesBlock = styled.div`
    display: flex;
    padding: 1rem;
    width: 768px;
    margin: 0 auto;
    @media screen and (max-width: 768px){
        width: 100%;
        overflow-x: auto;
    }
`;

const Category = styled.div`
    font-size: 1.125rem;
    cursor: pointer;
    white-space: pre;
    text-decoration: none;
    color: inherit;
    padding-bottom: 0.25rem;

    &:hover { 
        color: #495057;
    }

    & + & {
        margin-left: 1rem;
    }
`;

const Categories = () => {
    return (
        <CategoriesBlock>
            {categories.map(c => (
                <Category key={c.name}>{c.text}</Category>
            ))}
        </CategoriesBlock>
    )
}

export default Categories;
```

```react
// App.js

import NewsList from "./components/NewsList";
import Categories from "./components/Categories";

const App = () => {
  return (
    <>
      <Categories/>
      <NewsList/>
    </>
  )
};

export default App;
```



✔ **App에서 category 상태를 useState로 관리**

```react
// App.js

import { useState, useCallback } from "react";
import NewsList from "./components/NewsList";
import Categories from "./components/Categories";

const App = () => {
  const [category, setCategory] = useState('all');
  const onSelect = useCallback(category => setCategory(category), []);
  
  return (
    <>
      <Categories category={category} onSelect={onSelect}/>
      <NewsList category={category}/>
    </>
  )
};

export default App;
```

> onSelect : category 값을 업데이트하는 함수



```react
// components/Categories.js

import styled, {css} from "styled-components";

const categories = [
    {
        name: 'all',
        text: '전체보기'
    },
    {
        name: 'business',
        text: '비즈니스'
    },
    {
        name: 'science',
        text: '과학'
    },
    {
        name: 'entertainment',
        text: '엔터테인먼트'
    },
    {
        name: 'sports',
        text: '스포츠'
    },
    {
        name: 'health',
        text: '건강'
    },
    {
        name: 'technology',
        text: '기술'
    }
];

const CategoriesBlock = styled.div`
    display: flex;
    padding: 1rem;
    width: 768px;
    margin: 0 auto;
    @media screen and (max-width: 768px){
        width: 100%;
        overflow-x: auto;
    }
`;

const Category = styled.div`
    font-size: 1.125rem;
    cursor: pointer;
    white-space: pre;
    text-decoration: none;
    color: inherit;
    padding-bottom: 0.25rem;

    &:hover { 
        color: #495057;
    }

    ${props => props.active && css`
        font-weight: 600;
        border-bottom: 2px solid #22b8cf;
        color: #22b8cf;
        &:hover {
            color: #3bc9db;
        }
    `}

    & + & {
        margin-left: 1rem;
    }
`;


const Categories = ({ onSelect, category }) => {
    return (
        <CategoriesBlock>
            {categories.map(c => (
                <Category 
                    key={c.name}
                    active={category===c.name}
                    onClick={()=>onSelect(c.name)}
                >{c.text}</Category>
            ))}
        </CategoriesBlock>
    )
}

export default Categories;
```



### 06-2. API를 호출할 때 카테고리 지정하기

- NewsList 컴포넌트에서 현재 props로 받아 온 category에 따라 카테고리를 지정하여 API를 요청하도록 구현

```react
// components/NewsList.js

import { useState, useEffect } from "react";
import styled from "styled-components";
import NewsItem from "./NewsItem";
import axios from 'axios';

const NewsListBlock = styled.div`
    box-sizing: border-box;
    padding-bottom: 3rem;
    width: 768px;
    margin: 0 auto;
    margin-top: 2rem;
    @media screen and (max-width: 768px) {
        width: 100%;
        padding-left: 1rem;
        padding-right: 1rem;
    }
`;


const NewsList = ({ category }) => {
    const [articles, setArticles] = useState(null);
    const [loading, setLoading] = useState(false);

    useEffect(() => {
        // async를 사용하는 함수 따로 선언
        const fetchData = async() => {
            setLoading(true);
            try {
                const query = category === 'all' ? '' : `&category=${category}`;
                const response = await axios.get(
                    `https://newsapi.org/v2/top-headlines?country=kr${query}&apiKey=f83a3c447cca45f194986b5586bc6e94`,
                );
                setArticles(response.data.articles);
            } catch(e) {
                console.log(e);
            }
            setLoading(false);
        };
        fetchData();
    }, [category]);

    // 대기 중일 때
    if(loading) {
        return <NewsListBlock>대기중 ...</NewsListBlock>;
    }

    // 아직 articles 값이 설정되지 않았을 때 (필수)
    if (!articles) {
        return null;
    }

    // articles 값이 유효할  때
    return (
        <NewsListBlock>
            {articles.map(article => (
                <NewsItem key={article.url} article={article} />
            ))}
        </NewsListBlock>
    );
};

export default NewsList;
```

> category 값이 무엇인지에 따라 요청할 주소가 동적으로 바뀜.
>
> category 값이 바뀔 때마다 뉴스를 새로 불러와야 하기 때문에 useEffect의 의존 배열(두번째 파라미터)에 category를 넣어줘야 함



---



## 07. 리액트 라우터 적용하기



### 07-1. 리액트 라우터의 설치 및 적용

```bash
$ yarn add react-router-dom
```

```react
// index.js

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



### 07-2. NewsPage 생성

```react
// pages/NewsPage.js

import { useParams } from "react-router-dom";
import Categories from "../components/Categories";
import NewsList from "../components/NewsList";

const NewsPage = () => {
    const params = useParams();
    // 카테고리가 선택되지 않았으면 기본값 all 로 사용
    const category = params.category || 'all';

    return (
        <>
            <Categories />
            <NewsList category={category} />
        </>
    );
};

export default NewsPage;
```

> 현재 선택된 category 값을 URL 파라미터를 통해 사용할 것이므로 Categories 컴포넌트에서 현재 선택된 카테고리 값을 알려줄 필요도 없고, onSelect 함수를 따로 전달해 줄 필요도 없음

```react
// App.js

import { Route, Routes } from "react-router-dom";
import NewsPage from "./pages/NewsPage";

const App = () => {
  return (
    <Routes>
      <Route path="/" element={<NewsPage/>} />
      <Route path="/:category" element={<NewsPage/>} />
    </Routes>
  );
};

export default App;
```

> URL 파라미터가 있어도 없어도 NewsPage 컴포넌트를 보여줘야 하기 때문에 Route 컴포넌트를 두 번 사용



### 07-3. Categories에서 NavLink 사용하기

```react
// components/Categories.js

import styled from "styled-components";
import { NavLink } from "react-router-dom";

const categories = [
    {
        name: 'all',
        text: '전체보기'
    },
    {
        name: 'business',
        text: '비즈니스'
    },
    {
        name: 'science',
        text: '과학'
    },
    {
        name: 'entertainment',
        text: '엔터테인먼트'
    },
    {
        name: 'sports',
        text: '스포츠'
    },
    {
        name: 'health',
        text: '건강'
    },
    {
        name: 'technology',
        text: '기술'
    }
];

const CategoriesBlock = styled.div`
    display: flex;
    padding: 1rem;
    width: 768px;
    margin: 0 auto;
    @media screen and (max-width: 768px){
        width: 100%;
        overflow-x: auto;
    }
`;

const Category = styled(NavLink)`
    font-size: 1.125rem;
    cursor: pointer;
    white-space: pre;
    text-decoration: none;
    color: inherit;
    padding-bottom: 0.25rem;

    &:hover { 
        color: #495057;
    }

    &.active {
        font-weight: 600;
        border-bottom: 2px solid #22b8cf;
        color: #22b8cf;
        &:hover {
            color: #3bc9db;
        }   
    }
    

    & + & {
        margin-left: 1rem;
    }
`;


const Categories = ({ onSelect, category }) => {
    return (
        <CategoriesBlock>
            {categories.map(c => (
                <Category 
                    key={c.name}
                    className={({ isActive }) => (isActive ? 'active' : undefined)}
                    to={c.name === 'all' ? '/' : `/${c.name}`}
                >{c.text}
                </Category>
            ))}
        </CategoriesBlock>
    )
}

export default Categories;
```



## 08. usePromise 커스텀 Hook 만들기

- API 호출처럼 Promise를 사용해야 하는 경우

```react
// src/lib/usePromise.js

import { useState, useEffect } from "react";

export default function usePromise(promiseCreator, deps) {
    // 대기 중/완료/실패에 대한 상태 관리
    const [loading, setLoading] = useState(false);
    const [resolved, setResolved] = useState(null);
    const [error, setError] = useState(null);

    useEffect(() => {
        const process = async () => {
            setLoading(true);
            try {
                const resolved = await promiseCreator();
                setResolved(resolved);
            } catch (e) {
                setError(e);
            }
            setLoading(false);
        };
        process();
    }, deps);
    return [loading, resolved, error];
}
```

> usePromise : Promise의 대기중, 완료결과, 실패결과에 대한 상태 관리
>
> - usePromise의 의존 배열 deps를 파라미터로 받아옴

```react
// components/NewsList.js

import usePromise from "../lib/usePromise";
import styled from "styled-components";
import NewsItem from "./NewsItem";
import axios from 'axios';

const NewsListBlock = styled.div`
    box-sizing: border-box;
    padding-bottom: 3rem;
    width: 768px;
    margin: 0 auto;
    margin-top: 2rem;
    @media screen and (max-width: 768px) {
        width: 100%;
        padding-left: 1rem;
        padding-right: 1rem;
    }
`;


const NewsList = ({ category }) => {
    const [loading, response, error] = usePromise(() => {
        const query = category === 'all' ? '' : `&category=${category}`;
        return axios.get(
            `https://newsapi.org/v2/top-headlines?country=kr${query}&apiKey=f83a3c447cca45f194986b5586bc6e94`,
        );
    }, [category]);


    // 대기 중일 때
    if(loading) {
        return <NewsListBlock>대기중 ...</NewsListBlock>;
    }

    // 아직 articles 값이 설정되지 않았을 때 (필수)
    if (!response) {
        return null;
    }

    // 에러 발생 시
    if (error) {
        return <NewsListBlock>에러 발생 !</NewsListBlock>
    }
    
    // response 값이 유효할  때
    const { articles } = response.data;
    return (
        <NewsListBlock>
            {articles.map(article => (
                <NewsItem key={article.url} article={article} />
            ))}
        </NewsListBlock>
    );
};

export default NewsList;
```

