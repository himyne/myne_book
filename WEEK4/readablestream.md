# ReadableStream

Streams API의 인터페이스로, 바이트 데이터를 읽을 수 있는 스트림을 제공한다.

네트워크로부터 받은 데이터 스트림을 Chunk 단위로 읽고 다루는 것은 매우 유용하다.

## Chunk 단위로 데이터를 읽는다는 것

Chunk 단위로 데이터를 읽는다는 것은 전체 데이터를 한 번에 모두 읽어들이지 않고, 작은 단위로 나누어서 읽는 것을 뜻한다.

보통 데이터 스트림은 매우 큰 파일이나 네트워크 연결에서 발생하기 때문에 모든 데이터를 한 번에 처리할 수 없는 경우가 많다.

이때 chunk 단위로 데이터 스트림을 읽으면 필요한 부분만 처리할 수 있다.
전체 데이터를 한번에 메모리에 올리지 않아도 되기 때문에 메모리도 효율적으로 사용할 수 있다.

또한 데이터 스트림이 비동기적으로 전달되는 경우에, chunk 단위로 읽으면 전체 데이터가 모두 도착하기를 기다리지 않고, 일부 데이터가 도착한 시점부터 처리를 할 수 있게 한다.

{% hint style="info" %}

### 스트림이란?

데이터 컬렉션의 반복을 멋지게 처리하는 기능..(?)이다.
{% endhint %}

## 강의 실습 코드

```javascript
fetch("http://localhost:3000/products");
// → Promise

await fetch("http://localhost:3000/products");
// → Response

const response = await fetch("http://localhost:3000/products");
// → response.body는 ReadableStream

const reader = response.body.getReader();
//ReadableStream 형태의 body를 읽어들임

const chunk = await reader.read();
// → chunk.value는 Uint8Array 타입.
// → 원래는 chunk.done이 true일 때까지 반복해야 한다.

const body = new TextDecoder().decode(chunk.value);

const data = JSON.parse(body);
```

이 코드에서 response에는 fetch 메서드에서 반환된 응답 객체가 들어있다.

그런데 이 응답 객체에는 ReadableStream의 형태로 body 속성이 존재한다.

![body 속성](/images/body-property.jpg)

body 안에 있는 정보는 ReadableStream 형태이기 때문에 getReader()메서드를 사용해서 데이터를 읽어들여야 한다.

getReader()메서드로 읽어들인 값은 ReadableStreamDefaultReader 객체로 reader변수에 저장된다.

ReadableStreamDefaultReader객체에는 두가지 속성이 있다.

- value: 읽어온 데이터
- done: 더 이상 읽어올 데이터가 없는지 여부를 나타내는 값

done이 true가 될때까지 데이터 읽어들이는 것을 반복한다.

그러고 나서 reader.read()를 사용하여 데이터 스트림을 chunck 단위로 읽어오는 것이다.
