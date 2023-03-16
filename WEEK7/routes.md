# Routes

리액트 라우터의 Routes를 쓰는 방법을 배운다.

## 라우터란?

> 웹 어플리케이션에서 라우터는 사용자의 요청을 처리하고 해당 요청에 따른 적절한 페이지나 콘텐츠를 제공하는 기능을 담당하는 프로그램이다.

리액트에서는 React-router-dom으로 라우팅 기능을 구현할 수 있다.

Routes : 자식 route들을 구성하고 있는 단위이다.

## React Router

리액트 라우터는 "client side routing"을 가능하게 한다.

> "client side routing" : 일반 html 문서는 다른 문서로 연결할 수 있고 브라우저는 자체 history stack을 다룬다. 클라이언트 사이드 라우팅은 서버에 문서 요청 없이 브라우저 history stack을 조작할 수 있다.

이전의 웹사이트들은 브라우저가 웹서버로부터 문서를 요청하고 css와 javascript 파일들을 다운로드하고 평가했었다. 그리고 html에 보내서 화면을 렌더링하는 방식이었다.

그런데 client side routing는 링크를 클릭했을 때 서버로부터 다른 문서에 대한 요청 없이도 url의 업데이트가 가능하다.

대신에 앱은 즉시 새로운 UI를 렌더링할 수 있고 새로운 정보가 들어와서 페이지를 업데이트 해야할 때 `fetch`로 데이터 요청을 할 수도 있다.

브라우저가 새롭게 전체 문서를 요청하거나 css나 javascript 문서를 재평가할 필요가 없기 때문에 더 빠른 사용자 경험이 가능하다.

## 리액트 라우터 용어 정리(공식 사이트)

- URL - 주소창에 있는 URL입니다. 많은 사람들이 "URL"과 "경로"라는 용어를 혼용해서 사용하지만, React 라우터에서는 경로가 아니라 URL일 뿐입니다.

- location - 브라우저에 내장된 window.location 객체를 기반으로 하는 React 라우터 전용 객체입니다. "사용자가 현재 어디에 있는지"를 나타냅니다. 대부분 URL을 객체로 표현한 것이지만 그 이상의 의미가 있습니다.

- Location State - URL에 인코딩되지 않은 위치에 대해 지속되는 값입니다. 해시 또는 검색 매개변수(URL에 인코딩된 데이터)와 비슷하지만 브라우저의 메모리에 보이지 않게 저장됩니다.

- History Stack - 사용자가 탐색할 때 브라우저는 스택에 각 위치를 추적합니다. 브라우저에서 뒤로 가기 버튼을 길게 클릭하면 브라우저의 히스토리 스택을 바로 확인할 수 있습니다.

- History - React 라우터가 URL의 변경 사항을 구독하고 브라우저 히스토리 스택을 프로그래밍 방식으로 조작할 수 있는 API를 제공하는 객체입니다.

- History action - POP, PUSH, REPLACE 중 하나입니다. 사용자는 이 세 가지 이유 중 하나로 URL에 도달할 수 있습니다. 푸시는 새 항목이 히스토리 스택에 추가될 때 발생합니다(일반적으로 링크 클릭 또는 프로그래머가 강제로 탐색). Replace는 새 항목을 푸시하는 대신 스택의 현재 항목을 교체한다는 점을 제외하면 비슷합니다. 마지막으로 pop은 사용자가 브라우저 크롬에서 뒤로 또는 앞으로 버튼을 클릭할 때 발생합니다.

- Segment - URL의 일부 또는 / 문자 사이의 path pattern입니다. 예를 들어 "/users/123"에는 두 개의 세그먼트가 있습니다.

- Path Pattern - URL처럼 보이지만 동적 세그먼트("/users/:userId") 또는 별표 세그먼트("/docs/\*")와 같이 URL을 경로와 일치시키기 위한 특수 문자를 포함할 수 있습니다. 이들은 URL이 아니라 React 라우터가 일치시킬 패턴입니다.

- Dynamic Segment - 동적인 경로 패턴의 세그먼트로, 세그먼트의 모든 값과 일치할 수 있습니다. 예를 들어 /users/:userId 패턴은 /users/123과 같은 URL과 일치합니다.

- URL Params - Dynamic Segment와 일치하는 URL로부터 파싱된 값입니다.

- Router(라우터) - 모든 component와 hook이 작동하도록 하는 최상위 컴포넌트입니다.

- Route Config - 현재 위치에 대해 순위를 매기고 일치시킬(중첩 포함) 라우트 객체의 트리로, 라우트 일치 분기를 생성합니다.

- Route(라우트) - 일반적으로 { path , element } 또는 `<Route path element>` 형태의 개체 또는 라우트 요소입니다. path는 path pattern입니다. path pattern이 현재 URL과 일치하면 요소가 렌더링됩니다.

- Route Element - 또는 `<Route>`. 이 요소의 props는 `<Route>`에 의해 라우트를 생성하기 위해 읽히지만, 그렇지 않으면 아무 작업도 수행하지 않습니다.

```javascript
<Route path="/" element={<HomePage />} />
```

- Nested Routes(중첩 라우트) - 라우트에는 하위 경로가 있을 수 있고 각 경로가 세그먼트를 통해 URL의 일부를 정의하므로 단일 URL이 트리의 중첩된 "branch"에서 여러 경로와 일치할 수 있습니다. outlet, Relative links 등을 통해 레이아웃을 자동으로 중첩할 수 있습니다.

- Relative links - '/'로 시작하지 않는 링크는 렌더링되는 가장 가까운 경로를 상속합니다. 따라서 전체 경로를 알고 구축할 필요 없이 더 깊은 URL로 쉽게 링크할 수 있습니다.(?)

- Match - 라우트가 URL과 일치할 때 일치하는 URL 매개변수 및 라우트 이름과 같은 정보를 보유하는 객체입니다.

- Matches - 현재 위치와 일치하는 라우트(또는 경로 구성의 분기)의 배열입니다. 이 구조는 중첩된 라우트를 가능하게 합니다.

- Parent Route - 하위 경로가 있는 경로입니다.

- Outlet - matches 집합에서 다음 일치 항목을 렌더링하는 컴포넌트입니다.

- Index Route - 경로가 없는 자식 경로로, 부모의 URL에서 부모의 outlet에 렌더링합니다.

- Layout Route - 경로가 없는 상위 경로로, 특정 레이아웃 내에서 하위 경로를 그룹화하는 데만 사용됩니다.

리액트 라우터 설치 ⬇️

```bash
npm i react-router-dom
```

App.tsx를 리액트 라우터를 이용하여 코드 수정

```javascript
import { Routes, Route } from "react-router-dom";

import HomePage from "./pages/HomePage";
import Aboutpage from "./pages/AboutPage";
import Header from "./components/Header";
import Footer from "./components/Footer";

const pages = {
  "/": HomePage,
  "/about": Aboutpage,
};
export default function App() {
  const path = window.location.pathname;

  const Page = Reflect.get(pages, path) || HomePage;

  return (
    <div>
      <Header />
      <main>
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/" element={<HomePage />} />
        </Routes>
        <Page />
      </main>
      <Footer />
    </div>
  );
}
```

이렇게 거의 똑같이 바꿀 수 있다. 그런데 위처럼 하면 오류가 난다. 리액트 라우터는 웹 브라우저 환경에서 쓰는 것이므로 로컬에서는 브라우저 환경을 잡아주어야 한다.

App 컴포넌트는 나중에 테스트를 해야 하기 때문에 BrowserRouter를 main 파일에서 잡아준다.

## BrowserRouter

> React Router에서 제공하는 라우터 중 하나로, HTML5 History API를 사용하여 브라우저의 URL과 상호작용을 한다.

`<BrowserRouter>`는 URL을 사용하여 브라우저의 주소창에 현재 위치를 저장하고 브라우저에 내장된 history 스택을 사용하여 탐색한다.

=> 보통 최상위 컴포넌트인 App에서 선언해야함.

main.tsx

```javascript
import "reflect-metadata";

import App from "./App";
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";

function main() {
  const element = document.getElementById("root");

  if (!element) {
    return;
  }

  const root = ReactDOM.createRoot(element);

  root.render(
    <React.StrictMode>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </React.StrictMode>
  );
}

main();
```

테스트 환경에서의 리액트 라우터 설정은 MemoryRouter로 해주어야 한다.

## MemoryRouter

`<MemoryRouter>`는 내부적으로 배열에 위치를 저장한다. `<BrowserHistory>` 및 `<HashHistory>`와 달리 브라우저의 히스토리 스택과 같이 외부 소스에 연결되지 않습니다. 따라서 테스트와 같이 히스토리 스택을 완벽하게 제어해야 하는 시나리오에 이상적이다.

`<MemoryRouter initialEntries>`의 기본값은 `["/"]`(루트/URL에 있는 단일 항목)이다.
`<MemoryRouter initialIndex>`는 초기 항목의 마지막 인덱스를 기본값으로 한다.

App.test.tsx

```javascript
import { render, screen } from "@testing-library/react";

import { MemoryRouter } from "react-router-dom";

import App from "./App";

const context = describe;

describe("App", () => {
  context('when the current path is "/"', () => {
    it("renders the home page", () => {
      render(
        <MemoryRouter initialEntries={["/"]}>
          <App />
        </MemoryRouter>
      );

      screen.getByText(/Home/);
    });
  });
  context('when the current path is "/about"', () => {
    it("renders the about page", () => {
      render(
        <MemoryRouter initialEntries={["/about"]}>
          <App />
        </MemoryRouter>
      );

      screen.getByText(/This is test/);
    });
  });
});
```
