---
title: "App Router - 데이터 캐시, 메모이제이션, 풀 라우트 캐시, 서버 액션"
date: "2025-07-09"
category: "nextjs"
---
# App Router
## 데이터 캐시

> fetch의 두 번째 인자로 옵션을 전달해 사용
`fetch("api/address”, {cache: “force-cache"})`
> 

fetch 메서드를 활용해 불러온 데이터를 Next 서버에 보관
⇒ 불필요한 요청 줄여서 성능 향상 가능

여기서 주의할 점은, Next 에서는 fetch에 cache를 제공하지만 axios 같은 경우에는 제공하지 않는다는 것이다.

- `cache: “no-store”` ⇒ 캐싱 아예 하지 않음. 기본값 (14버전 까지는 캐싱되는 게 기본 값이었다.)
- `cache: “force-cache"` ⇒ 항상 캐싱함.
- `next: {revalidate: 3}` ⇒ 특정 시간 주기로 캐시 업데이트함. (마치 ISR 방식)
- `next: {tags: ['a']}` ⇒ 요청이 들어왔을 때만 캐시 업데이트함.(마치 on-demand ISR 방식)

## 리퀘스트 메모이제이션

> 요청을 기억하고 같은 요청의 경우 해당 요청을 캐싱해 최적화
한 번의 서버 렌더링 주기 후 초기화된다.
> 

렌더링이 종료되면 모든 리퀘스트 메모이제이션이 소멸된다.
따라서 데이터 캐시와는 비슷해보이지만 차이점이 존재한다.

Next 에서 자동으로 적용된다.

## ❌풀 라우트 캐시

> Next 서버에서 렌더링 결과를 캐싱하는 것
> 

정적으로 생성되는 것과 비슷하다고 볼 수 있다.

| 동적 페이지 | 정적 페이지 |
| --- | --- |
| 쿠키, 헤더, 쿼리스트링
사용한 컴포넌트 | 그 외의 컴포넌트 |

동적 페이지의 기준 : 쿠키, 헤더, 쿼리스트링을 사용한 컴포넌트

정적 페이지의 기준 : 동적 페이지가 아닌 것

정적 페이지에만 풀 라우트 캐시가 적용된다.

리퀘스트 메모이제이션이나 데이터 캐시 등 하나라도 revalidate 옵션이 붙은 경우 풀 라우트 캐시도 같이 업데이트 한다

이 기능을 구현하기 위해서는 Suspense 로 감싸주면 된다.

Suspense 태그로 감싸진 부분은 곧바로 렌더링 되지 않는다.
내부의 비동기 작업이 끝나기 전까지 fallback 부분을 보여준다.

```tsx
// layout.tsx

<Suspense fallback={<div>대체 ui</div>}>

</Suspense>
```

동적 페이지의 경우에는 generateStaticParams 를 사용한다.
이걸 사용하면 미리 페이지를 렌더링해둔다.

```tsx
export function generateStaticParams () {
	return [{id: "1"}, {id: "2"}]
}
```

## 라우트 세그먼트 옵션

> 페이지의 동작(revalidate, dynamic, static) 강제 설정
> 

```tsx
export consy dynamic = "옵션";
```

- auto : 아무것도 강제로 선택하지 않는 기본값
- force-dynamic : 강제로 dynamic 페이지로 설정
- force-static : 강제로 static 페이지로 설정
- error : 강제로 static 페이지로 설정 (동적 페이지로 해야하는 경우 에러를 발생시킴)

## 클라이언트 라우터 캐시

> 브라우저에 저장되는 캐시(데이터, 페이지 정보, 상태 등)
> 

새로고침 하거나 탭을 닫았다가 다시 열면 캐시가 초기화된다

## ❌서버 액션

> 브라우저에서 호출할 수 있는 서버에서 실행되는 비동기 함수
> 

서버에서만 실행되는 함수를 브라우저(클라이언트)가 직접 호출 가능

```tsx
async function createReviewAction(formData: FormData) {
	"use server";
	console.log("서버액션");
	console.log(formData);

	const author = formData.get("author")?.toString();
}

<form action={createReviewAction}>
	<input name="content" placeholder="" />
</form>
```

### 서버 액션 파일 분리하기

- 경로 : `src/actions/이름.action.ts`
- 함수 내부에 있던 `"use server"` 최상단으로 옮기기

### 재검증

> `revalidatePath()` 사용
> 

만약 새로운 댓글을 작성한 뒤에 해당 페이지에서 바로 새로운 댓글을 업데이트 해야한다면 revalidatePath 를 사용해 재검증한다.

이 함수는 서버측에서만 호출할 수 있는 메서드이다.

재검증 할 경우 데이터 캐시 뿐만 아니라 풀 라우트 캐시까지 삭제됨
⇒ 즉, 새롭게 업데이트 된 페이지는 풀라우트 캐시에 저장되지 않는다.

```tsx
// 1. 특정 주소에 해당하는 페이지만 재검증
revalidatePath(`/book/${booId}`)

// 2. 특정 경로의 모든 동적 페이지를 재검증
revalidatePath("/book/[id]", "page")

// 3. 특정 레이아웃을 갖는 모든 페이지 재검증
revalidatePath("/(with-searchbar)", "layout")

// 4. 모든 데이터 재검증
revalidatePath("/", "layout")

// 5. 태그 기준 데이터 캐시 재검증
revalidateTag(`review-${bookId}`)

{next: {tags:[`review-${bookId}`]}}
```

### 🖐️ ❌클라이언트에서 중복 제출 방지

> React의 `useActionState` 훅 사용
> 
- 전달 : 첫 번째 인수로 액션, 두 번째 인수로 초기값 넘겨준다.
- 반환 : state, formAction, isPending(로딩값)

```tsx
const [state, formAction, isPending] = useActionState(
	createReviewAction, 
	null
)

<form action={formAction}>
	<button disabled={isPending} type="submit"/>
</form>
```
