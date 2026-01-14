---
title: "4주차: DFS, BFS, Dynamic Programming"
date: "2025-09-25"
category: "코딩테스트"
---
# 4주차

## DFS

자료의 검색, 트리나 그래프를 탐색하는 방법

그래프의 최대 깊이 만큼 탐색한다

DFS를 구현하는 방법은 재귀 함수를 이용하는 방법과 스택을 이용한 방법이 있다.

재귀 함수를 이용한 방법은 구현하기 간단하지만 무한 루프의 가능성이 존재해 스택을 사용해 구현하는 편이 좋다.

```python
# 1. 시작 노드를 스택에 넣는다
# 2. 현재 스택의 노드를 빼서 visited 에 추가한다.
# 3. 현재 방문한 노드와 인접한 노드 중 방문하지 않은 노드를 스택에 추가한다.
graph = {
    1: [2, 5, 9],
    2: [1, 3],
    3: [2, 4],
    4: [3],
    5: [1, 6, 8],
    6: [5, 7],
    7: [6],
    8: [5],
    9: [1, 10],
    10: [9]
}

def dfs_stack(adjacent_graph, start_node):
    stack = [start_node]
    visited = []
    while stack:
        current_node = stack.pop()
        visited.append(current_node)
        for adjacent_node in adjacent_graph[current_node]:
            if adjacent_node not in visited:
                stack.append(adjacent_node)
    return visited
```

## BFS

한 노드를 시작으로 인접한 모든 정점들을 우선 방문하는 방법

인접한 노드들을 우선 탐색하기 때문에 너비 우선 탐색이라고 할 수 있다.

DFS와 구현 방식이 유사하지만 스택 대신 큐를 쓴다는 점에서 차이가 있다.

```python
# 큐를 구현할 때는 deque 를 사용한다

graph = {
    1: [2, 3, 4],
    2: [1, 5],
    3: [1, 6, 7],
    4: [1, 8],
    5: [2, 9],
    6: [3, 10],
    7: [3],
    8: [4],
    9: [5],
    10: [6]
}

def bfs_queue(adj_graph, start_node):
    queue = deque([start_node])
    visited = []

    while queue:
        current_node = queue.popleft()
        visited.append(current_node)
        for adjacent_node in adj_graph[current_node]:
            if adjacent_node not in visited:
                queue.append(adjacent_node)

    return visited
```

## Dynamic Programming

> 큰 문제를 작은 하위 문제로 나누고, 그 하위 문제들의 해답을 이용해서 전체 문제를 해결하는 기법
메모이제이션 : 이전에 수행했던 결과값을 저장해두었다가 동일한 연산이 발생했을 경우 가져다 쓰는 방법
> 

```python
input = 50

# memo 라는 변수에 Fibo(1)과 Fibo(2) 값을 저장해놨습니다!
memo = {
    1: 1,
    2: 1
}

def fibo_dynamic_programming(n, fibo_memo):
		if i in memo:
				return memo[i]
		
		cal_fibo = fibo_dynamic_programming(n - 1, fibo_memo) + fibo_dynamic_programming(n - 2, fibo_memo)
		fibo_memo[n] = cal_fibo
		return cal_fibo

print(fibo_dynamic_programming(input, memo))
```

Dynamic Programming은 문제를 쪼갤 수 있는 구조일 때만 사용이 가능하다는 단점이 존재한다.