---
title: "2주차: 클래스, 배열, 링크드 리스트, 이진 탐색"
date: "2025-09-12"
category: "코딩테스트"
---

# 2주차

## 클래스

```jsx
class Person:
	def __init__(self, param_name):
		print("test", self) // self 는 자기 자신을 의미
		self.name = param_name
		
	def talk(self):
		print("안녕하세요 저는", self.name, "입니다")

person_1 = Person("test1")
person_2 = Person("test2")
```

## Array

- 배열의 경우에는 위치를 바꾸기 위해 하나하나 이동해야한다.
- 각 원소에 즉시 접근할 수 있다. (인덱스를 통해)
- 배열은 크기가 정해진 데이터 공간이다.

## Linked List

- 원소에 접근하기 위해 이전 원소들을 거쳐서 지나가야한다.
- 링크드 리스트는 크기가 정해지지 않은 데이터 공간이다. 따라서 자유롭게 늘리거나 줄일 수 있다.
- 각 원소는 노드, 연결되는 부분은 포인터라고 한다.
- 링크드 리스트는 맨 첫 번째 노드인 head node를 head에 저장한다.

### 구현

- 노드
- 포인터

```python
class Node:
	def __init__(self, data):
		self.data = data
		self.next = None

class LinkedList:
    def __init__(self, value):
        self.head = Node(value)

    def append(self, value):
        cur = self.head
        while cur.next != None:
            cur = cur.next
        cur.next = Node(value)

    def print_all(self):
        cur = self.head
        while cur != None:
            print(cur.data)
            cur = cur.next

    def get_node(self, index):
        # 최대 길이를 모름
        cur = self.head
        count = 0
        while cur != None:
            if count == index:
                return cur
            else:
                count += 1
                cur = cur.next

        # 강의에서 제공한 방식
        # while count != index:
        #     cur = cur.next
        #     count += 1

        return cur

    def add_node(self, index, value):
        # A -> B -> C
        # A -> B -> X -> C (B.next = X, X.next = C)
        new_node = Node(value)
        if index == 0:
            new_node.next = self.head
            self.head = new_node
            return

        prev_node = self.get_node(index - 1)
        next_node = prev_node.next
        prev_node.next = new_node
        new_node.next = next_node

        # cur = self.head
        # count = 0
        # while count != index:
        #     cur = cur.next
        #     count += 1
        # prev = cur
        # cur = Node(value)
        #
        # cur.next = prev.next
        # prev.next = cur.head

    def delete_node(self, index):
        if(index == 0):
            self.head = self.head.next

        prev_node = self.get_node(index-1)
        next_node = self.get_node(index+1)
        prev_node.next = next_node
```

## 정리

| Array | Linked List |
| --- | --- |
| 데이터에 접근하는 경우가 빈번하다면 Array 사용이 유리하다. | 삽입과 삭제가 빈번하다면 Linked List 사용이 유리하다. |

## 이진 탐색

이진 탐색은 반드시 정렬된 배열의 경우에만 수행이 가능하다.

연산량이 절반씩 낮아지는 경우 대부분O(log(N)) 이라고 생각하면 된다.

```python
finding_target = 14
finding_numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16]

def is_existing_target_number_binary(target, array):
    current_min = 0
    current_max = len(array) - 1
    current_guess = (current_min + current_max) // 2

    while current_min <= current_max:
        if array[current_guess] == target:
            return True
        elif array[current_guess] < target:
            current_min = current_guess + 1
        else:
            current_max = current_guess - 1
        current_guess = (current_min + current_max) // 2

    return False

result = is_existing_target_number_binary(finding_target, finding_numbers)
print(result)
```

## 재귀 함수

자기 자신을 호출하는 함수

반드시 빠져나가는 지점을 지정해주어야한다.