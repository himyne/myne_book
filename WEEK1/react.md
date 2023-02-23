# 3. React

## 리액트란??

리액트는 MVC 중에서도 V(View)만 신경 쓰는 라이브러리라고 한다.

기존의 MVC, MVVM 패턴은 모델이 변화하면 뷰에 변경사항을 반영하는 방식으로 구현되었다.

하지만 데이터의 변화가 있을 때 모든 페이지가 새롭게 렌더링되는 것이 아니라 변화가 있는 곳만 렌더링한다면 확실히 성능이 뛰어날 것으로 보인다.

리액트는 결국 최대한 성능을 아끼고 편안한 사용자 경험을 제공하면서 구현하고자 개발한 프레임워크이다.

## render 함수

render() 함수는 컴포넌트가 어떻게 생겼는지를 정의한다.

## element 렌더링(카운터 예제)

```typescript
import ReactDOM from "react-dom/client";
import { App } from "./App";

function Demo({ count }: { count: number }) {
  return <p>DEMO : {count}</p>;
}

function main() {
  const element = document.getElementById("root");
  const element2 = document.getElementById("demo");

  if (!element || !element2) {
    return;
  }

  const root = ReactDOM.createRoot(element);
  const root2 = ReactDOM.createRoot(element2);

  root.render(<App />);

  let count = 0;

  root2.render(<Demo count={count} />);

  setInterval(() => {
    count += 1;
    root2.render(<Demo count={count} />);
  }, 1_000);
}

main();
```

리액트 렌더링의 장점은 변경된 부분만 업데이트 한다는 것이다.

## 리렌더링

리액트는 컴포넌트를 언제 다시 리렌더링할까?

-> state가 변할 때

```javascript
import { useState } from "react";

export function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Hello, {name || "world"}!</p>
      <p>Count: {count}</p>
      <button type="button" onClick={() => setCount(count + 1)}>
        클릭!
      </button>
    </div>
  );
}
```
