---
title: "Widget 라이프 사이클"
date: "2026-03-19"
category: "Flutter"
---

# Widget Life Cycle

## StatelessWidget

생성자 실행 → 빌드 실행

## StatefulWidget

<img width="1512" height="832" alt="image" src="https://github.com/user-attachments/assets/0210d1be-07dc-417d-a73a-dc7bacbaa710" />


<img width="1539" height="848" alt="image" src="https://github.com/user-attachments/assets/fd9da031-561a-4ed1-b86b-eecfea7bda8c" />


<img width="1533" height="847" alt="image" src="https://github.com/user-attachments/assets/f44c0ff6-5a8c-4419-b8bc-ba299ecd6c50" />


1. Constructor()
2. createState()
3. initState() : 시작될 때 딱 한 번만 실행이 된다
4. didChangeDependencies() : 실행된 이후에 여러번 다시 실행될 수 있다
5. dirty : State 클래스의 상태를 의미함
6. build()
7. clean : State 클래스의 상태를 의미함
8. deactivate() : State가 삭제되면 실행됨
9. dispose()

setState() 함수가 clean 상태를 dirty 상태로 바꾼다. 이로써 다시 build가 되는 것이다.

## 실습

[첫 번째 라이프사이클]

한 번 누른 경우 1, 2, 3, 4, 5 수행

다시 누른 경우 6, 7 수행

[두 번째 라이프사이클]

스타일을 변경하면 5만 다시 수행

[세 번째 라이프사이클]

생성자인 1번과 5번만 다시 수행

```dart
import 'package:flutter/material.dart';

class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  bool show = false;
  Color color = Colors.red;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SizedBox(
        width: double.infinity,
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            if (show)
              GestureDetector(
                onTap: () {
                  setState(() {
                    color = color == Colors.blue ? Colors.red : Colors.blue;
                  });
                },
                child: CodeFactoryWidget(color: color),
              ),
            SizedBox(height: 32.0),
            ElevatedButton(
              onPressed: () {
                setState(() {
                  show = !show;
                });
              },
              child: Text('보이기안보이기'),
            ),
          ],
        ),
      ),
    );
  }
}

class CodeFactoryWidget extends StatefulWidget {
  final Color color;

  CodeFactoryWidget({super.key, required this.color}) {
    print('1) Stateful widget Constructor');
  }

  @override
  State<CodeFactoryWidget> createState() {
    print('2) Stateful Widget Create State');

    return _CodeFactoryWidgetState();
  }
}

class _CodeFactoryWidgetState extends State<CodeFactoryWidget> {
  @override
  void initState() {
    print('3) Stateful Widget initState');
    super.initState();
  }

  @override
  void didChangeDependencies() {
    print('4) Stateful Widget didChangeDependencies');
    super.didChangeDependencies();
  }

  @override
  Widget build(BuildContext context) {
    print('5) Statful Widget build');
    return Container(color: widget.color, width: 50, height: 50);
  }

  @override
  void deactivate() {
    print('6) Statful Widget deactivate');
    super.deactivate();
  }

  @override
  void dispose() {
    print('7) Statful Widget dispose');
    super.dispose();
  }
}

```
