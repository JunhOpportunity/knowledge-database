---
title: "탐욕 알고리즘"
date: "2024-01-13"
category: "algorithm"
---
# 탐욕 알고리즘

> 최적해를 구하는데 사용되는 근사적인 방법
> 

매 선택지에서 현재 상황에 가장 좋다고 판단되는 선택을 한다

## 최적해를 얻을 수 있는 조건

**탐욕스러운 선택 속성**과 **최적 부분 구조 조건**을 가지고 있는 구조를 **`매트로이드`**라고 한다.

1. **탐욕스러운 선택 속성 존재**
앞의 선택이 이후의 선택에 영향을 주지 않는다
2. **최적 부분 구조 조건**
문제에 대한 최적해가 부분 문제에 대해서도 역시 최적해이다
⇒ 최소힙(아래로 갈수록 커지는 트리)으로 트리가 구현된다면 이를 만족시킬 수 있다

<img width="501" height="207" alt="image" src="https://github.com/user-attachments/assets/0e1787a9-755e-4540-ac14-286c054c34f5" />


⭕ 최소힙 구현으로 최적 부분 구조 조건 만족

<img width="475" height="188" alt="image" src="https://github.com/user-attachments/assets/55fc7711-70d3-4b87-b962-63bb1777673d" />


❌ 최적 부분 구조 조건 만족하지 않음

## 예시

지폐 대신 동전을 거슬러 준다고 했을 때, 1400원을 거슬러 주어야 한다면 500 X 2 + 100 X 4 가 되면서 매트로이드를 만족시킨다.

그런데 여기서 만약 동전이 서로 배수가 아닌 경우라면 최적 부분 구조 조건을 만족하지 않기 때문에 매트로이드가 아니다.

예를 들어 1800원을 거슬러 주어야 할 때 400원 짜리 동전도 존재한다면 500 X 3 + 100 X 3 또는 400 X 4 + 100 X 2 가 될 수 있기 때문이다.

## 구현

```jsx
class Coin {
	constructor(won) {
		this.won = won;
		this.count = 0;
	}
}

function changeCoin(money){
	let coins = [];
	coins.push(new Coin(500));
	coins.push(new Coin(100));
	coins.push(new Coin(50));
	coins.push(new Coin(10));

	for(let i = 0; i < coins.length; i++) {
		while(coins[i].won <= money) {
			coins[i].count++;
			money -= coins[i].won;
		}
	}
	
	console.log(coins);
}
```
