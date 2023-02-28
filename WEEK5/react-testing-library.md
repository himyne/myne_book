# React Testing Library

- [ ] React Testing Library
- [x] given - when - then 패턴
- [ ] Mocking
- [ ] Test fixture

React 컴포넌트를 사용자 입장에 가깝게 테스트 할 수 있는 도구

E2E 테스트 가까운 느낌을 주지만 E2E 테스트는 아니긴 하다. 브라우저에서 도는건 아니므로.

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

---

위에서 작성한 테스트 코드를 BDD 스타일로 바꿔보자

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
    // fireEvent : click, submit, change와 같은 DOM 이벤트를 시뮬레이션하고 발생하는 UI 변경을 확인하는 함수
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

일반적으론 백엔드와 소통하는 부분이 차지하는 비중이 큰데, 이 부분을 하나씩 가짜 구현으로 바꾸다 보면 어려울 때가 있으니 이럴 땐 MSW 등 다른 대안을 고려해보자.
