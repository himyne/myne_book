# 9-10주차 모르는 내용 정리

강의를 듣고 코드를 따라가다가 잘 모르거나 헷갈렸던 부분만 따로 정리했다.

## 이중 부정

왜 느낌표를 두개 사용해서 이중부정을 하는걸까?에 대한 의문

해결 -> 느낌표 두개 연산자는 null이나 undefined 값을 false로 변환할 때 사용하면 좋다고 한다. falsy한 값들을 처리할 때 사용하면 좋을 것 같다.

### falsy한 값 예

false, undefined, null, 0, -0, NaN, ‘’

```javascript
get valid() {
  return this.email.includes('@') && !!this.password;
}
```

강의 코드 중 !!this.password를 하는 부분에서 falsy값인 ''가 들어오면 false로 판단하여 유효성에서 false를 처리할 수 있다.

## 제네릭

타입스크립트를 자세히 공부하지 않아서 제네릭에 대한 이해가 부족했다.

### 제네릭이란?

> 데이터의 타입을 일반화한다는 뜻이다.

제네릭은 자료형을 딱 정하는게 아니라 여러 타입을 사용할 수 있게 해준다.

한번 선언으로 다양한 타입에 재사용이 가능하여 좋다. 또한 any처럼 타입을 지정하지는 않지만 타입을 체크하기 때문에 컴파일러가 오류를 찾을 수 있다.

예를 들어 sort함수의 경우 숫자, 문자열 등 다양한 타입의 배열을 정렬할 수 있는데 이 때 제네릭을 사용하면 타입을 선언할 때 지정해주지 않아도 추후에 타입을 지정할 수 있게 된다.

```typescript
function sort<T>(arr: T[]): T[] {
  //...
}
```

```javascript
type ComboBoxProps<T> = {
  label: string,
  selectedItem: T,
  items: T[],
  itemToId: (item: T) => string,
  itemToText: (item: T) => string,
  onChange: (item: T | null) => void,
};
```

위 코드에서는 T라는 ComboBoxProps에 아무 자료형이나 와도 된다는 것이다. 예를 들어 itemToId은 T타입을 인자로 받아 string으로 반환하는 함수이다.

컴포넌트의 재사용성에서도 좋다고 하는데 이건 아직 와닿지 않아 타입스크립트를 제대로 공부해봐야할 것 같다.

## getByRole

특정 역할을 가진 요소를 검색하고 반환한다.

```javascript
screen.getByRole("link", { name: "Home" });
```

위 코드에서 link는 HTMLElement이고, name: "Home"은 링크의 text가 Home과 일치하는 것을 찾아 선택하는 것이다.

```javascript
getByRole(
  // If you're using `screen`, then skip the container argument:
  container: HTMLElement,
  role: string,
  options?: {
    hidden?: boolean = false,
    name?: TextMatch,
    description?: TextMatch,
    selected?: boolean,
    busy?: boolean,
    checked?: boolean,
    pressed?: boolean,
    suggest?: boolean,
    current?: boolean | string,
    expanded?: boolean,
    queryFallbacks?: boolean,
    level?: number,
    value?: {
      min?: number,
      max?: number,
      now?: number,
      text?: TextMatch,
    }
  }): HTMLElement
```
