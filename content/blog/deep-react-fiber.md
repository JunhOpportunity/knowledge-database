---
title: "Fiber란 무엇인가?"
date: "2025-09-01"
category: "React"
series: "deep-react"
description: "Fiber란 무엇인지에 대해 알아보자."
---

<img width="1263" height="699" alt="image" src="https://github.com/user-attachments/assets/f01f73d0-13d4-4d4e-afa6-a040ac03b39c" />

React가 내부에서 각 컴포넌트를 표현하는 자료구조

Fiber를 사용하는 이유? React 15 까지는 재귀 호출 기반으로 렌더링을 진행했다.
그런데 컴포넌트 트리가 크면 렌더링 중간에 멈출 수 없기 때문에 브라우저가 작동하지 않는 문제가 발생.
따라서 React 16부터 Fiber를 도입하였다.
이로써 작업을 잘게 쪼개고 우선순위를 부여해서 중단했다가 다시 이어갈 수 있게 구현되었음.

React 엘리먼트 렌더링 구현 과정

루트 생성 → React 엘리먼트 

- React 엘리먼트는 루트를 통해 렌더링해야 한다.
**const** root = ReactDOM.createRoot(document.getElementById('root'));

React DOM은 업데이트 시 변경된 부분만 native DOM에 반영하도록 되어 있다.
⇒ 내부에서 비교 알고리즘을 통해 변경된 부분만 집어내 반영

native DOM은 무엇인가? 브라우저가 제공하는 실제 DOM 트리
조작할 때마다 브라우저 렌더링 엔진 개입 (리플로우, 리페인트 발생)

Virtual DOM은 무엇인가? JS 객체로 관리하는 가상 DOM 트리
브라우저의 DOM을 직접 건드리지 않고 메모리 안에서만 존재
만약 새로운 렌더링 요청이 오면 새로운 Virtual DOM을 생성하고 이전 Virtual DOM과 비교해서 변경된 부분만 Native DOM에 반영한다. (이 과정이 **Reconciliation (재조정)**)

React는 기본적으로 선언형 UI를 지향한다.
여기서 선언형이란? state와 props를 바꾸기만 하면 React가 알아서 Reconciliation 을 거쳐 업데이트를 처리하는 것을 말한다.

Ref는 직접 DOM을 조작할 때 사용하는데, 만약 Ref가 남용되면 Virtual DOM과 실제 DOM이 불일치할 수 있기 때문이다.

즉, Ref는 React의 선언적 렌더링 흐름을 우회하기 때문에 직접 DOM을 조작해 업데이트 해서 Virtual DOM은 변경사항을 인지하지 못하고 유지하게 된다. 이로써 다음 렌더링에서 Ref를 통해 업데이트된 값이 아닌 기존의 Virtual DOM의 값으로 덮어쓰게 된다.

JS 엔진은 단일 스레드이다. 사용자의 상호작용, DOM 이벤트, Ajax 응답 등 발생할 수 있는 모든 로직을 순차적으로 실행시킨다.

### React의 동작 단계

- Render 단계: JSX 선언 또는 `React.createElement()`를 통해 일반 객체인 Reat 엘리먼트를 생성한다.
- Reconcile 단계: 이전에 렌더링된 실제 DOM 트리와 새로 렌더링할 React 엘리먼트를 비교하여 변경점을 적용한다.
- Commit 단계: 새로운 DOM 엘리먼트를 브라우저 뷰에 커밋한다.

- Update 단계: props, state 변경 시 해당 컴포넌트와 하위 컴포넌트에 대해 위 과정을 반복한다.

### 참고 자료
- https://d2.naver.com/helloworld/2690975
