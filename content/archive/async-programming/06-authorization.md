---
title: "인증과 비동기 요청"
date: "2026-05-28"
category: "async"
---

# 인증과 비동기요청

## 인증 과정

![image.png](attachment:9ed29e68-23ce-4dda-b60b-8db3e5602e05:image.png)

## 세션과 쿠키

같은 도메인으로 요청을 보내면 쿠키값이 자동으로 포함되어 날아간다. 

## 로컬스토리지

`headers` 부분의 `Authorization` 속성에 추가

```jsx
const token = localStorage.getItem("token");

fetch('/', {
	method: "GET",
	headers: {
		Authorization: `Bearer ${token}`
	}
});

const getToken = async () => {
	const res = await fetch('/get-fetch', {
		method: "GET",
	});
	const data = await res.json();
	localStorage.setItem('token', data.token)
}

```

- 세션/쿠키 → 멀티 페이지
- 토큰 → 싱글 페이지
