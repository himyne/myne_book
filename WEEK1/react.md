# 3. React

## 렌더링(카운터 예제)

```typescript
import ReactDOM from 'react-dom/client';
import {App} from './App';

function Demo({count}: {
	count: number;
}) {
	return (
		<p>DEMO : {count}</p>
	)
}

function main() {
	const element = document.getElementById('root');
	const element2 = document.getElementById('demo');

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
		<p>Hello, {name || 'world'}!</p>
    <p>Count: {count}</p>
    <button type="button" onClick={() =>setCount(count+1)}>
      클릭!
    </button>
    </div>
  );
}
```

## 학습 키워드

- React란?
- React 컴포넌트
- React 리렌더링
- IoC(Inversion of Control)
- Library vs Framework
