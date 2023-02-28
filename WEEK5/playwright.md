# Playwright

- [ ] E2E(End to End) Test
- [ ] Headless Chrome
- [ ] Puppeteer
- [ ] Playwright
- [ ] CodeceptJS

웹 브라우저 기반 E2E 테스트 자동화 도구

HEADLESS 브라우저(화면에 안뜨는거 - 뒤에서만 도는거)를 기반으로 한 것이다.

Headless Chrome을 기반으로 Puppeteer라는 도구를 만들었었다. 구글에서 ms로 퍼피티어 개발자들이 가면서 만들었음. Playwright는 크롬, 사파리, 익스플로러 등 다 지원한다.

얘는 테스트 도구는 아니고 api만 제공한다. 테스트 러너까지 제공해서 굉장히 깔끔하게 쓸 수 있다.

크롤링할 때 쓰기도 한다. 그런데 우리는 테스트를 할거니까 개발환경으로 설치한다.eslint 플러그인도 같이 설치한다.

```bash
npm i -D @playwright/test eslint-plugin-playwright
```

express 서버도 돌려야한다. 진짜 테스트니까

그리고 config 파일을 만들어야 한다.

```javascript
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

CI-true npx playwright test => 명령어를 입력하면 headless로 실행가능하다. => 브라우저가 안뜨니까 훨씬 빨리된다.

간단한 서비스들은 codeceptjs로도 가능하다. => 훨씬 보기 편함(과제에서 codeceptjs로 테스팅을 했었는데 진짜 바로 이해가 될 정도로 보기 편했다.)

가능하면 로직들을 분리하고 ui는 테스트를 빡빡하게 짜지 말자. ui는 자주 바뀌기 때문

비즈니스 로직들은 테스트하고 ui는 어떻게 누르니까 연결이 된다.. 정도만 테스팅 하기

범용으로 쓰는 파일들은 최대한 영향력이 없게 코드를 짜면 좋다.

100개에 영향을 미치면 100개 테스트가 다 깨지니까.

1. 가능하면 비즈니스 로직은 따로 분리
2. 공용인 애들을 더 빡빡하게 만들기
3. 나머지는 mocking하던지 msw 써서 처리하기
4. 정말 확실하게 돌아가는지 보려면 e2e 테스트 도구 쓰기

사용자 입장에서 어떻게 되는지 볼 수 있는 처리를 꼼꼼히 해야함.(e2e)

실제 개발자가 아닌 프로덕트 오너분이 직접 테스트 코드를 작성했었음. 그러므로 다른 사람이 쓴거를 검증할 수만 있어도 된다. 같이 일하기 위해서 소통하는 용도로 codeceptJS도 좋다.
