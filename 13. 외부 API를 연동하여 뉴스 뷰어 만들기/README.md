# 외부 API를 연동하여 뉴스 뷰어 만들기

## axios로 API 호출해서 데이터 받아 오기

### axios

- 현재 가장 많이 사용되고 있는 자바스크립트 HTTP 클라이언트
- HTTP 요청을 Promise 기반으로 처리

#### 프로젝트 생성하기

```
$ yarn create react-app news-viewer
$ cd news-viewer
$ yarn add axios
```

#### .prettierrc

```json
{
    "singleQuote": true,
    "semi": true,
    "useTabs": false,
    "tabWidth": 2,
    "trailingComma": "all",
    "printWidth": 80
}
```