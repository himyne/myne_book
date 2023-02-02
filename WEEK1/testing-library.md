# 4. Testing Library

## Jest

거의 모든 것을 갖춘 테스트 도구이다.

```javascript
function add(x: number, y: number): number {
  return (x * 10 + y * 10) / 10;
}

const context = describe;

describe("add 함수는", () => {
  it("두 숫자의 합을 리턴한다", () => {
    expect(add(1, 2)).toBe(3);
  });

  context('두 개의 양수가 주어졌을 때', () => {
    it('항상 두 개의 숫자보다 큰 값을 돌려준다', () =>{
      expect(add(1,2)).toBeGreaterThan(2);
    });
  })

  context('0.1과 0.2가 주어져도', () => {
    it('이상한 값을 돌려주지 않는다', () =>{
      expect(add(0.1,0.2)).toBe(0.3);
    });
  })
});
```

## React testing library

UI 테스트에 특화된 라이브러리이다. 정말 사용자가 처음부터 끝까지 프로그램을 쓰는 것처럼 E2E 테스트를 만들 수 있다.

간단한 테스트 코드를 만들어보자

{% code title="Greeting.tsx" overflow="wrap" lineNumbers="true" %}
```javascript
export default function Greeting({name}: {
	name: string;
}) {
	return (
		<p>Hello, {name}</p>
	);
}
```
{% endcode %}

{% code title="Greeting.test.tsx" overflow="wrap" lineNumbers="true" %}
```javascript
import {render, screen} from '@testing-library/react';
import Greeting from './Greeting';

test('Greeting', () => {
	render(<Greeting name='world' />);

  screen.getByText('Hello, world');

  screen.getByText(/Hello/);
  screen.queryByText(/Hi/);
  
  expect(screen.queryByText(/Hi/)).toBeFalsy();
  expect(screen.queryByText(/Hi/)).not.toBeInTheDocument();
});
```
{% endcode %}

getByText에서 정규표현식을 이용하면 글자 전체가 아닌 일부만 테스트 가능하다.

queryByText는 괄호 안의 글자가 있는지 없는지 테스트해준다.

[jest-dom](https://github.com/testing-library/jest-dom)에 가면 테스팅 라이브러리들의 대한 설명이 자세히 나와있다.

그런데 프론트엔드에서 UI 테스트를 많이 작성할 필요는 없다. 조금만 UI를 바꿔도 테스트가 통과하지 못하기 때문이다.

그리고 테스트 도구도 계속 바뀔 수 있다. 현재는 jest와 React testing Library를 많이 쓰지만 이전에는 Mocha, chai등 다른 테스트 도구가 자주 쓰였었다. 현재 쓰는 도구가 언제든 밀려날 수 있고 트렌드가 언제든 바뀔 수 있다는 것을 알아두자.