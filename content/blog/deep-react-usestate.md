---
title: "useState는 사실 Linked List 구조였다?"
date: "2025-09-16"
category: "React"
series: "deep-react"
description: "useState의 비밀을 파헤치기 위해 코드를 뜯어보자."
---

<img width="1124" height="620" alt="image" src="https://github.com/user-attachments/assets/cc62d3a4-0be5-4d28-9035-7f9d0d17d992" />


# useState와 Linked List

useState 를 호출하면 Hook 객체를 생성하는 것이다.

여기서 queue 객체, udate 객체 등이 존재한다.

여기서 보면 next 가 존재하는데, 이것을 보고 Linked List 자료구조라는 것을 깨달을 수 있다.

이를 통해 hook 이라는 노드들이 Linked List 형태로 연결되어있다고 유추할 수 있다.

하나의 hook 객체 안에는 하나의 queue 객체를 갖고 있다.

```jsx
function mountWorkInProgressHook(): Hook {
	const hook: Hook = {
		memoizedState: null,
		
		baseState: null,
		queue: null.
		baseUpdate: null,
		
		next: null,
	}
	
	if (workInProgressHook === null) {
		// 아무것도 작업중인 훅이 없다면
		firstWorkInProgressHook = workInProgressHook = hook;
	} else {
		// Linked List로 구현되어있기 때문에 다음 훅을 연결할 때 현재훅.next 를 사용해 할당한다.
		workInProgressHook = workInProgressHook.next = hook;
	}
	return workInProgressHook;
}
```

큐 안에는 업데이트 객체가 들어있는데, 상태를 업데이트 하기 위해 갖고있는 자바스크립트 객체를 의미한다.

큐 객체 내부에 last는 마지막에 담겨있는 update 객체를 말한다.

dispatch 함수는 dispatchAction이 bind 되어있다.

왜냐하면 dispatch 는 외부로 노출되기 때문이다. (여기서 dispatch 함수가 결국 setState 함수인 것이다.)

```jsx
const queue = (hook.queue = {
	last: null,
	dispatch: null,
	// ...
})
```

[[React 까보기 시리즈] useState 의 state 는 어떤 코드로 쓰여있나?!](https://www.youtube.com/watch?v=7mU7ARgrpfI&list=PLpq56DBY9U2B6gAZIbiIami_cLBhpHYCA&index=6)
