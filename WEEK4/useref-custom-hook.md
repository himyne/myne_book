# useRef & Custom Hook

## useRef

컴포넌트 생애주기란?

리액트 컴포넌트가 생성되고 제거되는 과정을 의미한다.

컴포넌트가 만들어지고(마운트) 없어질 때까지(언마운트) 동일한 객체가 유지된다.

이때 컴포넌트 객체 자체가 값이 아니고, 값을 참조하기 위한 객체이다. 그러므로 언제든지 값을 변경할 수 있다.

자바스크립트에서 객체는 참조에 의한 전달이라고 배운적이 있는데 아마 그 개념과 같은 것이지 않을까 싶다.

### useRef의 특징

상태(state)가 변경되면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하지만, 레퍼런스 객체의 현재 값(current)이 바뀌더라도 렌더링에 영향을 주지 않는다.

카운터 예제를 예로 들어보자.

```javascript
export default function TimerControl() {
  //counter변수를 useRef로 할당
  const counter = useRef(1);

  const [playing, setPlaying] = useState(false);
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("Effect");
  }, []);

  const handleClick = () => {
    // 버튼을 누르면 useRef로 할당된 변수 counter의 값이 증가하도록 함
    counter.current += 1;
    // SetPlaying(!playing);
  };

  return (
    <div>
      <p>{counter.current}</p>
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

토글 버튼을 누를 때마다 counter.current의 값이 1씩 증가하지만 화면이 새로 렌더링 될때까지 화면에서는 변화가 없다.

어떤 경우에 useRef를 사용할까??

1. 컴포넌트가 사라질 때까지 동일한 값을 써야 하는 경우.

```javascript
import { useRef } from "react";

type CheckBoxFieldProps = {
  label: string,
  inStockOnly: boolean,
  setInStockOnly: (value: boolean) => void,
};

export default function CheckBoxField({
  label,
  inStockOnly,
  setInStockOnly,
}: CheckBoxFieldProps) {
  const id = useRef(`checkbox-${label}`.replace(/ /g, "-").toLowerCase());

  const handleChange = () => {
    setInStockOnly(!inStockOnly);
  };

  return (
    <div>
      <input
        type="checkbox"
        id={id.current}
        checked={inStockOnly}
        onChange={handleChange}
      />
      <label htmlFor={id.current}>{label}</label>
    </div>
  );
}
```

예를 들어 지난 예제에서 CheckBoxField의 토글 input의 라벨 값은 상태에 따라 변할 필요가 없다.

2. (특히 useEffect 등과 함께 쓰면서 만나게 되는) 비동기 상황에서 현재 값을 제대로 쓰고 싶은 경우.

```javascript
useEffect(() => {
  setTimeout(() => {
    console.log(filterText);
  }, 5_000);
}, []);
```

이렇게 filterText를 5초뒤에 출력하는 비동기 함수를 만들었다고 가정하자.

우리가 입력창에 입력한 값을 5초뒤에 출력하고 싶은건데 막상 실행시켜보면 기대한 대로 출력이 되지 않는다.

useEffect 함수를 시작할 때 filterText에 아무것도 없었기 때문에 그 값이 들어가서 빈 값만 출력이 된다.

이러한 문제를 해결하고 싶을 때 useRef를 사용하면 된다.

```javascript
const query = useRef("");

useEffect(() => {
  query.current = filterText;
}, [filterText]);
```

사용할 일은 거의 없다고 하지만 알고 있도록 하자.

## Custom Hook

Custom Hook은 로직을 재사용하기 위한 제일 쉬운 방법이다. 관심사를 분리할 수도 있다.

```javascript
import { useState } from "react";

import ProductTable from "./ProductTable";
import SearchBar from "./SearchBar";

import Product from "../types/Product";

import filterProducts from "../util/filterProducts";

function useProductsFilter(products: Product[]) {
  const [filterText, setFilterText] = useState < string > "";
  const [inStockOnly, setInStockOnly] = useState < boolean > false;

  const filteredProducts = filterProducts(products, {
    filterText,
    inStockOnly,
  });
  return {
    filterText,
    setFilterText,
    inStockOnly,
    setInStockOnly,
    filteredProducts,
  };
}

type FilterableProductTableProps = {
  products: Product[],
};

export default function FilterableProductTable({
  products,
}: FilterableProductTableProps) {
  const {
    filterText,
    setFilterText,
    inStockOnly,
    setInStockOnly,
    filteredProducts,
  } = useProductsFilter(products);

  return (
    <div className="filtered-products-container">
      <SearchBar
        filterText={filterText}
        setFilterText={setFilterText}
        inStockOnly={inStockOnly}
        setInStockOnly={setInStockOnly}
      />
      <ProductTable products={filteredProducts} />
    </div>
  );
}
```

이런 식으로 hook을 만들어서 상태를 관리하는 변수들의 관심사를 분리할 수 있다.

## Hook 규칙

Hook 호출은 규칙이 있어서 단순하게 쓰도록 노력해야 한다.

1. Function Component 바로 안쪽(함수의 최상위)에서만 호출.

조건문, 반복문, 중첩 함수 등 내부에서 Hook을 호출하면 안된다.

2. Function Component 내부 또는 Custom Hook안에서만 호출.

아래처럼 콜백함수나 조건문 안에서 Hook을 호출하는 실수를 할 수 있다.

```javascript
if (playing) {
  const products = useFetchProducts();
  console.log(products);
}
```
