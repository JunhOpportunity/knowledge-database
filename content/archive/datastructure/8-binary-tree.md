---
title: "이진 탐색 알고리즘과 이진 탐색 트리"
date: "2023-12-24"
category: "datastructure"
---
# 이진 탐색 알고리즘

> 반 씩 짤라서 답을 탐색하는 알고리즘
> 
- 성능은 O(logn)이다.

```jsx
function binarySearch(arr, target, start, end) {
	if(start > end) { // 더이상 쪼갤 수 없기 때문에 탐색을 끝냄
		return null;
	}
	
	let mid = Math.floor((start + end) / 2); // 반 내림
	if (arr[mid] == target) { // 중간 값이 찾는 값인 경우
		return mid;
	} else if (target > arr[mid]) { // 중간 값보다 큰 경우 (오른쪽 탐색)
		return binarySearch(arr, target, mid + 1, end);
	} else { // 중간 값보다 작은 경우 (왼쪽 탐색)
		return binarySearch(arr, target, start, mid - 1);
	}
}
```

# 🤔 이진 탐색 트리

> 왼쪽 서브트리는 작은 값, 오른쪽 서브트리는 큰 값으로 이루어진 트리
> 

<img width="461" height="355" alt="image" src="https://github.com/user-attachments/assets/f39ca90f-342a-4a7e-a3df-c23c79c4c380" />

## 특징

이진 탐색 트리는 삽입, 제거, 탐색의 성능이 좋다.
이진 탐색 트리는 삽입, 제거, 탐색의 성능은 O(n)이 될수도 있고 O(logn)이 될수도 있다.

그런데 이진 탐색 트리를 작은 노드부터 오름차순으로 삽입하게 된다면, 모두 오른쪽으로 삽입되기 때문에 연결 리스트와 같은 모습을 갖게 된다.

삽입할 때 최적의 상태로 삽입하면 되지만 사용자가 어떤 데이터를 삽입할 지 예측하는 것은 불가능하고 최적의 상태를 의식하며 삽입하기는 너무 불편하기도 하고 삽입과 제거를 반복하다보면 구조가 무너지게된다.

따라서 균형이 무너지지 않도록 유지하는 방법들이 나타났는데, 이것을 자가 균형 이진 탐색 트리라고 한다. (AVL 트리, 레드-블랙 트리)

## 조건

- 중복된 노드가 없어야 한다.
- 특정 노드의 왼쪽 자식 노드는 해당 노드의 값보다 항상 작은 값들로 이루어 져야 한다.
- 특정 노드의 오른쪽 자식 노드는 해당 노드의 값보다 항상 큰 값들로 이루어 져야 한다.
- 자식 노드와 서브 트리에도 위 모든 규칙이 적용되어야 한다.

## 이진 탐색 트리 추상 자료형

1. 데이터 삽입 - insert(data)
2. 데이터 제거 - remove(targetData)
① 터미널 노드 제거 
② 자식 노드가 한 개인 노드 제거
③ 자식 노드가 두 개인 노드 제거
3. 데이터 탐색 - search(targetData)

## 코드

```jsx
class BinarySearchTree {
	constructor(rootNode == null) {
		this.root = rootNode;
	}

	insert(data) {
		if(this.root == null) { // 비어있는 경우 (처음 삽입하는 경우)
			this.root = new BinaryTree(data);
			return;
		}
		// 노드가 하나 이상 존재하는 경우 (처음 삽입이 아닌 경우)
		let currentNode = this.root;
		// 부모 노드는 자식 노드를 참조할 수 있지만 자식 노드는 부모 노드를 
		// 참조할 수 없으므로 따로 부모 노드를 가리키는 변수 선언
		let parentNode = null; 

		while(currentNode != null) { // currentNode가 null이면 자리를 찾은 것
			parentNode = currentNode; 
			
			if(currentNode.gertData() > data) { // 작은 경우 왼쪽으로
				currentNode = currentNode.getLeftSubTree();
			} else if(currentNode.getData() < data) { // 큰 경우 오른쪽으로
				currentNode = currentNode.getRightSubTree();
			} else { // 같은 경우 중복된 노드는 없어야 하므로 그냥 리턴
				return;
			}
		}
		
		// 새로운 데이터 삽입
		let newNode = new BinaryTree(data);
		if(parentNode.getData() > data) { // 부모 노드보다 작으면 왼쪽 서브 노드에
			parentNode.setLeftSubTree(newNode);
		} else { // 부모 노드보다 크면 오른쪽 서브 노드에
			parentNode.setRightSubTree(newNode);
		}
	}

	search(targetData) {
		let currentNode = this.root; // 순회를 위한 변수
	
		while(currentNode != null) {
			if(currentNode.getData() == targetData) {
				return currentNode;
			} else if(currentNode.getData() > targetData) { // 작으니까 왼쪽 서브 노드로 이동
				currentNode = currentNode.getLeftSubTree();
			} else { // 크니까 오른쪽 서브 트리로 이동
				currentNode = currentNode.getRightSubTree();
			}
		}
		// 터미널 노드까지 갔는데 없을 경우 찾는 데이터가 존재하지 않으므로 null 반환
		return null;
	}
	
	remove(targetData) {
		// 루트노드를 제거할 때 사용하기 위한 것
		// 루트노드는 부모 노드를 갖고있지 않아서 다른 노드와 다른 방식으로 제거해야함.
		let fakeParentRootNode = new BinaryTree(0); 
		let parentNode = fakeParentRootNode;
		let currentNode = this.root;
		let deletingNode = null;
		
		// 루트노드의 부모가 가짜 루트노드가 됨. 이때 오른쪽이든 왼쪽이든 상관 없다.
		fakeParentRootNode.setRightSubTree(this.root);
		
		// 크기 비교 후 타겟을 찾거나 끝날때까지 왼쪽 또는 오른쪽 서브 트리로 이동
		while(currentNode != null && currentNode.getData() != targetData) {
			parentNode = currentNode;
			
			if(currentNode.getData() > targetData) {
				currentNode = currentNode.getLeftSubTree();
			} else {
				currentNode = currentNode.getRightSubTree();
			}
		}

		if(currentNode == null) { // 타겟이 없는 경우 그냥 종료
			return;
		}
		
		deletingNode = currentNode;

		// ① 터미널 노드 제거 (자식 노드가 없는 경우)
		if(deletingNode.getLeftSubTree() == null && deleteNode.getRightSubTree() == null) {
			// 제거할 노드가 왼쪽 자식노드인 경우
			if(parentNode.getLeftSubTree() == deletingNode) {
				parentNode.removeLeftSubTree();
			} else { // 제거할 노드가 오른쪽 자식노드인 경우
				parentNode.removeRightSubTree();
			} 
		// ② 자식 노드가 한 개인 노드 제거
		} else if(deletingNode.getLeftSubTree() == null || deletingNode.getRighttSubTree() == null) {
			let deletingNodeChild;
			// 제거할 노드의 왼쪽 자식노드가 있는 경우
			if(deletingNode.getLeftSubTree() != null) {
				deletingNodeChild = deletingNode.getLeftSubTree();
			// 제거할 노드의 왼쪽 자식노드가 있는 경우
			} else {
				deletingNodeChild = deletingNode.getRightSubTree();
			}

			// 부모노드의 왼쪽 자식노드가 제거할 노드인 경우
			// => 부모노드의 왼쪽 자식노드와 제거할 노드의 자식노드를 연결
			if(parentNode.getLeftSubTree() == deletingNode) {
				parentNode.setLeftSubTree(deletingNodeChild);
			// 부모노드의 오른쪽 자식노드가 제거할 노드인 경우
			// => 부모노드의 오른쪽 자식노드와 제거할 노드의 자식노드를 연결
			} else {
				parentNode.seRightSubTree(deletingNodeChild);
			}
		// ③ 자식 노드가 두 개인 노드 제거
		} else {
			let replacingNode = deletingNode.getLeftSubTree();
			let replacingNodeParent = deletingNode;
			// 대체할 노드를 찾아야 하기 때문에 왼쪽 자식 노드에서 가장 큰 값을 찾음.
			// 이때 가장 큰 값은 오른쪽으로 쭉 내려가면 바로 찾을 수 있다.
			while(replacingNode.getRightSubTree() != null) {
				replacingNodeParent = replacingNode;
				replacingNode = replacingNode.getRightSubTree();
			}
			let deletingNodeData = deletingNode.getData(); // 제거 후 반환할 값
			deletingNode.setData(replacingNode.getData()); // 찾은 값을 제거할 노드 위치로 옮김
	
			// 왼쪽 자식노드가 대체할 노드인 경우 (대체할 노드의 오른쪽 노드는 존재할 수 없으니 왼쪽 노드를 연결하는 것이다.)
			if(replacingNodeParent.getLeftSubTree() == replacingNode) {
				replacingNodeParent.setLeftSubTree(replacingNode.getLeftSubTree());
			// 오른쪽 자식노드가 대체할 노드인 경우 (대체할 노드의 오른쪽 노드는 존재할 수 없으니 왼쪽 노드를 연결하는 것이다.)
			} else {
				replacingNodeParent.setRightSubTree(replacingNode.getLeftSubTree());
			}
			
			// 삭제될 데이터를 대체된 데이터의 원래 위치에 넣어줌으로써 가비지 컬렉터가 제거할 수 있도록 한다.
			deletingNode = replacingNode;
			deletingNode.setData(deletingNodeData);
		}

		// 만약 루트 노드가 변경되었다면 
		if(fakeParentRootNode.getRightSubTree() != this.root) {
			this.root = fakeParentRootNode.getRightSubTree();
		}
	
		return deletingNode;
	}
}
```

+) 만약 해당 데이터를 참조하는 변수들이 사라지면 가비지 컬렉터가 더 이상 사용되지 않는 데이터로 판단하고 해당 데이터를 메모리에서 완전히 제거해버린다.
