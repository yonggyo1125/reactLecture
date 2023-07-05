# 컴포넌트 스타일링 

## 프로젝트 준비하기 

```
$ yarn create react-app styling-react
$ cd styling-react
$ yarn start 
```

## Sass 사용하기

- Sass(Syntactically Awesome Style Sheets)(문법적으로 매우 멋진 스타일시트)는 CSS 전처리기로 복잡한 작업을 쉽게 할 수 있도록 해 주고, 스타일 코드의 재활용성을 높여 줄 뿐만 아니라 코드의 가독성을 높여서 유지 보수를 더욱 쉽게 해 줍니다.
- Sass에서는  두 가지 확장자 .scss와  .sass를 지원합니다.
- Sass가 처음 나왔을 떄는 .sass 확장자만 지원되었으나 나중에 개발자들의 요청에 의해 .scss 확장다도 지원하게 되었습니다.

#### .sass 

```sass
$font-stack: Helvetica, sans-serif
$primary-color: #333

body 
	font: 100% $font-stack
	color: $primary-color
```

```scss
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
	font: 100% $font-stack;
	color: $primary-color;
}
```

- 주요 차이점을 살펴보면, .sass 확장자는 중괄호({})와 세미콜론(;)을 사용하지 않습니다. 
- 반면 .scss 확장자는 기존 CSS를 작성하는 방식과 비교해서 문법이 크게 다르지 않습니다. 
- 보통 .scss 문법이 더 자주 사용되므로 이 책에서는 .scss 확장자를 사용하여 스타일링을 작성해 보겠습니다.
- Sass를 사용하려면 sass라는 라이브러리를 설치해 주어야 합니다. 이 라이브러리는 Sass를 CSS로 변환해 줍니다.

```
$ yarn add sass
```

#### SassComponent.scss 

```scss
// 변수 사용하기
$red: #fa5252;
$orange: #fd7e14;
$yellow: #fcc419;
$green: #40c057;
$blue: #339af0;
$indigo: #5c7cfa;
$violet: #7950f2;

// 믹스인 만들기(재사용되는 스타일 블록을 함수처럼 사용할 수 있음)
@mixin square($size) {
	$calculated: 32px * $size;
	width: $calculated;
	height: $calculated;
}

.SassComponent {
	display: flex;
	.box { // 일반 CSS에서는 .SassComponent .box와 마찬가지
		background: red;
		cursor: pointer;
		transition: all 0 3s ease-in;
		&.red { 
			// .red 클래스가 .box와 함께 사용되었을 때
			background: red;
			@include square(1);
		}
		&.orange {
			background: $orange;
			@include square(2);
		}
		&.yellow {
			background: $yellow;
			@include square(3);
		}
		&.green {
			background: $green;
			@include square(4);
		}
		&.blue {
			background: $blue;
			@include square(5);
		}
		&.indigo {
			background: $indigo;
			@include square(6);
		}
		&.violet {
			background: $violet;
			@include square(7);
		}
		&:hover {
			// .box에 마우스를 올렸을 때
			background: black;
		}
	}
}
```

#### SassComponent.js

```javascript
import './SassComponent.scss'

const SassComponent = () => {
	return (
		<div className="SassComponent">
			<div className="box red" />
			<div className="box orange" />
			<div className="box yellow" />
			<div className="box green" />
			<div className="box blue" />
			<div className="box indigo" />
			<div className="box violet" />
		</div>
	);
};

export default SassComponent;
```

#### App.js

```javascript
import { Component } from 'react';
import SassComponent from './SassComponent';

class App extends Component {
	render() {
		return (
			<div>
				<SassComponent />
			</div>
		);
	}
}

export default App;
```

### utils 함수 분리하기

> 여러 파일에서 사용될 수 있는 Sass 변수 및 믹스인을 다른 파일로 분리하여 작성한 뒤 필요한 곳에서 쉽게 불러와 사용할 수 있습니다.

#### src/styles/utils.scss

```scss
// 변수 사용하기
$red: #fa5252;
$orange: #fd7e14;
$yellow: #fcc419;
$green: #40c057;
$blue: #339af0;
$indigo: #5cfcfa;
$violet: #7950f2;

// 믹스인 만들기(재사용되는 스타일 블록을 함수처럼 사용할 수 있음)
@mixin square($size) {
	$calculated: 32px * $size;
	width: $calculated;
	height: $calculated;
}
```

#### SassComponent.scss

```scss
@import './styles/utils';
.SassComponent {
	display: flex;
	.box { 
		background: red; // 일반 CSS에서는 .SassComponent .box와 마찬가지 
		cursor: pointer;
		transition: all 0.3s ease-in;
		...
	}
}
```

### sass-loader 설정 커스터마이징하기 

- SassComponent에서 utils를 불러올때 @import './styles/utils'; 형태로 불러올 수 있지만 프로젝트에 따라 디렉토리를 많이 만들어서 구조가 깊어졌다면 상위 폴더로 한참 거슬러 올라가야 한다는 단점이 있습니다.

- 예) src/components/somefeature/ThisComponent.scss

```css
@import  '../../../styles/utils';
```

- 이 문제점은 웹팩에서 Sass를 처리하는 sass-loader 설정을 커스터마이징하여 해결할 수 있습니다.
- create-react-app으로 만든 프로젝트는 구조의 복잡도를 낮추기 위해 세부 설정이 모두 숨겨져 있습니다. 이를 커스터마이징하려면 프로젝트 디렉터리에서 yarn eject 명령어를 통해 세부 설정을 밖으로 꺼내 주어야 합니다.
- create-react-app에서는 기본적으로 Git 설정이 되어 있는데, yarn eject는 아직 Git에 커밋되지 않은 변화가 있다면 진행되지 않으니, 먼저 커밋해 주어야 합니다.

```
$ git add .
$ git commit -m 'Commit before yarn eject'
$ yarn eject
$ react-scripts eject
```

#### webpack.config.jd - sassRegex 찾기

```javascript
{
	test: sassRegex,
	exclude: sassModuleRegex,
	use: getStyleLoaders(
		{
			importLoaders: 3, 
			sourceMap: isEnvProduction
				? shouldUseSourceMap
				: isEnvDevelopment,
		},
		'sass-loader'
	),
	sideEffect: true,
},
```

- use:에 있는  'sass-loader' 부분을 지우고, 뒷부분에 concat 을 통해 커스터마이징된 sass-loader 설정을 넣어주세요.

```javascript
{
	test: sassRegex,
	exclude: sassModuleRegex,
	use: getStyleLoaders(
		{
			importLoaders: 3, 
			sourceMap: isEnvProduction
				? shouldUseSourceMap
				: isEnvDevelopment,
		}).concat({
			loader: require.resolve("sass-loader"),
			options: {
				sassOptions: {
					includePaths: [paths.appSrc + "styles"],
				},
			},
		}),
	sideEffect: true,
},
```

- 설정 파일을 저장한 후, 서버를 껐다가 재시작합니다. 이제 utils.scss 파일을 불러올 때 현재 수정하고 있는 scss 파일 경로가 어디에 위치하더라도 앞부분에 상대 경로를 입력할 필요 없이 styles 디렉토리 기준 절대 경로를 사용하여 불러올 수 있습니다.

- SassComponent.scss 파일에서 import 구문을 다음과 같이 수정합니다.

```javascript
@import 'utils.scss';
```

- 새 파일을 생성할 때마다 utils.scss를 매번 이렇게 포함시키는 것도 번거롭기 떄문에 sass-loader의 additionalData 옵션을 설정하면 됩니다. additionalData 옵션을 설정하면 Sass 파일을 불러올 때마다 코드의 맨 윗부분에 특정코드를 포함시켜 줍니다.

- webpack.config.js를 열어서 sass-loader의 옵션에서 additionalData 필드를 다음과 같이 설정합니다.

```javascript
{
	test: sassRegex,
	exclude: sassModuleRegex,
	use: getStyleLoaders(
		{
			importLoaders: 3, 
			sourceMap: isEnvProduction
				? shouldUseSourceMap
				: isEnvDevelopment,
		}).concat({
			loader: require.resolve("sass-loader"),
			options: {
				sassOptions: {
					includePaths: [paths.appSrc + "styles"],
				},
				additionalData: "@import 'utils';",
			},
		}),
	sideEffect: true,
},
```

- 이렇게 하면 Sass에서 맨 윗줄에 있는 import 구문을 지워도 정상적으로 작동합니다.

### node_modules에서 라이브러리 불러오기


- yarn을 통해 설치한 라이브러리를 사용하는 기본적인 방법은 다음과 같이 상대 경로를 사용하여 node_modules까지 들어가서 불러오는 방법

```javascript
@import '../../../node_modules/library/styles';
```

- 그러나 이런 구조는 스타일 파일이 깊숙한 디렉터리에 위치할 경우 ../를 매우 많이 적어야 하므로 번거로울 수 있습니다. 이때는 물결 문자(~)를 사용하는 방법이 있습니다.

```javascript
@import '~library/styles';
```

- 물결 문자를 사용하면 자동으로 node_modules에서 라이브러리 디렉터리를 탐지하여 스타일을 불러올 수 있습니다.

```javascript
$ yarn add open-color include-media
```

#### utils.scss

```css 
@import '~include-media/dist/include-media';
@import '~open-color/open-color';
...
```

#### SassComponent.scss

```css
.SassComponent {
	display: flex;
	background: $oc-gray-2;
	@include media('<768px') {
		background: $oc-gray-9;
	}
	...
}
```


## CSS Module

- \[파일이름\]_\[클래스 이름\]__\[해시값\] 형태로 자동으로 만들어서 컴포넌트 스타일 클래스 이름이 중첩되는 현상을 방지해 주는 기술입니다.
- .module.css 확장자로 파일을 저장하기만 하면 CSS Module이 적용됩니다.

#### CSSModule.module.css 

```javascript
/* 자동으로 고유해질 것이므로 흔히 사용되는 단어를 클래스 이름으로 마음대로 사용 가능 */

.wrapper {
	background: black;
	padding: 1rem;
	color: white;
	font-size: 2rem;
}

/* 글로벌 CSS를 작성하고 싶다면 */

:global .something {
	font-size: 800;
	color: aqua;
}
```

#### CSSModule.js

```javascript
import styles from './CSSModule.module.css';
const CSSModule = () => {
	return (
		<div className={styles.wrapper}>
			안녕하세요, 저는 <span className="something">CSS Module!</span>
		</div>
	);
};

export default CSSModule;
```

#### App.js

```javascript
import { Component } from 'react';
import CSSModule from './CSSModule';

class App extends Component {
	render() {
		return (
			<div>
				<CSSModule />
			</div>
		);
	}
}

export default App;
```

#### CSSModule.module.css

```javascript
/* 자동으로 고유해질 것이므로 흔히 사용되는 단어를 클래스 이름으로 마음대로 사용 가능 */

.wrapper {
	background: black;
	padding: 1rem;
	color: white;
	font-size: 2rem;
}

.inverted {
	color: black;
	background: white;
	border: 1px solid black;
}

/* 글로벌 CSS를 작성하고 싶다면 */

:global .something {
	font-size: 800;
	color: aqua;
}
```

#### CSSModule.js

```javascript
import styles from './CSSModule.module.css';

const CSSModule = () => {
	return (
		<div className={`${styles.wrapper} ${styles.inverted}`}>
			안녕하세요, 저는 <span className="something">CSS Module</span>
		</div>
	);
};

export default CSSModule;
```

### classnames 

- CSS 클래스를 조건부로 설정할 떄 매우 유용한 라이브러리입니다.

```
$ yarn add classnames
```

#### classnames 간략 사용법

```javascript
import classNames from 'classnames';

classNames('one', 'two'); // 'one two'
classNames('one', { two: true }); // 'one two'
classNames('one', { two: false }); // 'one'
classNames('one', ['two', 'three']); // 'one two three'

const myClass = 'hello';
classNames('one', myClass, { myCondition: true }); // 'one hello myCondition'
```

```javascript
const MyComponent = ({ hightlighted, theme }) => (
	<div className={classNames('MyComponent', {highlighted}, theme)}>Hello</div>
);
```

#### CSSModule.js

```javascript
import ClassNames from 'classnames/bind';
import styles from './CSSModule.module.css';

const cx = classNames.bind(styles); // 미리 styles에서 클래스를 받아 오도록 설정하고

const CSSModule = () => {
	return (
		<div className={cx('wrapper', 'inverted')}>
			안녕하세요, 저는 <span className="something">CSS Module</span>
		</div>
	);
};

export default CSSModule;
```

### Sass와 함께 사용하기

- Sass를 사용할 때도 파일 이름 뒤에 .module.scss 확장자를 사용해 주면 CSS Module로 사용할 수 있습니다. 

#### CSSModule.module.scss

```scss
/* 자동으로 고유해질 것이므로 흔히 사용되는 단어를 클래스 이름으로 마음대로 사용 가능 */

.wrapper {
	background: black;
	padding: 1rem;
	color: white;
	font-size: 2rem;
	&.inverted {
		// inverted가 .wrapper와 함께 사용되었을 때만 적용
		color: black;
		background: white;
		border: 1px solid black;
	}
}



/* 글로벌 CSS를 작성하고 싶다면 */

:global { 
	// :global {}로 감싸기 
	.something {
	font-size: 800;
	color: aqua;
}
```

```javascript
import styles from './CSSModule.module.scss';
```

### CSS Module이 아닌 파일에서 CSS Module 사용하기

- CSS Module에서 글로벌 클래스를 정의할 때 :global을 사용했던 것처럼 CSS Module이 아닌 .css/.scss 파일에서도 :local을 사용하여 CSS Module을 사용할 수 있습니다.

```javascript 
:local .wrapper {
	/* 스타일 */

}

:local {
	.wrapper {
		/* 스타일 */
	}
}
```


## styled-components

- 자바스크립트 파일 안에 스타일을 선언하는 방식 / CSS-in-JS라고 부릅니다. 이 중에서 가장 선호하는 styled-components를 알아봅니다.

```
$ yarn add styled-components
```

#### StyledComponent.js

```javascript
import styled, { css } from 'styled-components';

const Box = styled.div`
	/* props로 넣어 준 값을 직접 전달해 줄 수 있습니다. */
	background: ${props => props.color || 'blue' };
	padding: 1rem;
	display: flex;
`;

const Button = styled.button`
	background: white;
	color: black;
	border-radius: 4px;
	padding: 0.5rem;
	display: flex;
	align-items: center;
	justify-content: center;
	box-sizing: border-box;
	font-size: 1rem;
	font-weight: 600;
	
	/* & 문자를 사용하여 Sass처럼 자기 자신 선택 가능 */
	&:hover {
		background: rgba(255, 255, 255, 0.9);
	}

	/* 다음 코드는 inverted 값이 true일 때 특정 스타일을 부여해 줍니다. */
	${props =>
		props.inverted && 
		css`
			background: none;
			border: 2px solid white;
			color: white;
			&:hover {
				background: white;
				color: black;
			}
		`};
		& + button {
			margin-left: 1rem;
		}
	`;

const StyledComponent = () => {
	<Box color="black">
		<Button>안녕하세요</Button>
		<Button inverted={true}>테두리만</Button>
	</Box>
};

export default StyledComponent;
```

#### App.js 

```javascript
import { Component } from 'react';
import StyledComponent from './StyledComponent';

class App extends Component {
	render() {
		return (
			<div>
				<StyledComponent />
			</div>
		);
	}
}

export default App;
```

> styled-components와 일반 classNames를 사용하는 CSS/Sass를 비교했을 때, 가장 큰 장점은 props 값으로 전달해 주는 값을 쉽게 스타일에 적용할 수 있다는 것입니다.

### Tagged 템플릿 리터럴 

- 다음과 같은 함수를 작성하고 나서 해당 함수 뒤에 템플릿 리터럴을 넣어 준다면, 템플릿 안에 넣은 값을 온전히 추출할 수 있습니다.

```javascript
function tagged(...args) {
	console.log(args);
}

tagged`hello ${{foo: 'bar'}} ${() => 'world'}!`
```

- Tagged 템플릿 리터럴을 사용하면 템플릿 사이사이에 들어가는 자바스크립트 객체나 함수의 원본 값을 그대로 추출할 수 있습니다.
- styled-components는 이러한 속성을 사용하여 styled-components로 만든 컴포넌트의 props를 스타일 쪽에서 쉽게 조회할 수 있도록 해줍니다.

### 스타일링된 엘리먼트 만들기

```javascript
import styled from 'styled-components';

const MyComponent = styled.div`
	font-size: 2rem;
`;
```

- styled.div 뒤에 Tagged 템플릿 문법을 통해 스타일을 넣어 주면, 해당 스타일이 적용된 div로 이루어진 리액트 컴포넌트가 생성됩니다. 
- \<MYComponent\>Hello\</MyComponent\>와 같은 형태로 사용할 수 있습니다.
- div가 아닌 button이나 input에 스타일링을 하고 싶다면 styled.button 혹은 styled.input 같은 형태로 뒤에 태그명을 넣어 주면 됩니다.
- 사용해야 할 태그명이 유동적이거나 특정 컴포넌트 자체에 스타일링할 경우 다음과 같은 형태로 구현할 수 있습니다.

```javascript
// 태그의 타입을 styled 함수의 인자로 전달
const MyInput = styled('input')`
	background: gray;
`;

// 아예 컴포넌트 형식의 값을 넣어 줌
const StyledLink = styled(Link)`
	color: blue;
`;
```

> 컴포넌트를 styled의 파라미터에 넣는 경우에는 해당 컴포넌트에 className props를 최상위 DOM의 className 값으로 설정하는 작업이 내부적으로 되어 있어야 합니다.

```javascript
const Sample = ({ className }) => {
	return <div className={className}>Sample</div>;
}

const StyledSample = styled(Sample)`
	font-size: 2rem;
`;
```

### 스타일에서 props 조회하기

- styled-components를 사용하면 스타일 쪽에서 컴포넌트에게 전달된 props 값을 참조할 수 있습니다.

#### StyledComponents.js - Box 컴포넌트

```javascript
const Box = styled.div`
	/* props로 넣어 준 값을 직접 전달해 줄 수 있습니다. */
	background: ${props => props.color || 'blue'};
	padding: 1rem;
	display: flex;
`;
```

- 이렇게 만들어진 코드는 JSX에서 사용될 때 다음과 같이 color 값을 props로 넣어 줄 수 있습니다.

```
<Box color="black">...</Box>
```

### props에 따른 조건부 스타일링

- styled-components에서는 조건부 스타일링을 간단하게 props로도 처리할 수 있습니다. 

#### StyledComponent.js - Button

```javascript
import styled, { css } from 'styled-components';
/* 단순 변수의 형태가 아니라 여러 줄의 스타일 구문을 조건부로 설정해야 하는 경우에는 css를 불러와야 합니다. */
const Button = styled.button`
	background: white;
	color: black;
	border-radius: 4px;
	padding: 0.5rem;
	display: flex;
	align-items: center;
	justify-content: center;
	box-sizing: border-box;
	font-size: 1rem;
	font-weight: 600;
	
	/* & 문자를 사용하여 Sass처럼 자기 자신 선택 가능 */
	&:hover {
		background: rgba(255, 255, 255, 0.9);
	}

	/* 다음 코드는 inverted 값이 true일 때 특정 스타일을 부여해 줍니다. */
	${props =>
		props.inverted && 
		css`
			background: none;
			border: 2px solid white;
			color: white;
			&:hover {
				background: white;
				color: black;
			}
		`};
		& + button {
			margin-left: 1rem;
		}
	`;
```

- 이렇게 만든 컴포넌트는 다음과 같이 props를 사용하여 서로 다른 스타일을 적용할 수 있습니다.

```html
<Button>안녕하세요</Button>
<Button inverted={true}>테두리만</Button>
```

- 스타일 코드 여러 줄을 props에 따라 넣어 주어야 할 때는 CSS를 styled-components에서 불러와야 합니다.

### 반응형 디자인 

- 일반 CSS를 사용할 때와 같이 media 쿼리(query)를 사용하면 됩니다.

#### StyledComponent.js - Box

```javascript
const Box = styled.div`
	/* props로 넣어 준 값을 직접 전달해 줄 수 있습니다. */
	background: ${props => props.color || 'blue' };
	padding: 1rem;
	display: flex;

	/* 기본적으로는 가로 크기 1024px에 가운데 정렬을 하고 
		가로 크기가 작아짐에 따라 크기를 줄이고 768px 미만이 되면 꽉 채웁니다. */
	width: 1024px;
	margin: 0 auto;
	@media (max-width: 1024px) {
		width: 768px;
	}
	@media (max-width: 768px) {
		width: 100%;
	}
`;
```

> 일반 CSS에서 할 때와 큰 차이가 없는데, 이 작업을 함수화하면 간단하게 사용할 수 있다.

```javascript
import styled, { css } from 'styled-components';

const sizes = {
	desktop: 1024,
	tablet: 768
};

// 위에 있는 size 객체에 따라 자동으로 media 쿼리 함수를 만들어 줍니다.
const media = Object.keys(sizes).reduce((acc, label) => {
	acc[label] = (...args) => css`
		@media (max-width: ${sizes[label] / 16}em) {
			${css(...args)};
		}
	`;

	return acc;
}, {});

const Box = styled.div`
	/* props로 넣어준 값을 직접 전달해 줄 수 있습니다. */
	background: ${props => props.color || 'blue' };
	padding: 1rem;
	display: flex;
	width: 1024px;
	margin: 0 auto;
	${media.desktop`width: 768px;`}
	${media.tablet`width: 100%;`};
`;
```

