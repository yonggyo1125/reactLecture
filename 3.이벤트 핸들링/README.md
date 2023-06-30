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

> 컴포넌트 