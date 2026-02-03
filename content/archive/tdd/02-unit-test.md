---
title: "단위 테스트란?"
date: "2024-01-26"
category: "tdd"
---

# 단위 테스트

## 단위 테스트란?

앱에서 테스트 가능한 가장 작은 소프트웨어를 실행해 예상대로 동작하는지 확인하는 테스트

⇒ 앱에서 테스트 가능한 단일 함수 또는 단일 컴포넌트를 실행해 상태 또는 UI, 행위를 검증하는 테스트

공통 컴포넌트(버튼, 인풋, 캐러샐, 아코디언 등)를 아토믹 컴포넌트라고 부르기도 한다.

단위 테스트는 앱의 전반적인 기능이 비즈니스 요구사항에 맞게 동작하는지 보장할 수 없고 여러 모듈이 조합되었을 때 발생하는 이슈는 찾을 수 없다. 따라서 단위 테스트에서 검증하지 못하는 부분을 통합테스트, E2E 테스트, 시각적 테스트 등 다양한 테스트로 보강해야 한다.

## 단위 테스트의 테스트 작성 패턴

### Arrange-Act-Assert (AAA) 패턴

- Arrange : 테스트를 위한 환경 만들기
- Act : 테스트 할 동작 발생
- Assert : 올바른 동작이 실행 되었는지 검증하거나 변경사항 검증하기

### 테스트 구현

1. Arrange - 컴포넌트 렌더링

React 컴포넌트 렌더링을 위해  Testing Library에서 제공하는 render API를 사용한다

render API를 호출해 테스트 환경의 jsDOM에 리엑트 컴포넌트가 렌더링 된 DOM 구조가 반영된다.

jsDOM: Node.js에서 사용하기 위해 많은 웹 표준을 순수 자바스크립트로 구현한 것

```jsx
it('className Prop으로 설정한 css class가 적용된다', async () => {
  // render API 호출
  await render(<TextField className="my-class"/>)
});
```

1. Assert

className이란 내부 prop이나 state 값을 검증하는 것이 아니다.

렌더링되는 DOM 구조가 올바르게 변경되었는지 확인하는 것이다.

```jsx
it('className Prop으로 설정한 css class가 적용된다', async () => {
  // render API 호출
  await render(<TextField className="my-class"/>)

	// Assert
	expect(screen.getByPlaceholderText('텍스트를 입력해 주세요.').toHaveClass('my-class'))
});
```

## 테스트 작성

### it & test

> 반드시 기대 결과에 대해 자세히 작성해 확실하게 어떤 테스트를 수행하는 지 알려야 한다.
> 

it과 test 는 같은 기능을 한다.

- it(’should ~~~’, () ⇒ {})
- test(’if ~~~’, () ⇒ {})

### describe

> 테스트를 그룹화 한다
> 
- describe(’그룹명’, () ⇒ {})

describe 안에서만 따로 함수를 정의해서 사용하는 경우도 있다.

## React Testing Library 쿼리

`screen.debug()` : 해당 컴포넌트의 JSDOM이 어떤 식으로 렌더링 되는지 코드로 보여줌

## Matcher

> 기대되는 결과를 확인하기 위해 사용되는 API의 집합
> 
- `expect().toHaveClass(’클래스명’)` : 특정 클래스가 적용되었는지 확인
- `expect().toBeInTheDocument()` : 특정 엘리먼트가 JSDOM에 존재하는지 확인
- `expect(스파이함수).toHaveBeenCalled(횟수)` : 특정 함수가 몇 번 호출되었는지 확인
- `expect(스파이함수).toBeChecked()` : 라디오나 체크박스의 체크 여부 확인
- `expect(screen.getByText('text')).toBeIn()` : DOM에 존재하는지 확인 (getBy, findBy 사용)

[CRLF 에러](https://www.notion.so/CRLF-3d583adb10e84135895896dc3084b1f2?pvs=21)

## Vite Test 설정

```jsx
// vite.config.js
export default defineConfig({
  plugins: [react(), eslint({ exclude: ['/virtual:/**', 'node_modules/**'] })],
  test: {
    globals: true, // vite test에서 제공하는 API를 별도의 import 없이 바로 사용 가능
    environment: 'jsdom', // jsdom 환경에서 test 구동
    setupFiles: './src/utils/test/setupTests.js', // test에 필요한 설정에 대한 파일
  },
  resolve: {
    alias: [{ find: '@', replacement: path.resolve(__dirname, 'src') }],
  },
});
```

[vitest 에러](https://www.notion.so/vitest-facf5ef5b54940d1b8cdc8fe0423a32b?pvs=21)

## 모든 테스트는 독립적으로 실행되어야 한다.

만약 A 테스트와 B 테스트를 실행하는데 A → B 순으로 실행하면 둘 다 통과하지만 B →  A 순으로 실행했을 때 통과하지 않는 테스트가 있다면 이것은 잘못된 테스트이다.

**테스트는 항상 순서에 상관없이 결과를 보장할 수 있어야 한다.**

- setup : 테스트를 실행하기 전 수행해야 하는 작업 (beforeEach beforeAll)
- teardown : 테스트를 실행한 뒤 수행해야 하는 작업 (afterEach afterAll)

### Setup

- beforeAll : 가장 먼저 실행 되고, 딱 한 번만 실행된다. 스코프 내에서 전역으로 공유할 환경이나 상태를 설정할 때 사용.
- beforeEach : 스코프에 따라 각 테스트마다 실행된다.

### Teardown

- afterAll : 모든 테스트의 실행이 완료된 후에 딱 한 번만 실행된다.
- afterEach : 테스트에 의해 생성된 상태를 초기화 하는데 사용

### 전역으로 Setup Teardown 설정

vite.config.js 파일에서 test 부분에 setupFiles를 설정하고 해당 파일에 작성해주면 된다.

또한 해당 파일에서 jest에서 제공하는 matcher 함수를 사용하기 위해 import 해온다.

```jsx
// my-class란 class가 항상 적용된 컴포넌트를 렌더링
beforeEach(async () => {
	await render(<TextField className="my-class" />);
})
```

setup과 teardown에서 전역 변수를 사용한 조건 처리는 독립성을 보장하지 못하고 신뢰성이 낮아지므로 지양하자.
