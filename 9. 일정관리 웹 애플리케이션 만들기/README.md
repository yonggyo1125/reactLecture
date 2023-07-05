  # 일정관리 웹 애플리케이션 만들기 

## 프로젝트 준비하기

### 프로젝트 생성 및 필요한 라이브러리 설치 

```
$ yarn create react-app todo-app
$ cd todo-app
$ yarn add sass classnames react-icons
```

> react-icons : 리액트에서 다양하고 예쁜 아이콘을 사용할 수 있는 라이브러리, SVG형태로 이루어진 아이콘을 리액트 컴포넌트처럼 쉽게 사용할 수 있습니다.

### Prettier 설정 

#### .prettierrc

```javascript
{
    "singleQuote": true,
    "semi": true,
    "useTabs": false,
    "trailingComma": "all",
    "printWidth": 80
}
```

#### index.css 수정

#### index.css 

```css
body {
    margin: 0;
    padding: 0;
    background: #e9ecef;
}
```

### App 컴포넌트 초기화

#### App.js

```javascript
const App = () => {
    return <div>Todo app을 만들자</div>;
};

export default App;
```

## UI 구성하기

- **TodoTemplate** : 화면을 가운데 정렬시켜 주며, 웹 타이틀(일정 관리)을 보여 줍니다. children으로 내부 JSX를 props로 받아 와사 렌더링해 줍니다.
- **TodoInsert** : 새로운 항목을 입력하고 추가할 수 있는 컴포넌트입니다. state를 통해 인풋의 상태를 관리합니다.
- **TodoListItem** : 각 할 일 항목에 대한 정보를 보여 주는 컴포넌트입니다. todo 객체를 props로 받아 와서 상태에 따라 다른 스타일의 UI를 보여 줍니다.
- **TodoList** : 배열을 props로 받아 온 후, 이를 배열 내장 함수 map을 사용해서 여러 개의 TodoListItem 컴포넌트를 변환하여 보여 줍니다.

### TodoTemplate 만들기 

- src 디렉토리에 components 디렉토리를 생성
- 그 안에 TodoTemplate.js와 TodoTemplate.scss 파일을 생성

#### TodoTemplate.js

```javascript
import './TodoTemplate.scss';

const TodoTemplate = ({ children }) => {
	return (
		<div className="TodoTemplate">
			<div className="app-title">일정 관리</div>
			<div className="content">{children}</div>
		</div>
	);
};

export default TodoTemplate;
```

#### App.js 

```javascript
import TodoTemplate from './components/TodoTemplate';

const App = () => {
	return <TodoTemplate>Todo App을 만들자</TodoTemplate>;
};

export default App;
```

#### jsconfig.json

- 닫혀 있는 파일에도 자동 완성 기능이 제대로 동작하려면 프로젝트 최상위 디렉토리에 jsconfig.json 파일을 만들어 주어야 합니다.

```javascript
{
	"compilerOptions": {
		"target": "es6"
	}
}
```
> jsconfig.json 파일을 저장하고 나면, 불러오려는 컴포넌트 파일이 열려 있지 않아도 자동 완성을 통해 컴포넌트를 불러와서 사용할 수 있습니다.

#### TodoTemplate.scss 

```scss 
.TodoTemplate {
	width: 512px;
	// width가 주어진 상황에서 좌우 중앙 정렬
	margin-left: auto;
	margin-right: auto;
	margin-top: 6rem;
	border-radius: 4px;
	overflow: hidden;
	
	.app-title {
		background: #22b8cf;
		color: white;
		height: 4rem;
		font-size: 1.5rem;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	
	.content {
		background: white;
	}
}
```

### TodoInsert 만들기 

- components 디렉토리에 TodoInsert.js 파일과 TodoInsert.scss 파일을 생성하세요.

#### TodoInsert.js

```javascript
import { MdAdd } from 'react-icons/md';
import './TodoInsert.scss'; 

const TodoInsert = () => {
	return (
		<form className="TodoInsert">
			<input placeholder="할 일을 입력하세요." />
			<button type="submit">
				<MdAdd />
			</button>
		</form>
	);
};

export default TodoInsert;
```

#### App.js

```javascript
import TodoTemplate from './components/TodoTemplate';
import TodoInsert from './components/TodoInsert';

const App = () => {
	return (
		<TodoTemplate>
			<TodoInsert />
		</TodoTemplate>
	);
};

export default App;
```

#### TodoInsert.scss 

```scss
.TodoInsert {
	
}
```