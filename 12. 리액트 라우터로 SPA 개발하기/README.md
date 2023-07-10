# 리액트 라우터로 SPA 개발하기

## 라우팅이란?
- 사용자가 요청한 URL에 따라 알맞는 페이지로 보여주는 것을 의미
- 여러 페이지로 구성된 웹 애플리케이션을 만들 때 페이지별로 컴포넌트들을 분리해가면서 프로젝트를 관리하기 위해 필요한 것이 라우팅 시스템
- 리액트에서 라우트 시스템을 구축하기 위해 사용할 수 있는 방법
	- **리액트 라우터(React Router)** 
		- 리액트 라우팅 관련 라이브러리들 중에서 가장 오래되었고, 가장 많이 사용됩니다. 
		- 컴포넌트 기반으로 라우팅 시스템을 설정할 수 있습니다.
	- **Next.js** 
		- 리액트 프로젝트의 프레임워크 입니다. 
		- Create React App 처럼 리액트 프로젝트 설정을 하는 기능, 라우팅 시스템, 최적화, 다국어 시스템 지원, 서버 사이드 렌더링 등 다양한 기능들을 제공
		- 이 프레임워크의 라우팅 시스템은 파일 경로 기반으로 작동합니다. 
		- 리액트 라우터의 대안으로 많이 사용되고 있습니다.
		
- 리액트 라우터를 사용하면 쉽게 리액트 라우터로 싱글 페이지 애플리케이션 (Single Page Application)을 만들 수 있습니다.

## SPA란?
- SPA(Single Page Application) : 싱글페이지 애플리케이션
- 한 개의 페이지로 이루어진 애플리케이션
- 참고) 멀티페이지 애플리케이션
	- 다른 페이지로 이동할 때마다 새로운 html을 받아오고, 페이지를 로딩할 떄마다 서버에서 CSS, JS, 이미지 파일 등의 리소스를 전달받아 브라우저 화면에 보여 줍니다.
	- 각 페이지마다 다른 html 파일을 만들어서 제공하거나 데이터에 따라 유동적인 html을 생성해 주는 템플릿 엔진을 사용하기도 합니다.
	- 사용자 인터렉션이 별로 없는 정적인 페이지들은 기존의 방식이 적합하지만, 사용자 인터렉션이 많이 다양한 정보를 제공하는 모던 웹 애플리케이션은 이 방식이 적합하지 않습니다. 
	- 새로운 페이지를 보여주어야 할 떄마다 서버 측에서 모든 준비를 한다면 그만큼 서버의 자원을 사용하는 것이고, 트래픽도 더 많이 나올 수 있습니다.
- 리액트 같은 라이브러리를 사용해서 뷰 렌더링을 사용자의 브라우저가 담당하도록 하고, 우선 웹 애플리케이션을 브라우저에 불러와서 실행시킨 후에 사용자와 인터렉션이 발생하면 필요한 부분만 자바스크립트를 사용하여 업데이트하는 방식을 사용하게 됩니다. 
- 새로운 데이터가 필요하다면 서버 API를 호출하여 필요한 데이터만 새로 불러와 애플리케이션에서 사용할 수 있게 됩니다.
- 한번만 html을 받아와서 웹 애플리케이션을 실행시킨 후에 필요한 데이터만 받아와 화면에 업데이트 하는 것이 싱글페이지 애플리케이션 입니다.
- 싱글 페이지 애플리케이션은 기술적으로는 한 페이지만 존재하는  것이지만, 사용자가 경험하기에는 여러 페이지가 존재하는 것 처럼 느낄 수 있습니다. 
- 리액트 라우터와 같은 라우팅 시스템은 사용자의 브라우저 주소창의 경로에 따라 알맞는 페이지를 보여주는데, 이후 링크를 눌러 다른 페이지로 이동하게 될 때 서버에 다른 페이지 html을 새로 요청하는 것이 아니라, 브라우저의 History API를 사용하여 브라우저의 주소창의 값만 변경하고 기존 페이지에 띄웠던 웹 애플리케이션을 그대로 유지하면서 라우팅 설정에 따라 다른 페이지를 보여줍니다.

## 리액트 라우터 적용 및 기본 사용법

### 프로젝트 생성 및 라이브러리 설치

```
$ yarn create react-app router-tutorial
$ cd router-tutorial
$ yarn add react-router-dom
```

### 프로젝트에 라우터 적용
- src/index.js 파일에서 react-router-dom에 내장되어 있는 BrowserRouter라는 컴포넌트를 사용하여 감싸면 됩니다.
- 이 컴포넌트는 웹 애플리케이션에 HTML5의 History API를 사용하여 페이지를 새로 불러오지 않고도 주소를 변경하고 현재 주소의 경로에 관련된 정보를 리액트 컴포넌트에서 사용할 수 있도록 해줍니다.

#### src/index.js 

```javascript
import React from 'react';
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

### 페이지 컴포넌트 만들기

#### src/pages/Home.js

```javascript 
const Home = () => {
	return (
		<div>
			<h1>홈</h1>
			<p>가장 먼저 보여지는 페이지입니다.</p>
		</div>
	);
};

export default Home;
```

#### src/pages/About.js

```javascript
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

### Route 컴포넌트로 특정 경로에 원하는 컴포넌트 보여주기

- Route 컴포넌트는 다음과 같이 사용합니다.

```
<Route path="주소규칙" element={보여 줄 컴포넌트 JSX} />
```

- Route 컴포넌트는 Routes 컴포넌트 내부에서 사용되어야 합니다.

#### src/App.js 

```javascript
import { Route, Routes } from 'react-router-dom';
import About from './pages/About';
import Home from './pages/Home';

const App = () => {
	return (
		<Routes>
			<Route path="/" element={<Home />} />
			<Route path="/about" element={<About />} />
		</Routes>
	);
};

export default App;
```

### Link 컴포넌트를 사용하여 다른 페이지로 이동하는 링크 보여주기

> 웹 페이지에서는 원래 링크를 보여줄 때 <code>a</code> 태그를 사용하는데, 리액트 라우터를 사용하는 프로젝트에서 <code>a</code>태그를 바로 사용하면 안됩니다. <code>a</code> 태그를 클릭하여 페이지를 이동할 때 브라우저에서는 페이지를 새로 불러오게 되기 때문입니다.<br><code>Link</code> 컴포넌트 역시 <code>a</code> 태그를 사용하긴 하지만, 페이지를 새로 불러오는 것을 막고 History API를 통해 브라우저 주소의 경로만 바꾸는 기능이 내장되어 있습니다.

#### 사용방법

```
<Link to="경로">링크 이름</Link>
```

#### src/pages/Home.js

```javascript
import { Link } from 'react-router-dom';

const Home = () => {
	return (
		<div>
			<h1>홈</h1>
			<p>가장 먼저 보여지는 페이지 입니다.</p>
			<Link to="/about">소개</Link>
		</div>
	);
};

export default Home;
```


## URL 파라미터와 쿼리스트링

- URL 파라미터 
	- 주소의 경로에 유동적인 값을 넣는 형태
	- 주로 ID또는 이름을 사용하여 특정 데이터를 조회할 때 사용
	- 예)  /profile/이름
	
- 쿼리스트링 
	- 주소의 뒷부분에 ? 문자열 이후 key=value로 값을 정의하며 &로 구분을 하는 형태
	- 키워드 검색, 페이지네이션, 정렬 방식 등 데이터 조회에 필요한 옵션을 전달할 때 사용
	- 예) /articles?page=1&keyword=react



### URL 파라미터

#### src/pages/Profile.js

```javascript
import { useParams } from 'react-router-dom';

const data = {
	lee: {
		name: '이이름',
		description: '개발자1',
	},
	kim: {
		name: '김이름',
		description: '개발자2',
	}
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
				<p>존재하지 않는 프로필입니다.</p>
			)}
		</div>
	);
};

export default Profile;
```

- URL 파라미터는 useParams 라는 Hook을 사용하여 객체 형태로 조회할 수 있습니다.
- URL 파라미터의 이름은 라우터 설정을 할 때 Route 컴포넌트의 path props를 통하여 설정합니다.

#### src/App.js 

```javascript
import { Route, Routes } from 'react-router-dom';
import About from './pages/About';
import Home from './pages/Home';
import Profile from './pages/Profile';

const App = () => {
	return (
		<Routes>
			<Route path="/" element={<Home />} />
			<Route path="/about" element={<About />} />
			<Route path="/profile/:username" element={<Profile />} />
		</Routes>
	);
};

export default App;
```

> URL 파라미터는 <code>/profiles/:username</code>과 같이 경로에 <code>:</code>를 사용하여 설정합니다. 만약 URL 파라미터가 여러개인 경우엔 <code>/profiles/:username/:field</code>와 같은 형태로 설정할 수 있습니다.

#### src/pages/Home.js

```javascript
import { Link } from 'react-router-dom';

const Home = () => {
	return (
		<div>
			<h1>홈</h1>
			<p>가장 먼저 보여지는 페이지입니다.</p>
			<ul>
				<li>
					<Link to="/about">소개</Link>
				</li>
				<li>
					<Link to="/profiles/lee">lee의 프로필</Link>
				</li>
				<li>
					<Link to="/profiles/kim">kim의 프로필</Link>
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

### 쿼리스트링

- 쿼리스트링을 사용할 때는 URL 파라미터와 달리 <code>Route</code> 컴포넌트를 사용할 때 별도로 설정해야 하는 것은 없습니다.

#### src/pages/About.js

```javascript
import { useLocation } from 'react-router-dom';

const About = () => {
	const location = useLocation();
	
	return (
		<div>
			<h1>소개</h1>
			<p>리액트 라우터를 사용해 보는 프로젝트입니다.</p>
			<p>쿼리스트링: {location.search}</p>
		</div>
	);
};

export default About;
```

- <code>useLocation</code> Hook은 <code>location</code> 객체를 반환하며 이 객체는 현재 사용자가 보고있는 페이지의 정보를 지니고 있습니다.
- 이 객체에는 다음과 같은 값들이 있습니다.
	- **pathname** : 현재 주소의 경로 (쿼리스트링 제외)
	- **search** : 맨 앞의 ? 문자 포함한 쿼리스트링 값
	- **hash** : 주소의 # 문자열 뒤의 값 (주로 History API가 지원되지 않는 구형 브라우저에서 클라이언트 라우팅을 사용할 때 쓰이는 해시 라우터에서 사용합니다.)
	- **state** : 페이지로 이동할 때 임의로 넣을 수 잇는 상태 값
	- **key** : <code>location</code> 객체의 고유 값, 초기에는 <code>default</code> 이며 페이지가 변경될때 마다 고유의 값이 생성됨
- 쿼리스트링은 <code>location.search</code> 값을 통해 조회를 할 수 있습니다.
- 주소창에 http://localhost:3000/about?detail=true&mode=1 라고 입력해 봅시다.

- 쿼리스트링 값이 <code>?detail=true&mode=1</code>으로 표시가 되고 있습니다. 이 문자열에서 앞에 있는 ?로 지우고 & 문자열로 분리한 뒤 key와 value 를 파싱하는 작업을 해야 하는데 보통 npm에서 qs 또는 querystring 패키지를 설치해서 처리할 수 있습니다. 

- 쿼리스트링을 따로 파싱까지 해야된다면 번거로울 수도 있는데 리액트 라우터에서는 useSearchParams라는 Hook을 통해서 쿼리스트링을 더욱 쉽게 다룰 수 있게 됩니다.

#### src/pages/About.js

```javascript
import { useSearchParams } from 'react-router-dom';

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
			<button onClick={onToggleDetail}>Toggle detail</button>
			<button onClick={onIncreaseMode}>mode + 1</button>
		</div>
	);
};

export default About;
```

- useSearchParams는 배열 타입의 값을 반환하며, 첫번째 원소는 쿼리파라미터를 조회하거나 수정하는 메서드들이 담긴 객체를 반환합니다.
- get 메서드를 통해 특정 쿼리파라미터를 조회할 수 있고, set 메서드를 통해 특정 쿼리파라미터를 업데이트 할 수 있습니다. 죄회시에 쿼리파라미터가 존재하지 않는다면 null로 조회됩니다. 
- 두번째 요소는 쿼리파라미터를 객체형태로 업데이트할 수 있는 함수를 반환합니다.

> 쿼리파라미터를 사용할 때 유의점은 쿼리파라미터를 조회할 때 값은 무조건 문자열 타입입라는 것. true 또는 false 값을 넣게 된다면 값을 비교할 때 꼭 'true'와 같이 따옴표로 감싸서 비교를 해야 하며, 숫자를 다룰때는 parseInt를 사용하여 숫자 타입으로 변환해야 한다.

## 중첩된 라우트

#### src/pages/Articles.js

```javascript
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
	);
};

export default Articles;
```

#### src/pages/Article.js

```javascript
import { useParams } from 'react-router-dom';

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

#### src/App.js

```javascript
import { Route, Routes } from 'react-router-dom';
import About from './pages/About';
import Article from './pages/Article';
import Articles from './pages/Articles';
import Home from './pages/Home';
import Profile from './pages/Profile';

const App = () => {
	return (
		<Routes>
			<Route Path="/" element={<Home />} />
			<Route path="/about" element={<About />}>
			<Route path="/profiles/:username" element={<Profile />} />
			<Route path="/articles" element={<Articles />} />
			<Route path="/articles/:id" element={<Article />} />
		</Routes>
	);
};

export default App;
```

#### src/pages/Home.js

```javascript
import { Link } from 'react-router-dom';

const Home = () => {
	return (
		<div>
			<h1>홈</h1>
			<p>가장 먼저 보여지는 페이지입니다.</p>
			<li>
				<Link to="/about">소개</Link>
			</li>
			<li>
				<Link to="/profiles/lee">개발자1의 프로필</Link>
			</li>
			<li>
				<Link to="/profiles/kim">개발자2의 프로필<Link>
			</li>
			<li>	
				<Link to="/profiles/void">존재하지 않는 프로필</Link>
			</li>
			<li>
				<Link to="/articles">게시글 목록</Link>
			</li>
		</div>
	);
};

export default Home;
```
#### src/App.js

- 중첩된 라우터 형태로 설정 

```javascript
import { Route, Routes } from 'react-router-dom';
import About from './pages/About';
import Article from './pages/Article';
import Articles from './pages/Articles';
import Home from './pages/Home';
import Profile from './pages/Profile';

const App = () => {
	return (
		<Routes>
			<Route path="/" element={<Home />} />
			<Route path="/about" element={<About />} />
			<Route path="/profiles/:username" element={<Profile />} />
			<Route path="/articles" element={<Articles />}>
				<Route path=":id" element={<Article />} />
			</Route>
		</Routes>
	);
};

export default App;
```

- <code>Articles</code> 컴포넌트에서 리액트 라우터에서 제공하는 <code>Outlet</code> 이라는 컴포넌트를 사용해주어야 합니다.
- 이 컴포넌트는 <code>Route</code>의 <code>children</code>으로 들어가는 JSX 요소를 보여주는 역할을 합니다.

#### src/pages/Articles.js 

```javascript
import { Link, Outlet } from 'react-router-dom';

const Articles = () => {
	return (
		<div>
			<Outlet />
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
	);
};

export default Articles;
```

> 위 코드에서 <code>Outlet</code> 컴포넌트가 사용된 자리에 중첩된 라우트가 보여지게 됩니다.

## 공통 레이아웃 컴포넌트 

> 중첩된 라우트와 <code>Outlet</code>은 페이지끼리 공통적으로 보여줘야 하는 레이아웃이 있을때도 사용할  수 있습니다.


#### src/Layout.js

```javascript
import { Outlet } from 'react-router-dom';

const Layout = () => {
	return (
		<div>
			<header style={{ background: 'lightgray', padding: 16, fontSize: 24 }}>
				Header
			</header>
			<main>
				<Outlet />
			</main>
		</div>
	);
};

export default Layout;
```

#### src/App.js

```javascript
import { Route, Routes } from 'react-router-dom';
import Layout from './Layout';
import About from './pages/About';
import Article from './pages/Article';
import Articles from './pages/Articles';
import Home from './pages/Home';
import Profile from './pages/Profile';

const App = () => {
	return (
		<Routes>
			<Route element={<Layout />}>
				<Route path="/" element={<Home />} />
				<Route path="/about" element={<About />} />
				<Route path="/profiles/:username" element={<Profile />} />
			</Route>
			<Route path="/articles" element={<Articles />}>
				<Route path=":id" element={<Article />} />
			</Route>
		</Routes>
	);
};

export default App;
```

### index props

- <code>Route</code> 컴포넌트에는 <code>index</code>라는 props가 있습니다. 이 props는 <code>path="/"</code>와 동일한 의미를 가집니다.
- <code>Home</code> 컴포넌트가 사용된 <code>Route</code> 컴포넌트를 다음과 같이 변경합니다.

#### src/App.js

```javascript
import { Route, Routes } from 'react-router-dom';
import Layout from './Layout';
import About from './pages/About';
import Article from './pages/Article';
import Home from './pages/Home';
import Profile from './pages/Profile';

const App = () => {
	return (
		<Routes>
			<Route path="/" element={<Layout />}>
				<Route index element={<Home />} />
				<Route path="/about" element={<About />} />
				<Route path="/profiles/:username" element={<Profile />} />
			</Route>
			<Route path="/articles" element={<Articles />}>
				<Route path=":id" element={<Article />} />
			</Route>
		</Routes>
	);
};

export default App;
```

> index props는 상위 라우트의 경로와 일치하지만, 그 이후에 경로가 주어지지 않았을 때 보여지는 라우트를 설정할때 사용합니다. <code>path="/"</code>와 동일한 역할을 하며 이를 좀 더 명시적으로 표현하는 방법입니다.

## 리액트 라우터 부가기능 

### useNavigate

- <code>useNavigate</code>는 <code>Link</code> 컴포넌트를 사용하지 않고 다른 페이지로 이동을 해야 하는 상황에 사용하는 Hook 입니다.

#### src/Layout.js

```javascript
import { Outlet, useNavigate } from 'react-router-dom';

const Layout = () => {
	const navigate = useNavigate();
	
	const goBack = () => {
		// 이전 페이지로 이동
		navigate(-1);
	};
	
	const goArticles = () => {
		// articles 경로로 이동
		navigate('/articles');
	};
	
	return (
		<div>
			<header style={{ background: 'lightgray', padding: 16, fontSize: 24 }}>
				<button onClick={goBack}>뒤로가기</button>
				<button onClick={goArticles}>게시글 목록</button>
			</header>
			<main>
				<Outlet />
			</main>
		</div>
	);
};

export default Layout;
```
  
- <code>navigate</code> 함수를 사용할 때 파라미터가 숫자 타입이라면 앞으로 또는 뒤로 이동합니다. 
	- <code>navigate(-1)</code> : 뒤로 한번 이동
	- <code>navigate(1)</code> : 앞으로 한번 이동(뒤로가 한번 한 후 동작)
- 다른 페이지로 이동을 할 때 <code>replace</code> 옵션을 사용하면 페이지 이동 기록이 남지 않습니다.

#### src/Layout.js - goArticles

```javascript
const goArticles = () => {
	navigate('/articles', { replace: true });
};
```

### NavLink

- <code>NavLink</code> 컴포넌트는 링크에서 사용하는 경로가 현재 라우트 경로와 일치하는 경우 특정 스타일 또는 CSS 클래스를 적용하는 컴포넌트입니다.
- 이 컴포넌트를 사용할 때 <code>style</code> 또는 <code>className</code>을 설정할 때 <code>{ isActive: boolean }</code>을 파라미터로 전달받는 함수 타입의 값을 전달합니다. 

```javascript
<NavLink 
	style={({isActive}) => isActive ? activeStyle : undefined }
/>
```

```javascript
<NavLink 
	className={({isActive}) => isActive ? 'active' : undefined }
/>
```

#### src/pages/Articles.js

```javascript
import { NavLink, Outlet } from 'react-router-dom';

const Articles = () => {
	const activeStyle = {
		color: 'green',
		fontSize: 21,
	};
	
	return (
		<div>
			<Outlet />
			<ul>
				<li>
					<NavLink 
						to="/articles/1"
						style={({ isActive }) => (isActive ? activeStyle  : undefined)}
					>
						게시글 1
					</NavLink>
				</li>
				<li>
					<NavLink 
						to="/articles/2"
						style={({ isActive }) => (isActive ? activeStyle : undefined)}
					>
						게시글 2
					</NavLink>
				</li>
				<li>
					<NavLink 
						to="/articles/3"
						style={({ isActive }) => (isActive ? activeStyle : undefined)}
					>
						게시글 3
					</NavLink>
				</li>
			</ul>
		</div>
	);
};

export default Articles;
```

- 게시글 링크가 현재 반복되고 있는데 이를 다음과 같이 리팩토링할 수 있습니다.

#### src/pages/Articles.js

```javascript
import { NavLink, Outlet } from 'react-router-dom';

const Articles = () => {
	return (
		<div>
			<Outlet />
			<ul>
				<ArticleItem id={1} />
				<ArticleItem id={2} />
				<ArticleItem id={3} />
			</ul>
		</div>
	);
};

const ArticleItem = ({ id }) => {
	const activeStyle = {
		color: 'green',
		fontSize: 21,
	};
	return (
		<li>
			<NavLink 
				to={`/articles/${id}`}
				style={({ isActive }) => (isActive ? activeStyle : undefined)}
			>
				게시글 {id}
			</NavLink>
		</li>
	);
};

export default Articles;
```

### NotFound 페이지 만들기

- 사전에 정의되지 않은 경로에 사용자가 진입했을 때 보여주는 페이지

#### src/pages/NotFound.js

```javascript
const NotFound = () => {
	return (
		<div
			style={{
				display: 'flex',
				alignItems: 'center',
				justifyContent: 'center',
				fontSize: 64,
				position: 'absolute',
				width: '100%',
				height: '100%'
			}}
		>
			404
		</div>
	);
};

export default NotFound;
```

#### src/App.js

```javascript
import { Route, Routes } from 'react-router-dom';
import Layout from './Layout';
import About from './pages/About';
import Article from './pages/Article';
import Articles from './pages/Articles';
import Home from './pages/Home';
import NotFound from './pages/NotFound';
import Profile from './pages/Profile';

const App = () => {
	return (
		<Routes>
			<Route path="/" element={<Layout />}>
				<Route index element={<Home />} />
				<Route path="/about" element={<About />} />
				<Route path="/profiles/:username" element={<Profile />} />
			</Route>
			<Route path="/articles" element={<Articles />}>
				<Route path=":id" element={<Article />} />
			</Route>
			<Route path="*" element={<NotFound />} />
		</Routes>
	);
};

export default App;
```

> <code>\*</code>는 와일드카드 문자, 아무 텍스트나 매칭한다는 의미<br>이 라우트 요소의 상단에 위치하는 라우트들의 규칙을 모두 확인하고, 일치하는 라우트가 없다면 이 라우트가 화면에 나타나게 됩니다.

### Navigate 컴포넌트

- <code>Navigate</code> 컴포넌트는 컴포넌트를 화면에 보여주는 순간 다른 페이지로 이동을 하고 싶을 때 사용하는 컴포넌트입니다. 즉, 페이지를 리다이렉트 하고 싶을 때 사용

#### src/pages/Login.js

```javascript
const Login = () => {
	return <div>로그인 페이지</div>;
};

export default Login;
```

#### src/pages/MyPage.js

```javascript
import { Navigate } from 'react-router-dom';

const MyPage = () => {
	const isLoggedIn = false;
	
	if (!isLoggedIn) {
		return <Navigate to="/login" replace={true} />;
	}
	
	return <div>마이페이지</div>;
};

export default MyPage;
```

#### src/App.js 

```javascript
import { Route, Routes } from 'react-router-dom';
import Layout from './layout';
import About from './pages/About';
import Article from './pages/Article';
import Articles from './pages/Articles';
import Home from './pages/Home';
import Login from './pages/Login';
import MyPage from './pages/MyPage';
import NotFound from './pages/NotFound';
import Profile from './pages/Profile';

const App = () => {
	return (
		<Routes>
			<Route path="/" element={<Layout />}>
				<Route index element={<Home />} />
				<Route path="/about" element={<About />} />
				<Route path="/profiles/:username" element={<Profile />} />
			</Route>
			<Route path="/articles" element={<Articles />}>
				<Route path=":id" element={<Article />} />
			</Route>
			<Route path="/login" element={<Login />} />
			<Route path="/mypage" element={<MyPage />} />
			<Route path="*" element={<NotFound />} />
		</Routes>
	);
};

export default App;
```

> 브라우저에서 <code>/mypage</code> 경로 이동시 바로 <code>/login</code> 페이지로 이동