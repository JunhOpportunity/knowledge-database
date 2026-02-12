---
title: "Dart 비동기 프로그래밍"
date: "2025-02-12"
category: "dart"
---
# 비동기 프로그래밍

## Future

- Future 자료형 사용
- Future.value()
- Future.delayed(Duration(second: _), () {})

```dart
Future<String> name = Future.value('이름');

Future.delayed(Duration(second: 2), () {
	print('test')
})
```

## Async - Await

> Future 를 리턴해주는 함수에만 사용할 수 있다.
> 

Await 를 사용해서 Future 작업을 기다릴 수 있다.

```dart
void addNumber(int x, int y) async {
	await Future.delayed(Duration(second: 2), () {
		print('test')
	})
}
```

위 함수의 경우에는 Future 를 리턴하지 않기 때문에 함수를 호출할 때 Await 키워드를 사용할 수 없다.

따라서 다음과 같이 바꾸어야 한다.

참고로, 자동으로 Future로 감싸서 값을 리턴해주기 때문에 따로 처리해주지 않아도 된다.

```dart
Future<int> addNumber(int x, int y) async {
	await Future.delayed(Duration(second: 2), () {
		print('test')
	})
	
	return x + y;
}
```

## Stream

### 기본 구조

- StreamController()
- controller.stream
- stream.listen((){})

```dart
import 'dart:async';

void main()
	final controller = StreamController();
	final stream = controller.stream;
	
	final streamListener1 = stream.listen((val) {
		print('Listener 1 : $val');
	});

	controller.sink.add(1); 
	controller.sink.add(2); 
	controller.sink.add(3); 
	controller.sink.add(4); 
	controller.sink.add(5);
}
```

### 여러번 리스닝

만약 여러번 리스닝 하고 싶다면 `asBroadcastStream()` 을 사용해야 한다.

이렇게 되면 아래 코드에서 Listener 1과 2가 번갈아가면서 리스닝한다.

```dart
void main()
	final controller = StreamController();
	final stream = controller.stream.asBroadcastStream()
	
	final streamListener1 = stream.listen((val) {
		print('Listener 1 : $val');
	});
	
	final streamListener2 = stream.listen((val) {
		print('Listener 2 : $val');
	});

	controller.sink.add(1); 
	controller.sink.add(2); 
	controller.sink.add(3); 
	controller.sink.add(4); 
	controller.sink.add(5);
}
```

### 조건부 리스닝

where 함수를 써주면 된다.

```dart
void main()
	final controller = StreamController();
	final stream = controller.stream.asBroadcastStream()
	
	final streamListener1 = stream.where((val) => val % 2 == 0)
	.listen((val) {
		print('Listener 1 : $val');
	});
	
	final streamListener2 = stream.listen((val) {
		print('Listener 2 : $val');
	});

	controller.sink.add(1); 
	controller.sink.add(2); 
	controller.sink.add(3); 
	controller.sink.add(4); 
	controller.sink.add(5);
}
```

### 함수형 스트림

- async*
- yield : return 대신 이 키워드를 사용

아래처럼 코드를 작성하면, for 문이 한 번 완료될 때마다 결과값을 yield 로 넘겨준다.

```dart
void main() {
	cal(2).listen((val) {
		print(val)
	})
}

Stream<int> cal(int number) async* {
	for(int i = 0; i < 5; i++) {
		yield i * number;
	}
}
```

만약 yield* 을 사용하면 해당 스트림이 모두 끝날때까지 다른 스트림을 실행하지 않게된다.

```dart
Stream<int> playAllStream() async* {
	yield* cal(1); // 얘 다 끝나고 아래꺼 시작
	yield* cal(1000);
}
```
