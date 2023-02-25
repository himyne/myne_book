# React Hook에서 자주 사용하는 것들

- useState : 상태 관리를 위한 훅
- useEffect : 부수 효과(side effect)를 관리하기 위한 훅
- useContext : Context API를 사용하기 위한 훅
- useRef : 변수나 DOM 요소를 저장하기 위한 훅
- useLayoutEffect : useEffect와 비슷하지만, 렌더링 이후에 동기적으로 실행되는 훅

등이 있는데 useState와 useEffect는 다뤘으니 useContext부터 알아보자.

이외에 useMemo, useReducer 등 많으니 추후에 사용해보고 공부해야겠다.

## 1. useContext

함수형 컴포넌트에서 Context API를 더 간편하게 사용할 수 있게 해준다.

{% hint style="info"%}

### Context API란?

컴포넌트 트리 안에서 전역적으로 데이터를 공유할 수 있게 해준다. 예를 들어, 로그인 정보, 언어, 테마와 같은 애플리케이션 전체에서 사용되는 정보를 한 번에 관리할 수 있다.

리액트는 데이터를 상위 컴포넌트에서 하위 컴포넌트로 props를 이용해 전달한다.

그런데 하위 컴포넌트들에서 계속 사용해야 하는 로그인 정보, 테마와 같은 데이터가 있다면 props로 전달하기 보다는 전역에서 관리하는 것이 편리할 것이다.

Context API를 이용하면 props가 전달하지 않아도 데이터가 필요한 컴포넌트들만 사용할 수가 있다.

{% endhint %}

```javascript
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee",
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222",
  },
};

// createContext의 인자에는 초기값을 넣는다.
const ThemeContext = React.createContext(themes.light);

//최상위 컴포넌트
function App() {
  return (
    // Provider의 value에는 넘겨주어야 할 값들을 넣어준다.
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

// 중간 컴포넌트는 테마 정보를 인자로 받지 않음
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

// 하위 컴포넌트이지만 props 없이 useContext로 전역 데이터인 테마정보를 가져올 수 있다.
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      I am styled by theme context!
    </button>
  );
}
```

## 2. useRef

변수나 DOM 요소를 저장하기 위한 훅이다

useRef함수는 ref객체를 반환하는데 ref객체는 `{ current: value }`가 들어있다.

```javascript
const ref = useRef(initialValue);
```

initialValue인 초기값은 언제든지 우리가 원하는 값으로 바꿔줄 수 있다.(ex - ref.current = "minhye")

반환된 ref객체는 컴포넌트가 언마운트되기 전까지는 값을 유지한다.

### useRef가 유용한 경우

- state가 변해도 같게 유지되어야 하는 변수 값을 저장하는 저장공간이 필요할 때
- DOM 요소에 접근할 때 => input 요소를 클릭하지 않아도 자동으로 focus를 줄 때(document.querySelector와 비슷)

```javascript
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

## 3. useLayoutEffect

화면 성능을 높일 수 있는 훅이다. useEffect와 비슷하지만 렌더링 전에 작업이 실행된다는 점이 다르다.

useEffect는 렌더링 후에 약속한 함수를 실행시켜주는 훅이었다. 그래서 화면이 리렌더링될 때 useEffect로 인해 바뀐 부분은 조금 지연되어 렌더링될 수가 있고 사용자 입장에서는 화면 깜빡임을 느낄 수도 있다.

컴포넌트가 업데이트 될 때 브라우저의 동작 순서를 보자

Render =>

React updates DOM => Cleanup LayoutEffects => Run LayOutEffects => (useLayoutEffect 실행 부분)

Browser paints screen => cleanup effects => run effects (useEffect 실행 부분)

useLayoutEffect와 useEffect는 동작 순서를 통해 알 수 있듯이 동작하는 위치만 다를 뿐이다.

다만 useLayoutEffect를 사용하면 브라우저에 페인팅되기 전에 일어나므로 화면 깜빡임 현상을 없앨 수 있다.(사용자 성능 향상)

> 기본적으로는 useEffect를 사용하되 화면 깜빡임이 보이는 것 같다면 사용하는 것이 좋다.
