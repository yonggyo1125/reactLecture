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