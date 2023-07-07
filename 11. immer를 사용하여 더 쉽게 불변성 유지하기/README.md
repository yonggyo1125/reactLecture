# immer를 사용하여 더 쉽게 불변성 유지하기

- immer라는 라이브러리를 사용하면, 구조가 복잡한 객체도 매우 쉽고 짧은 코드를 사용하여 불변성을 유지하면서 업데이트해 줄 수 있습니다.

## immer를 설치하고 사용법 알아보기

### 프로젝트 준비

```
$ yarn create react-app immer-tutorial
cd immer-tutorial
yarn add immer
```

### immer를 사용하지 않고 불변성 유지

```javascript
import { useRef, useCallback, useState } from 'react';

const App = () => {
	const nextId = useRef(1);
	const [form, setForm] = useState({ name: '', username: ''});
	const [data, setData] = useState({
		array: [],
		uselessValue: null
	});
	
	// input 수정을 위한 함수
	const onChange = useCallback(
		e => {
			e.preventDefault();
			const info = {
				id: nextId.current,
				name: form.name,
				username: form.username
			};
			
			// array에 새 항목 등록
			setData({
				...data,
				array: data.array.concat(info)
			});
			
			// form 초기화
			setForm({
				name: '',
				username: '',
			});
			nextId.current += 1;
		},
		[data, form.name, form.username]
	);
};

export default App;
```