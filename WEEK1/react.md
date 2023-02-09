# 3. React

## 학습 키워드

- React란?
- React 컴포넌트
- React 리렌더링
- IoC(Inversion of Control)
- Library vs Framework

## 리액트란??

리액트는 MVC 중에서도 V(View)만 신경 쓰는 라이브러리라고 한다.

기존의 MVC, MVVM 아키텍쳐 등을 토대로 내가 직접 개발한 경험이 없기 때문에 뷰만 신경 쓰는 것이 어떤 이점이 있는지 피부로 와닿지는 않았다.

하지만 데이터의 변화가 있을 때 모든 페이지가 새롭게 렌더링되는 것이 아니라 변화가 있는 곳만 렌더링한다면 확실히 성능이 뛰어날거란 것은 알 수 있는 것 같다.

리액트는 결국 데이터를 

## render 함수

render() 함수는 컴포넌트가 어떻게 생겼는지를 정의한다. 

## 렌더링(카운터 예제)

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
