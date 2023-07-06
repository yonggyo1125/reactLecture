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

