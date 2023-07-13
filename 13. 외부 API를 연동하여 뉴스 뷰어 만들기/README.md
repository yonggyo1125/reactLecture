# 외부 API를 연동하여 뉴스 뷰어 만들기

## axios로 API 호출해서 데이터 받아 오기

### axios

- 현재 가장 많이 사용되고 있는 자바스크립트 HTTP 클라이언트
- HTTP 요청을 Promise 기반으로 처리

#### 프로젝트 생성하기

```
$ yarn create react-app news-viewer
$ cd news-viewer
$ yarn add axios
```

#### .prettierrc

- 코드 스타일을 자동으로 정리하고 싶다면, 프로젝트 최상위 디렉토리에 .prettierrc 파일을 생성하여 다음 설정을 입력합니다.

```json
{
    "singleQuote": true,
    "semi": true,
    "useTabs": false,
    "tabWidth": 2,
    "trailingComma": "all",
    "printWidth": 80
}
```

#### jsconfig.json

- VS Code에서 파일 자동 불러오기 기능을 활용하고 싶다면 최상위 디렉토리에 jsconfig.json 파일을 다음과 같이 작성합니다.

```json
{
	"compilerOptions": {
		"target": "es6"
	}
}
```

#### App.js

```javascript
import { useState } from 'react';
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
			{data && <textarea rows={7} value={JSON.stringify(data, null, 2)} readOnly={true} />}
		</div>
	);
};

export default App;
```

- 위 코드에 async를 적용할 수도 있습니다.

#### App.js 

```javascript
import { useState } from 'react';
import axios from 'axios';

const App = () => {
	const [data, setData] = useState(null);
	const onClick = async () => {
		try {
			const response = await axios.get(
				'https://jsonplaceholder.typicode.com/todos/1',
			);
			setData(response.data);
		} catch (e) {
			console.log(e);
		}
	};
	return (
		<div>
			<div>
				<button onClick={onClick}>불러오기</button>
			</div>
			{data && <textarea rows={7} value={JSON.stringify(data, null, 2)} readOnly={true} />}
		</div>
	);
};

export default App;
```


## newsapi API 키 발급받기

- API 키는 https://newsapi.org/register 에 가입하면 발급받을 수 있습니다.

#### 전체 뉴스 불러오기

```
GET https://newsapi.org/v2/top-headlines?country=kr&apiKey=발급받은 API키
```

#### 특정 카테고리 뉴스 불러오기

```
GET https://newsapi.org/v2/top-headlines?country=kr&category=business&apiKey=발급받은 API키
```

#### App.js

```javascript
import { useState } from 'react';
import axios from 'axios';

const App = () => {
	const [data, setData] = useState(null);
	const onClick = async () => {
		try {
			const response = await axios.get(
				'https://newsapi.org/v2/top-headlines?country=kr&apiKey=발급받은 API키',
			);
			setData(response.data);
		} catch (e) {
			console.log(e);
		}
	};
	return (
		<div>
			<div>
				<button onClick={onClick}>불러오기</button>
			</div>
			{data && <textarea rows={7} value={JSON.stringify(data, null, 2)} />}
		</div>
	);
};

export default App;
```

## 뉴스 뷰어 UI 만들기

- styled-components를 사용하여 뉴스 정보를 보여 줄 컴포넌트를 만듭니다.
- styled-components를 설치합니다.

```
$ yarn add styled-components
```

#### components/NewsItem.js

```javascript  
import styled from 'styled-components';

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
const NewsItem = ({ article }) => {
	const { title, description, url, urlToImage } = article;
	return (
		<NewsItemBlock>
			{urlToImage && (
				<div className="thumbnail">
					<a href={url} target="_blank" rel="noopener noreferrer">
						<img src={urlToImage} alt="thumbnail" />
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

#### components/NewsItem.js

```javascript
import styled from 'styled-components';
import NewsItem from './NewsItem';

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
	urlToImage: 'https://via.placeholder.com/160',
};

const NewsList = () => {
	return (
		<NewsListBlock>
			<NewsItem article={sampleArticle} />
			<NewsItem article={sampleArticle} />
			<NewsItem article={sampleArticle} />
			<NewsItem article={sampleArticle} />
			<NewsItem article={sampleArticle} />
			<NewsItem article={sampleArticle} />
		</NewsListBlock>
	);
};
```

#### App.js 

```javascript
import NewsList from './components/NewsList';

const App = () => {
	return <NewsList />;
};

export default App;
```

## 데이터 연동하기

- 컴포넌트가 화면에 보이는 시점에 API를 요청
- useEffect를 사용하여 컴포넌트가 처음 렌더링되는 시점에 API를 요청하면 된다.
- 주의할 점: useEffect에 등록하는 함수에 async를 붙이면 안 되는데, useEffect에서 반환해야 하는 값은 뒷정리 함수이기 때문이다.
- useEffect 내부에 async/await를 사용하고 싶다면, 함수 내부에 async 키워드가 붙은 또다른 함수를 만들어서 사용해 주어야 한다.

#### components/NewsList.js 

```javascript
import { useState, useEffect } from 'react';
import styled from 'styled-components';
import NewsItem from './NewsItem';
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
	const [article, setArticles] = useState(null);
	const [loading, setLoading] = useStae(false);
	
	useEffect(() => {
		// async를 사용하는 함수 따로 선언
		const fetchData = async () => {
			setLoading(true);
			try {
				const response = await axios.get(
					'https://newsapi.org/v2/top-headlines?country=kr&apiKey=발급받은 api키'
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
	if (loading) {
		return <NewsListBlock>대기 중...</NewsListBlock>;
	}
	
	// 아직 articles 값이 설정되지 않았을 때
	if (!articles) {
		return null;
	}
	
	// articles 값이 유효할 때
	return (
		<NewsListBlock>
			{articles.map(article => (
				<NewsItem key={article.url} article={article} />
			))}
		</NewsListBlock>
	)
};

export default NewsList;
```

> map 함수를 사용하기 전에 꼭 !articles를 조회하여 해당 값이 현재 null이 아닌지 검사해야 합니다. 이 작업을 하지 않으면, 아직 데이터가 없을 때 null에는 map 함수가 없기 때문에 렌더링 과정에서 오류가 발생합니다.

## 카테고리 기능 구현하기

### 카테고리 선택 UI 만들기

#### components/Categories.js

```javascript
import styled from 'styled-components';

const categories = [
	{
		name: 'all',
		text: '전체보기',
	},
	{
		name: 'business',
		text: '비즈니스',
	},
	{
		name: 'entertainment',
		text: '엔터테인먼트',
	},
	{
		name: 'health',
		text: '건강',
	},
	{
		name: 'science',
		text: '과학',
	},
	{
		name: 'sports',
		text: '스포츠',
	},
	{
		name: 'technology',
		text: '기술',
	}
];

const CategoriesBlock = styled.div`
	display: flex;
	padding: 1rem;
	width: 768px;
	margin: 0 auto;
	@media screen and (max-width: 768px) {
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
	);
};

export default Categories;
```

#### App.js 

```javascript
import NewsList from './components/NewsList';
import Categories from './components/Categories';

const App = () => {
	return (
		<>
			<Categories />
			<NewsList />
		</>
	);	
};

export default App;
```

- App에서 category 상태를 useState로 관리하고, 추가로 category값을 업데이트 하는 onSelect라는 함수도 만들어 줍니다.
- category와 onSelect 함수를 Categories 컴포넌트에게 props로 전달해 줍니다. 또한, category 값을 NewsList 컴포넌트에게도 전달해 주어야 합니다.

#### App.js

```javascript
import { useState, useCallback } from 'react';
import NewsList from './components/NewsList';
import Categories from './components/Categories';

const App = () => {
	const [category, setCategory] = useState('all');
	const onSelect = useCallback(category => setCategory(category), []);
	
	return (
		<>
			<Categories category={category} onSelect={onSelect} />
			<NewsList category={category} />
		</>
	);
};

export default App;
```

- Categories에서는 props로 전달받은 onSelect를 각 Category 컴포넌트의 onClick으로 설정해 주고, 현재 선택된 카테고리의 값에 따라 다른 스타일을 적용합니다.

#### components/Categories.js

```javascript
import styled, { css } from 'styled-components';

const Categories = [
	...
];

const CategoriesBlock = styled.div`
	...
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

	${props => 
		props.active && css`
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
					active={category === c.name}
					onClick={() => onSelect(c.name)}
				>
					{c.text}
				</Category>
			))}
		</CategoriesBlock>
	);
};

export default Categories;
```

### API를 호출할 때 카테고리 지정하기

#### components/NewsList.js

```javascript
import { useState, useEffect } from 'react';
import styled from 'styled-components';
import NewsItem from './NewsItem';
import axios from 'axios';

const NewsListBlock = styled.div`
	...
`;

const NewsList = ({ category }) => {
	const [articles, setArticles] = useState(null);
	const [loading, setLoading] = useState(false);

	useEffect(() => {
		// async를 사용하는 함수 따로 선언
		const fetchData = async () => {
			setLoading(true);
			try {
				const query = category === 'all' ? '' : `&category=${category}`;
				const response = await axios.get(
					`https://newsapi.org/v2/top-headlines?country=kr${query}&apiKey=발급받은 API키`
				);
				setArticles(response.data.articles);
			} catch (e) {
				console.log(e);
			}
			setLoading(false);
		};
		fetchData();
	}, [category])

	...
};

export default NewsList;
```

## 리액트 라우터 적용하기 

### 리액트 라우터 설치 및 적용 

```
$ yarn add react-router-dom
```

#### index.js

```javascript 
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import { BrowserRouter } from 'react-router-dom';

ReactDOM.render(
	<BrowserRouter>
		<App />
	</BrowserRouter>,
	document.getElementById('root')
);
```

### NewsPage 생성 

#### pages/NewsPage.js

```javascript
import { useParams } from 'react-router-dom';
import Categories from '../components/Categories';
import NewsList from '../components/NewsList';

const NewsPage = () => {
	// 카테고리가 선택되지 않았으면 기본값 all로 사용
	const category = useParams().category || 'all';
	
	return (
		<>
			<Categories />
			<NewsList category={category} />
		</>
	);
};

export default NewsPage;
```

#### App.js 

```javascript
import { Routes, Route } from 'react-router-dom';
import NewsPage from './pages/NewsPage';

const App = () => {
	return (
		<Routes>
			<Route path="/:category?" element={<NewsPage />} />
		</Routes>
	);
};

export default App;
```

> 위 코드에서 사용된 path에 /:category? 와 같은 형태로 맨 뒤에 물음표 문자가 들어가 있는데, 이는 category 값이 선택적(optional)이라는 의미입니다. 즉 있을 수도 있고 없을 수도 있다는 뜻

### Categories에서 NavLink 사용하기

#### components/Categories.js

```javascript
import styled from 'styled-components';
import { NavLink } from 'react-router-dom';

const Categories = {
	...
};

const CategoriesBlock = styled.div`
	...
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

const Categories = () => {
	return (
		<CategoriesBlock>
			{categories.map(c => (
				<Category 
					key={c.name}
					className={({isActive}) => isActive ? 'active' : 'undefined'}
					to={c.name === 'all' ? '/' : `/${c.name}`}	
				>
				{c.text}
				</Category>
			))}
		</CategoriesBlock>
	);
};

export default Categories;
```

## usePromise 커스텀 Hook 만들기
> 컴포넌트에서 API 호출처럼 Promise를 사용해야 하는 경우 더욱 간결하게 코드를 작성할 수 있도록 해 주는 커스텀 Hook을 만들어서 적용해 봅니다.

#### lib/usePromise.js

```javascript
import { useState, useEffect } from 'react';

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

#### components/NewList.js

```javascript
import styled from 'styled-components';
import NewsItem from './NewsItem';
import axios from 'axios';
import usePromise from '../lib/usePromise';

const NewsListBlock = styled.div`
	...
`;

const NewsList = ({ category }) => {
	const [laoding, response, error] = usePromise(() => {
		const query = category === 'all' ? '' : `&category=${category}`;
		return axios.get(`https://newsapi.org/v2/top-headlines?country=kr${query}&apiKey=발급받은 API키`);
	}, [category]);

	// 대기 중일 때
	if (loading) {
		return <NewsListBlock>대기 중...</NewsListBlock>;
	}

	// 아직 response 값이 설정되지 않았을 때
	if (!response) {
		return null;
	}

	// 에러가 발생했을 때
	if (error) {
		return <NewsListBlock>에러 발생!</NewsListBlock>;
	}

	// response 값이 유효할 때
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