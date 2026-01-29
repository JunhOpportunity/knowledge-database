---
title: "아토믹 패턴"
date: "2024-02-11"
category: "디자인패턴"
series: "디자인패턴"
---
<img width="1037" height="576" alt="image" src="https://github.com/user-attachments/assets/84b9e388-5cbf-4276-beb3-5fd7c9f237c4" />


# 아토믹 디자인 패턴

> 화학적 관점에서 영감을 얻은 디자인 시스템.
> 

Atom → Molecule → Organism → Template → Page

## Atom

> 더 이상 분해할 수 없는 기본 컴포넌트
> 

<img width="539" height="402" alt="image" src="https://github.com/user-attachments/assets/f98519f5-ab5b-47fc-8de4-9db35542780d" />

label, input, button과 같이 기본 HTML element 태그 혹은 글꼴, 애니메이션, 컬러 팔레트, 레이아웃과 같이 추상적인 요소도 포함될 수 있다.

단일 컴포넌트로 사용하기에는 어려울 때가 많으므로 다른 Atom 들과 결합한 Molecule 이나 Organism 단위에서 테스트를 진행하는 것이 좋다.

## Molecule

> 여러 개의 atom을 결합하여 자신의 고유한 특성을 가진 컴포넌트
> 

<img width="540" height="159" alt="image" src="https://github.com/user-attachments/assets/40ef883c-8e81-4ab9-a86e-45e71aa37932" />

Molecule 은 반드시 한 가지 일만 해야 한다. SRP(SOLID 원칙 중 한 가지) 원칙을 지키며 재사용성과 UI에서의 일관성, 테스트하기 쉬운 조건이라는 이점을 가진다.

## Organism

> 서비스에서 표현될 수 있는 명확한 영역과 특정 컨텍스트를 가진 컴포넌트
> 

<img width="548" height="67" alt="image" src="https://github.com/user-attachments/assets/9beb216c-7c97-44df-89ed-035e802419cd" />

Atom 과 Molecule 이 결합된 구조이고, 이들이 결합되었기 때문에 재사용성은 상대적으로 낮아진다.

## Template

> page를 만들 수 있도록 여러 개의 organism, molecule로 구성된 컴포넌트
> 

<img width="546" height="405" alt="image" src="https://github.com/user-attachments/assets/08e0e706-ce6c-4630-92dc-ff29e5467280" />

실제 컴포넌트를 레이아웃에 배치하고 구조를 잡는 와이어 프레임이라고 할 수 있다.

## Page

> 유저가 볼 수 있는 실제 콘텐츠를 담은 컴포넌트
> 

<img width="547" height="408" alt="image" src="https://github.com/user-attachments/assets/4fac3493-9cee-469b-a3ba-b74572356280" />

Template의 인스턴스라고 할 수 있다.

## 주의 사항

- 아토믹 디자인의 각 단계는 선형(linear) 프로세스가 아니다. 즉,  1. atom, 2. molecule, 3. organism .. 이렇게 스텝 별로 나아가는 방법론이 아니다.
- 각 단위를 어떻게 나눠야 할 지 헷갈릴 수 있다. 따라서 팀원과의 합의가 필요하다
