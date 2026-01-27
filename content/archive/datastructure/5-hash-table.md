---
title: "해시 테이블"
date: "2023-12-19"
category: "datastructure"
---

# 🤔해시 테이블

> 해시 함수로 테이블을 다시 만드는 것
> 

<img width="412" height="591" alt="image" src="https://github.com/user-attachments/assets/20e3373c-1457-49f3-b5e8-78837d980d6b" />

- 해시 테이블은 Key 만 알면 Value에 O(1)의 성능으로 읽기, 삽입, 수정, 삭제가 가능하다
- 해시 테이블은 언어 별로 다른 이름으로도 불린다
    - 해시
    - 맵
    - 해시맵
    - 딕셔너리
- 해시 함수의 결과로 특정 Key에 이미 Value가 존재하는 경우를 `충돌` 이라고 부르는데, 이 경우에는 해당 인덱스를 연결 리스트로 구성해 데이터를 연결하면서 해결한다.
- 좋은 해시 함수는 데이터를 골고루 분배시켜주는 함수이다.
- 해시 테이블은 빠른 데이터 읽기, 삽입, 삭제가 가능하다는 장점이 있지만 메모리를 많이 차지한다는 단점을 갖고 있다.

## 해시 테이블 구현하기

### 해시 테이블의 추상자료형

1. 데이터 삽입 - set
2. 데이터 읽기 - get
3. 데이터 제거 - remove

```jsx
class HashData{
	constructor(key, value) {
		this.key = key;
		this.value = value;
	}
}

class HashTable{
	constructor() {
		this.arr = []
		for (let i = 0; i < 10; i++) { // 0~9 까지 연결리스트를 가진 해시 테이블 생성
				this.arr[i] = new DoublyLinkedList();
			}
	}
	
	hashFunction(number) { // 필요한 해시 함수 설정
		return number % 10;
	}
	set(key, value) {
		this.arr[this.hashFunction(key)].insertAt(0, new HashData(key, value));
	}
	get(key) { // 해당 인덱스의 해시 데이터들 중 key와 일치하는 데이터 검색
		let currentNode = this.arr[this.hashFunction(key)].head;
		while(currentNode != null) {
			if(currentNode.date.key == key) {
				return currentNode.data.value;
			}
		}
		return null;
	}
	remove(key) {
		let list = this.arr[this.hashFunction(key)];
		let currentNode = list.head;
		let deletedIndex = 0;
		while(currentNode != null) {
			if(currentNode.data.key = key) {
				return list.deleteAt(deletedIndex);
			}
			currentNode = currentNode.next;
			deletedIndex++;
		}
		return null;
	}
}
```

## Set
