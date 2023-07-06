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
	display: flex; 
	background: #495057;
	input {
		// 기본 스타일 초기화
		background: none;
		outline: none;
		border: none;
		padding: 0.5rem;
		font-size: 1.125rem;
		line-height: 1.5;
		color: white;
		&::placeholder {
			color: #dee2ef;
		}
		// 버튼을 제외한 영역을 모두 차지하기
		flex: 1;
	}
	button {
		// 기본 스타일 초기화
		background: none;
		outline: none;
		border: none;
		background: #868e96;
		color: white;
		padding-left: 1rem;
		padding-right: 1rem;
		font-size: 1.5rem;
		display: flex;
		align-items: center;
		cursor: pointer;
		transition: 0.1s background ease-in;
		&:hover {
			background: #adb5bd;
		}
	}
}
```

### TodoListItem과 TodoList 만들기 

- components 디렉토리에 TodoListItem.js와 TodoListItem.scss 생성

#### TodoListItem.js

```javascript 
import {
	MdCheckBoxOutlineBlank,
	MdCheckBox,
	MdRemoveCircleOutline
} from 'react-icon/md';
import './TodoListItem.scss';

const TodoListItem = () => {
	return (
		<div className="TodoListItem">
			<div className="checkbox">
				<MdCheckBoxOutlineBlank />
				<div className="text">할 일</div>
			</div>
			<div className="remove">
				<MdRemoveCircleOutline />
			</div>
		</div>
	);
};

export default TodoListItem;
```

- components 디렉토리에 TodoList.js와 TodoList.scss 파일 생성 

#### TodoList.js

```javascript
import TodoListItem from './TodoListItem';
import './TodoList.scss';

const TodoList = () => {
	return (
		<div className="TodoList">
			<TodoListItem />
			<TodoListItem />
			<TodoListItem />
		</div>
	);
};

export default TodoList;
```

#### App.js

```javascript
import TodoTemplate from './components/TodoTemplate';
import TodoInsert from './components/TodoInsert';
import TodoList from './components/TodoList';

const App = () => {
	return (
		<TodoTemplate>
			<TodoInsert />
			<TodoList />
		</TodoTemplate>
	);
};

export default App;
```

#### TodoList.scss 

```scss 
.TodoList {
	min-height: 320px;
	max-height: 513px;
	overflow-y: auto;
}
```

#### TodoListItem.scss

```scss
.TodoListItem {
	padding: 1rem;
	display: flex;
	align-items: center;
	&:nth-child(even) {
		background: #f8f9fa;
	}
	.checkbox {
		cursor: pointer;
		flex: 1; // 차지할 수 있는 영역 모두 차지
		display: flex;
		align-items: center;
		svg {
			// 아이콘 
			font-size: 1.5rem;
		}
		.text {
			margin-left: 0.5rem;
			flex: 1; // 차지할 수 있는 영역 모두 차지 
		}
		&.checked {
			svg {
				color: #22b8cf;
			}
			.text {
				color: #adb5bd;
				text-decoration: line-through;
			}
		}
	}
	.remove {
		display: flex;
		align-items: center;
		font-size: 1.5rem;
		color: #ff6b6b;
		&:hover {
			color: #ff8787;
		}
	}
	// 엘리먼트 사이사이에 테두리를 넣어 줌
	&+& {
		border-top: 1px solid #dee2e6;
	}
}
```

## 기능 구현하기

### App에서 todos 상태 사용하기

#### App.js

```javascript
import { useState } from 'react';
import TodoTemplate from './components/TodoTemplate';
import TodoInsert from './components/TodoInsert';
import TodoList from './components/TodoList';

const App = () => {
	const [todos, setTodos] = useState([
		{
			id: 1,
			text: '리액트의 기초 알아보기',
			checked: true,
		},
		{
			id: 2,
			text: '컴포넌트 스타일링해 보기',
			checked: true,
		},
		{
			id: 3,
			text: '일정 관리 앱 만들어 보기',
			checked: false,
		}
	]);

	return (
		<TodoTemplate>
			<TodoInsert />
			<TodoList todos={todos} />
		</TodoTemplate>
	);
};

export default App;
```

- todos 배열 안에 들어 있는 객체에는 각 항목의 고유 id, 내용, 완료 여부를 알여주는 값이 포함되어 있습니다.
- 이 배열은 TodoList에 props로 전달되고, TodoList에서 이 값을 받아 온 후 TodoItem으로 변환하여 설정합니다.

#### TodoList.js

```javascript
import {
	MdCheckBoxOutlineBlank,
	MdCheckBox,
	MdRemoveCircleOutline
} from 'react-icons/md';
import cn from 'classnames';
import './TodoListItem.scss';

const TodoListItem = ({ todo }) => {
	const { text, checked } = todo;

	return (
		<div className="TodoListItem">
			<div className={cn('checkbox', { checked })}>
				{checked ? <MdCheckBox /> : <MdCheckBoxOutlineBlank />}
				<div className="text">{text}</div>
			</div>
			<div className="remove">
				<MdRemoveCircleOutline />
			</div>
		</div>
	);
};

export default TodoListItem;
```

### 항목 추가 기능 구현하기

#### TodoInsert value 상태 관리하기

- 인풋에 입력하는 값을 관리할 수 있도록 useState를 사용하여 value라는 상태를 정의
- 추가로 인풋에 넣어줄 onChange 함수도 작성, 이 과정에서 컴포넌트가 리렌더링될 때마다 함수를 새로 만드는 것이 아니라, 한 번 함수를 만들고 재사용할 수 있도록 useCallback Hook을 사용

#### TodoInsert.js

```javascript
import { useState, useCallback } from 'react';
import { MdAdd } from 'react-icons/md';
import './TodoInsert.scss';

const TodoInsert = () => {
	const [value, setValue] = useState('');

	const onChange = useCallback(e => {
		setValue(e.target.value);
	}, []);

	return (
		<form className="TodoInsert">
			<input 
				placeholder="할 일을 입력하세요."
				value={value}
				onChange={onChange}
			/>
			<button type="submit">
				<MdAdd />
			</button>
		</form>
	);
};

export default TodoInsert;
```

#### todo 배열에 새 객체 추가하기


- todos 배열에 새 객체를 추가하는 onInsert 함수를 만듭니다. 
- 이 함수에서는 새로운 객체를 만들 때마다 id 값에 1씩 더해 주어야 하는데, id값은 useRef를 사용하여 관리합니다. 
- 여기서 useState가 아닌 useRef를 사용하여 컴포넌트에서 사용할 변수를 만드는 이유는 id 값은 렌더링되는 정보가 아니기 때문입니다. 이 값은 화면에 보이지도 않고, 이 값이 바뀐다고 해서 컴포넌트가 리렌더링될 필요도 없습니다. 
- 단순히 새로운 항목을 만들 때 참조되는 값입니다.
- onInsert 함수는 컴포넌트의 성능을 아낄 수 있도록 useCallback으로 감싸 줍니다.
- onInsert 함수를 만든 뒤에는 해당 함수를 TodoInsert 컴포넌트의 props로 설정

#### App.js 

```javascript
import { useState, useRef, useCallback } from 'react';
import TodoTemplate from './components/TodoTemplate';
import TodoInsert from './components/TodoInsert';
import TodoList from './components/TodoList';

const App = () => {
	const [todos, setTodos] = useState([
		{
			id: 1,
			text: '리액트의 기초 알아보기',
			checked: true,
		},
		{
			id: 2,
			text: '컴포넌트 스타일링해 보기',
			checked: true,
		},
		{
			id: 3,
			text: '일정 관리 앱 만들어 보기',
			checked: false,
		}
	]);

	// 고유값으로 사용될 id
	// ref를 사용하여 변수 담기
	const nextId = useRef(4);

	const onInsert = useCallback(
		text => {
			const todo = {
				id: nextId.current,
				text,
				checked: false,
			};
			setTodos(todos.concat(todo));
			nextId.current += 1; // nextId 1씩 더하기
		},
		[todos],
	);

	return (
		<TodoTemplate>
			<TodoInsert onInsert={onInsert} />
			<TodoList todos={todos} />
		</TodoTemplate>
	);
};

export default App;
```

#### TodoInsert에서 onSubmit 이벤트 설정하기

#### TodoInsert.js

```javascript
import { useState, useCallback } from 'react';
import { MdAdd } from 'react-icons/md';
import './TodoInsert.scss';

const TodoInsert = ({ onInsert }) => {
	const [value, setValue] = useState('');

	const onChange = useCallback(e => {
		setValue(e.target.value);
	}, []);

	const onSubmit = useCallback(
		e => {
			onInsert(value);
			setValue(''); 

			// submit 이벤트는 브라우저에서 새로고침을 발생시킵니다.
			// 이를 방지하기 위해 이 함수를 호출합니다.
			e.preventDefault();
		},
		[onInsert, value],
	);

	return (
		<form className="TodoInsert" onSubmit={onSubmit}>
			<input 
				placeholder="할 일을 입력하세요."
				value={value}
				onChange={onChange}
			/>
			<button type="submit">
				<MdAdd />
			</button>
		</form>
	);
};

export default TodoInsert;
```

### 지우기 기능 구현하기

- 리액트 컴포넌트에서 배열의 불변성을 지키면서 배열 원소를 제거해야 할 경우, 배열 내장 함수인 filter를 사용하면 매우 간편합니다.

#### 배열의 내장 함수 filter

- filter 함수는 기존 배열을 그대로 둔 상태에서 특정 조건을 만족하는 원소들만 따로 추출하여 새로운 배열을 만들어 줍니다.

```javascript
const array = [1,2,3,4,5,6,7,8,9,10];
const biggerThanFive = array.filter(number => number > 5);
```

- filter 함수에는 조건을 확인해 주는 함수를 파라미터로 넣어 주어야 합니다. 파라미터로 넣는 함수는 true 혹은 false 값을 반환해야 하며, 기서 true를 반환하는 경우만 새로운 배열에 포함됩니다.


#### todos 배열에서 id로 항목 지우기

- filter 함수를 사용하여 onRemove 함수를 작성합니다.

#### App.js 

```javascript
import { useState, useRef, useCallback } from 'react';
import TodoTemplate from './components/TodoTemplate';
import TodoInsert from './components/TodoInsert';
import TodoList from './components/TodoList';

const App = () => {
	...

	const onRemove = useCallback(
		id => {
			setTodos(todos.filter(todo => todo.id !== id));
		},
		[todos],
	);

	return (
		<TodoTemplate>
			<TodoInsert onInsert={onInsert} />
			<TodoList todos={todos} onRemove={onRemove} />
		</TodoTemplate>
	);
};

export default App;
```

#### TodoListItem에서 삭제 함수 호출하기

- TodoListItem에서 onRemove 함수를 사용하려면 TodoList 컴포넌트를 거쳐야 합니다. 
- props로 받아 온 onRemove 함수를 TodoListItem에 그대로 전달합니다.

#### TodoList.js

```javascript 
import TodoListItem from './TodoListItem';
import './TodoList.scss';

const TodoList = ({ todos, onRemove }) => {
	return (
		<div className="TodoList">
			{todos.map(todo => (
				<TodoListItem todo={todo} key={todo.id} onRemove={onRemove} />
			))}
		</div>
	);
};

export default TodoList;
```

#### TodoListItem.js

```javascript
import {
	MdCheckBoxOutlineBlank,
	MdCheckBox,
	MdRemoveCircleOutline,
} from 'react-icons/md';
import cn from 'classnames';
import './TodoListItem.scss';

const TodoListItem = ({ todo, onRemove }) => {
	const { id, text, checked } = todo;

	return (
		<div className="TodoListItem">
			<div className={cn('checkbox', { checked })}>
				{checked ? <MdCheckBox /> : <MdCheckBoxOutlineBlank /> }
				<div className="text">{text}</div>
			</div>
			<div className="remove" onClick={() => onRemove(id)}>
				<MdRemoveCircleOutline />
			</div>
		</div>
	);
};

export default TodoListItem;
```

### 수정 기능

- onToggle이라는 함수를 App에 만들고, 해당 함수를 TodoList 컴포넌트에게 props로 넣어 줍니다. 
- 그 다음에는 TodoList를 통해 TodoListItem까지 전달해 주면 됩니다.

#### onToggle 구현하기

#### App.js

```javascript
import { useState, useRef, useCallback } from 'react';
import TodoTemplate from './components/TodoTemplate';
import TodoInsert from './components/TodoInsert';
import TodoList from './components/TodoList';

const App = () => {
	...

	const onToggle = useCallback(
		id => {
			setTodos(
				todos.map(todo => todo.id === id ? { ...todo, checked: !todo.checked} : todo)
			);
		},
		[todos],
	);

	return (
		<TodoTemplate>
			<TodoInsert onInsert={onInsert} />
			<TodoList todos={todos} onRemove={onRemove} onToggle={onToggle} />
		</TodoTemplate>
	);
};

export default App;
```

- 위 코드에서는 배열 내장 함수 map을 사용하여 특정 id를 가지고 있는 객체의 checked 값을 반전 시켜 주었습니다. 불변성을 유지하면서 특정 배열 원소를 업데이트해야 할 때 map을 사용하면 짧은 코드로 쉽게 작성할 수 있습니다.
- onToggle 함수를 보면 todo.id === id ? ... :  ... 이라는 삼항 연산자가 사용되었습니다. 
- todo.id와 현재 파라미터로 사용된 id 값이 같을 때는 정해 준 규칙대로 새로운 객체를 생성하지만, id 값이 다를 때는 변화를 주지 않고 처음 받아 왔던 상태 그대로 반환합니다. 그렇기 때문에 map을 사용하여 만든 배열에서 변화가 필요한 원소만 업데이트되고 나머지는 그래로 남아 있게 됩니다.

#### TodoListItem에서 토글 함수 호출하기

#### TodoList.js

```javascript
import TodoListItem from './TodoListItem';
import './TodoList.scss';

const TodoList = ({ todos, onRemove, onToggle }) => {
	return (
		<div className="TodoList">
			{todos.map(todo => (
				<TodoListItem 
					todo={todo}
					key={todo.id}
					onRemove={onRemove}
					onToggle={onToggle}
				/>
			))}
		</div>
	);
};

export default TodoList;
```

#### TodoListItem.js 

```javascript
import {
	MdCheckBoxOutlineBlank,
	MdCheckBox,
	MdRemoveCircleOutline,
} from 'react-icons/md';

import cn from 'classnames';
import './TodoListItem.scss';

const TodoListItem = ({ todo, onRemove, onToggle }) => {
	const { id, text, checked } = todo;

	return (
		<div className="TodoListItem">
			<div className={cn('checkbox', { checked })} onClick={() => onToggle(id)}>
				{checked ? <MdCheckBox /> : <MdCheckBoxOutlineBlank />}
				<div className="text">{text}
			</div>
			<div className="remove" onClick={() => onRemove(id)}>
				<MdRemoveCircleOutline />
			</div>
		</div>
	);
};

export default TodoListItem;
```