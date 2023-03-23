# Global Style

- [x] Reset CSS
- [x] `box-sizing` 속성
- [x] `word-break` 속성
- [x] Theme
- [x] ThemeProvider

## css Reset

css Reset의 목표는 기본 줄 높이, 여백, 제목의 글꼴 크기 등에서 브라우저의 불일치를 줄이는 것이다.

Reset CSS를 그대로 사용하는 것은 권장하지 않으며 재설정을 하여 사용하는 것을 권장한다.

보통 styled-components를 위한 Reset CSS인 "styled-reset"을 설치해서 사용한다.

```bash
npm i styled-resetnpm i styled-reset
```

```javascript
import { Reset } from "styled-reset";
import Greeting from "./components/Greeting";
import Switch from "./components/Switch";

export default function App() {
  return (
    <div>
      <Reset />
      <Greeting />
      <Switch />
    </div>
  );
}
```

## Global Style 적용

```javascript
import { createGlobalStyle } from "styled-components";

const GlobalStyle = createGlobalStyle`
	html {
		box-sizing: border-box;
	}
	
	*,
	*::before,
	*::after {
		box-sizing: inherit;
	}
	
	html {
		font-size: 62.5%;
	}
	
	body {
		font-size: 1.6rem;
	}
	
	:lang(ko) {
		h1, h2, h3 {
			word-break: keep-all;
		}
	}
`;

export default GlobalStyle;
```

App 컴포넌트에서 GlobalStyle 컴포넌트 불러오기

```javascript
import { Reset } from "styled-reset";

import GlobalStyle from "./styles/GlobalStyle";

export default function App() {
  return (
    <>
      <Reset />
      <GlobalStyle />
      <Greeting />
    </>
  );
}
```

## box-sizing 속성

기본적으로 css에서 html 요소들은 박스 모델이다.

이 박스 모델을 padding, border, margin, content로 구분하는데 box-sizing 속성은 요소의 너비와 높이를 계산하는 방법을 지정한다.

box-sizing에는 content-box(기본값), border-box 속성 2가지가 있다.

- content-box : Content 영역을 기준으로 box의 size를 적용한다.

![content-box](/images/content-box.jpg)

- border-box : border 영역을 기준으로 box의 size를 적용한다.

![border-box](/images/border-box.jpg)

box-sizing 속성에서 global values를 사용하면 해당 요소에 대한 box-sizing 값이 상속되거나, 초기값으로 설정되거나, 기본값으로 복원된다.

- inherit: 부모 요소에서 상속받은 값을 사용한다.
- initial: 기본값을 사용한다.
- revert: 부모 요소에서 상속받은 값을 사용하지만, 해당 속성이 사용자에 의해 변경되었다면 그 변경된 값으로 다시 설정한다.
- revert-layer: 현재 레이어에서 가장 가까운 값으로 다시 설정한다.
- unset: 상속된 값이 있다면 상속받은 값을 사용하고, 그렇지 않다면 기본값을 사용한다.

## word-break 속성

글자가 블록 요소 내에서 줄바꿈이 될 때 단어를 분리하는 방법을 지정한다.

```css
/* Keyword values */
word-break: normal;
word-break: break-all;
word-break: keep-all;
```

- break-all : 글자 기준으로 줄바꿈
- keep-all : 단어 기준으로 줄바꿈

📌 Tip

- 영어 + 한글 데이터가 들어갈 수 있는 경우 word-break: break-all
- 영어만 들어간다면 word-wrap: break-word

## Theme

지금까지 배운 것이 이 Theme을 사용하기 위한 것이라고 해도 과언이 아님.

예를 들어 다크모드를 활용할 때 매우 좋다.

`<ThemeProvider>` 컴포넌트를 내보냄으로써 완전한 테마를 지원한다. 컨텍스트 API를 통해 아래에 있는 모든 React 컴포넌트에 테마를 제공한다.

### 강의 실습 코드

기본 Theme을 정의한다. color에 primary, secondary 등 이름을 붙여서 "의미"에 집중할 수 있다.

```javascript
const defaultTheme = {
  colors: {
    background: "#FFF",
    text: "#000",
    primary: "#F00",
    secondary: "#00F",
  },
};

export default defaultTheme;
```

App 컴포넌트에서 ThemeProvider의 props로 defaultTheme를 넘겨준다.

```javascript
import { ThemeProvider } from "styled-components";

import { Reset } from "styled-reset";

import defaultTheme from "./styles/defaultTheme";

import GlobalStyle from "./styles/GlobalStyle";

export default function App() {
  return (
    // ThemeProvider를 통해 아래에 있는 모든 컴포넌트에 theme 전달
    <ThemeProvider theme={defaultTheme}>
      <Reset />
      <GlobalStyle />
      <Greeting />
    </ThemeProvider>
  );
}
```

defaultTheme의 타입을 잡아주기 위해 Theme.ts 파일을 하나 더 만든다.

```javascript
import type defaultTheme from "./defaultTheme";

type Theme = typeof defaultTheme;

export default Theme;
```

styled-compnents 모듈의 기본 테마가 Theme 타입을 상속하도록 정의, 이미 존재하는 타입들에 추가적인 타입을 선언할 수 있도록 함.

```javascript
import 'styled-components';

import type Theme from './Theme';

declare module 'styled-components' {
	// eslint-disable-next-line @typescript-eslint/consistent-type-definitions
	export interface DefaultTheme extends Theme {}
}

```

이렇게 기본 테마를 만들어 두었으니 다크 모드 테마를 짤 때도 손쉽게 할 수 있다.

```javascript
import type Theme from "./Theme";

const darkTheme: Theme = {
  colors: {
    background: "#000",
    text: "#FFF",
    primary: "#F00",
    secondary: "#00F",
  },
};

export default darkTheme;
```

App에서 useDarkMode() 훅을 활용하면 더 쉽게 다크모드를 구현할 수 있다.

```javascript
import { useDarkMode } from "usehooks-ts";

import { Reset } from "styled-reset";

import { ThemeProvider } from "styled-components";

import defaultTheme from "./styles/defaultTheme";
import darkTheme from "./styles/darkTheme";

import GlobalStyle from "./styles/GlobalStyle";

import Greeting from "./components/Greeting";
import Switch from "./components/Switch";
import Button from "./components/Button";

export default function App() {
  const { isDarkMode, toggle } = useDarkMode();

  const theme = isDarkMode ? darkTheme : defaultTheme;

  return (
    <ThemeProvider theme={theme}>
      <div>
        <Reset />
        <GlobalStyle />
        <Button onClick={toggle} active={isDarkMode}>
          Toggle Dark Mode
        </Button>
        <Greeting />
        <Switch />
      </div>
    </ThemeProvider>
  );
}
```
