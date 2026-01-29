---
title: "가중 그래프와 다익스트라 알고리즘"
date: "2024-01-10"
category: "datastructure"
---

# 가중 그래프란?

> 간선의 크기를 저장하는 그래프
> 

여기서 크기는 정점 간의 거리 또는 정점 간의 비용을 말한다

## 구현

```jsx
class WeightedGraphVertex{
	constructor(value) {
		this.value = value;
		this.adjacent_vertices = {};
	}

	addAdjacentVertex(vertex, weight) {
		this.adjacent_vertices[vertex.value] = weight;
	}

	removeAdjacentVertex(vertex){
		delete this.adjacent_vertices[vertex.value];
	}
}
```

# 다익스트라 알고리즘

> 최단 경로 알고리즘
> 

## 경로 탐색 방법

1. 기준이 되는 정점으로부터 인접한 정점들까지의 거리를 조사한다.
2. 거리가 가까운 순으로 해당 정점으로 이동한 뒤 `기준이 되는 정점에서부터의 거리 + 현재 정점과 인접한 정점들의 거리` 를 계산해서 기준이 되는 정점에서부터 인접한 거리와 계산한 거리를 비교해 만약 계산한 거리가 더 짧다면 더 나은 경로를 찾은 것이므로 최단 거리를 변경해주는 방식으로 진행된다.

## 구현

```jsx
// 가중 그래프 재사용
class City{
	constructor(name) {
		this.name = name;
		this.adjacent_cities = {};
	}

	addAdjacentCity(city, distance) {
		this.adjacent_cities[city.name] = distance;
	}

	removeAdjacentVertex(city){
		delete this.adjacent_cities[city.name];
	}
}

class Dijstra{
	constructor(){
		this.all_cities = {};
	}
	
	registerCity(city) {
		this.all_cities[city.name] = city;
	}

	shortestPath(start_city, end_city) {
		let visited_cities = {};
		let unvisited_cities= {};
		let shortest_path_table = {};
	
		for(let city_name in this.all_cities) {
			unvisited_cities[city_name] = this.all_cities[city.name];
		}

		// 출발 도시가 등록되지 않은 경우 (없는 경우)
		if(unvisited_cities[start_city.name] == null) {
			return null;
		}else {
			// 순회하면서 모든 도시의 거리를 무한대로 설정
			for(let city_name in unvisited_cities) {
				shortest_path_table[city_name] = Infinity;
			}
		}
		// 시작 도시는 거리 0
		shortest_path_table[start_city.name] = 0;

		while(Object.keys(unvisited_cities).length > 0) {
			let closest_city_name = null;
			
			for(let city_name in unvisited_cities){
				// 
				if(closest_city_name == null || shortest_path_table[city_name] < shortest_path_table[closest_city_name]){
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
				let distance = shortest_path_table[closest_city_name] +
				visited_cities[closest_city_name].adjacent_cities[adjacent_cities_name];

				// 만약 계산된 거리가 기존의 최소 거리보다 짧은 경우 최단 거리 갱신
				if(shortest_path_table[adjacent_cities_name] > distance) {
					shortest_path_table[adjacent_cities_name] = distance;
				}
			}
		}
	}
}
```

```jsx
// 경유지가 있는 경우 최단 경로를 구하는 알고리즘
class Dijstra{
	constructor(){
		this.all_cities = {};
	}
	
	registerCity(city) {
		this.all_cities[city.name] = city;
	}

	shortestPath(start_city, end_city) {
		let visited_cities = {};
		let unvisited_cities= {};
		let shortest_path_table = {};
	
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
				let distance = shortest_path_table[closest_city_name].distance +
				visited_cities[closest_city_name].adjacent_cities[adjacent_cities_name];

				// 만약 계산된 거리가 기존의 최소 거리보다 짧은 경우 최단 거리 갱신
				if(shortest_path_table[adjacent_cities_name].distance > distance) {
					shortest_path_table[adjacent_cities_name].distance = distance;
					shortest_path_table[adjacent_cities_name].cirt= visited_cities[closest_city_name];
				}
			}
		}

		let path_string = this.showShortestPathRecursively(end_city.name, shortest_path_table);
		console.log(path_string;)
	}

	showShortestPathRecursively(destination_city_name, shortest_path_table, path_string = ""){
		if(shortest_path_table[destination_city_name].city == null) {
			path_string += destination_city_name;
			return path_string;
		}

		path_string = this.showShortestPathRecursively(shortest_path_table[destination_city_name].city.name), shortest_path_table, path_string);
		path_string += " -> " + destination_city_name;
		return path_string;
	}
}
```
