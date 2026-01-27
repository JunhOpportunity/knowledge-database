
# 덱

> head와 tail 양 쪽에서 자유롭게 삽입과 제거가 가능한 것
> 

<img width="543" height="109" alt="image" src="https://github.com/user-attachments/assets/e20e7f6e-a8d9-41a7-b0ff-8c8e9f44a80f" />

- 덱을 이용하면 스텍과 큐를 전부 구현할 수 있다

## 덱 구현하기

### 덱의 추상자료형

1. 모든 데이터 출력 - printAll
2. head에 데이터 삽입 - addFirst
3. head에서 데이터 제거 - removeFirst
4. tail에 데이터 삽입 - addLast
5. tail에서 데이터 제거 - removeLast
6. 리스트가 비어있는지 체크 - isEmpty

```jsx
class Deque {
	constructor() {
		this.list = new DoublyLinkedList();
	}

	printAll() {
		this.list.printAll();
	}
	addFirst(data) {
		this.list.insertAt(0, data);
	}
	removeFirst() {
		return this.list.deleteAt(0);
	}
	addLast() {
		this.list.insertAt(this.list.count, data);
	}
	removeLast() {
		return this.list.deleteLast()
	}
	isEmpty() {
		return (this.list.count == 0);
	}
}

export { Deque }
```
