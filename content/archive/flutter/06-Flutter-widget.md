---
title: "Flutter 위젯"
date: "2026-03-11"
category: "Flutter"
---

# 위젯 만들기

> 재사용 가능한 컴포넌트 같은 것을 만든다고 보면 된다.
> 

`stless` 입력하면 매우 빠르게 틀이 만들어진다.

```dart
// 위젯 생성
class HomeScreen extends StatelessWidget {
  // @override ~ return 부분은 자동완성
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Color(0xFF335CB0),
      body: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Image.asset('asset/img/logo.png'),
          CircularProgressIndicator(color: Colors.white),
        ],
      ),
    );
  }
}

// 위젯 사용
void main() {
  runApp(
    MaterialApp(
      debugShowCheckedModeBanner: false,
      home: HomeScreen()
    ),
  );
}
```

- `SizedBox(height: 28.0)` : 위젯 가장 바깥에 스타일을 설정해서 크기를 조절하는 것 대신 사이즈 조절을 위한 박스를 하나 중간에 추가한다.

### Image 위젯

1. asset/img/logo.png 생성
2. pubspec.yaml 파일 수정

```dart
flutter:
	assets:
    - asset/img/
```

1. body: Image.asset(’asset/img/logo.png’)
