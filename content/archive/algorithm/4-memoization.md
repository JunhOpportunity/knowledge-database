---
title: "메모이제이션과 타뷸레이션"
date: "2024-01-02"
category: "algorithm"
---
# 메모이제이션 (동적 프로그래밍 ①)

> 계산 결과를 저장해 같은 계산을 여러 번 수행하지 않도록 하는 기법
> 
- 메모이제이션은 재귀를 이용해 문제를 하향식으로 해결한다.

이미 실행되었던 함수가 나중에 똑같이 다시 실행되는 동작이 반복되면서 성능을 저하시킨다.

따라서 이때 이미 한 번 실행되었던 함수의 결과를 저장해두었다가 같은 함수 호출이 발생한다면 이미 실행되었던 함수의 결과를 재사용 하는 것을 메모이제이션이라고 한다.

메모이제이션을 사용하면 함술 호출 수가 현저하게 줄어들며 성능이 향상된다.

+) 메모이제이션은 속도를 위해 메모리를 사용한다.

이때 사용하는 자료구조가 바로 `해시 테이블`이다. 자바스크립트의 객체가 이와 동일하다.

### 코드 - 피보나치 함수를 해시 테이블을 사용해 성능 향상

메모이제이션을 사용해 피보나치 수열은 O(n^2) 에서 O(n)이 되었다.

```jsx
// 원본
function fibonacci(n) {
	if(n == 0 || n == 1) return n;
	return fibonacci(n - 2) + fibonacci(n - 1);
}

// 성능 향상
function fibonacci(n, memoization) {
	if(n == 0 || n == 1) return n;
	
	if(memoization[n] == null) { // 메모이제이션에 존재하지 않는 경우
		memo[n] = fibonacci(n - 2, memo) + fibonacci(n - 1, memo);
	}
	return memo[n]
}
```

# 타뷸레이션 (동적 프로그래밍 ②)

> 계산에 필요한 모든 값을 전부 미리 계산한 후 테이블에 저장해두는 기법
> 
- 타뷸레이션은 상향식으로 해결한다.

```jsx
function fibonacci(n, memoization) {
	if(n <= 1) return n;
	
	let table = [0, 1];
	
	for(let i = 2; i <= n; i++) {
		table[i] = table[i - 2] + table[i - 1];
	}
	return table[n];
}
```
