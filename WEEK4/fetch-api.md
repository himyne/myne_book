# Fetch API

Fetch API는 Promise 기반으로 구성되어 있어 비동기 처리 프로그래밍 방식에 잘 맞는 형태이다.

then, catch 체이닝으로 작성할 수도 있다는 장점도 있다.

## Fetch API 문법

```javascript
const fetchResposePromise = fetch(resource, [, init]);
```

인자 resource에는 우리가 데이터를 요청할 URL을 넣어주면 된다. 기본적으로는 GET요청으로 동작을 한다.

이 fetch함수에 데이터를 넣어 요청하면 반환되는 값은 **"Promise"**데이터 타입이고 응답 객체를 돌려준다. 그러므로 fetch를 이해하기 전에 Promise를 먼저 알아야 한다.

![프로미스 응답 객체](/images/promise-object.md.jpg)

```javascript
const fetched = fetch("http://localhost:3000/products");
//성공했을 때 then, 결과값을 첫번째 파라미터로 받을 수 있다.
fetched.then(function (result) {});
console.log(result); // result는 Response 객체이다.
//실패했을 때 catch, 실패이유를 알려준다.
fetched.catch(function (reason) {});
console.log(reason); // TypeError: Faled to ~~
```

통신에 성공을 하면 then으로 전달된 함수가 실행되고 실패하면 catch로 전달된 함수가 실행된다는 표준화된 처리를 할 수 있기 때문에 Promise를 도입한 것이다.

아래는 fetch로 GET 요청을 할 때 보편적으로 사용하는 코드이다.

```javascript
fetch('http://localhost:3000/products')
  .then((response) => response.json())
  .then((date) => console.log(data));
  .catch(function(reason){
    console.log(reason)
  })
```

POST 요청을 하고 싶을 때는 아래와 같이 작성한다.

```javascript
fetch("https://jsonplaceholder.typicode.com/posts", {
  method: "POST", // POST
  headers: {
    // 헤더 조작
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    // 자바스크립트 객체를 json화 한다.
    title: "Test",
    body: "I am testing!",
    userId: 1,
  }),
})
  .then((response) => response.json())
  .then((data) => console.log(data));
```

통신에 성공하면 then 함수가 실행되고 응답 객체를 반환한다고 했는데 그 안에는 서버와의 통신이 성공했는지 여부, 결과 등 여러가지 정보가 들어있다.

![응답 객체](/images/response-object.jpg)

### 응답 객체 속성들

- status: HTTP 상태 코드(ex - 200)
- ok: HTTP 상태 코드가 200~299일 경우 true

## Fetch - async/await

```javascript
async function main() {
  let res = await fetch("http://localhost:3000/products");
  let data = await res.json();
  const { products } = data;
  console.log(products);
}
```

위처럼 async await 키워드를 사용하여 fetch api를 사용할 수 있다.
