# React의 Hook

React 16.8 버전에서 도입이 되었다. (현재는 18버전 사용중)

{% hint style="info" %}

### Hook이란?

함수형 컴포넌트에서도 상태(state)와 생명주기(liftcycle) 기능을 사용할 수 있도록 하는 기능
{% endcode %}

hook은 기존에 있던 문제에서 몇가지를 해결한다.

기존 문제점

- HOC : 고차 컴포넌트 (Wrapper Hell)
  HOC 패턴은 기존 컴포넌트를 새로운 기능이 추가된 컴포넌트로 감싸는 방식이다. HOC 패턴은 중첩 구조가 생겨서 코드가 길어지고 가독성이 떨어지며 컴포넌트를 반환하는 함수로 구현되기 때문에 함수형 컴포넌트에서 사용이 어려웠다.

- Huge Components : 컴포넌트가 커지는 문제
- Confusing Classes : 혼란스러운 클래스

예전에는 상태를 가진 컴포넌트를 클래스 컴포넌트로 만들었었다.
그래서 클래스 컴포넌트에서의 상태 관리는 복잡한 코드를 유발했고 코드의 재사용성 또한 떨어지는 문제가 있었다.

함수형 컴포넌트에서 상태를 관리할 수 있으면 좋지 않을까 하여 리액트 훅이 등장한 것이다.

이를 통해 컴포넌트의 생명주기와 관련된 로직을 간결하게 작성할 수 있게 되었고 이제는 웬만하면 함수형 컴포넌트만 사용한다.

대표적인 Hooks

- useState → State Hook ⇒ React의 State
- useEffect ⇒ Side-effect
- useContext

## useState

useState는 가장 기본적인 Hook으로, 함수 컴포넌트에서 상태를 관리해야 할 때 사용한다.

useState는 배열을 반환하는데 첫 번째 요소는 현재 상태 값(state), 두 번째 요소는 상태를 업데이트할 때 사용하는 함수(setState)가 들어있다.

useState의 상태값이 업데이트 될 때마다 컴포넌트를 다시 렌더링한다.

### 숫자 증가 카운터 예제

```javascript
const Counter = () => {
  const [value, setValue] = useState(0);

  const handleClick = () => {
    setValue(value + 1);
  };

  return (
    <div>
      <p>카운터 : {value}</p>
      <button onclick={handleClick}>+1</button>
    </div>
  );
};
```

## useEffect

API 데이터 가져오기, 타이머 설정, DOM을 수정하는 것 등을 side effects라고 한다.

> side effect: 함수 내에서 컴포넌트 외부와 상호작용하는 모든 작업

이 side effects에는 클린업(정리)가 필요한 것과 아닌 것 두가지 종류가 있다.

### Clean-up을 이용하지 않는 effects

네트워크 요청, DOM 조작, 타이머 등은 정리가 필요없는 경우이다. 왜냐하면 실행 이후에는 신경 쓸 필요가 없기 때문이다.

```javascript
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
```

useEffect가 하는 일

useEffect Hook을 이용하면 리액트에게 컴포넌트가 렌더링 이후에 어떤 일을 수행해야하는지 알려준다. useEffect안에 우리가 넘긴 함수를 기억했다가 DOM 업데이트를 한 후에 호출한다.

컴포넌트 안에서 useEffect를 호출하는 이유

useEffect가 컴포넌트 내부에 있어야 count 변수라던지 그 어떤 prop에 접근할 수가 있기 때문이다. javascript의 클로저를 이용한 것이다.

렌더링 이후 수행 횟수

첫번째 렌더링과 이후 업데이트가 일어날 때마다 useEffect 함수 내부가 실행된다.

{% hint style="info" %}

### 버튼을 클릭하면 어떤 일이 벌어질까??

- 컴포넌트: 이봐 리액트, 내 상태를 1 로 변경해줘.
- 리액트: 상태가 1 일때의 UI를 줘.
- 컴포넌트: 여기 랜더링 결과물로 <p>You clicked 1 times</p> 가 있어.
  그리고 모든 처리가 끝나고 이 이펙트를 실행하는 것을 잊지 마: () => { document.title = 'You clicked 1 times' }.
- 리액트: 좋아. UI를 업데이트 하겠어. 이봐 브라우저, 나 DOM에 뭘 좀 추가하려고 해.
- 브라우저: 좋아, 화면에 그려줄게.
- 리액트: 좋아 이제 컴포넌트 네가 준 이펙트를 실행할거야.

() => { document.title = 'You clicked 1 times' } 를 실행하는 중.
{% endhint %}

useEffect가 어떻게 동작하는지 이해가 쉬운 설명이 있어 가져왔다.

핵심은 effect는 매 렌더링 후에 실행되며, 렌더링 시점의 prop과 state를 본다는 것이다.

> [useEffect 완벽 가이드](https://overreacted.io/ko/a-complete-guide-to-useeffect/)

위 링크가 useEffect를 정말 자세하고 이해하기 쉽게 설명해준다. 까먹을때마다 들어가서 읽어보자.

### 타이머 예제 - 강의

```javascript
import { useEffect, useState } from "react";

function Timer() {
  useEffect(() => {
    const savedTitle = document.title;
    const id = setInterval(() => {
      document.title = `Now: ${new Date().getTime()}`;
    }, 100);

    return () => {
      clearInterval(id);
      document.title = savedTitle;
    };
  });

  return <p>Playing</p>;
}

export default function TimerControl() {
  const [playing, setPlaying] = useState(false);
  const [count, setCount] = useState(0);

  // StrictMode일 때는 Effect가 두번 호출된다.
  useEffect(() => {
    console.log("Effect");
  }, []);

  const handleClick = () => {
    setPlaying(!playing);
  };

  return (
    <div>
      {playing ? <Timer /> : <p>stop</p>}
      <button type="button" onClick={handleClick}>
        Toggle
      </button>
      <p>{count}</p>
      <button
        type="button"
        onClick={() => {
          setCount(count + 1);
        }}
      >
        Increase
      </button>
    </div>
  );
}
```

타이머 예제에서 콘솔 창에 Effect가 두 번 실행되는 문제는 StrictMode로 개발하는 경우 발생한다.

예상치 못한 Side effect를 찾으려고 useEffect 등을 두 번 실행한다고 한다.

strictMode 공식 문서를 보고 이해가 잘 되지 않아 넘어갔었는데 strictmode에서 side effect를 찾기위해 어떤 것들을 하는지에 대해 더 공부해봐야겠다.

### Clean-up을 이용하는 effects

외부 데이터에 구독(subscription)을 설정해야 하는 경우, 메모리 누수가 발생하지 않도록 정리(clean-up)하는 것은 매우 중요하다.

```javascript
useEffect(() => {
  ChatAPI.subscribeToFriendStatus(props.id, handleStatusChange);
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.id, handleStatusChange);
  };
});
```

+) 이 부분은 아직 이해가 잘 되지 않아서 나중에 내용을 더 추가해야겠다.

### 의존성 배열을 이용한 fetch

useEffect의 두번째 인자로 []를 넣어주면 처음 한번만 실행되게 할 수 있다.

```javascript
const [products, setProducts] = useState<Product[]>([]);

useEffect(() => {
	const fetchProducts = async () => {
		const url = 'http://localhost:3000/products';
		const response = await fetch(url);
		const data = await response.json();
		setProducts(data.products);
	};

	fetchProducts();
}, []);
```

이렇게 빈 배열을 의존성 배열로 넣어주는 경우 주의해야 할 점이 있다.

의존성 배열에 필요한 값들이 없기 때문에 데이터가 변경되어도 다시 useEffect가 호출되지 않는다는 것을 알아야 한다.

## 실습 예제

```javascript
import {useEffect, useState} from 'react';
import FilterableProductTable from './components/FilterableProductTable';
import TimerControl from './components/Timer';

import Product from './types/Product';

export default function App() {
  const [products, setProducts] = useState<Product[]>([]);

  useEffect(() => {
    // Fetch
    const fetchProducts = async () => {
      const url = 'http://localhost:3000/products';
      const response = await fetch(url);
      const data = await response.json();
      setProducts(data.products);
    };

    fetchProducts();
    // 의존성 배열을 이용하면 한번만 가져온다.
  }, []);

  return (
    <div>
      <TimerControl />
      <hr />
      <FilterableProductTable products={products} />
    </div>
  );
}

```

아래처럼 의존성 배열에 userId 값을 넣어주면 userId가 변경될 때마다 useEffect의 호출이 다시 일어날 수 있다.

```javascript
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);
```
