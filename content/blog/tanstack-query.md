---
title: "Tanstack Query의 기본 문법과 실전 사용 방법"
date: "2026-07-30"
category: "Tanstack Query"
series: "Youreca-V2"
description: "Tanstack Query가 왜 필요한지에 대해 알아보고 기존 프로젝트에 어떻게 적용할 수 있을지 생각해보기"
---

<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/4ff9c5cb-05cb-454c-b89d-81738e2a86c7" />

# useQuery란 무엇이고 왜 필요할까?

useQuery는 도대체 왜 필요한걸까?

상태관리 라이브러리인 Redux를 사용해보았다면, 더 헷갈릴 수 있다.

한 마디로 정리하자면, `Tanstack Query는 전역 상태 관리 라이브러리가 아닌, 서버의 데이터를 관리하기 위한 라이브러리이다.` 

## 예전 코드의 문제점

이전에 Youreca 프로젝트를 하면서 가장 큰 고민거리 중 하나가 바로 이것이었다.

> 매 페이지마다 User 데이터를 호출 → 검증을 통해 페이지를 보여주거나 회원가입 페이지로 이동할지 판단 → 페이지를 보여줌
> 

이 과정이 너무 자주 일어났다는 점이 문제였다.

그때 당시에는 React 첫 프로젝트이기도 했고, 이런 상태관리 라이브러리나 데이터 관리 라이브러리 자체를 사용할 생각을 하지도 못했기때문에 모든 페이지 코드에 User 데이터를 호출하는 코드를 집어넣었다.

## Tanstack Query를 사용하면?

Tanstack Query를 사용하게되면 호출한 User 데이터를 캐시에 저장하고, 이후에 다시 useQuery를 사용해 데이터를 호출했을 때 이미 캐시에 존재하는 경우 API 요청을 보내지 않고 캐시 데이터를 사용해 비용을 줄일 수 있게 된다.

즉, 기존에는 매 페이지마다 User 데이터를 요청했기 때문에 해당 데이터 응답까지 기다리는 시간도 존재했고, 매번 API 요청이 이루어졌기 때문에 비용도 더 발생했지만 이제는 이러한 문제점들을 해결할 수 있게된 것이다.

## 예전 방식과 새로운 방식의 직관적인 차이점

```jsx
// 예전 방식 (총 3번 요청)

Home -> GET /users
Profile -> Get /users
Posts -> Get /users
```

```jsx
// 새로운 방식 (총 1번 요청)

Home -> GET /users
Profile -> 캐시 사용
Posts -> 캐시 사용
```

## 근데, 캐시된 데이터만 보여주면 문제가 생기지 않을까요?

그래서 Tanstack Query는 ‘언제 새 데이터를 가져와야하지?’ ‘얼마나 지나야 오래된 데이터가 되는 거지?’ ‘언제 새로고침하지?’ 라는 고민들을 대신 관리해준다.

이때 사용되는 개념이 바로 `staleTime` 이다.

> stale : 신선하지 않은, 진부한, 오래된 이라는 뜻의 영어 단어
> 

```jsx
useQuery({
  queryKey: ['users'],
  queryFn: getUsers,
  staleTime: 1000 * 60,
})
```

`useQuery`에서 `staleTime`을 `1000 * 60`으로 설정해두면 ‘1분 동안은 이미 받은 데이터를 사용하고, 1분이 지나면 오래된 데이터이므로 자동으로 다시 요청’ 이 과정을 거치게 된다.

게다가 창을 다시 열거나 네트워크가 끊겼다가 다시 연결되는 경우에도 데이터를 새로 요청하는데 이 역시 Tanstack Query에서 알아서 수행해준다.

만약 자동으로 주기적으로 요청을 하고 싶다면 `refetchInterval: 1000 * 60` 이렇게 작성해주면 1분마다 API 요청을 해준다.

## +) Redux랑 뭐가 다른거죠?

Redux는 전역 상태 관리 라이브러리인데, 보통 다음과 같은 클라이언트 상태를 관리한다.

- 다크모드/라이트모드
- 로그인 여부
- 사이드바 열림 여부

## 그래서 useQuery로 데이터를 저장하면, 다시 쓰기 위해서는 어떻게 해야하죠?

필자는 처음 useQuery를 작성하고 나서, 다음에 다른 페이지에서 해당 데이터를 사용하려면 또 다른 코드가 필요한 줄 알았다.

하지만, 다른 페이지에서도 아예 완벽히 똑같은 useQuery 코드를 작성해서 사용한다는 것을 알게되고 큰 충격을 받았다.

“그럼 왜 useQuery를 사용하는 거지? 어차피 모든 페이지에서 다 같은 코드를 써야하는데.”

그래서 더 깊게 찾아보면서 useQuery는 일단 요청한 데이터를 캐싱해서 다른 페이지에서도 사용할 수 있게 한다는 것은 이해했지만, 여전히 모든 페이지에서 같은 코드를 여러번 작성해야 한다는 점에서 조금 의아했다.

그래서 이 방법을 해결하기 위한, 그러니까 더 효율적으로 코드를 작성하기 위해서는 Custom Hook을 작성해서 사용하면 된다는 것을 알게되었다.

# useQuery 기본 문법

```jsx
import { useQuery } from '@tanstack/react-query'

type ResponseValue = {
  message: string
  time: string
}

export deafult function DelayData() {
	const {data, error} = useQuery<ResponseValue>({
		queryKey: ['delay'],
    queryFn: async () => {
      const res = await fetch('https://api.heropy.dev/v0/delay?t=1000')
      const data = await res.json()
      if (!data.time) {
        throw new Error('문제가 발생했습니다!')
      }
      return data
    },
    staleTime: 1000 * 10, // 10초
    retry: 1,
    placeholderData: prev => prev
	})
  {data && <div>{JSON.stringify(data)}</div>}
  {error && <div>{error.message}</div>}
}
```

### queryKey

이때 쿼리 키를 기준으로 쿼리를 캐싱한다.

```jsx
useQuery({
  queryKey: ["user", 1],
  queryFn: () => getUser(1)
})
```

이 경우, `"user", 1` 이라는 캐시 공간이 생성된다.

배열 형태로 지정하고, 다중 아이템 쿼리 키를 사용할 때는 순서가 중요하다.

### queryFn

데이터를 가져오는 비동기 함수.

반드시 데이터를 반환하거나 오류를 던져아 한다.

queryFn은 queryKey를 기반으로 데이터를 가져오는 함수이며, Promise를 반환해야 한다.

### placeholderData

새로운 데이터를 가져오는 과정에서 쿼리가 무효화되어 일시적으로 데이터가 없는 상태가 되면 데이터 출력 화면이 깜빡이거나 안 보일 수 있는데, 이런 문제를 방지하기 위해서 쿼리 함수가 호출되는 대기 상태(Pending)에서 표시할 데이터를 미리 지정할 수 있다.

새로운 데이터를 가져오기 직전의 이전 데이터를 넘기는 것도 가능하다.

### 상태 확인

- isFetching : 쿼리 함수가 실행 중인지의 여부 확인
- isPending : 캐시된 데이터가 없고 쿼리가 아직 완료되지 않은 상태인지 확인
- isLoading : 쿼리의 첫 번째 데이터 가져오기가 진행 중인지의 여부 확인

### enabled

```jsx
const {data} = useQuery({
  queryKey:["user", userId],
  queryFn:()=>getUser(userId),
  enabled: !!userId
})
```

처음 렌더링때는 userId가 없을 수 있기 때문에 이런 경우에는 API를 요청하지 않도록 하기 위해서 이렇게 구현하는 것이다.

# useMutation

데이터 변경 작업(생성, 수정, 삭제 등)을 위한 훅이다.

로그인, 회원가입, 게시글 작성, 댓글 작성, 수정, 삭제 모두 mutation이다.

즉, useQuery는 데이터 가져오기. useMutation은 내보내기에 집중하는 훅으로 이해하면 된다.

```jsx
import { useMutation, useQueryClient } from '@tanstack/react-query'

const queryClient = useQueryClient()

const { mutate, error, isPending, isError } = useMutation({
	  mutationFn: async (newUser: User) => { // 
      const res = await fetch('https://api.heropy.dev/v0/users', {
        method: 'POST',
        body: JSON.stringify(newUser)
      })
      if (!res.ok) throw new Error('변이 중 에러 발생!') // 변이 실패!
      return res.json() // 변이 성공!
    },
    onMutate: async newUser => {
      // 낙관적 업데이트 전에 사용자 목록 쿼리를 취소해 잠재적인 충돌 방지!
      await queryClient.cancelQueries({ queryKey: ['users'] })

      // 캐시된 데이터(사용자 목록) 가져오기!
      const previousUsers = queryClient.getQueryData<Users>(['users'])

      // 낙관적 업데이트
      if (previousUsers) {
        queryClient.setQueryData<Users>(['users'], [...previousUsers, newUser])
      }

      // 각 콜백의 context로 전달할 데이터 반환!
      return { previousUsers }
    },
    onSuccess: (data, newUser, context) => {
      console.log('onSuccess', data, newUser, context)
      // 변이 성공 시 캐시 무효화로 사용자 목록 데이터 갱신!
      queryClient.invalidateQueries({ queryKey: ['users'] })
    },
    onError: (error, newUser, context) => {
      console.log('onError', error, newUser, context)
      // 변이 실패 시, 낙관적 업데이트 결과를 이전 사용자 목록으로 되돌리기!
      if (context) {
        queryClient.setQueryData(['users'], context.previousUsers)
      }
    },
    onSettled: (data, error, newUser, context) => {
      console.log('onSettled', data, error, newUser, context)
    },
    retry: 3, // 변이 실패 시 3번 재시도
    retryDelay: 500 // 0.5초 간격으로 재시도
})

mutation.mutate(data)
```



# 실제 코드

실제로 실무에서 사용할 땐 이렇게 사용한다고 한다.

필자도 이번 Youreca V2 프로젝트에는 다음과 같은 구조로 파일을 관리하고 코드를 작성할 계획이다.

```jsx
// 폴더 구조

src/
 ├── api/
 │    └── user.ts
 ├── hooks/
 │    └── useUsers.ts
```

```jsx
// api/user.ts

export async function getUsers() {
  const res = await fetch("/api/users");

  if (!res.ok) {
    throw new Error("조회 실패");
  }

  return res.json();
}
```

```jsx
// hooks/useUsers.ts

import { useQuery } from "@tanstack/react-query";
import { getUsers } from "../api/user";

export function useUsers() {
  return useQuery({
    queryKey: ["users"],
    queryFn: getUsers,
    staleTime: 1000 * 60,
  });
}
```

```jsx
// 사용

const { data } = useUsers();
```
