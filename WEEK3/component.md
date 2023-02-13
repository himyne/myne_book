# React로 사고하기

[Thinking in React](https://beta.reactjs.org/learn/thinking-in-react) 문서에서 소개한 UI를 구현하는 5단계 중 2단계만 먼저 살펴본다.

1단계. UI를 컴포넌트 계층구조로 쪼개는 것(컴포넌트 트리같은 것)
2단계. 리액트를 정적인 버전으로 만들기

## 시작에 앞서서 필요한 것들

1. 데이터

   백엔드에서 [JSON](https://himyne.github.io/deepdive/deepdive-43/#02-json) 형태의 데이터를 돌려주는 API를 제공한다고 가정한다.

   - [REST API](https://himyne.github.io/deepdive/deepdive-44/)

     GET, POST, PUT/PATCH, DELETE (CRUD) => Resource 중심

   - GraphQL

     그래프 자료구조

     Query에서 얻고자 하는 것을 지정한다.

     Query(Read), Mutation(Command: Create, Update, Delete), Subscription(Event)

2. mockup

   디자이너가 만들어 놓은 UI의 모형을 뜻한다.

+) REST API와 GraphQL 공부

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

3. Design

4. Information Architecture

   => 실제로 가장 많이 쓴다, 백엔드를 아예 고치는 방법이다.

+) Information Architecture 공부, 컴포넌트를 나누는 기준 문서 더 찾아보기

## 2단계 - 정적 페이지 만들기

아래는 매우 불편하지만 데이터를 가지고 대강의 틀을 만든 코드이다.

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
        <div>
          <label>
            <input type="checkbox" />
            Only show products in stock
          </label>
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

이제 이 긴 코드들을 컴포넌트로 쪼개서 리팩토링을 해줄 것이다.

+) 강의 1시간까지 정리내용, 리팩토링 한 컴포넌트 파일들 추가 예정
