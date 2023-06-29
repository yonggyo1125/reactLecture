# 컴포넌트 

## 클래스형 컴포넌트 

* 함수형 컴포넌트 

```javascript
import './App.css';

function App() {
	const name = '리액트';
	return <div className="react">{name}</div>
}

export default App;
```

* 클래스형 컴포넌트 

```javascript
import { Component } from 'react';

class App extends Component {
	render() {
		const name = 'react';
		return <div className="react">{name}</div>;
	}
}

export default App;
```

## 첫 컴포넌트 생성 

* 코드 작성하기 

#### MyComponent.js

```javascript
const MyComponent = () => {
	return <div>나의 새롭고 멋진 컴포넌트</div>;
};

export default MyComponent;
```

* 모듈 내보내기(export)

```javascript 
export default MyComponent;
```

* 모듈 불러오기(import)

#### App.js

```javascript 
import MyComponent from './MyComponent';

const App = () => {
	return <MyComponent />;
};

export default App;
```

## props 

- props는 properties를 줄인 표현으로 컴포넌트 속성을 설정할 때 사용하는 요소입니다. 
- props 값은 해당 컴포넌트를 불러와 사용하는 부모 컴포넌트에서 설정할 수 있습니다.

* JSX 내부에서 props 렌더링

```javascript 
const MyComponent = props => {
	return <div>안녕하세요. 제 이름은 {props.name}입니다.</div>;
};

export default MyComponent;
```

* 컴포넌트를 사용할 때 props 값 지정하기 

#### App.js

```javascript
import MyComponent from './MyComponent';

const App = () => {
	return <MyComponent name="React" />;
};

export default App;
```

* props 기본값 설정: defaultProps

#### MyComponent.js

```javascript
const MyComponent = props => {
	return <div>안녕하세요. 제 이름은 {props.name}입니다.</div>;
};

MyComponent.defaultProps = {
	name: '기본 이름'
};

export default MyComponent;
```

* 태그 사이의 내용을 보여 주는 children

