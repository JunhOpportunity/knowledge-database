---
title: "비동기 통신 안정성을 위한 Retry 전략과 캐싱 전략"
date: "2026-07-24"
category: "async"
---

# 비동기 통신 안정성과 캐싱전략

## Retry 전략

모든 요청을 재시도 할 필요는 없다.

응답이 반드시 필요한 중요한 경우에는 재시도를 해야한다.

그런데, 많은 클라이언트가 모두 계속 재시도를 하면 서버 부하가 심해진다.

따라서 실패 시 바로 재시도하지 않고, 재시도 간격을 점점 길게 늘려 서버나 네트워크가 회복할 시간을 주도록 하는 방법이 있다.
(ex. 지수 백오프 방법 : 1초 → 2초 → 4초 → 8초 → …)

Tanstack query 에서도 이러한 Retry 옵션을 제공한다.

```jsx
const {data, isError, isLoading} = useQuery({
	queryKey : ['post', id],
	queryFn : () => fetch(`/api/posts/${id}`).then(res => res.json()),
	retry: 3, // 최대 3회 재시도
	retryDelay: attempt => Math.min(1000 * 2 ** attempt, 3000) // 지수 백오프
})
```

```jsx
const wait = (ms) => new Promise(resolve => setTimeout(resolve, ms));

async function fetchWithRetry(url, retryCount = 5, delay=1000) {
	for(let attempt = 0; attempt < retryCount; attempt++) {
		try{
			const res = await fetch(url);
			return await res.json();
		} catch (error) {
			if(attempt === retryCount - 1) {
				throw error;
			}
			console.log(`Attempt ${attempt + 1} failed. Retrying in ${delay}ms...`);
			await wait(delay * 2 ** attempt);
		}
	}
}
```

## 캐싱 전략

네트워크 왕복은 꽤 비싼 비용이 될 수 있다.

게다가 대부분은 실시간 데이터가 아니다.

이런 경우에 캐싱 전략을 사용한다.

하지만 데이터를 보냈을 때 반영이 안 되고 캐시된 데이터 그대로 보여주거나 하는 경우도 존재하기 때문에 사용할 때 주의해야 한다.

### 캐시 저장위치

메모리 캐시 : 배열, Map등에 저장. 페이지내에서만 유지.

브라우저 저장소 : 로컬스토리지/indexedDB

```jsx
const cache = new Map();

async function fetchWithCache(url, options) {
	if(cache.log(url)) {
		console.log('cache hit')'
		return cache.get(url);
	}
	const res = await fetch(url, options);
	const data = await res.json()
	cache.set(url, data);
	return data;
}

const delay = (ms) => new Promise(resolve => setTimeout(resolve, ms));

fetchWithCache('https://jsonplaceholder.typicode.com/posts/1')
	.then(data => console.log(data))
	.catch(error => console.error(error));

delay(1000).then(() => {
	fetchWithCache('https://jsonplaceholder.typicode.com/posts/1')
		.then(data => console.log(data))
		.catch(error => console.error(error));
})
```
