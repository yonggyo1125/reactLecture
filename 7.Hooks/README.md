# Hooks

- 실습을 진행하기 위해 create-react-app을 사용하여 새로운 프로젝트를 생성해 주세요.

```
$ yarn create react-app hooks-tutorial
```

## UseState 

- useState는 가장 기본적인 Hook이며, 함수 컴포넌트에서도 가변적인 상태를 지닐 수 있게 해 줍니다.
- 함수 컴포넌트에서 상태를 관리해야 한다면 이 Hook을 사용하면 됩니다.

#### Counter.js

```javascript
import { useState } from 'react';

const Counter = () => {
	const [value, setValue] = useState(0);
	
	return (
		<div>
			<p>
				현재 카운터 값은 <b>{value}</b>입니다.
			</p>
			<button onClick={() => setValue(value + 1)}>+1</button>
			<button onClick={() => setValue(value - 1)}>-1</button>
		</div>
	);
};

export default Counter;
```

- useState는 코드 상단에서 import 구문을 통해 불러오고, 다음과 같이 사용합니다.

```javascript
const [value, setValue] = useState(0);
```

- useState 함수의 파라미터에는 상태의 기본값을 넣어 줍니다. 
- 이 함수가 호출되면 배열을 반환하고, 그 배열의 첫 번째 원소는 상태 값, 두 번째 원소는 상태를 설정하는 함수 입니다. 이 함수에 파라미터를 넣어서 호출하면 전달받은 파라미터로 값이 바뀌고 컴포넌트가 정상적으로 리렌더링 됩니다.

#### App.js

```javascript
import Counter from './Counter';

const App = () => {
	return <Counter />;
};

export drfault App;
```

### useState를 여러 번 사용하기

- 하나의 useState 함수는 하나의 상태 값만 관리할 수 있습니다. 컴포넌트에서 관리해야 할 상태가 여러 개이면 useState를 여러 번 사용하면 됩니다.

#### Info.js

```javascript
import { useState } from 'react';

const Info = () => {
	const [name, setName] = useState('');
	const [nickname, setNickname] = useState('');
	
	const onChangeName = e => {
		setName(e.target.value);
	};
	
	const onChangeNickName = e => {
		setNickname(e.target.value);
	};
	
	return (
		<div>
			<div>
				<input value={name} onChange={onChangeName} />
				<input value={nickname} onChange={onChangeNickname} />
			</div>
			<div>
				<b>이름: </b> {name}</b>
			</div>
			<div>
				<b>닉네임:</b> {nickname}
			</div>
		</div>
	);
};

export default Info;
```

#### App.js 

```javascript
import Info from './Info';

const App = () => {
	return <Info />;
};

export default App;
```

## useEffect

