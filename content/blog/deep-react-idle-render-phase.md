---
title: "setState를 구성하는 IDLE과 Render Phase에 대해 알아보자"
date: "2025-09-24"
category: "React"
series: "deep-react"
description: "IDLE과 Render Phase란 무엇이고 어디에 쓰이는지에 대해 깊이 탐구해보자."
---


<img width="1124" height="623" alt="image" src="https://github.com/user-attachments/assets/b29ebcbd-0577-47dc-947d-43d4b73b4f4f" />


# setState를 호출하는 두 가지 상황, idle과 render phase

복습) idle이란? 어떠한 업데이트도 없는 상태

redner phase는 update된 내용을 적용하기 위해 Work를 scheduler에 예약해두었을 때를 말한다.

좀 더 쉽게 말하면, 컴포넌트의 함수나 렌더 함수가 실행되는 순간을 말한다.

이때 렌더 함수는 사이드 이팩트가 없는 순수한 연산만을 해야하는데 중간에 setState를 호출하면 렌더 중에 상태를 변경하는 상황이 발생한다.

이것을 redner phase 라고 한다.

```jsx
function IdleExample() {
	const [count, setCount] = useState(0)
	if(count === 1) setCount(prev => prev + 1)
	return <button onClick={() => setCount(prev => prev + 1)}> +1 </button>
}
```

이 코드에서 보면 초기 렌더링시에는 if 문이 false 이기때문에 그대로 렌더링이 종료된다.

하지만 이후에 버튼을 눌러 re-render이 발생하면 render 중간에 if 문이 true가 되므로 setState가 호출되어 렌더가 끝나기 전에 재랜더가 발생한다.

이런 경우 조건이 잘못되면 무한 렌더가 발생할 수 있다.

따라서 위 코드를 올바른 방식으로 바꾸면 이렇게 된다.

```jsx
function IdleExample() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <div>count: {count}</div>
      <button onClick={() => setCount(c => c + 1)}>+1</button>
    </div>
  );
}
```

하지만 만약 렌더 중간에 상태를 바꿔야 하는 경우라면 어떻게 해야할까?

useEffcet 내부로 조건식을 옮기면 된다.

```jsx
function SafeExample() {
  const [a, setA] = useState(0);

  useEffect(() => {
    if (a === 1) setA(prev => prev + 1);
  }, [a]);

  return <button onClick={() => setA(prev => prev + 1)}>+1</button>;
}
```

여기서 아주 중요한 내용을 깨달았는데 그것은 이럴때 발생하는 에러가 바로 `Too many re-renders` 라는 에러라는 것이다.

따라서 `Too many re-renders` 에러를 예방하기 위해서는 렌더 과정은 순수함수처럼 사이드 이팩트를 유발하지 않도록 작성해야 한다는 것을 깨달았다.

굉장히 쉽게 풀어쓰면 다음과 같다.

> 컴포넌트 본문 실행 시점에서는 setState를 사용하면 안되고 이벤트 핸들러나 effect 처럼 나중에 실행되는 함수 안에 setState를 사용하라.
> 

```jsx
export default function Main() {
  const [count, setCount] = useState(0);

  // ⛔ 여기에서 바로 setCount(...) 하면 안 됨
  setCount(prev => prev + 1);

	// ✅ 이벤트 핸들러 안에서는 사용 가능
  const handleClick = () => {
    if (count < 5) setCount(count + 1);
  };

  return (
    <button onClick={handleClick}>+1</button>
  );
}

```

[[React 까보기 시리즈] setState 를 호출하는 2가지 상황, idle 과 render phase 를 구분하는 조건문?!](https://www.youtube.com/watch?v=qz4NG8zrgjo&list=PLpq56DBY9U2B6gAZIbiIami_cLBhpHYCA&index=8)
