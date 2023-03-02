# React Testing Library(RTL)

- [x] React Testing Library
- [x] given - when - then 패턴
- [x] Mocking
- [x] Test fixture

> React 컴포넌트를 사용자 입장에 가깝게 테스트 할 수 있는 구현 기반의 도구이다. 현재 Enzyme의 대안으로 자리 잡았다.

## Enzyme vs React Testing Library

RTL이 등장하기 전에 Airbnb에서 만든 Enzyme이라는 테스팅 라이브러리가 많이 사용되었었다.

Enzyme은 실제 브라우저 DOM이 아닌 리액트가 만든 가상 DOM에서 테스트를 하는데 RTL은 jsdom 라이브러리를 제공하여 실제 브라우저 DOM을 기준으로 테스트를 한다.

그래서 Enzyme은 컴포넌트에 어떤 prop이 넘어가고 현재 상태(state)가 어떻게 되는지에 대해서 검증했다면, RTL은 실제 HTML 마크업의 모습이 어떤지에 대해 테스트하기 용이하다.

예를 들어 RTL은 `<div>Hello World<div>`라는 코드에서 div 태그를 사용하는지보다 Hello World라는 글자가 화면에 잘 출력되었는지를 중점으로 테스트한다.(사용자 경험과 유사)

## Jest vs React Testing Library

RTL은 앞장에서 다룬 Jest를 포함하는 구조이고 Jest의 대안으로 나온 것이 아니다.

- Jest : 자체적인 test runner와 test util 제공
- RTL : Jest + 리액트 컴포넌트 util 제공

그래서 jest와 코드 작성 방식과 사용 방법은 매우 유사하다.

RTL은 E2E 테스트 가까운 느낌을 주지만 브라우저에서 도는건 아니므로 E2E 테스트는 아니긴 하다.

하지만 굉장히 빠르고, 테스트 시나리오도 쉽게 작성할 수 있다.

`--npx tsc --noEmit` : 원래 컴파일한 결과가 js 파일로 생기는데 안생기게 하는 명령어

## TextField 컴포넌트 테스트 코드

```javascript
import { render, screen } from "@testing-library/react";

import TextField from "./TextField";

test("TextField", () => {
  const text = "Tester";
  const setText = () => {
    // do nothing...
  };
  // render: 테스트를 위해 컴포넌트를 jsdom에 렌더링한다.
  render(
    <TextField
      //라벨 추가
      label="Name"
      placeholder="Input your name"
      //범용적 표현으로 수정
      text={text}
      setText={setText}
    />
  );

  screen.getByLabelText("Name");
});
```

기존에 TextField 코드에는 label도 빠져있었고 text가 아니라 filterText를 사용하여 범용적인 표현이 아니었다.

이런 문제들을 테스트를 작성하면서 발견할 수도 있지만 개발하기 전 테스트를 작성했다면 더 빨리 찾아내서 문제를 수정했을 것이다.

시간이 지날수록 코드에 대한 지식이 감소하고 생각이 안나기 때문에 그때그때 리팩토링을 잘해주어야 한다. 그럴러면 TDD가 중요! 하지만 연습이 많이 필요하다.

## given - when - then 패턴

BDD 테스트 작성의 대표적인 예이다.

- Given : 주어진 환경 정의 (예: 검색창이 주어졌을 때)
- When : 사용자가 어떤 행위를 하는 것(예: 사용자가 검색창에 a를 입력하면)
- Then : 그에 따른 결과 정의(예: a를 인자로 setText가 실행되어야 한다)

위에서 작성한 테스트 코드를 BDD 스타일로 바꿔보자

[RTL Cheat Sheet](https://testing-library.com/docs/dom-testing-library/cheatsheet/)

## BDD 스타일로 작성한 테스트 코드

```javascript
import { fireEvent, render, screen } from "@testing-library/react";

import TextField from "./TextField";

const context = describe;

// TextField가 테스트 대상
describe("TextField", () => {
  //given : 검색창의 라벨, 상태 변수 등 테스트할 함수 안에서 주어진 환경들
  const label = "Name";
  const text = "Tester";

  const setText = jest.fn();

  //매번 실행 때마다 mock들은 초기화를 해줘야함
  beforeEach(() => {
    // setText.mockClear();
    jest.clearAllMocks();
  });

  //테스트 코드에서 반복되므로 함수로 따로 분리(관심사 분리)
  function renderTextField() {
    render(
      <TextField
        label={label}
        placeholder="input your name"
        text={text}
        setText={setText}
      />
    );
  }

  function inputText(value: string) {
    // fireEvent로 인터랙션(동작)만 검증
    // fireEvent : 쿼리 함수로 선택된 영역을 대상으로 특정 이벤트를 발생시키기 위한 이벤트 함수들을 담고 있음
    fireEvent.change(screen.getByLabelText(label), {
      target: { value },
    });
  }

  //요소들이 잘 렌더링되는지 확인
  it("renders elements", () => {
    //when : 텍스트 필드 함수를 렌더링 했을때
    renderTextField();

    //then : 라벨, placeholder, 인풋 텍스트 요소들이 화면에 잘 뜬다.
    screen.getByLabelText(label);
    screen.getByPlaceholderText(/name/);
    screen.getByDisplayValue(text);
  });
  // ------

  //when : 사용자가 검색창에 이름을 입력하면
  context("when user enters name", () => {
    beforeEach(() => {
      renderTextField();
    });
    //then : setText핸들러가 호출된다.
    it('calls "setText" handler', () => {
      inputText("New Name");
      //인자 New Name을 가지고 호출된다.
      expect(setText).toBeCalledWith("New Name");
    });
  });
});
```

그런데 우리가 지금 만들고 있는 웹은 express-demo-app에서 받아오는 데이터에 의존하고 있다. 실제 개발 환경에서는 이 데이터가 주어지지 않았을 수도 있고, 항상 서버를 켜고 프론트 작업을 하는것도 좋은 방법은 아니다.

이처럼 외부 의존성이 큰 테스트 코드를 작성한다면 해당 부분만 가짜로 구현할 수 있다.

```javascript
import { render, screen } from "@testing-library/react";

import App from "./App";

jest.mock("./hooks/useFetchProducts", () => () => [
  {
    category: "Fruits",
    price: "$1",
    stocked: true,
    name: "Apple",
  },
]);

test("App", () => {
  render(<App />);

  screen.getByText("Apple");
});
```

이런식으로 가능하긴한데 더 좋은 방법이 있다.

=> mock 함수에 module 쓰고 fixtrures 폴더 따로 만들기

```javascript
import { render, screen } from "@testing-library/react";
import fixtures from "../fixtures";
import App from "./App";
import useFetchProducts from "./hooks/useFetchProducts";

jest.mock("./hooks/useFetchProducts", () => () => fixtures.products);

test("App", async () => {
  render(<App />);

  expect(useFetchProducts).toBeCalled();
});
```

```javascript
// .fixtures/index.ts
import products from "./products";

export default {
  products,
};
```

```javascript
// .fixtures/products.ts
const products = [
  {
    category: "Fruits",
    price: "$1",
    stocked: true,
    name: "Apple",
  },
];

export default products;
```

그런데 아래 코드처럼 fetch하는 api들이 많아지면 너무 복잡해진다.

```javascript
import { render, screen } from "@testing-library/react";
import fixtures from "../fixtures";
import App from "./App";
import useFetchProducts from "./hooks/useFetchProducts";

// App은 가장 최상위 컴포넌트이기 때문에 불러올 것이 많아지면 이렇게 코드가 길어진다.=> 이럴 때 msw 사용하여 해결한다.
jest.mock("./hooks/useFetchProducts");
jest.mock("./hooks/useFetchShops");
jest.mock("./hooks/useFetchUsers");
jest.mock("./hooks/useFetchInfo");
jest.mock("./hooks/useFetch~~~");

// jest.mock('./hooks/useFetchProducts', () => () => fixtures.products)

test("App", () => {
  render(<App />);

  screen.getByText(/Apple/);

  expect(useFetchProducts).toBeCalled();
});
```

일반적으론 백엔드와 소통하는 부분이 차지하는 비중이 큰데, 이 부분을 하나씩 가짜 구현으로 바꾸다 보면 어려울 때가 있으니 이럴 땐 moking을 이용해보자

## Mocking 이란 ?

> 테스트할 때 외부 리소스에 의존하는 코드를 독립적으로 테스트하기 위해 가짜 객체를 만드는 기술

Mocking을 사용해야 하는 경우

- 테스트할 객체가 외부 객체에 의존중인 경우
- 외부 api가 정상적으로 작동하지 않거나 호출할 때 비용이 발생하는 경우
- api 데이터가 구축되지 않은 경우

### Mock 사용 시 주의사항

- mock 객체를 불필요하게 많이 사용하면 후에 유지보수 비용이 크게 발생할 수 있다.
- 가상의 객체이므로 실제 객체로 작동했을 때 예상과 다르게 동작할 수 있다.

mock 객체를 많이 만든다고 좋은 것은 아니다. 꼭 필요한 객체에 필요한 행동에만 mocking을 하는 겻이 바람직하다.

## Test Fixture

> 테스트 실행을 위해 베이스로 사용되는 객체들의 고정된 상태이다. 여러 테스트에서 같은 구성의 Date Set을 사용하고자 할 때 유용하다.

Test fixture를 사용하면 데이터베이스를 활용하여 테스트를 수행할 경우 매번 데이터 베이스를 생성하고 초기화하는 작업을 반복할 필요가 없다.

실제 react-demo-app 에서는 상품 데이터베이스가 고정적으로 필요한 상황이었다.

그래서 fixture 폴더를 만들고 그 안에 상품 객체를 만들었다.

index.ts

```javascript
import products from "./products";

export default {
  products,
};
```

products.ts

```javascript
const products = [
  {
    category: "Fruits",
    price: "$1",
    stocked: true,
    name: "Apple",
  },
];

export default products;
```

### Test Fixture 사용 예시

- Mock 또는 Fake Object의 세팅이나 생성, 삽입할 데이터가 필요한 경우
- 변하지 않으며 구체적으로 알고있는 데이터가 필요한 경우
- 특정 상태로 초기화가 필요한 객체가 존재할 경우
