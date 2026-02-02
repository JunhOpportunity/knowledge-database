---
title: "App Router - 서버 컴포넌트"
date: "2025-07-04"
category: "nextjs"
---
# App Router
## 서버 컴포넌트

> 서버 측에서 단 한 번만 실행되는 컴포넌트
NEXTJS 공식 문서 - 대부분의 페이지를 서버 컴포넌트로 구성하고 꼭 필요한 경우에만 클라이언트 컴포넌트 사용.
> 

페이지 라우터의 경우에는 자바스크립트 번들로 한꺼번에 다 묶은 다음에 브라우저에게 전달하기 때문에 불필요하게 많은 컴포넌트들이 자바스크립트 번들에 포함될 수밖에 없어서 자바스크립트 번들의 용량이 쓸데없이 커지게 된다.

| 서버 컴포넌트 | 클라이언트 컴포넌트 |
| --- | --- |
| 서버측에서 사전 렌더링 진행할 때 딱 한 번만 실행 | 사전 렌더링 진행할 때 한 번, 하이드레이션 진행할 때 한 번 실행됨 |
| 총 1번 실행 | 총 2번 실행 |
| 기본적으로 서버 컴포넌트임 | “use client” 작성 |
| 안에서 클라이언트 컴포넌트 import 가능 | 안에서 서버 컴포넌트 import 불가능
⇒ 에러 대신에 서버 컴포넌트 자체를 클라이언트 컴포넌트로 바꿔버린다. |

app router 에서 모든 컴포넌트는 기본적으로 서버 컴포넌트이다.
서버 컴포넌트는 브라우저에서 실행되지 않기 때문에 console.log 출력 안된다.
즉, 브라우저에서 사용하는 작업은 서버 컴포넌트에서 사용할 수 없다.(useEffect 등등)

Q. 반드시 클라이언트 컴포넌트 안에서 서버 컴포넌트를 써야 하는 경우라면 어떻게 해야할까?
⇒ children 으로 넘겨주면 서버 컴포넌트를 클라이언트 컴포너트로 바꾸지 않는다!

```tsx
// 클라이언트 컴포넌트
"use client";
export default function ClientComponent({children} : {children: ReactNode }) {
	return <div>{children}</div>
}

// index page
<ClientComponent>
	<ServerComponent/>
</ClientComponent>
```

## 주의사항

1. 클라이언트 컴포넌트에서 서버 컴포넌트를 import 할 수 없다
2. 서버 컴포넌트에서 클라이언트 컴포넌트에게 직렬화 되지 않은 Props는 전달이 불가능하다
자바스크립트의 함수는 클로저나 렉시컬 함수 때문에 직렬화가 불가능하다.

ex) 직렬화 예시

<img width="540" height="171" alt="image" src="https://github.com/user-attachments/assets/33f76533-87ab-46f3-9ceb-63831b264151" />


사전 렌더링 과정
⇒ 서버 컴포넌트 따로 실행(RSC payload 문자열 생성) - 완성된 HTML 페이지 실행

## 네비게이팅
Static 에 해당하는 페이지들은 RSC payload와 JS 번들을 모두 불러오고
Dynamic 에 해당하는 페이지들은 RSC payload만 프리패칭하고 JS 번들은 향후 실제 페이지 이동이 발생했을 때만 불러온다 
