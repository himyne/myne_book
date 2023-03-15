# Routing

라우팅으로 페이지를 이동하는 법을 배운다.

라우팅(routing) : 사용자가 접근한 URL에 따라서 파일을 로드하는 것

## HTML DOM API

HTML DOM API를 이용하면 DOM 요소에 접근 및 제어가 가능하고 Form 데이터라던지, 브라우저 히스토리에 대한 정보 접근 등이 가능하다.

이 중에서 우리는 History API 의 location이라는 인터페이스를 사용하면 url을 알 수 있다. document.location이나 window.location을 통해 접근할 수 있다.

## Location

Window.location 프로퍼티에 접근하면 읽기 전용인 loction 객체를 얻어올 수 있다.

이 객체는 현재 문서의 사용자가 어디에 있는지에 대한 위치 정보를 담고 있다.

```text
>window.location
< window.location
Location {ancestorOrigins: DOMStringList, href: 'https://www.naver.com/', origin: 'https://www.naver.com', protocol: 'https:', host: 'www.naver.com', …}
```

더불어 location 인터페이스는 객체가 연결된 url을 표현한다.

```text
> location.pathname
< '/'

> location.host
< 'www.naver.com'

location.hash : 눈에는 외계어처럼 보이는데 브라우저가 인코딩한 값이다.

encodeURI('예제') => 인코딩된 값

decodeURI(location.hash) => #예제를 반환
```

- location.pathname : '/' 문자 뒤 URL의 경로를 값으로 하는 DOMString
- location.host: URL의 호스트 부분을 값으로 하는 DOMString
- location.hostname: URL의 도메인 부분을 값으로 하는 DOMString

## 강의 코드

App.tsx

```javascript
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
        <Page />
      </main>
      <Footer />
    </div>
  );
}
```

./pages/Header.tsx

```javascript
export default function Header() {
  return (
    <header>
      <nav>
        <ul>
          <li>
            <a href="/">Home</a>
          </li>
          <li>
            <a href="/about">About</a>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

./pages/Footer.tsx

```javascript
export default function Footer() {
  return (
    <footer>
      <hr />
      Footer
    </footer>
  );
}
```

./components/AboutPage.tsx

```javascript
export default function Aboutpage() {
  return (
    <div>
      <h1>About</h1>
      <p>This is test</p>
    </div>
  );
}
```

./components/HomePage.tsx

```javascript
export default function HomePage() {
  return (
    <div>
      <h1>Welcome!</h1>
      <p>Hello, world</p>
    </div>
  );
}
```

다음 강의에서는 App.tsx 파일의 코드들을 리액트 라우터를 이용하여 바꿔줄 것이다.
