---
title: "Flutter Row & Column"
date: "2026-03-12"
category: "Flutter"
---
# Row & Column

Row : 가로로 위젯을 배치할 때 사용

Column : 세로로 위젯을 배치할 때 사용

특별한 제한사항이 없다면 Row 위젯과 Column 위젯의 주축은 최대 크기를 차지하고 반대축은 최소 크기를 차지한다.

- MainAxisAlignment : 주 축을 의미한다. (만약 Row 라면 가로 축을 의미하는데, 이때 주 축은 항상 최대 길이 (좌우 100%) 이고 반대축은 최소길이 (딱 위젯 크기 만큼만))

- CrossAxisAlignment
    - .start : 반대축의 시작에 정렬
    - .end : 반대축의 끝에 정렬
    - .center : 반대축의 중앙에 정렬
    - .stretch : 반대축으로 위젯들을 최대로 늘림
    - .baseline : 텍스트 기준선을 기준으로 위젯을 정렬

SafeArea : 화면의 끝부분에서 위젯이 잘리는 것을 방지하기 위해 사용함.

<img width="219" height="84" alt="image" src="https://github.com/user-attachments/assets/1989520c-e7db-464a-b461-a564121b17a9" />

<img width="199" height="88" alt="image" src="https://github.com/user-attachments/assets/cc44f8ab-f639-45c3-af96-6c1c680e45e9" />

- Expanded 위젯 : 해당 Row나 Column 안에 남는 공간을 전부 차지한다.

```dart
class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Container(
          color: Colors.black,
          width: double.infinity,
          child: Column(
            mainAxisAlignment: MainAxisAlignment.start,
            mainAxisSize: MainAxisSize.max,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: colors
                .map((e) => Container(height: 50.0, width: 50.0, color: e))
                .toList(),
          ),
        ),
      ),
    );
  }
}
```
