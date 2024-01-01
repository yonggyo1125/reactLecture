# 코드 이해하기

```jsx
import logo from './logo.svg';
import './App.css';

function App() {
    return (
        <div className="App">
            <header className="App-header">
                <img src={logo} className="App-logo" alt="logo" />
                <p>
                    Edit <code>src/App.js</code> and Save to reload
                </p>
                <a 
                    className="App-link"
                    href="https://reactjs.org"
                    target="_blank"
                    rel="noopener noreferrer"
                >
                    Learn React
                </a>
            </header>
        </div>
    );
}

export default App;
```

리액트에 방금 입문했다면 이 파일의 코드가 조금 낮설 수 있습니다. 코드를 하나씩 이해해 보면

```javascript
import logo from "./logo.svg";
import "./App.css";
```

- <code>import</code> 구문 : 이는 특정 파일을 불러오는 것을 의미, 리액트로 만든 프로젝트의 자바스크립트 파일에서는 import를 사용하여 다른 파일들을 불러와 사용할 수 있습니ㅏㄷ.

> 이렇게 모듈을 불러와서 사용하는 것은 사실 원래 브라우저에는 없던 기능입니다. 브라우저가 아닌 환경에서 자바스크립트를 실행할 수 있게 해 주는 환경인 Node.js에서는 import가 아닌 require라는 구문으로 패키지를 불러올 수 있습니다.
> 
> 이러한 기능을 브라우저에서도 사용하기 위해 <code>번들러(bundler)</code>를 사용합니다. 번들(bundle)은 묶는다는 뜻입니다. 즉, 파일을 묶듯이 연결하는 것입니다.
> 
> 대표적인 



# JSX란?
# JSX의 장점
# JSX 문법
# ESLint와 Prettier 적용하기