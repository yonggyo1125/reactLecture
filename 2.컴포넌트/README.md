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

#### App.js

```javascript
import MyComponent from './MyComponent';

const App = () => {
	return <MyComponent>리액트</MyComponent>;
};

export default App;
```

#### MyComponent.js

```javascript
const MyComponent = props => {
	return (
		<div>
			안녕하세요. 제 이름은 {props.name}입니다. <br />
			children 값은 {props.children}입니다.
		</div>
	);
};

MyComponent.defaultProps = {
	name: '기본 이름'
};

export default MyComponent;
```

* 비구조화 할당 문법을 통해 props 내부 값 추출하기

#### MyComponent.js

```javascript
const MyComponent = props => {
	const { name. children } = props;
	return (
		<div>
			안녕하세요. 제 이름은 {name}입니다. <br />
			children 값은 {children}입니다.
		</div>
	);
};

MyComponent.defaultProps = {
	name: '기본 이름'
};

export default MyComponent
```

* 함수 파라미터가 객체라면 그 값을 바로 비구조화해서 사용할 수 있습니다.

```javascript
const MyComponent = ({ name, children }) => {
	return (
		<div>
			안녕하세요. 제 이름은 {name}입니다. <br />
			children 값은 {children}입니다.
		</div>
	);
};

MyComponent.defaultProps = {
	name: '기본 이름'
};

export default MyComponent
```

* propTypes를 통한 props 검증 

#### MyComponent.js 

```javascript
import PropTypes from 'prop-types';

const MyComponent = ({ name, children }) => { 
	...
}

MyComponent.defaultProps = {
	name: '기본 이름'
};

MyComponent.propTypes = {
	name: PropTypes.string
};

export default MyComponent;
```

#### App.js

```javascript
import MyComponent from './MyComponent';

const App = () => {
	return <MyComponent name={3}>리액트</MyComponent>
};

export default App;
```

> 컴포넌트에 설정한 props가 propTypes에서 지정한 형태와 일치하지 않는다면 브라우저 개발자 도구의 Console 탭에 경고 메세지를 출력하여 개발자에게 propTypes가 잘못되었다는 것을 알려줍니다. 오류 메세지 확인하였다면 name 값을 제대로 입력해 주세요.

```javascript
import MyComponent from './MyComponent';

const App = () => {
	return <MyComponent name="React">리액트</MyComponent>
};

export default App;
``` 

* isRequired를 사용하여 필수 propTypes 설정

```javascript
import PropTypes from 'prop-types';

const MyComponent = ({ name, favoriteNumber, children }) => { 
	<div>
		안녕하세요. 제 이름은 {name}입니다. <br />
		children 값은 {children}입니다.
		<hr />
		제가 좋아하는 숫자는 {favoriteNumber}입니다.
	</div>
}

MyComponent.defaultProps = {
	name: '기본 이름'
};

MyComponent.propTypes = {
	name: PropTypes.string,
	favoriteNumber: PropTypes.number.isRequired
};

export default MyComponent;
```

> favoriteNumber를 설정하지 않았기 때문에 경고가 나타납니다. 경고를 확인했다면 MyComponent에 favoriteNumber 값을 제대로 전달하세요.

```javascript
import MyComponent from './MyComponent';

const App = () => {
	return <MyComponent name="React" favoriteNumber={1}>리액트</MyComponent>
};

export default App;
``` 

* PropTypes 종류

|종류|설명|
|---|----|
|array|배열|
|arrayOf(다른 PropType)|특정 PropType으로 이루어진 배열을 의미합니다.<br>예를 들어 arrayOf(PropTypes.number)는 숫자로 이루어진 배열입니다.|
|bool|true 혹은 false 값|
|func|함수|
|number|숫자|
|object|객체|
|string|문자열|
|symbol|ES6의 Symbol|
|node|렌더링할 수 있는 모든 것<br>(숫자, 문자열, 혹은 JSX 코드, children도 node PropType입니다.)|
|instanceOf(클래스)|특정 클래스의 인스턴스(예: instanceOf(MyClass))|
|oneOf(['dog', 'cat'])|주어진 배열 요소 중 하나|
|oneOfType([React.PropTypes.string, PropTypes.number])|주어진 배열 안의 종류 중 하나|
|objectOf(React.PropTypes.number)|객체의 모든 키 값이 인자로 주어진 PropType인 객체|
|shape([name: PropTypes.string, num: PropTypes.number])|주어진 스키마를 가진 객체|
|any|아무 종류|

* 클래스형 컴포넌트에서 props 사용하기

> 클래스형 컴포넌트에서 props를 사용할 때는 render 함수에서 this.props를 조회하면 됩니다.<br>defaultProps와 propTypes는 똑같은 방식으로 설정할 수 있습니다.

#### MyComponent.js

```javascript 
import { Component } from 'react';
import PropTypes from 'prop-types';

class MyComponent extends Component {
	render() {
		const { name, favoriateNumber, children } = this.props;
		return (
			<div>
				안녕하세요. 제 이름은 {name}입니다. <br />
				children 값은 {children}입니다.
				<hr />
				제가 좋아하는 숫자는 {favoriteNumber}입니다.
			</div>
		);
	}
}

MyComponent.defaultProps = {
	name: '기본 이름'
};

MyComponent.propTypes = {
	name: PropTypes.string,
	favoriteNumber: PropTypes.number.isRequired
};

export default MyComponent;
```

#### MyComponent.js

```javascript 
import { Component } from 'react';
import PropTypes from 'prop-types';

class MyComponent extends Component {
	static defaultProps = {
		name: '기본 이름'
	};
	static propTypes = {
		name: PropTypes.string,
		favoriteNumber: PropTypes.number.isRequired
	};

	render() {
		const { name, favoriateNumber, children } = this.props;
		return (
			<div>
				안녕하세요. 제 이름은 {name}입니다. <br />
				children 값은 {children}입니다.
				<hr />
				제가 좋아하는 숫자는 {favoriteNumber}입니다.
			</div>
		);
	}
}

export default MyComponent;
```

## state

- 리액트에서 state는 컴포넌트 내부에서 바뀔 수 있는 값을 의미합니다.
- props는 컴포넌트가 사용되는 과정에서 부모 컴포넌트가 설정하는 값이며, 컴포넌트 자신은 해당 props를 읽기 전용으로 사용할 수 있습니다.

* 클래스형 컴포넌트의 state 

#### Counter.js

```javascript 
import { Component } from 'react';

class Counter extends Component {
	constructor(props) {
		super(props);

		this.state = {
			number: 0
		};
	}
	render() {
		const { number } = this.state;
		return (
			<div>
				<h1>{number}</h1>
				<button onClick{() => { 
					this.setState({ number : number + 1}); 
				}}>
				+1
				</button>
			</div>
		);
	}
}

export default Counter;
```

> this.setState를 사용하여 state에 새로운 값을 넣을 수 있습니다.

#### App.js

```javascript 
import Counter from './Counter';

const App = () => {
	return <Counter />;
};

export default App;
```

* state 객체 안에 여러 값이 있을 때

```javascript 
import { Component } from 'react';

class Counter extends Component {
	constructor(props) {
		super(props);

		this.state = {
			number: 0,
			fixedNumber: 0
		};
	}
	render() {
		const { number, fixedNumber } = this.state;
		return (
			<div>
				<h1>{number}</h1>
				<h2>바뀌지 않는 값 : {fixedNumber}</h2>
				<button onClick{() => { 
					this.setState({ number : number + 1}); 
				}}>
				+1
				</button>
			</div>
		);
	}
}

export default Counter;
```

* state를 constructor에서 꺼내기

#### Counter.js 

```javascript 
import { Component } from 'react';

class Counter extends Component {
	state = {
		number: 0,
		fixedNumber: 0
	};

	render() {
		const { number, fixedNumber } = this.state;
		return (
			<div>
				<h1>{number}</h1>
				<h2>바뀌지 않는 값 : {fixedNumber}</h2>
				<button onClick{() => { 
					this.setState({ number : number + 1}); 
				}}>
				+1
				</button>
			</div>
		);
	}
}

export default Counter;
```

* this.setState에 객체 대신 함수 인자 전달하기

#### Counter.js - button onClick

```javascript 
onClick{() => {
	this.setState({ number: number + 1});
	this.setState({ number: this.state.number + 1 });
}};
```

> this.setState를 두번 사용해도 버튼을 클릭할때 1씩 더해진다. this.setState를 사용한다고 해서 state값이 바로 바뀌지는 않기 때문입니다. 이에 대한 해결책은 this.setState를 사용할 때 객체 대신에 함수를 인자로 넣어 줄 수 있습니다.

```javascript 
this.setState((prevState, props) => {
	return {
		// 업데이트하고 싶은 내용
	}
});
```

#### Counter.js - button

```javascript 
<button 
	onClick={() => {
		this.setState(prevState => {
			return {
				number: prevState.number + 1
			};
		})
	}}
>
+1
</button>
```

```javascript 
<button 
	onClick={() => {
		this.setState(prevState => ({
				number: prevState.number + 1
		}));
	}}
>
+1
</button>
```

* this.setState가 끝난 후 특정 작업 실행하기

#### Counter.js - button

```javascript 
<button
	onClick{() => {
		this.setState({
			number: number + 1
		}, () => {
			console.log(this.state);
		});
	}}
>
+1
</button>
```

* 함수 컴포넌트에서 useState 사용하기

* useState 사용하기

#### Say.js

```javascript 
import { useState } from 'react';

const Say = () => {
	const [message, setMessage] = useState('');
	const onClickEnter = () => setMessage('안녕하세요');
	const onClickLeave = () => setMessage('안녕히 가세요');

	return (
		<div>
			<button onClick={onClickEnter}>입장</button>
			<button onClick={onClickLeave}>퇴장</button>
			<h1>{message}</h1> 
		</div>
	);
};

export default Say;
```

> useState 함수의 인자에는 상태의 최소값을 넣어 줍니다.

#### App.js

```javascript
import Say from './Say';

const App = () => {
	return <Say />;
};

export default App;
```

* 한 컴포넌트에서 useState 여러번 사용하기

#### Say.js

```javascript 
import { useState } from 'react';

const Say = () => {
	const [message, setMessage] = useState('');
	const onClickEnter = () => setMessage('안녕하세요!');
	const onClickLeave = () => setMessage('안녕히 가세요!');

	const [color, setColor] = useState('black');

	return (
		<div>
			<button onClick={onClickEnter}>입장</button>
			<button onClick={onClickLeave}>퇴장</button>
			<h1 style={{ color }}>{message}</h1>
			<button style={{ color: 'red' }} onClick={() => setColor('red')}>
				빨간색
			</button>
			<button style={{ color: 'green'}} onClick={() => setColor('green')}>
				초록색
			</button>
			<button style={{ color: 'blue' }} onClick={() => setColor('blue')}>
				파란색
			</button>
		</div>
	);
};

export default Say;
```

## state를 사용할 때 주의사항 

- 클래스 컴포넌트든 함수 컴포넌트든 state값을 바꾸어야 할 때는 setState 혹은 useState를 통해 전달받은 세터 함수를 사용해야 합니다.
- 배열이나 객체 사본을 만들고 그 사본에 값을 업데이트한 후, 그 사본의 상태를 setState 혹은 세터 함수를 통해 업데이트합니다.

#### 예시

```javascript 
// 객체 다루기
const object = { a: 1, b: 2, c: 3 };
const newObject = { ...object, b: 2 }; // 사본을 만들어서 값만 덮어 쓰기 

// 배열 다루기
const array = [
	{ id: 1, value: true },
	{ id: 2, value: true },
	{ id: 3, value: false }
];

let nextArray = array.concat({ id: 4 }); // 새 항목 추가 
nextArray.filter(item => item.id !== 2); // id가 2인 항목 제거 
nextArray.map(item => (item.id === 1 ? { ...item, value: false } : item )); // id가 1인 항목의 value를 false로 설정 
```

> 객체에 대한 사본을 만들 때는 spread 연산자라 불리는 ...을 사용하여 처리하고 배열에 대한 사본을 만들 때는 배열의 내장 함수들을 활용합니다.