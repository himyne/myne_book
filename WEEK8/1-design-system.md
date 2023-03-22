# Design-system

- [x] 반응형 웹 디자인(Responsive web design)
- [ ] 디자인 시스템(Design System)
- [ ] Atomic Design

## 반응형 웹 디자인

> 모든 화면 크기와 해상도에서 잘 렌더링되고 사용성이 좋은 웹 페이지를 만들기 위한 디자인 접근 방식

반응형 웹 디자인은 다양한 디바이스 크기에 대응하는 접근 방식이므로, 태블릿, 휴대폰, 텔레비젼 등 어떤 디바이스에서도 화면에 맞게 자동으로 조정이 가능하다.

보통 유동 그리드, 유동 이미지, 미디어 쿼리를 사용하여 반응형 컨텐츠들을 생성한다.

반응형 웹 디자인은 별도의 기술이 아니다라는 것을 이해하는 것이 중요히다. 즉, 웹 디자인에 대한 접근 방식이나 콘텐츠를 보는데 사용되는 장치에 반응할 수 있는 레이아웃 생성에 사용되는 모범 사례 집합을 기술하는 데 사용되는 용어다.

### 미디어 쿼리

미디어 쿼리를 사용하면 사용자의 화면이 특정 너비나 해상도보다 큰지를 테스트하고 CSS를 선택적으로 적용하여 사용자의 요구에 맞게 스타일을 지정할 수 있다.

```css
@media screen and (min-width: 80rem) {
  .container {
    margin: 1em 2em;
  }
}
```

`min-width: 80rem`이라는 조건에 못미치면 아래 내용은 적용되지 않는다. 위 코드는 최소 너비가 80rem(1280픽셀) 이상일 때 .container 클래스가 적용되고 마진이 위 아래 1em, 좌우 2em으로 설정된다.

미디어 쿼리가 도입되고 레이아웃이 변경되는 지점을 중단점이라고 한다. 중단점을 사용할 때 절대 단위가 아닌 상대 단위로 정의하는 것이 좋다.

그리고 일반적으로 화면이 좁은 디바이스(모바일)의 레이아웃을 만들고 더 넓은 화면의 레이아웃을 구현한다.

미디어 쿼리는 반응형 웹 디자인에 도움이 될 수는 있지만 필수는 아니다. 유동 그리드, 상대 단위, 최소 최대 단위 값은 쿼리 없이도 사용이 가능하다.

### 다중 열(Multicol)

다중 열을 사용하면 열의 개수나 너비만큼 콘텐츠를 분할할 수 있다.

```css
.container {
  column-count: 3;
}

.container {
  /* 폰트 크기의 10배 */
  column-width: 10em;
}
```

![](/images/multicor.jpg)

### Flexbox

```css
@media screen and (min-width: 600px) {
  .wrapper {
    display: flex;
  }

  .col1 {
    flex: 1;
    margin-right: 5%;
  }

  .col2 {
    flex: 2;
  }
}
```

[flexbox 실습 예제](https://github.com/mdn/css-examples/blob/main/learn/rwd/flex-based-rwd.html)

[flexbox 소스 코드](https://github.com/mdn/css-examples/blob/main/learn/rwd/flex-based-rwd.html)

### css 격자(grid)

```css
@media screen and (min-width: 600px) {
  .wrapper {
    display: grid;
    grid-template-columns: 1fr 2fr;
    column-gap: 5%;
  }
}
```

+) 이 외에도 반응형 이미지, 반응형 폰트 등이 있다.
+) grid, flex 추가 공부, 실습 필요

## 디자인 시스템

> 웹이나 각종 서비스 UI 디자인에서 재사용 가능한 컴포넌트와 패턴을 정의하여, 전체 디자인에 일관성 있게 적용할 수 있도록 만든 가이드라인이나 규칙이다.

디자인 시스템이란 개념과 리액트 컴포넌트 개념이 잘 맞아 떨어져서 사용한다.

[Laura Kalbag의 Design Systems](https://24ways.org/2012/design-systems/)
유행하는 반응형 디자인을 만드신분

=> [내가 번역한 글](/WEEK8/laura-kalbug-design-systems.md)

[발표 자료](https://speakerdeck.com/laurakalbag/design-systems-1)

조금 더 실천적인 것

=> [Design Systems 101](https://www.nngroup.com/articles/design-systems-101/)

위 문서 요약, 정의 부분 발췌

- Summary: A design system is a set of standards to manage design at scale by reducing redundancy while creating a shared language and visual consistency across different pages and channels.

- 요약: 디자인 시스템은 여러 페이지와 채널에서 공유 언어와 시각적 일관성을 유지하면서 중복을 줄여 대규모로 디자인을 관리하기 위한 일련의 표준입니다.

- A design system is a complete set of standards intended to manage design at scale using reusable components and patterns.

- 정의: 디자인 시스템은 _재사용 가능한_ 구성 요소와 패턴을 사용하여 대규모로 디자인을 관리하기 위한 완전한 표준 세트입니다.

Jakob Nielsen과 Donald Norman은 UX 분야에서 전설적인 인물들이다. 사용자 입장에서 디자인을 하는 데에서 되게 유명함. 아샬이 자주 참고한다(이 회사엔 없지만 앨런 쿠퍼도 포함).

- [제이콥 닐슨](https://ko.wikipedia.org/wiki/제이콥_닐슨)
- [도널드 노먼](https://ko.wikipedia.org/wiki/도널드_노먼)
- [앨런 쿠퍼](https://en.wikipedia.org/wiki/Alan_Cooper)

### 사례

- [Atlassian Design System](https://atlassian.design/) : 중요 - color pallete, components(코드롤 제공됨)
- [Material Design (Google)](https://material.io/) :
- [Base Web (Uber)](https://baseweb.design/)
- [Polaris (Shopify)](https://polaris.shopify.com/): 우리나라보단 외국에서 유명
- [Lightning Design System (Salesforce)](https://www.lightningdesignsystem.com/) : 위와 동일(아코디언 - 일관된 방식으로 작동할 수 있도록 제공함)
- [Mailchimp Pattern Library](https://ux.mailchimp.com/patterns)
- [Ant Design](https://ant.design/)

+) 피그마, 스케치 활용

### Gallery

- [Design Systems Gallery](https://designsystemsrepo.com/design-systems/)
- [Design Systems](https://www.designsystems.com/open-design-systems/)

## Atomic Design

> [Atomic Design 소개 글](https://bradfrost.com/blog/post/atomic-web-design/)

> [Atomic Design 전자책](https://atomicdesign.bradfrost.com/)

원자 => 분자 => 조직 => 템플릿 => 페이지의 계층 구조로 나누는 디자인 패턴

카카오팀에서 사용했던 후기에 대한 블로그 글이 있다.

[아토믹 디자인을 활용한 디자인 시스템 도입기](https://fe-developers.kakaoent.com/2022/220505-how-page-part-use-atomic-design-system/)

역시 무조건 따르기보다는 개발 상황에 맞게 디자인 시스템을 구축하는 것이 좋다고 한다.
