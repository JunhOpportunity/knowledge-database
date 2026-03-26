---
title: "랜덤 숫자 생성 프로젝트"
date: "2026-03-26"
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

