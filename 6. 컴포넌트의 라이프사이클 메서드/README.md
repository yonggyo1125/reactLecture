# 컴포넌트의 라이프사이클 메서드

## 라이프사이클 메서드의 이해
- 라이프사이클 메서드의 종류는 총 아홉 가지 입니다.
- Will 접두사가 붙은 메서드는 어떤 작업을 작동하기 전에 실행되는 메서드
- Did 접두사가 붙은 메서드는 어떤 작업을 작동한 후에 실행되는 메서드 입니다.
- 이 메서드들은 컴포넌트 클래스에서 덮어 써 선언함으로써 사용될 수 있습니다.
- 라이프 사이클은 **마운트, 업데이트, 언마운트** 카테고리로 나눕니다.

### 마운트

- DOM이 생성되고 웹 브라우저상에 나타나는 것을 마운트(mount)라고 합니다. 이때 호출하는 메서드는 다음과 같습니다.
- 마운트할 때 호출하는 메서드
    - 1 컴포넌트 만들기
    - 2 **constructor** : 컴포넌트를 새로 만들 때마다 호출되는 클래스 생성자 메서드입니다.
    - 3 **getDerivedStateFromProps** : props에 있는 값을 state에 넣을 때 사용하는 메서드 입니다.
    - 4 **render** : UI를 렌더링 하는 메서드입니다.
    - 5 **componentDidMount** : 컴포넌트가 웹 브라우저상에 나타난 후 호출하는 메서드 입니다.


### 업데이트 

- 컴포넌트는 다음과 같은 총 네 가지 경우에 업데이트 합니다.
    - 1 props가 바뀔 때
    - 2 state가 바뀔 때
    - 3 부모 컴포넌트가 리렌더링될 때
    - 4 this.forceUpdate로 강제로 렌더링을 트리거할 때

- 컴포넌트는 다양한 이유로 업데이트 될 수 있습니다.
    - 부모 컴포넌트에서 넘겨주는 props가 바뀔때
	- 컴포넌트 자신이 들고 있는 state가 setState를 통해 업데이트될 때
	- 부모 컴포넌트가 리렌더링될 때
	
- 컴포넌트를 업데이트할 때는 다음 메서드를 호출합니다.
	- 1 **getDerivedStateFromProps** 
		- 이 메서드는 마운트 과정에서도 호출되며, 업데이트가 시작되기 전에도 호출됩니다. 
		- props의 변화에 따라 state 값에도 변화를 주고 싶을 때 사용합니다.
	- 2 **shouldComponentUpdate** 
		- 컴포넌트가 리렌더링을 해야 할지 말아야 할지를 결정하는 메서드입니다.
		- 이 메서드에서는 true 혹은 false 값을 반환해야 하며, true를 반환하면 다음 라이프사이클 메서드를 계속 실행하고, false를 반환하면 작업을 중지합니다. 즉, 컴포넌트가 리렌더링되지 않습니다.
		- 만약 특정 함수에서 this.forceUpdate() 함수를 호출한다면 이 과정을 생략하고 바로 render 함수를 호출합니다.
	- 3 **render** : 컴포넌트를 리렌더링합니다.
	- 4 **getSnapshotBeforeUpdate** : 컴포넌트 변화를 DOM에 반영하기 바로 직전에 호출하는 메서드입니다.
	- 5 **componentDidUpdate** : 컴포넌트의 업데이트 작업이 끝난 후 호출하는 메서드입니다.
	
### 언마운트 

> 마운트의 반대 과정, 즉 컴포넌트를 DOM에서 제거하는 것을 언마운트(unmount)라고 합니다.

- **componentWillUnmount** : 컴포넌트가 웹 브라우저에서 사라지기 전에 호출하는 메서드 입니다.

## 라이프사이클 메서드 살펴보기 

### render() 함수

```javascript
render() { ... }
```

- 컴포넌트의 모양새를 정의합니다. 라이프사이클 메서드 중 유일한 필수 메서드입니다.
- 이 메서드 안에서 this.props와 this.state에 접근할 수 있으며, 리액트 요소를 반환합니다. 
- 요소는 div 같은 태그가 될 수도 있고, 따로 선언한 컴포넌트가 될 수도 있습니다.
- 아무것도 보여 주고 싶지 않다면 null 값이나 false 값을 반환하도록 합니다.
- 이 메서드 안에서는 이벤트 설정이 아닌 곳에서 setState를 사용하면 안 되며, 브라우저의 DOM에 접근해서도 안 됩니다. DOM 정보를 가져오거나 state에 변화를 줄 때는 **componentDidMount**에서 처리해야 합니다.

### constructor 메서드

```javascript
constructor(props) { ... }
```

- 컴포넌트의 생성자 메서드로 컴포넌트를 만들 때 처음으로 실행됩니다. 이 메서드에서는 초기 state를 정할 수 있습니다.

### getDerivedStateFromProps 메서드 

- props로 받아온 값을 state에 동기화시키는 용도로 사용하며, 컴포넌트가 마운트될 때와 업데이트될 때 호출됩니다.

```javascript
static getDerivedStateFromProps(nextProps, prevState) {
	if (nextProps.value !== prevState.value) { // 조건에 따라 특정 값 동기화
		return { value: nextProps.value };
	}
	
	return null; // state를 변경할 필요가 없다면 null을 반환 
}
```

### componentDidMount 메서드

```javascript
componentDidMount() { ... }
```

- 컴포넌트를 만들고 첫 렌더링을 다 마친 후 실행합니다. 
- 이 안에서 다른 자바스크립트 라이브러리 또는 프레임워크의 함수를 호출하거나 이벤트 등록, setTimeout, setInterval, 네트워크 요청 같은 비동기 작업을 처리하면 됩니다.

### shouldComponentUpdate 메서드

```javascript
shouldComponentUpdate(nextProps, nextState) { ... }
```

- props 또는 state를 변경했을 때, 리렌더링을 시작할지 여부를 지정하는 메서드입니다.
- 이 메서드에서는 반드시 true 값 또는 false 값을 반환해야 합니다. 
- 컴포넌트를 만들 때 이 메서드를 따로 생성하지 않으면 기본적으로 언제나 true 값을 반환합니다. 이 메서드가 false 값을 반환한다면 업데이트 과정은 여기서 중지됩니다.
- 이 메서드 안에서 현재 props와 state는 this.props와 this.state로 접근하고, 새로 설정될 props또는 state는 nextProps와 nextState로 접근할 수 있습니다.
- 프로젝트 성능을 최적화할 때, 상황에 맞는 알고리즘을 작성하여 리렌더링을 방지할 때는 false값을 반환하게 합니다.

### getSnapshotBeforeUpdate 메서드 

- render에서 만들어진 결과물이 브라우저에 실제로 반영되기 직전에 호출됩니다. 
- 이 메서드에서 반환하는 값은 componentDidUpdate에서 세 번째 파라미터인 snapshot 값으로 전달받을 수 있는데, 주로 업데이트하기 직전의 값을 참고할 일이 있을 때 활용됩니다.

```javascript
getSnapshotBeforeUpdate(prevProps, prevState) {
	if (prevState.array !== this.state.array) {
		const { scrollTop, scrollHeight } = this.list
		
		return { scrollTop, scrollHeight };
	}
}
```

### componentDidUpdate 메서드

```javascript
componentDidUpdate(prevProps, prevState, snapshot) { ... }
```

- 리렌더링을 완료한 후 실행합니다. 
- 업데이트가 끝난 직후이므로 DOM 관련 처리를 해도 무방합니다.
- prevProps 또는 prevState를 사용하여 컴포넌트가 이전에 가졌던 데이터에 접근할 수 있습니다. 
- getSnapshotBeforeUpdate에서 반환한 값이 있다면 snapshot 값을 전달받을 수 있습니다.

### componentWillUnmount 메서드

```javascript
componentWillUnmount() { ... }
```

- 컴포넌트를 DOM에서 제거할 때 실행합니다.
- componentDidMount에서 등록한 이벤트 , 타이머, 직접 생성한 DOM이 있다면 여기서 제거 작업을 해야 합니다.

### componentDidCatch 메서드

- 컴포넌트 렌더링 도중에 에러가 발생했을 때 애플리케이션이 먹통이 되지 않고 오류 UI를 보여 줄 수 있게 해 줍니다. 

```javascript
componentDidCatch(error, info) {
	this.setState({
		error: true
	});
	console.log({ error, info });
}
```
- error는 파라미터에 어떤 에러가 발생했는지 알려줍니다. 
- info 파라미터는 어디에 있는 코드에서 오류가 발생했는지에 대한 정보를 줍니다.
- 이 메서드를 사용할 때는 컴포넌트 자신에게 발생하는 에러를 잡아낼 수 없고 자신의 this.props.children으로 전달되는 컴포넌트에서 발생하는 에러만 잡아낼 수 있습니다.

## 라이프사이클 메서드 사용하기

#### LifeCycleSample.js 

```javascript
import { Component } from 'react';

class LifeCycleSample extends Component {
	state = {
		number: 0,
		color: null,
	}
	
	myRef = null; // ref를 설정할 부분
	
	constructor(props) {
		super(props);
		console.log('constructor');
	}
	
	static getDerivedStateFromProps(nextProps, prevState) {
		console.log('getDerivedStateFromProps');
		if (nextProps.color !== prevProps.color) {
			return { color: nextProps.color};
		}
		
		return null;
	}
	
	componentDidMount() {
		console.log('componentDidMount');
	}
	
	shouldComponentUpdate(nextProps, nextState) {
		console.log('shouldComponentUpdate', nextProps, nextState);
		// 숫자가 마지막 자리가 4면 리렌더링하지 않습니다.
		return nextState.number % 10 !== 4;
	}
	
	componentWillUnmount() {
		console.log('componentWillUnmount');
	}
	
	handleClick = () => {
		this.setState({
			number: this.state.number + 1
		});
	}
	
	getSnapshotBeforeUpdate(prevProps, prevState) {
		console.log('getSnapshotBeforeUpdate');
		if (prevProps.color !== this.props.color) {
			return this.myRef.style.color;
		}
		return null;
	}
	
	componentDidUpdate(prevProps, prevState, snapshot) {
		console.log('componentDidUpdate', prevProps, prevState);
		if (snapshot) {
			console.log('업데이트되기 직전 색상: ', snapshot);
		}
	}
	
	render() {
		console.log('render');
		
		const style = {
			color: this.props.color
		};
		
		return (
			<div>
				<h1 style={style} ref={ref => this.myRef=ref}>
					{this.state.number}
				</h1>
				<p>color: {this.state.color}</p>
				<button onClick={this.handleClick}>
					더하기
				</button>
			</div>
		);	
	}
}

export default LifeCycleSample;
```

- 이 컴포넌트는 각 라이프사이클 메서드를 실행할 때마다 콘솔 디버거에 기록하고, 부모 컴포넌트에서 props로 색상을 받아 버튼을 누르면 state.number 값을 1씩 더합니다.
- getDerivedStateFromProps는 부모에게서 받은 color 값을 state에 동기화하고 있습니다.
- getSnapshotBeforeUpdate는 DOM에 변화가 일어나기 직전의 색상 속성을 snapshot 값으로 반환하며 이것을 componentDidUpdate에서 조회할 수 있게 했습니다.
- shouldComponentUpdate 메서드에서 state.number 값의 마지막 자리 수가 4이면 리렌더링을 취소하도록 설정했습니다.

### App  컴포넌트에서 예제 컴포넌트 사용

#### App.js

```javascript
import { Component } from 'react';
import LifeCycleSample from './LifeCycleSample';

// 랜덤 색상을 생성합니다.
function getRandomColor() {
	return '#' + Math.floor(Math.random() + 16777215).toString(16);
}

class App extends Component {
	state = {
		color: '#000000'
	}
	
	handleClick = () => {
		this.setState({
			color: getRandomColor()
		});
	}
	
	render() {
		return (
			<div>
				<button onClick={this.handleClick}>랜덤 색상</button>
				<LifeCycleSample color={this.state.color} />
			</div>
		);
	}
}

export default App;
```

> getRandomColor 함수는 state의 color 값을 랜덤 색상으로 설정합니다. 16777215를 hex로 표현하면 ffffff가 되므로 해당 코드는 000000부터 ffffff 값을 반환합니다.

> **React.StrictMode 적용**<br>React.StrictMode가 적용되어 있으면 일부 라이프사이클이 두 번씩 호출됩니다. 개발 환경에서만 두 번씩 호출되며 프로덕션 환경에서는 정상적으로 호출됩니다. 

### 에러 잡아내기

- LifeCycleSample 컴포넌트의 render 함수에서 의도적으로 에러를 한번 발생킵니다. 

#### LifeCycleSample.js

```javascript
render() {
	console.log('render');
	
	const style = {
		color: this.props.color
	};
	
	return (
		<div>
			{this.props.missing.value}
			<h1 style={style} ref={ref => (this.myRef = ref)}>
				{this.state.number}
			</h1>
			<p>color: {this.state.color}</p>
			<button onClick={this.handleClick}>더하기</button>
		</div>
	);
}
```

#### ErrorBoundary.js 

```javascript
import { Component } from 'react';

class ErrorBoundary extends Component {
	state = {
		error: false
	};
	componentDidCatch(error, info) {
		this.setState({
			error: true
		});
		console.log({ error, info });
	}
	render() {
		if (this.state.error) return <div>에러가 발생했습니다.</div>;
		return this.props.children;
	}
}

export default ErrorBoundary;
```

- 에러가 발생하면 componentDidCatch 메서드가 호출되며, 이 메서드는 this.state.error 값을 true로 업데이트해 줍니다.
- render 함수는 this.state.error 값이 true라면 에러가 발생했음을 알려 주는 문구를 보여 줍니다.
- 이 컴포넌트를 사용하여 App.js에서 LifeCycleSample 컴포넌트를 감싸 줍니다.

#### App.js

```javascript
import { Component } from 'react';
import LifeCycleSample from './LifeCycleSample';
import ErrorBoundary from './ErrorBoundary';

// 랜덤 색상을 생성합니다.
function getRandomColor() {
	return '#' + Math.floor(Math.random() + 16777215).toString(16);
}

class App extends Component {
	state = {
		color: '#000000'
	};
	
	handleClick = () => {
		this.setState({
			color: getRandomColor()
		});
	};
	
	render() {
		return (
			<div>
				<button onClick={this.handleClick}>랜덤 색상</button>
				<ErrorBoundary>	
					<LifeCycleSample color={this.state.color} />
				</ErrorBoundary>
			</div>
		);
	}
}

export default App;
```


#### 컴포넌트의 라이프사이클 메서드 흐름 

![컴포넌트 라이프사이클 메서드 흐름](https://raw.githubusercontent.com/yonggyo1125/reactLecture/master/6.%20%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EC%9D%98%20%EB%9D%BC%EC%9D%B4%ED%94%84%EC%82%AC%EC%9D%B4%ED%81%B4%20%EB%A9%94%EC%84%9C%EB%93%9C/images/1.png)

