---
title: "Page Router"
date: "2025-06-25"
category: "nextjs"
---

# Page Router

파일 또는 폴더 이름을 기준으로 페이지 라우팅을 제공

동적 경로의 경우에는 대괄호를 사용한다. /item/[id].js

## 파일들의 역할

### _app.tsx

모든 페이지에 공통적으로 적용될 레이아웃이나 데이터 적용을 위한 페이지이다.

_app.tsx 파일의 App 컴포넌트는, 모든 페이지의 부모가 되는 컴포넌트라고 보면 된다.

App 컴포넌트에 Nav bar 또는 header 를 추가하면 모든 페이지에 적용된다.

### _document.tsx

기존 React의 index.html 이라고 보면 된다.

Meta, font 등등의 html 태그 관리

### next.config.mjs

nextjs의 기본 설정들을 관리하는 곳.

reactStrictMode 설정도 가능하다. (잠재적 에러를 확인하기 위해 두 번 실행되는 것)

## 폴더 구조 및 페이지 구조

### 페이지 구성

페이지를 구성하기 위해서는 pages 폴더 내부에 해당 페이지의 파일을 생성하거나(/pages/search.tsx) 해당 페이지의 폴더와 파일(/pages/search/index.tsx)을 생성하면 된다.

Q. /pages/search.tsx 와 /pages/search/index.tsx 이렇게 두 개가 중복된 형태로 만들었다면 에러가 뜰까? ⇒ 에러 발생. 각 경로에 단 하나의 컴포넌트만 존재해야 한다.

### 쿼리 스트링 적용

쿼리 스트링을 사용하기 위해서는 `import {useRouter} from "next/router"` 을 활용해야 한다.

여기서 주의할 점은, app router의 경우에는 next/navigation 을 사용하므로 import 할 때 혼동하지 않도록 신경써야 한다.

```jsx
// localhost:3000/search?q=abc 접속

import { useRouter } from "next/router"

export default function Search() {
  const router = useRouter()

  const {q} = router.query;

  return <h1>Search {q}</h1>
}
```

### 동적 경로

`/pages/book/[id].tsx` 로 구성

이 경우에도 마찬가지로 쿼리 스트링처럼 useRouter 사용해서 값을 받아온다.

`[id]` 로 만들었으므로 query 안에 id로 저장된다.

<img width="264" height="74" alt="image" src="https://github.com/user-attachments/assets/776e8c3f-a911-4632-bf0e-1432d2a7d839" />


[…id].tsx : 를 사용하면 여러개의 동적 경로도 처리하고 싶은 경우에는 `catch all segment` 라고 하는 방식으로 구현하면 된다.
query 안에 id로 배열 형태로 저장된다.
이 경우에는 아무런 파라미터가 오지 않는 경우에는 에러가 발생한다
(localhost:3000/book 접속한 경우)

[[…id]].tsx : `optional catch segment` 아무런 파라미터가 오지 않는 경우까지 포함해서 모든 경우에 대해 대응할 수 있는 방법이다.

### 404 페이지

pages/404.tsx 생성 하면 바로 만들 수 있다.

### 네비게이팅

> 페이지 이동을 할 때는 `<Link href={”/”}` 컴포넌트를 사용한다.
함수 내부에서는 `router.push(”/”)` 를 사용한다.
> 

CSR 방식으로 페이지를 이동하기 때문에 빠르게 페이지 이동이 가능하다.

- 프로그래매틱한 페이지 이동 : 특정 조건이 만족했을 경우 함수 내부에서 페이지를 이동하도록 하는 것
- router.push(”/”) : 기본 이동. 히스토리에 기록됨
- router.back() : push와 비슷하지만 히스토리를 덮어쓰기 때문에 뒤로가기 안됨 (로그인 후 로그인 페이지를 안보이게 하거나 구글 폼 제출 후 URL 초기화 할 때 사용)
- router.replace() : 뒤로 가기

### 프리패칭

<Link> 로 구현한 페이지들은 프리패칭이 자동으로 되지만 프로그래메틱한 페이지 이동으로 구현한 페이지들은 프리팿이이 자동으로 이루어지지 않는다.

따라서 현재 컴포넌트가 마운트 되었을 때 프리패칭 되도록 구현하면 된다.
⇒ useEffect 사용

```jsx
useEffec(()=>{
	router.prefetch('/test')
}, [])
```

### API Routes

> API 응답을 설정할 수 있는 파일 (잘 쓰이진 않는다)
> 

`/pages/api/파일.ts`

localhost:3000/api/파일 

```jsx
import type { NextApiRequest, NextApiResponse } from "next";

type Data = {
  name: string;
};

export default function handler(
  req: NextApiRequest,
  res: NextApiResponse<Data>,
) {
  res.status(200).json({ name: "John Doe" });
}
```

### 스타일링

> index.module.css 로 이름을 작성한다.
> 

css 는 평소처럼 작성하고, 사용할 때는 `import style from “./index.module.css”` 로 불러와서 `style.h1` 이렇게 사용한다

```jsx
import style from "./index.module.css";

<h1 className={style.h1}>인덱스<h1>
```
