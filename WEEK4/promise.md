# Promise

## 비동기적이란?

```javascript
console.log(1);
console.log(2);
setTimeout(function () {
  console.log(3);
}, 5000);
console.log(4);
```

위 코드를 실행시키면 1->2->4->3 순으로 출력이 된다. 그 이유는 setTimout 함수가 비동기 함수이므로 5초 후에 setTimout함수의 콜백 함수가 실행되기 때문이다.

이렇게 병렬적으로 실행되는 것을 asynchronous(비동기적)이라고 한다.

비동기적으로 실행하면 동시적인 멀티태스킹이 일어나고 함수들이 각자 자기의 시간표를 가지고 그 시간표에 맞추어 실행을 하게된다.

그러므로 하나의 함수가 끝날때까지 기다렸다가 다음 함수가 실행되는 동기적 방식에 비해 실행 속도가 빠르다는 장점이 있다.

이러한 비동기적 방식은 서버와 통신을 할 때 주로 사용되는데 그 이유는 통신이 언제 끝날지 예측하기 어렵기 때문이다.

비동기적 함수들을 처리하기 위해 도입된 Promise에 대해 알아보자.

## Promise란?

Promise는 표준 빌트인 객체로 ES6에 도입되었다.

Promise 생성자 함수를 new 연산자와 함께 호출하면 프로미스 객체를 생성한다.
프로미스 생성자 함수는 콜백 함수를 인자로 받는다.
그 콜백 함수의 인자는 또 resolve(성공)와 reject(실패) 콜백 함수를 인수로 전달받는다.

프로미스는 비동기 처리 상태(state)와 처리 결과(value)를 관리하는 객체(object)라고 정의할 수 있다.

## Promise 문법

```javascript
// Promise 객체의 생성
const promise = new Promise((resolve, reject) => {
  if (/*비동기 작업 수행 성공 */) {
    resolve('result');
  }
  else { /* 비동기 작업 수행 실패 */
    reject('failure reason');
  }
});
```

## Promise의 3가지 상태

Promise는 이러한 3가지 상태에 따라 애플리케이션 제어를 용이하게 하기 위해 나온 것이다.

- pending(대기) : 처리가 완료되지 않은 대기 상태의 프로미스
- fulfilled(성공) : 성공적으로 처리 완료
- rejected(실패) : 처리가 실패로 끝남
- settled(성공 또는 실패) : 프로미스 객체의 상태가 변화하면 처리된 프로미스라고 부른다.

```javascript
// fulfilled promise(약속이 이행된 프라미스)
let promise = new Promise(function (resolve, reject) {
  // 프라미스가 만들어지면 executor 함수가 자동으로 실행

  // 1초 뒤에 일이 성공적으로 끝났다는 신호가 전달되면서 result는 '완료'가 된다.
  setTimeout(() => resolve("완료"), 1000);
});

// rejected promise(약속한 작업을 거부하는 경우)
let promise = new Promise(function (resolve, reject) {
  // 1초 뒤에 에러와 함께 실행이 종료되었다는 신호를 보낸다.
  setTimeout(() => reject(new Error("에러 발생!")), 1000);
});
```

new 연산자와 함께 호출하여 프로미스 객체를 만드는 순간 pending(대기)상태에 있다가 resolve 함수를 실행하면 fulfilled(이행) 상태가 되고, reject 함수를 실행하면 rejected(거부) 상태가 된다. 어떤 식으로든 상태가 변화하여 처리 완료가 되면 settled 프로미스이다.

## 프로미스의 후속 처리 메서드

### then

resolve 함수를 실행하여 fulfilled 상태가 되면 .then()을 이용하여 처리 결과 값을 받을 수 있다.

```javascript
new Promise((resolve) => resolve("fulfilled")).then(
  (v) => console.log(v),
  (e) => console.error(e)
); // fulfilled
```

```javascript
new Promise((_, reject) => reject(new Error("rejected"))).then(
  (v) => console.log(v),
  (e) => console.error(e)
); // Error: rejected
```

### catch

reject 함수를 실행하여 rejected 상태가 되면 .catch()를 이용하여 실패 이유를 받을 수 있다. 프로미스가 rejected 상태인 경우만 호출된다.

```javascript
new Promise((_, reject) => reject(new Error("rejected"))).catch((e) =>
  console.log(e)
); // Error: rejected
```

## 프로미스 메서드 체이닝

프로미스는 생성자 함수이고 인스턴스를 생성할 수 있다.

프로미스가 인스턴스이므로 메서드 체이닝 방식을 도입하면 선언적일수도 있고 콜백의 깊이가 깊어지는 것을 피할 수 있다.

그리고 프로미스의 then은 항상 프로미스를 반환하기 때문에 반환된 프로미스를 다시 호출할 수가 있는 것이다.

이렇게 여러개의 프로미스를 연결하여 사용하는 것을 프로미스 체이닝이라고 한다.

## async / await

ES8부터는 프로미스를 기반으로 동작하면서 그 전의 비동기 처리 방식보다 간단하고 가독성 좋은 방식인 async/await이 도입되었다.

프로미스의 then, catch, finally 메서드에 콜백 함수를 전달하여 후속 처리할 필요 없이 동기 처리처럼 프로미스를 사용할 수 있다.

- async가 붙은 함수는 프로미스를 반환한다.
- await 키워드를 만나면 프로미스가 처리(settled)될 때까지 기다린다.

프로미스로 구현된 코드와 async로 구현된 코드를 비교해보자.

### Promise 코드

```javascript
function p() {
  return new Promise((resolve, reject) => {
    resolve("hello");
  });
}

p().then((n) => console.log(n));
```

### async 코드

```javascript
async function p2() {
  return "hello2";
}

p2().then((n) => console.log(n));
```

프로미스는 new 연산자로 생성하고 인자로 resolve와 reject 함수를 넘겨주어야 하는 반면, async는 함수 앞에 키워드만 붙여도 프로미스로 인식되고 반환값도 프로미스 함수 안에서 resolve()와 똑같다.

async/await은 콜백함수, 프로미스, 제너레이터 등등 비동기 처리를 하는 방식중에 가장 가독성이 좋은 친구이다. 프로미스의 syntactic sugar라고도 볼 수 있다.

> async/await을 사용하면 await이 대기를 처리해주기 때문에 .then이 거의 필요하지 않습니다. 여기에 더하여 promise.catch 대신 일반 try..catch를 사용할 수 있다는 장점도 생깁니다. 항상 그러한 것은 아니지만, promise.then을 사용하는 것보다 async/await를 사용하는 것이 대개는 더 편리합니다. 그런데 문법 제약 때문에 async함수 바깥의 최상위 레벨 코드에선 await를 사용할 수 없습니다. 그렇기 때문에 관행처럼 .then/catch를 추가해 최종 결과나 처리되지 못한 에러를 다룹니다.

결국 아무리 async/await이 promise의 syntactic sugar라고 해도 프로미스를 사용해야 하는 경우가 있기 때문에 보기 편리하다고 해서 무조건 async/await을 쓰는 것은 아니다.

## await 사용하기

await 키워드는 async 함수 안에서만 사용 가능하다. await 키워드 뒤의 함수가 실행 완료될 때까지 기다려준다.

```javascript
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function getApple() {
  return delay(3000).then(() => "apple");
}

async function getApple() {
  await delay(3000);
  return "apple";
}
```

원래 프로미스 체이닝 기법으로 then을 추가해주어야 했지만 아래처럼 await 키워드를 사용하면 더 가독성 좋은 코드가 된다.

그리고 사실 await이 등장하게된 이유는 가독성 때문만은 아니다.

프로미스 체이닝 기법으로 비동기 함수 처리를 한다고 해도 너무 개수가 많으면 콜백 지옥이 일어날 수 있다.

```javascript
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}
async function getApple() {
  return delay(3000).then(() => "apple");
}
async function getBanana() {
  return delay(3000).then(() => "banana");
}

//콜백 지옥 발생
function printFruits() {
  return getApple().then((apple) => {
    return getBanana().then((banana) => console.log(`${apple} + ${banana}`));
  });
}
```

이러한 문제를 await 키워드를 사용하면 해결된다.

```javascript
async function printFruits() {
  const apple = await getApple();
  const banana = await getBanana();
  return console.log(`${apple} + ${banana}`);
}
```

+) 우선 영상을 많이 보고 문서를 찾아보면서 promise와 async/await 개념은 이해는 되었는데 코드를 직접 짜면서 원리를 파악해야 완벽히 이해했다고 할 수 있을 것 같다. promise 코드를 async/await으로 바꾸는 것이 능숙해질 수 있을때까지 연습하자.
