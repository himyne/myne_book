# TSyringe

- [ ] TSyringe
- [ ] 의존성 주입(Dependency Injection)
- [ ] reflect-metadata
- [ ] sington (싱글톤)

주사기 - 타입스크립트에서 사용하는 의존성 주입 도구(DI)

컴포넌트(프로그램을 구성하는 요소)들을 미묘하게 조립해야하는 상황에 활용된다.

```javascript
//components/CountControl.tsx
import useCounterStore from "../hooks/useCounterStore";

export default function CounterControl() {
  const store = useCounterStore();

  const handleClickIncrease = () => {
    store.increase();
  };

  const handleClickDecrease = () => {
    store.decrease();
  };

  return (
    <div>
      <p>{store.count}</p>
      <button type="button" onClick={handleClickIncrease}>
        Increase
      </button>
      <button type="button" onClick={handleClickDecrease}>
        Decrease
      </button>
    </div>
  );
}
```

```javascript
//components/Counter.tsx
import useCounterStore from "../hooks/useCounterStore";

export default function Counter() {
  const store = useCounterStore();

  return (
    <div>
      <p>Count: {store.count}</p>
    </div>
  );
}
```

```javascript
// App.tsx
import CounterControl from "./components/CountControl";
import Counter from "./components/Counter";

export default function App() {
  return (
    <>
      <Counter />
      <Counter />
      <CounterControl />
    </>
  );
}
```

```javascript
//hooks/useCounterStore.tsx
import { useEffect } from "react";
import useForceUpdate from "./useForceUpdate";
import { container } from "tsyringe";
import Store from "../stores/Store";

export default function useCounterStore() {
  const store = container.resolve(Store);

  const forceUpdate = useForceUpdate();
  // 원래는 useEffect를 해줘야한다. 안그러면 할때마다 추가하는것이기 때문
  // 물론 set이기 때문에 중복으로 안들어가긴한다.만약에 useCallback 없었으면 계속 들어감
  useEffect(() => {
    store.addListener(forceUpdate);

    return () => {
      store.removeListener(forceUpdate);
    };
  }, [store, forceUpdate]);

  return store;
}
```

```javascript
// hooks/useForceUpdate.ts
import { useState, useCallback } from "react";

export default function useForceUpdate() {
  const [, setState] = useState({});

  return useCallback(() => setState({}), []);
}
```

```javascript
// store/Store.ts
import {singleton} from 'tsyringe';

type Listener = () => void;

// @쓰는것을 decorate라고 함
@singleton()

export default class Store {
	count = 0;

	listeners = new Set<Listener>();

	increase() {
		this.count += 1;
		this.publish();
	}

	decrease() {
		this.count -= 1;
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

## Test

jest.config.js 파일에 setupTests 경로를 추가하고 setupTests.ts 파일에 `import 'reflect-metadata';`를 넣어주면 된다.

(+ 아직 Tsyringe가 무슨 역할을 하는지 잘 모르겠음. 개념을 더 공부하기)

테스트할 때는 store가 전역이기 때문에 각 테스트마다 영향을 받는다.
=> beforeEach에서 container.clearInstances()를 해주어야 한다.
(+ 아직 container 개념이 잘 이해가 안감. 더 공부해보기)

```javascript
import { render, screen, fireEvent } from "@testing-library/react";
import { container } from "tsyringe";

import App from "./App";

const context = describe;

test("App", () => {
  render(<App />);
});

describe("App", () => {
  // 전역에서 상태를 관리하므로 클리어를 해주어야한다.
  beforeEach(() => {
    container.clearInstances();
  });
  context("when press increase button once", () => {
    test("Counter", () => {
      render(<App />);

      fireEvent.click(screen.getByText("Increase"));

      const elements = screen.getAllByText("Count: 1");

      expect(elements).toHaveLength(2);
    });
  });

  context("when press increase button twice", () => {
    test("Counter", () => {
      render(<App />);

      fireEvent.click(screen.getByText("Increase"));
      fireEvent.click(screen.getByText("Increase"));

      const elements = screen.getAllByText("Count: 2");

      expect(elements).toHaveLength(2);
    });
  });
});
```
