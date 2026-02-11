---
title: "Dart 함수형 프로그래밍"
date: "2025-02-11"
category: "dart"
---

# 함수형 프로그래밍

## 리스트

리스트 → Map 으로 변환

리스트 → Set 으로 변환

```dart
List<String> aPink = ['a', 'b', 'c'];

print(aPink.asMap()); // {0: a, 1: b, 2: c}
print(aPink.toSet()); // {a, b, c}
```

스트링 → List 로 변환

```dart
String number = '1234';

// [1.jpg, 2.jpg, 3.jpg, 4.jpg]
final parsed = number.split('').map((x) => '$x.jpg').to List();
```

## List 필터링

```dart
List<Map<String, String>> people = [
	{
		'name': 'a',
		'group': 'A',
	},
	{
		'name': 'b',
		'group': 'B',
	}
]

// group의 값이 A인 것들만 가져와서 리스트로 바꾸기
final aPink = people.where((x) => x['group'] == 'A').toList()

```

## 유용한 함수

- reduce : prev는 처음 값, 그다음부터는 이전 계산값의 결과값
반드시 reduce 이전의 타입과 같은 타입만 반환할수 있다.
number 타입이 int 였는데 계산을 String 으로 해서 반환하면 에러가 발생한다.
    
    ```dart
    final result = number.reduce((prev, next) => prev + next)
    ```
    
- fold : reduce와 유사하지만 처음 값을 하나 지정해줄 수 있고, 이전 타입과 같지 않은 다른 타입을 반환하는 것도 가능하다.
    
    ```dart
    final result = number.fold(0, (prev, next) => prev + next)
    ```
    
