---
title: "그래프"
date: "2024-01-12"
category: "datastructure"
---

# 그래프란

트리는 그래프의 한 종류이다.
정확히는, 그래프에서 사이클이 존재하지 않는 그래프가 트리가 되는 것이다.
따라서 트리를 만족시키고 싶다면 사이클을 끊어주면 된다.
또한, 트리는 모든 노드가 연결되어 있어야 한다.

## 그래프 용어

- 정점(vertex) : 그래프에서 노드를 칭하는 단어
- 간선(edge) : 정점을 서로 잇는 선
- 인접(adjacent) : 서로 연결된 정점을 인접했다고 말한다
- 이웃(neighbpor) : 인접한 정점
- 방향 그래프 : 단방향, 양방향인지 관계를 나타내는 그래프 (SNS 팔로우 같은 것)

## 그래프의 성능

그래프는 정점 뿐만 아니라 간선의 개수도 성능에 영향을 미치기 때문에 V(Vertex)와 E(Edge)를 사용해 성능을 표시한다 (O(V+E))

## 구현

```jsx
class Vertex {
	constructor(value) {
		this.value = value;
		this.adjacent_vertices = [];
	}
	
	addAdjacentVertex(vertex) {
		this.adjacent_vertices.push(vertex);
	}

	removeAdjacentVertex(vertex) {
		for(let i = 0; i < this.adjacent_vertices.length; i++){
			if(this.adjacent_vertices[i] == vertex) {
				this.adjacent_vertices.splice(i--, 1);
			}
		}
	}
}

```

## 깊이 우선 탐색 알고리즘

> 시작 정점의 인접 정점 중 하나를 먼저 끝까지 탐색하고 나머지 인접 정점도 같은 방식으로 탐색하는 알고리즘
깊이의 끝까지 탐색한다고 해서 깊이 우선 탐색이라는 이름이 붙은 것이다.
> 

<img width="241" height="314" alt="image" src="https://github.com/user-attachments/assets/6f0b1d45-e254-4a5a-b4e7-ebefa41dfeb4" />

## 구현

```jsx
function DFS(vertex, visited_vertices = {}) {
	visited_vertices[vertex.value] = true;

	for(let adjacent of vertex.adjacent_vertices){
		// 이미 방문한 정점인 경우
		if(visited_vertices[adjacent.value] == true) {
			continue;
		} else {
			DFS(adjacent, visited_vertices);
		}
	}
}
```

# 너비 우선 탐색 알고리즘

> 시작 정점을 기준으로 넓게 퍼지면서 검색하는 것
> 

<img width="231" height="271" alt="image" src="https://github.com/user-attachments/assets/7935059c-a88b-4b09-8626-142ff452f2fc" />

## 구현

```jsx
import { Queue } from ""

function BFS(vertex) {
	let queue = new Queue(0;
	let visited_vertices = [];
	
	// 매개변수로 받은 시작 정점을 방문한 정점으로 저장하고
	// 그 정점을 큐에 넣어준다
	visited_vertices[vertex.value] = true;
	queue.enqueue(vertex);
	// 큐가 빌때까지 반복
	while(queue.isEmpty() == false) {
		let currentVertex = queue.dequeue().data;
		// 현재 정점의 인접 정점들을 순회
		for(let adjacent of currentVertex.adjacent_vertices) {
			// 이미 방문한 정점인 경우 아무것도 하지 않고 넘어감
			if(visited_vertices[adjacent.value]) {
				continue;
			} else {
				visited_vertices[adjacent.value] = true;
				queue.enqueue(adjacent);
			}
		}
	}
}
```
