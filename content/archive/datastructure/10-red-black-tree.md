---
title: "RED-BLACK 트리"
date: "2024-01-03"
category: "datastructure"
---
# Red-Black 트리

## AVL 트리와 Red-Black 트리의 차이점

## AVL 트리

> 데이터 검색이 빈번한 경우 유리
> 

AVL 트리는 더 엄격하게 균형을 유지한다.
데이터 삽입 또는 제거 시에 더 많은 회전이 발생한다.
⇒ 검색 성능이 좋다

## Red-Black 트리

> 데이터 삽입과 제거가 빈번한 경우 유리
> 

Red-Black 트리는 균형을 느슨하게 유지한다.
데이터 삽입 또는 제거 시에 더 적은 회전이 발생한다.
⇒ 검색 시 시간이 많이 걸린다

## Red-Black 트리의 규칙

1. 모든 노드는 빨간색 또는 검은색 두 가지 색 중 하나를 가지고 있다.
2. 루트노드는 항상 검은색이다.
3. 모든 터미널 노드(=NIL)는 검은색이다.
4. 연속해서 빨간색 노드가 올 수 없다.
5. 루트노드에서 터미널 노드인 NIL 노드까지 모든 경로에는 같은 수의 검은색 노드가 있어야 한다.

## 삽입

> 연속해서 빨간색 노드가 오는 것이 문제
> 
- 노드의 색깔을 다시 칠하는 Recoloring이 있다. (규칙을 위반하지 않게 색을 다시 칠한다.)
- 새로운 노드를 삽입할 때는 무조건 빨간색을 칠한다. (5번 규칙을 위반하지 않기 위해서)
- rebalanceAFterInsertion 함수로 균형을 잡아준다.
새로운 노드가 루트 노드인 경우에는 검은색으로 칠한다.
부모노드와 삼촌노드가 빨간색인 경우 할아버지 노드를 빨간색으로 칠하고 삼촌노드와 부모노드를 검은색으로 칠한다.
(만약 증조 할아버지 노드도 빨간색인 경우 재귀적으로 올라가며 색깔을 바꿔준다.)
- 부모 노드는 빨간색, 삼촌 노드는 검은색, 새로운 노드는 안쪽으로 꺾인 노드인 경우
- 부모 노드는 빨간색, 삼촌 노드는 검은색, 새로운 노드는 바깥쪽으로 꺾인 노드인 경우

## 제거

> Black Height가 달라지는 것이 문제
> 
- 제거할 노드의 두 개의 자식 노드가 모두 NIL 노드인 경우
- 제거할 노드의 자식 노드 중 하나의 자식 노드가 NIL 노드인 경우
- 제거할 노드의 자식에서 NIL 노드가 한 개도 없는 경우
⇒ 제거할 노드 제거 후에 왼쪽 자식 노드 중 가장 큰 값으로 대체
(제거한 노드가 빨간 노드인 경우 Black Height가 달라지지 않아 문제가 없지만 검은 노드인 경우 Black Height가 달라져 문제가 생긴다.)

## 추상 자료형

**공통**

1. rotateLeft(node)
2. rotateRight(node)
3. replaceParentsChild(parent, oldChild, newChild)

**삽입**

1. insert(data)
2. rebalanceAfterInsertion(node) - 데이터 삽입 후 네 가지 경우로 나눠서 Recoloring
3. getUncle(parent) - 삼촌 노드를 구하는 함수

**제거**

1. remove(data)
2. removeWithZeroOrOneChild(node) - 제거할 노드가 1개 이하의 자식 노드를 가지고 있을 때 제거하는 함수
3. getBiggestNode(node) - 자식 노드 중 가장 큰 값을 가진 자식 노드 가져오기
4. rebalanceAfterDeletion(node)
5. getSibling(node) - 형제 노드를 구하는 함수
6. handleRedSibling(node, sibling) - 형제 노드의 색깔이 빨간색인 경우 처리하는 함수
7. isBlack(node) - 노드의 색깔이 검은색인지 판단하는 함수
8. handleBlackSiblingWithAtLastOneRedChild(node, sibling) - 형제 노드의 색깔이 검은색이고 검은색 형제 노드가 하나 이상의 빨간색 자식 노드를 가지고 있는 경우 균형을 맞추는 함수

## 구현 (삭제 구현 중간에 안됨. 14:00 부터)

> 이진 트리와 이진 탐색 트리를 변형해 구현한다.
> 

```tsx
// 이진 트리 변경 사항
const RED = false; // 추가
const BLACK = true; // 추가

class binaryTree {
	constructor(data) {
		this.data = data;
		this.leftSubTree = null;
		this.rightSubTree = null;
		this.parentTree = null; // 추가
		
		// 기본적으로 삽입할 경우 빨간색이므로 초기 색깔을 빨간색으로 설정.
		this.color = RED; 
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
	
	getParent() { // 추가
		return this.parentTree;
	}
	setParent(tree) { // 추가
		this.parentTree = tree;
	}
}

export {BinaryRee, RED, BLACK};
```

```tsx
class RedBlackTree {
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
	
	// 오른쪽으로 쭉 뻗은 경우 회전시키기
	rotateLeft(node) {
		let parent = node.getParent();
		let rightChild = node.getRightSubTree();
		
		// 회전 - 오른쪽 자식 노드의 왼쪽 자식노드를 오른쪽 자식 노드로 연결
		node.setRightSubTree(rightChild.getLeftSubTree());
		// 오른쪽 자식 노드의 왼쪽 자식노드가 존재하는 경우
		if(rightChild.getLeftSubTree() != null) {
			rightChild.getLeftSubTree().setParent(node);
		}
		
		rightChild.setLeftSubTree(node);
		node.setParent(rightChild);
		
		this.replaceParentsChild(parent, node, rightChild);
	}

	// 왼쪽으로 쭉 뻗은 경우 회전시키기 (위 경우와 완전히 반대로 구현하면 됨.)
	rotateRight(node) {
		let parent = node.getParent();
		let rightChild = node.getLeftSubTree();
		
		// 회전 - 오른쪽 자식 노드의 왼쪽 자식노드를 오른쪽 자식 노드로 연결
		node.setLeftSubTree(leftChild.getRightSubTree());
		// 오른쪽 자식 노드의 왼쪽 자식노드가 존재하는 경우
		if(leftChild.getRightSubTree() != null) {
			leftChild.getRightSubTree().setParent(node);
		}
		
		leftChild.setRightSubTree(node);
		node.setParent(leftChild);
		
		this.replaceParentsChild(parent, node, leftChild);
	}
	
	replaceParentsChild(parent, oldChild, newChild) {
		if(parent == null) {
			this.root = newChild;
		} else if (parent.getLeftSubTree() == oldChild) {
			parent.setLeftSubTree(newChild);
		} else if (parent.getRightSubTree() == oldChild) {
			parent.setRightSubTree(newChild);
		}
	
		if(newChild != null) {
			newChild.setParent(parent);
		}
	}
	
	insert(data) {
		let current = this.root;
		let parent = null;

		while(current != null) {
			parent = current;
			if(data < current.getData()){
				current = current.getLeftSubTree();
			} else if(data > current.getData()) {
				current = current.getRightSubTree();
			} else { // 중복된 숫자가 존재하는 경우니까 그냥 무시
				return;	
			}
		}
		
		let newNode = new BinaryTree(data);
		if(parent == null) {
			this.root = newNode;
		} else if (data < parent.getData()) {
			parent.setLeftSubTree(newNode);
		} else {
			parent.setRightSubTree(newNode);
		}
		
		newNode.setParent(parent);
		this.rebalanceAfterInsertion(newNode);
	}
	
	rebalanceAfterInsertioin(node) {
		let parent = node.getParent();
		
		// 새로운 노드가 루트노드인 경우
		if(parent == null) {
			node.color = BLACK;
			return;
		}
		
		// 다음 세 가지 경우는 모두 부모노드가 빨간색인 경우에 수행하므로
		if(parent.color == BLACK) {
			returnl
		}

		
		let uncle = this.getUncle(parent);
		let grandParent = parent.getParent();

		// 부모노드와 삼촌노드가 모두 빨간색인 경우
		// => 부모 노드와 삼촌 노드를 검음색으로, 할아버지 노드를 검은색으로
		// 그 후에 규칙 위반 위험이 있으니 할아버지 노드 상대로 재귀 호출
		if(uncle != null && uncle.color == RED) {
			parent.color = BLACK;
			parent.color = BLACK;
			grandParent.color = RED;
			this.rebalanceAfterInsertion(grandParent);
		
		} else if (this.isBlack(uncle) == true) {
			// 부모노드는 빨간색이고 삼촌 노드는 검은색, 새로운 노드는 안쪽 손자인 경우
			if(grandParent.getRightSubTree() == parent && parent.getLeftSubTree() == node) {
				this.rotateRight(parent);
				this.rotateLeft(grandParent);
				node.color = BLACK;
				grandParent.color = RED;
			} else if (grandParent.getLeftSubTree() == parent && parent.getRightSubTree() == node) {
				this.rotateRLeft(parent);
				this.rotateRight(grandParent);
				node.color = BLACK;
				grandParent.color = RED;
			
			// 부모노드는 빨간색이고 삼촌 노드는 검은색, 새로운 노드는 바깥쪽 손자인 경우
			} else if (grandParent.getRightSubTree() == parent && parent.getRightSubTree() == node) {
				this.rotateLeft(grandParent);
				parent.color = BLACK;
				grandParent.color = RED;
			} else if (grandParent.getRightSubTree() == parent && parent.getRightSubTree() == node) {
				this.rotateRight(grandPArent);
				parent.color = BLACK;
				grandParent.color = RED;
			}
		}
	
		
		
	}
	
	getUncle(parent) {
		let grandParent = parent.getParent();
		if(grandParent.getLeftSubTree() == parent) {
			return grandParent.getRightSubTree();
		} else if(grandParent.getRightSubTree() == parent) {
			return grandParent.getLeftSubTree();
		}
		return null;
	}

	isBlack(node) {
		return node == null || node.color == BLACK;
	}

	remove(data) {
		let currentNode = this.root;
		// 제거할 노드 찾기
		while(currentNode != null && currentNode.getData() != data) {
			if(data < currentNode.getData()) {
				currentNode = currentNode.getLeftSubTree();
			} else if(data > currentNode.getData()) {
				currentNode = currentNode.getRightSubTree();
			}
		}
		
		// 제거할 노드를 찾지 못한 경우 함수 종료 (Early return)
		if(currentNode == null) {
			return;
		}

		let replaceNode = null;
		let deleteNodeColor = RED;

		// 제거할 노드의 자식 노드가 1개 이하인 경우
		if(currentNode.getLeftSubTree() == null || currentNode.getRightSubTree() == null) {
			replaceNode = this.removeWithZeroOrOneChild(currentNode);
			deleteNodeColor = currentNode.color;
		} else {
			let succesor = this.getBiggestNode(currentNode.getLeftSubTree());
			currentNode = this.removeWithZeroOrOneChild(succesor);
			deleteNodeColor = currentNode.color;
		}
		
		// 제거된 노드가 검은색인 경우 균형 맞추기
		if(deleteNodeColor == BLACK) {
			this.rebalanceAfterDeletion(replaceNode);
			
			if(replaceNode instanceof NilNode) {
				this.replaceParentsChild(replaceNode.getParent(), replaceNode, null);
			}
		}
	}
	
	// 제거할 노드의 자식 노드가 1개 이해인 경우 처리 함수
	removeWithZeroOrOneChild(node) {
		// 왼쪽에 제거할 노드의 자식 노드가 존재하는 경우
		if(node.getLeftSubTree() != null) {
			this.replaceParentsChild(node.getParent(), node, node.getLeftSubTree());
			return node.getLeftSubTree();
		// 오른쪽에 제거할 노드의 자식 노드가 존재하는 경우
		} else if (node.getRightSubTree() != null) {
			this.replaceParentsChild(node.getParent(), node, node.getRightSubTree());
			return node.getRightSubTree();
		// 양쪽 자식노드가 없는 경우
		} else {
			let newChild = (node.color == BLACK) ? new NilNode() : null;
			this.replaceParentsChild(node.getParent(), node, newChild);
			return newChild;
		}
	}

	// 해당 노드의 왼쪽 자식노드 중에서 가장 큰 노드 찾기
	getBiggestNode(node) {
		while(node.getRightSubTree() != null) {
			node = node.getRightSubTree();
		}
	
		return node;
	}
	
	rebalanceAfterDeletion(node) {
		if(node == this.root) {
			mode.color = BLACK;
			return;
		}
	}
}

class NilNode extends BinaryTree {
	constructor() {
		super(0); // 부모 클래스 생성자 호출
		this.color = BLACK;
	}
}
```
