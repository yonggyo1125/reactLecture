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

이 책의 프로젝트에서는 src/index.js를 시작으로 필요한 파일을 다 불러와서 번들링하게 됩니다.<br><br>
2017년부터는 브라우저에서도 import 구문을 사용할 수 있게 되었지만, 이는 단순히 다른 경로에 있는 자바스크립트를 불러오는 용도로만 사용되기 때문에 프로젝트 번들링과는 다릅니다.<br><br>

웹팩을 사용하면 SVG 파일과 CSS 파일도 불러와서 사용할 수 있습니다. 이렇게 파일들을 불러오는 것은 웹팩의 로더(loader)라는 기능이 담당합니다. 로더는 여러가지 종류가 있습니다. 예를 들어 <code>css-loader</code>는 CSS 파일을 불러올 수 있게 해 주고, <code>file-loader</code>는 웹 폰트나 미디어 파일 등을 불러올 수 있게 해 줍니다. 



# JSX란?
# JSX의 장점
# JSX 문법
# ESLint와 Prettier 적용하기