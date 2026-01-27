---
title: "Set"
date: "2023-12-20"
category: "datastructure"
---
# Set

> 데이터의 중복을 허용하지 않는 것
> 

## Set 구현하기

Set은 해시 테이블의 Value는 사용하지 않고 Key만 사용해서 구현한다.

즉, 데이터가 Key 이자 value 이다.

### Set의 추상자료형

1. 데이터 삽입 - add(data)
2. 데이터 체크 - isContain(data)
3. 데이터 제거 - remove(data)
4. 셋 비우기 - clear()
5. 셋이 비었는지 확인 - isEmpty()
6. 셋의 모든 데이터 출력 - printAll()

```jsx
class HashSet{
	constructor() {
		this.hashTable = new HashTable();
	}
	
	add(data) {
		if(this.hashTable.get(data) == null) {
			this.hashTable.set(data, -1) // value는 쓰이지 않기 때문에 -1 저장.
		}
	}
	isContain(data) {
		return this.HashTable.get(data) != null;
	}
	remove(data) {
		this.hashTable.remove(data);
	}
	clear() {
		for(let i = 0; i < this.hashTable.arr.length; i++) {
			this.hashTable.arr[i].clear();
		}
	}
	isEmpty() {
		let empty = true;
		for(let i = 0; i < this.hashTable.arr.length; i++) {
			if(this.hashdTAble.arr[i].count > 0) {
				empty = false;
				break;
			}
		}
		return empty;
	}
	printAll() {
		for(let i = 0; i < this.hashTable.arr.length; i++) {
			let currentNode = this.hashTable.arr[i].head;
			while(currentNode != null) {
				console.log(currentNode.data.key);
				currentNode = currentNode.next;
			}
		}
	}
}

export { HashSet }
```
