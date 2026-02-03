---
title: "사이프레스"
date: "2024-02-24"
category: "tdd"
---
# Cypress

> 실제 웹 앱에서 다양한 테스트를 작성할 수 있는 오픈 소스 자동화 도구
> 

E2E 테스트는 `플레이라이트`, `사이프레스`, `셀레늄`과 같은 도구를 사용하여 작성할 수 있다.

OSI 모델의 네트워크 계층에서 웹 트래픽을 즉시 읽고 변경할 수 있기 때문에 네트워크 단에서 브라우저에 들어오는 요청도 변경할 수 있다. 즉, 브라우저 안과 밖에서 일어나는 상황을 모두 제어할 수 있기 때문에 일관된 환경에서 테스트 결과를 제공한다는 강점이 있다.

Cypress는 로컬에 브라우저가 없는 경우를 대비하여 자동으로 크리미움 기반의 Electron 브라우저를 함께 설치하여 테스트 실행에 문제가 없도록 한다.

### head 모드와 headless 모드

**head 모드**

> 브라우저 UI까지 모두 구동하여 시각적으로 확인할 수 있는 환경에서 테스트 실행
> 

`cypress open` 으로 실행

브라우저 UI까지 모두 구동

시각적으로 확인 가능하기 때문에 디버깅 시 많이 이용한다.

**headless 모드**

> UI없이 브라우저 엔진만을 명령어 인터페이스로 제어하여 테스트 실행
> 

`cypress run` 으로 실행

브라우저 UI 없이 브라우저 엔진을 명령어 인터페이스로 제어

구동 속도가 상대적으로 빨라 CI 또는 클라우드 환경에서 구동하기 적합하다.

### Cypress의 유용한 기능

- 편리하고 빠른 디버깅
- 크롬 브라우저에서 테스트 진행하기 때문에 크롬 개발자 도구 사용 가능
- Time Travel - 테스트 코드가 실행될 때 스냅샷 이미지를 생성하기 때문에 각 단계별 렌더링 확인 가능
- 과정을 비디오로 녹화하거나 스크린샷 파일로 저장 가능

### E2E 테스트에서 단위 테스트와 통합 테스트를 작성하는 이유

- **컴포넌트 일부가 아닌 앱 자체에 대한 기능이 존재하는지 검증하기 위해서**
단위 테스트와 통합 테스트는 일부 컴포넌트의 조합을 테스트 하기 때문에 앱이 구동 되었을 때도 동일하게 동작하는지 확인하기 위해서이다.
- **통합 테스트로 검증한 기능을 파악하는 노력을 해서 분리해야 한다면 DX에 큰 장점이 없기 때문**
워크 플로우가 클 수록 파악하는 데 많은 시간이 소요되기 때문이다.

### 테스트 코드 작성

Cypress 테스팅 라이브러리는 는 리액트 테스팅 라이브러리와 유사한 함수를 사용한다.

하지만 geyBy 쿼리가 없어서 findBy와 findAllBy를 사용해야 한다.

Cypress는 should 구문을 사용해 단언을 실행한다.

- cypress.config.js 파일에 baseUrl을 선언해야 한다
- `cy.visit(경로)` : BaseUrl을 기준으로 웹 페이지로 접속할 수 있도록 도와주는 함수

```jsx
beforeEach(() => {
	cy.visit('/login');
})

it('이메일을 입력하지 않고 로그인 버튼을 클릭할 경우 "이메일을 입력하세요" 메세지 노출', () => {
	cy.findByLabelText('로그인').click();

	cy.findByText('이메일을 입력하세요').should('exist');
})

it('잘못된 양식의 이메일을 입력한 뒤 로그인 버튼을 클릭할 경우 "이메일 양식이 올바르지 않습니다" 메세지 노출', () => {
	cy.findByLabelText('이메일').type('abcd@kkk.com');
	cy.findByLabelText('로그인').click();

	cy.findByText('이메일 양식이 올바르지 않습니다').should('exist');
})

it('회원 가입 클릭 시 회원 가입 페이지로 이동한다', () => {
	cy.findByText('회원가입').click();

	cy.url().should('eq', `${Cypress.env('baseUrl')}/register`);
})
```

Cypress의 get API는 요소 조회에만 사용할 수 있는 것이 아니라 특정 요소를 alias로 지정해두고 쉽게 접근하도록 할 수 있다는 장점이 있다.

```jsx
beforeEach(() => {
	cy.visit('/login');
	cy.findByLabelText('로그인').as('loginBtn'); // 로그인 버튼을 loginBtn이라는 alias로 선언
})

it('...', () => {
	cy.get('@loginBtn').click();
})

```

## Cypress의 체이닝

Cypress에서는 커맨드를 Promise 체이닝을 통해 관리하며 각 커맨드는 체인이 종료되거나 오류가 발생할 때 까지 멈춰있는다.

내부적으로 다음 커맨드의 대상을 subject란 객체를 통해 관리하는데 여기서 subject 객체는 테스트의 시작 지점 또는 대상이 되는 요소를 의미한다. subject 객체가 있기 때문에 체이닝 형태로 코드를 작성할 수 있는 것이다.

Cypress의 API는 내부적으로 만든 체이닝 구조에 의해 비동기로 동작하기 때문에 subject란 객체를 따로 변수에 담아서 활용하는 것은 불가능하다.

커맨드가 실행될 때 각 subject는 내부적으로 비동기 대기열에서 대기하다가 실행되기 때문에 get API나 테스팅 라이브러리의 쿼리 실행이 완료되는 타이밍을 맞추기 위해서는 subject 체이닝 형태로 연속해서 커맨드를 실행하거나 then() API를 사용해야 한다.

then() API를 사용하는 경우 JS에서 제공하는 then() 과 똑같이 생겼지만 기능은 다르기 때문에 혼동해서는 안된다. 만약 then() API 가 끝났을 때 반환하는 값이 없을 경우 이전에 반환된 값을 그대로 사용한다. then() API 내에서는 jQuery 객체로 매핑되기 때문에 jQuery 메소드를 사용하여 다양한 작업을 할 수 있다.

## Retry-ability

Cpress의 핵심 기능

타이밍 제어를 편리하게 할 수 있도록 재시도 기능

대상이 되는 요소들이 잠재적으로 업데이트 될 가능성이 있다고 판단하여 원하는 상황이 발생할 때까지 특정 시간 동안 재시도를 실행하는 것이다.

예를 들어, API 요청이 아직 오지 않았을 경우를 대비하여 API 요청이 모두 완료되어 특정 요소가 렌더링 될 때 까지 계속해서 재시도 하는 것이다.

기본적으로 4초동안 커맨드를 재시도하며 커맨드의 옵션으로 시간을 변경할 수 있다.

```jsx
cy.findByLabelText('이메일', {timeout: 10000})
```

- Query : 전체 체이닝 로직을 재시도하며 실행 (get, Cypress 테스팅 라이브러리, find 등)
- Assertion : 단언을 수행하는 특별한 쿼리 유형 (should 등)
- Non-Query : 재시도를 하지 않으며 단 한 번만 실행되는 커맨드 (visit, click, then 등)

# 커스텀 커맨드와 커스텀 쿼리

사용자가 원하는 로직을 커스텀 커맨드와 커스텀 쿼리로 정의하여 사용할 수 있도록 기능을 제공한다.

## 커스텀 쿼리

Retry-ability 지원

동기로 동작하고 subject 결과를 받아 내부적으로 체이닝 코드를 재시도

`Cypress.Commands.addQuery()`와 `Cypress.Commands.overwriteQuery()`로 정의

※ 커스텀 쿼리는 재시도할 로직을 함수 형태로 작성하여 반환해야 한다.

```jsx
Cypress.Commands.addQuery('', () => {
	const getFn = cy.now('get', '[data-testid="cart-icon"]');

	//inner funciton 형태로 반환
	return subject => {
		const btn = getFn(subject);
		return btn;
	}
})
```

## 커스텀 커맨드

Retry-ability 미지원

비동기로 동작하고 특별한 설정이 없다면 subject를 이어 받지 않는다

내부적으로 재시도를 실행하지 않고 단 한 번만 실행한다

`Cypress.Commands.add()`와 `Cypress.Commands.overwrite()`로 정의

정말 특별한 일이 있지 않은 이상 `overwriteQuery`와 `overwrite` API는 거의 사용할 일이 없다.

### cy.session

Cypress는 여러 테스트에서 공유할 수 있는 쿠키, 로컬 스토리지, 세션 스토리지에 있는 정보들을 캐싱하고 복원하기 위해 cy.session 이라는 API를 제공한다.

이것을 사용하면 매 테스트에 반복적으로 로그인을 할 때 소요되는 시간을 많이 줄일 수 있다.

```jsx
cy.session(username, () => {
	cy.visit('/login');

	cy.findByLabelText('이메일').type(username);
	cy.findByLabelText('비밀번호').type(password);
	cy.findByLabelText('로그인').click();

	// 캐싱하기 전에 로그인 프로세스가 완료되도록 보장하기 위해 추가
	cy.location('pathname').should('eq', '/');
})
```

## 서버 요청 가로채기

Cypress에서 자체적으로 서버 요청을 가로챌 수 있는 모킹 기능을 지원한다.

intercept API를 사용하여 네트워크 요청과 응답을 조작할 수 있고 이에 대한 기록도 해두기 때문에 stubbing과 spying을 함께 진행한다고 표현한다.

- stubbing : 특정 네트워크 요청에 대해 미리 정해진 응답을 반환하는 것
- spying : 요청과 응답에 대한 호출 정보를 기록해두는 것

```jsx
// 요청 성공
cy.intercept('POST', 'http://localhost:3000/user', {statusCode: 200})

// 요청 실패
cy.intercept('POST', 'http://localhost:3000/user', {statusCode: 401})
```
