---
title: "랜덤 숫자 생성 프로젝트"
date: "2026-03-31"
category: "Flutter"
---

# 랜덤 숫자 생성기

## 버튼 디자인

버튼 디자인의 경우에는 여러가지 효과가 있어야 하기 때문에 특수한 방법으로 디자인을 해준다.

```dart
ElevatedButton(
  onPressed: () {},
  style: ElevatedButton.styleFrom(backgroundColor: redColor),
  foregroundColor: Colors.white, // 글자 색상 변경
  child: Text('버튼'),
),
```

```dart
[[1,2,3], [4,5,6], [7,8,9]].map((test) => Row(
  children: test
      .map((e) => Text(e.toString(), style: TextStyle(color: Colors.white)))
      .toList(),
).toList(),)

```
## 랜덤 변수 생성

```dart
import 'dart:math';

final rand = Random();
final randomNumber = rand.nextInt(1000); // 0 ~ 999
```

## 새 페이지 생성 및 이동

스택 형태로 페이지 이동이 구성된다고 보면 된다.

따라서 push와 pop을 통해 페이지간 이동이 구현된다.

```dart
// 새 페이지로 이동하기
Navigator.of(context).push(
  MaterialPageRoute(
    builder: (BuildContext context) {
      return SettingScreen();
    },
  ),
);

// 이전 페이지로 돌아가기
Navigator.of(context).pop();
```

## 페이지간 데이터 주고받기

Navigator 의 push와 pop을 통해 페이지간 이동을 구현할 수 있다.

이때 pop의 인자로 값을 넘겨주면 다시 이전 페이지에서 해당 인자로 넘겨받은 값을 사용할 수 있다.

```dart
// 이후 페이지
onSavePressed() {
  Navigator.of(context).pop(maxNumber);
}

// 이전 페이지
onSettingIconPressed() async {
  final result = await Navigator.of(context).push(
    MaterialPageRoute(
      builder: (BuildContext context) {
        return SettingScreen();
      },
    ),
  );
}
```

🤚 저장한 뒤에 다시 세팅에 들어갔을 때 값이 그대로 저장되는 것을 구현하는 부분에 대해서 이해가 안 된다.
