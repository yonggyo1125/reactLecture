# 리덕스 라이브러리 이해하기

- 리덕스는 가장 많이 사용하는 리액트 상태 관리 라이브러리 입니다.
- 리덕스를 사용하면 컴포넌트의 상태 업데이트 관련 로직을 다른 파일로 분리시켜서 더욱 효율적으로 관리할 수 있습니다.
- 컴포넌트끼리 똑같은 상태를 공유해야 할 때도 여러 컴포넌트를 거치지 않고 손쉽게 상태 값을 전달하거나 업데이트 할 수 있습니다.

## 개념 미리 정리하기

### 액션
- 상태에 어떤 변화가 필요하면 액션(action)이란 것이 발생합니다.
- 하나의 객체로 표현되며, 액션 객체는 다름과 같은 형식으로 이뤄져 있습니다.

```javascript
{
    type: 'TOGGLE_VALUE'
}
```

- 액션 객체는 type 필드를 반드시 가지고 있어야 합니다. 이 값은 액션의 이름 입니다.
- 그 외의 값들은 나중에 상태 업데이트 할 때 참고해야 할 값이며, 작성자 마음대로 넣을 수 있습니다.

```javascript
{
    type: 'ADD_TODO',
    data: {
        id: 1, 
        text: '리덕스 배우기'
    }
}

{
    type: 'CHANGE_INPUT',
    text: '안녕하세요'
}
```

### 액션 생성 함수(action creator)

> 액션 객체를 만들어 주는 함수

```javascript
function addTodo(data) {
    return {
        type: 'ADD_TODO',
        data
    };
}
```

```javascript
const changeInput = text => ({
    type: 'CHANGE_INPUT',
    text
});
```
