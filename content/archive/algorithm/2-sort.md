---
title: "버블 정렬, 선택 정렬, 삽입 정렬"
date: "2023-12-23"
category: "algorithm"
---

# 버블 정렬

> 앞 숫자와 비교해서 자리를 바꾸는 알고리즘
> 
- 이해와 구현은 쉽지만 성능은 그렇게 좋지 않다

버블 정렬은 원소가 하나 남을 때까지 반복하므로 수학 식으로 표현하면
(n - 1) + (n - 2) + (n - 3) + … + 2 + 1 이다.
이는 등차수열로 (n^2 - n)/2 이므로 성능은 O(n^2) 이다.

이를 간단하게 계산하려면 for문이 2개 들어있으니 n^2의 성능이겠구나 생각하면 쉽다.

```jsx
function bubbleSort(arr) {
	for(let i = 0; i < arr.length - 1; i++) { // 자리 교체는 배열 길이보다 1 적은 횟수 만큼 수행
		for(let j = 0; j < (arr.length - i - 1); j++) {
			if(arr[j] > arr[j + 1]) {
				let temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;
			}
		}
	}
}
```

# 선택 정렬

> 배열의 정렬되지 않은 영역의 첫 번째 원소를 시작으로 마지막 원소까지 비교 후 가장 작은 값을 첫 번째 원소로 가져오는 알고리즘
> 

<img width="570" height="98" alt="image" src="https://github.com/user-attachments/assets/86fb717a-fde3-4043-aeda-7909d6c041f7" />


빨간색은 정렬된 영역, 검은색은 정렬되지 않은 영역이다.

- 버블 정렬과 마찬가지로 구현은 쉽지만 성능은 그렇게 좋지 않다.

바깥쪽 for문이 실행될수록 안쪽 for문이 줄어드는 형태이므로 버블 정렬과 마찬가지로 O(n^2)의 성능을 가진다.

```jsx
function selectionSort(arr) {
	for(let i = 0; i < arr.length - 1; i++){
		let minValueIndex = i;
		
		for(let j = i + 1; j < arr.length; j++) {
			if(arr[j] < arr[minValueIndex]) {
				minValueIndex = j;
			}
		}
		
		let temp = arr[i];
		arr[i] = arr[minValueIndex];
		arr[minValueIndex] = temp;
	}
}
```

# 🤔삽입 정렬

> 정렬되지 않은 영역에서 데이터를 하나씩 꺼내서 정렬된 영역 내 적절한 위치에 삽입하는 알고리즘
> 

<img width="545" height="78" alt="image" src="https://github.com/user-attachments/assets/6972f69b-b31f-45c3-872c-cf8e4b758bd4" />


- 선택 정렬과 마찬가지로 구현은 쉽지만 성능은 그렇게 좋지 않다.

삽입 정렬도 마찬가지로 두 개의 for문을 갖고 있고 버블 정렬, 선택 정렬과 비슷한 로직을 갖고 있기 때문에 O(n^2)의 성능을 가진다.

```jsx
function insertionSort(arr) {
	for(let i = 1; i < arr.length; i++) { // 첫 번째 원소는 이미 정렬되어있다고 가정.
		let insertingData = arr[i]; // 정렬되지 않은 배열의 첫 번째 값
		let j;
		for(j = i - 1; j >= 0; j--) { // 정렬된 영역에서 가장 마지막부터 작은 값으로 내려가면서 비교
			if(arr[j] > insertingData) { // 정렬되지 않은 배열의 첫 번째 값이 더 작으면 한 칸 왼쪽으로 이동
				arr[j + 1] = arr[j]
			} else {
				break;
			}
		}
		// 순회 완료 후 자리를 찾으면 해당 자리에 덮어쓰기
		arr[j + 1] = insertingData;
	}
}
```
