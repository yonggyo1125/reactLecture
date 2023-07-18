# 리덕스 라이브러리 이해하기

- 리덕스는 가장 많이 사용하는 리액트 상태 관리 라이브러리 입니다.
- 리덕스를 사용하면 컴포넌트의 상태 업데이트 관련 로직을 다른 파일로 분리시켜서 더욱 효율적으로 관리할 수 있습니다.
- 컴포넌트끼리 똑같은 상태를 공유해야 할 때도 여러 컴포넌트를 거치지 않고 손쉽게 상태 값을 전달하거나 업데이트 할 수 있습니다.

## 개념 미리 정리하기

### 액션
- 상태에 어떤 변화가 필요하면 액션(action)이란 것이 발생합니다.
- 하나의 객체로 표현되며, 액션 객체는 다름과 같은 형식으로 이뤄져 있습니다.

```javascript
{
    type: 'TOGGLE_VALUE'
}
```

- 액션 객체는 type 필드를 반드시 가지고 있어야 합니다. 이 값은 액션의 이름 입니다.
- 그 외의 값들은 나중에 상태 업데이트 할 때 참고해야 할 값이며, 작성자 마음대로 넣을 수 있습니다.

```javascript
{
    type: 'ADD_TODO',
    data: {
        id: 1, 
        text: '리덕스 배우기'
    }
}

{
    type: 'CHANGE_INPUT',
    text: '안녕하세요'
}
```

### 액션 생성 함수(action creator)

> 액션 객체를 만들어 주는 함수

```javascript
function addTodo(data) {
    return {
        type: 'ADD_TODO',
        data
    };
}
```

```javascript
const changeInput = text => ({
    type: 'CHANGE_INPUT',
    text
});
```

> 어떤 변화를 일으켜야 할 때마다 액션 객체를 만들어야 하는데 매번 액션 객체를 직접 작성하기 번거로울 수 있고, 만드는 과정에서 실수로 정보를 놓칠 수도 있습니다. 이러한 일을 방지하기 위해 이를 함수로 만들어서 관리합니다.

### 리듀서

- 리듀서(reducer)는 변화를 일으키는 함수입니다.
- 액션을 만들어 발생시키면 리듀서가 현재 상태와 전달받은 액션 객체를 파라미터로 받아오고 두 값을 참고하여 새로운 상태를 만들어서 반환해 줍니다.


```javascript
const initialState = {
    counter: 1
};

function reducer(state = initialState, action) {
    switch (action.type) {
        case INCREMENT:
            return {
                counter: state.counter + 1
            };
        default:
            return state;
    }
}
```

### 스토어

- 프로젝트에 리덕스를 적용하기 위해 스토어(store)를 만듭니다. 
- 한 개의 프로젝트는 단 하나의 스토어만 가질 수 있습니다.
- 스토어 안에는 현재 애플리케이션 상태와 리듀서가 들어가 있으며, 그 외에도 몇 가지 중요한 내장 함수를 지닙니다.

### 디스패치

- 디스패치(dispatch)는 스토어의 내장 함수 중 하나입니다.
- 디스패치는 **액션을 발생시키는 것** 이라고 이해하면 됩니다.
- 이 함수는 dispatch(action)과 같은 형태로 액션 객체를 파라미터로 넣어서 호출합니다.
- 이 함수가 호출되면 스토어는 리듀서 함수를 실행시켜서 새로운 상태를 만들어 줍니다.

### 구독

- 구독(subscribe)도 스토어의 내장 함수 중 하나입니다.
- subscribe 함수 안에 리스너 함수를 파라미터로 넣어서 호출해 주면, 액션이 디스패치되어 상태가 업데이트될 때마다 이 리스너 함수가 호출됩니다.

```javascript
const listener = () => {
    console.log('상태가 업데이트됨');
};
const unsubscribe = store.subscribe(listener);

unsubscribe(); // 추후 구독을 비활성화할 때 함수를 호출
```

## 리액트 없이 쓰는 리덕스

- 리덕스는 리액트에 종속되는 라이브러리가 아닙니다.
- 리액트에서 사용하려고 만들어졌지만 다른 UI 라이브러리/프레임워크와 함께 사용할 수도 있습니다.

### Parcel로 프로젝트 만들기

> 이 도구를 사용하면 아주 쉽게 웹프로젝트의 서버를 구성할 수 있습니다.

```
$ yarn global add parcel-bundler

$ mkdir vanilla-redux
$ cd vanilla-redux
$ yarn init -y
```

#### index.html

```html
<html>
    <body>
        <div>바닐라 자바스크립트</div>
        <script src="./index.js"></script>
    </body>
</html>
```

#### index.js

```javascript
console.log('hello parcel');
```

- 다 작성한 후에 다음 명령어를 실행하면 개발용 서버가 실행됩니다.

```
$ parcel index.html
```

- 개발서버의 주소는 http://localhost:1234/ 이며, 파일을 저장할 때마다 자동으로 새로고침 됩니다.
- yarn을 사용하여 리덕스 모듈 설치

```
$ yarn add redux
```

### 간단한 UI 구성하기

#### index.css

```css
.toggle {
    border: 2px solid black;
    width: 64px;
    height: 64px;
    border-radius: 32px;
    box-sizing: border-box;
}

.toggle.active {
    background: yellow;
}
```

#### index.html

```javascript
<html>
    <head>
        <link rel="stylesheet" type="text/css" href="index.css">
    </head>
    <body>
        <div class="toggle"></div>
        <hr />
        <h1>0</h1>
        <button id="increase">+1</button>
        <button id="decrease">-1</button>
        <script src="./index.js"></script>
    </body>
</html>
```

### DOM 레퍼런스 만들기

#### index.js

```javascript
const divToggle = document.querySelector('.toggle');
const counter = document.querySelector('h1');
const btnIncrease = document.querySelector('#increase');
const btnDecrease = document.querySelector('#decrease');
```

### 액션 타입과 액션 생성 함수 정의

- 프로젝트의 상태에 변화를 일으키는 것을 액션이라고 합니다.
- 액션 이름은 문자열 형태로 주로 대문자로 작성하며 액션 이름은 고유해야 합니다.
- 이름이 중복되면 의도하지 않은 결과가 발생할 수 있습니다.

#### index.js

```javascript
const divToggle = document.querySelector('.toggle');
const counter = document.querySelector('h1');
const btnIncrease = document.querySelector('#increase');
const btnDecrease = document.querySelector('#decrease');

const TOGGLE_SWITCH = 'TOGGLE_SWITCH';
const INCREASE = 'INCREASE';
const DECREASE = 'DECREASE';
```

- 이 액션 이름을 사용하여 액션 객체를 만드는 액션 생성 함수를 작성해 줍니다.
- 액션 객체는 type 값을 반드시 갖고 있어야 하며, 그 외에 추후 상태를 업데이트할 때 참고하고 싶은 값은 마음대로 넣을 수 있습니다.

#### index.js

```javascript
const divToggle = document.querySelector('.toggle');
const counter = document.querySelector('h1');
const btnIncrease = document.querySelector('#increase');
const btnDecrease = document.querySelector('#decrease');

const TOGGLE_SWITCH = 'TOGGLE_SWITCH';
const INCREASE = 'INCREASE';
const DECREASE = 'DECREASE';

const toggleSwitch = () => ({ type: TOGGLE_SWITCH });
const increase = difference => ({ type: INCREASE, difference });
const decrease = () => ({ type: DECREASE });
```

### 초기값 설정 

#### index.js

```javascript
const divToggle = document.querySelector('.toggle');
const counter = document.querySelector('h1');
const btnIncrease = document.querySelector('#increase');
const btnDecrease = document.querySelector('#decrease');

const TOGGLE_SWITCH = 'TOGGLE_SWITCH';
const INCREASE = 'INCREASE';
const DECREASE = 'DECREASE';

const toggleSwitch = () => ({ type: TOGGLE_SWITCH });
const increase = difference => ({ type: INCREASE, difference });
const decrease = () => ({ type: DECREASE });

const initialState = {
    toggle: false,
    counter: 0
};
```

### 리듀서 함수 정의

- 리듀서는 변화를 일으키는 함수입니다.
- 함수의 파라미터로 state, action 값을 받아 옵니다.

#### index.js

```javascript
const divToggle = document.querySelector('.toggle');
const counter = document.querySelector('h1');
const btnIncrease = document.querySelector('#increase');
const btnDecrease = document.querySelector('#decrease');

const TOGGLE_SWITCH = 'TOGGLE_SWITCH';
const INCREASE = 'INCREASE';
const DECREASE = 'DECREASE';

const toggleSwitch = () => ({ type: TOGGLE_SWITCH });
const increase = difference => ({ type: INCREASE, difference });
const decrease = () => ({ type: DECREASE });

const initialState = {
    toggle: false,
    counter: 0
};

// state가 undefined일 때는 initialState를 기본값으로 사용
function reducer(state = initialState, action) {
    // action.type에 따라 다른 작업을 처리함
    switch (action.type) {
        case TOGGLE_SWITCH:
            return {
                ...state, // 불변성 유지를 해 주어야 합니다.
                toggle: !state.toggle
            };
        case INCREASE:
            return {
                ...state,
                counter: state.counter + action.difference
            };
        case DECREASE:
            return {
                ...state,
                counter: state.counter - 1
            };
        default: 
            return state;
    }
}
```

- 리듀서에서는 상태의 불변성을 유지하면서 데이터의 변화를 일으켜 주어야 합니다. 이 작업을 할 때 spread 연산자(...)를 사용하면 편합니다. 
- 객체의 구조가 복잡해지면 spread 연산자로 불변성을 관리하여 업데이트하는 것이 번거로울 수 있고 코드의 가독성도 나빠지기 떄문에 리덕스의 상태는 최대한 깊지 않은 구조로 진행하는 것이 좋습니다.
- 객체의 구조가 복잡해지거나 배열도 함게 다루는 경우 immer 라이브러리를 사용하면 좀 더 쉽게 리듀서를 작성할 수 있습니다.

### 스토어 만들기

- 스토어를 만들 때는 createStore 함수를 사용합니다. 
- 이 함수를 사용하면 코드 상단에 import 구문을 넣어 리덕스에서 해당 함수를 불어와야 하고, 함수 파라미터에는 리듀서 함수를 넣어 주어야 합니다.

#### index.js

```javascript
import { createStore } from 'redux';

...

const store = createStore(reducer);
```

- 스토어를 생성했으므로 스토어 내장 함수를 사용할 수 있습니다.

### render 함수 만들기

- 이 함수는 상태가 업데이트될 때마다 호출되며, 리액트의 render 함수와는 다르게 이미 html을 사용하여 만들어진 UI 속성을 상태에 따라 변경해 줍니다.

#### index.js

```javascript
...

const store = createStore(reducer);

const render = () => {
    const state = store.getState(); // 현재 상태를 불러옵니다.

    // 토클 처리
    if (state.toggle) {
        divToggle.classList.add('active');
    } else {
        divToggle.classList.remove('active');
    }

    // 카운터 처리
    counter.innerText = state.counter;
};

render();
```

## 구독하기 

- 스토어의 상태가 바뀔 때마다 render() 함수가 호출되도록 처리합니다. 이 작업은 스토어의 내장 함수 subscribe를 사용하여 수행할 수 있습니다.
- 추후 리액트 프로젝트에서 리덕스를 사용할 때는 이 함수를 직접 사용하지 않습니다. 컴포넌트에서 리덕스 상태 조회하는 과정에서 react-redux라는 라이브러리가 이 작업을 대신해 줍니다.


#### index.js 

```javascript
...

const render = () => {
    const state = store.getState(); // 현재 상태를 불러옵니다.
    // 토클 처리
    if (state.toggle) {
        divToggle.classList.add('active');
    } else {
        divToggle.classList.remove('active');
    }
    // 카운터 처리
    counter.innerText = state.counter;
};

render();
store.subscibe(render);
```

### 액션 발생시키기

#### index.js

```javascript
...

divToggle.onclick = () => {
    store.dispatch(toggleSwitch());
};
btnIncrease.onclick = () => {
    store.dispatch(increase(1));
};

btnDecrease.onclick = () => {
    store.dispatch(decrease());
};
```

## 리덕스의 세 가지 규칙 

### 단일 스토어

- 하나의 애플리케이션 안에는 하나의 스토어가 들어가 있습니다.
- 여러개의 스토어를 사용하는 것이 완전히 불가능하지는 않습니다. 특정 업데이트가 너무 빈번하게 일어나거나 애플리케이션의 특정 부분을 완전히 분리시킬 때 여러 개의 스토어를 만들수도 있지만, 상태 관리가 복잡해질 수 있으므로 권장하지 않습니다.

### 읽기 전용 상태

- 리덕스 상태는 읽기 전용입니다. 리덕스도 상태를 업데이트할 때 기존의 객체는 건드리지 않고 새로운 객체를 생성해 주어야 합니다.
- 리덕스에서 불변성을 유지해야 하는 이유는 내부적으로 데이터가 변경되는 것을 감지하기 위해 얕은 비교(shallow equality) 검사를 하기 때문입니다. 
- 객체 변화를 감지할 때 객체의 깊숙한 안쪽까지 비교하는 것이 아니라 겉핥기 식으로 비교하여 좋은 성능을 유지할 수 있는 것 입니다.

### 리듀서는 순수한 함수

- 변화를 일으키는 리듀서 함수는 순수한 함수여야 합니다.
- 순수한 함수는 다음 조건을 만족합니다.
    - 리듀서 함수는 이전 상태와 액션 객체를 파라미터로 받습니다.
    - 파라미터 외의 값에는 의존하면 안 됩니다.
    - 이전 상태는 절대로 건드리지 않고, 변화를 준 새로운 상태 객체를 만들어서 반환합니다.
    - 똑같은 파라미터로 호출된 리듀서 함수는 언제나 똑같은 결과 값을 반환해야 합니다.