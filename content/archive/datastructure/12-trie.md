---
title: "트라이"
date: "2024-01-09"
category: "datastructure"
---

# 트라이란?

> 자동 완성 기능을 구현할 수 있는 최고의 자료구조
> 
- retrieval(검색) 에서 유래되었다.
- 트라이는 저장하려는 단어를 한 글자씩 나눠서 해시테이블을 사용해 Key에는 글자를 저장하고 Value에는 자식 노드를 저장한다.
- 해시테이블의 성능은 O(1) 이지만, 글자 수에 따라 성능이 결정되므로 O(k)이다.
- 마지막 글자를 나타내기 위해 Key에는 애스터리스크(*), Value에는 0을 넣어준다.

## 구현 - 자동완성까지

```jsx
class Node{
	constructor() {
		this.children = {};
	}
}

class Trie{
	constructor() {
		this.root = new Node();
	}

	insert(word) {
		let currentNode = this.root;
		for(let char of word) { // 김 -> 치 -> 찌 -> 개
			// 이미 단어가 존재하는 경우
			if(currentNode.children[char] != null) {
				currentNode = currentNode.children[char];
			// 단어가 존재하지 않는 경우
			} else {
				let newNode = new Node();
				currentNode.children[char] = newNode;
				currentNode = newNode;
			}
		}
		// 마지막 글자라는 것을 표시
		currentNode.children["*"] = 0;
	}

	search(word) {
		let currentNode = this.root;
		for(let char of word) { 
			// 검색하려는 단어가 존재하는 경우
			if(currentNode.children[char] != null) {
				currentNode = currentNode.children[char];
			// 검색하려는 단어가 없는 경우
			} else {
				return null;
			}
		}
		return currentNode;
	}

	// 자동 완성 기능을 위해 필요한 함수
	getAllWords(startNode = null, word = "", words  []) {
		let currentNode = this.root;
		if(startNode != null) {
			currentNode = startNode;
		}

		for(let key in currentNode.children) {
			// 단어의 끝인 경우
			if(key == "*") {
				words.push(word);
			} else {
				let childNode = currentNode.children[key];
				this.getAllWords(childNode, word + key, words);
			}
		}

		return words;
	}

	autoComplete(word) {
		let currentNode = this.search(word);
		if(currentNode == null) {
			return null;
		}

		return this.getAllWords(currentNode, word);
	}
}
```

## 구현 - 검색 빈도가 높은 순으로 자동완성

```jsx
import { Heap } from ""
class Node{
	constructor() {
		this.children = {};
	}
}

class Trie{
	constructor() {
		this.root = new Node();
	}

	insert(word) {
		let currentNode = this.root;
		for(let char of word) { // 김 -> 치 -> 찌 -> 개
			// 이미 단어가 존재하는 경우
			if(currentNode.children[char] != null) {
				currentNode = currentNode.children[char];
			// 단어가 존재하지 않는 경우
			} else {
				let newNode = new Node();
				currentNode.children[char] = newNode;
				currentNode = newNode;
			}
		}
		// 마지막 글자라는 것을 표시
		currentNode.children["*"] = 0;
	}

	search(word, isCounting = false) {
		let currentNode = this.root;
		for(let char of word) { 
			// 검색하려는 단어가 존재하는 경우
			if(currentNode.children[char] != null) {
				currentNode = currentNode.children[char];
			// 검색하려는 단어가 없는 경우
			} else {
				return null;
			}
		}
		if(isCounting == true) {
			currentNode.children["*"]++; // 마지막 글자의 에스터리스크 값이 0에서 증가됨.
		}
		
		return currentNode;
	}

	// 자동 완성 기능을 위해 필요한 함수
	getAllWords(startNode = null, word = "", words  []) {
		let currentNode = this.root;
		if(startNode != null) {
			currentNode = startNode;
		}

		for(let key in currentNode.children) {
			let childNode = currentNode.children[key];
			// 단어의 끝인 경우
			if(key == "*") {
				words.push(new WordData(word, childNode));
			} else {
				this.getAllWords(childNode, word + key, words);
			}
		}

		return words;
	}

	autoComplete(word) {
		let currentNode = this.search(word);
		if(currentNode == null) {
			return null;
		}

		return this.getAllWords(currentNode, word);
	}

	autoCompleteByCount(word) {
		let words = this.autoComplete(word);

		let heap = new Heap();
		// 힙을 최대 힙으로 바꿔줌
		heap.isBigPriority = function(first, second) {
			return (first.priority > second.priority);
		}

		for(let word of words) {
			heap.insert(word);
		}

		// 우선순위 순서대로 정렬해서 데이터 반환 (dequeue 하면서 정렬되니까.)
		let sortedBySearchCount = [];
		do {
			let removed = heap.remove();
			if(removed = null){
				break;
			} else {
				srotedBySearchCount.push(removed);
			}
		}while(true)

		return sortedBySearchCount;
	}
}

class WordData{
	constructor(word, count) {
		this.word = word;
		this.count = count;
		this.priority = count; // 검색된 횟수가 우선순위
	}
}
```
