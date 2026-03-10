---
title: "Flutter 기본 개념"
date: "2025-03-10"
category: "Flutter"
---

# 기본 개념

- /lib/main.dart 파일이 가장 기본이 되는 파일이다.
- 화면에 보여지는 모든 것은 위젯이라고 칭한다.
- MaterialApp은 항상 최상위에 위치한다.
- Scaffold는 바로 아래에 위치한다.

```dart
void main() {
  // 플러터 앱을 실행
  runApp(
    MaterialApp(
      debugShowCheckedModeBanner: false, // 배경 스타일링
      home: Scaffold(
        backgroundColor: Colors.black, // 텍스트 스타일링
        body: Center(
          child: Text(
              'TEST',
              style: TextStyle(
                color: Colors.white
              )
          ),
        )
      ),
    ),
  );
}
```
