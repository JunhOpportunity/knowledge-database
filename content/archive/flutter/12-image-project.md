---
title: "Timer와 Pageview를 활용한 Carousel"
date: "2026-03-24"
category: "Flutter"
---

# 전자액자 프로젝트

## Timer

지정한 시간이 지난 뒤 한 번 또는 주기적으로 무언가를 실행할 수 있게 해준다.

기본적으로 Dart 에서 제공해준다. (`dart:async` 패키지)

```dart
// 한 번만 실행
Timer(
	Duration(seconds: 1),
	() {
		print('1초 뒤에 한 번 실행')
	};
)

// 주기적으로 실행
Timer.periodic(
	Duration(seconds: 1),
	(Timer timer) {
		print('1초 뒤에 한 번 실행 반복')
		if(n == 5) {
			timer.cancel();
		}
	};
)
```

## Pageview 위젯

- Pageview 위젯을 사용하면 여러 개의 위젯들을 슬라이드 하게 만들 수 있다.

```dart
// children에는 list만 넣을 수 있기 때문에 toList 사용
class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  Timer? timer;
  PageController controller = PageController();

  @override
  void initState() {
    super.initState();

    timer = Timer.periodic(Duration(seconds: 2), (timer) {
      int currentPage = controller.page!.toInt();
      int nextPage = currentPage + 1;

      if (nextPage > 4) {
        nextPage = 0;
      }

      controller.animateToPage(
        nextPage,
        duration: Duration(milliseconds: 500),
        curve: Curves.linear,
      );
    });
  }

  @override
  void dispose() {
    if(timer != null) {
      timer!.cancel();
    }

    controller.dispose();

    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: PageView(
        controller: controller,
        children: [1, 2, 3, 4, 5]
            .map(
              (e) => Image.asset('asset/img/image_$e.jpeg', fit: BoxFit.cover),
            )
            .toList(),
      ),
    );
  }
}
```

controller를 생성했다면 dispose를 사용해서 삭제해주어야 함.
