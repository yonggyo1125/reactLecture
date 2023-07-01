# 이벤트 핸들링

## 리액트의 이벤트 시스템 

- 리액트의 이벤트 시스템은 웹 브라우저의 HTML 이벤트와 인터페이스가 동일하기 때문에 사용법이 비슷합니다.

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
            ...
        </div>
    );
};

export default Say;
```

* 이벤트를 사용할 때 주의사항 

- 이벤트 이름은 카멜표기법으로 작성
- 이벤트에 실행할 자바스크립트 코드를 전달하는 것이 아니라, 함수 형태의 값을 전달
- DOM 요소에만 이벤트를 설정할 수 있음
    - div, button, input, form, span 등의 DOM 요소에는 이벤트를 설정할 수 있지만, 직접 만든 컴포넌트에는 이벤트를 자체적으로 설정할 수 없습니다.
    - 다음과 같이 MyComponent에 onClick 값을 설정한다면 MyComponent를 클릭할때 onSomething 함수를 실행하는 것이 아니라, 그냥 이름이 onClick인 props를 MyComponent에 전달해 줄 뿐입니다.

```javascript 
<MyComponent onClick={doSomething} />
```

> 컴포넌트에 자체적으로 이벤트를 설정할 수는 없습니다. 하지만 전달받은 props를 컴포넌트 내부의 DOM 이벤트로 설정할 수 있습니다.

```javascript
<div onClick={this.props.onClick}>
    ...
</div>
```

* 리액트에서 지원하는 이벤트 종류

- Clipboard
- Composition
- Keyboard
- Focus
- Form 
- Mouse
- Selection
- Touch
- UI
- Wheel
- Image
- Animation
- Transition

## 이벤트 핸들링 익히기

* 컴포넌트 생성 및 불러오기 

#### EventPractice.js

```javascript 
import { Component } from 'react'; 

class EventPractice extends Component {
    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
            </div>
        );
    }
}

export default EventPractice
```

#### App.js 

```javascript
import EventPractice from './EventPractice';

const App = () => {
    return <EventPractice />;
};

export default App;
```

* onChange 이벤트 핸들링하기

```javascript
import { Component } from 'react';

class EventPractice extends Component {
    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input 
                    type="text"
                    name="message"
                    placeholder="아무거나 입력해 보세요"
                    onChange={
                        (e) => {
                            console.log(e);
                        }
                    }
                />
            </div>
        );
    }
}

export default EventPractice;
```

> 콘솔에 기록되는 e객체는 SyntheticEvent로 웹 브라우저의 네이티브 이벤트를 감싸는 객체입니다. 네이티브 이벤트와 인터페이스가 같으므로 순수 자바스크립트에서 HTML 이벤트를 다룰 때와 똑같이 사용하면 됩니다.<br>SytheticEvent는 네이티브 이벤트와 달리 이벤트가 끝나고 나면 이벤트가 초기화되므로 정보를 참조할 수 없습니다. 예를 들어, 0.5초 뒤에 e객체를 참조하면 e객체 내부의 모든 값이 비워지게 됩니다.<br>만약 비동기적으로 이벤트 객체를 참조할 일이 있다면 e.persist() 함수를 호출해 주어야 합니다.

* state에 input 값 담기

#### EventPractice.js

```javascript
import { Component } from 'react';

class EventPractice extends Component {
    state = {
        message: ''
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input 
                    type="text"
                    name="message" 
                    placeholder="아무거나 입력해 보세요"
                    value={this.state.message}
                    onChange={
                        (e) => {
                            this.setState({
                               message: e.target.value 
                            })
                        }
                    }
                />
            </div>
        );
    }
}

export default EventPractice;
```

* 버튼을 누를 때 comment 값을 공백으로 설정

#### EventPractice.js

```javascript
import { Component } from 'react';

class EventPractice extends Component {
    state = {
        message: ''
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input
                    ...
                />
                <button onClick={
                    () => {
                        alert(this.state.message);
                        this.setState({
                            message: ''
                        });
                    }
                }>확인</button>
            </div>
        );
    }
}

export default EventPractice;
```

* 임의 메서드 만들기 

> **이벤트에 실행할 자바스크립트 코드를 전달하는 것이 아니라, 함수 형태의 값을 전달합니다.** 그래서 이벤트를 처리할 때 렌더링을 하는 동시에 함수를 만들어서 전달해 주었습니다. 이 방법 대신 함수를 미리 준비하여 전달하는 방법도 있습니다. 성능상으로는 차이가 거의 없지만 가독성은 훨씬 높습니다.


* 기본 방식

#### EventPractice.js 

```javascript
import { Component } from 'react';

class EventPractice extends Component {
    state = {
        message: ''
    }

    constructor(props) {
        super(props);
        this.handleChange = this.handleChange.bind(this);
        this.handleClick = this.handleClick.bind(this);
    }

    handleChange(e) {
        this.setState({
            message: e.target.value
        });
    }

    handleClick() {
        alert(this.state.message);
        this.setState({
            message: ''
        });
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input 
                    type="text"
                    name="message"
                    placeholder="아무거나 입력해 보세요"
                    value={this.state.message}
                    onChange={this.handleChange}
                />
                <button onClick={this.handleClick}>확인</button>
            </div>
        );
    }
}

export default EventPractice;
```
> 함수가 호출될 때 this는 호출부에 따라 결정되므로, 클래스의 임의 메서드가 특정 HTML요소의 이벤트로 등록되는 과정에서 메서드와 this의 관계가 끊어져 버립니다. 이 때문에 임의 메서드가 이벤트로 등록되어도 this를 컴포넌트 자신으로 제대로 가리키기 위해서는 메서드를 this와 바인딩(binding)하는 작업이 필요합니다. 만약 바인딩하지 않는 경우라면 this가 undefined를 가리키게 됩니다.<br>현재 constructor 함수에서 함수를 바인딩하는 작업이 이루어지고 있습니다.

* Property Initializer Syntax를 사용한 메서드 작성 

- 화살표 함수 형태로 메서드를 정의하는 것 

```javascript
import { Component } from 'react';

class EventPractice extends Component {

    state = {
        message: ''
    }

    handleChange = (e) => {
        this.setState({
            message: e.target.value
        });
    }

    handleClick = () => {
        alert(this.state.message);
        this.setState({
            message: ''
        });
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input 
                    type="text"
                    name="message"
                    placeholder="아무거나 입력해 보세요"
                    value={this.state.message}
                    onChange={this.handleChange}
                />
                <button onClick={this.handleClick}>확인</button>
            </div>
        );
    }
}

export default EventPractice;
```

* input 여러 개 다루기

- event 객체를 활용하는 것, e.target.name 값을 사용하면 됩니다.

#### EventPractice.js

```javascript
import { Component } from 'react';

class EventPractice extends Component {

    state = {
        username: '',
        message: ''
    }

    handleChange = (e) => {
        this.setState({
            [e.target.name]: e.target.value
        });
    }

    handleClick = () => {
        alert(this.state.username + ': ' + this.state.message);
        this.setState({
            username: '',
            message: ''
        });
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input
                    type="text"
                    name="username"
                    placeholder="사용자명"
                    value={this.state.username}
                    onChange={this.handleChange}
                />
                <input 
                    type="text"
                    name="message" 
                    placeholder="아무거나 입력해 보세요"
                    value={this.state.message}
                    onChange={this.handleChange}
                />
                <button onClick={this.handleClick}>확인</button>
            </div>
        );
    }
}

export default EventPractice;
```

> 객체 안에서 key를 [ ]로 감싸면 그 안에 넣은 레퍼런스가 가리키는 실제 값이 key 값으로 사용됩니다.

* onKeyPress 이벤트 핸들링

```javascript
import { Component } from 'react';

class EventPractice extends Component {

    state = {
        username: '',
        message: ''
    }
	
	handleChange = (e) => {
		this.state({
			[e.target.name] = e.target.value
		});
	}
	
	handleClick = () => {
		alert(this.state.username + ': ' + this.state.message);
		this.setState({
			username: '',
			message: ''
		});
	}
	
	handleKeyPress = (e) => {
		if (e.key === 'Enter') {
			this.handleClick();
		}
	}
	
	render() {
		return (
			<div>
				<h1>이벤트 연습</h1>
				<input 
					type="text"
					name="username"
					placeholder="사용자명"
					value={this.state.username}
					onChange={this.handleChange}
				/>
				<input 
					type="text"
					name="message"
					placeholder="아무거나 입력해 보세요"
					value={this.state.message}
					onChange={this.handleChange}
					onKeyPress={this.handleKeyPress}
				/>
				<button onClick={this.handleClick}>확인</button>
			</div>
		);
	}
}

export default EventPractice;
```

* 함수 컴포넌트로 구현해 보기

#### EventPractice.js 

```javascript
import { useState } from 'react';

const EventPractice = () => {
	const [username, setUsername] = useState('');
	const [message, setMessage] = useState('');
	const onChangeUsername = e => setUsername(e.target.value);
	const onChangeMessage = e => setMessage(e.target.value);
	const onClick = () => {
		alert(username + ': ' + message);
		setUsername('');
		setMessage('');
	};
	
	const onKeyPress = e => {
		if (e.key === 'Enter') {
			onClick();
		}
	};
	
	return (
		<div>
			<h1>이벤트 연습</h1>
			<input 
				type="text"
				name="username"
				placeholder="사용자명"
				value={username}
				onChange={onChangeUsername}
			/>
			<input 
				type="text"
				name="message"
				placeholder="아무거나 입력해 보세요"
				value={message}
				onChange={onChangeMessage}
				onKeyPress={onKeyPress}
			/>
			<button onClick={onClick}>확인</button>
		</div>
	);
};

export default EventPractice;
```

> 인풋의 갯수가 많아질 것 같으면 e.target.name을 활용하는 것이 더 좋을 수 있습니다.

#### EventPractice.js 

```javascript 
import { useState } from 'react';

const EventPractice = () => {
	const [form, setForm] = useState({
		username: '',
		message: ''
	});
	
	const [ username, message ] = form;
	const onChange = e => {
		const nextForm = {
			...form,  // 기존의 form 내용을 이 자리에 복사한 뒤 
			[e.target.name]: e.target.value  // 원하는 값을 덮어 씌우기
		};
		setForm(nextForm);
	};
	
	const onClick = () => {
		alert(username + ': ' + message);
		setForm({
			username: '',
			message: ''
		});
	};
	
	const onKeyPress = e => {
		if (e.key === 'Enter') {
			onClick();
		}
	};
	
	return (
		<div>
			<h1>이벤트 연습</h1>
			<input 
				type="text"
				name="username"
				placeholder="사용자명"
				value={username}
				onChange={onChange}
			/>
			<input 
				type="text"
				name="message"
				placeholder="아무거나 입력해 보세요"
				value={message}
				onChange={onChange}
				onKeyPress={onKeyPress}
			/>
			<button onClick={onClick}>확인</button>
		</div>
	);
};

export default EventPractice;
```

> e.target.name 값을 활용하려면, 위와 같이 useState를 쓸 때 인풋 값들이 들어 있는 form 객체를 사용해 주면 됩니다.