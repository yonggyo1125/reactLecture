# Context API

## Context API를 사용한 전역 상태관리 흐름 이해하기

### 일반적인 전역 상태 관리 흐름

![일반적인 전역 상태](https://raw.githubusercontent.com/yonggyo1125/reactLecture/master/14.%20Context%20API/images/image1.png)

- 리액트 애플리케이션은 컴포넌트 간에 데이터를 props로 전달하기 때문에 컴포넌트 여기저기서 필요한 데이터가 있을 때는 주로 최상위 컴포넌트인 App의 state에 넣어서 관리합니다.
- App이 지니고 있는 value 값을 F 컴포넌트와 J 컴포넌트에 전달하려면 여러 컴포넌트를 거쳐야 합니다. F의 경우 App -> A -> B -> D의 흐름이고, J의 경우 App -> H -> J의 흐름입니다.
- 실제 리액트 프로젝트에서는 더 많은 컴포넌트를 거쳐야 할 때도 있고 다루어야 하는 데이터가 훨씬 많아질 수 있으므로, 이런 방식을 사용하면 유지 보수성이 낮아질 가능성이 있습니다.

### Context API를 사용한 전역 상태 관리 흐름

![Context API를 사용한 전역 상태](https://raw.githubusercontent.com/yonggyo1125/reactLecture/master/14.%20Context%20API/images/image2.png)

- 최상위 컴포넌트에서 여러 컴포넌트를 거쳐 props로 원하는 상태와 함수를 전달했지만 Context API를 사용하면 Context를 만들어서 단 한번에 원하는 값을 받아 와서 사용할 수 있습니다.

## Context API 사용법 익히기

### 프로젝트 생성

```
$ yarn create react-app context-tutorial
```

### 새 Context 만들기

#### contexts/color.js

```javascript
import { createContext } from 'react';

const ColorContext = createContext({ color: 'black' });

export default ColorContext;
```

> 새 Context를 만들 때는 createContext 함수를 사용합니다. 파라미터에는 해당 Context의 기본 상태를 지정합니다.

### Consumer 사용하기

- 색상을 props로 받아 오는 것이 아니라 ColorContext 안에 들어 있는 Consumer라는 컴포넌트를 통해 색상을 조회합니다.

#### components/ColorBox.js

```javascript
import ColorContext from '../contexts/color';

const ColorBox = () => {
    return (
        <ColorContext.Consumer>
            {value => (
                <div
                    style={{
                        width: '64px',
                        height: '64px',
                        background: value.color
                    }}
                />
            )}
        </ColorContext.Consumer>
    );
};

export default ColorBox;
```

> Consumer 사이에 중괄호를 열어서 그 안에 함수를 넣어 주었습니다. 이러한 패턴을 Function as a child 혹은 Render Props라고 합니다. 컴포넌트의 children이 있어야 할 자리에 일반 JSX 혹은 문자열이 아닌 함수를 전달하는 것

#### Render Props 예제

```javascript
const RenderPropsSample = ({ children }) => {
    return <div>결과: {children(5)}</div>;
};
```

- 위와 같은 컴포넌트가 있다면 추후 사용할 때 다음과 같이 사용할 수 있습니다.

```javascript
<RenderPropsSample>{value => 2 * value}</RenderPropsSample>;
```

- RenderPropsSample에게 children props로 파라미터에 2를 곱해서 반환하는 함수를 전달하면 해당 컴포넌트에서는 이 함수에 5를 인자로 넣어서 "결과: 10"을 렌더링합니다.

#### App.js

```javascript
import ColorBox from './components/ColorBox';
const App = () => {
    return (
        <div>
            <ColorBox />
        </div>
    );
};

export default App;
```

### Provider

> Provider를 사용하면 Context의 value를 변경할 수 있습니다.

#### App.js

```javascript
import ColorBox from './components/ColorBox';
import ColorContext from './contexts/color';
const App = () => {
    return (
        <ColorContext.Provider value={{ color: 'red' }}>
            <div>
                <ColorBox />
            </div>
        </ColorContext.Provider>
    );
};

export default App;
```

> Provider는 사용했는데 value를 명시하지 않았다면, 이 기본값을 사용하지 않기 때문에 오류가 발생합니다. Provider를 사용할 때는 value 값을 명시해 주어야 제대로 작동합니다.

## 동적 Context 사용하기

### Context 파일 수정하기

> Context의 value에는 무조건 상태 값만 있어야 하는것은 아니고 함수 역시 전달해 줄 수 있습니다.

#### contexts/color.js

```javascript
import { createContext, useState } from 'react';

const ColorContext = createContext({
    state: { color: 'black', subcolor: 'red' },
    actions: {
        setColor: () => {},
        setSubcolor: () => {}
    }
});

const ColorProvider = ({ children }) => {
    const [color, setColor] = useState('black');
    const [subcolor, setSubcolor] = useState('red');

    const value = {
        state: { color, subcolor },
        actions: { setColor, setSubcolor }
    };
    return (
        <ColorContext.Provider value={value}>{children}</ColorContext.Provider>
    );
};

// const ColorConsumer = ColorContext.Consumer와 같은 의미
const { Consumer: ColorConsumer } = ColorContext;

// ColorProvider와 ColorConsumer 내보내기
export { ColorProvider, ColorConsumer };

export default ColorContext;
```

### 새로워진 Context를 프로젝트에 반영하기

#### App.js

```javascript
import ColorBox from './components/ColorBox';
import { ColorProvider } from './contexts/color';

const App = () => {
    return (
        <ColorProvider>
            <div>
                <ColorBox />
            </div>
        </ColorProvider>
    );
};

export default App;
```

#### components/ColorBox.js

```javascript
import { ColorConsumer } from '../contexts/color';

const ColorBox = () => {
    return (
        <ColorConsumer>
            {value => (
              <>
                <div
                    style={{
                        width: '64px',
                        height: '64px',
                        background: value.state.color
                    }}
                />
                <div
                    style={{
                        width: '32px',
                        height: '32px',
                        background: value.state.subcolor
                    }}
                />
              </>
            )}
        </ColorConsumer>
    );
};

export default ColorBox;
```

- 위 코드에서 객체 비구조화 할당 문법을 사용하면 다음과 같이 value를 조회하는 것을 생략할 수 있습니다.


#### components/ColorBox.js

```javascript
import { ColorConsumer } from '../contexts/color';

const ColorBox = () => {
    return (
        <ColorConsumer>
            {({ state }) => (
                <>
                    <div
                        style={{
                            width: '64px',
                            height: '64px',
                            background: state.color
                        }}
                    />
                    <div
                        style={{
                            width: '32px',
                            height: '32px',
                            background: state.subcolor
                        }}
                    />
                </>
            )}
        </ColorConsumer>
    );
};

export default ColorBox;
```

### 색상 선택 컴포넌트 만들기

#### components/SelectColors.js

```javascript
const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];

const SelectColors = () => {
    return (
        <div>
            <h2>색상을 선택하세요.</h2>
            <div style={{ display: 'flex' }}>
                {colors.map(color => (
                    <div 
                        key={color}
                        style={{
                            background: color,
                            width: '24px',
                            height: '24px',
                            cursor: 'pointer'
                        }}
                    />
                ))}
            </div>
            <hr />
        </div>
    );
};

export default SelectColors;
```

#### App.js 

```javascript
import ColorBox from './components/ColorBox';
import { ColorProvider } from './contexts/color';
import SelectColors from './components/SelectColors';

const App = () => {
    return (
        <ColorProvider>
            <div>
                <SelectColors />
                <ColorBox />
            </div>
        </ColorProvider>
    );
};

export default App;
```

#### components/SelectColors.js

```javascript
import { ColorConsumer } from '../contexts/color';

const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];

const SelectColors = () => {
    return (
        <div>
            <h2>색상을 선택하세요.</h2>
            <ColorConsumer>
            {({ actions }) => (
                <div style={{ display: 'flex' }}>
                    {colors.map(color => (
                        <div 
                            key={color}
                            style={{ background: color, width: '24px', height: '24px', cursor: 'pointer' }}
                            onClick={() => actions.setColor(color)}
                            onContextMenu={e => {
                                e.preventDefault(); // 마우스 오른쪽 버튼 클릭 시 메뉴가 뜨는 것을 무시함
                                actions.setSubcolor(color);
                            }}
                        />
                    ))}
                </div>
            )}
            </ColorConsumer>
            <hr />
        </div>
    );
};

export default SelectColors;
```

## Consumer 대신 Hook 또는 static contextType 사용하기

### useContext Hook 사용하기

> 리액트에 내장되어 있는 Hooks 중에서 useContext라는 Hook을 사용하면, 함수 컴포넌트에서 Context를 아주 편하게 사용할 수 있습니다.

#### components/ColorBox.js

```javascript
import { useContext } from 'react';
import ColorContext from '../contexts/color';

const ColorBox = () => {
    const { state } = useContext(ColorContext);
    return (
        <>
            <div
                style={{
                    width: '64px',
                    height: '64px',
                    background: state.color
                }}
            />
            <div 
                style={{
                    width: '32px',
                    height: '32px',
                    background: state.subcolor
                }}
            />
        </>
    );
};

export default ColorBox;
```

> children에 함수를 전달하는 Render Props 패턴이 불편하다면, useContext Hook을 사용하여 훨씬 편하게 Context 값을 조회할 수 있습니다. 그러나 Hook은 함수 컴포넌트에서만 사용할 수 있습니다.

### static contextType 사용하기

> 클래스형 컴포넌트에서 Context를 좀 더 쉽게 사용하고 싶다면 static contextType을 정의하는 방법이 있습니다.

#### components/SelectColors.js

```javascript
import { Component } from 'react';

const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];

class SelectColors extends Component {
    static contextType = ColorContext;

    handleSetColor = color => {
        this.context.actions.setColor(color);
    };

    handleSetSubcolor = subcolor => {
        this.context.actions.setSubcolor(subcolor);
    };

    render() {
        return (
            <div>
                <h2>색상을 선택하세요.</h2>
                <div style={{ display: 'flex' }}>
                    {colors.map(color => (
                        <div
                            key={color}
                            style={{
                                background: color,
                                width: '24px',
                                height: '24px',
                                cursor: 'pointer'
                            }}
                            onClick={() => this.handleSetColor(color)}
                            onContextMenu={e => {
                                e.preventDefault();
                                this.handleSetSubcolor(color);
                            }}
                        />
                    ))}
                </div>
                <hr />
            </div>
        );
    }
}

export default SelectColors;
```

> static contextType을 정의하면 클래스 메서드에서도 Context에 넣어 둔 함수를 호출할 수 있다는 장점이 있습니다. 단점이라면, 한 클래스에서 하나의 Context밖에 사용하지 못한다는 것입니다.