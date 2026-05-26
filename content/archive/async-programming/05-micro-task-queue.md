---
title: "Micro Task Queue와 Macro Task Queue"
date: "2026-05-26"
category: "async"
---

# 마이크로 태스크 큐

```jsx
// A
console.log('start')

// B
setTimeout(() => {
	console.log('setTimeout')
})

// C
Promise.resolve().then(() => {
	console.log('promise.then')
})

// D
console.log('end')
```

다음 코드는 어떤 순서로 출력될까?

A → D → B → C 순으로 생각할 수 있다.

왜냐하면 큐는 보통 먼저 들어온 순서대로 수행되기 때문이다.

하지만, 마이크로 태스크 큐 때문에 Promise의 콜백 함수가 setTimeout보다 먼저 출력된다.

지금까지 알고있었던 태스크 큐는 `매크로 태스크 큐` 이다.

- Macro Task Queue : setTimeout, setInterval, DOM Event
- Micro Task Queue : Promise.then()

Micro Task Queue는 항상 연속으로 실행되어야 한다.

즉, Micro Task Queue에서 콜백이 실행대기중이라면 Macro Task는 실행되지 못하고 계속 대기해야 한다.

## Micro Task의 중단

```jsx
Promise.resolve().then(async() => {
	console.log('promise.then');
	await delay(2000) // 여기서 중단됨
}).then(() => {
	console.log('promise.then.then')
})

setTimeout(() => {
	console.log('setTimeout')
})
```

위에서는 delay 부분에서 Micro Task가 중단되고, setTimeout이 실행된다.

즉, `await` 동안 Micro Task Queue가 비어있게되고 이때 setTimeout이 실행되는 것이다.
