# 2. TypeScript

## 학습 키워드

- REPL
- TypeScript
  - Interface vs Type
  - 타입 추론
  - Union Type vs Intersection Type
  - Optional Parameter

## REPL

REPL(콘솔 환경)을 쓰고 싶다면 ts-node를 실행하면 된다.

```bash
npx ts-node
```

ts-node는 node_modules에 설치되어 있지 않으므로 따로 설치를 해주어야 한다.

## 타입스트립트의 타입

### 기본적인 타입

자바스크립트를 이용할 때는 typeof 연산자를 이용해서 타입을 추론하곤 했었다.

대표적으로 `string`, `number`, `boolean` 등이 있다.

### 배열

그렇다면 배열의 타입은 어떻게 지정할까?

예를 들어 `[1, 2, 3]`과 같이 숫자로만 이루어진 배열이면 `number[]` 구문을 사용한다.

### any 타입

타입 검사 오류가 발생하는 것을 원하지 않을 때 사용할 수 있다. 그러므로 아무 타입이나 다 들어올 수 있다.

## 타입 지정

### 변수/객체

```typescript
let name: string;
let age: number;

name = "홍길동";
age = "13";

let human: {
  name: string;
  age: number;
};

human = { name: "홍길동", age: 13 };
```

일반 변수나 객체 내부까지 type을 정의할 수 있다.
type 별칭이나 interface를 사용하여 계속 재사용이 가능하다.

### 배열 타입 지정

```typescript
let numbers: number[];

numbers = [1, 2, 3];
```

기본적으로는 위처럼 사용하면 되지만 조금 더 엄격하게 타입을 관리하고 싶다면 Tuple을 쓴다. 배열 개수와 타입까지 지정할 수 있다.

```typescript
let anythings: any[];
let anythings: ["hp", 256];

let pair: [string, number];
pair = ["hp", 256];
```

### 인터페이스 확장하기

```typescript
interface Animal {
  name: string;
}

interface Bear extends Animal {
  honey: boolean;
}

const bear = getBear();
bear.name;
bear.honey;
```

### 교집합을 통하여 타입 확장하기

```typescript
type Animal = {
  name: string;
};

type Bear = Animal & {
  honey: Boolean;
};

const bear = getBear();
bear.name;
bear.honey;
```

### 함수 타입 지정

함수 반환값과 인자에도 타입 지정이 가능하다.

```typescript
function getMutiply(x: number, y: number): number {
  return x * y;
}
```

### 유니온 타입

```typescript
let y: true | false;

y = true;
y = false;
```

유니온 타입을 이용하면 서로 다른 두개 이상의 타입들을 사용할 수 있다.
아래와 같이 유용하게 사용 가능하다. 주로 함수에서 많이 사용한다.

```typescript
type Category = "food" | "toy" | "bag";
let c: Category;

c = "toy";
c = "bread"; // error

function fetchProducts({ category }: { category: Category }) {
  console.log(`Fetch ${category}`);
}

fetchProducts({ category: "food" }); // Fetch food
fetchProducts({ category: "bread" }); // error
```

### 옵셔널 연산자

기본적으로 함수에 인자 값이 안들어오면 undefined로 설정하고 싶을 때 사용한다.

```typescript
function add(x: number, y?: number): number {
  return x + (y || 0);
}
```

인자 값이 안들어왔을 때 대안으로 더 좋은 방법으로는 인자에 기본값을 설정하는 것이다.

```typescript
function add(x: number, y: number = 0): number {
  return x + y;
}

function greeting(name?: string): string {
  return `Hello, ${name || "world"}`;
}
```

Optional Parameter는 매개변수가 객체일 때 자주 활용된다. (나이가 있을수도 있고 없을수도 있기 때문)

```typescript
function greeting({ name, age }: { name: string; age?: number }): string {
  return age ? `${name} (${age})` : name;
}
```

## 코드 예시

```typescript
//App.tsx
export function App({ name }: { name?: string }) {
  return <p>Hello, {name || "world"}!</p>;
}
//main.tsx
import ReactDOM from "react-dom/client";
import { App } from "./App";

const element = document.getElementById("root");

if (element) {
  const root = ReactDOM.createRoot(element);

  root.render(<App name="minhye" />);
}
```
