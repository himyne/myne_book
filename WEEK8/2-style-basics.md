# Style Basics

## 학습 키워드

- [x] className
- [x] 의미있는 마크업

> 💡 의미있는 마크업을 하기 위해서는 HTML Element 들이 어떤 의미를 가지고 있는지 정확하게 알고 있어야 합니다. 레퍼런스를 참고하여 HTML 마크업에 대해 정확하게 이해한 뒤 정리하고 넘어가시면 좋습니다.

참고 레퍼런스: [https://developer.mozilla.org/ko/docs/Web/HTML/Reference](https://developer.mozilla.org/ko/docs/Web/HTML/Reference)

## className

jsx에서 html에 class를 넣을 때는 className을 사용해야 한다.

자바스크립트에서 class는 예약어이기 때문에 className이라고 씀.

## 의미있는 마크업

> 각 HTML 코드에서 각 요소가 무엇을 나타내는지 명확히 정의하여 웹 페이지의 구조와 의미를 이해하기 쉽게 만드는 것

웹 표준을 지키고 의미론적으로 마크업을 작성하는 것은 매우 중요함.

### 시맨틱 태그

> 시맨틱이 '의미론적인'이라는 뜻으로 의미를 부여한 태그를 말함.

div나 span처럼 의미가 없는 태그들은 태그 이름만 보고 어떤 내용인지 유추가 되지 않는다.

반면 form, table, article 등 의미가 있는 태그들은 내용을 정확하게 정의한다.

시맨틱 태그를 사용함으로써 검색엔진최적화(SEO), 시각장애 사용자의 스크린 리더, 기업 및 커뮤니티에서 데이터를 공유하고 재사용할 수 있다는 장점이 있다.

![웹사이트 구조](/images/web-structure.jpg)

### 1. Meta Data

책의 표지와 같은 역할로 검색엔진에게 해당 페이지가 전반적으로 어떤 내용을 담고 있는지 알려준다.

📌 원티드의 메타 데이터 예시

```html
<html lang="ko">
  <!--어떤 언어 사이트가 구성되어있는지 명시-->
  <head>
    <!-- 문서 정보 -->
    <meta charset="utf-8" />
    <!-- 문자가 utf-8로 인코딩됨-->
    <meta name="description" content="커리어 성장과 행복을 위한 여정,..." />
    <!-- 검색에 노출되는 키워드를 쓰기 용이함 -->
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <!-- 반응형 웹 지원 여부 판단 가능 -->
    <title>커리어 여정을 행복하게, 원티드</title>
    <!-- 검색엔진이 가장 먼저 접하는 메타정보-->
  </head>
</html>
```

메타 데이터들은 `<head> </head>`에 포함된다.

<head> 요소는 기계가 식별할 수 있는 문서 정보를 담는다. 보통 검색 엔진 등 기계 처리를 위한 정보이니 사람이 읽을 수 있는 정보는 <header>요소에 넣는 것이 좋다.

### 2. outline/heading 구성

글을 쓸 때 주제에 따라 단락을 나누듯이 마크업을 작성할 때도 시맨틱한 섹션과 heading으로 내용을 구분하면 좋다.

- `<section>` : 이름처럼 내용을 주제별로 나누거나 묶을 때 사용하는 태그
- `<nav>` : 사이트의 네비게이션 탐색 메뉴를 나눌 때 사용되는 태그. 주로 `<li>`와 같이 사용된다.
- `<article>` : 독립적으로 출판될 수 있는 내용을 포함(예: 인스타 피드, 기사 내용, 대화형 위젯, 블로그 글, 제품 카드)
- `<header>, <footer>` : 보통 문서의 GNB(Global Navigation Bar)를 header의 자손으로 하고, 회사 정보 등을 footer에 넣는다.
- `<main>` : 문서의 핵심 주제나 핵심 기능과 관련있는 콘텐츠 작성. 한 페이지에 하나의 `<main>`태그가 올 수 있다.
- `<heading>` : heading은 상하관계를 잘 고려해서 작성해야한다.
- `<aside>` : 사이드바 또는 콜아웃 상자로 사용됨

![토스 개발팀 문서 heading 예시](/images/toss_heading.jpg)

### 3. 자주 사용되는 시맨틱 태그들

- `<dialog>` : 닫을 수 있는 경고창, 검사기 등 팝업 대화 상자에 쓰인다.
- `<figure>, <figcaption>` : 독립적인 콘텐츠를 표현. `<figcaption>` 요소를 사용해 설명을 붙일 수 있다.
- `<mark>` : 현재 맥락에서 중요하다는 표시, 즉 하이라이트한 부분을 나타낸다.
- `<address>` : 사람, 단체, 조직 등에 대한 연락처 정보
- `<progress>` : 어느 작업의 완료 정도. 주로 진행 표시줄의 형태
- `<strong>` : 굵은 글씨로 표시되는 강조하는 컨텐츠(보통 p태그 안에서 특정 단어를 따로 스타일링 할 경우 사용)
- `<em>`: 텍스트의 강세(기울임). strong 태그와 유사하게 사용
- `<textarea>`: 두 줄 이상의 텍스트를 편집하는 박스
- `<datalist>`: 고를 수 있는 옵션들을 추천하는 선택지. option 요소를 여럿 담는다.
- ``
- `<option>`: select, optgroup, datalist 요소 안에 항목을 나타내는 옵션
- `<fieldset>`: 웹 양식의 여러 label을 묶을 때 사용한다. `<legend>` 요소로 그룹의 설명을 제공할 수도 잇다.

## css-in-js의 기본적인 방법

```html
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <title>React Demo App</title>
    <style>
      .greeting {
        color: #00f;
      }
    </style>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="./src/main.tsx"></script>
  </body>
</html>
```

## Basic: Inline Style

```javascript
export default function Greeting() {
  return (
    <p
      style={{
        color: "#00F",
      }}
    >
      Hello, world!
    </p>
  );
}
```

이 방법에서는 사실상 style이 css가 아닌 js라고 볼 수 있다.
js의 객체처럼 사용하기 때문에 타입스크립트의 도움을 받을 수도 있다.

```javascript
const darkMode = false;

function primaryColor() {
  return darkMode ? "#F00" : "#00F";
}

export default function Greeting() {
  return (
    <p
      style={{
        color: primaryColor(),
      }}
    >
      Hello, world!
    </p>
  );
}
```
