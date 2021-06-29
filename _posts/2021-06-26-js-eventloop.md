---
layout: post
title:  "setTimeout과 promise의 관하여 (eventloop)"
date:   2021-06-26 13:00:00 +0900
categories: javascript
---

# setTimeout과 promise의 관하여 (eventloop)

최근 흥미로운 코드를 보고 동작방식이 궁금하여 알아보게 되었습니다.

```js
Promise.resolve().then(() => console.log('a'));
setTimeout(() => console.log('b'), 0)

// a b
```
위 코드는 a, b가 출력이 됩니다.

여기까지는 그럴수 있다고 생각하는데 재밌는건 아래도 동일한 결과가 나왔다는겁니다.
```js
setTimeout(() => console.log('b'), 0)
Promise.resolve().then(() => console.log('a'));

// a b
```
이것에 의문점을 가지고 왜 그런지 자료조사를 하게 되었습니다.

---
## Promise

Promise는 **비동기 함수를 처리하고 완료,실패 처리를 할수있는 객체** 입니다.

그래서 보통 아래와 같이 많이씁니다.

```js
const promise = new Promise((resolve, reject) => {...});
promise.then(() => {...}).catch((e) => {...})
```

여기서 생기는 의문은 Promise가 resolve됬을때 자바스크립트 엔진(Nodejs, WebAPI)들은 어떻게 처리를 하는가 였습니다.

그 처리하는 개념이 **이벤트 루프** 라고합니다.

setTimeout에서 느낀 의문점을 쓰고 이벤트루프에 대해 정리할것입니다.

---
## setTimeout delay값의 의미

setTimeout은 보통 delay초 후에 이 함수를 실행하라는 의미로 해석하지만 정확한 의미는 아래와 같습니다.

**최소 delay초에 메시지큐에 callback 함수를 넣는다.**

메시지큐도 자바스크립트 이벤트루프에 관한 얘기에 나오는데 일단 위와같이 이해하는게 좋을것이라 생각합니다.

저 의미는 최소 delay초 이기 때문에 delay이상이 걸릴수도 있다는 점을 알아둬야 합니다.

보통 하는 몇초뒤에 처리한다는 개념이 아닌 메시지큐에 넣기 떄문에 앞에 메시지가 처리되지 않으면 새로들어온 메시지또한 처리되지 않습니다. (FIFO이기 때문에)

그렇다면 이 코드는 어떻게 동작하는건지 이제 설명할수 있게됩니다.
```js
setTimeout(callback, 0)

// 최소 0초후 callback을 메시지큐에 넣는다.
```

---
## 이벤트루프

메시지큐는 뭐고, Promise에서 처리한다는 그 이벤트루프는 대체 뭘까?

간력하게 설명하면 **이벤트루프는 하나의 개념으로 NodeJS나 브라우저같은 자바스크립트가 구동하는 환경에서 동시성을 처리하는 모델**이다.

이벤트루프는 루프틱이 시작할때마다 JobQueue와 TaskQueue를 체크해서 콜스택에 올려놓고 처리합니다.

여기서 처음 의문이였던 setTimeout과 Promise중 Promise가 왜 먼저실행되는지에 대해 알수 있게됩니다.

이벤트 루프에 관해 아래 링크가 너무나 잘 설명되어 있습니다.
- https://developer.mozilla.org/ko/docs/Web/JavaScript/EventLoop
- https://meetup.toast.com/posts/89

## 돌아와서 setTimeout 과 Promise

> 출처: https://dmitripavlutin.com/javascript-promises-settimeout/
>
> 위 링크를 참고하여 만든 글입니다. 정말 잘 설명된 글이라 한번 꼭 읽어보세요!

이벤트루프는 JobQueue에 있는걸 우선적으로 TaskQueue를 다음에 처리합니다.


```js
setTimeout(() => console.log('b'), 0)
Promise.resolve().then(() => console.log('a'));
```

이 두 코드를 실행했을때 setTimeout의 콜백과 Promise의 콜백은 스케쥴러로 인해 WebAPI에 추가가 됩니다. 

webAPI는 각각의 콜백을 큐에 넣게되는데, 이때 Promise의 콜백은 JobQueue에 넣고, setTimeout의 콜백은 TaskQueue에 넣습니다.

이벤트루프는 아까 얘기했던것처럼 JobQueue를 먼저 처리하고 TaskQueue를 다음에 처리합니다.

그래서 둘이 위치를 바꿔도 Promise 콜백이 먼저 실행이 되고 setTimeout 콜백은 후에 실행이됩니다.

---
간접적으로 이벤트루프가 어떻게 동작하는지 알수 있었습니다.

나중에는 NodeJS에서 사용중인 IO루프(이벤트 루프)인 libuv에 대해 알아보고 싶은데 언제쯤해보련지..

libuv: http://docs.libuv.org/en/v1.x/index.html

