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