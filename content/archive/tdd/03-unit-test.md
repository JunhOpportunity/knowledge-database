---
title: "단위 테스트 작성과 모킹"
date: "2024-01-24"
category: "tdd"
---
# 단위 테스트 작성하기

## 단위 테스트를 작성하지 않는 컴포넌트

- 별도의 상태 변경이나 비즈니스 로직 없이 UI만 렌더링하는 컴포넌트 (스토리북과 같은 도구를 통해 검증)
- 로직은 있지만 복잡하지 않은 컴포넌트 (상위 컴포넌트의 통합 테스트로 검증)

## 단위 테스트를 작성하기 적합한 것들

- 유틸 함수 (src/utils/common.js)
- 리액트 훅
- 여러 컴포넌트가 조합되어있지만 하나의 기능만 있는 단순한 컴포넌트

### 만약 외부 모듈에 대한 의존성이 존재하는 경우

> 단위 테스트에서 외부 모듈에 대한 검증은 완전히 분리하고 모듈의 특정 기능을 제대로 호출하는지만 검증한다. (이때 모듈을 제대로 호출하는지 확인하기 위해 모킹을 사용한다.)
> 

단위 테스트를 작성하려고 하는 컴포넌트에 useNavigate를 사용하고 있어서 의존성이 존재한다면, 이 동작도 테스트를 해야할까? 그렇지 않다. 이미 useNavigate는 react-router 에서 테스트가 이루어 졌으므로 추가적인 검증은 불필요하다고 판단할 수 있다.

## 모킹

> 실제 모듈을 모킹한 모듈로 대체하여 테스트를 실행한다
> 
1. `vi.mock()` 를 사용한다
2. `vi.mock('모킹할 모듈 이름', () => {함수 작성})`
3. 일부 모듈에 대해서만 모킹을 작성하고 나머지는 그대로 가져오고 싶다면 `vi.importActual(’모킹할 모듈 이름’)`
4. 모킹하고 싶은 특정 모듈만 `vi.fn()`을 사용해 스파이 함수로 호출한다.

```jsx
const navigateFn = vi.fn();

vi.mock('react-router-dom', async () => {
  const original = await vi.importActual('react-router-dom');

  return { ...original, UserActivate: () => navigateFn };
});

it('"홈으로 가기" 링크를 클릭할경우 "/"경로로 navigate함수가 호출된다', async () => {
  const {user} = await render(<EmptyNotice/>);

  await user.click(screen.getByText('홈으로 가기'));

  expect(navigateFn).toHaveBeenNthCalledWith(1, '/');
});
```

1. 테스트가 끝난 후에 teardown을 통해 다른 테스트 실행시 영향을 주지 않도록 해주어야 한다.
`restoreAllMocks` 도 있으니 한 번 찾아보자.

```jsx
afterEach(() => {
  server.resetHandlers();
  vi.clearAllMocks();
});

afterAll(() => {
  vi.resetAllMocks();
  server.close();
});
```

### 모킹의 장단점

> 모킹은 테스트를 독립적으로 분리하여 효과적으로 검증할 수 있게 도와주지만
지나치게 많은 모킹은 테스트의 신뢰성을 저하시키며 변경에 취약해지게 된다.
> 
- 외부 모듈과 의존성을 제외하고 필요한 부분만 검증이 가능하다
- 실제 모듈과 완전히 동일한 모의 객체를 구현하는 것은 큰 비용이 발생한다
- 모의 객체를 남용하는 것은 테스트 신뢰성을 낮춘다

## 리액트 훅 테스트

- 리액트 훅은 반드시 리액트 컴포넌트 내에서만 호출되어야 정상적으로 실행된다
⇒ 다행히도 React-Testing-Library 에서는 리액트 컴포넌트 없이도 리액트 훅이 실행되도록 하는 `renderHook` 을 제공한다.

### renderHook

- result : 훅을 호출하여 얻은 결과 값을 반환 / result.current 값의 참조를 통해 최신 상태를 추적할 수 있다.
- rerender : 훅을 원하는 인자와 함께 새로 호출하여 상태를 갱신

```jsx
import { renderHook, act } from '@testing-library/react';
import useConfirmModal from './useConfirmModal';

it('호출 시 initialValue 인자를 지정하지 않는 경우 isModalOpened 상태가 false로 설정된다.', () => {
  const {result, render} = renderHook(useConfirmModal);

  expect(result.current.isModalOpened).toBe(false);
});
```

```jsx
it('호출 시 initialValue 인자를 boolean 값으로 지정하는 경우 해당 값으로 isModalOpened 상태가 설정된다.', () => {
  const { result } = renderHook(() => useConfirmModal(true));

  expect(result.current.isModalOpened).toBe(true);
});
```

### act 함수

> 컴포넌트를 렌더링 한 뒤 업데이트 하는 코드의 결과를 검증하고 싶을 때 사용
> 

상호작용(렌더링, 이펙트 등)을 함께 그룹화 하고 실행해 렌더링과 업데이트가 실제 앱이 동작하는 것과 유사한 방식으로 동작하도록 도와준다.
⇒ 테스트 환경에서 act를 사용하면 가상의 돔(jsdom)에 제대로 반영되었다는 가정하에 테스트가 가능해진다.

테스트 환경에서 컴포넌트 렌더링 결과를 jsdom에 반영하기 위해서는 반드시 act 함수를 호출해야 한다.

React Testing Library를 사용하면서 act 함수를 호출하지 않은 이유는 내부적으로 act 함수를 호출하여 상태를 반영하기 때문이다.

따라서 React Testing Library를 사용하지 않고 직접 상태를 변경하는 경우에는 반드시 act 함수를 사용해야 한다.

```jsx
// ❌ act 함수 적용 전 (상태 변경 반영 되지 않음) -> 테스트 실패
it('훅의 toggleIsModalOpened()를 호출하면 isModalOpened 상태가 toggle된다.', () => {
  const {result} = renderHook(useConfirmModal);

  result.current.toggleIsModalOpened();

  expect(result.current.isModalOpened).toBe(true);
});

// ⭕ act 함수 적용 후 (상태 변경 반영 됨) -> 테스트 성공
it('훅의 toggleIsModalOpened()를 호출하면 isModalOpened 상태가 toggle된다.', () => {
  const { result } = renderHook(useConfirmModal);

  act(() => {
    result.current.toggleIsModalOpened();
  });

  expect(result.current.isModalOpened).toBe(true);
});
```

## 비동기 타이머 테스트

테스트 코드는 비동기 타이머와 무관하게 동기적으로 실행된다.
이를 해결하기 위해 타이머를 모킹하면 된다.

타이머를 모킹했으므로 반드시 모킹 초기화를 해주어야 한다.

- `vi.useFakeTimers()` : 타이머 모킹
- `vi.useRealTimers()` : 타이머 모킹 초기화
- `vi.advanceTimersByTime(시간)` : 특정 시간 만큼 흐른 것 처럼 제어
- `vi.setSystemTime(new Date(시간))` : 테스트가 구동되는 현재 시간을 정의

```jsx
describe('debounce', () => {
  beforeEach(() => {
    vi.useFakeTimers();
  });
	
	afterEach(() => {
		vi.useRealTimers();
	})

  it('특정 시간이 지난 후 함수가 호출된다.', () => {
    vi.useFakeTimers();

    const spy = vi.fn();
    const debouncedFn = debounce(spy, 300);
    debouncedFn();

    vi.advanceTimersByTime(300);

    expect(spy).toHaveBeenCalled();
  });
})
```
