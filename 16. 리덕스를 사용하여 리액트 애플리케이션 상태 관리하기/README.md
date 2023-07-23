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

- 위 컴포넌트를 리덕스와 연동하려면 react-redux에서 제공하는 connect 함수를 사용해야 합니다.

```javascript
connect(mapStateToProps, mapDispatchToProps)(연동할 컴포넌트)
```

- **mapStateToProps** : 리덕스 스토어 안의 상태를 컴포넌트의 props로 넘겨주기 위해 설정하는 함수
- **mapDispatchToProps** : 액션 생성 함수를 컴포넌트의 props로 넘겨주기 위해 사용하는 함수
- connect 함수를 호출하고 나면 다른 함수를 반환합니다. 반환된 함수에 컴포넌트를 파라미터로 넣어 주면 리덕스와 연동된 컴포넌트가 만들어집니다.

```javascript
const makeContainer = connect(mapStateToProps, mapDispatchToProps);
makeContainer(타깃 컴포넌트);
```

#### containers/CounterContainer.js

```javascript
import { connect } from "react-redux";
import Counter from "../components/Counter";

const CounterContainer = ({ number, increase, decrease }) => {
  return (
    <Counter number={number} onIncrease={increase} onDecrease={decrease} />
  );
};

const mapStateToProps = (state) => ({
  number: state.counter.number,
});

const mapDispatchToProps = (dispatch) => ({
  // 임시함수
  increase: () => {
    console.log("increase");
  },
  decrease: () => {
    console.log("decrease");
  },
});
export default connect(mapStateToProps, mapDispatchToProps)(CounterContainer);
```

#### App.js

```javascript
import Todos from "./components/Todos";
import CounterContainer from "./containers/CounterContainer";

const App = () => {
  return (
    <div>
      <CounterContainer />
      <hr />
      <Todos />
    </div>
  );
};

export default App;
```

#### containers/CounterContainer.js

```javascript
import { connect } from "react-redux";
import Counter from "../components/Counter";
import { increase, decrease } from "../modules/counter";

const CounterContainer = ({ number, increase, decrease }) => {
  return (
    <Counter number={number} onIncrease={increase} onDecrease={decrease} />
  );
};

const mapStateToProps = (state) => ({
  number: state.counter.number,
});

const mapDispatchToProps = (dispatch) => ({
  increase: () => {
    dispatch(increase());
  },
  decrease: () => {
    dispatch(decrease());
  },
});

export default connect(mapStateToProps, mapDispatchToProps)(CounterContainer);
```

> connect 함수를 사용할 때는 일반적으로 mapStateToProps와 mapDispatchToProps를 미리 선언해 놓고 사용합니다. 그러나 connect 함수에 익명 함수 형태로 선언해도 됩니다.

#### containers/CounterContainer.js

```javascript
import { connect } from "react-redux";
import Counter from "../components/Counter";

const CounterContainer = ({ numner, increase, decrease }) => {
  return (
    <Counter number={number} onIncrease={increase} onDecrease={decrease} />
  );
};

export derault connect(
  state => ({
    number: state.counter.number,
  }),
  dispatch => ({
    increase: () => dispatch(increase()),
    decrease: () => dispatch(decrease()),
  }),
)(CounterContainer);
```

> 컴포넌트에서 액션을 디스패치하기 위해 각 액션 생성 함수를 호출하고 dispatch로 감싸는 작업이 번거로울 수 있습니다. 액션 생성함수의 개수가 많아지면 더더욱 그렇습니다. 이런 경우는 리덕스에서 제공하는 **bindActionCreators** 유틸 함수를 사용하면 간편합니다.

#### containers/CounterContainer.js

```javascript
import { bindActionCreators } from "redux";
import { connect } from "react-redux";
import Counter from "../components/Counter";
import { increase, decrease } from "../modules/counter";

const CounterContainer = ({ number, increase, decrease }) => {
  return (
    <Counter number={number} onIncrease={increase} onDecrease={decrease} />
  );
};

export default connect(
  (state) => ({
    number: staet.counter.number,
  }),
  (dispatch) =>
    bindActionCreators(
      {
        increase,
        decrease,
      },
      dispatch
    )
)(CounterContainer);
```

> 더 편한 방법이 있는데, mapDispatchToProps에 해당하는 파라미터를 함수 형태가 아닌 액션 생성 함수로 이루어진 객체 형태로 넣어 주는 것입니다.

#### containers/CounterContainer.js

```javascript
import { connect } from "react-redux";
import Counter from "../components/Counter";
import { increase, decrease } from "../modules/counter";

const CounterContainer = ({ number, increase, decrease }) => {
  return (
    <Counter number={number} onIncrease={increase} onDecrease={decrease} />
  );
};

export default connect(
  (state) => ({
    number: state.counter.number,
  }),
  {
    increase,
    decrease,
  }
)(CounterContainer);
```

> 두 번째 파라미터를 객체 형태로 넣어 주면 connect 함수가 내부적으로 bindActionCreators 작업을 대신해 줍니다.

### TodosContainer 만들기

#### containers/TodosContainer.js

```javascript
import { connect } from "react-redux";
import { changeInput, insert, toggle, remove } from "../module/todos";
import Todos from "../components/Todos";

const TodosContainer = ({
  input,
  todos,
  changeInput,
  insert,
  toggle,
  remove,
}) => {
  return (
    <Todos
      input={input}
      todos={todos}
      onChangeInput={changeInput}
      onInsert={insert}
      onToggle={toggle}
      onRemove={remove}
    />
  );
};

export default connect(
  ({ todos }) => ({
    input: todos.input,
    todos: todos.todos,
  }),
  {
    changeInput,
    insert,
    toggle,
    remove,
  }
)(TodosContainer);
```

#### App.js

```javascript
import CounterContainer from "./containers/CounterContainer";
import TodosContainer from "./containers/TodosContainer";

const App = () => {
  return (
    <div>
      <CounterContainer />
      <hr />
      <TodosContainer />
    </div>
  );
};

export default App;
```

#### components/Todos.js

```javascript
const TodoItem = ({ todo, onToggle, onRemove }) => {
  return (
    <div>
      <input
        type="checkbox"
        onClick={() => onToggle(todo.id)}
        checked={todo.done}
        readOnly={true}
      />
      <span style={{ textDecoration: todo.done ? "line-through" : "done" }}>
        {todo.text}
      </span>
      <button onClick={() => onRemove(todo.id)}>삭제</button>
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
    onInsert(input);
    onChangeInput(""); // 등록 후
  };

  const onChange = (e) => onChangeInput(e.target.value);
  return (
    <div>
      <form onSubmit={onSubmit}>
        <input value={input} onChange={onChange} />
        <button type="submit">등록</button>
      </form>
      <div>
        {todos.map((todo) => (
          <TodoItem
            todo={todo}
            key={todo.id}
            onToggle={onToggle}
            onRemove={onRemove}
          />
        ))}
      </div>
    </div>
  );
};

export default Todos;
```

## 리덕스 더 편하게 사용하기

### redux-actions

- redux-actions를 사용하면 액션 생성함수를 더 짧은 코드로 작성할 수 있습니다.
- 리듀서를 작성할 때도 switch/case 문이 아닌 handleActions라는 함수를 사용하여 각 액션마다 업데이트 함수를 설정하는 형식으로 작성해 줄 수 있습니다.

```
$ yarn add redux-actions
```

### counter 모듈에 적용하기

#### modules/counter.js

```javascript
import { createAction } from 'redux-actions';

const INCREASE = 'counter/INCREASE';
const DECREASE = 'counter/DECREASE';

export const increase = createAction(INCREASE);
export const decrease = createAction(DECREASE);

...
```

- handleActions라는 함수를 사용하면 리듀서 함수도 더 간단하고 가독성 높게 작성할 수 있습니다.

#### modules/counter.js

```javascript
import { createAction, handleActions } from "redux-actions";

const INCREASE = "counter/INCREASE";
const DECREASE = "counter/DECREASE";

export const increase = createAction(INCREASE);
export const decrease = createAction(DECREASE);

const initialState = {
  number: 0,
};

const counter = handleActions(
  {
    [INCREASE]: (state, action) => ({ number: state.number + 1 }),
    [DECREASE]: (state, action) => ({ number: state.number - 1 }),
  },
  initialState
);
```

### todos 모듈에 적용하기

- createAction으로 액션을 만들면 액션에 필요한 추가 데이터는 payload라는 이름을 사용합니다.

```javascript
const MY_ACTION = "sample/MY_ACTION";
const myAction = createAction(MY_ACTION);
const action = myAction("hello worl");

/*
  결과:
  { type: MY_ACTION, payload: 'hello world' }
*/
```

- 액션 생성 함수에서 받아 온 파라미터를 그대로 payload에 넣는 것이 아니라 변형을 주어서 넣고 싶다면, createAction의 두 번째 함수에 payload를 정의하는 함수를 따로 선언해서 넣어 주면 됩니다.

```javascript
const MY_ACTION = "sample/MY_ACTION";
const myAction = crreateAction(MY_ACTION, (text) => `${text}!`);
const action = myAction("hello world");
/*
  결과:
  { type: MY_ACTION, payload: 'hello world!' }
*/
```

#### modules/todos.js

```javascript
import { createAction } from "redux-actions";

const CHANGE_INPUT = "todos/CHANGE_INPUT"; // 인풋 값을 변경함
const INSERT = "todos/INSERT"; // 새로운 todo를 등록함
const TOGGLE = "todos/TOGGLE"; // todo를 체크/체크 해제함
const REMOVE = "todos/REMOVE"; // todo를 제거함

export const changeInput = createAction(CHANGE_INPUT, (input) => input);

let id = 3; // insert가 호출될 때마다 1씩 더해집니다.
export const insert = createAction(INSERT, (text) => ({
  id: id++,
  text,
  done: false,
}));

export const toggle = createAction(TOGGLE, (id) => id);
export const remove = createAction(REMOVE, (id) => id);
```

> 액션 생성 함수는 액션에 필요한 추가 데이터를 모두 payload라는 이름으로 사용하기 때문에 action.id, action.todo를 조회하는 대신, 모두 공통적으로 action.payload 값을 조회하도록 리듀서를 구현해 주어야 합니다.

#### modules/Todos.js

```javascript
import { createAction, handleActions } from 'redux-actions';

...

const todos = handleActions(
  {
    [CHANGE_INPUT]: (state, action) => ({ ...state, input: action.payload }),
    [INSERT]: (state, action) => ({
      ...state,
      todos: state.todos.concat(action.payload),
    }),
    [TOGGLE]: (state, action) => ({
      ...state,
      todos: state.todos.map(todo =>
        todo.id === action.payload ? { ...todo, done: !todo.done } : todo,
      ),
    }),
    [REMOVE]: (state, action) => ({
      ...state,
      todos: state.todos.filter(todo => todo.id !== action.payload),
    }),
  }
  initialState,
);

export default todos;
```

> 모든 추가 데이터 값을 action.payload로 사용하기 때문에 리듀서 코드를 볼때 혼동이 있을 수 있습니다. 객체 비구조화 할당 문법으로 action 값의 payload 이름을 새로 설정해 주면 action.payload가 정확히 어떤 값을 의미하는지 더 쉽게 파악할 수 있습니다.

#### modules/todos.js

```javascript
...

const todos = handleActions(
  {
    [CHANGE_INPUT]: (state, { payload: input }) => ({ ...state, input }),
    [INSERT]: (state, { payload: todo }) => ({
      ...state,
      todos: state.todos.concat(todo),
    }),
    [TOGGLE]: (state, { payload: id }) => ({
      ...state,
      todos: state.todos.map(todo => todo.id === id ? { ...todo, done: !todo.done } : todo)
    }),
    [REMOVE]: (state, { payload: id }) => ({
      ...state,
      todos: state.todos.filter(todo => todo.id !== id),
    }),
  },
  initialState
);

export default todos;
```

### immer

- 리듀서에서 상태를 업데이트할 때는 불변성을 지켜야 하기 때문에 spread 연산자(...)와 배열의 내장 함수를 활용했습니다. 그러나 모듈의 상태가 복잡해질수록 불편성을 지키기가 까다로워집니다.
- 객체의 구조가 복잡해지거나 객체로 이루어진 배열을 다룰 경우, immer를 사용하면 훨신 편리하게 상태를 관리할 수 있습니다.

```
$ yarn add immer
```

#### modules/todos.js

```javascript
import { createAction, handleActions } from 'redux-actions';
import produce from 'immer';

...

const todos = handleActions(
  {
    [CHANGE_INPUT]: (state, { payload: input }) =>
      produce(state, draft => {
        draft.input = input;
      }),
    [INSERT]: (state, { payload: todo }) =>
      produce(state, draft => {
        draft.todos.push(todo);
      }),
    [TOGGLE]: (state, { payload: id }) =>
      produce(state, draft => {
        const todo = draft.todos.find(todo => todo.id === id);
        todo.done = !todo.done;
      }),
    [REMOVE]: (state, { payload: id }) =>
      produce(state, draft => {
        const index = draft.todos.findIndex(todo => todo.id === id);
        draft.todos.splice(index, 1);
      }),
  },
  initialState,
);

export default todos;
```

## Hooks를 사용하셔 컨테이너 컴포넌트 만들기

> 리덕스 스토어와 연동된 컨테이너 컴포넌트를 만들 때 connect 함수를 사용하는 대신 react-redux에서 제공하는 Hooks를 사용할 수도 있습니다.

### useSelector로 상태 조회하기

- useSelector Hook을 사용하면 connect 함수를 사용하지 않아도 리덕스의 상태를 조회할 수 있습니다.

```javascript
const 결과 = useSelector(상태 선택 함수);
```

- 상태 선택 함수는 mapStateToProps의 형태와 동일합니다.
- CounterContainer에서 connect 함수 대신 useSelector를 사용하여 counter.number 값을 조회함으로써 Counter에게 props를 넘겨 줍니다.

#### containers/CounterContainer.js

```javascript
import { useSelector } from "react-redux";
import Counter from "../components/Counter";
import { increase, decrease } from "../modules/counter";

const CounterContainer = () => {
  const number = useSelector((state) => state.counter.number);
  return <Counter number={number} />;
};

export default CounterContainer;
```

### useDispatch를 사용하여 액션 디스패치하기

- 이 Hook은 컴포넌트 내부에서 스토어의 내장 함수 dispatch를 사용할 수 있게 해 줍니다.
- 컨테이너 컴포넌트에서 액션을 디스패치해야 한다면 이 Hook을 사용하면 됩니다.

```javascript
const dispatch = useDispatch();
dispatch({ type: "SAMPLE_ACTION" });
```

#### containers/CounterContainer.js

```javascript
import { useSelector, useDispatch } from "react-redux";
import Counter from "../components/Counter";
import { increase, decrease } from "../modules/counter";

const CounterContainer = () => {
  const number = useSelector((state) => state.counter.number);
  const dispatch = useDispatch();
  return (
    <Counter
      number={number}
      onIncrease={() => dispatch(increase())}
      onDecrease={() => dispatch(decrease())}
    />
  );
};

export default CounterContainer;
```

- 숫자가 바뀔때마다 컴포넌트가 리렌더링되고 onIncrease 함수와 onDecrease 함수가 새롭게 만들어지고 있습니다.
- useCallback으로 액션을 디스패치하는 함수를 감싸주면 컴포넌트 성능을 최적화할 수 있습니다.

#### containers/CounterContainer.js

```javascript
import { useCallback } from "react";
import { useSelector, useDispatch } from "react-redux";
import Counter from "../components/Counter";
import { increase, decrease } from "../modules/counter";

const CounterContainer = () => {
  const number = useSelector((state) => state.counter.number);
  const dispatch = useDispatch();
  const onIncrease = useCallback(() => dispatch(increase()), [dispatch]);
  const onDecrease = useCallback(() => dispatch(decrease()), [dispatch]);
  return (
    <Counter number={number} onIncrease={onIncrease} onDecrease={onDecrease} />
  );
};

export default CounterContainer;
```

> useDispatch를 사용할 땐 useCallback과 함께 쓰는 것이 좋습니다.

### useStore를 사용하여 리덕스 스토어 사용하기

- useStore Hooks를 사용하면 컴포넌트 내부에서 리덕스 스토어 객체를 직접 사용할 수 있습니다.
- useStore는 어쩔수 없이 스토어에 직접 접근해야 하는 상황에서만 사용해야 합니다. 사용하는 경우가 많지 않습니다.

```javascript
const store = useStore();
store.dispatch({ type: "SAMPLE_ACTION" });
store.getState();
```

### TodosContainer를 Hooks로 전환하기

- TodosContainer를 connect 함수 대신에 useSeletor와 useDispatch Hooks를 사용하는 방식으로 변경해 봅시다.

#### containers/TodosContainer.js

```javascript
import { useCallback } from "react";
import { useSelector, useDispatch } from "react-redux";
import { changeInput, insert, toggle, remove } from "../modules/todos";
import Todos from "../components/Todos";

const TodosContainer = () => {
  const { input, todos } = useSelector(({ todos }) => ({
    input: todos.input,
    todos: todos.todo,
  }));
  const dispatch = useDispatch();
  const onChangeInput = useCallback(
    (input) => dispatch(changeInput(input)),
    [dispatch]
  );
  const onInsert = useCallback((text) => dispatch(insert(text)), [dispatch]);
  const onToggle = useCallback((id) => dispatch(toggle(id)), [dispatch]);
  const onRemove = useCallback((id) => dispatch(remove(id)), [dispatch]);

  return (
    <Todos
      input={input}
      todos={todos}
      onChangeInput={onChangeInput}
      onInsert={onInsert}
      onToggle={onToggle}
      onRemove={onRemove}
    />
  );
};

export default TodosContainer;
```

### useActions 유틸 Hooks을 만들어서 사용하기

- useActions는 원래 react-redux에 내장된 상태로 릴리즈될 계획이었으나 리덕스 개발 팀에서 꼭 필요하지 않다고 판단하여 제외된 Hook 입니다. 그 대신 공식 문서에서 그대로 복사하여 사용할 수 있도록 제공하고 있습니다.
- [참고링크](https://react-redux.js.org/next/api/hooks#recipe-useactions)
- 이 Hook을 사용하면 여러 개의 액션을 사용해야 하는 경우 코드를 깔끔하게 정리하여 작성할 수 있습니다.

#### lib/useActions.js

```javascript
import { bindActionCreators } from "redux";
import { useDispatch } from "react-redux";
import { useMemo } from "react";

export default function useActions(actions, deps) {
  const dispatch = useDispatch();
  return useMemo(
    () => {
      if (Array.isArray(actions)) {
        return actions.map((a) => bindActionCreators(a, dispatch));
      }
      return bindActionCreators(actions, dispatch);
    },
    deps ? [dispatch, ...deps] : deps
  );
}
```

#### containers/TodosContainer.js

```javascript
import { useSelector } from "react-redux";
import { changeInput, insert, toggle, remove } from "../modules/todos";
import Todos from "../components/Todos";
import useActions from "../lib/useActions";

const TodosContainer = () => {
  const { input, todos } = useSelector(({ todos }) => ({
    input: todos.input,
    todos: todos.todo,
  }));

  const [onChangeInput, onInsert, onToggle, onRemove] = useActions(
    [chagneInput, insert, toggle, remove],
    []
  );
  return (
    <Todos
      input={input}
      todos={todos}
      onChangeInput={onChangeInput}
      onInsert={onInsert}
      onToggle={onToggle}
      onRemove={onRemove}
    />
  );
};

export default TodosContainer;
```

### connect 함수와의 주요 차이점

- **connect 함수**를 사용하여 컨테이너 컴포넌트를 만들었을 경우, 해당 컨테이너 컴포넌트의 부모 컴포넌트가 리렌더링될 때 해당 컨테이너 컴포넌트의 props가 바뀌지 않았다면 리렌더링이 자동으로 방지되어 성능이 최적화됩니다.
- **useSelector 함수**를 사용하여 리덕스 상태를 조회했을 때는 이 최적화 작업이 자동으로 이루어지지 않으므로, 성능 최적화를 위해서는 React.memo를 컨테이너 컴포넌트에 사용해 주어야 합니다.

#### containers/TodosContainer.js

```javascript
import React from "react";
import { useSelector } from "react-redux";
import { changeInput, insert, toggle, remove } from "../modules/todos";
import Todos from "../components/Todos";
import useActions from "../lib/useActions";

const TodosContainer = () => {
  ...
};

export default React.memo(TodosContainer);
```
