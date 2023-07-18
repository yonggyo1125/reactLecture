# 리덕스를 사용하여 리액트 애플리케이션 상태 관리하기

## 작업 환경 설정

```
$ yarn create react-app react-redux-tutorial
$ cd react-redux-tutorial
$ yarn add redux react-redux
```

#### .prettierrc

```javascript
{
    "singleQuote": true,
    "semi": true,
    "useTabs": false,
    "tabWidth": 2,
    "trailingComma": "all",
    "printWidth": 80
}
```


## UI 준비하기

- 리액트 프로젝트에서 리덕스를 사용할 때 가장 많이 사용하는 패턴은 프리젠테이셔널 컴포넌트와 컨테이너 컴포넌트를 분리하는 것입니다.
- **프리젠테이션 컴포넌트**란 주로 상태 관리가 이루어지지 않고, props만 받아 와서 화면에 UI를 보여 주기만 하는 컴포넌트를 말합니다.
- 이와 달리 **컨테이너 컴포넌트**는 리덕스와 연동되어 있는 컴포넌트로, 리덕스로부터 상태를 받아 오기도 하고 리덕스 스토어에 액션을 디스패치하기도 합니다.
- 이 패턴을 사용하면 코드의 재사용성도 높아지고 관심사의 분리가 이루어져 UI를 작성에 좀 더 집중 할 수 있습니다.


