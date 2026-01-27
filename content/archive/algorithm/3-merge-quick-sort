---
title: "병합 정렬과 퀵 정렬"
date: "2023-12-28"
category: "algorithm"
---

# 😃🤔🤔 병합 정렬 (분할 정복 ①)

> 가장 작은 단위로 나눈 뒤 조금씩 정렬해서 병합하는 알고리즘
> 

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c77b689e-2c1c-4a87-8551-170233e3494c/280af109-ca95-45b7-a309-1767ad4afc12/Untitled.png)

**divide and conquer(분할 정복)** : 해결하기 힘든 문제가 발생한다면 이걸 한 번에 해결하려고 하지 말고, 해결하기 쉬울 정도로 문제를 쪼갠 다음 하나씩 해결하자.

- 성능이 좋지만 이해와 구현이 어렵다.

finalMerge 함수 내에서 흩어진 배열을 합치는 부분이 성능을 좌우한다.
이때 하나 하나의 데이터가 두 개로 합쳐질 때 비교 연산을 두 번 한다.
마찬가지로 두개 두개의 데이터가 네 개로 합쳐질 때는 비교 연산을 네 번 한다.
각 단계를 거칠 때마다 영역의 수가 반으로 줄기 때문에 O(logn)의 성능을 가진다.
분할된 배열을 병합할 때는 n개의 데이터를 n번 비교하므로 최종적으로는 O(nlogn)의 성능을 가진다.

## 로직

1. 쪼갤 수 없을 때까지 쪼갠다
2. 가장 아래 부분부터 조금씩 정렬하며 올라온다
3. 마지막 정렬을 할 때 왼쪽과 오른쪽 영역의 첫 번째 값부터 비교하며 더 작은 값을 먼저 위로 올려보낸다
4. 올려보내던 도중에 왼쪽 인덱스를 가리키는 값이 오른쪽 영역을 침범했거나, 오른쪽 영역을 가리키는 값이 영역 밖으로 나가버린다면 해당 영역은 정렬이 끝난 것이고 다른 영역 역시 그대로 올려주면 되기 때문에 정렬이 종료된다

## 코드

```jsx
function mergeSort(arr, leftIndex, rightIndex) { // leftIndex : 왼쪽 끝, rightIndex: 오른쪽 끝
	if(leftIndex < rightIndex) {
		let midIndex = parseInt((leftIndex + rightIndex) / 2);
		mergeSort(arr, leftIndex, midIndex);
		mergeSort(arr, midIndex + 1, rightIndex);
		finalMerge(arr, leftIndex, midIndex, rightIndex);
	}
}

// 마지막에 양쪽 배열을 하나씩 비교하며 정렬하는 최종 함수
function finalMerge(arr, leftIndex, midIndex, rightIndex) {
	let leftAreaIndex = leftIndex; // 왼쪽 구역의 첫 번째 인덱스
	let rightAreaIndex = midIndex + 1; // 오른쪽 구역의 첫 번째 인덱스

	let tempArr = [];
	tempArr.length = rightIndex + 1;
	tempArr.fill(0, 0, rightIndex + 1);

	let tempArrIndex = leftIndex; // 새로운 배열에서 어디까지 정렬되었는지 체크하는 변수
	while(leftAreaIndex <= midIndex && rightAreaIndex <= rightIndex) { // 한 쪽이라도 정렬이 끝나지 않을 때까지
		if(arr[leftAreaIndex] <= arr[rightAreaIndex]) {
			tempArr[tempArrIndex] = arr[leftAreaIndex++];
		} else {
			tempArr[tempArrIndex] = arr[rightAreaIndex++];
		}
		tempArrIndex++;
	}
	// 한 쪽 정렬이 끝난 경우 다른 영역을 그대로 붙여넣기
	if(leftAreaIndex > midIndex) { // 오른쪽 영역의 병합이 덜 된 경우
		for(let i = rightArrIndex; i <= rightIndex; i++) {
			tempArr[tempArrIndex++] = arr[i];
		}
	} else { // 왼쪽 영역의 병합이 덜 된 경우
		for(let i = leftAreaIndex; i <= midIndex; i++) {
			tempArr[tempArrIndex++] = arr[i];
		}
	}

	// 새로운 배열을 원래 배열에 옮기기
	for(let i = leftIndex; i <= rightIndex; i++) {
		arr[i] = tempArr[i];
	}
}
```

# 퀵 정렬 (분할 정복 ②)

> 퀵 정렬은 병합 정렬과 같은 분할 정복 알고리즘이다. 마찬가지로 재귀 함수를 사용한다.
> 
- 피벗이 매번 배열의 가운데가 되는 경우
데이터가 한 개가 될 때까지 반으로 나누므로 logn 의 성능을 가지고, 이 작업을 데이터 n개만큼 반복해야 하므로 n을 마저 곱해주면 O(nlogn)의 성능을 가진다.
- 피벗이 배열을 반으로 가르지 않고 한쪽에 쏠리는 경우(최악의 경우)
O(n^2)의 성능을 가진다.
- 퀵 정렬이 적은 비교와 적은 메모리 공간을 차지하기 때문에 병합 정렬보다 더 효율적인 알고리즘으로 평가된다.

## 로직

1. 퀵 정렬에서는 정렬하기 전에 배열에 있는 숫자 중 하나를 `피벗` 으로 설정한다.
2. leftStartIndex와 rightStartIndex가 존재하는데, leftStartIndex가 피벗보다 큰 값을 만날 때까지 오른쪽으로 이동한다.
3. rightStartIndex가 피벗보다 작은 값을 만날 때까지 왼쪽으로 이동한다.
4. leftStartIndex와 rightStartIndex를 서로 교환한다.
5. 위 과정을 반복하다가 서로 지나쳤다면 더 이상 진행하지 않는다.
6. rightStartIndex와 피벗을 서로 교환한다. 이때 rightStartIndex 자리로 온 피벗을 기준으로 왼쪽은 피벗보다 작은 값들이, 오른쪽은 피벗보다 큰 값들이 모이게 된다.
7. 이제 피벗을 기준으로 양 쪽을 위 과정을 수행한다면 모든 데이터가 정렬되게 된다.

## 코드

```jsx
function quickSort(arr, left, right) {
	if(left <= right) {
		let pivot = divide(arr, left, right);
		quickSort(arr, left, pivot - 1); // 피벗 기준 왼쪽
		quickSort(arr, pivot + 1, right); // 피벗 기준 오른쪽
	}
}

function divide(arr, left, right) {
	let pivot = arr[left]; // 맨 첫번째 값을 피벗으로 설정
	let leftStartIndex = left + 1; // 시작은 피벗 다음부터이므로 + 1
	let rightStartIndex = right;
	
	while(leftStartIndex <= rightStartIndex) { 
		// leftStartIndex 값이 피벗보다 큰 값을 찾기 전까지 오른쪽으로 이동
		while(leftStartIndex <= right && pivot >= arr[leftStartIndex]) {
			leftStartIndex++;
		}
		// rightStartIndex 값이 피벗보다 작은 값을 찾기 전까지 왼쪽으로 이동
		while(rightStartIndex >= (left + 1) && pivot <= arr[rightStartIndex]) {
			rightStartIndex--;
		}
		
		// 둘이 마주치거나 지나쳤다면 진행을 멈추고 서로 바꿈
		if(leftStartIndex <= rightStatIndex) {
			swap(arr, leftStartIndex, rightStartIndex);
		}
	}
	// rightStartIndex와 피벗의 위치를 서로 바꾸고 가장 첫 번째 반환
	swap(arr, left, rightStartIndex);
	return rightStartIndex;
}

function swap(arr, index1, index2) {
	let temp = arr[index1];
	arr[index1] = arr[index2];
	arr[index2] = temp'
}
```
