# ref: DOM에 이름 달기
> 리액트 컴포넌트 안에서도 id를 사용할 수는 있습니다. JSX 안에서 DOM에 id를 달면 해당 DOM을 렌더링 할때 그대로 전달합니다. 하지만 큭수한 경우가 아니면 사용을 권장하지 않습니다. 예를 들어 같은 컴포넌트를 여러 번 사용한다고 가정해 보세요. HTML에서 DOM의 id는 유일(unique)해야 하는데, 어떤 상황에서는 중복 id를 가진 DOM이 여러개 생기니 잘못된 사용됩니다.<br>ref는 전역적으로 작동하지 않고 컴포넌트 내부에서만 적용하기 때문에 이런 문제가 생기지 않습니다.<br>대부분은 id를 사용하지 않고도 원하는 기능을 구현할 수 있지만, 다른 라이브러리나 프레임워크와 함께 id를 사용해야 하는 상황이 발생할 수 있습니다. 이런 상황에서는 컴포넌트를 만들 때마다 id 뒷부분에 추가 텍스트를 붙여서 중복 id가 발생하는 것을 방지해야 합니다.

##  ref는 어떤 상황에서 사용해야 할까?

- DOM을 꼭 직접적으로 건드려야 할 때

* 예제 컴포넌트 생성

#### ValidationSample.css

```css
.success {
	background-color: lightgreen;
}

.failure {
	background-color: lightcoral;
}
```

#### ValidationSample.js

```javascript
import { Component } from 'react';
import './ValidationSample.css';

class ValidationSample extends Component {
	state = {
		password: '',
		clicked: false,
		validated: false
	}
	
	handleChange = (e) => {
		this.setState({
			password: e.target.value
		});
	}
	
	handleButtonClick = () => {
		this.setState({
			clicked: true,
			validated: this.state.password === '0000'
		});
	}
	
	render() {
		return (
			<div>
				<input
					type="password"
					value={this.state.password}
					onChange={this.handleChange}
					className={this.state.clicked ? (this.state.validated ? 'success' : 'failture') : '' }
					/>
					<button onClick={this.handleButtonClick}>검증하기</button>
			</div>
		);
	}
}

export default ValidationSample;
```

#### App.js

```javascript
import { Component } from 'react';
import ValidationSample from './ValidationSample';

class App extends Component {
	render() {
		return (
			<ValidationSample />
		);
	}
}

export default App;
```

* DOM을 꼭 사용해야 하는 상황

- 특정 input에 포커스 주기
- 스크롤 박스 조작하기
- Canvas 요소에 그림 그리기

> 이때는 어쩔 수 없이 DOM에 직접적으로 접근해야 하는데, 이를 위해 바로 ref를 사용합니다.

## ref 사용

* 콜백 함수를 통한 ref 설정 

- ref를 달고자 하는 요소에 ref라는 콜백 함수를 props로 전달해 주면 됩니다. 
- 이 콜백 함수는 ref 값을 파라미터로 전달받습니다. 
- 함수 내부에서 파라미터로 받은 ref를 컴포넌트의 멤버 변수로 설정해 줍니다.

```html
<input ref={(ref) => {this.input=ref}} />
``` 

> 이렇게 하면 앞으로 this.input은 input 요소의 DOM을 가리킵니다. ref의 이름은 원하는 것으로 자유롭게 지정할 수 있습니다. DOM 타입과 관계없이 this.superman = ref처럼 마음대로 지정합니다.

* createRef를 통한 ref 설정 

```javascript
import { Component } from 'react';

class RefSample extends Component {
	input = React.createRef();
	
	handleFocus = () => {
		this.input.current.focus();
	}
	
	render() {
		return (
			<div>
				<input ref={this.input} />
			</div>
		);
	}
}

export default RefSample;
```

> createRef를 사용하여 ref를 만드려면 우선 컴포넌트 내부에서 멤버 변수로 React.createRef()를 담아 주어야 합니다. 그리고 해당 멤버 변수를 ref를 담고자 하는 요소에 ref props로 넣어 주면 ref 설정이 완료됩니다. 설정한 뒤 나중에 ref를 설정해 준 DOM에 접근하려면 this.input.current를 조회하면 됩니다.

* input에 ref 달기

```javascript
...
<input ref={(ref) => this.input=ref}
	...
/>
```

* 버튼 onClick 이벤트 코드 수정

#### ValidationSample.js - handleButtonClick 메서드

```javascript
handleButtonClick = () => {
	this.setState({
		clicked: true,
		validated: this.state.password === '0000'
	});
	this.input.focus();
}
```

## 컴포넌트에 ref 달기

> 리액트에서는 컴포넌트에도 ref를 달 수 있습니다. 이 방법은 주로 컴포넌트 내부에 있는 DOM을 컴포넌트 외부에서 사용할 때 씁니다. 컴포넌트에 ref를 다는 방법은 DOM에 ref를 다는 방법과 같습니다.

* 사용법

```javascript
<MyComponent 
	ref={(ref) => {this.myComponent=ref}}
/>
```

> 이렇게 하면 MyComponent 내부의 메서드 및 멤버 변수에도 접근할 수 있습니다. 즉, 내부의 ref에도 접근할 수 있습니다.<br>(예: myComponent.handleClick, myComponent.input 등).

* 컴포넌트 초기 설정 

> 스크롤 박스가 있는 컴포넌트를 하나 만들고, 스크롤바를 아래로 내리는 작업을 부모 컴포넌트에서 실행하는 예

* 컴포넌트 파일 생성

#### ScrollBox.js

```javascript
import { Component } from 'react';

class ScrollBox extends Component {
	render() {
		const style = {
			border: '1px solid black',
			height: '300px',
			width: '300px',
			overflow: 'auto',
			position: 'relative'
		};
		
		const innerStyle = {
			width: '100%',
			height: '650px',
			background: 'linear-gradient(white, black)'
		};
		
		return (
			<div
				style={style}
				ref={(ref) => {this.box=ref}}>
				<div style={innerStyle} />
			</div>
		);
	}
}

export default ScrollBox;
```

* App 컴포넌트에서 스크롤 박시 컴포넌트 렌더링

#### App.js

```javascript
import { Component } from 'react';
import ScrollBox from './ScrollBox';

class App extends Component {
	render() {
		return (
			<div>
				<ScrollBox />
			</div>
		);
	}
}

export default App;
```

* 컴포넌트에 메서드 생성 

#### ScrollBar.js

```javascript
import { Component } from 'react';

class ScrollBox extends Component {
	
	scrollToBottom = () => {
		const { scrollHeight, clientHeight } = this.box;
		/* 앞 코드에서는 비구조화 할당 문법을 사용했습니다. 
			다음 코드와 같은 의미 입니다.
			const scrollHeight = this.box.scrollHeight;
			const clientheight = this.box.clientHeight;
		*/
	}
	
	render() {
		...
	}
}

export default ScrollBox;
```

> **scrollTop** : 세로 스크롤바 위치(0~350)<br>**scrollHeight**: 스크롤이 있는 박스 안의 div 높이(650)<br>**clientHeight**: 스크롤이 있는 박스의 높이(300)

> scrollToBottom 메서드의 첫 번째 줄에서는 ES6의 비구조화 할당 문법을 사용했습니다.<br>이렇게 만든 메서드는 부모 컴포넌트인 App 컴포넌트에서 ScrollBox에 ref를 달면 사용할 수 있습니다.

* 컴포넌트에 ref 달고 내부 메서드 사용

#### App.js

```javascript
import { Component } from 'react';
import ScrollBox from './ScrollBox';

class App extends Component {
	render() {
		return {
			<div>
				<ScrollBox ref={(ref) => this.scrollBox.ref} />
				<button onClick={() => this.scrollBox.scrollToBottom()}>
					맨 밑으로
				</button>	
			</div>
		};
	}
}

export default App;
```

> 문법상으로 onClick={this.scrollBox.scrollBottom} 같은 형식으로 작성해도 틀린 것은 아니나 처음 렌더링될 때는 this.scrollBox값이 undefined이므로 this.scrollBox.scrollToBottom 값을 읽어 오는 과정에서 오류가 발생합니다. 화살표 함수 문법을 사용하여 아예 새로운 함수를 만들고 그 내부에서 this.scrollBox.scrollBottom 메서드를 실행하면, 버튼을 누를 때(이미 한 번 렌더링을 해서 this.scrollBox를 설정한 시점) this.scrollBox.scrollBottom 값을 읽어 와서 실행하므로 오류가 발생하지 않습니다.

> 함수형 컴포넌트에서 ref를 사용하려면 useRef라는 Hook 함수를 사용합니다. 사용법은 React.createRef와 유사합니다.