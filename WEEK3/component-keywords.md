# REST API와 GraphQL

## REST API

### API(Application Programming Interface)란?

애플리케이션 프로그래밍 인터페이스라는 용어에서 인터페이스만 조금 낯설어서 용어를 찾아보았다.

내(사용자)가 컴퓨터에 글을 쓰기 위해 키보드라는 매개체를 이용한다. 이 때 키보드를 컴퓨터와 나 사이의 인터페이스라고 한다.

그렇다면 애플리케이션 프로그래밍 인터페이스라는 것은 애플리케이션 간의 매개체라고 생각할 수 있겠다.

정확한 API의 정의는 정의 및 프로토콜 집합을 사용하여 두 소프트웨어 구성 요소가 서로 통신할 수 있게 하는 메커니즘이다.

조금 더 쉽게 이해하자면 어떤 소프트웨어가 다른 소프트웨어로부터 지정된 형식으로 요청받을 수 있는 수단인 것이다.

### REST API란?

API가 동작하는 4가지 방식이 있는데 그중 가장 많이 사용되고 유연한 API이다.

REST는 HTTP를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍쳐이고, REST API란 REST 아키텍쳐 스타일에 부합하는 API를 말한다.

조금 더 실질적인 이해를 해보자면 개발자들끼리 데이터를 주고받을 때 요청하는 사람이 있을 것이고 응답하는 사람이 있을 것이다.

이때 요청하는 사람이 아무렇게나 요청하는게 아니라 정해진 형식, 규약에 맞추어 요청을 한다면 이 규약을 알고 있는 모든 개발자들은 데이터를 주고받기 더 수월할 것이다.

그래서 REST 규칙을 지킨 REST API가 널리 사용되는 것이고 REST API는 프론트엔드가 서버에 데이터를 요청할 때 주로 사용되는 개념이므로 알고있는 것이 좋다.

### REST란?

로이필딩이라는 사람에 의해 처음 정의되었다.

Representational State Transfer => 한국어로 바꾸면 상태 전달 표현이다.

나는 일단 용어 자체만 봤을 때 바뀌는 자원들의 상태를 서버와 클라이언트 사이에서 전달할 때 사용되는 표현/규칙이라고 이해했다.

REST 아키텍쳐 스타일에 부합하면 RESTful하다고 한다.

RESTful하게 만든 API는 요청을 보내는 주소만 봐도 대략 뭘 요청하는지 알 수가 있다.

### REST의 구성 요소

REST는 자원(Resource), 메소드(Method), 메세지(Message) 이 세가지로 구성되어 있다.

**1. 자원**

자원은 이름을 지닐 수 있는 모든 정보, 이미지, 문서 등 개념적이고 실존하는 대상을 뜻한다. 그리고 자원은 객체로 보기 때문에 변하지 않는 식별자가 필요하다.

그래서 `/user/1`과 같이 URI(Uniform Resource Identifier)를 통해 자원을 식별하고 접근하는 것이다.

URI를 작성할 때도 규칙이 있다.

- `/`는 계층 관계를 나타낸다.
- URI를 이루는 자원들은 동사가 아닌 명사로 이루어져 있어야 한다.
- URI에서는 \_보다 -를 권장한다.
- URI 스키마(http://)와 호스트(www.naver.com)를 제외하고는 소문자를 사용한다.

이제 자원은 URI로 식별되고 표현될 수 있다는 것을 알았다.

**2. 메소드**

우리가 자원을 요청할 때 어떤 행위로 요청할 것인지를 알려주는 것이 메소드이다. 예를 들어 학교에 학생 정보를 요청한다고 해보자.

반에 5번 학생의 데이터를 그냥 조회만 할 수도 있고, 6번 학생을 추가하여 데이터를 생성할 수도 있고, 3번 학생을 삭제할 수도 있다. 또한 4번 학생의 이름을 수정할 수도 있는 것이다.

이렇게 읽기, 생성, 삭제, 수정이라는 행위 4가지로 분류되고 이것들을 메소드라고 하는 것이다.

Create 생성 - POST

Read 읽기 - GET

Update 수정 - PUT | PATCH (전체적인 데이터 수정은 PUT, 일부 데이터 수정은 PATCH)

Delete 삭제 - DELETE

## GraphQL은 왜 등장했는가?

만약 REST API 방식으로 학교에 학생 한 명의 국어 성적을 알고 싶다고 하면 국어 성적을 포함한 학생 한 명의 모든 데이터를 받아와야 한다.

심지어 학생 한 명의 정보를 위해 반 아이들의 정보까지 모두 불러와야 한다.

이러한 과정에서 데이터의 양도 많아지고 반 정보, 학생 정보 등 요청이 여러 번 일어난다.

그런데 REST API와는 달리 GraphQL은 딱 한 번으로 원하는 정보만을 요청할 수 있다.

불필요한 HTTP 요청 횟수가 있고 항상 응답의 사이즈가 크다는 REST API의 단점을 보완하기 위해 등장한 것이 GraphQL이다.

[이해를 도운 블로그 글](https://c17an.netlify.app/blog/front-end/graphql-%EB%A7%8C%EB%82%98%EB%B3%B4%EA%B8%B0-1%ED%8E%B8/article/)

## GraphQL

쿼리를 사용해서 필요한 데이터만 구체적으로 요청하고 받는 방식이다.

GraphQL이 가진 장점 덕분에 단순한 방식의 설계로 이루어진 프로젝트를 할 때는 개발 비용과 기간을 줄여줄 수 있다.

하지만 사양이 조금 복잡해진다면 응답보다 요청이 더 길어지고 클라이언트의 부담이 오히려 증가할 수 있다는 단점도 존재한다.

또한 하나의 엔드포인트로 요청을 보내기 때문에 HTTP 캐싱이 어렵기도 하다.(REST API는 같은 url 요청이면 기억을 해서 캐싱이 되는데 graphql은 캐싱이 안된다.)

데이터를 읽을 때 GraphQL은 쿼리문으로 요청을 한다. 그리고 나머지 수정, 삭제, 생성 등은 뮤테이션(Mutation)이 처리한다.

[Mutation 실습 블로그 글](https://velog.io/@tmdwns1521/NODEJS-Graphql-Mutation-%EA%B5%AC%ED%98%84)

## REST API vs GraphQL

REST API의 단점을 보완하기 위해 GraphQL이 개발되었고 필요에 따라 엄청 유리하게 사용할 수 있게 되었지만 완벽히 REST API를 대체할 수는 없다.

REST API와 GraphQL의 장단점을 따져보고 유연하게 자신의 상황에 맞추어 사용하면 된다.

데이터가 너무 방대한데 내가 쓰는 데이터는 한정되어 있다면 GraphQL을, 프로젝트의 규모가 크고 고정된 요청과 응답만 필요한 상황이라면 캐싱이 가능한 REST API를 사용하는 것이 좋을 것 같다.

# JSON

백엔드에서 주는 데이터는 JSON 형식으로 된 데이터라고 강의에서 배웠다.

## JSON이란??

[이전에 정리했던 블로그 글](https://himyne.github.io/deepdive/deepdive-43/#02-json)

json은 순수한 텍스트로 구성된 규칙이 있는 데이터 구조이자 클라이언트와 서버 간의 http 통신을 위한 텍스트 데이터 포맷이다.

나는 아직 json 형식의 데이터를 다뤄본적은 없고 `package.json`파일에서 가장 많이 사용해봤던 것 같다.

### JSON 표기 방식

```json
{
  "name": "Kang",
  "age": 20,
  "alive": true,
  "hobby": ["traveling", "tennis"]
}
```

중괄호 안에서 쌍따옴표를 이용해서 표기해야 한다.

일반 자바스크립트의 객체처럼 원하는 만큼 중첩시켜서 사용할 수도 있다.

JSON형식에서는 null, number, string, array, object, boolean을 사용할 수 있다.

# DSL(Domain-Specific Language) : 도메인 특화 언어

DSL은 특정 기능이나 영역을 위해 만들어진 언어이기 때문에 DSL 언어로 하나의 애플리케이션을 만드는 것은 불가능하다.

주로 아래 작업들을 처리하기 위해 많이 사용된다.

- UI 디자인
- 소프트웨어 설정 설명
- 테스트 사양
- 작업 흐름 규칙 정의
- 데이터 조작(SQL)

범용적으로 사용하는 언어가 아니라 특수 목적을 위해 만들어진 언어이므로 보통 단순하고 이해하기 쉬운 문법이다.

그래서 코드의 가독성도 좋고 간결하다. 잘 설계된 DSL을 이용하면 관심사가 분리되어 유지보수가 쉬워질 수 있다.

[DSL 같은 React 컴포넌트 작성](https://medium.com/@aitherios/write-react-components-like-a-dsl-c02d64b499ef)

영어 글이지만 DSL과 React UI를 연관지어 이해하기 가장 좋은 글이다.

# 선언형 프로그래밍과 명령형 프로그래밍

길찾기로 예를 들어 선언형 프로그래밍과 명령형 프로그래밍을 이해해보자.

내가 가고 싶은 목적지가 서울역이라고 가정하면, 명령형은 출발지에서 서울역까지 가는 과정(HOW)을 하나하나 상세히 설명한다.

그에 반해 선언형은 출발지와 목적지(WHAT)만 정의하고 구체적인 길을 찾아가는 과정은 설명하지 않는다.

이런 경우 명령형은 당연히 명령한 대로 결과가 나오겠지만 선언형은 어떻게 나올까?

목적지인 서울역까지 가는 방법을 이미 알고 있기 때문에 선언만으로 길찾기를 할 수 있는 것이다. (대신에 명령형 레이어가 그 안에 들어있겠지만)

## 리액트에서 선언형 프로그래밍이 가능하다는 것은?

우리가 보편적으로 사용하는 언어들은 모두 명령형 프로그래밍 기법이다.

선언형 프로그래밍은 위에서 다뤘던 DSL의 형태로 자주 사용된다고 한다.

웹 페이지를 표현하기 위해 만들어진 HTML도 특수 목적을 위해 만들어진 언어이므로 DSL이기도 하다.

실제 웹페이지를 작성할 때 HTML에서 무엇을 보여주어야 하는지 선언하면 브라우저의 절차적 알고리즘으로 화면에 표시할 점들로 변환해준다.

그런데 리액트도 HTML과 유사한 모양으로 UI를 구성한다.

결론적으로 리액트는 HTML과 유사한 모양의 DSL을 사용한 선언형 프로그래밍이 가능한 프레임워크라는 것이다.
