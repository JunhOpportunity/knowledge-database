---
title: "3주차: 스택, 큐, 해쉬"
date: "2025-09-17"
category: "코딩테스트"
---
# 3주차
## 스택

> 한쪽 끝으로만 자료를 넣고 뺄 수 있는 자료구조
> 
- push(data) : 맨 앞에 데이터 넣기
- pop() : 맨 앞에 데이터 뽑기
- peek() : 맨 앞에 데이터 보기
- isEmpty() : 스택이 비었는지 확인하기

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class Stack:
    def __init__(self):
        self.head = None

    def push(self, value):
        new_node = Node(value)
        if self.head == None:
            self.head = new_node
            return new_node.data
        new_node.next = self.head
        self.head = new_node

        return new_node.data

    # pop 기능 구현
    def pop(self):
        # cur = self.head
        # cur_next = cur.next
        #
        # self.head.next = None
        #
        # self.head = cur_next
        #
        # return cur.data # 마지막 데이터 반환
        if self.is_empty():
            return "Stack is Empty"
        delete_head = self.head
        self.head = self.head.next
        return delete_head

    def peek(self):
        if self.is_empty():
            return "Stack is empty!"

        return self.head.data

    # isEmpty 기능 구현
    def is_empty(self):
        return self.head is None
```

## 큐

실제로 코딩테스트에서 큐를 사용할 때는 `collections.deque` 를 사용해야 한다.

```jsx
from collections import deque
queue = deque()
queue.append(3) # enqueue
queue.popleft() # dequeue
```

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class Queue:
    def __init__(self):
        self.head = None
        self.tail = None

    def enqueue(self, value):
        new_node = Node(value)
        if self.head == None:
            self.head = self.tail = new_node
            return new_node.data
        else:
            self.tail.next = new_node
            self.tail = new_node
        return new_node.data

    def dequeue(self):
        if self.is_empty():
            return print("현재 큐가 비어있습니다.")
        
        deleted_node = self.head
        self.head = self.head.next
        deleted_node.next = None

        return deleted_node.data

    def peek(self):
        if self.is_empty():
            return print("현재 큐가 비어있습니다.")
        return self.head.data

    def is_empty(self):
        return self.head == None
```

## 🤚해쉬

> 자바스크립트 객체같은 구조, 데이터의 검색과 저장이 매우 빠르다
> 

해쉬 함수를 이용해 키에 대한 숫자를 만들고 이 숫자를 배열 길이로 나누어서 인덱스를 생성하는 방식이다.

```jsx
index = hash(key) % len(배열)
배열[index] = value
```

그런데 이렇게 되면 해쉬의 값이 같거나  index의 값이 똑같이 만들어질 경우 충돌이 발생한다.

### 1. 충돌 방지 - Chaning

이 문제를 해결하는 첫 번째 방법이 링크드 리스트를 이용한 Chaning 기법이다.

![alt text](image-2.png)

![alt text](image-3.png)

배열 안에 각각 링크드 리스트를 넣어둔 형태인데 쉽게 말해서 2차원 배열이라고 보면 된다.

```jsx
import java.util.ArrayList;
import java.util.List;

class DictChaining {

    static class LinkedTuple {
        private List<KeyValuePair> items;
        
        public LinkedTuple() {
            this.items = new ArrayList<>();
        }
        
        public void add(String key, Object value) {
            this.items.add(new KeyValuePair(key, value));  // ["333", 7] -> ["77", 6]
        }
        
        public Object get(String key) {
            for (KeyValuePair pair : items) {
                if (pair.key.equals(key)) {
                    return pair.value;
                }
            }
            return null;
        }
        
        static class KeyValuePair {
            String key;
            Object value;
            
            public KeyValuePair(String key, Object value) {
                this.key = key;
                this.value = value;
            }
        }
    }
    
    public static void main(String[] args) {
        LinkedTuple linkedTuple = new LinkedTuple();
        
        linkedTuple.add("333", 7);
        linkedTuple.add("77", 6);
        System.out.println(linkedTuple.get("333"));
    }
}
```

|  | Stack | Queue | Hash Table |
| --- | --- | --- | --- |
| 특정 원소 조회 | O(N) | O(N) | Ω(1)  |
| 중간에 삽입 삭제 | O(N) | O(N) | Ω(1)  |
| 데이터 추가 | O(1) | O(1) | Ω(1)  |
| 정리 | 가장 최근에 들어온 순서대로 처리되어야 하는 일에 필요하다. → Ctrl Z | 들어온 순서대로 처리되어야 하는 일에 필요하다. → 줄서서 받아먹는 경우 | 해시 충돌이 잘 안 일어날 수 있는 상황에서는 매우 좋다. 따라서 해시 함수가 잘 데이터를 흩뿌려줄 수 있는 걸 골라야 한다. |

### 2. 충돌 방지 - 남는 공간에 넣기

```jsx
all_students = ["나연", "정연", "모모", "사나", "지효", "미나", "다현", "채영", "쯔위"]
present_students = ["정연", "모모", "채영", "쯔위", "사나", "나연", "미나", "다현"]

# 반복문
def get_absent_student(all_array, present_array):
		result = []
    for x in all_array:
	    exist = False
	    for y in present_array:
		    if x == y
			    break
			if exist = False:
				result.append(x)
    return result
 
 # 딕셔너리 사용
 def get_absent_student(all_array, present_array):
    dict = {}
    for key in all_array:
        dict[key] = True  # 아무 값이나 넣어도 상관 없습니다! 여기서는 키가 중요한거니까요

    for key in present_array:  # dict에서 key 를 하나씩 없앱니다
        del dict[key]

    for key in dict.keys():  # key 중에 하나를 바로 반환합니다! 한 명 밖에 없으니 이렇게 해도 괜찮습니다.
        return key

print(get_absent_student(all_students, present_students))

print("정답 = 예지 / 현재 풀이 값 = ",get_absent_student(["류진","예지","채령","리아","유나"],["리아","류진","채령","유나"]))
print("정답 = RM / 현재 풀이 값 = ",get_absent_student(["정국","진","뷔","슈가","지민","RM"],["뷔","정국","지민","진","슈가"]))
```