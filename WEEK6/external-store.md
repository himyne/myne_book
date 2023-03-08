# External Store

리액트의 외부에서 external store를 관리하는 방법을 배운다.

- [ ] 관심사의 분리
- [ ] Layered Architecture
- [ ] Flux Architecture
- [ ] useReducer
- [ ] useCallback

커다란 시스템은 작은 부품을 모아서 만드는 것이다.

App

- Header
- Main
- Greeting
- Counter
- Post
- PostForm
  -TextField
- Footer

이렇게 많은 파일로 나뉘지만 기능이 다 다르다.
관심사 분리 => 서로 각 파일끼리 뭘하는지 알고 싶지 않다는 것

위 외에도 설계(아키텍처) 관점에서 보는 것이 있다.
사용자한테 가까운거(ui)와 먼 거 기준으로 구분

사람 - 스위치 -> 모터 -> 바퀴
(ui) (비즈니스 로직)

스위치의 관심사 : 켜고 껐을 때 동작하는지 안하는지만 신경쓴다.
모터 : 전류가 흐르면 돌기만 하면된다.
이런 식으로 다 다른 관심사를 가지는 것

input -> process -> output

what's your name?

input

process => hello, [name] => 메시지
//좋은 프로그램이라면 이 부분을 짤라낸다
function greeting(name:string){
return `Hello,${name}`
}

output

## external store

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
    //setState({...state}) 로 해도 가능
  };

  return forceUpdate;
}
```

이게 external store의 기본 아이디어이다.

아래처럼 더 간단히 바꿔줄 수 있다.

```javascript
import { useState, useCallback } from "react";

export default function useForceUpdate() {
  const [state, setState] = useState(0);

  return useCallback(() => {
    setState({});
  }, []);
}
```

```javascript
import useForceUpdate from "../hooks/useForceUpdate";

// Business logic : 크게 잘 안바뀌기 때문에 테스트 유지보수에 좋음
// 리액트와 상관없는 부분(화면에 어떻게 보이던 알빠냐)

const state = {
  count: 0,
};

// 예시에서 모터와 같은 역할
function increase() {
  state.count += Math.random() * 10;
}

// UI : ui는 비교적 자주 바뀌는데 비즈니스 로직이 엮여있다면 테스트가 터져서 유지보수가 힘듬.

export default function Counter() {
  const forceUpdate = useForceUpdate();

  const handleClick = () => {
    // 이 ui 로직에서는 increase 함수가 랜덤으로 수를 증가시키는지 알 필요가 없음
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
