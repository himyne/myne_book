# Router

App 컴포넌트의 역할

1. 전체적으로 어떤 레이아웃을 취하고 있는가
2. 어떻게 라우팅이 되는가?

그런데 App 컴포넌트의 역할을 또 분리할 수 있다.

```javascript
import { Routes, Route } from "react-router-dom";

import HomePage from "./pages/HomePage";
import Aboutpage from "./pages/AboutPage";
import Header from "./components/Header";
import Footer from "./components/Footer";

// 이전에 pages 객체로 처리했던 것과 비슷하게 처리하는 컴포넌트
function Page() {
  return (
    <Routes>
      <Route path="/" element={<HomePage />} />
      <Route path="/about" element={<Aboutpage />} />
    </Routes>
  );
}

// 레이아웃은 따로 관리
export default function App() {
  return (
    <div>
      <Header />
      <main>
        <Page />
      </main>
      <Footer />
    </div>
  );
}
```

더 나아가 레이아웃을 관리하는 Layout 컴포넌트와 경로를 관리하는 routes를 App 파일에서 분리해준다. => 이제 App 테스트가 아니라 routes를 테스트 하게 된다.

## 강의 실습 코드

App.tsx

```javascript
import {
  Routes,
  Route,
  createBrowserRouter,
  RouterProvider,
  Outlet,
} from "react-router-dom";

import routes from "./routes";

// Routes를 이용해서 routes 객체를 통으로 만들 수 있다.

const router = createBrowserRouter(routes);

export default function App() {
  // 모든 라우터 객체가 RouterProvider의 props로 전달되어 앱을 렌더링하고 나머지 API를 활성화한다.
  return <RouterProvider router={router} />;
}
```

## RouterProvider & createBrowserRouter

createBrowserRouter는 BrowserRouter를 생성하는 함수이다.

이 함수를 사용하여 BrowserRouter 인스턴스를 만들면, 라우트를 정의하고 RouterProvider를 사용하여 React 애플리케이션에서 라우팅을 관리할 수 있다.

createBrowserRouter는 일반적으로 RouterProvider와 함께 사용된다. RouterProvider는 createBrowserRouter를 호출하여 생성한 BrowserRouter 인스턴스를 사용하여 라우팅을 처리한다.

Layout.tsx

```javascript
import { Outlet } from "react-router-dom";

import Header from "./components/Header";
import Footer from "./components/Footer";

export default function Layout() {
  return (
    <div>
      <Header />
      <main>
        <Outlet />
      </main>
      <Footer />
    </div>
  );
}
```

routes.tsx

```javascript
// 테스트에서 route 정보를 필요로 하므로 분리하는 것이 좋다.

import HomePage from "./pages/HomePage";
import AboutPage from "./pages/AboutPage";
import Layout from "./Layout";

//이 라우트가 App 컴포넌트에서 createBrowserRouter의 인자로 들어갈 부분이다.
const routes = [
  {
    element: <Layout />,
    children: [
      { path: "/", element: <HomePage /> },
      { path: "/about", element: <AboutPage /> },
    ],
  },
];

export default routes;
```

routes.test.tsx

```javascript
import { render, screen } from "@testing-library/react";

import { createMemoryRouter, RouterProvider } from "react-router-dom";

import routes from "./routes";

const context = describe;

describe("routes", () => {
  function renderRouter(path: string) {
    const router = createMemoryRouter(routes, { initialEntries: [path] });
    render(<RouterProvider router={router} />);
  }

  context('when the current path is "/"', () => {
    it("renders the home page", () => {
      renderRouter("/");

      screen.getByText(/Home/);
    });
  });
  context('when the current path is "/about"', () => {
    it("renders the home page", () => {
      renderRouter("/about");

      screen.getByText(/This is test/);
    });
  });
});
```

리액트 라우터를 이용한 방법은 브라우저에서 창을 이동할 때마다 전체 페이지가 새로 렌더링 된다. 이럴 경우, 예를 들어 음악을 듣고 있었는데 링크를 새로 누르면 음악이 꺼지는 상황이 일어난다.

이를 해결해주기 위해 navigation을 사용한다.
