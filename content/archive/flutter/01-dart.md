---
title: "Dart 기본 문법"
date: "2025-02-09"
category: "dart"
---


# 기본 문법

> 반드시 마지막에 세미콜론
> 
- 기본 함수 : void main() {}
- 변수 선언 : var variable = 12;
변수는 재선언 할 수 없음.
- 출력 : print(variable);
- ‘${variable}’ : 이거 사용할 때 백틱이 아닌 그냥 따옴표 사용

## 타입

> 타입 끝에 물음표를 넣으면 null 이 들어갈 수 있다. (String?)
변수 끝에 느낌표를 넣으면 null 이 절대 들어갈 수 없다. (name2!)
> 
- int
- double
- bool (true/false)
- String
- var : 타입을 자동으로 유추해서 타입 고정.
- dynamic : var와 비슷하지만 이후에 다른 타입 넣으면 타입 바뀜.
- final : 한 번 선언한 뒤로 절대 변경이 불가능함. 상수 역할
final의 경우에는 빌드 타임에 값을 알고 있지 않아도 된다.
⇒ DateTime 사용 가능.
- const : 한 번 선언한 뒤로 절대 변경이 불가능함.
const의 경우에는 빌드 타임에 값을 알고 있어야 한다.
즉, 코드를 작성하는 순간부터 값을 알고 있어야 한다.
- DateTime : 날짜와 시간을 저장하는 변수 (코드가 실행되는 그 시간)
DateTime now = DateTime.now();

## 오퍼레이터

- a ??= b : 만약 a 값이 null 일 경우 b 값으로 바꿔라.

## 타입 비교

- a is b : a의 타입이 b인지 확인
number1 is int

## 리스트

- List<int> name = [’a’, ‘b’, ‘c’];
- name.length : 길이 구하기
- name.add(’d’) : 값 삽입
- name.remove(’d’) : 값 제거
- name.indexOf(’a’) : 인덱스 가져오기

## Map

키의 타입과 값의 타입 두 개를 선언해주어야 한다.

```tsx
Map<String, String> dict = {
  ‘a’ : ‘A’,
  ‘b’ : ‘B’,
};

// 추가 1
dict.addAll ({
	'e' : 'E',
});

// 추가 2
dict['f'] = 'F'

// 찾기
dict['a'] // A

// 삭제
dict.remove('a')

// key 값들 모두 가져오기
dict.keys

// value 값들 모두 가져오기
dict.values
```

## Set

> 중복값 들어갈 수 없다.
리스트랑 비슷함.
> 

```tsx
final Set<string> names = {
	'a',
	'b',
	'c'
}

// 추가
names.add('d')

// 삭제
names.remove('d')

// 존재하는지 확인
names.contains('b')
```

## loop

```tsx
// 기본 for 루프 말고 다른 방법
for(int number in numbers) {
	// ...
}
```

## enum

```tsx
enum Status {
	approved,
	pending,
	rejected
}

void main() {
	Status status = Status.pending
}
```

## 함수

- positional parameter : 순서가 중요한 파라미터
- optional parameter : 있어도 되고 없어도 되는 파라미터 (대괄호 안에)
- named parameter : 이름이 있는 파라미터 (순서가 중요하지 않음)

```tsx
// positional parameter
addNumbers(int x, int y, int z) {}

// optional parameter
addNumbers(int x, [int = 20 y, int z = 30]) {}

// named parameter
addNumbers({
	required int x,
	required int y,
	required int z,
}) {}

addNumbers(x: 10, y: 20, z: 30)
```

## typedef

```tsx
// 시그니처
typedef Operation = int Function(int x, int y);

// 더하기
int add(int x, int y) => x + y;

// 사용 (그냥 add 사용하는 것과 똑같이 나옴)
void main() {
	Operation operation = add;
	
	int result = operation(10, 20)
}
```
