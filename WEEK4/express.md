# Express란?

express는 node.js의 초창기부터 있었던 아주 유명한 "서버 웹 프레임워크"이다.

node.js로 뭔가를 만들 때 기본적으로 express를 고려할 정도로 많이 사용한다. 그 이유는 express에는 웹 애플리케이션을 만들기 위한 각종 라이브러리와 미들웨어 등이 내장되어 있어서 개발하기 편하고 개발자들끼리 코드의 통일성을 높일 수 있기 때문이다.

그리고 nodeJS로 REST 서버를 편리하게 구현할 수 있게 해준다.

## Express 설치 및 개발 환경 세팅

```bash
npm init -y

npm i -D typescript
npx tsc --init
npm i -D ts-node

npm i -D eslint
npx eslint --init

npm i express
npm i -D @types/express
```

```javascript
import express from "express";

const port = 3000;

//express 실행하면 app이라는 인스턴스가 만들어짐
const app = express();

//get : 요청, "/" : 라우팅 루트, (req, res) : 콜백함수의 인자
// req(request): 요청 정보, res(response):응답 정보
app.get("/", (req, res) => {
  // 응답에 "Hello,world!"라는 정보를 담아서 보내겠다는 뜻이다.
  res.send("Hello, world!");
});

//port에는 위에서 선언한 포트번호 3000이 들어간다. 3000번 포트에서 듣고있다는 의미
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

express는 코드를 고칠때마다 서버를 다시 켜야한다. 그래서 개발할 때는 nodemon이라는 것을 사용하여 코드를 고칠때마다 자동으로 서버를 껐다켤 수 있게 하면 좋다.

대신 nodemon은 ts-node에 대한 의존성이 있으므로 ts-node가 꼭 설치되어 있어야 한다.

## URL 구조

![URL](/images/url.png)

쿼리와 자원에 대한 경로는 뒤의 HTTP 메소드에서 다룬다.

### 프로토콜이란?

컴퓨터끼리 네트워크 통신을 할 때 규약이다. 웹을 이용할 때 HTTP와 HTTPS 프로토콜을 자주 사용하는데 HTTPS는 HTTP에 보안을 더한 프로토콜이다.

컴퓨터 원격 통신에는 SSH 프로토콜이, 이메일 통신을 할 때는 SMTP 프로토콜을 사용한다. 프로토콜 별로 다른 포트번호를 갖는다.

### 도메인 주소

도메인 네임에는 원래 IP 주소가 들어가는데 알아보기 힘들기 때문에 사람이 인식할 수 있는 네트워크용 영문 주소로 변환한 것이다.

도메인 네임을 이용한다면 DNS(도메인 네임 서버)를 거쳐서 IP를 반환받아야 한다.

보통 서버에다가 도메인 네임을 붙이려면 호스팅 서비스에서 도메인 네임을 구매해야 한다.

### PORT란?

은행 고객센터터에 전화를 할 때 대표번호를 누르고 그 안에서 원하는 서비스에 따라 또 버튼을 누르고 상담을 한다.

이처럼 서버에는 포트번호 별로 포트가 나누어져 있다.

네이버에 접속할때 그냥 주소창에는 "http://www.naver.com"만 쳐도 되지만 사실 우리는 HTTP의 포트번호 80번에 들어간 것이다.

"http://www.naver.com:80"으로 쳐도 똑같이 naver에 들어갈 수 있다. HTTPS의 경우 443번임

한 서버 내에서 여러개의 포트를 켜놓을 수 있다.

+) 자세한 내용 더 추가하기

## REST API

REST API의 제약 조건으로 6가지가 있다.

- Server-client 구조
- Stateless : 무상태
- Cacheable : 캐시 처리 가능
- Layered System : 계층화
- Code-On-Demand : Optional
- Uniform Interface : 인터페이스의 일관성

하지만 보통 이 제약 조건들을 모두 충족시키지는 않고 Uniform Interface만이라도 충족하면 다른 제약 조건들을 만족시킬 수 있다.

### Uniform Interface 제약 조건

인터페이스의 일관성(Uniform Interface)도 지켜야할 제약 조건이 있다.

1. identification of resources: 자원(resource)가 URI로 식별되어야 한다.
2. manipulation of resources through representations: 자원을 조작(CRUD)할 때 HTTP 메세지에 Verb(동사)를 담아서 보낸다.
3. self-descriptive messages: 메세지는 스스로를 설명해야 한다.
4. hypermedia as the engine of application state(HATEOAS): 애플리케이션의 상태는 하이퍼링크로 전이되어야 한다.

위 제약 조건 중 1,2번만 만족해도 된다.

상품 목록에 대한 데이터로 예를 들자.

상품 목록에 상품 하나를 추가하는 경우 `/create-product` 이렇게 하면 인터페이스의 일관성의 1번 규칙에서 어긋난다. 자원은 product인데 `/create-product`라는 URI는 식별 가능하지 않다. 그리고 보통은 URI를 이루는 자원들은 동사가 아닌 명사로 이루어져 있어야 한다.

결론적으로 자원은 `/product`와 같이 작성하고 CRUD에 대해 HTTP 메서드를 사용하면 된다.

- 기본 리소스 URL: /products

  1. Read (Collection) → GET /products ⇒ 상품 목록 확인
  2. Read (Item) → GET /products/{id} ⇒ 특정 상품 정보 확인
  3. Create (Collection Pattern 활용) → POST /products ⇒ 상품 추가 (JSON 정보 함께 전달)
  4. Update (Item) → PUT 또는 PATCH /products/{id} ⇒ 특정 상품 정보 변경 (JSON 정보 함께 전달)
  5. Delete (Item) → DELETE /products/{id} ⇒ 특정 상품 삭제

## HTTP Method (GET, POST, PUT/PATCH, DELETE)

HTTP Method는 자원(URI)에 대한 행위를 알리는 방법이다.

GET(읽기, 조회), POST(등록, 추가), PUT/PATCH(수정,업데이트), DELETE(삭제)가 대표적이고 실제로는 총 8개의 메소드가 존재한다.

- HEAD: 서버 리소스의 헤더(메타 데이터의 취득)
- OPTIONS: 리소스가 지원하고 있는 메소드의 취득
- CONNECT: 프록시 동작의 터널 접속을 변경

1. GET - 읽기

```javascript
GET / product / 1;
```

- GET요청이 성공적으로 이루어지면 JSON으로 HTTP응답 코드를 반환한다.(상태 200)
- GET요청은 주소창의 URL으로만 요청을 한다.
- 에러가 발생하면 404(Not found)나 400(Bad request)가 뜬다. 실제로 깃헙에서도 로그인 안되있을 때 레포에 접속하면 많이 본 에러이다.
- GET은 캐싱이 가능하여 데이터를 한번 더 조회해도 저장한 값을 보여주기 때문에 POST보다 속도가 빠르다.
- GET 요청은 parameter / query 두 가지의 방식이 있다.

  a. 파라미터로 받기

  주소창에 localhost:3000/user/minhye

  ```javascript
  app.get("/user/:id", (req, res) => {
    const q = req.params;
    console.log(q.id); // minhye가 출력

    res.json({ userid: q.id }); // {"userid": "minhye"}
  });
  ```

  b. 쿼리로 받기

  주소창에 localhost:3000/user/asdf?q=minhye&age=26/

  ?를 기점으로 key=value 형태로 데이터를 표현한다.

  ```javascript
  app.get("/user/:id", (req, res) => {
    const q = req.query;
    console.log(q); // minhye
    console.log(q.age); // 26

    res.json({ userid: q.q, age: q.age }); // {"userid": "minhye", "age": "26"}
  });
  ```

2. POST - 생성

```javascript
POST /product
body: {name: "example"}
Content-Type: "application/json"
```

- 새로운 리소스를 생성할 때 사용된다.
- 데이터 생성을 요청 시 요청 몸체(body)에 데이터를 같이 보내야 한다. 요청 몸체에 데이터 정보가 들어있고 URL에는 없기 때문에 조금 더 안전하다.
- 요청에 성공적으로 응답하면 몸체에 저장한 데이터와 함께 성공했다는 응답을 보낸다.

3. PUT - 교체 / PATCH - 수정

- 특정 리소스 전체를 교체할 때 사용한다.
- 요청 시 요청 몸체(body)에 교체할 데이터를 같이 보내야 한다.
- 요청에 성공적으로 응답하면 몸체에 저장한 데이터와 함께 성공했다는 응답을 보낸다.

```javascript
PUT /product/1
body : {date : "update example"}
Content-Type : "application/json"
```

4. DELETE - 삭제

- 데이터를 삭제할 때 사용한다.
- 요청 시 요청 몸체에 보내지 않아도 된다. URL에 어떠한 데이터를 삭제할지 파라미터로 받는다.
- 삭제 요청에 성공적으로 응답하면 성공 응답만 보낸다.

```javascript
DELETE / user / 1;
```
