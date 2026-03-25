---
title: "CupertinoDatePicker를 활용한 날짜 계산"
date: "2026-03-25"
category: "Flutter"
---


# U&I 프로젝트

너비나 높이를 설정할 때 보통 `double.infinity`를 한다.

하지만 `MediaQuery.of(context).size.width` 이렇게 하면 똑같은 기능을 하지만 나중에 1/2, 1/3 이렇게 하고 싶은 경우에 바로 나누어주면 되기 때문에 유용하다.

아이폰의 노치 부분을 항상 생각해야 하기 때문에 `SafeArea` 위젯으로 감싸주면 된다.

## 테마 만들기

특정 스타일을 여러번 재사용 하고 싶다면 테마를 만들어서 사용하면 된다.

```dart
void main() {
  runApp(
    MaterialApp(
      theme: ThemeData(
        fontFamily: 'sunflower', // 기본 폰트
        textTheme: TextTheme(
          bodyLarge: TextStyle(color: Colors.white, fontSize: 30),
          bodyMedium: TextStyle(color: Colors.white, fontSize: 20),
        ),
      ),
      home: HomeScreen(),
    ),
  );
}
```

```dart
class _Top extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    final textTheme = Theme.of(context).textTheme;

    return Expanded(
      child: Container(
        child: Column(
          children: [
            Text('U&I', style: textTheme.displayLarge),
          ],
        ),
      ),
    );
  }
}
```

## 날짜 정하기

<img width="304" height="222" alt="image" src="https://github.com/user-attachments/assets/cbe1bcaa-f794-48e8-a48d-997415f991fa" />


애플에서 기본으로 제공해주는 `CupertinoDatePicker`를 사용한다.

여기서는 구현할 때 모달 창을 띄워두고 바깥 부분을 클릭하면 사라지는 형태로 구현한다.

이때 필요한 것이 `Dialog`이다.

```dart
IconButton(
  iconSize: 60,
  color: Colors.red,
  onPressed: () {
    showCupertinoDialog(
      context: context,
      barrierDismissible: true, // 모달 창 바깥쪽 누르면 창 닫히게
      builder: (BuildContext context) {
        return Align(
          alignment: Alignment.center,
          child: Container(
            color: Colors.white,
            height: 300,
            child: CupertinoDatePicker(
              mode: CupertinoDatePickerMode.date,
              onDateTimeChanged: (DateTime date) {
                print(date);
              },
              dateOrder: DatePickerDateOrder.ymd,
            ),
          ),
        );
      },
    );
  },
  icon: Icon(Icons.favorite),
),
```

만약 날짜의 차이를 구하고 싶다면 `now.difference(selectedDate).inDays` 이 함수를 활용하면 된다.
