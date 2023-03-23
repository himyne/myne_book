# CSS in JS

- [x] CSS in JS 란
- [x] CSS

> CSS 를 잘 사용하기 위해선 CSS 자체에 대해서 잘 알고 있어야 합니다. 레퍼런스를 참고하여 CSS 를 정리해봅시다. 스타일 자체는 굉장히 많기 때문에 자주 사용하는 것들 위주로 찾아서 정리하시면 좋습니다. 특히나 선택자, 박스모델, media query, Flexbox, Grid 정도는 필수적으로 알고 계셔야 합니다.

참고 레퍼런스:

- [https://developer.mozilla.org/ko/docs/Web/CSS/Reference](https://developer.mozilla.org/ko/docs/Web/CSS/Reference)
- [flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)

## css-in-JS 정의

아래처럼 스타일된 컴포넌트를 사용하는 것, 즉 자바스크립트 코드 안에 css를 작성하는 기술을 말한다.

inline 스타일과는 DOM노드를 연결하는 방식에서 차이가 있다.

```javascript
const style = {
  color: red;
  backgroundColor: black;
}

<p style={style}>inline style</p>
```

```html
<p style="color: red; background-color: black;">inline style</p>
```

위 방식처럼 p태그 안에 css가 객체로 들어가는 것이 inline style이고

```javascript
import styled from "styled-components";
// Create a component that renders a <p> element with blue text
const BlueText = styled.p`
  color: blue;
`;

//이렇게 쓰는 것을 css-in-js라고함
<BlueText>My blue text</BlueText>;
```

```html
<style>
  .hash134s21 {
    color: white;
    background-color: black;
  }
</style>

<p class="hash134s21">Hello CSS-in-JS</p>
```

css-in-js는 DOM 상단에 `<style>` 태그를 추가하여 p 태그의 class 속성으로 스타일이 들어가는 방식이다.

자바스크립트 객체로 스타일을 넣으면 :before, html, body 태그들은 지원하지를 않는다고 하는데 CSS in JS는 인라인 스타일에 비해 모든 CSS를 사용할 수 있다.

또한 css in js를 사용하면 컴포넌트로 생각할 수 있다는 큰 장점이 있다. css 모델을 문서 레벨이 아닌 컴포넌트 레벨로 추상화 할 수 있기 때문에 유지보수에 더 편리하다.

[통합 스타일링 언어](https://blog.rhostem.com/posts/2017-06-24-unified-styling-language)

[CSS-in-JS에 관해 알아야 할 모든 것](https://d0gf00t.tistory.com/22) - 중요한건 Thinking in components

## css-in-js의 단점

[CSS-in-JS와 성능](https://hyeonseok.com/blog/877)

[우리가 CSS-in-JS와 헤어지는 이유](https://junghan92.medium.com/%EB%B2%88%EC%97%AD-%EC%9A%B0%EB%A6%AC%EA%B0%80-css-in-js%EC%99%80-%ED%97%A4%EC%96%B4%EC%A7%80%EB%8A%94-%EC%9D%B4%EC%9C%A0-a2e726d6ace6)

1. css가 js에 포함된 경우와 파일로 분리한 경우의 성능을 비교했을 때 css in js가 성능이 더 좋지 않다.

실제 비교를 해본 결과 특정 사용자 인터랙션 블로킹 시간은 2배 가까이 차이가 나고 그 차이도 1초로 상당히 컸다고 한다.

2. 번들 크기를 늘린다.(styled-components는 12.7kb)
3. 리액트 devTools를 어지럽힌다, 런타임 오버헤드를 더한다.

이 외에도 많은 장단점들이 있으니 이런 장단점을 평가하고 해당 기술이 사용 사례에 적합한지 여부에 따라 결정을 내리는 것이 좋다고 한다.

### css-in-js의 성능 이슈에 대한 대안

- [Linaria](https://linaria.dev/)
  - CSS를 평범한 텍스트로 작성.
  - React에 종속적이지 않지만, React Styled Component도 지원함.
- [vanilla-extract](https://vanilla-extract.style/)
  - CSS를 오브젝트 형태로 표현. React의 Inline Style과 유사함.
  - React와 무관하게 사용 가능.
