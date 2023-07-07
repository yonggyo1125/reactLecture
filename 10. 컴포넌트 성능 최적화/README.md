# 컴포넌트 성능 최적화

## 많은 데이터 렌더링하기

- 실제로 렉(lag)을 경험할 수 있도록 많은 데이터를 렌더링해 보겠습니다.

#### App.js 

```javascript
import { useState, useRef, useCallback } from 'react';
import TodoTemplate from './components/TodoTemplate';
import TodoInsert from './components/TodoInsert';
import TodoList from './components/TodoList';

function createBulkTodos() {
    const array = [];
    for (let i = 1; i <= 2500; i++) {
        array.push({
            id: i,
            text: `할 일 ${i}`,
            checked: false,
        });
    }
    return array;
}

const App = () => {
    const [todos, setTodos] = useState(createBulkTodos);

    // 고유값으로 사용될 id
    // ref를 사용하여 변수 담기
    const nextId = useRef(2501);

    ...
};

export default App;
```

> 주의할 점은 useState의 기본값에 함수를 넣어 주었다는 점, useState(createBulkTodos())라고 작성하면 리렌더링될 때마다 createBulkTodos 함수가 호출되지만, useState(createBulkTodos) 처럼 파라미터를 함수 형태로 넣어 주면 컴포넌트가 처음 렌더링될 때만 createBulkTodos 함수가 실행될 것입니다.

## 느려지는 원인 분석 

- 컴포넌트는 다음과 같은 상황에서 리렌더링이 발생합니다.
    - 자신이 전달받은 props가 변경될 때
    - 자신의 state가 바뀔 때
    - 부모 컴포넌트가 리렌더링될 때
    - forceUpdate 함수가 실행될 때
- '할 일 1' 항목을 체크할 경우 App 컴포넌트의 state가 변경되면서 App 컴포넌트가 리렌더링 됩니다. 부모 컴포넌트가 리렌더링되었으니 TodoList 컴포넌트가 리렌더링되고 그 안의 무수한 컴포넌트들도 리렌더링 됩니다.
- '할 일 1' 항목은 리렌더링되어야 하는 것이 맞지만, '할 일 2'부터 '할 일 2500'까지는 리렌더링을 안 해도 되는 상황인데 모두 리렌더링되고 있으므로 이렇게 느린 것입니다. 컴포넌트의 개수가 많지 않다면 모든 컴포넌트를 리렌더링해도 느려지지 않는데, 지금처럼 약 2,000개가 넘어가면 성능이 저하됩니다.

## React.memo를 사용하여 컴포넌트 성능 최적화

- 컴포넌트의 리렌더링을 방지할 때는 shouldComponentUpdate라는 라이프사이클을 사용하면 됩니다. 
- 그러나 함수 컴포넌트에서는 라이프사이클 메서드를 사용할 수 없습니다.
- 그 대신 React.memo라는 함수를 사용합니다. 컴포넌트의 props가 바뀌지 않았다면, 리렌더링하지 않도록 설정하여 함수 컴포넌트의 리렌더링 성능을 최적화해 줄 수 있습니다.

#### TodoListItem.js 

```javascript
import React from 'react';
import {
    MdCheckBoxOutlineBlank,
    MdCheckBox,
    MdRemoveCircleOutline,
} from 'react-icons/md';
import cn from 'classnames';
import './TodoListItem.scss';

const TodoListItem = ({ todo, onRemove, onToggle }) => {
    ...
};

export default React.memo(TodoListItem);
```

> 이제 TodoListItem 컴포넌트는 todo, onRemove, onToggle이 바뀌지 않으면 리렌더링을 하지 않습니다.

## onToggle, onRemove 함수가 바뀌지 않게 하기

- React.memo를 사용하는 것만으로 컴포넌트 최적화가 끝나지는 않습니다. 현재 프로젝트에서는 todos 배열이 업데이트되면 onRemove와 onToggle 함수도 새롭게 바뀝니다. 
- onRemove와 onToggle 함수는 배열 상태를 업데이트하는 과정에서 최신 상태의 todos를 참조하기 때문에 todos 배열이 바뀔 때마다 함수가 새로 만들어집니다. 
- 함수가 계속 만들어지는 상황을 방지하는 방법은 두 가지 입니다.
    - useState의 함수형 업데이트 기능을 사용하는 것 
    - useReducer를 사용하는 것

### useState의 함수형 업데이트

- 기존 setTodos 함수를 사용할 때는 새로운 상태를 파라미터로 넣어 주었습니다.
- setTodos를 사용할 때 새로운 상태를 파라미터로 넣는 대신, 상태 업데이트를 어떻게 할지 정해주는 업데이트 함수를 넣어줄 수도 있습니다. 이를 함수형 업데이트라고 합니다.

```javascript
const [number, setNumber] = useState(0);
// prevNumber는 현재 number 값을 가리킵니다.
const onIncrease = useCallback(
    () => setNumber(prevNumber => prevNumber + 1),
);
```

- setNumber(number+1)을 하는 것이 아니라, 어떻게 업데이트할지 정의해 주는 업데이트 함수를 넣어줍니다.
- 그러면 useCallback을 사용할 때 두 번째 파라미터로 넣는 배열에 number를 넣지 않아도 됩니다.

#### App.js

```javascript
import { useRef, useState, useCallback } from 'react';
import TodoTemplate from './components/TodoTemplate';
import TodoInsert from './components/TodoInsert';
import TodoList from './components/TodoList'; 

function createBulkTodos() {
    const array = [];
    for (let i = 1; i <= 2500; i++) {
        array.push({
            id: i,
            text: `할 일 ${i}`,
            checked: false,
        });
    }
    return array;
}

const App = () => {
    const [todos, setTodos] = useState(createBulkTodos);

    // 고유값으로 사용될 id
    // ref를 사용하여 변수 담기
    const nextId = useRef(4);

    const onInsert = useCallback(text => {
        const todo = {
            id: nextId.current,
            text,
            checked: false,
        };
         setTodos(todos => todos.concat(todo));
         nextId.current += 1; // nextId 1씩 더라기
    }, []);

    const onRemove = useCallback(id => {
        setTodos(todos => todos.filter(todo => todo.id !== id));
    }, []);

    const onToggle = useCallback(id => {
        setTodos(todos => 
            todos.map(todo => 
                todo.id === id ? { ...todo, checked: !todo.checked } : todo
            )
        );
    }, []);

    return (
        <TodoTemplate>
            <TodoInsert onInsert={onInsert} />
            <TodoList todos={todos} onRemove={onRemove} onToggle={onToggle} />
        </TodoTemplate>
    );
};

export default App;
```

> setTodos를 사용할 때 그 안에 todos => 만 앞에 넣어주면 됩니다.

### useReducer 사용하기

- useState의 함수형 업데이트를 사용하는 대신 useReducer를 사용해도 onToggle, onRemove가 계속 새로워지는 문제를 해결할 수 있습니다.

#### App.js

```javascript
import { useReducer, useRef, useCallback } from 'react';
import TodoTemplate from './components/TodoTemplate';
import TodoInsert from './components/TodoInsert';
import TodoList from './components/TodoList';

function createBulkTodos() {
    const array = [];
    for (let i = 1; i <= 2500; i++) {
        array.push({
            id: i,
            text: `할 일 ${i}`,
            checked: false,
        });
    }
    return array;
}

function todoReducer(todos, action) {
    switch (action.type) {
        case 'INSERT': // 새로 추가
            return todos.concat(action.todo);
        case 'REMOVE': // 제거
            return todos.filter(todo => todo.id !== action.id);
        case 'TOGGLE': // 토글
            return todos.map(topdo => todo.id === action.id ? { ...todo, checked: !todo.checked } : todo);
        default:
            return todos;
    }
}

const App = () => {
    const [todos, dispatch] = useReduer(todoReducer, undefined, createBulkTodos);

    // 고유값으로 사용될 id
    // ref를 사용하여 변수 담기
    const nextId = useRef(2501);

    const onInsert = useCallback(text => {
        const todo = {
            id: nextId.current,
            text,
            checked: false,
        };
        dispatch({ type: 'INSERT', todo });
        nextId.current += 1; // nextId 1씩 더하기
    }, []);

    const onRemove = useCallback(id => {
        dispatch({ type: 'REMOVE', id });
    }, []);

    const onToggle = useCallback(id => {
        dispatch({ type: 'TOGGLE', id });
    }, []);

    return (
        <TodoTemplate>
            <TodoInsert onInsert={onInsert} />
            <TodoList todos={todos} onRemove={onRemove} onToggle={onToggle} />
        </TodoTemplate>
    );
}

export default App;
```

- useReducer를 사용할 때는 원래 두 번째 파라미터에 초기 상태를 넣어 주어야 합니다.
- 지금은 그 대신 두 번쨰 파라미터에 undefined를 넣고, 세 번째 파라미터에 초기 상태를 만들어 주는 함수인 createBulkTodos를 넣어 주었는데, 이렇게 하면 컴포넌트가 맨 처름 렌더링될 때만 createBulkTodos 함수가 호출됩니다.
- useReducer를 사용하는 방법은 상태를 업데이트 하는 로직을 모아서 컴포넌트 바깥에 둘 수 있다는 장점이 있습니다.

## 불변성의 중요성

```javascript
const onToggle = useCallback(id => {
    setTodos(todos => 
        todos.map(todo => todo.id === id ? { ...todo, checked: !todo.checked } : todo)
    );
}, []);
```

- 기존 데이터를 수정할 때 직접 수정하지 않고, 새로운 배열을 만든 다음에 새로운 객체를 만들어서 필요한 부분을 교체해 주는 방식으로 구현되었습니다.
- 업데이트가 필요한 곳에서는 아예 새로운 배열 혹은 새로운 객체를 만들기 때문에, React.memo를 사용했을 때 props가 바뀌었는지 혹은 바뀌지 않았는지를 알아내서 리렌더링 성능을 최적화해 줄 수 있습니다.
- 이렇게 기존의 값을 직접 수정하지 않으면서 새로운 값을 만들어 내는 것을 **불변성을 지킨다**고 합니다.

```javascript
const array = [1,2,3,4,5];

const nextArrayBad = array;  // 배열을 복사는 것이 아니라 똑같은 배열을 가리킵니다.
nextArrayBad[0] = 100;
console.log(array === nextArrayBad); // 완전히 같은 배열이기 때문에 true

const nextArrayGood = [...array]; // 배열 내부의 값을 모두 복사합니다.
nextArrayGood[0] = 100;
console.log(array === nextArrayGood); // 다른 배열이기 때문에 false

const object = {
    foo: 'bar',
    value: 1
};

const nextObjectBad = object; // 객체가 복사되지 않고, 똑같은 객체를 가리킵니다.
nextObjectBad.value = nextObjectBad.value + 1;
console.log(object === nextObjectBad); // 같은 객체이기 때문에 true

const nextObjectGood = {
    ...object, // 기존에 있던 내용을 모두 복사해서 넣습니다.
    value: object.value + 1 // 새로운 값을 덮어 씁니다.
};
console.loog(object === nextObjectGood); // 다른 객체이기 때문에 false
```

- 불변성이 지켜지지 않으면 객체 내부의 값이 새로워져도 바뀐 것을 감지하지 못합니다. 그러면 React.memo에서 서로 비교하여 최적화하는 것이 불가능합니다.
- 전개 연산자(... 문법)을 사용하여 객체나 배열 내부의 값을 복사할 때는 얕은 복사(shallow copy)를 하게 됩니다. 즉, 내부의 값이 완전히 새로 복사되는 것이 아니라 가장 바깥쪽에 있는 값만 복사됩니다.
- 따라서 내부의 값이 객체 혹은 배열이라면 내부의 값 또한 따로 복사해 주어야 합니다. 
- 배열 혹은 객체의 구조가 매우 복잡해진다면 이렇게 불변성을 유지하면서 업데이트하는 것도 까다로워집니다. 이렇게 복잡한 상황인 경우 immer라는 라이브러리의 도움을 받으면 정말 편하게 작업할 수 있습니다.

## TodoList 컴포넌트 최적화하기

- 리스트에 관련한 컴포넌트를 최적화할 떄는 리스트 내부에서 사용하는 컴포넌트도 최적화해야 하고, 리스트에 사용되는 컴포넌트 자체도 최적화해 주는 것이 좋습니다.

#### TodoList.js

```javascript
import React from 'react';
import TodoListItem from './TodoListItem';
import './TodoList.scss';

const TodoList = ({ todos, onRemove, onToggle }) => {
	return (...);
};

export default React.memo(TodoList);
```

## react-virtualized를 사용한 렌더링 최적화

- 현재 컴포넌트가 맨 처음 렌더링될 때 2,500개 컴포넌트 중 2,491개 컨포넌트는 스크롤하기 전에는 보이지 않음에도 불구하고 렌더링이 이루어집니다. 그리고 나중에 todos 배열에 변동이 생길 때도 TodoList 컴포넌트 내부의 map 함수에서 배열의 처음부터 끝까지 컴포넌트로 변환해 주는데, 이 중에서 2,491개는 보이지 않으므로 시스템 자원 낭비입니다.

- react-virtualized를 사용하면 리스트 컴포넌트에서 스크롤되기 전에 보이지 않는 컴포넌트는 렌더링하지 않고 크기만 차지하게끔 할 수 있습니다. 만약 스크롤되면 해당 스크롤 위치에서 보여 주어야 할 컴포넌트를 자연스럽게 렌더링 시킵니다.

### 최적화 준비

```
$ yarn add react-virtualized
```

### TodoList 수정 

#### TodoList.js

```javascript
import React, { useCallback } from 'react';
import { List } from 'react-virtualized';
import TodoListItem from './TodoListItem';
import './TodoList.scss';

const TodoList = ({ todos, onRemove, onToggle }) => {
	const rowRenderer = useCallback(
		({ index, key, style }) => {
			const todo = todos[index];
			return (
				<TodoListItem
					todo={todo}
					key={key}
					onRemove={onRemove}
					onToggle={onToggle}
					style={style}
				/>
			);
		},
		[onRemove, onToggle, todos]
	);
	
	return (
		<List 
			className="TodoList"
			width={512} // 전체 크기
			height={513} // 전체 높이
			rowCount={todos.length} // 항목 개수
			rowHeight={57} // 항목 높이
			rowRenderer={rowRenderer} // 항목을 렌더링할 때 쓰는 함수 
			list={todos} // 배열
			style={{ outline: 'none' }} // List에 기본 적용되는 outline 스타일 제거
		/>
	);
};

export default React.memo(TodoList);
```

- List 컴포넌트를 사용하기 위해 rowRenderer라는 함수를 새로 작성해 주었습니다. 이 함수는 react-virtualized의 List 컴포넌트에서 각 TodoItem을 렌더링할 때 사용하며, 이 함수를 List 컴포넌트의 props로 설정해 주어야 합니다. 이 함수는 파라미터에 index, key, style 같은 객체 타입으로 받아 와서 사용합니다.

- List 컴포넌트를 사용할 때는 해당 리스트의 전체 크기와 각 항목의 높이, 각 항목을 렌더링할 때 사용해야 하는 함수, 그리고 배열을 props로 넣어 주어야 합니다. 그러면 이 컴포넌트가 전달받은 props를 사용하여 자동으로 최적화해 줍니다.  


### TodoListItem 수정

#### TodoListItem.js - render

```javascript
import React from 'react';
import {
	MdCheckBoxOutlineBlank,
	MdCheckBox,
	MdRemoveCircleOutline,
} from 'react-icons/md';
import cn from 'classnames';
import './TodoListItem.scss';

const TodoListItem = ({ todo, onRemove, onToggle, style }) => {
	const { id, text, checked } = todo;
	
	return (
		<div classNames="TodoListItem-virtualized" style={style}>
			<div className="TodoListItem">
				<div 
					className={cn('checkbox', { checked })}
					onClick={() => onToggle(id)}
				>
					{ checked ? <MdCheckBox /> : <MdCheckBoxOutlineBlank /> }
					<div className="text">{text}</div>
				</div>
				<div className="remove" onClick={() => onRemove(id)}>
					<MdRemoveCircleOutline />
				</div>
			</div>
		</div>
	);
};

export default React.memo(TodoListItem); 
```

#### TodoListItem.scss

```scss 
.TodoListItem-virtualized {
	& + & {
		border-top: 1px solid #dee2e6;
	}
	&:nth-child(even) {
		background: #f8f9fa;
	}
}

... 

```