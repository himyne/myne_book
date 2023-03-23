# Styled Components

- [x] styled-componets

> 유명한 라이브러리들은 대부분 ‘왜’ 만들어졌는지 혹은 품고 있는 철학 등이 잘 정리되어 있는 경우가 많습니다. 라이브러리를 사용할 때는 공식문서를 꼭 살펴보는 습관을 하시는 게 좋습니다.

참고 레퍼런스: [https://styled-components.com/docs/basics](https://styled-components.com/docs/basics)

## Styled-components란?

> 스타일 컴포넌트는 React 컴포넌트 시스템의 스타일을 지정하기 위해 CSS를 어떻게 개선할 수 있을지 고민한 결과이다.

## 왜 사용하는가?

스타일 컴포넌트는 개발자를 위한 향상된 경험 외에도 다음과 같은 장점들이 있다:

1. 자동 크리티컬 CSS: Styled Components는 페이지에서 어떤 컴포넌트가 렌더링되는지 추적하고 해당 스타일만 삽입하고 다른 것은 완전히 자동으로 삽입한다. 코드 분할과 함께 사용하면 사용자가 필요한 최소한의 코드를 로드할 수 있다.

2. 클래스 이름에 버그 없음: Styled Components는 스타일에 고유한 클래스 이름을 생성한다. 중복, 중복 또는 철자 오류에 대해 걱정할 필요가 없다.

3. 손쉬운 CSS 삭제: 코드 어딘가에서 클래스 이름이 사용되었는지 알기 어려울 수 있지만, Styled Components는 모든 스타일이 특정 컴포넌트에 연결되어 있기 때문에 명확하게 알 수 있다. 컴포넌트가 사용되지 않거나(도구가 감지할 수 있는) 삭제되면 모든 스타일도 함께 삭제된다.

4. 간단한 동적 스타일링: props나 글로벌 테마를 기반으로 컴포넌트의 스타일을 조정하면 수십 개의 클래스를 수동으로 관리할 필요 없이 간단하고 직관적으로 스타일을 적용할 수 있다.

5. 간편한 유지 관리: 컴포넌트에 영향을 미치는 스타일링을 찾기 위해 여러 파일을 뒤질 필요가 없으므로 코드베이스의 규모에 관계없이 유지 관리가 매우 간편하다.

6. 자동 벤더 프리픽스: 현재 표준에 맞춰 CSS를 작성하고 나머지는 스타일이 지정된 컴포넌트가 처리하도록 하라.

개별 컴포넌트에 바인딩만 하면 익숙한 CSS를 계속 작성하면서 이러한 모든 이점을 누릴 수 있다.

## styled-components 설치

```bash
npm i styled-components
npm i -D @types/styled-components @swc/plugin-styled-components
```

Babel Plugin을 swc에서 쓸 수 있도록 한 것도 함께 설치

`.swcrc` 파일 작성

```json
{
  "jsc": {
    "experimental": {
      "plugins": [
        [
          "@swc/plugin-styled-components",
          {
            "displayName": true,
            "ssr": true
          }
        ]
      ]
    }
  }
}
```

## 강의 실습 코드

비쥬얼 스튜디오 코드에서 vscode-styled-components 확장 프로그램 꼭 사용할 것

styled componenet에 추가로 스타일을 입히는 방법-> 괄호 넣어주기

```javascript
import styled from "styled-components";

const Paragraph = styled.p`
  color: #00f;

  strong {
    font-size: 2em;
    color: red;
  }
`;

//기존에 있는걸 끌어다 쓸 때 괄호에 넣어준다.
const BigParagraph = styled(Paragraph)`
  font-size: 2em;

  strong {
    font-size: 1em;
  }
`;

export default function Greeting() {
  return <BigParagraph>Hello, world!</BigParagraph>;
}
```

아래는 기존 컴포넌트에 스타일을 입히는 방식이다.

```javascript
import styled from "styled-components";
//styled가 className을 만들어주는 역할을 하는데 그냥 함수만 만들면 className을 만들어줄 수 없다. className을 인자로 받아야한다.
function HelloWorld({ className }: React.HTMLAttributes<HTMLElement>) {
  return <p className={className}>Hello, world!</p>;
}

const SmallHelloWorld = styled(HelloWorld)`
  font-size: 0.1em;
`;

export default function Greeting() {
  return <SmallHelloWorld />;
}
```
