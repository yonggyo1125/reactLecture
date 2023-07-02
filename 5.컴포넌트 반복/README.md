# 컴포넌트 반복 

## 자바스크립트 배열의 map() 함수

- 자바스크립트 배열 객체의 내장 함수인 map 함수를 사용하여 반복되는 컴포넌트를 렌더링할 수 있습니다. 
- map 함수는 파라미터로 전달된 함수를 사용해서 배열 내 각 요소를 원하는 규칙에 따라 변환한 후 그 결과를 새로운 배열을 생성합니다.

### 문법 

```javascript
arr.map(callback, [thisArg])
```

- 이 함수의 파라미터는 다음과 같습니다.
	- **callback** : 새로운 배열의 요소를  생성하는 함수로 파라미터는 다음 세가지 입니다.
		- **currentValue** : 현재 처리하고 있는 요소
		- **index** : 현재 처리하고 있는 요소의 index 값
		- **array** : 현재 처리하고 있는 원본 배열
	- **thisArg(선택 항목)** : callback 함수 내부에서 사용할 this 레퍼런스
	
- 예제

```javascript
const numbers = [1,2,3,4,5];

const processed = numbers.map(num => num * num);

console.log(processed);
```


## 데이터 배열을 컴포넌트 배열로 변환하기


### 컴포넌트 수정하기 

#### IterationSample.js 

```javascript
const IterationSample = () => {
	const names = ['눈사람', '얼음', '눈', '바람'];
	const nameList = names.map(name => <li>{name}</li>);
	return <li>{nameList}</li>;
};

export default IterationSample;
```


> map 함수에서 JSX를 작성할 때는 앞서 다른 예제처럼 DOM 요소를 작성해도 되고, 컴포넌트를 사용해도 됩니다.

#### App.js

```javascript
import { Component } from 'react';
import IterationSample from './IterationSample';

const App extends Component {
	render() {
		return (
			<IterationSample />
		);
	}
}

export default App;
```

> 크롬 개발자 도구의 콘솔을 열어보면 "key" prop이 없다는 경고 메시지를 표시합니다.

## key

> 리액트에서는 key는 컴포넌트 배열을 렌더링했을 때 어떤 원소에 변동이 있었는지 알아내려고 사용합니다. 예를 들어 유동적인 데이터를 다룰 때는 원소를 새로 생성할 수도, 제거할 수도, 수정할 수도 있습니다. key가 없을 때는 Virtual DOM을 비교하는 과정에서 리스트를 순차적으로 비교하면서 변화를 감지합니다. 하지만 key가 있다면 이 값을 사용하여 어떤 변화가 일어났는지 더욱 빠르게 알아낼 수 있습니다.

### key 설정 

- key 값은 언제나 유일해야 합니다. 따라서 데이터가 가진 고유값을 key 값으로 설정해야 합니다.
- 예를 들어 다음과 같이 게시판의 게시물을 렌더링한다면 게시물 번호를 key 값으로 설정해야 합니다.

```javascript
const articleList = articles.map(article => (
	<Article 
		title={article.title}
		writer={article.writer}
		key={article.id}
	/>
));
```

> 위 예와 같이 고유번호가 없는 경우는 map 함수에 전달되는 콜백함수의 인수인 index 값을 사용하면 됩니다.

#### IterationSample.js

```javascript
const IterationSample = () => {
	const names = ['눈사람', '얼음', '눈', '바람'];
	const namesList = names.map((name, index) => <li key={index}>{name}</li>);
	return <ul>{namesList}</ul>;
};

export default IterationSample;
```

> 고유한 값이 없을 때만 index 값을 key로 사용해야 합니다. index를 key로 사용하면 배열이 변경될 때 효율적으로 리렌더링하지 못합니다.

## 응용 

### 초기 상태 설정하기

#### IterationSample.js

```javascript
import { useState } from 'react';

const IterationSample = () => {
	const [names, setNames] = useState([
		{ id : 1, text: '눈사람' },
		{ id : 2, text: '얼음' },
		{ id : 3, text: '눈' },
		{ id : 4, text: '바람' }
	]);
	const [inputText, setInputText] = useState('');
	const [nextId, setNextId] = useState(5); // 새로운 항목을 추가할 때 사용할 id
	
	const namesList = names.map(name => <li key={name.id}>{name.text}</li>);
	return <ul>{namesList}</ul>
};

export default IterationSample;
```

> map 함수를 사용할 때 key 값을 index 대신 name.id 값으로 지정해 주었습니다.

### 데이터 추가 기능 구현하기

#### IterationSample.js 

```javascript 
import { useState } from 'react';

const IterationSample = () => {
	const [names, setNames] = useState([
		{ id: 1, text: '눈사람' },
		{ id: 2, text: '얼음' },
		{ id: 3, text: '눈' },
		{ id: 4, text: '바람' }
	]);
	const [inputText, setInpuText] = useState('');
	const [nextId, setNextId] = useState(5); // 새로운 항목을 추가할 때 사용할 id
	
	const onChange = e => setInputText(e.target.value);
	
	const namesList = names.map(name => <li key={name.id}>{name.text}</li>);
	return (
		<>
			<input value={inputText} onChange={onChange} />
			<button>추가</button>
			<ul>{namesList}</ul>
		</>
	);
};

export default IterationSample;
```

- 버튼을 클릭했을 때 호출할 onClick 함수를 선언하여 버튼의 onClick 이벤트를 설정합니다.
- onClick 함수에서는 배열의 내장 함수 concat을 사용하여 새로운 항목을 추가한 배열을 만들고 setNames를 통해 상태를 업데이트해 줍니다.

#### IterationSample.js

```javascript
import { useState } from 'react';

const IterationSample = () => {
	const [names, setNames] = useState([
		{ id: 1, text: '눈사람' },
		{ id: 2, text: '얼음' },
		{ id: 3, text: '눈' },
		{ id: 4, text: '바람' }
	]);
	const [inputText, setInputText] = useState('');
	const [nextId, setNextId] = useState(5); // 새로운 항목을 추가할 때 사용될 id
	
	const onChange = e => setInputText(e.target.value);
	const onClick = () => {
		const nextNames = names.concat({
			id: nextId, // nextId 값을 id로 설정하고
			text: inputText
		});
		setNextId(nextId + 1); // nextId 값에 1을 더해 준다.
		setNames(nextNames); // names 값을 업데이트 한다.
		setInputText(''); // inputText를 비운다.
	};
	
	const namesList = names.map(name => <li key={name.id}>{name.text}</li>);
	return (
		<>
			<input value={inputText} onChange={onChange} />
			<button onClick={onClick}>추가</button>
			<ul>{namesList}</ul>
		</>
	);
};

export default IterationSample;
```

> 배열에 새 항목을 추가할 때 배열의 push 함수를 사용하지 않고 concat을 사용했는데, 이는 push 함수는 기존 배열 자체를 변경해 주는 반면, concat은 새로운 배열을 만들어 준다는 차이점이 있습니다.<br>리액트에서 상태를 업데이트할 때는 기존 상태를 그대로 두면서 새로운 값을 상태로 설정해야 합니다. 이를 **불변성 유지**라고 합니다. 불변성 유지를 해 주어야 나중에 리액트 컴포넌트 성능을 최적화할 수 있습니다.


### 데이터 제거 기능 구현하기

- 마찬가지로 불변성을 유지하면서 업데이트해 주어야 합니다. 
- 불변성을 유지하면서 배열의 특정 항목을 지울 때는 배열의 내장 함수 filter를 사용합니다.

#### IterationSample.js 

```javascript
import { useState } from 'react';

const IterationSample = () => {
	const [names, setNames] = useState([
		{ id: 1, text: '눈사람' },
		{ id: 2, text: '얼음' },
		{ id: 3, text: '눈' },
		{ id: 4, text: '바람' }
	]);
	const [inputText, setInputText] = useState('');
	const [nextId, setNextId] = useState(5); // 새로운 항목을 추가할 때 사용될 id
	
	const onChange = e => setInputText(e.target.value);
	const onClick = () => {
		const nextNames = names.concat({
			id: nextId, // nextId 값을 id로 설정하고
			text: inputText
		});
		setNextId(nextId + 1); // nextId 값에 1을 더해 준다.
		setNames(nextNames); // names 값을 업데이트 한다.
		setInputText(''); // inputText를 비운다.
	};
	
	const onRemove = id => {
		const nextNames = names.filter(name => name.id !== id);
		setNames(nextNames);
	};
	
	const namesList = names.map(name => <li key={name.id} onDoubleClick{() => onRemove(name.id)}>{name.text}</li>);
	return (
		<>
			<input value={inputText} onChange={onChange} />
			<button onClick={onClick}>추가</button>
			<ul>{namesList}</ul>
		</>
	);
};

export default IterationSample;
```