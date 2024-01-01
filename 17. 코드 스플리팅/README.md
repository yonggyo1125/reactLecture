# 코드 스플리팅

> 리액트 프로젝트를 완성하여 사용자에게 제공할 때는 빌드 작업을 거쳐서 배포해야 합니다. 빌드 작업을 통해 프로젝트에서 사용되는 자바스크립트르 파일 안에서 불필요한 주석, 경고메세지, 공백 등을 제거하여 파일 크기를 최소화하기도 하고, 브라우저에게 JSX 문법이나 다른 최신 자바스크립트 문법이 원활하게 실행되도록 코드의 트랜스파일 작업도 할 수 있습니다. 만약 프로젝트 내에 이미지와 같은 정적 파일이 있다면 해당 파일을 위한 경로도 설정되지요.
> 
> 이 작업은 웹팩(webpack)이라는 도구가 담당합니다. 웹팩에서 별도의 설정을 하지 않으면 프로젝트에서 사용 중인 모든 자바스크립트 파일이 하나의 파일로 합쳐지고, 모든 CSS 파일도 하나의 파일로 합쳐집니다.
> 
> CRA로 프로젝트를 빌드할 경우 최소 두 개 이상의 자바스크립트 파일이 생성되는데, CRA의 기본 웹팩 설정에는 SplitChunks라는 기능이 적용되어 node_modules에서 불러온 파일, 일정 크기 이상의 파일, 여러 파일 간에 공유된 파일을 자동으로 따로 분리시켜서 캐싱의 효과를 제대로 누릴 수 있게 해 줍니다.


리액트 프로젝트를 생성해서 빌드해봅시다.

```
yarn create react-app splitting-sample
cd splitting-sample
yarn build
```

프로젝트 디렉터리 안에 있는 <code>build/static</code> 디렉토리를 열어 보세요. 자바스크립트 파일 여러 개가 만들어져 있습니다.

![image1](https://raw.githubusercontent.com/yonggyo1125/reactLecture/master/17.%20%EC%BD%94%EB%93%9C%20%EC%8A%A4%ED%94%8C%EB%A6%AC%ED%8C%85/images/image1.png)

> 파일 이름을 보면 '7b7b7b'와 같은 해시(hash) 값이 포함되어 있습니다. 이 값은 빌드하는 과정에서 해당 파일의 내용에 따라 생성되며, 이를 통해 브라우저가 새로 파일을 받아야 할지 받지 말아야 할지를 알 수 있습니다.
> 
> 현재 2로 시작하는 파일에는 React, ReactDOM 등 node_modules에서 불러온 라이브러리 관련 코드가 들어 있고, main으로 시작하는 파일에는 직접 프로젝트에 작성하는 App 같은 컴포넌트에 대한 코드가 들어가 있습니다. 열어보면 2로 시작하는 파일은 코드가 매우 긴 반면, main으로 시작하는 파일은 코드가 매우 짧을 것입니다.
> 
> <code>SplitChunks</code>라는 웹팩 기능을 통해 자주 바뀌지 않는 코드들이 2로 시작하는 파일에 들어 있기 때문에 캐싱의 이점을 더 오래 누릴 수 있습니다.


2로 시작하는 파일 이름을 확인하세요(예시와 해시값은 다를 수 있습니다.). 그 다음에 App.js의 코드를 변경해 봅시다.

#### App.js

```jsx
import logo from './logo.svg'
import './App.css';

function App() {
    return (
        <div className="App">
            <header className="App-header">
                <img src={logo} className="App-logo" alt="logo" />
                <p>Hello React!</p>
            </header>
        </div>
    );
}

export default App;
```




# 자바스크립트 함수 비동기 로딩 

# React.lazy와 Suspense를 통한 컴포넌트 코드 스플리팅
