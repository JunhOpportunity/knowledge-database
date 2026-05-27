---
title: "Fetch API"
date: "2026-05-27"
category: "async"
---

# Fetch API

## Fetch 함수 설명

```jsx
fetch(url, {
	method: 'GET',
	headers: {
		'Content-Type': 'application/json',
		Authorization: 'Bearer 토큰'
	},
	body: JSON.stringfy(data),
	credentials: 'include',
	signal: controller.signal
})
```

- 첫 번째 파라미터 : URL
- 두 번째 파라미터 :
    - Method
        - GET : 조회
        - POST : 생성
        - PUT : 전체 교체
        - PATCH : 부분 수정
        - DELETE : 데이터 삭제
    - headers
        - Content-type : 데이터 형식
        - Authorization : 토큰정보 전달
        - X-Custom-Header : 사용자 내부 규약용

## 응답 처리

```jsx
fetch(url)
	.then(res => res.json()) // promise 객체
	.then(data => {
		console.log(data)
	})
```

응답 body는 바로 읽을 수 있는 텍스트가 아니다.

stream 으로 들어오는 데이터이기 때문에 이 데이터를 끝까지 읽은 뒤에, JSON 파싱을 통해 자바스크립트에서 다룰 수 있는 객체 형태로 변환된다.

## Promise 병렬 처리

여러 개의 Fetch 요청을 병렬적으로 처리하는 것도 가능하다.

```jsx
const aPromise = fetch(url).then()
const bPromise = fetch(url).then()
```

이런 식으로도 가능한데, 실행 순서는 보장되지 않기 때문에 주의해야 한다.

그런데 만약, 둘 다 도착한 뒤에 무언가를 처리하고 싶다면?

즉, 두 요청은 서로 독립적이지만 두 개의 응답이 모두 필요하다면?

이때 사용하는 게 `Promise.all` 이다.

```jsx
Promise.all([
	fetch('/a').then()
	fetch('/b').then()
])
.then(([dataA, dataB]) => {
	a(dataA);
	b(dataB);
})
```

|  | 정상 상황 | 오류 상황 |
| --- | --- | --- |
| Promise.race() | 제일 먼저 끝난 것 사용. | 가장 먼저 끝난 것이 실패라면 즉시 reject |
| Promise.any() | 제일 먼저 끝난 것 사용. | 모두 실패해야 reject |

## 파일 업로드와 멀티 파트

binary 파일을 전송할 때는 constent-type을 `multipart/from-data` 를 사용하자.

```jsx
const fileInput = document.getElementById('imageInput');
const resultDiv = document.getElementById('result');

async function uploadImage() {
	const file = fileInput.files[0];
	const formData = new FormData();
	formData.append('image', file);
	
	try {
		const response = await fetch('/upload', {
			method: 'POST',
			body: formData
		})
		
		const imageData = await response.json();
	}
}
```
