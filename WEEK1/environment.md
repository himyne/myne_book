# 1. 개발 환경

## 학습 키워드

- Node.js
- NPM(Node Package Manager)
  - package.json / package-lock.json
  - node_modules
  - npx
- ES Modules vs CommonJS

## Node.js(Javascript)]

Javascript는 어렵다.

왜냐하면 계속 바뀌는 Node.js를 기반으로 하기 때문에 앞으로의 흐름과 변화에 잘 대응할 수 있어야 한다. (Deno는 쉽지만 잘 쓰지 않음)

node.js 홀수 버전은 가장 최신이고, 짝수 버전은 안정적인 버전을 뜻한다.

node -v로 현재 버전을 확인하고 주기적으로 LTS 버전으로 업데이트를 할 필요가 있다.

설치 가능한 버전을 확인하는 fnm 명령어를 이용해서 버전을 확인하고 LTS 버전을 설치한다.

### 설치 가능한 버전 확인

```bash
fnm ls-remote
```

### LTS(Long Term Support) 버전을 설치하고 기본으로 사용하게 한다

```bash
fnm install --lts
fnm use lts-latest
fnm default $(fnm current)
```

### 설치된 상태 확인

```bash
fnm list

fnm current
```

## TypeScript + React + Jest + Parcel 개발 환경 세팅

- 적절한 작업 폴더를 만들고 터미널에서 VSCode를 열면 편하다.

```bash
cd study

mkdir 폴더명

cd 폴더명

code .
```

- npm 패키지를 준비하는게 첫 번째 작업이다.

```bash
npm init -y
```

위 명령어를 입력하면 package.json 파일이 만들어지고 기본적인 내용이 들어간다.

-y를 포함하지 않으면 질문들을 많이 하는데 -y를 붙이면 질문 없이 한 번에 패키지가 만들어진다.

- node_modules를 통째로 커밋하는 것을 방지하기 위해 .gitignore 파일을 잊지 않고 작성해야 한다.

```bash
/node_modules/
/dist/
```

gitignore 사이트나 [gitignore 언어별 모음 깃헙 레포](https://github.com/github/gitignore)에서 복사하는 방법도 있다.(강추)

## TypeScript

### 설치 및 실행

npm 명령어로 설치하면 보통 npx로 실행을 한다.

```bash
npm i -D typescript
```

프로그램으로 진짜 쓰는 것이 아니라 개발환경으로 사용되는 것들에는 -D 명령어를 붙인다.(package.json에서 devDependencie)

=> 이렇게 하면 배포하는 크기가 줄어들 수 있다거나 실행될 수 있는 우려를 방지한다는 장점이 있다.

만약 리액트를 프로그램으로 실제로 쓰기 위해서 -D 명령어 없이 설치하면 dependencies로 들어간다.

### 타입스크립트 설정

tsconfig.json 파일에서 jsx 속성을 "react-jsx"로 변경한다.

## ESlint 설치

```bash
npm i -D eslint

npx eslint --init
```

import / export 를 이용해서 개발한다.(bundler 사용 예정)

.eslintrc.js 파일에서 jest: true를 미리 잡아주면 좋다.

.eslintignore 파일도 잊지 않고 만들어준다.

eslint로 파일의 오류를 수정하고 싶으면 `npx eslint --fix` 명령어를 사용하거나 `npm run lint` 명령어를 사용한다.

**뭐든지 설치하는 동안에는 package.json 파일을 건드리면 충돌이 일어날 수 있으니 건드리지 않는다.**

## 리액트 설치

```bash
npm i react react-dom

npm i -D @types/react @types/react-dom
```

## 테스팅 도구 설치

```bash
npm i -D jest @types/jest @swc/core @swc/jest jest-environment-jsdom @testing-library/react @testing-library/jest-dom
```

설치 후에 jest.config.js 파일을 만들어준다.

## Parcel 설치

```bash
npm i -D parcel
```

package.json 파일 script 부분에 아래 내용을 추가한다.

```bash
    "start": "parcel --port 8080",
    "build": "parcel build",
    "check": "tsc --noEmit",
    "lint": "eslint --fix --ext .js,.jsx,.ts,.tsx .",
    "test": "jest",
    "coverage": "jest --coverage --coverage-reporters html",
    "watch:test": "jest --watchAll"
```
