---
title: "Flutter 기본 개념"
date: "2026-03-10"
category: "Flutter"
---

# 기본 개념

- /lib/main.dart 파일이 가장 기본이 되는 파일이다.
- 화면에 보여지는 모든 것은 위젯이라고 칭한다.
- MaterialApp은 항상 최상위에 위치한다.
- Scaffold는 바로 아래에 위치한다.
- 배경 색상은 `Colors` 에서 선택하거나 `Color(0xFF335CB0)` 이렇게 Color 클래스 내부에 `0xFF` 뒤에 헥스 코드를 넣어준다.

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

```dart
void main() {
  runApp(
    MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        backgroundColor: Color(0xFF335CB0),
        body: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Image.asset('asset/img/logo.png'),
            CircularProgressIndicator(color: Colors.white),
          ],
        ),
      ),
    ),
  );
}
```

- `CircularProgressIndicator(color: Colors.white)` 를 사용하면 간편하게 로딩 프로그레스 UI를 보여줄 수 있다.
