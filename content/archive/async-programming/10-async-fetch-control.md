---
title: "비동기 통신 제어"
date: "2026-06-04"
category: "async"
---



# 비동기 통신 제어와 응답 일관성

## 네트워크 요청 횟수 줄이기

전략 1. 입력이 일시적으로 멈출 때만 요청을 보내자.

+) throttling (쓰로틀링)

## 요청 취소

만약 사용자가 특정 요청을 보낸 뒤에 응답을 확인하기 전에 페이지를 벗어난다면?

`AbortController` 라는 기능이 있다.

Fetch 요청을 중간에 중단하는 기능이다.

```jsx
const controller = new AbortController();
const signal = controller.signal;

fetch('https://', { signal })
	.then(response => response.json())
	.then(data => console.log(data))
	.catch(err => {
		if(err.name === 'AbbortError') {
			console.log('요청이 취소되었습니다.')
		} else {
			console.log('다른 에러 발생')
		}
});

// 임의로 요청 취소하기
setTimeout(() => {
	controller.abort();
}, 300);
```
