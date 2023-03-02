# MSW

- [x] Service worker
- [x] MSW(Mock Service Worker)
- [x] polyfill(폴리필)

## Service Worker

> 서비스 워커는 웹 응용 프로그램, 브라우저, 그리고 네트워크 사이의 프록시 서버 역할이다.

### 프록시란?

> "대리"라는 뜻으로, 클라이언트와 서버가 데이터를 주고받을 때 징검다리(중개자) 역할을 한다.

클라이언트가 서버에 데이터를 요청할 때 프록시가 먼저 받고 서버로 보낸다.

웹 브라우저 <---> 웹 프록시 <---> 웹 서버

다시 서비스 워커로 돌아와서 서비스 워커는 특정 사이트 하나 혹은 그 이상의 페이지를 제어하는 스크립트이다.(와닿지 않는 문장..어떤걸 제어한다는 것일까..?)

서비스 워커는 자신이 제어하는 페이지에서 발생하는 이벤트를 수신할 수 있다. 웹에서 네트워크 요청과 같은 이벤트를 가로채서 수정할 수 있고 다시 웹으로 돌려보낼 수도 있다. 서비스에서 이용하는 리소스(자원)을 캐싱할 수도 있다.

이러한 특징 덕분에 UI 스레드를 차단하지 않고 백그라운드에서 작업들을 수행할 수 있으므로 애플리케이션의 반응성이 향상된다.

### 서비스 워커의 사용 예시

- 네트워크가 원활할 때 동기화를 시켜주는 백그라운드 동기화 기능
- 푸시 이벤트를 생성할 때
- 높은 비용의 계산을 처리할 때(복잡한 알고리즘을 사용하여 이미지나 비디오를 처리하는 경우)
- 네트워크 요청을 가로채는 행위(MSW와 관련)

### 서비스 워커의 사용 제한 환경

- IE 브라우저에서는 지원 X
- localhost가 아니라면 https 보안 프로토콜 환경에서만 가능

### 서비스 워커의 위치

브라우저 화면을 띄우지 않아도 네트워크 요청을 가로챌 수 있고 리소스를 캐싱할 수 있는 이유는 탭의 외부에 위치하기 때문이다.

서비스 워커를 통해 푸시알림을 받을수도 있고, 백그라운드에서 앱 데이터를 최신화할 수도 있다.

우선 서비스 워커는 오프라인 상태에서도 웹/앱의 데이터, 네트워크 요청과 같은 정보들을 알 수 있게 해주는 역할이라고 알아두고 넘어가자.

## MSW(Mock Service Worker)

이전 장에서 데이터에 의존성이 있는 코드를 테스트할 때 mocking과 test fixture를 이용하여 데이터를 받아오는 함수에 대해 가짜로 작성했었다.

이것은 코드레벨에서 가짜로 구현한 것이지만 MSW를 이용하면 Mock 서버를 구축하지 않아도 네트워크 레벨에서 가짜로 구현이 가능하다.

> MSW란? : 서버의 네트워크 요청을 가로채서 모의 응답을 보내주는 역할

오프라인 작업을 지원하는 서비스 워커의 기능을 활용한 것이라 볼 수 있다.

어떠한 주소로 뭔갈 요청했을 때 프록시에서 가로채서 결과를 만들 때 사용 가능하다.

### MSW 동작 원리

![동작 원리](/images/msw.jpg)

그림을 보면 서버를 전혀 통하지 않는 것을 볼 수 있다.

msw는 테스트 환경(Node.js 기반) 외에 웹 브라우저도 지원하기 때문에, API 스펙은 나왔지만 아직 구현되지 않은 경우 임시로 사용할 수도 있다.

단순히 임시 서버를 만들 거라면 Express를 쓰는 게 더 낫지만, 테스트 코드도 지원하면서 겸사겸사 웹 브라우저를 지원하는 용도로는 나쁘지 않은 선택이다.

하지만 이모든 것도 다 진짜 같은 테스트이기 때문에 진짜 테스트도 필요하다.

실제 E2E 테스트는 다음 장에서 배울 Playwright으로 한다.

## MSW 설치

```bash
npm i -D msw
```

- jest.config.js

```javascript
module.exports = {
  testEnvironment: "jsdom",
  setupFilesAfterEnv: [
    "@testing-library/jest-dom/extend-expect",
    // 아래 속성 추가
    "<rootDir>/src/setupTests.ts",
  ],
  transform: {
    "^.+\\.(t|j)sx?$": [
      "@swc/jest",
      {
        jsc: {
          parser: {
            syntax: "typescript",
            jsx: true,
            decorators: true,
          },
          transform: {
            react: {
              runtime: "automatic",
            },
          },
        },
      },
    ],
  },
  testPathIgnorePatterns: ["<rootDir>/node_modules/", "<rootDir>/dist/"],
};
```

요청 핸들러 목록(handlers.ts), 브라우저와 서버별 설정(setupTests.ts)파일을 만드는 것을 mock 정의라고 한다. 이 파일들은 mock 폴더를 생성 후 관리하는 것이 좋다.

- 모의 서버 설정

src/setupTests.ts

```javascript
//fetch 함수를 제공하는 폴리필 라이브러리(뒤에서 추가 내용 정리)
import "whatwg-fetch";

import server from "./mocks/server";

// 들어오는 요청 수신 대기하도록 서버 설정
// onUnHanledRequest: "error"는 핸들러 함수에 내용이 없을 때 에러나게 함
beforeAll(() => server.listen({ onUnhandledRequest: "error" }));

//모든 테스트가 완료되면 서버를 닫음
afterAll(() => server.close());

//각 개별 테스트 후 테스트끼리 영향을 주지 않도록 모의 서버 핸들러를 재설정
afterEach(() => server.resetHandlers());
```

whatwg-fetch를 가져오는 것 => 환경에서 fetch 함수를 사용할 수 있고 모의 서버에서 HTTP 요청을 가로채서 응답하는 데 사용할 수 있다.

- MSW 라이브러리로 모의 서버 설정

```javascript
import { setupServer } from "msw/node";

import handlers from "./handlers";

const server = setupServer(...handlers);

export default server;
```

- MSW 핸들러 코드 작성

handlers 파일은 express 작성 문법과 유사하다.

```javascript
import { rest } from "msw";
import fixtures from "../../fixtures";

const BASE_URL = "http://localhost:3000";

// handlers는 보통은 여러개를 작성하므로 배열의 형태임
const handlers = [
  // rest : REST api 방식으로 핸들러 작성
  // ctx: 실제로 어떻게 응답을 처리할지에 대한 세부 내용
  rest.get(`${BASE_URL}/products`, (req, res, ctx) => {
    const { products } = fixtures;

    //
    return res(ctx.status(200), ctx.json({ products }));
  }),
];
export default handlers;
```

- App.test.ts 테스트 코드 작성

```javascript
import { render, screen, waitFor } from "@testing-library/react";
import App from "./App";

test("App", async () => {
  render(<App />);

  // 안에 있는게 될때까지 확인한다.(기다리는 것보단 확인이라는 표현이 적합)
  // waitFor이 프로미스로 되어있어서 await 해줘야함
  await waitFor(() => {
    screen.getByText("Apple");
  });
});
```

MSW를 활용하면 mocking없이 훨씬 간단하게 작성된 것을 볼 수 있다.

## polyfill (폴리필)

자바스크립트에는 fetch처럼 새로운 문법이나 기존에 없던 내장 함수에 대한 정의가 추가되곤한다. 그래서 브라우저가 지원하지 않는 자바스크립트 코드들을 지원 가능하도록 변환해주어야 한다.

폴리필을 위한 트랜스파일러에는 Babel이 있다. 순간 바벨이 폴리필이라는건가?? 하며 헷갈렸는데 바벨은 ES6에서 쓰이는 최신 문법을 ES5로 바꿔주는 역할을 하는 친구이다.

바벨이 ES6에서 추가된 모든 문법을 바꿀 수는 없다. 특히 ES5 이하를 지원하는 브라우저에서 Promise나 Set 등은 읽어낼 수가 없기 때문에 이럴 때 폴리필이 필요하다.

> 폴리필이란 ? : 변경된 표준을 준수할 수 있게 기존 함수의 동작 방식을 수정하거나 새롭게 구현한 함수의 스크립트

쉽게 말해 구현이 누락된 새로운 기능을 메꿔주는 역할을 한다.

### 바벨이 컴파일 가능한 눈법

- const, let
- spread operator
- arrow function
- class
- destructuring

### 폴리필이 필요한 문법

- 새로운 객체 (Promise, IntersectionObserver, Set, Map …)
- 기존 객체의 새로운 메서드 (Array.prototype.includes, Object.entries …)
- 새로운 함수 (fetch)

바벨이 window.Object, window.Array와 같이 전역 공간에 생성되는 객체가 아니거나, 기존에 존재하지 않는 메서드를 만들어줄 수는 없다.

[GitHub에서 만든 fetch polyfill](https://github.com/github/fetch)
