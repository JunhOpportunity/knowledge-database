---
title: "App Router - 병렬 라우트와 인터셉팅 라우트"
date: "2025-07-10"
category: "nextjs"
---
# App router
## 병렬 라우트

> 하나의 화면에 여러 개를 렌더링 시키는 것
> 

```tsx

// src/app/parallel/@sidebar/page.tsx

// src/app/parallel/layout.tsx
import { ReactNode } from "react";

export default function Layout({
  children,
  sidebar,
}: {
  children: ReactNode;
  sidebar: ReactNode;
}) {
  return (
    <div>
      {children}
      {sidebar}
    </div>
  );
}
```

소셜 미디어 서비스나 관리자 대시보드 등 복잡한 구조를 갖는 UI에 유용하게 활용된다.

폴더 이름 앞에 골뱅이가 붙은 것을 `슬롯`이라고 부른다.

슬롯 : 병렬로 렌더링 될 페이지 컴포넌트를 보관하는 폴더

부모 폴더의 레이아웃에 props로 전달된다.

슬롯의 경로는 무시하기 때문에 슬롯 내부에 하위 경로 폴더가 없는 경우 그냥 무시하고 이전 페이지를 보여준다.
이때 주의 할 점은 새로고침 할 경우에는 이전 페이지가 존재하지 않기 때문에 404 에러를 발생시킨다는 것이다.
따라서 슬롯별로 렌더링 할 페이지가 없을 경우를 대비해 default 페이지를 만들어야 한다. (각 슬롯 내부에 `default.tsx` 생성)

<img width="546" height="390" alt="image" src="https://github.com/user-attachments/assets/91e95d47-a3e0-44b5-8afb-ef5f7ba0a278" />


여기서 만약 `http://localhost:3000/parallel/a` 로 접속했다면, 보라색 페이지만 업데이트 되고, 나머지 파란색과 초록색 페이지는 그대로 유지된다.

- 만약 에러 발생하면 .next 폴더 제거 후 다시 실행
- 슬롯 개수는 제한이 없다

## ❌인터셉팅 라우트

> 특정 조건에 따라 원래 페이지가 아닌 다른 페이지 렌더링 하는 것
> 

### 설명

여기서 특정 조건이란? 개발자가 임의로 설정할 수 없다. 기본적으로 세팅되어있는 조건이고, 초기 접속이 아닌 경우에 동작하도록 설정된다.

ex) 인스타그램에서 게시글을 눌렀을 때 모달 형태로 보여주다가, 새로고침 할 경우 아예 상세 페이지로 넘어가버리는 것

### 구현

`(.)book/[id]` 이렇게 작성하면 현재 동일 경로에 존재하는 `book/[id]` 가로챈다.

만약 한 경로 상위에 있다면 `(..)`, 두 경로 상위에 있다면 `(..)(..)` 를 적어준다.

`(…)` 은 app 폴더 바로 아래에 있는 경로를 말한다.

```tsx
// 인터셉팅 페이지 (기존 페이지 그대로 렌더링 되도록 porps 설정)
import BookPage from "@/app/book/[id]/page";

export default function Page(props:any) {
	return(
		<div>
			<Modal>
				<BookPage {...props} />
			</Modal>
		</div>
	)
}
```

```tsx
// src/components/modal.tsx 모달생성
"use client";

import {ReactNode} from "react";
import style from "./modal.module.css";
import {createProtal} from "react-dom";
import {useRouter} from "next/navigation"

export default function Modal({children} : {children: ReactNode}) {
	
	const dialogRef = useRef<HTMLDialogElement>(null);
	const router = useRouter();
	
	useEffect(()=> {
		if(!dialogRef.current?.open) { // 모달이 닫혀있는 경우
			dialogRef.current?.showModal(); // 모달 열어두기
			dialogRef.current?.scrollTo({top:0}); // 스크롤 위치 맨 위로
		}
	}, []);
	
	return createPortal(
		<dialog 
			onClose={() => router.back()} // ESC 눌렀을때
			onClick={(e) => { // 바깥쪽 눌렀을때
				if((e.target as any).nodeName === "DIALOG") {
					router.back();
				}
			}}
			className={style.modal}
			ref={dialogRef}
		>
			{children}
		</dialog>,
		document.getElementById("modal-root") as HTMLElement
	)
}

// src/app/layout.tsx
	body 바로 아래에
	<div id="modal-root"></div>
</body>
```

```tsx
// 모달 스타일
.modal {
	width: 80%;
	max-width: 500px;
	
	margin-top: 20px;
	border-radius: 5px;
	border:none;
}

.modal::backdrop { // 모달 뒷부분 흐릿하게
	background: rgba(0, 0, 0, 0.7);
}

```

🤚 병렬 라우트와 인터셉팅 라우트 같이 쓰는 거 좋음
