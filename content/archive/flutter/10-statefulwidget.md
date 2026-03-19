---
title: "StatefulWidget"
date: "2026-03-18"
category: "Flutter"
---

# StatefulWidget

플러터의 위젯은 불변 법칙을 따른다.

즉, 한 번 생성된 위젯은 절대 변경될 수 없다.

따라서 플러터는 위젯의 스타일이 변경된다면 기존의 위젯을 삭제하고 새 위젯이 생성되는 형식으로 화면에 그림을 그린다.

이를 적용하기 위해서는 build() 함수를 재실행해야 한다. (Hot Reload 번개 버튼 클릭)

그런데 Hot Reload 버튼은 개발 환경에서만 사용 가능하기 때문에 이것을 플러터가 알아서 수행하도록 하기 위해서 필요한 것이 StatefulWidget이다.

이것을 구현하기 위해서는 딱 두 가지가 필요하다.

1. StatefulWidget 클래스를 상속받은 클래스
2. State<> 클래스를 상속받은 기존 코드가 들어간 클래스

```dart
class HomeScreen extends StatefulWidget {
  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  Color color = Colors.blue;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        width: double.infinity,
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: () {
                if (color == Colors.blue) {
                  color = Colors.red;
                } else {
                  color = Colors.blue;
                }
                print('색상 변경 : color $color');
                setState(() {

                });
              },
              child: Text('색상 변경'),
            ),
            SizedBox(height: 32.0),
            Container(width: 50.0, height: 50.0, color: color),
          ],
        ),
      ),
    );
  }
}
```
