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