## JSX란??

JSX는 공식적인 문법은 아니고 XML처럼 ECMAScript에서 확장된 문법이다.

그렇기 때문에 우리가 jsx로 코드를 작성하면 바벨이나 여러가지 트랜스파일러들이 표준 ECMAScript로 변환해준다.

바벨은 특히 사이트에서 JSX를 Javascript로 변환된 코드도 바로 확인할 수 있다.

[React-JSX](https://ko.reactjs.org/docs/introducing-jsx.html)
[Draft](https://facebook.github.io/jsx/)

그리고 JSX는 React에서 나왔지만 React에서만 쓰는 것은 아니다. 뷰나 다른 프레임워크에서도 쓸 수 있다고 한다.

### JSX 사용 목적

리액트는 mvc 중에서도 뷰만 신경쓰는 프레임워크이고 UI 관련 작업을 주로 한다.

그렇기 때문에 마크업과 트리구조로 이루어진 JSX를 사용하는 것이 시각적으로 큰 이점이 있다고 한다.

javascript언어보다 html이 보기 편하고 쉬운 이유와 같은 맥락이다.

결론적으로 리액트에서 jsx 사용은 필수는 아니지만 시각적인 이점 때문에 사용하는 것이다!!

## JSX는 Syntactic sugar이다?

자바스크립트에서도 클래스가 기존 프로토타입 기반 패턴을 클래스 기반 패턴처럼 사용하는 문법적 설탕이라는 말이 있다.

jsx도 javascript의 createElement 함수를 html의 시멘틱 태그처럼 간단하게 표현 가능한 문법적 설탕을 제공한다.

만약 jsx와 같은 Syntactic sugar가 제공되지 않았다면 어떻게 되었을까??

아래 바벨에서 jsx 문법을 javascript로 변환한 코드들의 예시를 통해 살펴보자.

## JSX => Javascript 변환 코드 예시

### EX 1)

```javascript
//jsx
<p>Hello, world!</p>
```

```javascript
//JS로 변환
React.createElement("p", null, "Hello, world!");
```

jsx에서는 html에서 p태그를 사용하듯이 간단하게 표현되어 있어 가독성이 좋다.

그런데 js로 변환하는 순간 p태그 내의 내용 뿐만 아니라 p태그도 함수의 인자로 표현되는 것을 볼 수 있다. 확실히 가독성이 좋지는 않아보인다.

> 바벨에서 JSX 파일에 /_ @jsx ~~~ _/ 주석을 추가하면 React.createElement 대신 "~~~"을 쓸 수도 있다.

### EX 2)

```javascript
//jsx
<Greeting name="world" dog={{ maru: "10kg" }} />
```

```javascript
//JS로 변환
React.createElement(Greeting, { name: "world", age: { maru: "10kg" } });
```

Greeting처럼 태그 이름을 마음대로 설정해도 표현이 가능하다.

### EX 3)

```javascript
//jsx
<Button type="submit" onClick={() => console.log("Clicked")}>
  Send
</Button>
```

```javascript
//JS로 변환
React.createElement(
  "button",
  {
    type: "submit",
    onClick: () => console.log("Clicked"),
  },
  "Send"
);
```

onClick 속성 같은 경우, 함수가 들어있을 수도 있다.

속성 객체들 내부에서 onClick은 Key, 화살표 함수는 Value로 변환된다.

### EX 4)

```javascript
//jsx
<div className="test">
  <p>Hello, world!</p>
  <Button type="submit">Send</Button>
</div>
```

```javascript
//JS로 변환
React.createElement(
  "div",
  { className: "test" },
  React.createElement("p", null, "Hello, world!"),
  React.createElement(Button, { type: "submit" }, "Send")
);
```

어느정도 jsx 문법이 javascript에서는 어떻게 동작하는지 알아보았다.

jsx 문법이 어떻게 변환되는지 완벽히 알고 있을 필요는 없겠지만 아예 모르고 jsx 문법만을 배워 사용하는 것과는 큰 차이가 있지 않을까 싶다.

## 근데 React.createElement가 뭐야??

### createElement(type, props, ...children)

```javascript
React.createElement(
  type, // 태그 이름 문자열 | 리액트 컴포넌트 | React.Fragment
  [props], // 리액트 컴포넌트에 넣어주는 데이터 객체
  [...children] // 자식으로 넣어주는 요소들
);
```

말 그대로 **리액트 앱의 가장 단위인 element**를 만들어주는 친구이다.

```javascript
//repl 환경
const element = document.createElement("div");
element.className = "test";

element; // <div class="test"></div>
```

위처럼 자바스크립트 DOM 트리에서 element(노드)를 만드는것처럼 리액트도 유사한 방법으로 노드(element)를 생성한다.

매우 복잡하겠지만 createElement를 사용하면 순수 javascript만으로도 컴포넌트를 만들 수 있는 것이다.

아래 코드를 보면 React.createElement가 왜 복잡한지 알 수 있다.

```javascript
<div>
  <div>
    <h1>주제</h1>
    <ul>
      <li>React</li>
      <li>Vue</li>
    </ul>
  </div>
</div>
```

```javascript
ReactDOM.render(
  React.createElement(
    "div",
    null,
    React.createElement(
      "div",
      null,
      React.createElement("h1", null, "주제"),
      React.createElement(
        "ul",
        null,
        React.createElement("li", null, "React"),
        React.createElement("li", null, "Vue")
      )
    )
  ),
  document.querySelector("#root")
);
```

그리고 React.createElement는 DOM에서 요소 노드를 만든 것과 동일한 역할을 하고 있다는 것도 볼 수 있다.
