# navigation

## Web APIs - History

DOM Window 객체는 history 객체를 통해 browser의 session 기록을 제공한다.

브라우저에서 뒤로 이동하거나 앞으로 이동하는 것을 가능하게 하는 메서드와 프로퍼티들이 있다.

History 인터페이스는 현재 페이지가 로드된 탭을 조작할 수 있다.

history 객체는 스택과 비슷한 형태로 되어있는데 pushState() 메서드와 replaceState() 메서드는 히스토리를 수정하고 추가할 수 있다.

history 객체를 사용하면 SPA(Single Page Application)에서 브라우저의 뒤로가기와 같은 동작을 구현할 수 있다.

> SPA: SPA는 Single Page Application의 약어로, 한 개의 웹 페이지로 구성된 애플리케이션을 의미한다. 즉, 전통적인 서버 사이드 렌더링 방식이 아닌, 클라이언트 측에서 렌더링이 이루어지는 방식을 말한다. 초기 로딩 시 모든 리소스를 다운로드하고, 이후에는 필요한 데이터만 서버로부터 비동기식으로 가져와 동적으로 페이지를 갱신한다. 이러한 방식으로, 서버와의 통신을 최소화하고 빠른 페이지 로딩과 부드러운 사용자 경험을 제공할 수 있다.

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

`<Link>`는 사용자가 클릭하거나 탭하여 다른 페이지로 이동할 수 있게 해주는 요소이다. 리액트 라우터 돔에서 `<Link>`는 링크하는 리소스를 가리키는 실제 href가 있는 접근 가능한 `<a>` 엘리먼트를 렌더링한다.

상대 경로 `<Link to>` 값(슬래시(/)로 시작하지 않는)은 부모 경로에 상대적으로 해석되며, 이는 해당 `<Link>`를 렌더링 한 경로에 매칭된 URL 경로를 기반으로 구축된다.

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

`<NavLink>`는 "active" 또는 "pending" 여부를 알 수 있는 특별한 종류의 `<Link>`이다. 이는 현재 선택된 탭을 표시하려는 이동 경로 또는 탭 집합과 같은 탐색 메뉴를 만들 때 유용하다.

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

기본적으로 active 클래스는 active 상태일 때 `<NavLink>` 컴포넌트에 추가되므로 CSS를 사용하여 스타일을 지정할 수 있다.

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

`<Navigate>` 요소는 렌더링될 때 현재 위치를 변경한다. 이 요소는 useNavigate를 둘러싼 컴포넌트 wrapper이며 프로퍼티와 동일한 인수를 모두 허용한다.

무조건 홈으로 이동하게 하는 로그아웃 페이지를 Navigate를 이용하여 만들 수 있다.

```javascript
import { Navigate } from "react-router-dom";

export default function LogoutPage() {
  return <Navigate to="/" />;
}
```

```javascript
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
