# navigation

## History.pushState()

원래는 url 앞에 #을 붙여서 사용했었는데 History.pushState()라는 메서드를 사용하면 브라우저에서 지원이 된다.

```javascript
import { SyntheticEvent } from "react";

export default function Header() {
  const handleClick = (event: SyntheticEvent) => {
    event.preventDefault();
    const state = {};
    const title = "";
    // state랑 title은 잘 안씀
    const url = "/about";
    //무조건 about으로 향하게 한다.
    history.pushState(state, title, url);
  };

  return (
    <header>
      <nav>
        <ul>
          <li>
            <a href="/" onClick={handleClick}>
              Home
            </a>
          </li>
          <li>
            <a href="/about" onClick={handleClick}>
              About
            </a>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

## Link

```javascript
import { Link } from "react-router-dom";

export default function Header() {
  return (
    <header>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about">About</Link>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

## NavLink

```javascript
import { NavLink } from "react-router-dom";

export default function Header() {
  return (
    <header>
      <nav>
        <ul>
          <li>
            <NavLink to="/">Home</NavLink>
          </li>
          <li>
            <NavLink to="/about">About</NavLink>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

NavLink를 이용하면 `<a aria-current="page" class="active" href="/">Home</a>` 이와 같이 class를 입력해주지 않았는데 class가 active하다는 생긴다. 클릭된 링크가 active가 되는 것이다.

이를 이용해서 스타일도 응용해서 처리해줄 수 있다.

```html
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <title>React Demo App</title>
    <style>
      .active {
        font-size: 2rem;
      }
    </style>
  </head>
  <body>
    <div id="root"></div>
    <div id="demo"></div>
    <script type="module" src="./src/main.tsx"></script>
  </body>
</html>
```

## Navigate

무조건 홈으로 이동하게 하는 로그아웃 페이지를 Navigate를 이용하여 만들 수 있다.

```javascript
import { Navigate } from "react-router-dom";

export default function LogoutPage() {
  return <Navigate to="/" />;
}
```

```javascript
// 테스트에서 route 정보를 필요로 하므로 분리하는 것이 좋다.

import HomePage from "./pages/HomePage";
import AboutPage from "./pages/AboutPage";
import LogoutPage from "./pages/LogoutPage";
import Layout from "./Layout";

const routes = [
  {
    element: <Layout />,
    children: [
      { path: "/", element: <HomePage /> },
      { path: "/about", element: <AboutPage /> },
      { path: "/logout", element: <LogoutPage /> },
    ],
  },
];

export default routes;
```

테스트에서 “ReferenceError: Request is not defined” 에러가 나면 “whatwg-fetch”를 임포트해서 해결할 수 있다.

routes.test.tsx(logout에 대한 테스트 추가)

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
  context('when the current path is "/logout"', () => {
    it("redirects to the home page", () => {
      renderRouter("/logout");

      screen.getByText(/Welcome/);
    });
  });
});
```

따로 로그아웃 페이지를 만들지 않고 바로 처리해도 되긴 하다.

```javascript
import { Link, NavLink, useNavigate } from "react-router-dom";

export default function Header() {
  const navigate = useNavigate();

  const handleClickLogout = () => {
    navigate("/");
  };

  return (
    <header>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about">About</Link>
          </li>
          <li>
            <button type="button" onClick={handleClickLogout}>
              Log out
            </button>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```
