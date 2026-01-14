---
title: "3주차: 버블, 선택, 삽입, 병합 정렬"
date: "2025-09-16"
category: "코딩테스트"
---

# 3주차

## 버블 정렬

```python
input = [4, 6, 2, 9, 1]

def bubble_sort(array):
    for index_x in range(len(array) - 1):
        for index_y in range(len(array) - index_x - 1):
            if array[index_y] > array[index_y + 1]:
                array[index_y], array[index_y + 1] = array[index_y + 1], array[index_y]

    return array

bubble_sort(input)
print(input)  # [1, 2, 4, 6, 9] 가 되어야 합니다!

print("정답 = [1, 2, 4, 6, 9] / 현재 풀이 값 = ",bubble_sort([4, 6, 2, 9, 1]))
print("정답 = [-1, 3, 9, 17] / 현재 풀이 값 = ",bubble_sort([3,-1,17,9]))
print("정답 = [-3, 32, 44, 56, 100] / 현재 풀이 값 = ",bubble_sort([100,56,-3,32,44]))
```

## 선택 정렬

> 선택해서 정렬한다. 즉, 제일 큰 애 또는 제일 작은 애를 선택해서 처음이나 마지막에 배치하는 것을 반복하는 방식
> 

🤚 주의해야 할 점 : 이 부분은 범위를 n - x 로 잡는다는 것에 유의해야 한다.

왜냐하면 맨 앞부터 정렬되어가기 때문에 해당 인덱스를 제외하고 뒤로 이동하며 확인하기 때문이다.

그림으로 설명하면 좀 더 쉬울 것 같다.
![alt text](image.png)

```python
input = [4, 6, 2, 9, 1]

def selection_sort(array):
    n = len(array)
    for x in range(n - 1): # x = 0
        for y in range(n - x): # y = 0 ~ 5 - 0 - 1 = 4
            if array[x] > array[y + x]:
                array[x], array[y + x] = array[y + x], array[x]

    return array

selection_sort(input)
print(input) # [1, 2, 4, 6, 9] 가 되어야 합니다!

print("정답 = [1, 2, 4, 6, 9] / 현재 풀이 값 = ",selection_sort([4, 6, 2, 9, 1]))
print("정답 = [-1, 3, 9, 17] / 현재 풀이 값 = ",selection_sort([3,-1,17,9]))
print("정답 = [-3, 32, 44, 56, 100] / 현재 풀이 값 = ",selection_sort([100,56,-3,32,44]))
```

## 🤚삽입 정렬

> 범위를 조금씩 늘리면서 올바른 위치에 삽입하는 것
> 

버블 정렬과 선택 정렬은 이미 정렬 되어있는 상태라고 하더라도 최소한 O(N^2) 의 시간이 걸리지만
삽입 정렬의 경우 이미 정렬 되어있는 상태라고 한다면 최소 O(N) 의 시간이 걸리게 된다.

🤚 이해가 되지 않는 점 : `x-1-y` 부분에 대한 이해가 필요하다.

⇒ 이 부분은 `for x in range(1,n)` 부분을 통해 이해할 수 있다.
x = 0, y = 0 일 때 array[-1] 을 탐색하지 않느냐 하는 부분이 의문점이었는데 x는 1부터 시작하므로 이 경우가 성립할 수 없다는 것을 깨닫게 되었다.

삽입 정렬을 구현할 때는 시작 위치를 주의해야 한다는 것을 기억해두자. (맨 처음은 이미 정렬된 상태이니까)
![alt text](image-1.png)

```python
input = [4, 6, 2, 9, 1]

def insertion_sort(array):
    n = len(array)
    new_array = []
    for x in range(1, n): # 4 -> 4, 6 -> 4, 2, 6 -> 2, 4, 6
        for y in range(x): # x = 0 -> y = 0 / x = 1, y= 0
            if array[x - 1 - y] > array[x - y]:
                array[x - 1 - y], array[x - y] = array[x - y], array[x - 1 - y]
            else:
                break # 이미 모두 정렬되어 있으므로 넘어가면 됨
    return array

insertion_sort(input)
print(input) # [1, 2, 4, 6, 9] 가 되어야 합니다!

print("정답 = [4, 5, 7, 7, 8] / 현재 풀이 값 = ",insertion_sort([5,8,4,7,7]))
print("정답 = [-1, 3, 9, 17] / 현재 풀이 값 = ",insertion_sort([3,-1,17,9]))
print("정답 = [-3, 32, 44, 56, 100] / 현재 풀이 값 = ",insertion_sort([100,56,-3,32,44]))
```

## 🤚병합 정렬 (복습할 때 반드시 다시 구현)

> 배열의 앞부분과 뒷부분으로 나누어서 각각 정렬한 후에 다시 병합하는 작업을 반복하는 것
> 

```python
array = [5, 3, 2, 1, 6, 8, 7, 4]

def merge(array1, array2):
    result = []
    array1_index = 0
    array2_index = 0

    while array1_index < len(array1) and array2_index < len(array2):
        if array1[array1_index] < array2[array2_index]:
            result.append(array1[array1_index])
            array1_index += 1
        else:
            result.append(array2[array2_index])
            array2_index += 1

    while array1_index < len(array1):
        result.append(array1[array1_index])
        array1_index += 1

    while array2_index < len(array2):
        result.append(array2[array2_index])
        array2_index += 1
        
		return result

def merge_sort(array):
    if len(array) <= 1:
        return array
    mid = len(array) // 2
    left_array = array[:mid]
    right_array = array[mid:]
    return merge(merge_sort(left_array), merge_sort(right_array))
```