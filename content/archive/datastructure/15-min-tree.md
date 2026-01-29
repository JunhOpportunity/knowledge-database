---
title: "최소 신장 트리"
date: "2024-01-13"
category: "datastructure"
---

# 신장 트리란?

그래프에 순환 구조가 없이 모든 정점이 연결되어있는 트리

<img width="539" height="273" alt="image" src="https://github.com/user-attachments/assets/42729b61-7d30-485e-a015-51c66b78f61e" />

# 최소 신장 트리란?

> 간선의 길이가 가장 짧은 신장 트리를 말한다.
> 

최소 신장 트리 = 최소 비용 신장 트리

최소 신장 트리를 구현하기 위한 두 가지 알고리즘이 존재한다.

- 크루스칼 알고리즘
- 프림 알고리즘

## 프림 알고리즘

> 매 순간 가장 가까운 정점을 찾아서 모든 정점을 가장 짧게 연결하는 알고리즘 ⊂ 탐욕 알고리즘
> 

최단 경로 알고리즘인 다익스트라 알고리즘과 비슷하다

다익스트라 알고리즘은 최단 거리가 보장되지만, 프림 알고리즘은 최단 거리가 아닐 수 있다.

| 다익스트라 | 프림 |
| --- | --- |
| 최단 거리 보장 | 최소 비용 연결 (최단 거리 보장 X) |
| 방향 그래프, 무방향 그래프 둘 다 동작 | 무방향 그래프 동작 |

## 구현

```jsx
class Prim{
	constructor(){
		this.all_cities = {};
	}
	
	registerCity(city) {
		this.all_cities[city.name] = city;
	}

	MST(start_city) {
		let visited_cities = {};
		let unvisited_cities= {};
		let mst_table = {};
	
		for(let city_name in this.all_cities) {
			unvisited_cities[city_name] = this.all_cities[city.name];
		}

		// 출발 도시가 등록되지 않은 경우 (없는 경우)
		if(unvisited_cities[start_city.name] == null) {
			return null;
		}else {
			// 순회하면서 모든 도시의 거리를 무한대로 설정
			for(let city_name in unvisited_cities) {
				// shortest_path_table[city_name] = Infinity;
				shortest_path_table[city_name] = {distance: Infinity, city: null};
			}
		}
		// 시작 도시는 거리 0
		// shortest_path_table[start_city.name] = 0;
		shortest_path_table[city_name] = {distance: 0, city: null};

		while(Object.keys(unvisited_cities).length > 0) {
			let closest_city_name = null;
			
			for(let city_name in unvisited_cities){
				// 
				if(closest_city_name == null || shortest_path_table[city_name].distance < shortest_path_table[closest_city_name].distance){
					closest_city_name = city_name;
				}
			}
	
			// 가장 가까운 도시를 방문한 도시로 설정하고 방문하지 않은 도시에서 제거
			visited_cities[closest_city_name] = unvisited_cities[closest_city_name];
			delete unvisited_cities[closest_city_name];

			for(let adjacent_city_name in visited_cities[closest_city_name].adjacent_cities) {
				if(unvisited_cities[adjacent_city_name] == null) {
					continue;
				}

				// 출발 도시에서 현재 도시까지의 거리 + 현재 도시에서 인접 도시까지의 거리
				// let distance = shortest_path_table[closest_city_name].distance +
				// visited_cities[closest_city_name].adjacent_cities[adjacent_cities_name];

				let distance = visited_cities[closest_city_name].adjacent_cities[adjacent_cities_name];

				// 만약 계산된 거리가 기존의 최소 거리보다 짧은 경우 최단 거리 갱신
				if(shortest_path_table[adjacent_cities_name].distance > distance) {
					shortest_path_table[adjacent_cities_name].distance = distance;
					shortest_path_table[adjacent_cities_name].cirt= visited_cities[closest_city_name];
				}
			}
		}
		console.log(mst_table);
	}
}
```
