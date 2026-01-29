---
title: "Next.js App router 에서 TanStack Query를 효율적으로 쓸 수 있는 방법에 대해 알아보자"
date: "2024-02-24"
category: "React"
description: "React 19 버전에 등장 예정인 새로운 훅, use 훅에 대해 알아봅시다."
---
<img width="754" height="380" alt="image" src="https://github.com/user-attachments/assets/dde6d198-15a8-48ad-9092-830192d3e08a" />


우리는 보통 `useQuery` 라는 커스텀 훅을 정의해 사용하거나 tanstack Query를 사용해  fetch 요청을 보냅니다.

```jsx
// useQuery
function useQuery({queryFn}) {
	const [data, setData] = useState(null);
	const [error, setError] = useState(null);
	const [loading, setLoading] = useState(true);

	useEffect(() => {
		fetch('/api/data')
			.then(setData)
			.catch(setError)
			.finally(() => setLoading(false));
	}, []);
	
	return {data, error, loading };
}

// tanstack Query
import { useQuery } from '@tanstack/reack-query'

const { data, error, loading } = useQuery({
	queryFn: () => fetch('/api/data'),
})
```

그런데 왜 fetch 요청을 보낼 때 커스텀 훅을 생성해서 사용하거나 tanstack Query같은 도구를 사용하는 것일까요?

비동기로 데이터를 불러오기 위해서는 useEffect 내부에서 요청을 해야 하는데 매번 컴포넌트에 동일한 코드를 작성하지 않기 위해 useQuery 훅을 만들거나 도구를 통해 요청을 해 깔끔한 코드를 작성할 수 있기 때문입니다.

그리고 이러한 과정을 따로 거치지 않게 하기 위해 React에서는 `use` 라는 훅을 제시하고 있습니다.

현재 `use` 훅은 React 공식 문서의 Canary 항목으로 기재되어 있으나 React 19 버전에 공식적으로 제공될 예정이라고 합니다.

## `use`란

컴포넌트 내에서  Promise 또는 Context와 같은 리소스 값을 읽을 때 사용하는 React의 Hook입니다.

여기서 정말 중요한 것은 ‘컴포넌트 내에서’ 라는 것입니다.

왜냐하면 기존에 컴포넌트 내에서 데이터를 비동기적으로 호출하기 위해서는 특정 훅을 사용하거나 추가적인 코드를 작성해야만 했기 때문입니다.

게다가 React 컴포넌트는 동기적으로 렌더링되기 때문에 컴포넌트 자체를 async로 만드는 것은 불가능했습니다.

그런데 use 를 사용해 컴포넌트 자체를 async로 만들지 않고 특정 훅이나 추가적인 코드를 작성하지 않아도 바로 비동기적으로 데이터를 가져올 수 있게 되었다는 뜻입니다.

게다가 다른 React Hook들과는 달리 use는 조건문과 루프 내에서 호출할 수 있기 때문에 조금 더 자유로운 사용이 가능하다는 장점이 존재합니다.

use Hook이 반환하는 값을 확인해보면 한가지 특이한 점을 찾을 수 있습니다.

요청에 대한 결과 데이터만 반환하고 error와 loading에 대해서는 따로 반환하지 않는다는 점입니다.

이때 반환되는 데이터는 Promise 형태이기 때문에 Suspense를 트리거 할 수 있습니다.

에러와 로딩은 제외하고 데이터에만 집중할 수 있다는 것입니다.

따라서 Suspense와 ErrorBoundary로 감싸주면 Promise가 해결될 때까지 Suspense의 fallback이 표시되고 에러가 발생한 경우 ErrorBoundary의 fallback이 표시됩니다.

## 사용 방법

사용 방법은 굉장히 간단합니다.

```jsx
import { use } from 'react';

const value = use(resource);
```

### Promise를 use에 호출하는 경우

```jsx
// App.js
export default function App() {
  const promiseData = fetchPromiseData();
  return (
    <Suspense fallback={<h1>로딩중...</h1>}>
      <Test promiseData={promiseData} />
    </Suspense>
  );
}

// Test.js
import { use } from 'react';

export default function Test({ promiseData }) {
  const data = use(promiseData);
  return <h1>{data}</h1>;
}
```

Promise 형태의 data 값이 완전히 받아와 질 때까지 Suspense의 fallback이 표시됩니다.

### Context API를 호출하는 경우

```jsx

// App.js
const ThemeContext = createContext(null);

export default function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Test />
    </ThemeContext.Provider>
  )
}

// Test.js
export default function Test() {
	const theme = use(ThemeContext);
  return (
    <div>{theme}</div>
  );
}
```

## 주의 사항

- `use` 역시 컴포넌트 또는 Hook 내부에서만 사용이 가능합니다. 조건부로 호출할 수 있지만 React가 렌더링할 때만 작동하는 React의 Hook 이기 때문입니다.
- 여기서 정말 주의해야 할 것은 클로저 함수에서 `use`를 호출하면 안된다는 제약이 존재한다는 것입니다. 따라서 `map`, `filter`, `reduce` 와 같은 메서드 호출에 전달된 클로저 내에서 `use` 를 호출하는 것은 불가능 합니다. 사실 다른 훅들 같은 경우에는 따로 에러가 발생하면서 제대로 동작하지 않지만 `use` 의 경우에는 런타임에 그대로 동작해버리기 때문에 사용할 때 제약을 신경쓰며 굉장히 조심스럽게 사용해야 합니다. (런타임 오류로 처리되지 않고 컴파일러 오류로 처리됩니다.)
- 리엑트 공식 문서에서는 서버 컴포넌트에서 데이터를 가져올 때 `async-await` 사용을 권장하고 있습니다. 즉, 클라이언트 컴포넌트에서만 `use`를 사용하라는 것으로 이해할 수 있습니다.
- `use` 훅은 `try-catch` 문 내에서 호출할 수 없기 때문에 `.then().catch().finally()` 메서드를 사용하거나 ErrorBoundary로 감싸주어야 합니다.

## 마치며
여기까지 React의 `use` 훅에 대해 알아보았습니다.

작년에 개최된 개발자 컨퍼런스인 FEConf 2023의 한 세션에서 소개된 내용인데 굉장히 유용하다는 생각이 들어 추가적인 자료들을 조사하며 글을 작성해 보았는데요.

앞으로 출시될 다양한 기능들에 대해 더 알고 싶으시다면 React Github의 RFC 문서들을 확인해 보시면 좋을 것 같습니다.

### Reference
- [FEConf 2023 - use 훅이 바꿀 리액트 비동기 처리의 미래 맛보기](https://www.youtube.com/watch?v=Hd1JeePasuw)
- [React 공식 문서 - use hook](https://react.dev/reference/react/use#usage)
- [React RFC 문서 - 0000-first-class-support-for-promises](https://github.com/acdlite/rfcs/blob/first-class-promises/text/0000-first-class-support-for-promises.md)
- [React RFC PR - 0000-first-class-support-for-promises ](https://github.com/reactjs/rfcs/pull/229)
- [Youtube - This New React Hook Breaks All The Rules And I Love It](https://www.youtube.com/watch?v=zdNF9FJWJ8o)
- [Youtube - REACT 19 IS AWESOME](https://www.youtube.com/watch?v=Zrsf8_YWcH8)
