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

이렇게 모듈을 불러와서 사용하는 것은 사실 원래 브라우저에는 없던 기능입니다. 브라우저가 아닌 환경에서 자바스크립트를 실행할 수 있게 해 주는 환경인 Node.js에서는 import가 아닌 require라는 구문으로 패키지를 불러올 수 있습니다.<br><br>

이러한 기능을 브라우저에서도 사용하기 위해 <code>번들러(bundler)</code>를 사용합니다. 번들(bundle)은 묶는다는 뜻입니다. 즉, 파일을 묶듯이 연결하는 것입니다.<br><br>

대표적인 번들러로 웹팩, Parcel, browserify라는 도구들이 있으며 각 도구마다 특성이 다릅니다. 리액트 프로젝트에서는 주로 웹팩을 사용하는 추세입니다. 편의성과 확장성이 다른 도구보다 뛰어나기 때문입니다. 번들러 도구를 사용하면 import(또는 require)로 모듈을 불러왔을 때 불러온 모듈을 모두 합쳐서 하나의 파일을 생성해 줍니다. 또 최적화 과정에서 여러 개의 파일로 분리될 수도 있습니다.<br><br>

![image1](https://raw.githubusercontent.com/yonggyo1125/reactLecture/master/1.%20JSX/images/image1.png)

프로젝트에서는 src/index.js를 시작으로 필요한 파일을 다 불러와서 번들링하게 됩니다.<br><br>
2017년부터는 브라우저에서도 import 구문을 사용할 수 있게 되었지만, 이는 단순히 다른 경로에 있는 자바스크립트를 불러오는 용도로만 사용되기 때문에 프로젝트 번들링과는 다릅니다.<br><br>

웹팩을 사용하면 SVG 파일과 CSS 파일도 불러와서 사용할 수 있습니다. 이렇게 파일들을 불러오는 것은 웹팩의 로더(loader)라는 기능이 담당합니다. 로더는 여러가지 종류가 있습니다. 예를 들어 <code>css-loader</code>는 CSS 파일을 불러올 수 있게 해 주고, <code>file-loader</code>는 웹 폰트나 미디어 파일 등을 불러올 수 있게 해 줍니다. 그리고 <code>babel-loader</code>는 자바스크립트 파일들을 불러오면서 최신 자바스크립트 문법으로 작성된 코드를 바벨이라는 도구를 사용하여 ES5 문법으로 변환해 줍니다.

> ES5는 이전 버전의 자바스크립트를 의미합니다. 최신 자바스크립트 문법을 EC5 형태로 변환하는 것은 구버전 웹브라우저와 호환하기 위해서입니다. 현재 대부분의 최신 웹브라우저에서는 자체적으로 최신 자바스크립트 문법을 바로 실행할 수 있지만, 구버전 웹 브라우저에서는 실행되지 않기 때문에 사전에 꼭 변환해 주어야 합니다. 또한 리액트 컴포넌트에서 사용하는 JSX라는 문법도 정식 자바스크립트 문법이 아니므로 ES5 형태의 코드로 변환해야 합니다.
> 
> ES5이후 문법들은 ECMAScript 2015(ES6), ECMAScript 2016(ES7) 등과 같은 형태로 이름이 지어집니다.

웹팩의 로더는 원래 직접 설치하고 설정해야 하지만 리액트 프로젝트를 만들 때 사용했던 <code>create-react-app</code>이 번거로운 작업을 모두 대신해 주기 때문에 별도의 설정을 할 필요가 없습니다. 

```jsx
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
```

위 코드는 App이라는 컴포넌트를 만들어 줍니다. function 키워드를 사용하여 컴포넌트를 만들었는데, 이를 함수 컴포넌트라고 부릅니다. 프로젝트에서 컴포넌트를 렌더링하면(렌더링이란 **보여준다**는 것을 의미합니다) 함수에서 반환하고 있는 내용을 나타냅니다. 함수에서 반환하는 내용을 보면 마치 HTML을 작성한 것 같으나 이 코드는 HTML이 아닙니다. 그렇다고 문자열 템플릿도 아닙니다. 이런 코드를 <code>JSX</code>라고 부릅니다. 

# JSX란?

> JSX는 자바스크립트의 확장 문법이며 XML과 매우 비슷하게 생겼습니다. 이런 형식으로 작성된 코드는 브라우저에서 실행되기 전에 코드가 번들링되는 과정에서 바벨을 사용하여 일반 자바스크립트 형태의 코드로 변환됩니다. 

<br>

> JSX

```jsx
function App() {
    return (
        <div>
            Hello <b>react</b>
        </div>
    );
}
```

이렇게 작성된 코드는 다음과 같이 변환됩니다.

```javascript
function App() {
    return React.createElement("div", null, "Hello", React.createElement("b", null, "react"));
}
```

만약 컴포넌트를 렌더링할 때마다 JSX 코드를 작성하는 것이 아니라 위 코드처럼 매번 <code>React.createElement</code> 함수를 사용해야 한다면 매우 불편합니다. <code>JSX</code>를 사용하면 매우 편리하게 UI를 렌더링할 수 있습니다.

# JSX의 장점

## 보기 쉽고 익숙하다

일반 자바스크립트만 사용한 코드와 JSX로 작성한 코드를 한번 비교해 봐도 JSX를 사용하는 편이 더 가독성이 높고 작성하기 쉽다고 느겨질 것입니다. 결국 HTML 코드를 작성하는 것과 비슷하기 때문입니다. 사실 이것이 JSX를 사용하는 주된 이유입니다. 자바스크립트로 요소들을 일일이 만들어야 한다면 불편합니다.

## 더욱 높은 활용도

JSX에서는 div나 span과 같은 HTML 태그를 사용할 수 있을 뿐만 아니라, 앞으로 만들 컴포넌트도 JSX 안에서 작성할 수 있습니다. App.js에서는 App 컴포넌트가 만들어져 있고 src/index.js 파일을 열어보면 이 App 컴포넌트를 마치 HTML 태그 쓰듯이 그냥 작성합니다.

> src/index.js

```jsx
import ReactDOM from 'react-dom'
import './index.css';
import App from './App';

ReactDOM.render(
    <React.StrictMode>
        <App />
    </React.StrictMode>,
    document.getElementById('root')
);
```

> ReactDOM.render는 무엇인가요?
> 
> 이 코드는 컴포넌트를 페이지에 렌더링하는 역할을 하며, react-dom 모듈을 불러와 사용할 수 있습니다. 이 함수의 첫 번째 파라미터에는 페이지에 렌더링할 애용을 JSX 형태로 작성하고 두 번째 파라미터에는 해당 JSX를 렌더링할 document 내부 요소를 설정합니다. 여기서는 id가 root인 요소 안에 렌더링을 하도록 설정했는데, 이 요소는 <code>public/index.html</code> 파일을 열어서 확인할 수 있습니다. 

> React.StrictMode는 무엇인가요?
> 
> <code>React.StrictMode</code>는 리액트 프로젝트에서 리액트의 레거시 기능을 사용하지 못하게 하는 기능입니다. 이를 사용하면 문자열 ref, componentWillMount 등 나중에는 완전히 사라지게 될 옛날 기능을 사용했을 때 경고를 출력합니다.  

# JSX 문법

> JSX는 편리한 문법이지만, 올바르게 사용하려면 몇 가지 규칙을 준수해야 합니다.

## 감싸인 요소

컴포넌트에 여러 요소가 있지만 반드시 부모 요소 하나로 감싸야 합니다. 한번 App.js 파일의 App 컴포넌트 함수 내부를 지우고 다음과 같이 작성해 보세요.

```jsx
function App() {
    return (
        <h1>리액트 안녕!</h1>
        <h2>잘 작동하니?</h2>
    );
}

export default App;
```

- 이런 형태의 코드는 제대로 작동하지 않습니다. 코드를 저장한 후 웹 브라우저나 개발 서버를 실행했던 터미널을 열어 보면 오류가 나타날 것입니다.
- 요소 여러 개가 부모 요소 하나에 의하여 감싸져 있지 않기 때문에 오류가 발생했습니다. 
- 이 오류는 다음과 같이 코드를 작성하여 해결할 수 있습니다. 

> src/App.js

```jsx
function App() {
    return (
        <div>
            <h1>리액트 안녕!</h1>
            <h2>잘 작동하니?</h2>
        </div>
    );  
}

export default App;
```

- 리액트 컴포넌트에서 요소 여러 개를 하나의 요소로 꼭 감싸 주어야 합니다. 이것은 <code>Virtual DOM</code>에서 컴포넌트 변화를 감지해 낼 때 효율적으로 비교할 수 있도록 컴포넌트 내부는 하나의 DOM 트리 구조로 이루어져야 한다는 규칙이 있기 때문입니다.
- 그러나 위 코드에서 꼭 div 요소를 사용하고 싶지 않을 수도 있습니다. 그런 경우에는 리액트 v16 이상 부터 도입된 <code>Fragment</code>라는 기능을 사용하면 됩니다.

> src/App.js

```jsx
import { Fragment } from 'react';

function App() {
    return (
        <Fragment>
            <h1>리액트 안녕!</h1>
            <h2>잘 작동하니?</h2>
        </Fragment>
    );  
}

export default App;
```

- 코드 상단 import 구문에서 react 모듈에 들어 있는 <code>Fragment</code>라는 컴포넌트를 추가로 불러옵니다.
- <code>Fragment</code>는 다음과 같은 형태로도 표현할 수 있습니다.

```jsx 
function App() {
    return (
        <>
            <h1>리액트 안녕!</h1>
            <h2>잘 작동하니?</h2>
        </>
    );  
}

export default App;
```

## 자바스크립트 표현

> JSX 안에서는 자바스크립트 표현식을 쓸 수 있습니다. 자바스크립트 표현식을 작성하려면 JSX 내부에서 코드를 { }로 감싸면 됩니다. 자바스크립트 값을 JSX 안에서 한번 렌더링해 봅시다. 

> src/App.js 

```jsx
function App() {
    const name = '리액트';
    return (
        <>
            <h1>{name}안녕</h1>
            <h2>잘 작동하니?</h2>
        </>
    );
}

export default App;
```

## If문 대신 조건부 연산자

> JSX 내부의 자바스크립트 표현식에서 if문을 사용할 수는 없습니다. 하지만 조건에 따라 다른 내용을 렌더링해야 할 때는 JSX 밖에서 if문을 사용하여 사전에 값을 설정하거나, { }안에서 조건부 연산자를 사용하면 됩니다. 조건부 연산자의 또 다른 이름은 삼항 연산자입니다. 

> src/App.js  

```jsx
function App() {
    const name = '리액트';
    return (
        <div>
            {name === '리액트' ? (
                <h1>리액트입니다.</h1>
            ) : (
                <h2>리액트가 아닙니다.</h2>
            )}
        </div>
    );
}

export default App;
```

## AND 연산자(&&)를 사용한 조건부 렌더링 



## undefined를 렌더링하지 않기

## 인라인 스타일링 

## class 대신 className 

## 꼭 닫아야 하는 태그

## 주석 

# ESLint와 Prettier 적용하기