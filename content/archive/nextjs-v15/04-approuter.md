---
title: "App Router - 기본"
date: "2025-07-01"
category: "nextjs"
---

# App Router

## 페이지 라우팅 설정

> 경로 폴더 생성 후 반드시 page.tsx 파일을 생성해야만 한다.
> 

동적 경로 역시 [id] 라는 폴더 생성 후 해당 폴더 내부에 page.tsx 파일을 생성해야 한다.

- app/book/page.tsx
- app/book/[id]/page.tsx

## 쿼리 스트링 전달

app router의 경우에는 쿼리 스트링이 모두 props 로 전달된다.

이때, 객체 형태의 promise 로 전달되므로 사용하기에 앞서 타입 정의가 필요하다

```jsx
export default async function Page({
  searchParams,
}: {
  searchParams: Promise<{ q: string }>;
}) {
  const { q } = await searchParams;
  return <h1>Search {q}</h1>;
}
```

Q. 어떻게 함수 컴포넌트에 async 키워드를 붙일 수 있는가?
⇒ 서버 컴포넌트이기 때문에가능하다.

## 동적 경로

동적 경로의 경우에는 param 객체 안에 promise 형태로 id가 들어있다.

```jsx
export default async function Page({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;

  return <div>{id}</div>;
}
```

## 레이아웃

> 현재 적용된 경로의 모든 하위 경로에 똑같이 적용됨
> 

여기서 주의할 점은, 레이아웃에 page를 전달할 수 있도록 children을 추가해 주어야 한다는 점이다.

하위에 여러 개의 레이아웃들이 존재한다면 모두 중첩해서 적용된다.

Q. 특정 페이지에만 레이아웃을 설정하고 싶다면? 
⇒ 라우트 그룹 사용. 소괄호로 감싸서 그룹화함. 경로에는 아무런 영향을 미치지 않는 폴더이다. 따라서 이 폴더를 생성한 뒤에 여기로 옮겨두면 된다.
