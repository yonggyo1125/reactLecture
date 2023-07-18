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
import Counter from './components/Counter';

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
    const onSubmit = e => {
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
import Counter from './components/Counter';
import Todos from './components/Todos';

const App = () => {
    return (
        <div>
            <Counter number={0} />
        </div>
    );
};

export default App;
```