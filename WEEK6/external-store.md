# External Store

리액트의 외부에서 external store를 관리하는 방법을 배운다.

- [x] 관심사의 분리
- [x] Layered Architecture
- [x] Flux Architecture
- [x] useReducer
- [ ] useCallback

## External Store란?

일반적인 아키텍쳐에서 보면 리액트에서 오히려 UI가 가장 외부에 있고 store가 내부에 있는데 external store는 리액트 바깥에 있다는 뜻이다.

상태가 바뀌면 지금껏 useState를 썼었는데 더이상 useState가 아닌 상태가 되는 것이다.

> 외부 스토어라는 것은 우리가 구독(subscribe)하는 무언가를 의미한다. 예를 들어 리덕스 스토어, 글로벌 면수, dom 상태이다.

그럼 구독한다는 것은 무엇을 의미할까. External store에서 구독이란, 상태가 변경되었을 때 해당 변경 사항을 자동으로 감지하고 처리할 수 있도록 등록하는 것을 말한다.

리액트에서 컴포넌트 상태가 변경되면 해당 컴포넌트를 다시 렌더링하는 방식으로 상태 관리를 한다. 그런데 상태가 너무 많고 복잡해지면 컴포넌트 간에 상태 전달이 복잡해지고 유지보수도 어렵다. 이 때 external store를 이용하여 전역에서 상태를 관리하자는 것이다. 전역에서 상태를 관리하면 상태가 변경될때마다 모든 구독자들에게 알리고 업데이트를 할 수가 있게된다.

우리가 구독하는 무언가는 전역 상태 스토어의 특정 값이나 상태인 것이다.

> Internal Store: props, context, useState, useReducer 등 리액트가 관리하는 상태를 의미한다.

external store는 결국 리액트 외부에서 상태를 관리하겠다는 것이다. 말은 외부에서 상태를 관리하는 것이지만 실제로는 external store가 가장 안쪽에 있고 신경써야할 부분이다.

## 관심사의 분리

> 관심사 분리 : 서로 각 파일끼리 뭘하는지 알고 싶지 않다는 것

보통 리액트 애플리케이션은 여러 컴포넌트로 이루어져 있고 각 컴포넌트는 특정 역할과 책임을 가진다. 이 때 각 컴포넌트들이 다른 컴포넌트와 의존하지 않도록 구현하기 위해서 관심사의 분리를 해야한다.

관심사를 분리하는 기준에도 여러가지가 있다. 아래처럼 하나의 App이 있다고 가정하자.

### 기능별

App

- Header
- Main
- Greeting
- Counter
- Post
- PostForm
  -TextField
- Footer

이렇게 많은 파일로 나뉘지만 기능은 다 다르다.

위 앱은 기능으로만 컴포넌트들을 분리했지만 이외에도 설계(아키텍처) 관점에서 관심사를 분리할 수도 있다.

### 아키텍쳐 관점

흔히 사용되는 Layered Architecture에서는 사용자한테 가까운거(ui)와 먼 거(business logic)를 기준으로 관심사를 구분한다.

사람 - 스위치 -> 모터 -> 바퀴

(ui) (비즈니스 로직)

- 스위치의 관심사 : 켜고 껐을 때 동작하는지 안하는지만 신경쓴다.
- 모터 : 전류가 흐르면 돌기만 하면된다.

### Layerd Architecture

> 여러 레이어를 분리하여 레이어마다 해야 할 역할을 정의해놓은 구조이다. 구성되는 계층의 숫자에 따라 N 계층 아키텍처라고도 한다.

일반적으로 4개의 레이어로 구분하여 4-Tier Layered Architecture라고도 하는데 비교적 단순한 애플리케이션이면 3개로 나눌 수도 있다.

- Presentation Layer : 사용자 인터페이스(ui)가 주 관심사이다. 비즈니스 로직이 어떻게 수행되는지는 알 필요가 없다.
- Business Layer : persistence 계층에서 데이터를 가져와서 비즈니스 로직을 수행하고 그 결과를 presentatoin 계층으로 전달한다.
- Persistence Layer : 데이터 출처와 그 데이터를 가져오고 다루는 것이 주 관심사이다.
- Database Layer : 데이터베이스가 위치함

이 모든 계층을 정확히 지키는 것이 중요한게 아니라 이렇게 분리를 하는 이유를 잘 이해하고 사용하는 것이 중요한 것이다. 그렇기 때문에 설득력 있는 구조라면 어떻게 설계해도 괜찮다고 한다.

레이아웃 아키텍쳐에서 각각 나누어진 계층들은 수직적으로 배치된다. 이것 또한 주요한 특징 중 하나이다. 이 구조에서 특정 레이어는 바로 하위 레이어에만 연결되고 특정 레이어는 다른 레이어의 내부 동작을 모르게 된다.

즉, 각 계층은 캡슐화되어있고 단일 책임을 갖는다.

### 프로세스 관점

거대한 프로그램이 아니라면 input -> process -> output 3단계로 코드를 구분만 해도 코드를 이해하고 유지보수 하는데 도움이 된다. 하나의 output은 다시 사용자에게

what's your name?

input => 입력

process => Hello, [name] => 메시지

위처럼 이름을 입력하면 프로세스 부분에서 처리를 해주는데 좋은 프로그램이라면 이 프로세스 부분을 짤라내서 함수로 나누어준다.

function greeting(name:string){
return `Hello,${name}`
}

널리 알려진 MVC로 거칠게 매핑하면 아래와 같다.

Model - Process
View - Output
Controller - Input

## Flux Architecture

> flux는 단방향 데이터 흐름을 활용해 보다 예측 가능한 상태를 관리할 수 있는 웹 어플리케이션 아키텍처이다.

flux 어플리케이션은 dispatcher, store, view 세 가지 부분으로 나뉜다. 데이터는 단방향 흐름이다.

### Action

> 상태 변경 요청을 담은 자바스크립트 객체

액션 이름(type)이 담겨있고 데이터는 payload에 담긴다. store가 dispatcher에 등록해둔 콜백 함수를 통해 모든 action이 store로 전달된다.

### Dispatcher

> 모든 데이터 변경 요청이 경유하는 중앙 허브

리액트에서 View는 중앙의 dispatcher를 통해 action을 전파한다. 새로운 액션이 들어오면 처리하여 상태 저장소로 보내주는 역할이다.

- 내부에 상태 관리 로직은 없다.
- store간 의존성을 관리한다.

### Store

> 어플리케이션 상태 및 로직 컨테이너

- dispatcher에서 전달된 action을 통해 상태를 변경한다.
- 상태가 변경되면 view에 통지한다.

### View

> 상태에 따라 화면을 출력하는 역할(로봇)

- controller-view 역할 두가지를 동시에 한다. 변경 이벤트를 듣는 controller-view가 자식 view에게 새로운 데이터를 제공한다.
- store에서 전달받은 변경된 상태를 화면에 렌더링한다.
- 사용자에 의해 상태가 변경되면 또 수신하고 dispatcher에 action을 전달하기도 한다.

### flux 아키텍쳐의 장점과 한계

장점 : 데이터 흐름의 구조화 -> 유지보수/확장성 증가, 쉬운 단위 테스트

한계 : 작은 기능을 위해 초기에 많은 코드가 필요하다. 오버 프로그래밍이 될 수 있다. 이러한 한계 때문에 redux와 같은 상태 관리 도구가 등장했다.

## Redux

리덕스는 단일 store를 사용하여 좀 더 단순하다. action을 어떻게 표현하느냐가 사용성에서 큰 차이를 만들지만 상태를 ui에 반영하는 방법은 모두 같다.

```javascript
const state = {
  name: "tester",
};
// 기존 state 객체가 변경되지 않는다. 새 상태를 만들어서 씀
const nextState = { ...state, name: "New Name" };
```

reducer를 이용해서 상태를 변경해준다.

1. Action : 상태 변경 메시지 객체
2. Store : dispatch를 통해 action을 받고, 사용자가 정의한 reducer를 통해 state를 변경한다.
3. View : state를 반영하여 렌더링한다.

3단계 프로세스와 완벽히 일치하진 않지만 거칠게 매핑하면 다음과 같다.

- input - Action + dispatch
- process - reducer
- output - View

## external store 강의 코드

리액트 외부에서 상태를 관리하는 것이므로 useState를 더이상 사용하지 않는다. 예를 들어

const state = 0;

state += 1;

이제는 이런식으로 상태를 관리한다.

useState로 상태를 관리하지 않기 때문에 상태가 변해도 리렌더링이 일어나지 않을 것이다. 그렇기 때문에 강제로 렌더링을 해주는 useForceUpdate 훅을 만들어주어야 한다. 이것이 external store의 기본 아이디어!

옛날에는 forceUpdate라는 것이 있었는데 사용하지 않을 것을 권장하고 있다. 그래서 이 비슷한 것을 useReducer로 만들어줄 수도 있다. 하지만 이 예제에서는 상태가 그냥 숫자이므로 일단 useState를 사용할 것임.

```javascript
import useForceUpdate from "../hooks/useForceUpdate";

const state = {
  count: 0,
};

export default function Counter() {
  const forceUpdate = useForceUpdate();

  const handleClick = () => {
    state.count += 1;
    forceUpdate();
  };

  return (
    <div>
      <p>{state.count}</p>
      <button type="button" onClick={handleClick}>
        Increase
      </button>
    </div>
  );
}
```

```javascript
//useForceUpdate 훅
import { useState } from "react";

export default function useForceUpdate() {
  const [state, setState] = useState(0);
  // const [state, setState] = useState({})

  const forceUpdate = () => {
    setState(state + 1);
    // 굳이 1을 더해주지 않아도 setState({...state}) 로 간단하게 가능
  };

  return forceUpdate;
}
```

이게 external store의 기본 아이디어 코드이다.

아래처럼 더 간단히 바꿔줄 수 있다.

대신 useCallback을 사용하면 함수가 매번 바뀌는게 아니라 항상 같은게 된다. useEffect와 비슷하게 [] 안에 함수가 바뀌면 useCallback안의 콜백함수도 새롭게 호출된다.

useCallback을 왜씀??

=> forceUpdate를 다른 데서 사용할 때 useEffect(~~~, [forceUpdate]) 이런식으로 쓸 수가 있다. forceUpdate 함수가 바뀌면 따로 어떤 동작을 해달라는 것인데 지금 예제에서는 항상 같다.

```javascript
import { useState, useCallback } from "react";

//이 훅은 useCallback 함수가 return 값이다.
export default function useForceUpdate() {
  const [, setState] = useState({});

  //항상 새 객체가 들어오므로 인자로 {}만 들어와도 상관이 없음
  return useCallback(() => setState({}), []);
}
```

```javascript
import useForceUpdate from "../hooks/useForceUpdate";

// Business logic : 크게 잘 안바뀌기 때문에 테스트 유지보수에 좋음
// 리액트와 상관없는 부분(화면에 어떻게 보이던 알빠냐)

const state = {
  count: 0,
};

// 예시에서 모터와 같은 역할 -> 비즈니스 로직
function increase() {
  // state.count += 1;
  state.count += Math.random() * 10;
}

// UI : ui는 비교적 자주 바뀌는데 비즈니스 로직이 엮여있다면 테스트가 터져서 유지보수가 힘듬.

export default function Counter() {
  const forceUpdate = useForceUpdate();

  const handleClick = () => {
    // 이 ui 로직에서는 increase 함수가 랜덤으로 수를 증가시키는지 하나를 증가시키는지지 알 필요가 없음
    // => 관심사의 분리
    increase();
    forceUpdate();
  };

  return (
    <div>
      <p>{state.count}</p>
      <button type="button" onClick={handleClick}>
        Increase
      </button>
    </div>
  );
}
```

UI가 굉장히 많이 바뀔 수 있는데 그럴때마다 비즈니스 로직이 묶여있다면 테스트하기가 힘들다. 관심사 분리 필수!!

## useReducer

```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init);

//reducer는 이러한 함수임
function reducer(state) {
  return state + 1;
  // 상태의 초기값이 있으면
  // return {...state, x: x + 1};
}
```

useReducer는 useState의 대체 함수이다. 아래는 useState로 만들었던 counter를 reducer를 사용해서 만든 코드이다.

```javascript
const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  // dispatch에 action type을 전달
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
    </>
  );
}
```

useReducer의 첫 번째 인자로 reducer함수를 전달해주고 두 번째 인자로 리듀서의 기본값인 {count: 0}을 넣어준다.

dispatch함수에 action을 전달하면 리듀서 함수가 호출되는 구조이다.

상태를 어떻게 바꾸는지에 대한 비즈니스 로직이 리액트 컴포넌트 외부로 분리될 수 있다는 것이 장점이다.

## useCallback

useCallback을 이용하면 함수가 변경될 때만 새로 렌더링 해줄 수가 있다.
그렇기 때문에 특정 함수를 새로 만들지 않고 재사용하고 싶을 때 사용한다.
