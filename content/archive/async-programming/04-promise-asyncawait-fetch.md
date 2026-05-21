---
title: "Promise 패턴 - Promise, Async/Await, Fetch"
date: "2026-05-21"
category: "async"
---

# Promise

- pending : 처리 대기중
- fulfill : 완료
- reject : 실패함

## async/await

> 결국 blocking 하는 코드이므로 조심히 다루어야 함
> 

async 함수는 무조건 Promise로 감싼 값을 반환한다.

⇒ Promise.resolve(값) 으로 반환된다.

## Q. Promise 객체를 반환하니까 이렇게 바꿔도 되지 않을까?

```jsx
function delay(ms) {
	return new Promise((resolve) => {
		setTimeout(() => {
			resolve(`${ms}`)
		}, ms)
	})
}

async function delay(ms) {
	return setTimeout(() => {
		resolve(`${ms}`)
	}, ms)
}
```

이 코드처럼, return new Promise 를 사용하지 않고 그냥 비동기 함수로 만들어도 똑같이 Promise 객체가 리턴되는 게 아닐까? 라는 궁금증이 생겼다.

결론부터 말하면 이렇게 하면 오류가 발생한다.

1. resolve 라는 함수가 존재하지 않는다.
Promise 내부에서 제공하는 resolve 함수가 존재하지 않기 때문에 에러가 발생한다.
2. setTimeout은 Promise 객체를 리턴하지 않는다.
setTimeout은 Web API이기 때문에 리턴할 때 Promise가 아니라 타이머를 리턴한다.
따라서 아래 코드에서는 타이머 ID를 품은 성공 상태의 Promise를 즉시 리턴해버린다.
(아래 코드에서도 clearTimeout을 사용하기 위해 ID를 가져오는 것처럼 ID 자체를 반환하기 때문에 생각한 대로 코드가 흘러가지 않는다.)
    
    ```jsx
    const setTimeoutID = setTimeout(() => {})
    ```
    
3. setTimeout은 Promise를 지원하지 않는다.
setTimeout은 async/await가 나오기 훨씬 전부터 있던 비동기 방식이기 때문에 둘을 함께 쓰려면 기존 코드처럼 new Promise로 직접 감싸주어야한다.

즉, 이 코드를 제대로 작동하는 async 함수로 만들고 싶다면 반드시 `new Promise` 코드를 사용해 Promise로 만들어야 한다.

## Top-level await

> 자바스크립트에서 async 함수 내부가 아닌 코드 가장 바깥쪽 영역에서도 await 키워드를 사용할 수 있게 해주는 기능
> 

과거에는 즉시 실행 함수로 전체 코드를 감싸서 await를 사용해야 했다.

```jsx
// 과거 방식: 문법 에러 (async 함수 밖이라 await 사용 불가)
const response = await fetch('https://api.example.com/data');
const data = await response.json();

// 억지 해결책: 즉시 실행 함수(IIFE)로 전체 코드를 감싸야 했음
(async () => {
    const response = await fetch('https://api.example.com/data');
    const data = await response.json();
    console.log(data);
})();
```

## Fetch

```jsx
fetch("https://jsonplaceholder.typicode.com/posts/1")
.then(res => {
    return res.json();
}).then(data => {
    console.log(data.title)
})

// await 사용
let res = await fetch("https://jsonplaceholder.typicode.com/posts/1")
let data = await res.json()
console.log('결과', data.title)
```
