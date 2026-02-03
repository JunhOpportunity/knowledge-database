---
title: "React 테스팅 라이브러리"
date: "2024-01-30"
category: "tdd"
---

## React Testing Library

> UI 컴포넌트를 사용자가 사용하는 방식으로 테스트 하자는 것이 핵심 철학이다
> 

DOM 노드를 쿼리(조회)하고 사용자와 비슷한 방식으로 이벤트를 발생시킨다.

사용자가 동작하는 것 위주로 우선순위가 결정된다. (사용자의 동작과 상관 없는 class name을 직접 찾아서 동작시키는 것 같은 경우에는 가장 마지막에 이루어진다.)

```jsx
it('텍스트를 입력하면 onChange prop으로 등록한 함수가 호출된다.', async () => {
	const spy = vi.fn();
  const {user} = await render(<TextField onChange={spy}/>);

  const textInput = screen.getByPlaceholderText('텍스트를 입력해 주세요.');

  await user.type(textInput, 'test');

	expect(spy).toHaveBeenCalledWith('test')
})
```

- `user.type` 함수를 사용해 사용자가 직접 입력하는 것처럼 시뮬레이션 할 수 있다.
- `vi.fn()` : 스파이 함수, 테스트 코드에서 특정 함수가 호출되었는지, 함수의 인자로 어떤 것이 넘어왔는지 어떤 값을 반환하는지 등 다양한 값 저장. 보통 콜백 함수나 이벤트 헨들러가 올바르게 호출되었는지 확인할 때 사용

```jsx
it('엔터키를 입력하면 onEnter prop으로 등록한 함수가 호출된다.', async () => {
	const spy = vi.fn();
  const {user} = await render(<TextField onEnter={spy}/>);

  const textInput = screen.getByPlaceholderText('텍스트를 입력해 주세요.');

  await user.type(textInput, 'test{Enter}');

	expect(spy).toHaveBeenCalledWith('test');
})
```

- `user.type` 함수에서 인자로 ‘test{Enter}` 를 입력하면 엔터가 입력된다.

```jsx
it('포커스가 활성화되면 onFocus prop으로 등록한 함수가 호출된다.', async () => {
	// 포커스 활성화
	// - 탭 키로 인풋 요소를 포커스 이동
	// - 인풋 요소를 클릭했을 때
	// - textInput.focus()로 직접 발생
	const spy = vi.fn();
  const {user} = await render(<TextField onFocus={spy}/>);

  const textInput = screen.getByPlaceholderText('텍스트를 입력해 주세요.');

  await user.click(textInput);

	expect(spy).toHaveCalled();
})
```

- `user.click` 함수로 인풋 요소 클릭 (click과 연관된 것들 모두 가능. 포커스, 마우스 다운, 마우스 업 등)
- 스파이 함수가 호출되었는지 확인하기 위해서는 toHaveCalled() 사용

```jsx
it('포커스가 활성화되면 border 스타일이 추가된다.', async () => {
  const {user} = await render(<TextField onFocus={spy}/>);

  const textInput = screen.getByPlaceholderText('텍스트를 입력해 주세요.');

  await user.click(textInput);

	expect(textInput).toHaveStyle({borderWidth:2, borderColor: 'rgb(25, 118, 210)'})
```
