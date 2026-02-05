---
title: "Virtual DOM이란 무엇인가?"
date: "2025-09-08"
category: "React"
series: "deep-react"
description: "Virtual DOM이란 무엇인지 알아보고, 직접 React 코드를 분석해보자."
---

<img width="1130" height="628" alt="image" src="https://github.com/user-attachments/assets/35a89c71-663a-4a47-80c2-d318b40c9839" />


# Virtual DOM

React는 매 렌더링 때마다 새로운 Virtual DOM을 생성한다.

매번 DOM 전체를 새로 만드는 것은 매우 비효율적이기 때문에 React는 이전 Fiber 트리와 새 Fiber 트리를 비교해서 변경된 부분만 업데이트 하는 것이다.

그리고 이 과정을 Reconciliation 이라고 부른다.

그렇다면 Reconciliation 규칙이 존재할까?

1. 타입이 다르면 새로 만든다. (<div> → <span> 이면 div를 통째로 버리고 새 span을 생성)
2. 타입이 같으면 속성 비교 후 업데이트 한다. (<div className=”a”> → <div className=”b”> 이면 class만 바꿈)
3. 리스트는 key로 비교한다. (key가 같으면 바꾸지 않음. key가 다르면 새로 생성)

ReactChildFober.js 파일을 살펴보면 reconcileChildFibers 라는 함수도 존재한다.

```jsx
function reconcileChildFibers(returnFiber, currentFirstChild, newChild) {
  // case 1: 단일 element
  if (typeof newChild === 'object' && newChild !== null) {
    switch (newChild.$$typeof) {
      case REACT_ELEMENT_TYPE:
        return placeSingleChild(
          reconcileSingleElement(returnFiber, currentFirstChild, newChild)
        );
    }
  }

  // case 2: 배열일 경우 (children이 여러 개)
  if (isArray(newChild)) {
    return reconcileChildrenArray(returnFiber, currentFirstChild, newChild);
  }

  // case 3: 문자열, 숫자 (text node)
  if (typeof newChild === 'string' || typeof newChild === 'number') {
    return reconcileSingleTextNode(returnFiber, currentFirstChild, '' + newChild);
  }

  // 나머지: null → 삭제 처리
  return deleteRemainingChildren(returnFiber, currentFirstChild);
}
```

```jsx
function reconcileChildrenArray(returnFiber, currentFirstChild, newChildren) {
  // old fiber (이전 children)
  let oldFiber = currentFirstChild;
  // 새로운 children (배열)
  const newFiberArray = [];

  for (let i = 0; i < newChildren.length; i++) {
    const newChild = newChildren[i];

    if (oldFiber && sameTypeAndKey(oldFiber, newChild)) {
      // 재사용 가능 → update
      newFiberArray.push(useFiber(oldFiber, newChild.props));
    } else {
      // 재사용 불가 → 새로운 fiber 생성
      newFiberArray.push(createFiberFromElement(newChild));
    }

    oldFiber = oldFiber.sibling; // 다음 oldFiber 비교
  }

  // 남아있는 oldFiber 삭제
  deleteRemainingChildren(returnFiber, oldFiber);

  return newFiberArray;
}

```

```jsx
const root = ReactDOM.createRoot(
	document.getElementById('root') as HTMLElement,
);
root.render(
	<React.StrictMode>
		<App />
	</React.StrictMode>
)
```

[[React 까보기 시리즈] useState 가 코드로 정의된 곳은 어디일까? reconciler 까지 찾아가기!](https://www.youtube.com/watch?v=9OjyQ2rB0zg&list=PLpq56DBY9U2B6gAZIbiIami_cLBhpHYCA&index=4)
