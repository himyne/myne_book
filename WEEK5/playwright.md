# Playwright

- [x] E2E(End to End) Test
- [x] Headless Chrome
- [x] Puppeteer
- [x] Playwright
- [x] CodeceptJS

> Playwright이란? : 웹 브라우저 기반 E2E 테스트 자동화 도구

## E2E 테스트란?

> End To End 테스트의 약자로 애플리케이션의 흐름을 처음부터 끝까지 테스트하는 것을 의미한다.

실제 사용자가 애플리케이션을 사용하는 것과 똑같이 시나리오를 작성하고 그에 맞는 테스트 코드를 작성하여 테스트하는 것이다.

테스트 피라미드의 가장 꼭대기에 위치하여 이 테스트를 통과하면 애플리케이션의 무결성을 증명할 수 있다.

테스트 비용에 따라 E2E 테스트 10%, 통합 테스트 30%, 유닛 테스트 60% 와 같이 비중을 조절해야 한다.

## Headless Chrome

크롬은 GUI(Graphical User Interface) 없이 코드를 구현하는 헤드리스 자동화 테스트를 지원한다. Linux, macOS, Windows에서 사용할 수 있다.

### Headless Browser란?

> 사용자 인터페이스가 없는 웹 브라우저로, 다른 브라우저와 동일하지만 화면에서는 아무것도 볼 수 없다. (백그라운드에서 작동하는 브라우저)

이 장에서 배울 Playwright도 Headless 브라우저를 기반으로 한 것이다.

헤드리스 브라우저는 원래 우분투, Linux와 같은 GUI가 없는 경우에 명령 프롬프트를 통해 사용되고 더불어 우리가 공부하고 있는 테스팅에서도 유용하게 사용된다.

### Headless 브라우저의 장점

- 꼭 브라우저를 보지 않아도 테스트가 성공적으로 실행되는지만 지켜볼 경우에 사용할 수 있다. 다른 앱과 더불어 병렬적으로 테스트를 실행해야 할 때 UI 기반 브라우저는 메모리를 많이 사용하므로 헤드리스 브라우저를 사용하는 것이 좋다.
- 실제로 브라우저를 띄우고 테스트 하는것보다는 테스트 속도도 더 빠르다.

헤드리스 브라우저로 테스트하는 명령어 ⬇️

```bash
CI-true npx playwright test
```

## Puppeteer

Puppeteer 또한 E2E 테스트를 위해 만들어진 테스팅 도구이다. Playwright 이전에 Headless Chrome을 기반으로 만들어졌다. chrome과 Chromium 브라우저에 특화되어 있다.

> Chromium이란? : 구글에서 개발한 오픈소스 웹 브라우저 프로젝트이다. Opera, Microsoft Edge, Vivaldi, Brave 등이 Chromium 기반으로 작동함

puppeteer는 Google이 개발하고 유지하고 관리하는 반면 playwright은 ms에서 개발하고 있다. 둘 다 거의 유사한 기능이지만 puppeteer보다 playwright이 더 빠르고 안정적이며 메모리 사용량도 적다.

또한 playwright은 firefox, safari와 같은 다른 브라우저도 지원하여 멀티 브라우저 테스트를 수행하기 더 쉽다.

## Playwright 사용하기

Playwright은 테스팅을 위한 api들을 제공한다. 테스트 러너까지 제공해서 굉장히 깔끔하게 쓸 수 있다.

크롤링할 때 쓰기도 하는데 우리는 테스트를 할거니까 개발 환경으로 설치한다. eslint 플러그인도 같이 설치한다.

```bash
npm i -D @playwright/test eslint-plugin-playwright
```

최종적인 진짜 테스트이기 때문에 express 서버도 켜놓아야 한다.

그리고 playwright config 파일도 만들어야 한다.

```javascript
// playwright.config.ts
import { PlaywrightTestConfig } from "@playwright/test";

const config: PlaywrightTestConfig = {
  testDir: "./tests",
  retries: 0,
  use: {
    channel: "chrome",
    baseURL: "http://localhost:8080",
    //ci 환경이 잡혀있으면 headless로 띄운다.
    headless: !!process.env.CI,
    screenshot: "only-on-failure",
  },
};

export default config;
```

config 파일에서 설정한대로 test 디렉토리를 만들고 그 안에 home.spec.ts 파일에 테스트를 작성한다.

```javascript
//home.spec,ts

import { test, expect } from "@playwright/test";

test("Filter products", async ({ page }) => {
  await page.goto("/");

  await expect(page.getByText("Apple")).toBeVisible();

  const searchInput = page.getByLabel("Search");

  await searchInput.fill("a");

  await expect(page.getByText("Apple")).toBeVisible();

  await searchInput.fill("aa");

  await expect(page.getByText("Apple")).toBeHidden();
});

test("Click the “Increase” button", async ({ page }) => {
  await page.goto("/");

  const count = 13;

  // 전부 다 클릭되었을 때 다음 것 실행하기 (Promise가 리턴되려면  forEach가 아니라 map써야함)
  await Promise.all(
    [...Array(count)].map(async () => {
      await page.getByText("Increase").click();
    })
  );

  await expect(page.getByText(`${count}`)).toBeVisible();
});
```

같은 폴더 경로에 eslintrc.js 파일도 만들어주면 좋다.

```javascript
module.exports = {
  env: {
    // jest 끔
    jest: false,
  },
  extends: ["plugin:playwright/playwright-test"],
  rules: {
    // 아래 설정은 mocks폴더에서도 하면 좋음
    "import/no-extraneous-dependencies": "off",
  },
};
```

테스트를 실행하는 명령어 ⬇️

```bash
npx playwright test
```

test-results 디렉터리에는 에러가 생겼을 때의 스크린샷이 담긴다. 이 디렉터리도 gitignore 파일에 적어주자.

## CodeceptJs

> node.js 기반의 E2E 테스트 프레임워크이다.

간단한 서비스들은 codeceptjs로도 E2E 테스트가 가능하다. => 다른 도구들보다 훨씬 보기 편하다.(과제에서 codeceptjs로 테스팅을 했었는데 바로 이해가 될 정도로 보기 편했다.)

```javascript
Scenario("check Welcome page on site", ({ I }) => {
  I.amOnPage("/");
  I.see("Welcome");
});
```

이러한 문법으로 바로 알아볼 수가 있어 다른 분야와의 협업에도 매우 좋다.

## 강의 결론 요약

가능하면 로직들을 분리하고 ui는 테스트를 빡빡하게 짜지 말자. ui는 자주 바뀌기 때문

비즈니스 로직들은 테스트하고 ui는 어떻게 누르니까 연결이 된다.. 정도만 테스팅 하기

범용으로 쓰는 파일들은 최대한 영향력이 없게 코드를 짜면 좋다. 100개에 영향을 미치면 100개 테스트가 다 깨지니까.

1. 가능하면 비즈니스 로직은 따로 분리
2. 공용인 애들을 더 빡빡하게 만들기
3. 나머지는 mocking하던지 msw 써서 처리하기
4. 정말 확실하게 돌아가는지 보려면 e2e 테스트 도구 쓰기

사용자 입장에서 어떻게 되는지 볼 수 있는 처리를 꼼꼼히 해야함.(e2e)

실제 개발자가 아닌 프로덕트 오너분이 직접 테스트 코드를 작성했었음. 그러므로 다른 사람이 쓴거를 검증할 수만 있어도 된다. 같이 일하기 위해서 소통하는 용도로 codeceptJS도 좋다.
