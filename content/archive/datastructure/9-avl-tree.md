---
title: "AVL 트리"
date: "2024-01-01"
category: "datastructure"
---

# AVL 트리

> 이진 탐색 트리의 균형을 유지하는 자료구조
> 

## 균형이 맞는지 확인하는 방법

1. 루트 노드를 기준으로 왼쪽 서브트리와 오른쪽 서브트리의 높이를 구한다.
2. 두 서브트리의 높이 차이를 구한다.
3. 높이 차이가 1 이하라면 균형이 맞다고 판단한다.
4. 이 과정을 반복하며 모든 서브트리를 순회하며 높이 차이를 확인한다.

## 균형이 맞지 않는다면

<img width="541" height="148" alt="image" src="https://github.com/user-attachments/assets/6f0b652e-aa4c-4be7-8dca-43c752848e48" />

균형이 맞지 않는 트리는 `회전`시켜서 균형을 맞춘다.

## 회전의 종류

**LL 회전(Left Left Rotation / Single Left Rotation)** : 오른쪽으로 쭉 뻗은 노드를 반시계 방향으로 회전

<img width="348" height="154" alt="image" src="https://github.com/user-attachments/assets/08e3b2f2-84f6-4da6-90ae-f6236100285c" />

**RR 회전(Right Right Rotation / Single Right Rotation)** : 왼쪽으로 쭉 뻗은 노드를 시계 방향으로 회전

<img width="446" height="218" alt="image" src="https://github.com/user-attachments/assets/5bb8aa79-9b80-48ab-88d3-89b5134ff7f8" />

LR 회전(Left Right Rotation) : 왼쪽으로 뻗어서 중간에서 안쪽으로 꺾인 노드를 반시계 방향으로 먼저 회전한 뒤에 시계 방향으로 회전 시킨다.

<img width="435" height="193" alt="image" src="https://github.com/user-attachments/assets/a8a66a91-76e2-40bf-887d-ecf0e04f0e58" />

RL 회전(Right Left Rotation) : 오른쪽으로 뻗어서 중간에 안쪽으로 꺾인 노드를 시계 방향으로 먼저 회전한 뒤에 반시계 방향으로 회전 시킨다. 

<img width="513" height="209" alt="image" src="https://github.com/user-attachments/assets/e39b2a17-71dc-43e7-bc7e-5293823db170" />

## 코드

> 이진 탐색 트리를 상속받아 삽입, 제거시 균형을 맞추는 기능만 구현하면 된다.
> 

```jsx
class AVLTree {
	constructor(rootNode == null) {
		this.root = rootNode;
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

	getHeight(node) {
		if(node == null) {
			return 0; // 노드가 비어있는 경우 height 프로퍼티 참조 불가하므로 0 리턴
		} else {
			return node.height
		}
	}

	updateHeight(node) {
		// 해당 노드의 왼쪽 노드와 오른쪽 노드의 높이를 구하고
		// 더 높은 높이에 + 1 해주면 해당 노드의 높이가 구해진다.
		let leftChildHeight = this.getHeight(node.getLeftSubTree());
		let rightChildHeight = this.getHeight(node.getRightSubTree());
		node.height = Math.max(leftChildHeight, rightChildHeight) + 1;
	}

	// 좌 우 서브트리의 균형을 확인하는 함수
	getBalanceFactor(node) { 
		// 만약 양수라면 왼쪽 서브트리가, 음수라면 오른쪽 서브 트리가 더 높은 것이다.
		return this.getHeight(node.getLeftSubTree()) - this.getHeight(node.getRightSubTree());
	}

	rotateLeft(node) {
		let childNode = node.getRightSubTree();
		node.setRightSubTree(childNode.getLeftSubTree());
		childNode.setLeftSubTree(node);
		
		this.updateHeight(node);
		this.updateHeight(childNode);
		
		return childNode; // 새 루트 노드가 된 노드를 반환
	}
	rotateRight(node) {
		let childNode = node.getLEftSubTree();
		node.setLeftSubTree(childNode.getRightSubTree());
		childNode.setRightSubTree(node);
		
		this.updateHeight(node);
		this.updateHeight(childNode);
		
		return childNode; // 새 루트 노드가 된 노드를 반환
	}

	rotation(targetNode, data) {
		// data는 트리의 균형을 무너뜨린 원인이 되는 노드
		let balanceFactor = this.getBalanceFactor(targetNode);
		let isRootNode = false;
		if(targetNode == this.root) { // 회전하려는 노드가 루트노드인 경우
			isRootNode = true;
		}
	
		// LL 회전이 필요한 경우 (오른쪽으로 쭉 뻗은 경우)
		if(balanceFactor < -1 && data > targetNode.getRightSubTree.getData()) {
			targetNode = this.rotateLeft(targetNode);
		
		// RR 회전이 필요한 경우 (왼쪽으로 쭉 뻗은 경우)
		} else if(balanceFactor > 1 && data < targetNode.getLeftSubTree().getData()) {
			targetNode = this.rotateRight(targetNode);

		// LR 회전이 필요한 경우
		} else if(balanceFactor > 1 && data > targetNode.getLeftSubTree().getData()) {
			// 1. LL 회전
			targetNode.setLeftSubTree(this.rotateLeft(targetNode.getLeftSubTree()));
			// 2. RR 회전
			targetNode = this.rotateRight(targetNode);

		// RL 회전이 필요한 경우
		} else if(balanceFactor < -1 && data < targetNode.getRightSubTree().getData()) {
			// 1. RR 회전
			targetNode.setRightTree(this.rotateRight(targetNode.getRightSubTree()));
			// 2. LL 회전
			targetNode = this.rotateLeft(targetNode);
		}
	
		// 루트 노드가 회전한 경우
		if(isRotateNode) {
			this.root = targetNode;
		}
		
		return targetNode;
	}
	
	// 노드를 제거할 때 필요한 불균형을 찾는 함수
	getUnBlanceNode(targetRootNode, unBlanceNode = null) {
		// [기저조건] 터미널 노드를 찾아서 그 노드가 불균형을 일으키는 노드라고 저장
		if(targetRootNode.getLeftSubTree() == null && targetRootNode.getRightSubTree() == null) {
			unBalanceNode = targetRootNode;
			return unBalanceNode
		}
	
		// 불균형 일으키는 노드를 찾는 로직
		let balanceFactor = this.getBalanceFactor(targetRootNode);
		if(balanceFactor > 0) {
			unBalanceNode = this.getUnBalanceNode(targetRootNode.getLeftSubTree(), unBalanceNode);
		} else if(balanceFactor < 0) {
			unbalanceNode = this.getUnBalanceNode(targetRootNode.getRightSubTree(), unBalanceNode);	
		} else {
			unBalanceNode = targetRootNode.getRightSubTree();
		}
	
		return unBalanceNode;
	}

	// 재귀함수를 사용해 하향식으로 삽입 구현
	insert(targetRootNode, data) {
		// 기저조건 : 삽입하려는 노드가 비어있는 상태인 경우
		if(targetRootNode == null) {
			targetRootNodoe = new BinaryTree(data)
		}
		
		// AVL 트리에 처음 데이터를 삽입하는 경우 새로 만든 노드를 루트 노드로 설정
		if(this.root == null) {
			this.root = targetRootNode;
		// 중복된 데이터가 존재하는 경우
		} else if(targetRootNode.getData() == data) {
			return targetRootNode;
		// 삽입하려는 데이터가 해당 노드보다 작은 경우 (왼쪽 삽입)
		} else if(targetRootNode.getData() > data) {
			targetRootNode.setLeftSubTree(this.insert(targetRootNode.getLeftSubTree(), data));
		// 삽입하려는 데이터가 해당 노드보다 큰 경우 (오른쪽 삽입)
		} else {
			targetRootNode.setRightSubTree(this.insert(targetRootNode.getRightSubTree(), data));
		}
		
		// 데이터가 삽입되었으니 높이 업데이트
		this.updateHeight(targetRootNode);
		// 회전 (균형이 맞지 않을 경우에만 회전됨)
		// 삽입하는 노드가 균형을 무너뜨리므로 따로 균형을 무너뜨리는 노드를 찾을 필요가 없음.
		targetRootNode = this.rotation(targetRootNode, data);

		return targetRootNode;
	}

	// 재귀함수를 사용해 하향식으로 제거 구현
	remove(targetRootNode, data, parentNode = null) {
		// 삭제할 노드가 왼쪽 자식노드에 존재하는 경우
		if(targetRootNode.getData() > data && targetRootNode.getLeftSubTree() != null) {
			targetRootNode.setLeftSubTree(this.remove(targetRootNode.getLeftSubTree(), data, targetRootNode));
		// 삭제할 노드가 오른쪽 자식노드에 존재하는 경우
		} else if(targetRootNode.getData() < data && targetRootNode.getLeftSubTree() != null) {
			targetRootNode.setRightSubTree(this.remove(targetRootNode.getRightSubTree(), data, targetRootNode));
		// 삭제할 노드를 찾은 경우 (data == targetRootNode)
		} else if(targetRootNode.getData() == data) {
			targetRootNode = this.removeHelper(targetRootNode, data, parentNode);

			// 루트 노드를 삭제해 기저조건으로 종료되어서 높이 업데이트와 회전을 못하는 경우를 위해 작성
			if(parentNode == null && targetRootNode != null) {
				this.updateHeight(targetRootNode);
				let unBalanceNode = this.getUnbalanceNode(targetRootNode);
				targetRootNode = this.rotation(targetRootNode, unBalanceNode.getData());
			}
		}

		// 데이터가 삭제되었으니 높이 업데이트
		// 루트 노드를 삭제하는 경우 기저조건으로 바로 종료되기 때문에 이 영역이
		// 실행되지 않아서 높이 업데이트와 회전이 불가능하다.
		this.updateHeight(targetRootNode);
		// 어떤 노드가 트리의 균형을 무너뜨리는지 확인해야함. (insert와 다른 점)
		let unBalanceNode = this.getUnbalanceNode(targetRootNode);
		targetRootNode = this.rotation(targetRootNode, unBalanceNode.getData());
		return targetRootNode;
	}

	removeHelper(deletingNode, data, parentNode) {
		// 이진 탐색 트리의 remove와 유사해서 붙여넣음.
		
		let fakeParentRootNode = new BinaryRee(0);
		fakeParentRootNode.setRightSubTree(this.root);
	
		if(parentNode == null) {
			parentNode = fakeParentRootNode;
		}
	
		let deletingNodeChild = null;
	
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
			deletingNodeChild = deletingNode;
		}

		// 만약 루트 노드가 변경되었다면 
		if(fakeParentRootNode.getRightSubTree() != this.root) {
			this.root = fakeParentRootNode.getRightSubTree();
		}
	
		return deletingNodeChild;
	}
}
```

ㅋ
