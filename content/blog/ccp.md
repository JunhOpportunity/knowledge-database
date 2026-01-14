---
title: "컴파운드 컴포넌트 적용해보기"
date: "2024-02-15"
category: "디자인패턴"
---

# Compound Component
React CCP(Compound Component Pattern) 적용해보기

## Compound Component란?
> 서로 연관된 컴포넌트를 한 파일에 묶은 후에 최상위 컴포넌트에 정적 프로토타입으로 지정하는 방식
> 

## Compound Component를 사용하는 이유

CCP는 깔끔한 코드 작성에 효과적이기 때문입니다.

그러나 클린 코드를 위해서만 이 패턴을 사용하는 것은 아닙니다.

### 1. 재사용성

하위 컴포넌트들을 결합하여 하나의 상위 컴포넌트로 만들기 때문에 각각의 하위 컴포넌트들을 재사용 할 수 있을 뿐만 아니라 상위 컴포넌트도 재사용이 가능해집니다.

### 2. 유연성

하위 컴포넌트를 각각 추가하거나 제외시킬 수 있기 때문에 컴포넌트의 형태와 동작을 쉽게 변경할 수 있습니다.

### 3. 구조의 명확성

상위 컴포넌트와 하위 컴포넌트 간의 관계가 명확해지므로 코드의 가독성을 높이고 유지보수가 용이해집니다.

### 4. 추상화

상위 컴포넌트를 통해 컴포넌트의 내부 동작을 추상화하고 숨길 수 있습니다.

## 사용 방법

CCP는 사용 방법이 굉장히 간단합니다.

상위 컴포넌트에서 필요한 하위 컴포넌트들을 상위 컴포넌트 내부에 모아두고 필요할 때 사용하는 것이 전부입니다.

1. 필요한 하위 컴포넌트들을 모두 import 하기
2. 컴포넌트를 호출할 때 사용할 이름을 정하고 해당 컴포넌트를 할당하기
`상위컴포넌트.호출이름 = 하위컴포넌트;` 
3. 필요한 하위 컴포넌트들을 호출해 사용하기

```jsx
import AaComponent from "./AaComponent";
import BbComponent from "./BbComponent" ;
import CcComponent from "./CcComponent ";

export default function CompoundComponent({children}: {children: ReactNode}) {
  return (
    <div>{children}</div>
  );
}

CompoundComponent.Aa = AaComponent;
CompoundComponent.Bb = BbComponent;
CompoundComponent.Cc = CcComponent;
```

```jsx

import CompoundComponent from "./component/CompoundComponent"

function App() {
  return (
    <CompoundComponent>
      <CompoundComponent.Aa />
      <CompoundComponent.Bb />
      <CompoundComponent.Cc />
    </CompoundComponent>
  );
}
```

이제 `CompoundComponent` 가 필요한 곳이라면 하위 컴포넌트들을 일일이 import 하지 않고 상위 컴포넌트 하나만 import 하더라도 모두 사용할 수 있습니다.

또한 `CompoundComponent` 를 다른 곳에서 재사용 할 때 동일한 형태가 아니라 하위 컴포넌트인 `Aa` , `Bb` , `Cc`  컴포넌트 중 일부만 사용하고 싶다면 간단하게 해당 컴포넌트만 추가하지 않으면 됩니다.

```jsx
// 하위 컴포넌트인 Bb 컴포넌트는 사용하고 싶지 않은 경우
import CompoundComponent from "./component/CompoundComponent"

function Main() {
  return (
    <CompoundComponent>
      <CompoundComponent.Aa />
      <CompoundComponent.Cc />
    </CompoundComponent>
  );
}
```
어떤가요? 더 깔끔하게 작성되었다고 느껴지시나요?

좀 더 깔끔하고 명확한 구조를 갖춘 코드가 완성되었습니다.

### ② CCP를 활용해 컴포넌트 재사용하기

![image](https://github.com/JunhOpportunity/compound-component/assets/89464762/b94d4a78-c13f-4ea6-9ba2-1b88f13ab6e5)


그렇다면 이번에는 CCP를 조금 더 활용해보기 위해 컴포넌트 재사용 방법에 대해 알아봅시다.

만약, 해당 유저의 상세 페이지로 이동하는 `평판 작성하러 가기` 버튼을 눌러서 상세 페이지로 이동했을 때 맨 위에 `UserCard` 의 버튼만 제거한 채로 같은 유저 카드를 보여주고 싶다면 어떻게 해야할까요?

하위 컴포넌트인 `<UserCard.Navigate />` 만 제거하면 바로 구현이 가능합니다.

```jsx
function App() {
  return (
    <UserCard user={user}>
      <UserCard.ProfileImage />
      <UserCard.Name />
      <UserCard.Job />
      <UserCard.Intro />
    </UserCard>
  );
}
```

따라서 컴포넌트를 약간 변경해서 재사용하고 싶은 경우에도 CCP를 사용하면 효율적이라는 것을 알게 되었습니다.

### ③ 무신사의 상품 카드를 CCP의 관점에서 바라보기

위 예시들을 기반으로 유명 사이트의 상품 카드를 CCP의 관점으로 바라보는 것도 가능합니다.

무신사의 스타일 스냅의 코디맵 카테고리와 브랜드 룩북 카테고리를 예시로 들어보겠습니다.

![image](https://github.com/JunhOpportunity/compound-component/assets/89464762/fc51717f-43c0-4459-9074-714c79c06db8)


기본적인 카드의 형태는 동일하지만 상품 이미지 하단에 있는 부분인 하위 컴포넌트는 약간 다른 모습이라는 것을 확인할 수 있습니다.

이 구조를 간단하게 시각적으로 나타내볼까요?

![image](https://github.com/JunhOpportunity/compound-component/assets/89464762/14702156-f177-44d4-80ed-d435e384aea3)


A는 공통적으로 사용되는 상위 컴포넌트, B와 C에서 다른 부분이 하위 컴포넌트이니 하위 컴포넌트만 다르게 호출해주면 바로 구현이 가능할 것 같네요.

CCP로 작성해보면 재사용이 가능하기 때문에 굉장히 간단하게 작성할 수 있다는 것을 알 수 있습니다.

```jsx
// B
function BrandLookBook() {
  return (
    <ProductCard product={product}>
      <ProductCard.snapImage />
      <ProductCard.snapInfo>
				<ProductCard.goodsInfo>
			</ProductCard.snapInfo>
    </ProductCard>
  );
}
```

```jsx
// C
function CoordiMap() {
  return (
    <ProductCard product={product}>
      <ProductCard.snapImage />
      <ProductCard.snapInfo>
				<ProductCard.goodsImage/>
				<ProductCard.goodsInfo>
			</ProductCard.snapInfo>
    </ProductCard>
  );
}
```

만약 CCP를 사용하지 않았다면 BrandLookBook 컴포넌트와 CoordiMap 컴포넌트 내에 일일이 하위 컴포넌트들을 가져와서 사용하거나 유사한 컴포넌트를 여러 개 작성하며 많은 비용이 낭비되었을 수 있습니다.

이외에도 정말 다양한 방법으로 CCP를 활용할 수 있으니 또 어떤 곳에 CCP를 적용할 수 있을 지 한 번 생각해 볼 시간을 드리기 위해 카드 형태의 컴포넌트에 대한 예시만 작성해보았습니다.

CCP는 이렇게 활용할 수 있는 곳도 많고 장점도 많지만 단점도 존재합니다.

## 단점

### 복잡성

여러 컴포넌트를 함께 사용하기 때문에 초기 설정이 복잡합니다.

따라서 빠르게 웹앱을 구축하고 배포해야 하는 상황이라면 CCP를 적용하지 않는 것이 적합할 수 있습니다.

### 학습 곡선

CCP에 대해 학습하고 적용하는 것은 어렵지 않고 오랜 기간이 걸리지 않습니다.

하지만 컴포넌트를 중점적으로 사용하는 React에 대한 이해가 부족하다면 CCP를 적용하기 힘들 수 있고 React 사용에 능숙하다고 해도 아무런 학습 없이 바로 CCP를 적용할 수는 없습니다.

따라서 컴포넌트를 중점적으로 사용하지 않는 프레임워크만 다뤄보았거나 CCP를 학습할 시간조차 없는 아주 긴박한 상황이라면 다른 방법을 모색해 보아야 합니다.

### 너무나도 많은 유연성

CCP는 쉽게 구조를 변경할 수 있어서 유연성이 좋은 편이지만 이것이 처음 CCP를 적용한 개발자의 의도가 아닌 전혀 다른 방식으로 컴포넌트를 사용하는 상황이 발생할 수 있기 때문입니다.

이로 인해 예기치 않은 동작이 발생할 수 있으므로 협력하는 개발자들과 CCP 사용에 대한 규칙을 정하는 것이 필요할 수 있습니다.

### 테스트의 어려움

CCP는 단일 컴포넌트보다 많은 컴포넌트로 구성되어 있기 때문에 테스트를 진행할 때 까다로울 수 있습니다.

이렇게 Compound Component Pattern 에 대해서 알아보았습니다.

CCP는 정말 다양한 곳에 활용할 수 있기 때문에 제가 작성한 예시에만 한정해서 적용해보지 마시고 여러 코드에 적용해보시면서 확실하게 이해해보시기 바랍니다.

그런 다음 예전에 작성해둔 코드들 중 CCP 적용에 적합한 코드가 있다면 한 번 적용시켜 보면서 한층 더 깔끔한 코드를 보며 성장의 기쁨을 느껴보시기 바랍니다.

## 참고 자료

- https://www.patterns.dev/react/compound-pattern/
- [원티드 프리온보딩 12월](https://www.wanted.co.kr/events/pre_challenge_fe_16) - [오종택](https://github.com/saengmotmi) 멘토 강의

[https://velog.io/@junhopportunity/React-Compound-패턴-적용하기](https://velog.io/@junhopportunity/React-Compound-%ED%8C%A8%ED%84%B4-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)
