---
title: "최대 유량 문제"
date: "2024-01-14"
category: "algorithm"
---
# 최대 유량 문제

> 흐를 수 있는 물의 용량이 다른 여러 관을 통해서 Source → Sink 방향으로 최대한 많은 양의 물을 보내는 문제
> 
- Source : 물을 보내는 부분
- Sink : 물을 받는 목적지 도시
- 용량 : 물이 흐를 수 있는 양
- 유량 : 현재 관에서 흐르고 있는 물의 양
- 잔여 용량 : 용량 - 유량

이 문제를 해결하기 위해 두 가지 탐욕 알고리즘을 사용할 수 있다

- 포드 폴커슨 알고리즘 (깊이 우선 탐색)
- 에드몬드 카프 알고리즘 (너비 우선 탐색)

## 포드 폴커슨 알고리즘

1. 증가 경로 찾기 - Source 에서 Sink로 가는 경로 하나를 깊이 우선 탐색으로 찾기
2. 증가 경로에 흐를 수 있는 최대 유량 찾기
3. 찾은 증가 경로에서 보낼 수 있는 최대 유량을 흘려 보냄

## 구현

```jsx
class City{
	constructor(name) {
		this.name = name;
		this.adjacent_cities = {};
	}
	
	// 인접 도시 추가 함수 (근처 도시 연결하고 현재 도시에서 연결되는 간선의 용량과 흐르는 물의 양 저장)
	addAdjacentCity(city, flowAndCapacity) {
		this.adjacent_cities[city.name] = flowAndCapacity;
		city.adjacent_cities[this.name] = {flow: 0, capacity: 0};
	}

	removeAdjacentCity(city) {
		delete this.adjacent_cities[city.name];
	}
}

class MaximumFlow{
	constructor(name) {
		this.all_cities = [];
		this.paths = []; // 깊이 우선 탐색으로 찾은 경로를 저장하기 위한 스택
	}
		
	registerCity(city) {
		this.all_cities[city.name] = city;
	}

	// 깊이 우선 탐색
	DFS(source, sink, visited_cities = {}) {
		// 기저조건 - source와 sink 가 같은 도시인 경우
		if(source.name == sink.name) {
			return true;
		}

		visited_cities[source.name] = true;
		
		for(let adjacent in source.adjacent_cities) {
			let edge = source.adjacent_cities[adjacent];
			// 이미 방문한 도시인 경우
			if(visited_cities[adjacent]) {
				continue;
			// 잔여 용량이 1 이상인 경우(= 물이 흐를 수 있는 경우)
			} else if(edge.capacity - edge.flow > 0) {
				this.paths.push(source);
				// 재귀 호출로 sink 까지 가는 경로가 있는 경우
				if(this.DFS(this.all_cities[adjacent], sink, visited_cities) == true) {
					return true;
				// 재귀 호출로 sink 까지 가는 경로가 없다면 잘못된 경로이므로 제거
				} else {
					this.paths.pop();
				}
			}
		}
		
		// 모든 인접 도시를 시도했으나 경로가 없으면 false 리턴
		return false;
	}
	
	FordFulkerson(source, sink) {
		let total = 0;

		// 재귀가 불가능 할 때까지(더이상 증가 유량이 없을 때까지) 수행
		while(this.DFS(source, sink)) {
			this.paths.push(sink); // 마지막 sink는 배열에 저장되어있지 않으므로 추가
			let currentPathFlow = Infinity;
		
			// 증가 경로를 돌면서 최대 유량 계산
			// 어차피 간선에 흐르는 물의 양을 찾는 것이므로 마지막 sink는 제외
			for(let i = 0; i < this.path.length - 1; i++) {
				let currentCity = this.paths[i];
				let nextCity = this.paths[i + 1];
				let edge = currentCity.adjacent_cities[nextCity.name];
				// 관의 잔여 용량이 현재 증가 경로에서 최솟값인 경우 최솟값으로 업데이트
				currentPathFlow = Math.min(currentPathFlow, (edge.capacity - edge.flow));
			}
			
			// 실제로 물을 흘려보냄
			for(let i = 0; i < this.path.length - 1; i++) {
				let currentCity = this.paths[i];
				let nextCity = this.paths[i + 1];
				// 정방향 물 흐름
				currentCity.adjacent_cities[nextCity.name].flow += currentPathFlow;
				// 역방향 물 흐름
				nextCity.adjacent_cities[currentCity.name].flow -= currentPathFlow;
			}
			total += currentPathFlow;
			this.paths = [];
		}
	}
}
```
