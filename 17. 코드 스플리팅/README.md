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

<code>yarn build</code> 명령어로 프로젝트를 다시 빌드 합니다.<br>
빌드 후 <code>build/static</code> 디렉터리를 다시 열어 보면 기존에 node_modules에서 불러온 라이브러리가 들어 있던 2로 시작하는 파일의 이름은 바뀌지 않았고, 작성하는 컴포넌트 관련 코드가 들어 있던 main으로 시작하는 파일의 이름은 바뀐 것을 확인할 수 있습니다.

![image2](https://raw.githubusercontent.com/yonggyo1125/reactLecture/master/17.%20%EC%BD%94%EB%93%9C%20%EC%8A%A4%ED%94%8C%EB%A6%AC%ED%8C%85/images/image2.png)

이렇게 파일을 분리하는 작업을 <code>코드 스플리팅</code>이라고 합니다. 프로젝트에 기본 탑재된 <code>SplitChunks</code>기능을 통한 코드 스플리팅은 단순히 효율적인 캐싱 효과만 있을 뿐입니다. 예를 들어 페이지 A, B, C로 구성된 싱글 페이지 애플리케이션(SPA)을 개발한다고 가정해 봅시다. 사용자가 A 페이지에 방문했다면 B 페이지와 C 페이지에서 사용하는 컴포넌트 정보는 필요하지 않습니다. 사용자가 실제로 B 혹은 C 페이지로 이동하려고 할 때만 필요합니다.<br><br>

하지만 리액트 프로젝트에 별도로 설정하지 않으면 A,B,C 컴포넌트에 대한 코드가 모두 한 파일(main)에 저장되어 버립니다. 만약 애플리케이션의 규모가 커지면 지금 당장 필요하지 않은 컴포넌트 정보도 모두 불러오면서 파링 크기가 매우 커집니다. 그러면 로딩이 오래 걸리기 때문에 사용자 경험도 안 좋아지고 트래픽도 많이 나오게됩니다. <br><br>

이러한 문제점을 해결해 줄 수 있는 방법이 바로 <code>코드 비동기 로딩</code>입니다. 이 또한 코드 스플리팅 방법 중 하나입니다. 코드 비동기 로딩을 통해 자바스크립트 함수, 객체, 혹은 컴포넌트를 처음에는 불러오지 않고 필요한 시점에 불러와서 사용할 수 있습니다.

# 자바스크립트 함수 비동기 로딩 

컴포넌트 코드를 스플리팅하기 앞서 일반 자바스크립트 함수를 스플리팅해 보겠습니다. src 디렉터리에 notify.js 파일을 생성하여 다음 함수를 작성해보세요.


> notify.js

```jsx
export default function notify() {
    alert('안녕하세요!');
}
```

**Hello React!** 문구를 누르면 notify 함수가 실행되도록 App 컴포넌트를 수정합니다.

> src/App.js

```jsx
import logo from './logo.svg';
import './App.css';
import notify from './notify';

function App() {
    const onClick = () => {
        notify();
    };
    
    return (
        <div className="App">
            <header className="App-header">
                <img src={logo} className="App-logo" alt="logo" />
                <p onClick={onClick}>Hello React</p>
            </header>
        </div>
    );
}

export default App;
```

이렇게 코드를 작성하고 빌드하면 notify 코드가 main 파일 안에 들어가게 됩니다. 하지만 다음과 같이 import를 상단에서 하지 않고 <code>import()</code> 함수 형태로 메서드 안에서 사용하면, 파일을 따로 분리시켜서 저장합니다. 그리고 실제 함수가 필요한 지점에 파일을 불러와서 함수를 사용할 수 있습니다. <br><br>

코드를 다름과 같이 수정합니다.

> src/App.js

```jsx
import logo from './logo.svg';
import './App.css';

function App() {
    const onClick = () => {
        import('./notify').then(result => result.default());
    };
    
    return (
        <div className="App">
            <header className="App-header">
                <img src={logo} className="App-logo" alt="logo" />
                <p onClick={onClick}>Hello React</p>
            </header>
        </div>
    );
}

export default App;
```

import를 함수로 사용하면 Promise를 반환합니다. 이렇게 import를 함수로 사용하는 문법은 비록 아직 표준 자바스크립트가 아니지만, stage-3 단계에 있는 dynamic import라는 문법입니다. 현재는 웹팩에서 지원하고 있으므로 별도의 설정 없이 프로젝트에 바로 사용할 수 있습니다. 이 함수를 통해 모듈을 불러올 때 모듈에서 <code>default</code>로 내보낸 것은 <code>result.default</code>를 참조해야 사용할 수 있습니니다.<br><br>

브라우저를 열고 개발자 도구의 Network 탭을 연 다음, Hello React!를 클릭해 보세요.

![image3](https://raw.githubusercontent.com/yonggyo1125/reactLecture/master/17.%20%EC%BD%94%EB%93%9C%20%EC%8A%A4%ED%94%8C%EB%A6%AC%ED%8C%85/images/image3.png)

<b>Hello React!</b>를 클릭하는 시점에 새로운 자바스크립트 파일을 불러올 것입니다. 불러온 파일의 내용을 확인해 보면 notify에 관련된 코드만 들어 있습니다. 

# React.lazy와 Suspense를 통한 컴포넌트 코드 스플리팅
