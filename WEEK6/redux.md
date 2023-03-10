# Redux 따라하기

dispatch를 받고 selector를 이용해서 상태를 얻을 수 있다.

dispatch에 액션을 던져준다.(객체로)

external store는 순수한 자바스크립트에 가깝기 때문에 테스트하기 쉽다.

쉬린지는 객체만 넘겨줄수가 있다.

실제로 리덕스 써보면 컨텍스트로 스토어를 만들어서 넘겨줄 수 있다.

## 강의 에제

### Store 폴더

BaseStore.ts

```javascript
export type Action<Payload> = {
	type: string;
	payload?: Payload;
};

type Reducer<State, Payload> = (state: State, action: Action<Payload>) => State;

type Listener = () => void;

type Reducers<State> = Record<string, Reducer<State, any>>;

export default class BaseStore<State> {
	state: State;

	reducer: Reducer<State, any>;

	listeners = new Set<Listener>();

	constructor(initialState: State, reducers: Reducers<State>) {
		this.state = initialState;

		this.reducer = (state: State, action: Action<any>) => {
			const reducer = Reflect.get(reducers, action.type);
			if (!reducer) {
				return state;
			}

			return reducer(state, action);
		};
	}

	// Dispatch는 액션을 받는다.
	dispatch(action: Action<any>) {
		// Reducer를 실행해준다. 그러면 새로운 state가 나옴
		this.state = this.reducer(this.state, action);
		// 그러고 나서 publish하면 된다.
		this.publish();
	}

	publish() {
		this.listeners.forEach(listener => {
			listener();
		});
	}

	addListener(listener: () => void) {
		this.listeners.add(listener);
	}

	removeListener(listener: () => void) {
		this.listeners.delete(listener);
	}
}

```

store.ts

```javascript
import { singleton } from "tsyringe";
import BaseStore, { type Action } from "./BaseStore";

// Type State = {
//  count: number;
// };

const initialState = {
  count: 0,
  // 상태를 추가한다면?
  name: "Tester",
};

export type State = typeof initialState;

const reducers = {
  increase(state: State, action: Action<number>) {
    return {
      ...state,
      count: state.count + (action.payload ?? 1),
    };
  },

  decrease(state: State, action: Action<number>) {
    return {
      ...state,
      count: state.count - (action.payload ?? 1),
      // Name: `${state.name}.`,
    };
  },
};

export function increase(step?: number) {
  return { type: "increase", payload: step };
}

export function decrease(step?: number) {
  return { type: "decrease", payload: step };
}

// @쓰는것을 decorate라고 함함
@singleton()
export default class Store extends BaseStore<State> {
  constructor() {
    super(initialState, reducers);
  }
}
```

### hooks 폴더

useDispatch.ts

```javascript
import { type Action } from "../stores/BaseStore";
import { container } from "tsyringe";
import Store from "../stores/Store";

export default function useDispatch() {
  const store = container.resolve(Store);

  return (action: Action<any>) => {
    store.dispatch(action);
  };
}
```

useForceUpdate.ts

```javascript
import { useState, useCallback } from "react";

export default function useForceUpdate() {
  const [, setState] = useState({});

  return useCallback(() => setState({}), []);
}
```

useSelector.ts

```javascript
import Store, { type State } from "../stores/Store";
import { container } from "tsyringe";
import useForceUpdate from "./useForceUpdate";
import { useEffect, useRef } from "react";

type Selector<T> = (state: State) => T;

export default function useSelector<T>(selector: Selector<T>): T {
  const store = container.resolve(Store);

  const state = useRef(selector(store.state));

  const forceUpdate = useForceUpdate();

  useEffect(() => {
    const update = () => {
      // 특정 조건이 맞으면 forceUpdate
      const newState = selector(store.state);
      // Selector의 결과가 객체인 경우 처리가 필요하다.
      if (newState !== state.current) {
        forceUpdate();
        state.current = newState;
      }
    };

    store.addListener(update);

    return () => {
      store.removeListener(forceUpdate);
    };
  }, [store, forceUpdate]);

  return selector(store.state);
}
```

### Components 폴더

CountControl.tsx

```javascript
import useDispatch from "../hooks/useDispatch";
import useSelector from "../hooks/useSelector";
import { decrease, increase, type State } from "../stores/Store";

// UI : ui는 비교적 자주 바뀌는데 비즈니스 로직이 엮여있다면 테스트가 터져서 유지보수가 힘듬.

export default function CounterControl() {
  // Action을 넘겨주기 위한 dispatch는 이렇게 받고
  const dispatch = useDispatch();
  // 상태는 useSelector를 통해 받는다.
  const count = useSelector((state: State) => state.count);

  return (
    <div>
      <p>{count}</p>
      <button
        type="button"
        onClick={() => {
          dispatch(increase());
        }}
      >
        Increase
      </button>
      <button
        type="button"
        onClick={() => {
          dispatch(increase(10));
        }}
      >
        Increase 10
      </button>
      <button
        type="button"
        onClick={() => {
          dispatch(decrease());
        }}
      >
        Decrease
      </button>
      <button
        type="button"
        onClick={() => {
          dispatch(decrease(10));
        }}
      >
        Decrease 10
      </button>
    </div>
  );
}
```

Counter.tsx

```javascript
import useSelector from "../hooks/useSelector";

// UI : ui는 비교적 자주 바뀌는데 비즈니스 로직이 엮여있다면 테스트가 터져서 유지보수가 힘듬.

export default function Counter() {
  const count = useSelector((state) => state.count);

  return (
    <div>
      <p>Count: {count}</p>
    </div>
  );
}
```

NameCard.tsx

```javascript
import { useEffect } from "react";
import useSelector from "../hooks/useSelector";

// UI : ui는 비교적 자주 바뀌는데 비즈니스 로직이 엮여있다면 테스트가 터져서 유지보수가 힘듬.

export default function NameCard() {
  const name = useSelector((state) => state.name);
  // 이름 상태는 안바뀌는데 재렌더링이 되는 문제
  useEffect(() => {
    console.log("render NameCard");
  });
  return (
    <div>
      <p>Name: {name}</p>
    </div>
  );
}
```
