---
title: "SSR, SSG, ISR"
date: "2025-06-30"
category: "nextjs"
---

# SSR

> getServerSideProps 사용
> 

| 장점 | 단점 |
| --- | --- |
| 페이지 내부의 데이터를 항상 최신으로 유지 | 데이터 요청이 늦어질 경우 모든게 늦어진다 |

페이지 컴포넌트보다 먼저 실행 되어서 필요한 데이터 미리 가져옴

```tsx
// SSR 구현
export const getServerSideProps = () ⇒ {
	const data = {}
	
	return {
		props : {
			data,
		}
	}
} 

// 사용 시 타입 선언
export default function Home({
	data,
}: InferGetServerSidePropsType<typeof getServerSideProps>) {

	return (<div></div>)
}
```

getServerSideProps에서 쿼리 스트링을 가져올 때는 `GetServerSidePropsContext` 를 사용한다.

```tsx
export const getServerSideProps = async (
	context: GetServerSidePropsContext
) => {
	const q = context.query.q;
	
}
```

URL의 파라미터를 가져올 때는 `context.params.id`를 사용하면 되는데, `undefined` 일 가능성이 있다는 이유로 에러가 발생한다.
따라서 `context.params!.id` 로 단언해주면 해결된다.

# SSG

> 빌드 타임에 미리 사전 렌더링 하는 방식
`getStaticProps` 사용
> 

| 장점 | 단점 |
| --- | --- |
| 매우 빠른 속도로 페이지 보여줌 | 매번 똑같은 페이지만 응답함 |
|  | 최신 데이터 반영 어려움 |

```tsx
// 사용 시 타입 선언
export default function Home({
	data,
}: InferGetStaticPropsType<typeof getStaticProps>) {

	return (<div></div>)
}
```

개발 모드에서는 SSR처럼 들어갈 때마다 재요청한다. 따라서 프로덕션 모드로 실행해야 확인이 가능.

아무 설정도 없으면 기본적으로 SSG 방식으로 수행된다.

사전 렌더링 과정에서는 쿼리 스트링을 가져올 수 없다. 왜냐하면 페이지가 이미 다 만들어지 상태이고, 이후에 사용자의 입력으로 쿼리 스트링이 변경되기 때문이다. 따라서 이런 경우에는 그냥 평소처럼 페이지 컴포넌트 내에서 데이터를 요청해서 가져오는 방식으로 해야한다. (useEffect)

## 동적 경로 설정 방법

그래서 동적 경로의 경우에는 미리 경로를 지정해주어야 한다.
여기서 path 부분이 동적 경로 목록을 의미하고
fallback 은 그 외의 경로를 의미한다.
false 로 설정할 경우 not-found 반환

- false : 404 not found
- blocking : 미리 지정하지 않은 경우 실시간으로 즉시 생성
처음 만들땐 SSR 방식으로 생성되고, 이후에는 한 번 생성되었으니 다시 SSG 방식으로 페이지를 반환한다.
- true : 즉시 생성 + 페이지만 미리 반환
props 없는 데이터가 없는 페이지만 미리 렌더링하고, 이후에 데이터 있는 페이지 렌더링

```tsx
// 동적 경로 지정
export const getStaticPaths = () => {
	return {
		path: [
			{params: {id: "1"}},
			{params: {id: "2"}},
			{params: {id: "3"}},
		],
		fallback: false,
	}
}
```

# ISR

> 사전 렌더링 이후 일정 시간을 주기로 새로운 페이지 렌더링
SSR과 SSG 두 방식의 장점을 모두 갖고있음
revalidate 사용
> 

초단위로 설정 가능

```tsx
export const getStaticProps = () ⇒ {
	const data = {}
	
	return {
		props : {
			data,
		},
		revalidate: 3,
	}
} 
```

## on-demand ISR

시간과 관계 없이 사용자가 요청할 때마다 데이터가 변하는 경우 문제가 생김.

아래 코드에서 localhost:3000/api/revalidate 로 요청을 보내면 페이지가 바뀌게 된다.

```tsx
// pages/api/revalidate.ts
import {NextApuRequest, NextApiResponse} from "next";

export default async function hanlder(
	req: NextApiRequest,
	res: NextApiResponse
) {

	try {
		await res.revalidate("/");
		return res.json({revalidate: true});
	} catch (err) {
		res.status(500).send("revalidation Failed")
	}

```
