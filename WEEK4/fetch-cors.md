# Fetch & CORS

## express 코드

```javascript
import express from "express";

const port = 3000;

const app = express();

app.get("/", (req, res) => {
  res.send("Hello, world!!");
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});

app.get("/products", (req, res) => {
  const products = [
    {
      category: "Fruits",
      price: "$1",
      stocked: true,
      name: "Apple",
    },
    {
      category: "Fruits",
      price: "$1",
      stocked: true,
      name: "Dragonfruit",
    },
    {
      category: "Fruits",
      price: "$2",
      stocked: false,
      name: "Passionfruit",
    },
    {
      category: "Vegetables",
      price: "$2",
      stocked: true,
      name: "Spinach",
    },
    {
      category: "Vegetables",
      price: "$4",
      stocked: false,
      name: "Pumpkin",
    },
    {
      category: "Vegetables",
      price: "$1",
      stocked: true,
      name: "Peas",
    },
  ];

  res.send({ products });
});
```

## 개발자 도구 console

```javascript
> const reponse = await fetch('http://localhost:3000/products');

> const reader = response.body.getReader();

> const chunk = await reader.read()
  {value: Uint8Array(434), done: false}
  done: false
  value: Uint8Array(434) [123, 34, 112, 114, 111, 100, 117, 99, 116, 115, 34, 58, 91, 123, 34, 99, 97, 116, 101, 103, 111, 114, 121, 34, 58, 34, 70, 114, 117, 105, 116, 115, 34, 44, 34, 112, 114, 105, 99, 101, 34, 58, 34, 36, 49, 34, 44, 34, 115, 116, 111, 99, 107, 101, 100, 34, 58, 116, 114, 117, 101, 44, 34, 110, 97, 109, 101, 34, 58, 34, 65, 112, 112, 108, 101, 34, 125, 44, 123, 34, 99, 97, 116, 101, 103, 111, 114, 121, 34, 58, 34, 70, 114, 117, 105, 116, 115, 34, 44, 34, …]

```

chunk.value는 Unit8Array(biteArray)이기 때문에 string으로 바꿔주어야 한다.

biteArray는 텍스트 디코더를 이용하여 string으로 바꿀 수 있다.
그리고 이것을 또 JSON.parse를 사용하여 바꿔주면 우리가 원하는 형태로 얻을 수 있다.

```javascript
JSON.parse(new TextDecoder().decode(chunk.value));
```

그런데 이 방식은 chuck의 done 속성이 true일 때까지 반복해야 하기도 하고 굉장히 복잡하다.

사실 Fetch API는 JSON을 기본 지원한다. 그래서 아래와 같이 작성하면 바로 얻을 수 있다.

```javascript
const response = await fetch("http://localhost:3000/products");
const data = await response.json();
```

지금까지는 GET 요청만 다뤘는데 다른 HTTP Method를 쓰고 싶다면 fetch함수의 두번째 인자에 추가 내용을 넣어주면 된다.

```javascript
// POST 메서드 구현 예제
async function postData(url = "", data = {}) {
  // 옵션 기본 값은 *로 강조
  const response = await fetch(url, {
    method: "POST", // *GET, POST, PUT, DELETE 등
    mode: "cors", // no-cors, *cors, same-origin
    cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
    credentials: "same-origin", // include, *same-origin, omit
    // 아래 형태는 정말 자주 쓴다.
    headers: {
      "Content-Type": "application/json",
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
    redirect: "follow", // manual, *follow, error
    referrerPolicy: "no-referrer", // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
    //JSON 파일 넘겨주는 부분
    body: JSON.stringify(data), // body의 데이터 유형은 반드시 "Content-Type" 헤더와 일치해야 함
  });
  return response.json(); // JSON 응답을 네이티브 JavaScript 객체로 파싱
}

postData("https://example.com/answer", { answer: 42 }).then((data) => {
  console.log(data); // JSON 데이터가 `data.json()` 호출에 의해 파싱됨
});
```

## React Demo App에서 express 서버 연결하기

```javascript
import ReactDOM from "react-dom/client";
import App from "./App";

async function main() {
  const url = "http://localhost:3000/products";
  const response = await fetch(url);
  const data = await response.json();
  const { products } = data;
  console.log(products);

  const element = document.getElementById("root");

  if (!element) {
    return;
  }

  const root = ReactDOM.createRoot(element);

  root.render(<App />);
}

main();
```

위처럼 코드를 작성하면 에러가 발생한다.

{% hint style="danger" %}
Access to fetch at 'http://localhost:3000/products' from origin 'http://localhost:8080' **has been blocked by CORS policy**: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.
{% endhint %}

에러를 잘 읽어보면 CORS 정책에 의해 막혔다고 한다. CORS 정책이 무엇인지 알아보자.

## CORS(Cross-Origin Resource Sharing)

CORS 정책은 **"웹브라우저가 가지고 있는 기본 보안 정책**이다.

더 자세히 말하자면 추가 HTTP 헤더를 사용하여, 한 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제이다.

위의 코드에서 발생했던 에러는 웹 페이지(8080포트)와 리소스를 요청한 곳(3000포트)이 다른 출처라서 브라우저 단에서 막은 것이다. 실제 개발자 도구의 Network를 확인하면 서버에 요청하고 응답을 받아오는 과정은 성공적으로 되었다는 것을 알 수 있다.

우리가 실습하는 내용에서는 react-demo-app 웹 애플리케이션이 express를 사용하여 작성한 express-demo-app의 자원에 접근할 수 있도록 http 헤더를 사용하여 권한을 부여해야 에러가 발생하지 않을 것이다.

어떻게 권한을 부여할까?

바로 REST API 서버에서 Headers에 "Access-Control-Allow-Origin" 속성을 추가하면 된다.

express는 이미 CORS 미들웨어가 있기 때문에 설치해서 바로 사용할 수 있다.

### CORS 설치 명령어

```bash
npm i cors
npm i -D @types/cors
```

### CORS 정책을 적용한 express-demo-app 코드

```javascript
import cors from "cors";

app.use(cors());
```

원래는 option들을 여러가지 잡을 수 있지만 기본으로 cors()만 작성해주어도 에러가 해결이 된다.

이제 express-demo-app에서 받은 데이터로 react-demo-app에서 사용할 수 있도록 해보자

{% code title="main.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
async function main() {
  const url = "http://localhost:3000/products";
  const response = await fetch(url);
  const data = await response.json();
  const { products } = data;

  const element = document.getElementById("root");

  if (!element) {
    return;
  }

  const root = ReactDOM.createRoot(element);

  root.render(
    <React.StrictMode>
      <App products={products} />;
    </React.StrictMode>
  );
}

main();
```

{% endcode %}

{% code title="App.tsx" overflow="wrap" lineNumbers="true" %}

```javascript
export default function App({ products }: { products: Product[] }) {
  return (
    <div>
      <FilterableProductTable products={products} />
    </div>
  );
}
```

{% endcode %}

이렇게 백엔드와 프론트엔드를 연결해서 사용하는 것이다.
