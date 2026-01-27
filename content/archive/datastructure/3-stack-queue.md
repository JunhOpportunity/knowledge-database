---
title: "스택과 큐"
date: "2023-12-18"
category: "datastructure"
---

# 스택

> FILO (First In Last Out)  가장 먼저 들어간 데이터가 가장 나중에 나오는 것
> 

<img width="630" height="262" alt="image" src="https://github.com/user-attachments/assets/331e6381-6846-46ec-ad99-ff1760940d02" />


- `Ctrl + Z` 가 단순히 이전 작업으로 이동하는 것이 아니라, 스택 작업에서 가장 최근에 수행되었던 작업을 삭제하는 것이다.

## 스택 구현하기

스택은 구현하기가 아주 간단하다.
만약 연결 리스트로 스택을 구현한다면, 무조건 첫 번째인 head 자리에 추가하거나 제거하면 된다.

### 스택의 추상자료형

1. 데이터 삽입 - push
2. 데이터 제거 - pop
3. Head 데이터 참조 - pick
4. 스택이 비어있는지 확인 - isEmpty

```jsx
class Stack{
	constructor() {
		this.list = new LinkedList(); // 빈 연결리스트 생성
	}
	push(data) {
		this.list.insertAt(0, data); // 항상 Head에 삽입
	}
	pop() {
		try{
			return this.list.deleteAt(0); // 항상 index 0 제거
		} catch(error) { // 배열이 비어있는 경우 null 반환
			return null;
		}
	}
	pick() { 
		return this.list.getNodeAt(0);
	}
	isEmpty() {
		return (this.list.count == 0);
	}
}

export { Stack }

// 스택 테스트
import { Stack } from "";
let stack = new Stack();

stack.push(1);
stack.push(2);
stack.push(3);

console.log(stack.pop().data); // 3
console.log(stack.pick().data); // 2
stack.isEmpty(); // false
console.log(stack.pop()); // 2
console.log(stack.pop()); // 1
stack.isEmpty(); // true
console.log(stack.pop()); // Error => null
```

# 🤔큐

> FIFO(First In First Out) 먼저 들어간 데이터가 먼저 나오는 것
> 

<img width="536" height="119" alt="image" src="https://github.com/user-attachments/assets/d635345a-48b9-46b1-a42c-be0fa70dbfdc" />


- 놀이기구를 탈 때 줄을 서서 먼저 온 순서대로 탑승하는 것
- 운영체제에서 프로세스의 작업 요청을 들어온 순서대로 큐에 넣고 CPU가 순서대로 꺼내서 처리 함. (FIFO 스케줄링)

큐는 일반적인 단방향 연결 리스트를 사용해서 기능을 구현하기는 쉽지 않다. 따라서 양방향 연결 리스트로 기능을 추가해서 구현해야 한다.

## 큐 구현하기

### 큐의 추상자료형

1. 데이터 삽입 - enqueue
2. 데이터 제거 - dequeue
3. 가장 먼저 삽입된 데이터 참조 - front
4. 큐가 비었는지 확인 - isEmpty

```jsx
import { DoublyLinkedList } from "";

class Queue {
	constructor() {
		this.list = new DoublyLinkedList();
	}
	
	enqueue(data) {
		this.list.insertAt(0, data);
	}
	dequeue() {
		try () {
			return this.list.deleteLast();
		} catch (e) {
			return null;
		}
	}
	front() {
		return this.list.tail;
	}
	isEmpty() {
		return (this.list.count == 0);
	}
}

export { Queue }
```

```jsx
// DoublyLinkedList.js (양방향 연결 리스트)
class Node{
	constructor(data, next = null, prev = null) {
		this.data = data;
		this.next = next;
		this.prev = prev;
	}
}

class DoublyLinkedList{
	constructor() {
		this.head = null; // 연결리스트의 시작 노드를 가리키는 것
		this.tail = null; // 연결리스트의 마지막 노드를 가리키는 것
		this.count = 0; // 총 저장된 노드의 수를 저장하는 것
	}

	printAll() { // 모든 연결리스트의 데이터를 출력하는 메서드
		let currentNode = this.head;
		while(currentNode != null) {
			console.log(currentNode.data);
			currentNode = current.next;
		}
	}
	
	clear() {
		this.head = null;
		this.count = 0;
	}

	insertAt(index, data) { // 처음 또는 중간에 삽입하는 메서드
		if(index > this.count || index < 0) {
			throw new Error("잘못된 범위를 선택하였습니다.")	
		}
		let newNode = new Node(data);

		if(index == 0) { // 처음 위치에 삽입되는 경우
			newNode.next = this.head; // 삽입 이전의 처음 위치 노드가 새로 삽입될 노드의 다음
			if(this.head != null) { // head 는 계속해서 마지막으로 추가한 노드가 됨
				this.head.prev = newNode;
			}
			this.head = newNode;
		} else if (index == this.count) { // tail에 노드를 추가하는 경우
			newNode.next = null;
			newNode.prev = this.tail;
			this.tail.next = newNode;
		} else { // 처음이 아닌 위치에 삽입되는 경우
			let currentNode = this.head;
			for(let i = 0; i < index - 1; i++){ // 원하는 위치 이전 노드까지 이동
				currentNode = currentNode.next;
			}
			newNode.next = currentNode.next // 새로운 노드의 다음 노드가 원하는 위치 이전 노드의 다음 노드(밀려날 노드)를 가리킴
			newNode.prev = currentNode; // 새로운 노드의 이전 노드가 밀려난 노드의 이전 노드임을 가리킴
			current.next = newNode; // 이전 노드가 새로운 노드를 가리킴
			newNode.next.prev = newNode; // 밀려난 노드의 이전 노드를 새로 삽입한 노드로 가리킴
		}
		if(newNode.next == null) { // 새로 삽입한 노드가 가장 먼저 들어간 위치인 경우 tail로 설정
			this.tail = newNode
		}
		this.count++; // 노드가 삽입되었으니 개수를 하나 증가시킨다.
	}
	
	insertLast(data) {
		this.insertAt(this.count, data);
	}

	deleteAt(index) {
		if(index > this.count || index < 0) {
			throw new Error("잘못된 범위를 선택하였습니다.")	
		}
		let currentNode = this.head;

		if(index == 0) { // 첫 번째 노드 제거
			let deleteNode = this.head;
			if(this.head.next == null) { // 노드가 하나 남았을 때
				this.head = null;
				this.tail = null;
			} else { // 노드가 두개 이상 남았을 때
				this.head = this.head.next;
				this.head.prev = null;
			}
			this.count--;
			return deleteNode; // 삭제된 노드 반환
		} else if (index == this.count - 1) { // 마지막 노드 삭제
			let deletedNode = this.tail;
			this.tail.prev.next = null;
			this.count --;
			return deleteNode;
		} else {
			for(let i = 0; i < index - 1; i++) {
				currentNode = currentNode.next;
			}
			let deleteNode = currentNode.next;
			currentNode.next = deleteNode.next;
			currentNode.next.prev = currentNode;
			this.count--;
			return deleteNode;
		}
	}

	deleteLast() {
		return this.deleteAt(this.count - 1);
	}

	getNodeAt(index) {
		if(index > this.count || index < 0) {
			throw new Error("잘못된 범위를 선택하였습니다.")	
		}
		let currentNode = this.head;
		for(let i = 0; i < index; i++) {
			currentNode = currentNode.next;
		}
		return currentNode;
	}
}
****
export { Node, DoublyLinkedList }
```

## 덱
