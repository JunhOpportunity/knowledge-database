---
title: "에러 핸들링"
date: "2026-06-02"
category: "async"
---

# 에러 핸들링

- try - catch 문에서 에러가 발생하면 catch 로 간다
- `throw new Error();` 를 사용해 에러를 던질 수 있다

## 에러 처리가 필요한 부분

1. 네트워크 통신
2. 사용자 입력
3. 파일 접근
4. 외부 라이브러리 사용
5. DOM 조작
6. 비동기처리

## Fetch 통신 에러 핸들링

> then과 catch
> 

```jsx
fetch('https://')
	.then(res => {
		if(!res.ok) {
			throw new Error(`http 오류. 상태코드 : ${res.status}`)
		}
	})
	.catch(error => console.error('에러 발생 : ', error.message))
```
