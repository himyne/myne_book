# Props와 attrs

- [x] props
- [x] attrs

## Props

활성화 여부를 표현하거나 특정 스타일을 잡아줄 때 유용하다.

```javascript
import styled, { css } from "styled-components";
import { useBoolean } from "usehooks-ts";

type ButtonProps = {
  active: boolean,
};

// 이렇게 해도 되고
function background(props: ButtonProps) {
  return props.active ? "#00F" : "#FFF";
}

// 내부에 써도 된다.(이미 타입이 지정되어 있으므로 또해줄 필요없음)
const Button =
  styled.button <
  ButtonProps >
  `
  background: #FFF;
  color: #000;
  border: 1px solid ${(props) => (props.active ? "#F00" : "#888")};

  ${(props) =>
    props.active &&
    css`
      background: #f00;
      color: #fff;
    `}
`;

export default function Switch() {
  const { value: active, toggle } = useBoolean(false);

  return (
    <Button type="button" onClick={toggle} active={active}>
      On/Off
    </Button>
  );
}
```

props.active && css에서 css 는 써도 되고 안써도 된다. 타입 확장의 도움을 받기위해 사용할 뿐이다.

### 공식 문서 예시

input 컴포넌트의 모든 프로퍼티가 DOM 노드에 전달된다.

여기서 inputColor prop은 DOM을 통하지 않지만 type과 defaultValue는 DOM을 통한다. 비표준 속성은 자동으로 필터링되는 것이다.

```javascript
const Input = styled.input`
  padding: 0.5em;
  margin: 0.5em;
  color: ${(props) => props.inputColor || "palevioletred"};
  background: papayawhip;
  border: none;
  border-radius: 3px;
`;

render(
  <div>
    <Input defaultValue="@probablyup" type="text" />
    <Input defaultValue="@geelen" type="text" inputColor="rebeccapurple" />
  </div>
);
```

## attrs

attrs를 사용하면 속성을 나타낼 수 있다. 버튼에서 되게 자주 쓰인다.

```javascript
import styled from "styled-components";

// type 속성을 전달 받았다.
const Button = styled.button.attrs({
  type: "button",
})`
  border: 1px solid #888;
  background: transparent;
  cursor: pointer;
`;

export default Button;
```

### 공식 사이트 예시

```javascript
const Input = styled.input.attrs((props) => ({
  // 정적인 props를 정의할 수도 있고
  type: "text",
  // 동적인 props를 정의할 수도 있다.
  size: props.size || "1em",
}))`
  color: palevioletred;
  font-size: 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;

  /* 여기에서 동적으로 계산된 프롭을 사용한다 */
  margin: ${(props) => props.size};
  padding: ${(props) => props.size};
`;

render(
  <div>
    <Input placeholder="A small text input" />
    <br />
    <Input placeholder="A bigger text input" size="2em" />
  </div>
);
```
