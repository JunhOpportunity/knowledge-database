---
title: "비동기 에러 핸들링"
date: "2026-06-03"
category: "async"
---

# 비동기 에러 핸들링

## try - catch 문에서 비동기 에러가 잡힐까?

try - catch 의 경우에는 동기 코드만 감지하기 때문에 비동기 상황에서 발생한 에러를 탐지할 수 없다.

```jsx
try {
	setTimeout(() => {
		throw new Error('비동기 내부에서 발생한 오류');
	}, 100)
} catch (err) {
	console.error(err.message);
}
```

따라서 다음과 같은 코드에서는 catch 문에서 에러가 잡히지 않게 된다.

따라서 `Promise` 를 반환하도록 해서 비동기적으로 처리해야 한다.

```jsx
async funciton foo() {
	try {
		await new Promise((_, reject) => {
			setTimeout(() => {
				reject(new Error('error'))
			}, 1000);
		});
	} catch(error) {
		console.log('에러 발생', error)
	}
}
```

## fetch 비동기 에러 핸들링 처리

fetch 함수 자체가 통신 과정에서 에러가 생겼을 때 reject 되고 그 에러를 catch 처리한다.

```jsx
async function fetchData(url) {
	try {
		const response = await fetch(url);
		if(!response.ok) {
			throw new Error(`HTTP 오류! ${response.status}`);
		}
		const data = await response.json();
	} catch(error) {
		console.error(error.message);
	}
}

// 함수화
async function a() {
	const res = await fetch('https://');
	if(!res.ok) throw new Error(res.status);
	return await res.text();
}

async function b() {
	try {
		const result = await a();
	} catch (error) {
		console.error('에러 발생 : ', error)
	}
}
```

위 코드의 함수화 부분처럼 a와 b 양쪽에서 에러를 처리하는 게 좋다.
