---
title: "Flutter Padding"
date: "2026-03-17"
category: "Flutter"
---

# Padding

- EdgeInsets.all(8.0) : 상하좌우 모두 8.0 만큼 패딩
- EdgeInsets.symmetric(horizontal: 32.0) : 좌우만 32
- EdgeInsets.symmetric(vertical: 32.0) : 상하만 32
- EdgeInsets.only(top: 32.0) : 원하는 위치에만 추가 가능 (top, left, right, bottom)
- EdgeInsets.fromLTRB(8.0, 8.0, 8.0, 8.0) : 좌 - 상 - 우 - 하 순서로 입력

```dart
          child: Padding(
            padding: const EdgeInsets.all(8.0),
            child: Container(
              color: Colors.blue,
              width: 50.0,
              height: 50.0,
            ),
```
