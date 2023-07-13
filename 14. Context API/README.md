# Context API

## Context API를 사용한 전역 상태관리 흐름 이해하기

### 일반적인 전역 상태 관리 흐름

[일반적인 전역 상태](https://raw.githubusercontent.com/yonggyo1125/reactLecture/master/14.%20Context%20API/images/image1.png)

- 리액트 애플리케이션은 컴포넌트 간에 데이터를 props로 전달하기 때문에 컴포넌트 여기저기서 필요한 데이터가 있을 때는 주로 최상위 컴포넌트인 App의 state에 넣어서 관리합니다.
- App이 지니고 있는 value 값을 F 컴포넌트와 J 컴포넌트에 전달하려면 여러 컴포넌트를 거쳐야 합니다. F의 경우 App -> A -> B -> D의 흐름이고, J의 경우 App -> H -> J의 흐름입니다.
- 실제 리액트 프로젝트에서는 더 많은 컴포넌트를 거쳐야 할 때도 있고 다루어야 하는 데이터가 훨씬 많아질 수 있으므로, 이런 방식을 사용하면 유지 보수성이 낮아질 가능성이 있습니다.

### Context API를 사용한 전역 상태 관리 흐름

[Context API를 사용한 전역 상태](https://raw.githubusercontent.com/yonggyo1125/reactLecture/master/14.%20Context%20API/images/image2.png)

- 최상위 컴포넌트에서 여러 컴포넌트를 거쳐 props로 원하는 상태와 함수를 전달했지만 Context API를 사용하면 Context를 만들어서 단 한번에 원하는 값을 받아 와서 사용할 수 있습니다.

