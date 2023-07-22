# 리덕스를 사용하여 리액트 애플리케이션 상태 관리하기

## 작업 환경 설정

```
$ yarn create react-app react-redux-tutorial
$ cd react-redux-tutorial
$ yarn add redux react-redux
```

#### .prettierrc

```javascript
{
    "singleQuote": true,
    "semi": true,
    "useTabs": false,
    "tabWidth": 2,
    "trailingComma": "all",
    "printWidth": 80
}
```

## UI 준비하기

- 리액트 프로젝트에서 리덕스를 사용할 때 가장 많이 사용하는 패턴은 프리젠테이셔널 컴포넌트와 컨테이너 컴포넌트를 분리하는 것입니다.
- **프리젠테이션 컴포넌트**란 주로 상태 관리가 이루어지지 않고, props만 받아 와서 화면에 UI를 보여 주기만 하는 컴포넌트를 말합니다.
- 이와 달리 **컨테이너 컴포넌트**는 리덕스와 연동되어 있는 컴포넌트로, 리덕스로부터 상태를 받아 오기도 하고 리덕스 스토어에 액션을 디스패치하기도 합니다.
- 이 패턴을 사용하면 코드의 재사용성도 높아지고 관심사의 분리가 이루어져 UI를 작성에 좀 더 집중 할 수 있습니다.

![프리젠티이셔널 컴포넌트와 컨테이너 컴포넌트](https://raw.githubusercontent.com/yonggyo1125/reactLecture/master/16.%20%EB%A6%AC%EB%8D%95%EC%8A%A4%EB%A5%BC%20%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC%20%EB%A6%AC%EC%95%A1%ED%8A%B8%20%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98%20%EC%83%81%ED%83%9C%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/images/1.png)

- UI 관련된 프리젠테이셔널 컴포넌트는 src/components 경로에 저장하고, 리덕스와 연동된 컨테이너 컴포넌트는 src/containers 컴포넌트에 작성합니다.

## 카운터 컴포넌트 만들기

#### components/Counter.js

```javascript
const Counter = ({ number, onIncrease, onDecrease }) => {
  return (
    <div>
      <h1>{number}</h1>
      <div>
        <button onClick={onIncrease}>+1</button>
        <button onClick={onDecrease}>-1</button>
      </div>
    </div>
  );
};

export default Counter;
```

#### App.js

```javascript
import Counter from "./components/Counter";

const App = () => {
  return (
    <div>
      <Counter number={0} />
    </div>
  );
};

export default App;
```

### 할 일 목록 컴포넌트 만들기

#### components/Todos.js

```javascript
const TodoItem = ({ todo, onToggle, onRemove }) => {
  return (
    <div>
      <input type="checkbox" />
      <span>예제 텍스트</span>
      <button>삭제</button>
    </div>
  );
};

const Todos = ({
  input, // 인풋에 입력되는 텍스트
  todos, // 할 일 목록이 들어 있는 객체
  onChangeInput,
  onInsert,
  onToggle,
  onRemove,
}) => {
  const onSubmit = (e) => {
    e.preventDefault();
  };
  return (
    <div>
      <form onSubmit={onSubmit}>
        <input />
        <button type="submit">등록</button>
      </form>
      <div>
        <TodoItem />
        <TodoItem />
        <TodoItem />
        <TodoItem />
        <TodoItem />
      </div>
    </div>
  );
};

export default Todos;
```

#### App.js

```javascript
import Counter from "./components/Counter";
import Todos from "./components/Todos";

const App = () => {
  return (
    <div>
      <Counter number={0} />
      <hr />
      <Todos />
    </div>
  );
};

export default App;
```

## 리덕스 관련 코드 작성하기

- 가장 일반적인 구조로 actions, containers, reducers라는 세 개의 디렉토리를 만들고 그 안에 기능별로 파일을 하나씩 만드는 방법
- 코드를 종류에 따라 다른 파일에 작성하여 정리할 수 있어서 편리하지만, 새로운 액션을 만들 때마다 세 종류의 파일을 모두 수정해야 하기 때문에 불편
- 이 방식은 리덕스 공식 문서에서도 사용되므로 가장 기본적이라 할 수 있지만, 사람에 따라 불편할 수도 있는 구조

### ducks 패턴

- 액션 타입, 액션 생성 함수, 리듀서 함수를 기능별로 파일 하나에 몰아서 다 작성하는 방식

### counter 모듈 작성하기

> Ducks 패턴을 사용하여 액션 타입, 액션 생성함수, 리듀서를 작성한 코드를 **모듈**이라고 합니다.

### 액션타입 정의하기

- modules 디렉토리를 생성하고 그 안에 counter.js 파일을 다음과 같이 작성합니다.

#### modules/counter.js

```javascript
const INCREASE = "counter/INCREASE";
const DECREASE = "counter/DECREASE";
```

- 가장 먼저 해야 할 작업은 액션 타입을 정의하는 것입니다.
- 액션 타입은 대문자로 정의하고, 문자열 내용은 **모듈 이름/액션 이름**과 같은 형태로 작성합니다.
- 문자열 안에 모듈 이름을 넣음으로써, 프로젝트가 커졌을 때 액션의 이름이 충돌되지 않게 해 줍니다.

### 액션 생성 함수 만들기

#### modules/counter.js

```javascript
const INCREASE = "counter/INCREASE";
const DECREASE = "counter/DECREASE";

export const increase = () => ({ type: INCREASE });
export const decrease = () => ({ type: DECREASE });
```

### 초기 상태 및 리듀서 함수 만들기

```javascript
const INCREASE = "counter/INCREASE";
const DECREASE = "counter/DECREASE";

export default increase = () => ({ type: INCREASE });
export default decrease = () => ({ type: DECREASE });

const initialState = {
    number: 0
};

function counter(state = initialState, action) {
    switch (action.type) {
        case INCREASE:
            return {
                number: state.number + 1
            };
        case DECREASE:
            return {
                number: state.number - 1
            };
        default:
            return state;
    }
}

export default counter;
```

> export는 여러 개를 내보낼 수 있지만 export default는 단 한개만 내보낼 수 있다.

```javascript
import counter from "./counter";
import { increase, decrease } from "./counter";

// 한번에 불러오고 싶을 때
import counter, { increase, decrease } from "./counter";
```

### todos 모듈 만들기

### 액션 타입 정하기

#### modules/todos.js

```javascript
const CHANGE_INPUT = "todos/CHANGE_INPUT"; // 인풋 값을 변경함
const INPUT = "todos/INSERT"; // 새로운 todo를 등록함
const TOGGLE = "todos/TOGGLE"; // todo를 체크/체크 해제함
const REMOVE = "todos/REMOVE"; // todo를 제거함
```

### 액션 생성 함수 만들기

#### modules/todos.js

```javascript
const CHANGE_INPUT = "todos/CHANGE_INPUT"; // 인풋 값을 변경함
const INSERT = "todos/INSERT"; // 새로운 Todo를 등록함
const TOGGLE = "todos/TOGGLE"; // todo를 체크/체크 해제함
const REMOVE = "todos/REMOVE"; // todo를 제거함

export const changeInput = (input) => ({
  type: CHANGE_INPUT,
  input,
});

let id = 3; // insert가 호출될 때마다 1씩 더해집니다.
export const insert = (text) => ({
  type: INSERT,
  todo: {
    id: id++,
    text,
    done: false,
  },
});

export const toggle = (id) => ({
  type: TOGGLE,
  id,
});

export const remove = (id) => ({
  type: REMOVE,
  id,
});
```

### 초기 상태 및 리듀서 함수 만들기

#### modules/todos.js

```javascript
...

const initialState = {
    input: '',
    todos: [
        {
            id: 1,
            text: '리덕스 기초 배우기',
            done: true
        },
        {
            id: 2,
            text: '리액트와 리덕스 사용하기',
            done: false
        }
    ]
};


function todos(state = initialState, action) {
    switch (action.type) {
        case CHANGE_INPUT:
            return {
                ...state,
                input: action.input
            };
        case INSERT:
            return {
                ...state,
                todos: state.todo.concat(action.todo)
            };
        case TOGGLE:
            return {
                ...state,
                todos: state.todos.map(todo => todo.id === action.id ? { ...todo, done: !todo.done } : todo)
            };
        case REMOVE:
            return {
                ...state,
                todos: state.todo.filter(todo => todo.id !== action.id)
            };
        default:
            return state;
    }
}

export default todos;
```

### 루트 리듀서 만들기

- createStore 함수를 사용하여 스토어를 만들 때는 리듀서를 하나만 사용하여야 하므로 리듀서를 하나로 합쳐야 합니다.
- 이 작업은 리덕스에서 제공하는 combineReducers라는 유틸 함수를 사용하면 쉽게 처리할 수 있습니다.

#### modules/index.js

```javascript
import { combineReducers } from "redux";
import counter from "./counter";
import todos from "./todos";

const rootReducer = combineReducers({
  counter,
  todos,
});

export default rootReducer;
```

> 파일 이름을 index.js로 설정해 주면 불러올 때 디렉터리 이름까지만 입력하여 불러올 수 있습니다.

```javascript
import rootReducer from "./modules";
```

## 리액트 애플리케이션에 리덕스 적용하기

### 스토어 만들기

#### src/index.js

```javascript
import ReactDOM from "react-dom";
import { createStore } from "redux";
import "./index.css";
import App from "./App";
const rootReducer from './modules';

const store = createStore(rootReducer);

ReactDOM.render(<App />, document.getElementById('root'));
```

### Provider 컴포넌트를 사용하여 프로젝트에 리덕스 적용하기

> 리액트 컴포넌트에서 스토어를 사용할 수 있도록 App 컴포넌트를 react-redux에서 제공하는 Provider 컴포넌트로 감싸 줍니다. 이 컴포넌트를 사용할 때는 store를 props로 전달해 주어야 합니다.

#### src/index.js

```javascript
import ReactDOM from "react-dom";
import { createStore } from "redux";
import { Provider } from "react-redux";
import "./index.css";
import App from "./App";
import rootReducer from "./modules";

const store = createStore(rootReducer);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

### Redux DevTools의 설치 및 적용

- Redux DevTools는 리덕스 개발자 도구이며, 크롬 확장 프로그램으로 설치하여 사용할 수 있습니다.
- 크롬 웹 스토어(https://chrome.google.com/webstore/)에서 Redux DevTools를 검색하여 설치합니다.
- 설치하고 나면 리덕스 스토어를 만드는 과정에서 다음과 같이 적용합니다.

```javascript
const store = createStore(
  rootReducer /* preloadedState, */,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);
```

- 패키지를 설치하여 적용하면 코드가 깔끔해집니다. 패키지를 설치하고 적용해 봅니다.
- 패키지를 설피하여 사용한다고 해도 크롬 확장 프로그램은 설치해야 합니다.

```
$ yarn add redux-devtools-extension
```

- 다음과 같이 적용해 줍니다.

#### src/index.js

```javascript
import ReactDOM from "react-dom";
import { createStore } from "redux";
import { Provider } from "react-redux";
import { composeWithDevTools } from "redux-devtools-extension";
import "./index.css";
import App from "./App";
import rootReducer from "./modules";

const store = createStore(rootReducer, composeWithDevTools());

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

## 컨테이너 컴포넌트 만들기

> 리덕스 스토어와 연동된 연동된 컴포넌트를 컨테이너 컴포넌트라고 부릅니다.

### CounterContainer 만들기

#### containers/CounterContainer.js

```javascript
import Counter from "../components/Counter";

const CounterContainer = () => {
  return <Counter />;
};

export default CounterContainer;
```
