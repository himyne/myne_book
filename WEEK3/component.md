# React로 사고하기

[Thinking in React](https://beta.reactjs.org/learn/thinking-in-react) 문서에서 소개한 UI를 구현하는 5단계 중 2단계만 먼저 살펴본다.

1단계. UI를 컴포넌트 계층구조로 쪼개는 것(컴포넌트 트리같은 것)
2단계. 리액트를 정적인 버전으로 만들기

## 시작에 앞서서 필요한 것들

### 1. 데이터

백엔드에서 [JSON](https://himyne.github.io/deepdive/deepdive-43/#02-json) 형태의 데이터를 돌려주는 API를 제공한다고 가정한다.

- [REST API](/WEEK3/rest-api-graphql.md)

  GET, POST, PUT/PATCH, DELETE (CRUD) => Resource 중심

  ![Resource](/images/rest-api-resource.jpg)

  리소스는 위 그림과 같은 데이터를 의미한다.

- [GraphQL](/WEEK3/rest-api-graphql.md)

  그래프 자료구조

  Query에서 얻고자 하는 것을 지정한다.

  Query(Read), Mutation(Command: Create, Update, Delete), Subscription(Event)

### 2. mockup

디자이너가 만들어 놓은 UI의 모형을 뜻한다.

그리고 이 데이터와 목업을 가지고 사용자가 볼 수 있도록 UI를 구성한다.

React는 선언형(HTML과 유사한 모양의 DSL을 사용)으로 UI를 구성할 수 있다.

UI를 한번 선언해두면 안에 있는 내용이 바꼈을 때 자동으로 업데이트가 가능하다.

+) 선언형 프로그래밍 공부

## 1단계 - UI를 컴포넌트 계층 구조로 쪼개기

### 리액트의 특징

> 컴포넌트를 기반으로 한다.

- 캡슐화된 간단한 컴포넌트들을 모아서 복잡한 UI를 만들어낸다.
  자동차의 여러 부품들을 조합하다보면 다양한 제품을 만들 수 있는 것과 비슷하다.

### 컴포넌트를 나누는 기준

1. 단일 책임 원칙(SRP) : SOLID 원칙 중에서 첫번째 원칙

   한가지 컴포넌트가 한가지 일만 하도록 하는 것이 좋다.

2. CSS

   보통은 html 코드가 길어지는 경우가 많기 때문에 이를 해결하기 위해서 이미 우리가 알고 있는 css의 class 문법을 재활용한다.

   ```html
   <div class="product">
     <div class="thumbnail">
       <div class="price"></div>
     </div>
   </div>
   ```

3. Atomic Design

4. Information Architecture

   => 실제로 가장 많이 쓴다, 백엔드를 아예 고치는 방법이다.

+) Information Architecture 공부, 컴포넌트를 나누는 기준 문서 더 찾아보기

## 2단계 - 정적 페이지 만들기

```javascript
[
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  { category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" },
  { category: "Fruits", price: "$2", stocked: false, name: "Passionfruit" },
  { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" },
  { category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin" },
  { category: "Vegetables", price: "$1", stocked: true, name: "Peas" },
];
```

![리액트 공식 문서 mockup](/images/thinking-in-react-mockup.jpg)

![강의에서 만든 demo 화면](/images/react-demo-app.jpg)

아래는 매우 길고 불편하지만 위 데이터와 mockup을 가지고 대강의 틀을 만든 코드이다.

```javascript
type Product = {
  category: string,
  price: string,
  stocked: boolean,
  name: string,
};

const products: Product[] = [
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  { category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" },
  { category: "Fruits", price: "$2", stocked: false, name: "Passionfruit" },
  { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" },
  { category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin" },
  { category: "Vegetables", price: "$1", stocked: true, name: "Peas" },
];

export default function App() {
  const categories = products.reduce(
    (acc, product) =>
      acc.includes(product.category) ? acc : [...acc, product.category],
    []
  );
  return (
    <div className="filtered-products-container">
      <div className="search-bar">
        <div>
          <input type="text" placeholder="Search..." />
        </div>
        <div>
          <input type="checkbox" id="only-stock" />
          <label htmlFor="only-stock">Only show products in stock</label>
        </div>
      </div>
      <table className="product-table">
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <th colSpan={2}>{categories[0]}</th>
          </tr>
          {products
            .filter((product) => product.category === categories[0])
            .map((product) => (
              <tr key={product.name}>
                <td>{product.name}</td>
                <td>{product.price}</td>{" "}
              </tr>
            ))}
          <tr>
            <th colSpan={2}>{categories[1]}</th>
          </tr>
          {products
            .filter((product) => product.category === categories[1])
            .map((product) => (
              <tr key={product.name}>
                <td>{product.name}</td>
                <td>{product.price}</td>{" "}
              </tr>
            ))}
        </tbody>
      </table>
    </div>
  );
}
```

모든 코드가 App파일 안에 담겨있는데 이제 이 긴 코드들을 컴포넌트로 쪼개서 리팩토링을 해줄 것이다.

먼저 아래 표시한 부분 코드의 컴포넌트를 따로 빼줄 것이다.

![첫 번째 컴포넌트 분리](/images/first-component-separation.jpg)

App 파일에서 지정해둔 Product 타입들도 따로 폴더를 만들어서 파일 분리 해준다.

{% code title="./types/Product.ts" overflow="wrap" lineNumbers="ture" %}

```javascript
interface Product {
  category: string;
  price: string;
  stocked: boolean;
  name: string;
}

export default Product;
```

{% endcode %}

{% code title="ProductsInCategory.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import Product from "./types/Product";

type ProductsInCategoryProps = {
  category: string,
  products: Product[],
};

export default function ProductsInCategory({
  category,
  products,
}: ProductsInCategoryProps) {
  const productsInCategory = products.filter(
    (product) => product.category === category
  );
  return (
    <>
      <tr>
        <th colSpan={2}>{category}</th>
      </tr>
      {productsInCategory.map((product) => {
        <tr key={product.name}>
          <td>{product.name}</td>
          <td>{product.price}</td>{" "}
        </tr>;
      })}
    </>
  );
}
```

{% endcode %}

ProductsInCategory.tsx 파일도 components 폴더에 따로 만들어준 뒤 분리해주었다.

그리고 map을 이용하여 반복을 할 때는 key가 필요하다.

{% code title="App.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import ProductsInCategory from "./components/ProductsInCategory";

import Product from "./types/Product";

const products: Product[] = [
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  { category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" },
  { category: "Fruits", price: "$2", stocked: false, name: "Passionfruit" },
  { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" },
  { category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin" },
  { category: "Vegetables", price: "$1", stocked: true, name: "Peas" },
];

export default function App() {
  const categories = products.reduce(
    (acc, product) =>
      acc.includes(product.category) ? acc : [...acc, product.category],
    []
  );
  return (
    <div className="filtered-products-container">
      <div className="search-bar">
        <div>
          <input type="text" placeholder="Search..." />
        </div>
        <div>
          <input type="checkbox" id="only-stock" />
          <label htmlFor="only-stock">Only show products in stock</label>
        </div>
      </div>
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
              product={products}
            />
          ))}
        </tbody>
      </table>
    </div>
  );
}
```

{% endcode %}

남은 App파일의 `<tbody>` 내부가 조금 깔끔해졌다.

이제 ProductInCategory 내부에서 아래 그림처럼 컴포넌트를 분리해줄 것이다.

![두 번째 컴포넌트 분리](/images/second-component-separation.jpg)

{% code title="ProductRow.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import Product from "../types/Product";

type ProductRowProps = {
  product: Product,
};

export default function ProductRow({ product }: ProductRowProps) {
  return (
    <tr>
      <td>{product.name}</td>
      <td>{product.price}</td>
    </tr>
  );
}
```

{% endcode %}

{% code title="ProductCategoryRow.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
export default function ProductCategoryRow({ category }: { category: string }) {
  return (
    <tr>
      <th colSpan={2}>{category}</th>
    </tr>
  );
}
```

{% endcode %}

{% code title="ProductsInCategory.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import Product from "../types/Product";
import ProductCategoryRow from "./ProductCategoryRow";
import ProductRow from "./ProductRow";

type ProductsInCategoryProps = {
  category: string,
  products: Product[],
};

export default function ProductsInCategory({
  category,
  products,
}: ProductsInCategoryProps) {
  const productsInCategory = products.filter(
    (product) => product.category === category
  );
  return (
    <>
      <ProductCategoryRow category={category} />
      {productsInCategory.map((product) => (
        <ProductRow key={product.name} product={product} />
      ))}
    </>
  );
}
```

{% endcode %}

이제 App 파일에서 ProductTable을 분리해줄 것이다.

![세 번째 컴포넌트 분리](/images/third-component-separation.jpg)

{% code title="App.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import ProductsInCategory from "./components/ProductsInCategory";

import Product from "./types/Product";

const products: Product[] = [
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  { category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" },
  { category: "Fruits", price: "$2", stocked: false, name: "Passionfruit" },
  { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" },
  { category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin" },
  { category: "Vegetables", price: "$1", stocked: true, name: "Peas" },
];

function ProductTable({ products }: { products: Product[] }) {
  const categories = products.reduce(
    (acc: string[], product: Product) =>
      acc.includes(product.category) ? acc : [...acc, product.category],
    []
  );

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

export default function App() {
  return (
    <div className="filtered-products-container">
      <div className="search-bar">
        <div>
          <input type="text" placeholder="Search..." />
        </div>
        <div>
          <input type="checkbox" id="only-stock" />
          <label htmlFor="only-stock">Only show products in stock</label>
        </div>
      </div>
      <ProductTable products={products} />
    </div>
  );
}
```

{% endcode %}

이제 컴포넌트 분리에 익숙해졌으니 한 번에 최종적으로 아래 그림처럼 컴포넌트 분리를 해줄 것이다.

![컴포넌트 분리 완료](/images/final-component-separation.jpg)

## 분리 완성된 코드

{% code title="App.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import FilterableProductTable from "./components/FilterableProductTable";
import Product from "./types/Product";

const products: Product[] = [
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  { category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" },
  { category: "Fruits", price: "$2", stocked: false, name: "Passionfruit" },
  { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" },
  { category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin" },
  { category: "Vegetables", price: "$1", stocked: true, name: "Peas" },
];

export default function App() {
  return (
    <div>
      <FilterableProductTable products={products} />
    </div>
  );
}
```

{% endcode %}

{% code title="/types/Product.ts" overflow="wrap" lineNumbers="true" %}

```javascript
interface Product {
  category: string;
  price: string;
  stocked: boolean;
  name: string;
}

export default Product;
```

{% endcode %}

{% code title="/components/FilterableProductTable.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import ProductTable from "./ProductTable";
import SearchBar from "./SearchBar";

import Product from "../types/Product";

type FilterableProductTableProps = {
  products: Product[],
};

export default function FilterableProductTable({
  products,
}: FilterableProductTableProps) {
  return (
    <div className="filtered-products-container">
      <SearchBar />
      <ProductTable products={products} />
    </div>
  );
}
```

{% endcode %}

{% code title="/components/SearchBar.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import CheckBoxField from "./CheckBoxField";

export default function SearchBar() {
  return (
    <div className="search-bar">
      <div>
        <input type="text" placeholder="Search..." />
      </div>
      <CheckBoxField label="Only show products in stock" />
    </div>
  );
}
```

{% endcode %}

{% code title="/components/CheckBoxField.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import { useRef } from "react";

type CheckBoxFieldProps = {
  label: string,
};

export default function CheckBoxField({ label }: CheckBoxFieldProps) {
  const id = useRef(`checkbox-${label}`.replace(/ /g, "-").toLowerCase());

  return (
    <div>
      <input type="checkbox" id={id.current} />
      <label htmlFor={id.current}>{label}</label>
    </div>
  );
}
```

{% endcode %}

{% code title="/components/ProductTable.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import ProductsInCategory from "./ProductsInCategory";

import Product from "../types/Product";

import selectCategories from "../util/selectCategories";

type ProductTableProps = {
  products: Product[],
};

export default function ProductTable({ products }: ProductTableProps) {
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

{% endcode %}

원래 ProductsTable 파일에서는 데이터 안에서 같은 카테고리 종류들을 가져오는 연산을 했었다.

이것도 util 폴더로 분리해서 selectCategories 함수를 만들어주었다.

{% code title="/util/selectCategories.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import Product from "../types/Product";

export default function selectCategories(products: Product[]): string[] {
  return products.reduce((acc: string[], product: Product) => {
    const { category } = product;
    return acc.includes(category) ? acc : [...acc, category];
  }, []);
}
```

{% endcode %}

{% code title="/components/ProductsInCategory.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import selectProducts from "../util/selectProducts";
import ProductCategoryRow from "./ProductCategoryRow";
import ProductRow from "./ProductRow";

import Product from "../types/Product";

type ProductsInCategoryProps = {
  category: string,
  products: Product[],
};

export default function ProductsInCategory({
  category,
  products,
}: ProductsInCategoryProps) {
  const productsInCategory = selectProducts(products, category);
  return (
    <>
      <ProductCategoryRow category={category} />
      {productsInCategory.map((product) => (
        <ProductRow key={product.name} product={product} />
      ))}
    </>
  );
}
```

{% endcode %}

{% code title="/util/selectProducts.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import Product from "../types/Product";

export default function selectProducts(
  items: Product[],
  category: string
): Product[] {
  return items.filter((item) => item.category === category);
}
```

{% endcode %}

{% code title="/components/ProductCategoryRow.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
export default function ProductCategoryRow({ category }: { category: string }) {
  return (
    <tr>
      <th colSpan={2}>{category}</th>
    </tr>
  );
}
```

{% endcode %}

{% code title="/components/ProductRow.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
import Product from "../types/Product";

type ProductRowProps = {
  product: Product,
};

export default function ProductRow({ product }: ProductRowProps) {
  return (
    <tr>
      <td>{product.name}</td>
      <td>{product.price}</td>
    </tr>
  );
}
```

{% endcode %}
