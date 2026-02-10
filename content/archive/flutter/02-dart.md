---
title: "Dart 함수형 프로그래밍(OOP)"
date: "2025-02-10"
category: "dart"
---

# 객체지향 프로그래밍

## 클래스

```dart
// 클래스 선언
class Idol {
	final String
	final List<String> members;

	const Idol(this.name, this.members);
	
	Idol.fromList(List values)
			: this.members = values[0],
			  this.name = values[1];
	void sayHello() {
		print('안녕하세요 Sfthis.name}입니다.') ;
	}
}

// 인스턴스 선언 후 사용
Idol aIdol = Idol()

// 둘은 같은 것으로 본다.
Idol aPink = const Idol(
	'A',
	[1, 2, 3]
);

Idol bPink = const Idol(
	'A'
	[1, 2, 3]
)
```

- const로 클래스를 만든 경우에는 같은 값을 가진 두 const class는 같은 것으로 판단된다. (원래 객체는 내부 값이 모두 같아도 새로 생성한 다른 객체의 경우 다르다고 판단하는데, 특이한 경우이니 기억해두자.)
- static 키워드를 사용하면 모든 클래스에서 공통적으로 사용할 수 있는 변수가 된다. 따라서 한 번 값을 설정하고나면 전부 공용으로 사용한다.
함수의 경우에는 static 으로 선언하면 인스턴스 선언 없이도 메서드를 사용할 수 있다.

## getter, setter

getter는 따로 파라미터를 받지 않는다.

setter는 무조건 하나의 파라미터만 받는다.
왜 그런것이냐? 평소에 알던 방식이랑은 다른 방식으로 값을 넣기 때문이다.
기존의 방식인 함수 사용이 아니다!

setter는 거의 안쓴다.

```dart
class Idol {
	String
	List<String> members;

	Idol(this.name, this.members);
	
	Idol.fromList(List values)
			: this.members = values[0],
			  this.name = values[1];
	void sayHello() {
		print('안녕하세요 Sfthis.name}입니다.') ;
	}
	
	String get firstMember{
		return this.members[0]
	}
	
	set firstMember(String name) {
		this.members[0] = name;
	}
}

// getter 사용
aPink.firstMember
// setter 사용
aPink.firstMember = "abc"
```

## private

함수나 클래스나 변수 이름 앞에 언더스코어를 붙여주면 된다.

## 상속

- super 라는 키워드가 부모 클래스를 의미한다.
- 자식 클래스는 부모 클래스가 될 수 있다. 즉, 타입을 비교할 때 부모 클래스인지 아닌지 확인했을 때 true로 나온다.
- 반대로 부모 클래스는 자식 클래스가 될 수 없다.

```dart
// 기본 클래스
class Idol {
	String name;
	int membersCount;

	Idol({
		required this.name, 
		required this.membersCount
	});
}

// 상속
class BoyGroup extends Idol {
	BoyGroup(
		String name,
		int memberCount,
	) : super(
		name : name,
		memberCount: memberCount
	);
}
```

## Method overriding

> 상속한 함수를 덮어쓴다.
> 

그냥 함수 작성하면 되는데, `@override` 라는 키워드를 적어주면 좋다.

## Interface

> 타입 선언처럼 나중에 클래스 선언할 때 어떤 것이 들어가는지 적어주는 것.
> 

```dart
// 인터페이스
class IdolInterface {
	String name;
	
	IdolInterface(this.name);
	
	void sayName() {}
}

// 사용
class BoyGroup implements IdolInterface {
	// ...
}
```

## Generic

```dart
class Lecture<T> {
	final T id;
	final String name;
}

void main() {
	Lecture<int> lecture1 = Lecture(123, 'lecture1')
}
```
