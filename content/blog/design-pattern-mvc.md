---
title: "MVC 패턴"
date: "2023-12-04"
category: "디자인패턴"
series: "디자인패턴"
---

<img width="1036" height="578" alt="image" src="https://github.com/user-attachments/assets/71c34693-ef08-4b60-8021-1782c538c124" />


# MVC 패턴

<img width="539" height="412" alt="image" src="https://github.com/user-attachments/assets/e93bdb0e-ea88-48eb-88cd-332d76af3af8" />

사용자가 검색을 하면 Controller 에서 Model에게 검색에 대한 데이터를 요청한다. 데이터가 Controller에 전달되면 Controller는 데이터를 View에게 전달하고 View에서 사용자가 보는 UI에 검색 결과 데이터를 집어 넣어서 웹 페이지를 보여준다.

## Model

> 데이터와 관련된 부분
> 

## View

> 사용자에게 보여지는 부분
> 

## Controller

> Model과 View를 이어주는 부분
> 

## MVC 패턴의 규칙

### 1. Model은 Cotroller와 View에 의존하지 않아야 한다.

Model 내부에 Controller와 View에 관련된 코드가 존재해서는 안된다.
즉, Model 내부에서 Controller와 View를 import 하면 안된다는 것이다.

### 2. View는 Model에만 의존해야 하고 Controller에는 의존하면 안된다.

View 내부에는 Model에 대한 코드만 존재해야 한다. Controller에 대한 코드가 존재해서는 안된다.

### 3. View가 Model로부터 데이터를 받을 때는 사용자마다 다르게 보여주어야 하는 데이터에 대해서만 받아야 한다.

모든 사용자에게 공통적으로 보여져야 하는 부분에 대해서는 Model로부터 데이터를 받아서는 안된다. View가 자체적으로 가지고 있어야 하는 정보들이다. (예를 들면 Navigation Bar, 주문하기 버튼 등)

### 4. Controller는 Model과 View에 의존해도 된다.

Controller 내부에는 Model과 View의 코드가 존재해도 된다.
왜냐하면 Controller는 Model과 View의 중개자 역할을 하면서 전체 로직을 구성하기 때문이다.

### 5. View가 Model로부터 데이터를 받을 때, 반드시 Controller에서 받아야 한다.

View가 Model로부터 데이터를 받을 때 데이터가 옮겨지는 과정은 모두 Controller에서 이루어져야 한다는 것이다.
