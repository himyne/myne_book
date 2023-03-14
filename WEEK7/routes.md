# Routes

리액트 라우터의 Routes를 쓰는 방법을 배운다.

리액트 라우터 설치

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
