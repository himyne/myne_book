# React로 사고하기 #2

이전 component 강의에서 2단계 정적 페이지를 만드는 것까지 완료했다.

이번 문서에서는 Thinking In React 문서의 3,4,5단게를 다루고 동적인 페이지로 만들어볼 것이다.

![컴포넌트 계층 구조](/images/final-component-separation.jpg)

위 그림에서처럼 지금 이 앱에는 상위 컴포넌트, 하위 컴포넌트가 있다.

그렇기 때문에 어떠한 컴포넌트의 state(상태)가 바뀌면 해당 컴포넌트와 하위 컴포넌트들이 다시 렌더링된다.

일관성 있고 효율적인 코드를 위해 DRY(Don't Repeat Yourself - 중복 배제) 원칙을 따르는 SSOT(Single Source Of Truth)를 만들면 좋다.

+) DRY 원칙과 SSOT 개념 보강 필요

## React에서 State란?

state가 될 수 있는 몇가지 조건들이 존재한다.

1. 변경되어야 한다. 변경되지 않는 것은 state로 다룰 가치가 없다.

예를 들어 앞서 만든 앱에서 products는 state로 다룰 가치가 있다.
품목이나 재고가 변화할 수 있기 때문이다.

2. 부모 컴포넌트가 props를 통해 전달한다면 state가 아니다.

함수의 인자로 전달되는 props들은 전달받기만 하는 것이기 때문에 당연히 state가 될 수 없다.

3. 다른 state나 props를 이용해 계산 가능하다면 state가 아니다.

예를 들어 아래 코드를 보자

```javascript
export default function ProductTable({ products }: { products: Product[] }) {
  const categories = selectCategories(products);

  return (
    <table className="product-table">
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>
        {categories.map((category) => (
          <ProductsInCategory
            key={category}
            category={category}
            products={products}
          />
        ))}
      </tbody>
    </table>
  );
}
```

`categories`는 `products`를 props로 받아서 `selectCategories`라는 함수에 의해 계산되어진 변수이다.

그렇기 때문에 `categories`와 같은 애들도 state가 될 수 없다.

{% hint style="danger" %}

categories 같이 state가 아닌 변수를 아래처럼 사용하지 말자!

```javascript
const [categories, setCategories] = useState<string[]>([]);

      useEffect(() => {
        setCategories(selectCategories(products));
      }, [products]);
```

{% endhint %}

이 앱에서 state로 다루어야 하는 것들은 아래 그림에서 표시한 검색창과 체크박스이다.

![state](/images/state.jpg)

검색창에 검색어를 입력하거나, 체크박스도 클릭하면 화면 렌더링에 변경이 있을 것이기 때문이다.

그럼 이 상태들을 어떤 컴포넌트에서 소유하고 관리해야할까?

바로 해당 상태에 의존적인 컴포넌트를 모두 포함하는 최상위 컴포넌트가 소유해야 한다. 이것을 '[Lifting State Up](https://ko.reactjs.org/docs/lifting-state-up.html)(상태 끌어올리기)'라고 한다.

이제 본격적으로 코드에서 상태를 최상위 컴포넌트로 끌어올리는 작업을 시작해보자.

## useState

+) 문서 내용 보강 필요

## 상태 끌어올리기(Lifting State Up)

우선 토글 기능이 있는 CheckBoxField에 상태를 추가한다고 하면 아래와 같이 코드를 작성할 수 있다.

{% code title="/components/CheckBoxField.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
export default function CheckBoxField({
  label,
  inStockOnly,
  setInStockOnly,
}: CheckBoxFieldProps) {
  const id = useRef(`checkbox-${label}`.replace(/ /g, "-").toLowerCase());

  // 끌어올려야 하는 상태
  const [inStockOnly, setInStockOnly] = useState < boolean > false;

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

{% endcode %}

여기서 우리는 `CheckBoxField`함수에 있는 `inStockOnly`,`setInStockOnly`를 끌어올려서 이들을 다루는 최상위 컴포넌트인 `FilterableProductTable`로 가져와야 한다.

우리가 상위 컴포넌트에서 하위 컴포넌트로 무언가를 전달하기는 쉬운데 하위 컴포넌트에서 상위 컴포넌트로 끌어올리려면 어떻게 해야할까?

바로 함수를 props로 넘겨주는 것이다.

여기서 함수는 [일급객체](https://himyne.github.io/deepdive/deepdive-18/)이기 때문에 값으로 취급될 수 있고 인자로 넘겨주기도 가능함과 동시에 jsx 문법 중괄호 안에서도 사용이 가능하다.

{% code title="/components/FilterableProductTable.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
export default function FilterableProductTable({
  products,
}: FilterableProductTableProps) {
  //끌어올려진 상태
  const [inStockOnly, setInStockOnly] = useState < boolean > false;

  //상태에 따라 상품을 걸러주는 함수 생성 후 분리
  const filteredProducts = filterProducts(products, inStockOnly);

  return (
    <div className="filtered-products-container">
      <SearchBar inStockOnly={inStockOnly} setInStockOnly={setInStockOnly} />
      <ProductTable products={filteredProducts} />
    </div>
  );
}
```

{% code title="/util/filterProducts.ts" overflow="wrap" lineNumbers="true" %}

```javascript
import type Product from "../types/Product";

export default function filterProducts(
  products: Product[],
  inStockOnly: boolean
): Product[] {
  const filterdProducts = products.filter(
    (product) => !inStockOnly || product.stocked
  );

  return filterdProducts;
}
```

{% endcode %}

{% code title="/components/SearchBar.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
export default function SearchBar({
  filterText,
  setFilterText,
  inStockOnly,
  setInStockOnly,
}: SearchBarProps) {
  return (
    <div className="search-bar">
      <div>
        <input type="text" placeholder="Search..." />
      </div>
      <CheckBoxField
        label="Only show products in stock"
        inStockOnly={inStockOnly}
        setInStockOnly={setInStockOnly}
      />
    </div>
  );
}
```

{% endcode %}

최상위 컴포넌트로부터 실제 CheckBoxField 컴포넌트가 있는 곳까지 함수로 연결이 되었다. (FilterableTable => SearchBar => CheckBoxField)
