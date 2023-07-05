# 일정관리 웹 애플리케이션 만들기 

## 프로젝트 준비하기

### 프로젝트 생성 및 필요한 라이브러리 설치 

```
$ yarn create react-app todo-app
$ cd todo-app
$ yarn add sass classnames react-icons
```

> react-icons : 리액트에서 다양하고 예쁜 아이콘을 사용할 수 있는 라이브러리, SVG형태로 이루어진 아이콘을 리액트 컴포넌트처럼 쉽게 사용할 수 있습니다.

### Prettier 설정 

#### .prettierrc

```javascript
{
    "singleQuote": true,
    "semi": true,
    "useTabs": false,
    "trailingComma": "all",
    "printWidth": 80
}
```

#### index.css 수정

#### index.css 

```css
body {
    margin: 0;
    padding: 0;
    background: #e9ecef;
}
```

### App 컴포넌트 초기화

#### App.js

```javascript
const App = () => {
    return <div>Todo app을 만들자</div>;
};

export default App;
```

## UI 구성하기

- **TodoTemplate** : 화면을 가운데 정렬시켜 주며, 웹 타이틀(일정 관리)을 보여 줍니다. children으로 내부 JSX를 props로 받아 와사 렌더링해 줍니다.
- **TodoInsert** : 새로운 항목을 입력하고 추가할 수 있는 컴포넌트입니다. state를 통해 인풋의 상태를 관리합니다.
- **TodoListItem** : 각 할 일 항목에 대한 정보를 보여 주는 컴포넌트입니다. todo 객체를 props로 받아 와서 상태에 따라 다른 스타일의 UI를 보여 줍니다.
- **TodoList** : 배열을 props로 받아 온 후, 이를 배열 내장 함수 map을 사용해서 여러 개의 TodoListItem 컴포넌트를 변환하여 보여 줍니다.

### TodoTemplate 만들기 

- src 디렉토리에 components 디렉토리를 생성
- 그 안에 TodoTemplate.js와 TodoTemplate.scss 파일을 생성