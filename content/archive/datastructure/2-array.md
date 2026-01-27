---
title: "배열과 연결리스트"
date: "2023-12-15"
category: "datastructure"
---

# 배열

## 일반적인 언어들의 배열

<img width="545" height="180" alt="image" src="https://github.com/user-attachments/assets/93c1046b-056f-433e-9830-77866b8e5387" />


배열을 선언할 때는 크기 만큼의 공간이 할당되는데. 데이터가 들어가 있지 않은 공간은 일명 쓰레기값으로 채워진다.

배열의 인덱스 참조는 길이에 상관없이 한 번에 가져오기 때문에 **O(1)의 성능**이다.
따라서 배열은 읽기/쓰기 에서 좋은 성능을 보인다.

하지만 배열은 데이터 삽입과 삭제, 추가 등의 성능은 좋지 않다.

만약 배열의 크기보다 많은 값을 할당했다면, 해당 값의 개수만큼 연속적인 공간을 찾아서 그 공간을 할당한 뒤 새로 복사를 해줘야 하므로 성능에 문제가 된다.

그렇다고 이를 예방하기 위해 엄청나게 많은 공간을 할당하는 경우에는 전부 사용하지 않기도 하고 메모리가 엄청나게 낭비되므로 좋지 않다.

## 자바스크립트의 배열

<img width="545" height="162" alt="image" src="https://github.com/user-attachments/assets/888b35a0-f2bd-438c-b58a-e1afa2dd8c0a" />


자바스크립트는 불연속적으로 메모리를 연결해서 배열처럼 보이게 만든다.

## 연결리스트

연결리스트는 배열의 일정 크기 이상 할당할 경우 발생하는 문제점에 대한 내용을 보완할 수 있는 자료구조이다.

마치 자바스크립트의 배열처럼 메모리 공간을 연속적으로 할당하지 않고 불연속적으로 분산해 할당한 뒤 연결한다.
이때 각각의 분산된 공간이 `노드`이다.

<img width="458" height="171" alt="image" src="https://github.com/user-attachments/assets/169efa76-ad4a-445a-8edb-925ea10764c6" />

노드는 `데이터를 담는 변수`와 `다음 노드를 가리키는 변수`를 가지고 있다.

연결리스트는 첫 노드의 주소만 알고 있으면 다른 모든 노드에 접근할 수 있다.

연결리스트는 배열을 선언할 때 초기 크기를 알려주지 않아도 된다는 장점이 있다.

게다가 연결리스트는 중간에 데이터를 삽입 또는 삭제하는 경우에 다음 노드를 가리키는 변수만 바꿔주며 연결해주면 되기 때문에 데이터 삽입과 삭제가 간단해진다.

연결리스트에서 데이터 참조는 O(n)의 성능을 가진다.

연결리스트에서 배열은 메모리에서 힙 영역에 런타임의 불연속적인 빈 공간에 할당한다.

# 연결리스트 구현해보기

## 추상 자료형

어떠한 데이터와 그 데이터에 대한 연산을 표기하는 것

## 연결리스트의 추상 자료형

1. 모든 데이터 출력 - printAll()
2. 모든 데이터 제거  - clear()
3. 인덱스 삽입 - insertAt(index, data)
4. 마지막 삽입 - insertLast(data)
5. 인덱스 삭제 - deleteAt(index)
6. 마지막 삭제 - deleteLast()
7. 인덱스 읽기 - getNodeAt(index)

```jsx
// LinkedLList.js
class Node{
	constructor(data, next = null) {
		this.data = data;
		this.next = next;
	}
}

class LinkedList{
	constructor() {
		this.head = null; // 연결리스트의 시작 노드를 가리키는 것
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
			this.head = newNode;
		} else { // 처음이 아닌 위치에 삽입되는 경우
			let currentNode = this.head;
			for(let i = 0; i < index - 1; i++){ // 원하는 위치 이전 노드까지 이동
				currentNode = currentNode.next;
			}
			newNode.next = current.next // 새로운 노드의 다음 노드가 원하는 위치 이전 노드의 다음 노드(밀려날 노드)를 가리킴
			current.next = newNode; // 이전 노드가 새로운 노드를 가리킴
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
			this.head = this.head.next;
			this.count--;
			return deleteNode; // 삭제된 노드 반환
		} else {
			for(let i = 0; i < index - 1; i++) {
				currentNode = currentNode.next;
			}
			let deleteNode = currentNode.next;
			currentNode.next = deleteNode.next;
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
export { Node, LinkedList }

// 실행파일에서 노드 생성
import { Node, LinkedList } from ''

// 노드 테스트
let node1 = new Node(1);
let node2 = new Node(2);
let node3 = new Node(3);

node1.next = node2;
node2.next = node3;

// 연결리스트 테스트
let list = new LinkedList();

list.insertAt(0, 0);
list.insertAt(1, 1);
list.insertAt(2, 2);
list.insertAt(3, 3);
list.insertAt(4, 4);
list.printAll(); // 0 1 2 3 4
list.clear() // 
list.insertLast()
```
