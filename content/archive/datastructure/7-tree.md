---
title: "트리와 노드, 이진 트리"
date: "2023-12-13"
category: "datastructure"
---

# 트리

<img width="535" height="232" alt="image" src="https://github.com/user-attachments/assets/2e863e84-be91-4118-b8dd-b2391313ff9b" />

## 트리의 레벨과 높이

> 자식 노드로 내려갈수록 레벨이 증가하고, 트리에서 가장 높은 레벨을 높이라고 한다.
> 

<img width="534" height="198" alt="image" src="https://github.com/user-attachments/assets/deefea4a-7163-4467-a3ca-bf5b07c3b659" />

## 루트 노드

> 최상위에 존재하는 노드
> 

## 터미널 노드

> 자식 노드가 존재하지 않는 노드 (잎 노드)
> 

## 인터널 노드

> 부모 노드가 존재하면서 자식 노드도 존재하는 노드 (루트 노드와 터미널 노드를 제외한 노드)
> 

## 서브 트리

> 루트 노드를 제외한 묶음들. 터미널 노드 하나도 서브 트리라고 할 수 있다.
> 

# 이진 트리

> 각각의 노드가 최대 두 개의 자식 노드를 가질 수 있는 트리
> 

자식 노드를 0개 ~ 2개 갖고 있는 노드로 이루어진 트리를 말한다.
하나의 노드라도 자식 노드가 2개를 초과한다면 이진 트리가 아니다.

## 포화 이진 트리

> 트리의 최대 레벨에 있는 모든 터미널 노드가 꽉 찬 트리
> 

## 완전 이진 트리

> 최대 레벨을 제외한 나머지 레벨이 모두 꽉 채워져있고 최대 레벨의 노드들은 왼쪽부터 채워진 트리
> 

**⇒ 해당 레벨이 n 이라고 했을 때 마지막 레벨을 제외하고 나머지 레벨은 그 레벨의 노드의 개수가 2^(n-1)을 만족해야 하고, 마지막 레벨은 왼쪽부터 채워져야 한다.**

<img width="541" height="194" alt="image" src="https://github.com/user-attachments/assets/2447d7a5-c2f9-4b67-9d65-a204d2c1d789" />

## 이진 트리의 순회 (재귀 사용)

<img width="304" height="228" alt="image" src="https://github.com/user-attachments/assets/27009872-a7fe-43c8-839c-950011079845" />

- 전위 순회 : 1 → 2 → 3
- 중위 순회 : 2 → 1 → 3
- 후위 순회 : 2 → 3 → 1

## 이진 트리의 추상자료형

1. 해당 트리의 노드 데이터 리턴 - getData()
2. 해당 트리의 노드 데이터 설정 - setData(data)
3. 해당 트리의 왼쪽 서브 트리의 첫 노드 리턴 - getLeftSubTree()
4. 해당 트리의 오른쪽 서브 트리의 첫 노드 리턴 - getRightSubTree()
5. 해당 트리의 왼쪽 서브 트리로 위치 이동 - setLeftSubTree(tree)
6. 해당 트리의 오른쪽 서브 트리로 위치 이동 - setRightSubTree(tree)
7. 전위순회 - preOrderTraversal()
8. 중위순회 - inOrderTraversal()
9. 후위순회 - postOrderTraversal()
10. 해당 트리의 왼쪽 서브 트리 제거 - removeLeftSubTree()
11. 해당 트리의 오른쪽 서브 트리 제거 - removeRightSubTree()

```jsx
class binaryTree {
	constructor(data, leftTree = null, rightTree = null) {
		this.data = data;
		this.leftSubTree = leftTree;
		this.rightSubTree = rightTree;
		this.height = 1;
	}
	
	getData() {
		return this.data;
	}
	setData(data) {
		this.data = data;
	}
	getLeftSubTree() {
		return this.leftSubTree;
	}
	getRightSubTree() {
		return this.rightSubTree;
	}
	setLeftSubTree(tree) {
		this.leftSubTree = true;
	}
	setRightSubTree(tree) {
		this.rightSubTree = true;
	}

	// 이진 트리 순회
	// 1. 전위 순회 (1 -> 2 -> 3)
	preOrderTraversal() {
		if(tree == null) return; // 기저조건
		
		console.log(tree.data);
		this.preOrderTraversal(tree.getLeftSubTree()); // 왼쪽
		this.preOrderTraversal(tree.getRightSubTree()); // 오른쪽
	}
	// 2. 중위 순회 (2 -> 1 -> 3) 
	inOrderTraversal() {
		if(tree == null) return; // 기저조건

		this.inOrderTraversal(tree.getLeftSubTree());
		console.log(tree.data);
		this.inOrderTraversal(tree.getRightSubTree());
	}
	// 3. 후위 순회 (2 -> 3 -> 1)
	postOrderTraversal() {
		if(tree == null) return; // 기저조건
		
		this.postOrderTraversal(tree.getLeftSubTree());
		this.postOrderTraversal(tree.getRightSubTree());
		console.log(tree.data);
	}
	// 왼쪽 서브 트리 제거
	removeLeftSubTree() {
		let deletingNode = this.getLeftSubTree(); // 제거 해야 할 노드, 반환을 위해
		this.setLeftSubTree(null); // 왼쪽 서브 트리를 null로 설정해 연결 끊기
		return deletingNode;
	}
	// 오른쪽 서브 트리 제거
	removeRightSubTree() {
		let deletingNode = this.getLeftSubTree(); // 제거 해야 할 노드, 반환을 위해
		this.setRightSubTree(null); // 왼쪽 서브 트리를 null로 설정해 연결 끊기
		return deletingNode;
	}
}
```
