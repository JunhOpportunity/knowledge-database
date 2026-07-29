---
title: "React Hook 정리 (useState, useEffect, useRef, useCallback, useMemo)"
date: "2026-07-28"
category: "react"
---

# React Hook

## useState

## useEffect

## useRef

useState와 useRef는 비슷한 역할을 한다.

- useState : 값이 변경되면 컴포넌트를 다시 렌더링해야할 때 사용
- useRef : 값을 저장하지만 렌더링을 발생시키고 싶지 않을 때 사용

```jsx
const [count, setCount] = useState(0);

const increase = () => {
  setCount(count + 1);
};

// ------------------------------------------

const count = useRef(0);

const increase = () => {
  count.current += 1;
};
```

useState 같은 경우 다음 순서로 동작하게 된다.
count 변경 → React에게 알려줌 → 컴포넌트 재렌더링 → 화면 업데이트

useRef 같은 경우 다음 순서로 동작하게 된다.
count 변경 → 값만 변경 → React는 모름 → 재렌더링 발생 X

### useRef의 구조

useRef는 객체를 반환한다.

```jsx
const ref = useRef(값);

// 결과
{
  current: 값
}

ref.current
```

### 사용처 1. DOM을 직접 조작하는 경우

React에서 DOM을 직접 조작하는 경우에 많이 사용한다.

예를 들어, 버튼을 클릭하면 input에 자동 포커스 되는 기능을 구현한다고 해보자.

이때, 기본 지식만을 활용하면 querySelector 를 사용해서 포커스하도록 구현할 것이다.

```jsx
document.querySelector("input").focus();
```

하지만 React에서는 querySelector를 사용한 적이 거의 없지 않은가?

따라서 이때 활용하는게 바로 useRef이다.

```jsx
import { useRef } from "react";

function Login() {
  const inputRef = useRef<HTMLInputElement>(null);

  const focusInput = () => {
    inputRef.current?.focus();
  };

  return (
    <>
      <input ref={inputRef} />

      <button onClick={focusInput}>
        입력하기
      </button>
    </>
  );
}
```

### 사용처 2. 이전 값을 기억해야 하는 경우

예를 들어, 이전 props 값을 알고 싶다고 한다면 이런 식으로 이전 값을 저장할 수 있다.

```jsx
function User({name}: {name:string}) {

  const prevName = useRef("");

  useEffect(() => {
    prevName.current = name;
  }, [name]);

  return (
    <>
      현재 이름: {name}
      <br/>
      이전 이름: {prevName.current}
    </>
  );
}
```

### 쉽게 이해하기

> useRef는 React에서 렌더링과 관계없이 값을 유지하기 위한 Hook이다. useState와 달리 값이 변경되어도 리렌더링을 발생시키지 않으며, 주로 DOM 접근이나 이전 값 저장, 타이머 ID 관리처럼 컴포넌트 생명주기 동안 유지해야 하지만 화면 업데이트가 필요하지 않은 값을 저장할 때 사용한다.
> 
- 화면에 보여줘야 하는 값 : useState
    - 좋아요 개수
    - 검색어
    - 입력값
    - 로그인 상태
- 화면에는 필요 없지만 기억해야 하는 값 : useRef
    - DOM 요소
    - 이전 값
    - 스크롤 위치
    - 렌더링 횟수

즉, useRef는 재렌더링이 발생해도 값을 유지한다. 하지만 재렌더링을 유발하진 않는다.

## useMemo

메모이제이션을 사용하면 이전에 계산한 결과를 재사용해서 불필요한 계산을 줄일 수 있다.

### 사용처 1. 계산 비용이 큰 경우

filter, sort, 복잡한 계산

### 사용처 2. 객체 참조 유지가 필요한 경우

```jsx
const options = useMemo(()=>({
 theme:"dark"
}),[]);
```

## useCallback

React에서 함수도 매 렌더링마다 새로 생성된다.

이때 useCallback 을 사용해서 함수를 만들면, 첫 렌더링 이후에 기존 함수를 재사용한다.

```jsx
const handleClick = useCallback(() => {
  console.log("click");
}, []);
```

### 사용처 1. 자식 컴포넌트에 함수 전달

```jsx
<Child onClick={handleClick}/>
```

### 사용처 2. Custom Hook 내부

```jsx
const fetchUser = useCallback(()=>{
 ...
},[]);
```

### 쉽게 이해하기

> 메모이제이션은 이전 계산 결과를 저장하고 동일한 입력에 대해 재사용하는 최적화 기법이다. React에서는 useMemo를 통해 계산된 값을, useCallback을 통해 함수를 메모이제이션할 수 있다. 다만 모든 곳에 적용하는 것이 아니라 계산 비용이나 렌더링 비용이 큰 경우 선택적으로 적용해야 한다.
> 

### Youreca 프로젝트에 적용한다면?

1. 평판 목록 필터링 - useMemo 사용

매번 서버 데이터를 가져와서 해당 데이터를 필터링 한 뒤에 내보내지 않고 useMemo를 사용해서 비용을 아낄 수 있다.

```jsx
// 기존에 재랜더링 될 때마다 생성되는 함수
const filteredReviews = reviews.filter(
  (review) => review.category === category
);

// useMemo를 사용해 개선
const filteredReviews = useMemo(() => {
  return reviews.filter(
    (review) => review.category === category
  );
}, [reviews, category]);
```

1. 자식 컴포넌트에게 함수를 props로 넘기는 경우 - useCallback 사용

부모가 렌더링될 때마다 deleteReview 함수가 생성되므로, 이 함수를 사용중인 ReviewItem도 계속 다시 렌더링된다.

따라서 이를 해결하기 위해 deleteReview 함수를 useCallback을 사용해 만들면 리렌더링 되는 경우에도 기존 함수를 재사용하게 된다.

```jsx
// 기존 구조
function ReviewList() {
  const deleteReview = (id:number)=>{
    ...
  }
  return (
    <ReviewItem
      onDelete={deleteReview}
    />
  )
}

// useCallback을 사용해 개선
const deleteReview = useCallback((id:number)=>{
},[]);
```
