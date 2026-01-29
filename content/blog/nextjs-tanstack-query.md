---
title: "Next.js App router 에서 TanStack Query를 효율적으로 쓸 수 있는 방법에 대해 알아보자"
date: "2025-07-16"
category: "상태관리"
description: "클라이언트 컴포넌트 내부에 서버 컴포넌트를 써야 하는 상황이 발생했다. 어떻게 해결해야 할까..?"
---

<img width="1023" height="569" alt="image" src="https://github.com/user-attachments/assets/5ce83b05-1a20-4ded-90f6-5abc50dd4b1e" />


# Next.js App router 에서 TanStack Query를 효율적으로 쓸 수 있는 방법에 대해 알아보자
## 1. `TanStack Query + App Router` 사용 시 직면하는 문제

Tanstack Query를 사용하기 위해 최상위에 layout에서  QueryClientProvider로 래핑하면 다음 문제가 발생합니다.

- `QueryClientProvider`는 `useState` 등 클라이언트 훅을 사용하므로 `use client`를 선언해야 함
- `layout.tsx` 전체가 클라이언트 컴포넌트로 변환됨
- 결과적으로 layout이 클라이언트 컴포넌트가 되면 그 안에 위치한 모든 컴포넌트도 클라이언트 컴포넌트가 되어버림
- 서버 컴포넌트의 장점을 잃어버림

## 2. 왜 클라이언트 컴포넌트를 최소화해야 할까?

- JS 번들 크기 증가
- 하이드레이션 비용 증가
- SEO/초기 로딩 속도 저하

클라이언트 컴포넌트에서 서버 컴포넌트를 사용할 경우 렌더링 과정에서 클라이언트 컴포넌트는 서버와 브라우저에서 모두 실행되지만 서버 컴포넌트는 서버에서만 실행되므로 이런 경우에 발생할 수 있는 문제를 방지하기 위해 서버 컴포넌트를 클라이언트 컴포넌트로 바꾸기 때문입니다.

NextJS 공식 문서에서 꼭 필요한 경우에만 클라이언트 컴포넌트를 사용하고 그 외의 경우에는 대부분 서버 컴포넌트를 사용하라고 권장하고 있습니다.

그렇다면 왜 클라이언트 컴포넌트의 사용을 최소화 하는 것을 권장하는 걸까요?

클라이언트 컴포넌트의 개수가 많아지면 많아질수록 브라우저에게 전달되는 자바스크립트 번들의 용량이 커지기 때문입니다.
이렇게 되면 클라이언트에서 실행할 자바스크립트가 많아지고 초기 렌더링과 하이드레이션 시간이 길어집니다.

결국 사용자가 콘텐츠를 보기까지의 시간이 느려지고 퍼포먼스에도 부정적인 영향을 미치게 됩니다.

## 3. 해결방안: `QueryProvider`를 클라이언트 컴포넌트로 따로 분리하기

지금 상황 같은 경우에는 결국 클라이언트 컴포넌트 안에 서버 컴포넌트가 들어가야 하기 때문에 이런 특수한 경우에는 서버 컴포넌트를 클라이언트 컴포넌트에서 `children props` 로 받아서 렌더링 시켜주면 해결됩니다.

### 3-1. React Query 관련 로직만 클라이언트에서 실행되도록 따로 분리하기

```tsx
// 클라이언트 컴포넌트 QueryProvider 만들기
"use client"

export default function QueryProvider({ children }: { children: ReactNode }) {
  const [queryClient] = useState(() => new QueryClient())

  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  )
}

```

### 3-2. layout.tsx는 서버 컴포넌트로 유지하기

```tsx
// layout.tsx
    <html lang="en">
      <body>
        <QueryProvider>
          <Header />
          {children}
          <ToastProvider />
        </QueryProvider>
      </body>
    </html>
```

## 4. 이렇게 하면..
![](https://velog.velcdn.com/images/junhopportunity/post/47d6dc40-0461-4692-b35e-689266f33353/image.png)

![](https://velog.velcdn.com/images/junhopportunity/post/f7537158-87b5-4e8e-8b58-c4d9ef40a476/image.png)

개발자 도구의 콘솔에서는 클라이언트 컴포넌트인 QueryProvider, Header 의 출력 문구만 출력되는 것을 확인할 수 있고 터미널에서는 레이아웃, QueryProvider, Header, Main page 출력 문구가 모두 출력되는 것을 확인할 수 있었습니다.

## +)  그렇다면 왜 children 으로 넘기는 건 괜찮은 걸까?

클라이언트 컴포넌트가 서버 컴포넌트를 감싸고 있지만 직접 서버 컴포넌트 내부의 구현을 침범하지 않기 때문에 문제가 발생하지 않습니다.

여기서 직접 서버 컴포넌트 내부 구현을 침범하지 않는 다는 것은 children 으로 서버 컴포넌트를 넘겨준다는 의미입니다.

## 한 줄 요약

> TanStack Query를 Next.js App Router에서 사용할 때는
QueryClientProvider를 별도 클라이언트 컴포넌트로 분리해 사용하면
서버 컴포넌트의 이점을 유지하면서 상태 관리를 도입할 수 있습니다.
>
