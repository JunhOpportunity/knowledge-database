---
title: "상태 관리 모킹과 MSW 모킹"
date: "2024-02-10"
category: "tdd"
---

# 상태 관리 모킹

React Redux, Recoil, zustand 등 상태 관리 라이브러리에 대한 모킹 가이드를 제공하고 있다.

장바구니 상품 정보는 로그인한 사용자와 매핑되므로 앱 전반적으로 필요한 데이터이기 때문에 zustand를 사용한 상태 관리가 필요하다.

`__mocks__` 폴더 내부에 위치한 파일들은 vitest나 jest에서 특정 모듈을 자동 모킹할 때 사용한다

```jsx
// __mocks__/justand.js
const { create: actualCreate } = await vi.importActual('zustand');
import { act } from '@testing-library/react';

// 앱에 선언된 모든 스토어에 대해 재설정 함수를 저장
const storeResetFns = new Set();

// 스토어를 생성할 때 초기 상태를 가져와 리셋 함수를 생성하고 set에 추가합니다.
export const create = createState => {
  const store = actualCreate(createState);
  const initialState = store.getState();
  storeResetFns.add(() => store.setState(initialState, true));
  return store;
};

// 테스트가 구동되기 전 모든 스토어를 리셋합니다.
beforeEach(() => {
  act(() => storeResetFns.forEach(resetFn => resetFn()));
});
```

만약 특정 초기 값으로 초기화를 진행하고 싶은 경우에는 테스트 전에 모킹을 진행해야 한다.

```jsx
import { useCartStore } from '@/store/cart';
import { useFilterStore } from '@/store/filter';
import { useUserStore } from '@/store/user';

const mockStore = (hook, state) => {
  const initStore = hook.getState();
  hook.setState({ ...initStore, ...state }, true);
};

export const mockUseUserStore = state => {
  mockStore(useUserStore, state);
};

export const mockUseCartStore = state => {
  mockStore(useCartStore, state);
};

export const mockUseFilterStore = state => {
  mockStore(useFilterStore, state);
};
```

통합 테스트를 작성할 때는 페이지의 비즈니스 로직을 기준으로 최대한 실제 사용성과 유사하게 범위를 나누는 것이 중요하다.

## 🤔 특정 상황에서 alert 노출 테스트

stubGlobal이라는 함수를 사용하여 JSDOM 내의 윈도우 객체에 대한 기본 동작을 변경할 수 있다.

```jsx
it('특정 아이템의 수량이 1000개로 변경될 경우 "최대 999개 까지 가능합니다!"라고 경고 문구가 노출된다', async () => {
  const alertSpy = vi.fn();

  // window.alert를 alertSpy로 대체
  vi.stubGlobal('alert', alertSpy);

  // 컴포넌트 렌더링 후 테이블 데이터의 row 조회
  const { user } = await render(<ProductInfoTable />);
  const [firstItem] = screen.getAllByRole('row');

  // 인풋 요소에 alert가 발생할 조건을 입력 (999 초과 시 alert 발생)
  const input = within(firstItem).getByRole('textbox');

  await user.clear(input);
  await user.type(input, '1000');

  // 1 번 호출되며 alert 문구까지 출력되는지 확인
  expect(alertSpy).toHaveBeenNthCalledWith(1, '최대 999개 까지 가능합니다!');
});
```

React 테스팅 라이브러리에서 queryBy로 시작하는 함수는 요소의 존재 유무를 판단할때 사용하는 query이다. geyBy로 시작하는 함수와 다르게 요소가 존재하지 않아도 에러를 전지지 않기 때문에 요소가 DOM에 존재하지 않는 경우에 queryBy로 시작하는 함수를 사용하여 단언하는 것을 공식문서에서 권장하고 있다.

요소가 존재하지 않는지 단언할때만 queryBy 함수를 사용하는 것이 좋다.

# MSW로 API 모킹하기

> 브라우저의 서비스 워커를 사용하여 실제 API 요청을 가로채는 워킹 라이브러리
> 

테스트 환경이 구동되는 Node.js의 경우에는 서비스 워커가 없어서 내부적으로 XHR, Fetch와 같은 모듈에 요청을 가로채는 인터셉터를 구현하여 모킹한다. (브라우저와 Node 환경에서 모두 API 모킹을 가능하도록 도와준다.)

일관된 통합 테스트 실행을 위해 API 응답 모킹을 통해 원하는 데이터로 고정

만약 통합 테스트를 실행할 때마다 api를 호출하면 테스트 실행 시간이 오래 걸릴 수 있고
서버에 이슈가 있거나 데이터 변경이 생긴다면 테스트가 실패할 것이다.
이때 필요한 것이 바로 모킹이다.

## 구현 (MSW v1.X )

- 브라우저 모킹은 `rest` 사용
- Node 모킹은 `setUpServer` 사용

```jsx
export const server = setupServer(...handlers);

beforeAll(() => {
  server.listen(); // 서버 구동
});

afterEach(() => {
  server.resetHandlers(); // 런타임에 변경한 MSW 모킹을 초기화
  vi.clearAllMocks();
});

afterAll(() => {
  vi.resetAllMocks();
  server.close(); // 서버 종료
});
```

React 테스팅 라이브러리는 비동기 코드 처리를 위해 findBy 쿼리를 제공한다. (1초동안 요소가 있는지 기다리면서 조회한다.)

waitFor 을 사용해도 동일한 효과를 얻을 수 있다.

## Tanstack Query

- Tanstack Query에서는 기본적으로 잘못된 Query에 대해 세 번 재시도 하는데 테스트 할 때도 이런식으로 작동한다면 시간이 오래 걸리므로 재시도를 생략하도록 한다.

```jsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { render } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import React from 'react';
import { Toaster } from 'react-hot-toast';
import { MemoryRouter } from 'react-router-dom';

// https://tanstack.com/query/v4/docs/react/guides/testing
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      // ✅ turns retries off
      retry: false, 
    },
  },
  logger: {
    log: console.log,
    warn: console.warn,
    // ✅ no more errors on the console for tests
    error: process.env.NODE_ENV === 'test' ? () => {} : console.error,
  },
});

export default async (component, options = {}) => {
  const { routerProps } = options;
  const user = userEvent.setup();

  return {
    user,
    ...render(
      <QueryClientProvider client={queryClient}>
        <MemoryRouter {...routerProps}>{component}</MemoryRouter>
        <Toaster />
      </QueryClientProvider>,
    ),
  };
};
```

## use - 동적으로 API 모킹하기

이미 API 모킹 설정이 되어 있지만 테스트 실행시에 응답을 변경하여 API 모킹을 다시 해야하는 경우 사용하는 MSW에서 제공하는 함수 

이 함수를 사용하면 동적으로 API 응답을 변경할 수 있다.

여기서 주의할 점은 안정성 있는 테스트를 위해서는 server.use 로 변경한 API 모킹 응답을 원래대로 초기화 하는 것이 좋다.
이때 필요한 것이 바로 server.resetHandlers() 함수이다. 이 함수를 통해 동적으로 변경한 모킹을 초기화 할 수 있다.

1. use 함수를 사용하려면 msw 구동을 위해 setupServer를 사용하여 반환된 server 인스턴스를 사용해야 한다. 이때 초기의 구동을 위해 설정한 msw의 서버 인스턴스와 동일한 인스턴스를 사용해야 기존에 모킹된 api 응답을 정상적으로 변경할 수 있다.
2. server.use() 호출 후에 use 함수 내부에 다시 모킹하기를 원하는 API의 url과 응답 형태를 작성

```jsx
// setupTests.js
export const server = setupServer(..handlers);

// 실제 사용하는 곳
server.use(
	rest.get('/user', (_, res, ctx) => {
		return res(
			ctx.status(200),
			ctx.json({
				email: "abc@gmail.com",
				id: userId,
				name: "name"
			})
		)
	})
)

```
