---
title: "우선순위 큐"
date: "2024-01-06"
category: "datastructure"
---
# 우선순위 큐

`우선순위 큐`는 `큐`처럼 `연결리스트`를 사용해 구현할 수 있지만 `배열`이나 `연결리스트`는 매번 전체 요소를 정렬해야 하고 삽입과 삭제의 성능이 좋지 않으므로 `힙`으로 우선순위 큐를 구현한다. 

## 힙

> 완전 이진 트리(마지막 레벨을 제외한 모든 레벨의 노드들이 다 채워져 있지만 크기에 따른 정렬은 되어있지 않은 트리) + 우선순위가 지정되어 있는 트리
> 
- 힙의 삽입과 제거의 성능은 O(logn)이다.
- 힙은 우선순위가 가장 높은 노드를 제거하는데, 우선순위가 가장 높은 노드는 루트노드이다.

`최소 힙` : 루트 노드가 가장 작은 값을 가지는 완전 이진 트리

`최대 힙` : 루트 노드가 가장 큰 값을 가지는 완전 이진 트리

힙을 구현하기 위해서는 마지막으로 삽입된 노드의 위치를 기억해주는 변수가 존재해야 한다.

## 구현

> Red-Black 트리에서 사용된 조금 수정된 이진 트리 코드를 가져와 색상을 나타내는 변수를 제거해 사용
> 

```jsx
// 이진 트리
class binaryTree {
	constructor(data) {
		this.data = data;
		this.leftSubTree = null;
		this.rightSubTree = null;
		this.parentTree = null; // 추가
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

export {BinaryTree};
```

```jsx
// 힙
import { BinaryTree } from ""

class Heap{
	constructor(){
		this.root = null;
		this.lastInsertedNode = null;
	}
	
	insert(data) {
		// 마지막으로 삽입된 노드가 null이라면 데이터를 처음 삽입하는 경우이다.
		if(data.lastInsertedNode == null) {
			this.lastInsertedNode = new BinaryTree(data);
			this.root = this.lastInsertNode;
			return;
		}
		
		// getInsertingParent() : 새로 삽입될 노드 위치의 부모 노드를 리턴
		let getInsertingParent = this.getInsertingParent();
		let newNode = new BinaryTree(data);

		// 완전 이진 트리의 규칙에 따라 왼쪽부터 채운다.
		// 왼쪽이 빈 경우
		if(insertingParent.getLeftSubTree() == null) {
			insertingParent.setLeftSubTree() = newNode;
		// 오른쪽이 빈 경우
		} else if (insertingParent.getRightSubTree() == null) {
			insertingParent.setRightSubTree() = newNode;
		}
		newNode.setParent(insertingParent);
		this.lastInsertedNode = newNode;

		// 새로 삽입된 데이터가 부모 노드와 비교하며 우선 순위에 맞게 위치 찾아가기
		while(newNode.getParent() != null) {
			// 새로 삽입된 노드가 부모 노드보다 우선순위가 높은 경우
			// 부모 노드와 새로 삽입된 노드의 자리를 바꾸고 한단계 위로 올라감
			if(this.isBigPriority(newnode.getData(), newNode.getParent().getData()) == true) {
				let tempData = newNode.getParent().getData();
				newNode.getParent().setData(newNode.getData());
				newNode.setData(tempData);
				newNode = newNode.getParent();
			// 새로 삽입된 노드가 부모 노드보다 우선순위가 낮은 경우
			// 우선순위에 맞는 위치를 찾은 것이라서 위치 찾기 종료
			}else {
				break;
			}
		}
	}
	
	// 특정 데이터에 따라 조금씩 수정.
	// 우선순위 비교 (더 작은 값이 우선순위가 높은 경우로 구현)
	// first가 second 보다 작은 경우 true 리턴해서 우선순위가 더 높다고 알림
	isBigPriority(first, second) {
		return (first.priority < second.priority);
	}

	getInsertingParent() {
		// 마지막으로 삽입된 노드가 루트노드인 경우
		if(this.lastInsertedNode.getParent() == null) {
			return this.lastInsertedNode;
		
		} else {
			// 마지막으로 삽입된 노드가 부모노드의 왼쪽 자식노드인 경우
			if(this.lastInsertedNode = this.lastInsertedNode.getParent().getLeftSubTree()){
				return this.lastInsertedNode.getParent();
			// 마지막으로 삽입된 노드가 부모노드의 오른쪽 자식노드인 경우
			} else {
				let current = this.lastInsertedNode;
				let firstRightSibling = null; // 상위 노드중에 가장 처음 오른쪽 형제가 잇는 경우 이를 저장하기 위한 변수
				
				while(current.getParent().getParent() != null) {
					current = current.getParent();
					
					firstRightSibling = this.getRightSibling(current);
					// 오른쪽 자식노드가 비어있는 경우
					if(firstRightSibling != null) {
						break;
					}
				}

				// 오른쪽 형제노드가 존재하는 경우 왼쪽 서브트리로 계속 내려감.
				if(firstRightSibling != null) {
					while(firstRightSibling.getLeftSubTree() != null) {
						firstRightSibling = firstRightSibling.getLeftSubTree();
					}
					return firstRightSibling;

				// 오른쪽 형제노드가 존재하지 않는 경우 마지막 레벨이 꽉 차있는 경우니까 가장 왼쪽 아래 삽입
				} else {
					current = this.root;
					while(current.getLeftSubTree() != null) {
						current = current.getLeftSubTree();
					}
					return current;
				}
			}
		}
	}
	
	getLeftSibling(node) {
		if(node.getParent().getLeftSubTree() != null){
			return node.getParent().getLeftSubTree();
		}
		return null;
	}

	getRightSibling(node) {
		// 현재 노드가 왼쪽 자식노드인 경우
		if(node.getParent().getRightSubTree() != null){
			return node.getParent().getRightSubTree();
		}
		return null;
	}

	remove() {
		let deleteNode = null;
		
		// 루트 노드를 제거하는 경우 = 마지막 노드가 루트 노드인 경우
		if(this.lastInsertedNode == this.root) {
			deleteNode = this.root;
			this.lastInsertedNode = null;
			return deleteNode;
		}
		
		// 루트노드가 아닌 다른 노드를 제거하는 경우
		let prevLastInsertedNode = this.getNewLastInsertedNode();
		let tempData = this.root.getData();
		this.root.setData(this.lastInsertedNode.getData());
		this.lastInsertedNode.setData(tempData);
		
		// 부모 노드의 왼쪽 자식노드를 제거해야 하는 경우
		if(this.lastInsertedNode.getParent().getLeftSubTree() == this.lastInsertedNode) {
			this.lastInsertedNode.getParent().setLeftSubTree(null);
		// 부모 노드의 오른쪽 자식 노드를 제거해야 하는 경우
		} else {
			this.lastInsertedNode.getParent().setRightSubTree(null);
		}
		// 루트 노드와 마지막 삽입된 노드를 바꾼 뒤 마지막 삽입된 노드의 위치로 이동한 노드를 제거
		this.lastInsertedNode.setParent(null);
		deleteNode = this.lastInsertedNode;
		this.lastInsertedNode = prevLastInsertedNode;

		// 루트 노드로 이동한 마지막 삽입된 노드의 적절한 위치를 찾아서 이동시킴
		let current = this.root;
		do {
			let higherChild = this.getHigherPriorityChild(current.getLeftSubTree(), current.getRightSubTree());
			// 우선순위가 높은 자식노드가 없는경우
			if(higherChild == null) { 
				break;

			// current 노드와 우선순위가 높은 자식 중 current 노드의 우선순위가 더 낮은 경우 자식노드와 위치 변경
			} else if(this.isBigPriority(current.getData(), higherChild.getData()) == false){
				let tempData = current.getData();
				current.setData(higherChild.getData());
				higherChild.setData(tempData);
				current = higherChild;

			// current 노드의 우선순위가 더 높은 경우 제자리를 찾은 것
			} else {
				break;
			}
		} while(current.getLeftSubTree() != null || current.getRightSubTree() != null)
		
		return deletedNode;
	}

	getHeigherPriorityChild(left, right) {
		// 왼쪽 노드가 없다면 오른쪽 노드의 우선순위가 높다고 판단하고 오른쪽 노드 리턴
		if(left == null) {
			return right;
		} else if(right == null){
			return left;
		} else if(this.isBigPriority(left.getData(), right.getData())) {
			return left;
		} else {
			return right;
		}
	}

	// 마지막으로 삽입된 노드의 위치를 기준으로 이전 노드의 위치를 찾는 함수
	getNewLastInsertedNode() {
		let preLastInsertedNode = null;
		
		// 마지막에 삽입된 노드가 부모의 왼쪽 자식노드인 경우
		if(this.lastInsertedNode == this.lastInsertedNode.getParent().getLeftSubTree()){
			let current = this.lastInsertedNode;
			let firstLeftSibling = null;
			while(current.getParent().getParent() != null) {
				current = current.getParent();
				firstLeftSibling = this.getLeftSibling(current);
				if(firstLeftSibling != null) {
					break
				}
			}

			// 부모 노드의 왼쪽 형제 노드가 존재하는 경우
			if(firstLeftSibling != null) {
				while(firstLeftSibling.getRightSubTree() != null) {
					firstLeftSibling = firstLeftSibling.getRightSubTree();
				}
				prevLastInsertedNode = firstLeftSibling;
			// 부모 노드의 왼쪽 형제 노드가 존재하지 않는 경우 - 오른쪽 끝까지 내려감
			} else {
				current = this.root;
				while(current.getRightSubTree() != null) {
					current = current.getRightSubTree();
				}
				prevLastInsertedNode = current
			}

		// 마지막에 삽입된 노드가 부모의 오른쪽 자식노드인 경우
		} else {
			prevLastInsertedNode = this.lastInsertedNode.getParent().getLeftSubTree();
		}

		return prevLastInsertedNode;
	}
}

export { Heap }; 
```

```jsx
// 우선순위 큐
class PriorityQueue{
	constructor() {
		this.heap = new Heap();
		// 우선순위를 변경하고 싶은 경우
		this.heap.isBigPrioity = function(first, second) {
			return (first.priority > second.priority);
		}
	}

	enqueue(data) {
		this.heap.insert(data);
	}
	
	dequeue() {
		return this.heap.remove();
	}
}

// 사용
class Monster{
	constructor(name, health){
		this.name = name;
		this.health = health;
		this.priority = health;
	}
}

let priorityQueue = new PriorityQueue();
priorityQueue.enqueue(new Monster("슬라임", 100))
priorityQueue.enqueue(new Monster("슬라임", 90))
priorityQueue.enqueue(new Monster("슬라임", 80))
priorityQueue.enqueue(new Monster("슬라임", 70))
```

```jsx
// 힙 정렬 알고리즘 : O(nlogn) 의 성능
// 삽입과 제거의 성능은 O(logn) 이지만 정렬은 데이터 n개에 대해 삽입과 제거가 이루어지기 때문이다.
// 현재 힙은 최소 힙 이다. (오름차순으로 제거되면서 정렬됨.)
import {Heap} from ""

class MyData{
	constructor(data) {
		this.data = data;
		this.priority = data;
	}
}

let heap = new Heap();

heap.insert(new MyData(2));
heap.insert(new MyData(8));
heap.insert(new MyData(5));
heap.insert(new MyData(6));
heap.insert(new MyData(10));
heap.insert(new MyData(4));
heap.insert(new MyData(3));
heap.insert(new MyData(7));
heap.insert(new MyData(9));
heap.insert(new MyData(1));

let arr = [];

arr.push(heap.remove());
arr.push(heap.remove());
arr.push(heap.remove());
arr.push(heap.remove());
arr.push(heap.remove());
arr.push(heap.remove());
arr.push(heap.remove());
arr.push(heap.remove());
arr.push(heap.remove());
arr.push(heap.remove());

console.log(arr);
// [{1}, {2}, {3}, {4}, {5}, {6}, {7}, {8}, {9}, {10}]
```
