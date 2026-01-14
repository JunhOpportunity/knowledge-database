---
title: "4주차: 트리, 힙, 그래프"
date: "2025-09-22"
category: "코딩테스트"
---

# 4주차

## 트리

> 비선형 자료구조
> 

| 이진트리 | 완전 이진트리 |
| --- | --- |
| 최대 두 개의 자식 | 왼쪽 노드부터 차례로 삽입 |

## 완전 이진 트리

배열을 통해서 완전 이진트리를 나타낼 수 있다. (0 번째 인덱스 자리는 비워둔다 None)

- 현재 인덱스 * 2 = 왼쪽 자식의 인덱스
- 현재 인덱스 * 2 + 1 = 오른쪽 자식의 인덱스
- 현재 인덱스 // 2 = 부모의 인덱스

## 힙

> 특정 순서에 맞춰 항상 데이터를 정렬해두는 자료구조
> 

항상 큰 값이 상위 레벨에 있고 작은 값이 하위 레벨에 있어야 한다 

즉, 부모 노드의 값이 자식 노드의 값보다 항상 커야한다 (Max heap)

최솟값이 맨 위로 가는 힙도 있다 (Min Heap)

힙도 마찬가지로 완전 이진트리 구조이다

### 원소 삽입

```jsx
class MaxHeap:
    def __init__(self):
        self.items = [None]

    def insert(self, value):
        self.items.append(value)
        cur_index = len(self.items) - 1

        while cur_index > 1:  # cur_index 가 1이 되면 정상을 찍은거라 다른 것과 비교 안하셔도 됩니다!
            parent_index = cur_index // 2
            if self.items[parent_index] < self.items[cur_index]:
                self.items[parent_index], self.items[cur_index] = self.items[cur_index], self.items[parent_index]
                cur_index = parent_index
            else:
                break

```

### 원소 제거

힙에서 원소를 제거할 때 항상 루트 노드를 삭제한다

1. 루트 노드와 맨 끝에 있는 원소를 교체
2. 맨 뒤로 이동한 루트 노드 원소를 삭제
3. 변경된 노드와 자식 노드들을 비교해서 더 큰 자식노드가 있다면 자리 변경

※ 여기서 주의해야 할 점은, 두 자식 중 더 큰 자식과 부모를 바꾸어야 한다는 점이다

```python
    def delete(self):
        heap_length = len(self.items) - 1
        self.items[1], self.items[heap_length] = self.items[heap_length], self.items[1]

        deleted_node = self.items.pop(heap_length)
        heap_length -= 1

        cur_index = 1
        max_index = cur_index

        while cur_index*2 <= heap_length:
            if cur_index*2 <= heap_length and self.items[cur_index] < self.items[cur_index*2]:
                max_index = cur_index*2
            if cur_index <= heap_length and self.items[cur_index] < self.items[cur_index*2+1]:
                max_index = cur_index*2+1
            if max_index == cur_index:
                break

            self.items[cur_index], self.items[max_index] = self.items[max_index], self.items[cur_index]
            cur_index = max_index

        return deleted_node
```
## 그래프

인접 행렬과 인접 리스트 두 가지로 구현이 가능한데,

인접 행렬은 탐색에 빠르지만 많은 공간이 필요하고

인접 리스트는 적은 공간을 사용하지만 탐색에 많은 시간이 필요하다


