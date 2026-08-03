---
title: "NextJS에서 Tanstack query provider 설정 방법"
date: "2026-08-03"
category: "youreca"
---


# NextJS에서 Tanstack query

React에서는 Tanstack Query Provider를 최상단 main 파일에 사용하면 되는 구조였다.

하지만 Next에서는 어떨까?

Next의 App Router 방식은 `layout.tsx` 에서 Provider를 감싸야한다.

### 왜 page.tsx가 아닌 layout.tsx 인가?

NextJS는 각 폴더의 page.tsx 파일이 해당 페이지가 된다.

따라서, page.tsx에 Provider 설정을 하면 해당 페이지에서는 사용이 가능하지만 다른 페이지로 이동할 경우 Provider가 다시 생성된다.

반면에 layout.tsx는 하위 모든 페이지에 적용되기 때문에 최상위에 있는 layout.tsx에서 Provider를 감싸야 하는 것이다.

### 근데 Tanstack Query는 Client Component에서 사용 가능한 거 아닌가?

Tankstack Query Provider를 감싸기 위해서는 Client Component에서 사용이 가능하다.

그런데 layout.tsx는 Server Component 이다.

그럼 layout.tsx를 클라이언트 컴포넌트로 바꾸어야 하는 걸까?

아니다. 그렇게 되면 이제 모든 페이지가 클라이언트 컴포넌트가 되기 때문에 NextJS를 사용하는 의미가 없어지게 된다.

클라이언트 컴포넌트의 하위 컴포넌트들은 모두 클라이언트 컴포넌트가 되기 때문이다.

따라서 다음과 같은 방식으로 이를 해결할 수 있다.

### 클라이언트 컴포넌트 분리하기

1. Tanstack Query Provider를 작성하기 위한 파일 생성

렌더링 될 때마다 QueryClient가 새로 생성되는 것을 방지하기 위해 useState를 사용해 queryClient를 한 번만 생성한다.

```jsx
// providers/QueryProvider.tsx

"use client" // 클라이언트 컴포넌트 선언

export default function Providers({children} : Props) {
  const [queryClient] = useState(() => new QueryClient());

  return(
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  )
}
```

1. layout.tsx 수정

```jsx
export default function Providers({children} : Props) {
  const [queryClient] = useState(() => new QueryClient());

  return(
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  )
}
```
