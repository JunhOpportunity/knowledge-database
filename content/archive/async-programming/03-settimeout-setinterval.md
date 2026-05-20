---
title: "비동기 흐름 제어 - setTimeout & setInterval"
date: "2026-05-20"
category: "async"
---

## 비동기 흐름 제어

### setTimeout / clearTimeout

> 몇 초 뒤에 실행
> 

이 함수도 반환값이 존재한다.

따라서 반환 값을 사용해서 실행되기 전에 취소할 수 있다.

```jsx
setTimeout(() => {
	console.log("1초뒤 실행")
}, 1000)

// 중간에 취소

let timeoutId = setTimeout(() => {
	console.log("1초뒤 실행")
}, 1000)

clearTimeout(timeoutId); // 취소됨.
```

### setInterval / clearInterval

> 몇 초 마다 반복해서 실행
> 

```jsx
const intervalId = setInterval(() => {
	count++;
	console.log(`현재 카운트: ${count}`)
}, 1000);

clearInterval(intervalId);
```
